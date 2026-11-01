---
title: Revisão final — GenAI Engineer Associate
type: final-review
tags:
  - genai-engineer-associate
  - final-review
  - exam-morning
status: published
---

# Revisão final — GenAI Engineer Associate (revisão de 20 minutos na manhã do exame)

> [!important]
> Guia do exame: publicado em 18 de março de 2026 (PDF republicado como "August 2026"). São 45 questões pontuadas de **múltipla escolha e seleção múltipla**, mais até 10 questões não pontuadas, em 90 minutos. Responda com base na interface de produto cobrada pelo guia (Agent Bricks, Agent Framework, Model Serving, Vector Search, Genie Spaces), mesmo que o produto tenha sido renomeado ou se tornado legado desde então.

## Fatos de 2 minutos que aparecem *com mais frequência*

- **Uma aplicação composta de IA = um endpoint, não N microsserviços** — retriever + reranker + LLM + ferramentas dentro de um único `ResponsesAgent`
- **`ResponsesAgent`** é a interface de agente recomendada; `ChatAgent` e `ChatModel` são *legados*
- **`log_model(..., resources=[...])`** → `agents.deploy()` provisiona o endpoint de serving, credenciais de curta duração, tabelas de inferência, rastreamento em tempo real e o Review App
- Tipos de índice do **Vector Search**: **Delta Sync** (atualizado automaticamente a partir de uma tabela Delta com CDF habilitado) versus **Direct Vector Access** (envia vetores via API); endpoint padrão = menor latência, otimizado para armazenamento = mais de 1 bilhão de vetores
- **Cobrança do FMAPI**: pagamento por token (sem reserva) versus throughput provisionado (capacidade reservada)
- **Janela de embeddings ≥ tamanho do chunk**: `bge-large-en` 512 tokens, `gte-large-en` 8192 tokens (ambos com 1024 dimensões)
- **Juízes que precisam de ground truth**: `Correctness`, `RetrievalSufficiency`, `ToolCallCorrectness`
- **Agent Bricks**: perguntas e respostas sobre documentos → Knowledge Assistant; vários agentes colaborando → Multi-Agent Supervisor; documentos → linhas estruturadas → Information Extraction
- **MCP**: primeiro gerenciado, depois externo (credenciais em um armazenamento governado), e personalizado (Databricks Apps) por último
- **AI Gateway** (cobrado em Evaluation and Monitoring): tabelas de inferência = payloads; tabelas de uso = tokens e solicitantes; limites de taxa = QPM/TPM, HTTP 429
- **PII é mascarada *antes* da geração de embeddings** — depois de indexada, ela pode ser recuperada para qualquer prompt

## Revisão rápida de 5 minutos por domínio (6 domínios)

### Seção 3 — Desenvolvimento de aplicações (30 %)

- Chamada de ferramenta = o LLM escolhe a função; cadeia = caminho fixo; agente = loop dirigido pelo LLM com `max_iterations`
- Multiagente + **Genie** para dados estruturados: Conversation API (`start-conversation` → consultar o estado → consultar o resultado), `GenieAgent` ou Genie MCP
- Fase de avaliação (conjunto de dados curado, ground truth) versus fase de monitoramento (traces ativos amostrados, sem ground truth)
- Escolha o **menor modelo que passe na avaliação** quando custo ou latência forem prioritários; consulte os model cards para saber a tarefa, o tamanho do contexto e a licença

### Seção 4 — Montagem e implantação de aplicações (22 %)

- Controle de acesso: **passthrough automático** (recursos declarados), **OBO** (permissões do usuário, escopos declarados), **secrets** (`{{secrets/scope/key}}`)
- Lote → `ai_query()` (ou AI Functions específicas para a tarefa); `failOnError => false` impede que uma única linha com erro faça o job falhar
- **Prompt Registry**: versões imutáveis, aliases mutáveis (`@production`) para promoção e rollback
- A memória fica em um armazenamento externo e durável (sessões/memória com suporte do Lakebase), nunca na memória do processo
- Interfaces: Databricks Apps (o backend mantém as credenciais), aplicação Genie para Teams, backend de bot do Slack

### Seção 1 — Design de aplicações (14 %)

