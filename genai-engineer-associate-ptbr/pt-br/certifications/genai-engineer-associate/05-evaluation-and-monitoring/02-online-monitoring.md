---
title: Monitoramento on-line
type: study-material
tags:
  - genai-engineer-associate
  - monitoring
  - inference-tables
  - drift
status: published
---

# Monitoramento on-line

## Visão geral

A avaliação off-line informa se o aplicativo *funcionaria* em um conjunto de dados estático. O **monitoramento on-line** informa se ele *está* funcionando em produção — minuto a minuto, solicitação por solicitação. No Databricks, o monitoramento on-line é baseado em **Inference Tables** (tabelas Delta de solicitações/respostas capturadas automaticamente), **tabelas de sistema** (uso, latência, custo), **alertas do Lakeflow Jobs** (notificações de drift/falha) e execuções de **LLM como juiz do MLflow** em amostras de produção capturadas.

> [!abstract]
>
> - **Inference Tables** são os dados brutos — cada prompt e resposta é capturado automaticamente em uma tabela Delta do UC
> - **Painéis de latência/custo** criados sobre Inference Tables + `system.serving.endpoint_usage`
> - **Detecção de drift** — monitore a distribuição de entradas (tópico, tamanho e idioma do prompt) e de saídas (taxa de recusa, tamanho, pontuação de fidelidade)
> - **Backtests do Databricks Agent Evaluation** — reproduza periodicamente uma amostra de solicitações de produção capturadas usando os juízes RAG integrados (fundamentação, relevância, correção, segurança)
> - **Alertas** — alertas de DBSQL + notificações do Lakeflow Jobs quando os limites são ultrapassados

> [!tip] O que o exame cobra
>
> - Que o monitoramento on-line se baseia em Inference Tables, não em registros personalizados do aplicativo
> - As categorias de métricas que devem sempre ser monitoradas: latência (P50/P95/P99), custo (tokens/solicitação), taxa de recusa/erro, drift de qualidade (LLM como juiz) e drift da distribuição de entradas
> - Que os alertas são configurados por meio de alertas de DBSQL (sobre métricas calculadas a partir de Inference Tables) ou notificações do Lakeflow Jobs
> - A diferença entre avaliação off-line (conjunto de dados estático) e monitoramento on-line (amostra do tráfego ativo)

---

## O que monitorar

| Categoria | Métrica | Motivo |
| :--- | :--- | :--- |
| **Latência** | Latência ponta a ponta P50 / P95 / P99 | A cauda lenta prejudica a experiência; P99 é o sinal de alerta |
| **Custo** | Tokens por solicitação, $ por solicitação, por aplicativo/usuário | Detecta prompts descontrolados e loops acidentais |
| **Confiabilidade** | Taxa de erro, taxa de recusa, taxa de timeout | Picos indicam problemas no modelo upstream ou regressões nos prompts |
| **Qualidade (LLM como juiz)** | Fidelidade, relevância da resposta, pontuação de segurança em uma amostra | Detecta drift de qualidade mesmo quando latência/custo parecem normais |
| **Drift de entrada** | Distribuição do tamanho dos prompts, tópicos, mistura de idiomas | Novos casos de uso ou ataques alteram essa distribuição |
| **Drift de saída** | Tamanho das respostas, padrões de recusa, sentimento | Muitas vezes, é o primeiro sinal de alteração no modelo subjacente |

## Uma consulta SQL simples de monitoramento

```sql
-- Latency + token roll-up by hour for the past 7 days
-- Schema: inference table enabled by AI Gateway (request_time, execution_duration_ms, request, response)
SELECT
  date_trunc('hour', request_time)          AS hour,
  COUNT(*)                                  AS requests,
  AVG(execution_duration_ms)                AS avg_latency_ms,
  PERCENTILE(execution_duration_ms, 0.95)   AS p95_latency_ms,
  AVG(response:usage.total_tokens::INT)     AS avg_tokens,
  SUM(response:usage.total_tokens::INT)     AS total_tokens
FROM main.serving_logs.customer_support_rag_payload
WHERE request_date >= current_date() - INTERVAL 7 DAYS
GROUP BY 1
ORDER BY 1 DESC;
```

