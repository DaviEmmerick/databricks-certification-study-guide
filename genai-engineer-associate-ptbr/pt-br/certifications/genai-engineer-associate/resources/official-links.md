---
title: Recursos oficiais — GenAI Engineer Associate
type: reference
tags: [genai-engineer-associate, official-links]
status: published
---

# Links oficiais de recursos e documentação

Lista selecionada de documentação oficial da Databricks, MLflow e recursos relacionados ao exame GenAI Engineer Associate. Links verificados em 26/10/2026. Vários produtos foram renomeados ou passaram para o status *legacy* em 2026; a coluna de observações informa o nome usado no exame.

## Certificação

| Recurso | URL |
|---|---|
| Página da certificação Databricks GenAI Engineer Associate | <https://www.databricks.com/learn/certification/genai-engineer-associate> |
| Guia do exame (PDF, edição de agosto de 2026 — exame publicado em 18 de março de 2026) | <https://www.databricks.com/sites/default/files/2026-10/databricks-certified-gen-ai-associate-exam-guide-august-2026.pdf> |
| Visão geral e perguntas frequentes sobre certificações | <https://www.databricks.com/learn/certification> |
| Notas de versão (outubro de 2026) | <https://docs.databricks.com/aws/en/release-notes/product/2026/october> |

## Foundation Model APIs e Model Serving

| Recurso | URL |
|---|---|
| Modelos fundacionais compatíveis (nomes dos endpoints, janelas de contexto) | <https://docs.databricks.com/aws/en/machine-learning/foundation-model-apis/supported-models> |
| Modelos pré-treinados no Unity Catalog e Marketplace | <https://docs.databricks.com/aws/en/generative-ai/pretrained-models> |
| Configurar acesso a recursos por meio de endpoints de model serving (secrets) | <https://docs.databricks.com/aws/en/machine-learning/model-serving/store-env-variable-model-serving> |
| AI Functions (`ai_query`, `ai_parse_document`, `ai_extract`, `ai_mask`, …) | <https://docs.databricks.com/aws/en/large-language-models/ai-functions> |
| Referência da função `ai_query` | <https://docs.databricks.com/aws/en/sql/language-manual/functions/ai_query> |

## Vector Search (agora Databricks AI Search)

| Recurso | URL |
|---|---|
| Visão geral do Databricks AI Search (antigo Vector Search) | <https://docs.databricks.com/aws/en/ai-search/ai-search> |
| Criar endpoints e índices do AI Search | <https://docs.databricks.com/aws/en/ai-search/create-ai-search> |
| Consultar um índice do AI Search | <https://docs.databricks.com/aws/en/ai-search/query-ai-search> |

## Agentes: Agent Framework, Agent Bricks, Genie, MCP

| Recurso | URL | Observação |
|---|---|---|
| Desenvolver agentes na Databricks (Agent Bricks) | <https://docs.databricks.com/aws/en/agents/> | Página inicial atual |
| Knowledge Assistant | <https://docs.databricks.com/aws/en/agents/agent-bricks/knowledge-assistant> | Legado desde 12/10/2026; cobrado no exame |
| Supervisor Agent (Multi-Agent Supervisor) | <https://docs.databricks.com/aws/en/agents/agent-bricks/multi-agent-supervisor> | Legado desde 12/10/2026; cobrado no exame |
| Information Extraction | <https://docs.databricks.com/aws/en/agents/agent-bricks/info-extraction> | A versão atual usa `ai_extract` |
| Ofertas legadas de agentes (mapa de substituições) | <https://docs.databricks.com/aws/en/agents/legacy/> | |
| Agent Bricks CLI (Beta) | <https://docs.databricks.com/aws/en/agents/custom-agents/agent-bricks-cli> | Novo caminho orientado a código |
| Implantar agentes na Databricks (Agent Runtime) | <https://docs.databricks.com/aws/en/agents/deploy/> | |
| Implantar um agente no Model Serving (`agents.deploy()`) | <https://docs.databricks.com/aws/en/agents/custom-agents/model-serving/deploy-agent> | Legado; cobrado no exame |
| Autenticação de agentes (Model Serving) | <https://docs.databricks.com/aws/en/agents/custom-agents/model-serving/agent-authentication-model-serving> | Passthrough, OBO, manual |
| Schema legado de agentes (`ChatAgent`, `ChatModel`) | <https://learn.microsoft.com/en-us/azure/databricks/generative-ai/agent-framework/agent-legacy-schema> | Migre para `ResponsesAgent` |
| Usar Genie em sistemas multiagente | <https://docs.databricks.com/aws/en/agents/custom-agents/model-serving/multi-agent-genie> | |
| Genie Agents API (Conversation API) | <https://docs.databricks.com/aws/en/genie-agents/conversation-api> | Genie Spaces foi renomeado para Genie Agents em 08/07/2026 |
| MCPs na Databricks | <https://docs.databricks.com/aws/en/agents/mcp-tools/> | |
| MCPs fornecidos pela Databricks | <https://docs.databricks.com/aws/en/agents/mcp-tools/built-in-mcp-services> | `system.ai.*` via Unity Gateway |
| Servidor MCP do Genie One | <https://docs.databricks.com/aws/en/agents/mcp-tools/genie-mcp> | `/api/2.0/mcp/genie` será desativado em 31/10/2026 |
| Servidores MCP externos | <https://docs.databricks.com/aws/en/ai-gateway/register-mcp-service> | |
| Servidores MCP personalizados (Databricks Apps) | <https://docs.databricks.com/aws/en/agents/mcp-tools/custom-mcp> | |
| Memória e sessões de agentes | <https://docs.databricks.com/aws/en/agents/custom-agents/stateful-agents> | Com suporte do Lakebase |
| Aplicação Databricks Genie no Microsoft Teams | <https://docs.databricks.com/aws/en/integrations/msft-teams> | Public Preview |

