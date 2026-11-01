---
title: "AI Gateway: acompanhamento de uso e controle de custos"
type: study-material
tags:
  - genai-engineer-associate
  - evaluation-and-monitoring
  - ai-gateway
  - unity-gateway
  - inference-tables
  - usage-tracking
  - rate-limits
  - cost-control
status: published
---

# AI Gateway: acompanhamento de uso e controle de custos

## Visão geral

O guia do exame de 2026 inclui o AI Gateway no domínio **Avaliação e monitoramento**: *“Usar AI Gateway (Inference Tables, Usage Tables e limites de taxa) para acompanhar um LLM ou agente implantado via Agent Framework”* e *“Usar recursos do Databricks para controlar custos de LLM.”* O AI Gateway é a camada de governança diante dos endpoints de serving. Ele registra cada solicitação e resposta em **tabelas de inferência**, registra tokens e solicitantes em **tabelas de sistema de uso** e aplica **limites de taxa** — as três ferramentas para ver o que um LLM ou agente ativo está fazendo e quanto custa.

> [!abstract]
>
> - **Tabelas de inferência (registro de cargas úteis)** — JSON de solicitação e resposta de cada chamada em uma tabela Delta do Unity Catalog (`<catalog>.<schema>.<prefix>_payload`)
> - **Tabelas de uso** — `system.serving.endpoint_usage` (+ `system.serving.served_entities`) para endpoints de serving; `system.ai_gateway.usage` para o Unity Gateway mais recente
> - **Limites de taxa** — consultas por minuto (QPM) ou tokens por minuto (TPM) para o endpoint, cada usuário, grupos ou service principals; solicitações que excedem o limite recebem HTTP 429
> - **Controles de custo** — limites de taxa, orçamentos, modo de preço adequado (pay-per-token vs. throughput provisionado), scale-to-zero, modelos menores e `ai_query()` em lote

> [!tip] O que o exame cobra
>
> - Qual recurso do gateway responde a cada pergunta: *o que foi perguntado e respondido?* (tabelas de inferência), *quem usou quantos tokens?* (tabelas de uso), *como impedir que um consumidor gaste demais?* (limites de taxa)
> - Configurar o registro de cargas úteis, o acompanhamento de uso e os limites de taxa em um endpoint
> - Escolher alavancas de custo para um cenário (tráfego irregular vs. estável, lote vs. tempo real, limites por usuário)
> - Combinar dados de uso e de cargas úteis para atribuir custos

---