Os nomes das colunas seguem o [esquema de tabela de inferência do AI Gateway](https://docs.databricks.com/aws/en/ai-gateway/inference-tables-serving-endpoints); todos os agentes implantados com `agents.deploy()` registram dados nesse tipo de tabela.

Coloque essa consulta em uma **view materializada** que seja atualizada a cada 5 minutos e configure um alerta de DBSQL para a linha mais recente, acionado quando P95 ultrapassar o valor-alvo.

## Backtest com LLM como juiz

```python
import mlflow
from databricks import sql

# Sample 500 recent production requests
with sql.connect(server_hostname=..., http_path=...) as conn:
    with conn.cursor() as cursor:
        cursor.execute("""
            SELECT request:messages AS messages, response:choices AS response
            FROM main.serving_logs.customer_support_rag_payload
            WHERE request_time >= current_timestamp() - INTERVAL 1 DAY
            ORDER BY rand()
            LIMIT 500
        """)
        df = cursor.fetchall_arrow().to_pandas()

# Use the Databricks Agent Evaluation framework: built-in RAG judges
# (groundedness, answer relevance, correctness, safety) scored by a stronger LLM
with mlflow.start_run(run_name="prod_backtest_daily"):
    results = mlflow.evaluate(
        data=df,
        model_type="databricks-agent",
    )
```

Execute isso segundo uma programação (diariamente); gere um alerta se a média móvel ficar abaixo de um limite.

> [!note] Atualização da plataforma (out. de 2026)
> `mlflow.evaluate(model_type="databricks-agent")` é a API Agent Evaluation do MLflow 2. Com o MLflow 3, não é mais necessário criar um job de backtest manual: o **monitoramento de produção** (o *Agent Monitoring* do exame) registra scorers no experimento do agente e pontua continuamente uma amostra de traces ativos — `Safety().register(name="safety").start(sampling_config=ScorerSamplingConfig(sample_rate=1.0))`. Consulte [Scorers, juízes, feedback de SME e Agent Monitoring do MLflow](./03-mlflow-scorers-judges-and-feedback.md) e [Configurar monitoramento de produção](https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/production-monitoring).

## Padrões de alerta

- **Alerta de DBSQL** em uma consulta que calcula uma métrica a partir de Inference Tables — é acionado quando um limite é ultrapassado
- **Notificações do Lakeflow Jobs** no job de backtest — são acionadas quando o job falha ou quando o resultado de uma tarefa ultrapassa um limite personalizado
- **Alertas baseados em tabelas de sistema** sobre custos (`system.serving.endpoint_usage`) — são acionados quando os gastos diários ultrapassam o orçamento

## Casos de uso

- **Detectar regressão de qualidade após trocar o modelo** — um backtest de LLM como juiz revela que a fidelidade caiu após a troca de provedores por meio da divisão de tráfego do Gateway
- **Detectar custos descontrolados causados por um ataque de prompt injection** — um aumento repentino de tokens por solicitação gera um alerta para a equipe em poucos minutos
- **Identificar um novo caso de uso** — o painel de drift de entrada mostra que usuários começaram a perguntar sobre um tópico para o qual o aplicativo não foi projetado
- **Planejar capacidade** — a tendência de uso em `system.serving.endpoint_usage` ajuda a identificar quando aumentar a capacidade provisionada

## Problemas e erros comuns

- **Inference Tables desativadas no endpoint** — não há dados de auditoria; ative o registro de cargas úteis nas configurações de AI Gateway do endpoint
- **Diferenças no esquema** — tabelas de inferência do AI Gateway usam `request_time`, `request_date` e `execution_duration_ms`; tabelas mais antigas de logs de solicitações de agentes usam `timestamp_ms`. Confira o esquema da tabela consultada
- **Backtest executado em amostras desatualizadas** — amostre somente as últimas N horas/dias; caso contrário, você estará avaliando comportamento antigo
- **Excesso de alertas** — alertas demais fazem com que ninguém os acompanhe. Comece com latência P99 + custo + taxa de erro; adicione alertas de qualidade depois de estabelecer a linha de base

## Dicas para o exame

> [!tip]
>
> - **Inference Tables são a base** do monitoramento on-line. São as mesmas tabelas Delta usadas na trilha de auditoria de governança.
> - Monitore sempre, no mínimo, **latência + custo + taxa de erro**. A qualidade (LLM como juiz) vem em seguida.
> - **Drift on-line** = a distribuição de entradas em produção diverge do conjunto de avaliação. **Regressão de qualidade on-line** = as métricas de LLM como juiz caem em uma amostra recente.
> - Os alertas são acionados pelo **DBSQL** ou pelo **Lakeflow Jobs**, não pelo próprio Mosaic AI.

## Principais conclusões

- O monitoramento on-line usa Inference Tables + tabelas de sistema + alertas de DBSQL + notificações do Lakeflow Jobs
- Monitore 6 categorias: latência, custo, confiabilidade, qualidade, drift de entrada e drift de saída
- Backtests com LLM como juiz em amostras de produção capturadas detectam regressões de qualidade
- Não ative o monitoramento sem uma Inference Table; sem a tabela, você estará operando às cegas

## Tópicos relacionados

- [Avaliação (off-line)](./01-evaluation-llm-apps.md)
- [Scorers, juízes, feedback de SME e Agent Monitoring do MLflow](./03-mlflow-scorers-judges-and-feedback.md)
- [AI Gateway: acompanhamento de uso e controle de custos](./04-ai-gateway-usage-and-cost-control.md)
- [Governança — a trilha de auditoria](../06-governance/01-governance-overview.md#a-trilha-de-auditoria)
- [Mosaic AI Model Serving](../../../../../certifications/genai-engineer-associate/02-assembling-and-deploying-apps/01-mosaic-ai-and-foundation-models.md)

## Documentação oficial

- [Inference Tables](https://docs.databricks.com/en/machine-learning/model-serving/inference-tables.html)
- [Avaliação de LLM do MLflow](https://mlflow.org/docs/latest/llms/llm-evaluate/index.html)
- [Alertas do Databricks SQL](https://docs.databricks.com/en/sql/user/alerts/index.html)
- [Notificações do Lakeflow Jobs](https://docs.databricks.com/en/jobs/notifications.html)

---

**[← Anterior: Avaliação de aplicativos de LLM](./01-evaluation-llm-apps.md) | [↑ Voltar para Avaliação e monitoramento](./README.md) | [Próximo: Scorers, juízes, feedback de SME e Agent Monitoring do MLflow](./03-mlflow-scorers-judges-and-feedback.md) →**