## MLflow para GenAI

| Recurso | URL |
|---|---|
| Introdução ao ResponsesAgent | <https://mlflow.org/docs/latest/genai/flavors/responses-agent-intro/> |
| Scorers e juízes LLM | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/concepts/scorers> |
| Juízes LLM integrados (quais precisam de ground truth) | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/concepts/judges/> |
| Scorers baseados em código (personalizados) | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/custom-scorers> |
| Alinhar juízes com feedback humano | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/align-judges> |
| Coletar feedback e criar conjuntos de dados | <https://docs.databricks.com/aws/en/mlflow3/genai/human-feedback/> |
| Configurar monitoramento de produção (Agent Monitoring) | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/production-monitoring> |
| Prompt Registry | <https://docs.databricks.com/aws/en/mlflow3/genai/prompt-version-mgmt/prompt-registry/> |
| Tutorial: avaliar e aprimorar um agente | <https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/evaluate-app> |

## AI Gateway, Unity Gateway e guardrails

| Recurso | URL |
|---|---|
| Governança para endpoints de model serving (AI Gateway, legado) | <https://docs.databricks.com/aws/en/ai-gateway/overview-serving-endpoints> |
| Configurar AI Gateway em endpoints de serving (rastreamento de uso, registro de payloads, limites de taxa, guardrails) | <https://docs.databricks.com/aws/en/ai-gateway/configure-ai-gateway-endpoints> |
| Tabelas de inferência para endpoints de serving | <https://docs.databricks.com/aws/en/ai-gateway/inference-tables-serving-endpoints> |
| Governança de IA com Unity Gateway | <https://docs.databricks.com/aws/en/ai-gateway/> |
| Rastreamento de uso do Unity Gateway (`system.ai_gateway.usage`) | <https://docs.databricks.com/aws/en/ai-gateway/usage-tracking> |
| Limites de taxa do Unity Gateway | <https://docs.databricks.com/aws/en/ai-gateway/rate-limits> |
| Gerenciar orçamentos do Unity Gateway | <https://docs.databricks.com/aws/en/ai-gateway/budgets> |
| Governar um MCP | <https://docs.databricks.com/aws/en/ai-gateway/govern-mcp-service> |

## Recursos de prática

| Recurso | URL |
|---|---|
| Databricks Academy e catálogo de treinamentos | <https://www.databricks.com/learn/training> |
| Databricks Free Edition (substitui a Community Edition descontinuada) | <https://docs.databricks.com/aws/en/getting-started/free-edition> |
| Limitações da Free Edition (sem Knowledge Assistant; um endpoint de AI Search) | <https://docs.databricks.com/aws/en/getting-started/free-edition-limitations> |
| Notebook de exemplo: Genie multiagente + LangGraph | <https://docs.databricks.com/aws/en/notebooks/source/generative-ai/langgraph-multiagent-genie.html> |

> [!note]
> A Databricks Community Edition foi descontinuada em 1º de janeiro de 2026 ([anúncio da comunidade Databricks](https://community.databricks.com/t5/announcements/psa-community-edition-retires-on-january-1-2026-move-to-the-free/m-p/141888)). Use a **Free Edition** para praticar na plataforma; a lista de verificação prática do guia do exame foi dimensionada para ela.

---

[← Voltar aos recursos](./README.md)