- Defina primeiro a especificação de entrada/saída; escolha a **tarefa mais específica** (parágrafo → síntese = sumarização)
- Fatos por entidade → tabela/ferramenta com chave; conhecimento explicativo → retriever
- Ordem das ferramentas: identificar → recuperar → calcular → agir (ações irreversíveis por último, com aprovação)
- RAG para fatos que mudam; fine-tuning para estilo/formato/persona

### Seção 2 — Preparação de dados (14 %)

- Imagens digitalizadas → OCR (`pytesseract`); HTML → BeautifulSoup; PDFs → `pypdf` / PyMuPDF; SQL → `ai_parse_document`
- Menos embeddings → **chunks maiores** e **menos sobreposição**
- Métricas de recuperação: recall@k (encontrou?), precision@k (os resultados são relevantes?), MRR (o melhor veio primeiro?)
- Reranking = maior precisão no topo, com custo de latência

### Seção 6 — Avaliação e monitoramento (12 %)

- `mlflow.genai.evaluate(data, predict_fn, scorers)`; `@scorer` para verificações determinísticas, `make_judge()` para juízes LLM personalizados
- Divergência entre especialistas → rubricas + calibração, depois julgamentos alinhados em `mlflow.genai.evaluate()`
- Agent Monitoring: `scorer.register(...).start(sampling_config=ScorerSamplingConfig(sample_rate=...))`
- Alavancas de custo: limites de taxa, orçamentos, modo de cobrança, scale-to-zero, modelos menores, inferência em lote

### Seção 5 — Governança (8 %)

- Mascaramento: na ingestão (mais rápido e seguro), máscaras de coluna / filtros de linha na recuperação, PII `MASK` do gateway (mantém as solicitações respondíveis) versus `BLOCK`
- Entrada maliciosa: filtro de segurança, prompts resistentes a injeção, ferramentas com privilégio mínimo, limites de taxa, aprovação humana
- Licenciamento: licença dos dados, termos de serviço, base legal de privacidade, licença do modelo — tudo antes da ingestão; registre a procedência
- Texto problemático: corrija a fonte (exclua, substitua, redija) antes de depender de filtros de saída

## Lembretes sobre armadilhas comuns

| Armadilha | Resposta correta |
| :--- | :--- |
| "Aplicação composta = 3 endpoints encadeados" | Errado — um endpoint |
| "Criar uma subclasse de `ChatModel` para um agente novo" | Legado — use `ResponsesAgent` |
| "Usar `Correctness` para monitorar tráfego ativo" | Precisa de ground truth — use juízes sem expectativas |
| "Tirar a média das notas dos especialistas para resolver divergências" | Rubricas + calibração |
| "Armazenar prompts no Git / JSON e sobrescrevê-los na implantação" | Versões e aliases do MLflow Prompt Registry |
| "PAT no JavaScript da aplicação" | O backend da aplicação chama o endpoint |
| "Criar um wrapper MCP personalizado para tudo" | Servidores gerenciados + externos |
| "Redigir PII na recuperação" | Mascarar na ingestão (antes dos embeddings) |
| "Busca híbrida + reranking em uma barra de busca com latência crítica" | Desative ambos se a latência for a métrica prioritária |
| "`beautifulsoup` para PNGs digitalizados" | `pytesseract` (OCR) |

## Exame de hoje — planejamento dos 90 minutos

- **45 questões pontuadas em 90 minutos ≈ 2 minutos por questão**, menos o tempo destinado a eventuais questões não pontuadas
- **Seleção múltipla**: conte quantas respostas são necessárias; avalie cada opção individualmente
- Enunciados longos com cenários: identifique a **prioridade declarada** (latência, custo, qualidade, manutenção, governança) — ela determina a escolha entre duas opções plausíveis
- Em geral, vence o **recurso gerenciado mais completo da Databricks**: Agent Bricks ou MCP gerenciado em vez de código personalizado; tabelas de inferência em vez de logging próprio; Prompt Registry em vez de controle de versões improvisado

## Coma. Hidrate-se. Respire.

Você está pronto. Boa prova!

---

**[← Voltar aos recursos](./README.md)** | **[↑ Voltar a GenAI Engineer Associate](../README.md)**