> [!note] Atualização da plataforma (out. de 2026)
> O que o exame chama de **AI Gateway** agora está documentado como **AI Gateway (legado) para endpoints de serving**. Seu sucessor, **Unity Gateway**, está disponível em geral e governa serviços de modelos, provedores de modelos externos e serviços MCP como securables do Unity Catalog ([Governança para endpoints de model serving (legado)](https://docs.databricks.com/aws/en/ai-gateway/overview-serving-endpoints), [Governança de IA com Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/)). As tabelas são diferentes: **endpoints do Unity Gateway registram uso em `system.ai_gateway.usage`; endpoints de serving legados, em `system.serving.endpoint_usage`** ([Observabilidade do Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/observability)). **Orçamentos**, inclusive orçamentos por usuário do Unity Gateway, ficaram disponíveis em geral em 2026-07-06 ([Notas de versão de julho de 2026](https://docs.databricks.com/aws/en/release-notes/product/2026/july)). Os conceitos cobrados no exame — tabelas de inferência, tabelas de uso e limites de taxa — existem nos dois.

## Recursos em resumo

| Recurso | Pergunta respondida | Pago? |
| :--- | :--- | :---: |
| **Registro de cargas úteis** (tabelas de inferência) | O que exatamente foi enviado e retornado? Quais solicitações falharam? | Sim |
| **Acompanhamento de uso** (tabelas de sistema) | Quem chamou qual endpoint, com quantos tokens e quando? | Sim |
| **Permissões e limites de taxa** | Quem pode chamar e quanto pode usar? | Não |
| **AI Guardrails** (Public Preview) | Bloquear conteúdo inseguro e detectar PII em solicitações e respostas | — |
| **Divisão de tráfego** | Encaminhar porcentagens do tráfego entre modelos servidos | Não |
| **Fallbacks** | Repetir em outro modelo servido após 429 ou 5xx (modelos externos) | Não |

Segundo a [visão geral legada](https://docs.databricks.com/aws/en/ai-gateway/overview-serving-endpoints), tabelas de inferência e acompanhamento de uso são pagos; permissões de consulta, limites de taxa, fallbacks e divisão de tráfego são gratuitos.

### O suporte depende do tipo de endpoint

| Recurso | Modelo externo | FMAPI pay-per-token | FMAPI throughput provisionado | Agentes Databricks | Modelo personalizado |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Permissões e limites de taxa | ✓ | ✓ | ✓ | — | ✓ |
| Registro de cargas úteis | ✓ | ✓ | ✓ | ✓ | ✓ |
| Acompanhamento de uso | ✓ | ✓ | ✓ | — | ✓ |
| AI Guardrails | ✓ | ✓ | ✓ | — | — |
| Fallbacks | ✓ | — | — | — | — |
| Divisão de tráfego | ✓ | — | ✓ | — | ✓ |

> [!important]
> Para um **agente** implantado com o Agent Framework, o registro de cargas úteis em tabelas de inferência está sempre disponível (`agents.deploy()` o habilita), e os próprios traces do agente vão para o MLflow. O acompanhamento de uso e os limites de taxa se aplicam aos **endpoints de LLM chamados pelo agente** (Foundation Model APIs ou modelos externos), onde os tokens são consumidos. Limites de TPM não podem ser aplicados a endpoints que servem modelos personalizados ou agentes. Fontes: [matriz de recursos legada](https://docs.databricks.com/aws/en/ai-gateway/overview-serving-endpoints) e [página de configuração](https://docs.databricks.com/aws/en/ai-gateway/configure-ai-gateway-endpoints).

## Configurar o gateway em um endpoint

Configure na seção **AI Gateway** da interface do serving endpoint ou com `PUT /api/2.0/serving-endpoints/{name}/ai-gateway` ([referência REST](https://docs.databricks.com/api/workspace/servingendpoints/putaigateway)):

```json
{
  "usage_tracking_config": { "enabled": true },
  "inference_table_config": {
    "enabled": true,
    "catalog_name": "main",
    "schema_name": "serving_logs",
    "table_name_prefix": "support_llm"
  },
  "rate_limits": [
    { "key": "endpoint", "calls": 600, "renewal_period": "minute" },
    { "key": "user", "calls": 30, "renewal_period": "minute" },
    { "key": "user_group", "principal": "data-science", "tokens": 200000, "renewal_period": "minute" }
  ]
}
```

| Campo do limite de taxa | Valores |
| :--- | :--- |
| `key` | `endpoint` (limite global), `user` (padrão por usuário), `user_group`, `service_principal` |
| `calls` / `tokens` | QPM e/ou TPM; se ambos forem definidos, aplica-se o mais restritivo |
| `principal` | E-mail do usuário, nome do grupo ou ID do service principal para limites personalizados |
| `renewal_period` | `minute` (o único valor compatível) |

Regras dos limites de taxa: o limite do endpoint é um teto global; limites personalizados substituem o padrão por usuário; um limite específico de usuário prevalece sobre o limite de grupo; até **20** limites de taxa e **5** limites de grupo por endpoint; por padrão, não há limites.

## Tabelas de inferência: acompanhar a atividade de um endpoint ativo

Ativar o registro de cargas úteis cria `<catalog>.<schema>.<prefix>_payload` (uma view sobre uma tabela de telemetria `_otel_logs`). As linhas chegam em cerca de uma hora; cargas úteis maiores que 1 MiB não são registradas; respostas em streaming são agregadas ([tabelas de inferência para endpoints de serving](https://docs.databricks.com/aws/en/ai-gateway/inference-tables-serving-endpoints)).

| Coluna | Uso |
| :--- | :--- |
| `request_time`, `request_date` | Monitoramento de séries temporais e eliminação de partições |
| `databricks_request_id`, `client_request_id` | Combinar com tabelas de uso e com seus próprios logs |
| `status_code` | Monitorar taxa de erros |
| `execution_duration_ms` | Latência do modelo (exclui sobrecarga de rede) |
| `request`, `response` | JSON bruto — prompts, respostas, uso de tokens e contexto recuperado |
| `requester` | Usuário ou service principal que fez a chamada |
| `sampling_fraction` | Fator de subamostragem, se houver |

```sql
-- Error rate and model latency per hour, last 7 days
SELECT
  date_trunc('hour', request_time)                AS hour,
  COUNT(*)                                        AS requests,
  AVG(CASE WHEN status_code >= 400 THEN 1 ELSE 0 END) AS error_rate,
  PERCENTILE(execution_duration_ms, 0.95)         AS p95_ms
FROM main.serving_logs.support_llm_payload
WHERE request_date >= current_date() - INTERVAL 7 DAYS
GROUP BY 1
ORDER BY 1 DESC;
```

Agentes implantados com `agents.deploy()` também recebem `<model>_payload_request_logs` (solicitações formatadas, respostas e traces do MLflow) e `<model>_payload_assessment_logs` (feedback do Review App); o Databricks marca os logs de solicitações e avaliações como descontinuados em favor do tracing do MLflow 3 e de `mlflow.log_feedback()`.

## Tabelas de uso: quem usou o quê

```sql
-- Tokens per requester and endpoint, last 30 days (legacy serving endpoints)
SELECT
  se.endpoint_name,
  eu.requester,
  SUM(eu.input_token_count)  AS input_tokens,
  SUM(eu.output_token_count) AS output_tokens,
  COUNT(*)                   AS requests
FROM system.serving.endpoint_usage AS eu
JOIN system.serving.served_entities AS se
  ON eu.served_entity_id = se.served_entity_id
WHERE eu.request_time >= current_date() - INTERVAL 30 DAYS
GROUP BY 1, 2
ORDER BY output_tokens DESC;
```

- `endpoint_usage` registra contagens de tokens e caracteres, código de status, solicitante, indicador de streaming e um mapa `usage_context` por solicitação; `served_entities` contém metadados do endpoint e do modelo
- Por padrão, somente **administradores da conta** podem consultar essas tabelas
- Adicione identificadores, como `end_user_to_charge`, a `usage_context` na solicitação para atribuir custos a usuários finais ou aplicativos
- Para serviços do Unity Gateway, consulte `system.ai_gateway.usage`, use o painel de uso integrado e combine `system.billing.usage` com `system.billing.list_prices` para estimar gastos ([Acompanhar o uso de modelos](https://docs.databricks.com/aws/en/ai-gateway/usage-tracking))

## Controlar custos de LLM

Item do guia do exame: *“Usar recursos do Databricks para controlar custos de LLM.”*

| Alavanca | Como reduz custos |
| :--- | :--- |
| **Limites de taxa** (QPM / TPM) | Limita consumidores descontrolados, loops e abuso; promove equidade por usuário |
| **Orçamentos** | Limites de gastos com alertas; no Unity Gateway, limites por usuário e bloqueio de uso |
| **Acompanhamento de uso + `usage_context` / tags** | Mostra qual equipe, aplicativo ou usuário impulsiona os gastos, permitindo agir |
| **Pay-per-token** | Sem custo ocioso — ideal para tráfego baixo ou irregular e protótipos |
| **Throughput provisionado** | Capacidade reservada — custo por token menor em volume alto e estável e latência previsível |
| **Scale-to-zero** (modelos personalizados/agentes) | Sem custo quando ocioso, em troca de latência de inicialização a frio |
| **Modelos menores ou específicos para tarefas** | Menor custo por token; AI Functions como `ai_classify` para tarefas específicas |
| **Processamento em lote com `ai_query()`** | Cargas off-line são executadas como jobs, em vez de usar um endpoint sempre ativo |
| **Higiene de prompts e contexto** | Prompts mais curtos, menos trechos recuperados, limites de `max_tokens` |
| **Políticas de uso/orçamento serverless** | Marca gastos serverless (Agent Bricks exige uma política com orçamento diferente de zero) para atribuição |

## Divisão de tráfego e fallbacks

As porcentagens de entidades servidas devem somar 100 %. Use a divisão de tráfego para **lançamentos canário** (por exemplo, 10 % para um modelo candidato) e compare a qualidade nas tabelas de inferência antes de ampliar o tráfego. Um modelo com 0 % de tráfego pode servir apenas como entidade de fallback. **Fallbacks** repetem a solicitação na próxima entidade servida após um 429 ou 5xx e são compatíveis com endpoints de **modelos externos**. A primeira tentativa bem-sucedida (ou a última tentativa com falha) é registrada nas tabelas de uso e de cargas úteis.

## Casos de uso

- **Limites por equipe** — um limite de TPM `user_group` impede uma equipe de ciência de dados de consumir a capacidade compartilhada do endpoint
- **Estorno de custos** — tabelas de uso combinadas com `served_entities`, agrupadas pelo ID do aplicativo em `usage_context`, alimentam um relatório mensal de custos
- **Análise de incidente** — a tabela de inferência mostra os prompts exatos que produziram uma resposta incorreta, com solicitante (`requester`) e horário
- **Protótipo com tráfego irregular → produção estável** — migre de pay-per-token para throughput provisionado quando o tráfego se tornar previsível

## Problemas e erros comuns

### HTTP 429 Too Many Requests

**Cenário:** Clientes recebem respostas 429 durante os horários de pico.
**Solução:** Um limite de taxa foi atingido (endpoint, usuário ou grupo). Aumente ou reorganize os limites e implemente novas tentativas do cliente com recuo exponencial.

### Tabela de inferência vazia

**Cenário:** O registro de cargas úteis foi ativado, mas a tabela não contém linhas.
**Solução:** As linhas podem levar até uma hora para aparecer; cargas úteis acima de 1 MiB são ignoradas; a identidade que configura o recurso precisa de `USE CATALOG`, `USE SCHEMA` e `CREATE TABLE`. Não renomeie, exclua nem altere o esquema da tabela — o registro pode parar.

### Acesso negado à tabela de uso

**Cenário:** O proprietário do endpoint ativou o acompanhamento de uso, mas não consegue consultar `system.serving.endpoint_usage`.
**Solução:** Por padrão, somente administradores da conta podem ler a tabela; peça a um administrador que conceda acesso às tabelas de sistema ou compartilhe um painel.

## Dicas para o exame

> [!tip] Dicas para o exame
>
> - **Tabelas de inferência** = conteúdo de solicitações/respostas. **Tabelas de uso** = tokens, solicitante e contagens. **Limites de taxa** = limites de QPM/TPM (429 quando excedidos).
> - Chaves de limite de taxa: `endpoint`, `user`, `user_group`, `service_principal`; limite específico do usuário prevalece sobre o do grupo; o limite do endpoint é global.
> - Alavancas de custo: limites de taxa, orçamentos, pay-per-token vs. throughput provisionado, scale-to-zero, modelos menores, `ai_query()` em lote.
> - Mapeamento real: endpoints de serving legados → `system.serving.endpoint_usage`; Unity Gateway → `system.ai_gateway.usage`.

## Principais conclusões

- AI Gateway adiciona registro de cargas úteis, acompanhamento de uso, limites de taxa, guardrails, divisão de tráfego e fallbacks aos endpoints de serving
- Tabelas de inferência armazenam solicitações e respostas brutas para monitoramento, depuração e backtests de avaliação
- Tabelas de sistema de uso atribuem tokens e custos a solicitantes, endpoints e aplicativos
- Limites de taxa e orçamentos limitam gastos; modo de preço, tamanho do modelo, scale-to-zero e inferência em lote os reduzem
- Unity Gateway é o sucessor disponível em geral, com tabela de uso, orçamentos e políticas próprios

## Tópicos relacionados

- [Monitoramento on-line](./02-online-monitoring.md)
- [Scorers, juízes, feedback de SME e Agent Monitoring do MLflow](./03-mlflow-scorers-judges-and-feedback.md)
- [Mosaic AI e Foundation Models](../../../../../certifications/genai-engineer-associate/02-assembling-and-deploying-apps/01-mosaic-ai-and-foundation-models.md)
- [Visão geral da governança](../06-governance/01-governance-overview.md)

## Documentação oficial

- [Governança para endpoints de model serving (legado)](https://docs.databricks.com/aws/en/ai-gateway/overview-serving-endpoints)
- [Configurar governança em endpoints de model serving (legado)](https://docs.databricks.com/aws/en/ai-gateway/configure-ai-gateway-endpoints)
- [Tabelas de inferência para endpoints de serving (legado)](https://docs.databricks.com/aws/en/ai-gateway/inference-tables-serving-endpoints)
- [Governança de IA com Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/)
- [Limites de taxa do Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/rate-limits)
- [Gerenciar orçamentos para Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/budgets)

---

**[← Anterior: Scorers, juízes, feedback de SME e Agent Monitoring do MLflow](./03-mlflow-scorers-judges-and-feedback.md) | [↑ Voltar para Avaliação e monitoramento](./README.md)**
