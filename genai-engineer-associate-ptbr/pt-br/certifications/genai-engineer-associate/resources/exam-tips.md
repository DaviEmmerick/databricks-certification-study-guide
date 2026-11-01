---
title: Dicas para o exame GenAI Engineer Associate
type: exam-tips
tags: [genai-engineer-associate, exam-tips, certification]
status: published
---

# Dicas e estratégias para o exame GenAI Engineer Associate

Estratégias práticas para passar no exame Databricks Certified Generative AI Engineer Associate na primeira tentativa. Alinhadas ao guia do exame publicado em 18 de março de 2026 ([PDF, edição de agosto de 2026](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-gen-ai-associate-exam-guide-august-2026.pdf)).

## Formato do exame

| Detalhe | Valor |
|---|---|
| Questões pontuadas | 45 |
| Itens não pontuados | Até 10 (não identificados; o tempo já está incluído) |
| Duração | 90 minutos |
| Nota de aprovação | Aprovado / reprovado — não há nota numérica de corte publicada |
| Código nos enunciados | Python (SQL para manipulação de dados fora de ML) |
| Formato das questões | **Múltipla escolha e seleção múltipla** ("Quais DUAS…") |
| Aplicação | Fiscalizado on-line (guia do exame); a página da certificação também lista centros de prova |
| Recursos de consulta | Nenhum |

## Pesos dos domínios e tempo de estudo

| Seção do guia do exame | Peso | ≈ Questões | Tempo de estudo recomendado |
|---|---|---|---|
| 3. Application Development | 30 % | ~13–14 | 10–12 horas |
| 4. Assembling and Deploying Applications | 22 % | ~10 | 8–10 horas |
| 1. Design Applications | 14 % | ~6 | 4–5 horas |
| 2. Data Preparation | 14 % | ~6 | 4–5 horas |
| 6. Evaluation and Monitoring | 12 % | ~5–6 | 4–5 horas |
| 5. Governance | 8 % | ~3–4 | 2–3 horas |

Application Development e Assembling and Deploying Applications representam mais da metade do exame — priorize esses domínios, mas não deixe de estudar os itens adicionados em 2026 (Agent Bricks, MCP, juízes e scorers), concentrados nas seções menores.

## Gerenciamento do tempo

| Etapa | Tempo | Meta |
|---|---|---|
| Primeira passada (responder a todas) | 60 min | ~80 s por questão (os itens não pontuados também estão incluídos) |
| Revisão das questões sinalizadas | 20 min | Reavaliar as respostas incertas |
| Revisão final | 10 min | Verificar se cada questão de seleção múltipla tem o número exigido de respostas |

Sinalize as questões sobre as quais estiver em dúvida e continue. Não gaste mais de 2 minutos em uma única questão durante a primeira passada.

## Questões de seleção múltipla

- O enunciado informa quantas respostas selecionar ("Quais DUAS ações…") — escolha exatamente essa quantidade.
- Avalie cada opção **individualmente** como verdadeira ou falsa para o cenário e, então, selecione as verdadeiras.
- Exemplo oficial: para reduzir 150 milhões de embeddings para menos de 100 milhões, escolha **aumentar o tamanho dos chunks** *e* **diminuir a sobreposição**.
- Exemplo oficial: para uma fonte de internet gerenciada e uma API de terceiros com chave, escolha **configurar o servidor MCP gerenciado** *e* **registrar o servidor MCP externo com sua chave em um armazenamento de secrets**.

## Tópicos essenciais

### É obrigatório saber (alta frequência)

- [ ] **Etapas de um pipeline RAG** — carregar → analisar/limpar → dividir em chunks → gerar embeddings → gravar no Delta → indexar → recuperar → (reranquear) → ampliar o contexto → gerar
- [ ] **Tipos de índice do Vector Search** — Delta Sync (sincroniza automaticamente a partir de uma tabela Delta com CDF habilitado) versus Direct Vector Access (você faz upsert dos vetores)
- [ ] **Tipos de endpoint** — padrão (menor latência, ~320 milhões de vetores em 768 dimensões) versus otimizado para armazenamento (mais de 1 bilhão de vetores, indexação mais rápida, ~250 ms de latência adicional)
- [ ] **Consistência dos embeddings** — o mesmo modelo na indexação e na consulta; janela ≥ tamanho do chunk
- [ ] **Foundation Model APIs** — pagamento por token (sem configuração, latência variável) versus throughput provisionado (capacidade reservada)
- [ ] **`ResponsesAgent` + MLflow Tracing** — interface de agente recomendada; `ChatAgent` / `ChatModel` são legados
- [ ] **`log_model(..., resources=[...])`** — recursos declarados permitem credenciais automáticas durante a implantação
- [ ] **`mlflow.genai.evaluate()`** — conjunto de dados + `predict_fn` + scorers; saiba quais juízes precisam de ground truth
- [ ] **Compensações na divisão em chunks** — tamanho, sobreposição, estrutura, semântica, relação pai-filho

### É recomendável saber (frequência média)

- [ ] **Agent Bricks** — Knowledge Assistant (perguntas e respostas sobre documentos com citações), Multi-Agent Supervisor (coordena Genie, KA e ferramentas), Information Extraction (documentos → linhas com estrutura definida por schema)
- [ ] **Genie em sistemas multiagente** — Genie Agent (antigo Genie Space) para dados estruturados via Conversation API, `GenieAgent` ou MCP
- [ ] **Servidores MCP** — primeiro gerenciados, depois externos, e personalizados (Databricks Apps) por último
- [ ] **Controle de acesso** — passthrough automático versus on-behalf-of-user versus secrets (`{{secrets/scope/key}}`)
- [ ] **`ai_query()`** — inferência em lote sobre tabelas; `failOnError`, `responseFormat`, `modelParameters`
- [ ] **Prompt Registry** — versões imutáveis, aliases mutáveis (`@production`) para promoção e rollback
- [ ] **AI Gateway** — tabelas de inferência (payloads), tabelas de uso (tokens, solicitantes), limites de taxa (QPM/TPM, HTTP 429)
- [ ] **Busca híbrida e reranking** — recuperação por palavras-chave + vetores; reranking com cross-encoder aumenta a precisão, mas custa latência
- [ ] **Guardrails** — filtragem de segurança, PII `MASK` versus `BLOCK`, prompts resistentes a prompt injection, ferramentas com privilégio mínimo

### É bom saber (frequência mais baixa)

- [ ] **HyDE, recuperação de múltiplas consultas, compressão contextual**
- [ ] **Scorers personalizados** — `@scorer` (determinístico) versus `make_judge()` (juiz LLM personalizado); alinhamento do juiz com feedback de SMEs
- [ ] **Agent Monitoring** — `scorer.register(...).start(sampling_config=ScorerSamplingConfig(sample_rate=...))`
- [ ] **Memória persistente** — estado da sessão e memória de longo prazo em armazenamentos com suporte do Lakebase, não na memória do processo
- [ ] **Interfaces de agentes** — Databricks Apps; aplicação Genie para Microsoft Teams; backends de bots para Slack
- [ ] **Licenciamento** — licenças de dados, termos de serviço, licenças de modelos, metadados de procedência

## Armadilhas comuns do exame

| Armadilha | O que o exame avalia | Entendimento correto |
|---|---|---|
| Consistência do modelo de embeddings | É possível indexar e consultar com modelos diferentes? | **Não** — vetores de modelos diferentes são incompatíveis. |
| Métrica de similaridade | O que o Vector Search usa? | ANN (HNSW) com **distância L2**; normalize os embeddings se quiser ordenação por cosseno ([AI Search](https://docs.databricks.com/aws/en/ai-search/ai-search)). |
| O maior modelo sempre vence | O maior LLM é sempre o melhor? | Não — quando custo ou latência forem a prioridade declarada, escolha o menor modelo que passe na avaliação. |
| Documentos digitalizados | Qual pacote extrai texto de imagens `.png` digitalizadas? | **OCR — `pytesseract`**; BeautifulSoup, scrapy e pyquery são ferramentas de HTML. |
| Fatos por entidade | Datas de entrega por pedido — armazenamento vetorial? | Não — uma **tabela indexada por ID** (feature store / lookup), não documentos. |
| Juízes com ground truth | `Correctness` pode avaliar tráfego ativo? | Não — precisa de expectativas. Use `RelevanceToQuery`, `RetrievalGroundedness`, `Safety`, `Guidelines` em produção. |
| Divergência entre SMEs | Tire a média das avaliações? | Não — defina **rubricas**, **calibre** os SMEs e use julgamentos alinhados em `mlflow.genai.evaluate()`. |
| Promoção de prompts | Mescle branches ou sobrescreva arquivos? | **Versões e aliases de prompts do MLflow**. |
| Tokens no navegador | Um Databricks App pode chamar um agente de JavaScript usando um PAT? | Não — o **backend da aplicação** chama o endpoint; as credenciais nunca chegam ao navegador. |
| Wrapper MCP personalizado | Envolva ferramentas gerenciadas e de terceiros em um único servidor personalizado? | Não — use o servidor **gerenciado** e registre o servidor **externo**. |
| Busca com latência crítica | Ative a busca híbrida e o reranking? | Não quando a latência for a métrica mais importante — ambos aumentam a latência. |
| Domínio do AI Gateway | O rastreamento do gateway é um tópico de Governance? | O guia do exame lista tabelas de inferência, tabelas de uso e limitação de taxa em **Evaluation and Monitoring**. |

## Números de referência rápida

| Item | Valor |
|---|---|
| Questões pontuadas / tempo | 45 / 90 minutos (+ até 10 itens não pontuados) |
| `databricks-bge-large-en` | Janela de 512 tokens, 1024 dimensões |
| `databricks-gte-large-en` | Janela de 8192 tokens, 1024 dimensões |
| Endpoint padrão versus otimizado para armazenamento | ~320 milhões versus mais de 1 bilhão de vetores em 768 dimensões |
| Subagentes e ferramentas do Supervisor | Até 50 |
| Tabelas do Genie Agent | Até 50 tabelas, views ou metric views |
| Scorers de monitoramento em produção | Até 20 por experimento |
| Limites de taxa por endpoint | Até 20 (5 específicos por grupo); somente período de renovação `minute` |
| Latência da tabela de inferência | Linhas disponíveis em cerca de uma hora; payloads acima de 1 MiB não são registrados |

## Na véspera do exame

- [ ] Revise a tabela de pesos dos domínios — dedique tempo extra a Application Development e Assembling and Deploying Applications.
- [ ] Releia as [tabelas de cobertura do guia do exame](../README.md#tópicos-de-estudo) no README de cada pasta e revise os itens que não conseguir explicar.
- [ ] Revise quais juízes do MLflow precisam de ground truth e a diferença entre `@scorer` e `make_judge()`.
- [ ] Revise o Agent Bricks: qual brick atende a cada problema.
- [ ] Revise as diferenças entre servidores MCP gerenciados, externos e personalizados.
- [ ] Confirme o acesso à sua conta de exame e a instalação do software de fiscalização.
- [ ] Durma 8 horas.

## Durante o exame

- [ ] Identifique a prioridade declarada (latência, custo, qualidade, manutenção, governança) antes de ler as opções.
- [ ] Nas questões de seleção múltipla, conte quantas respostas são exigidas e avalie cada opção individualmente.
- [ ] Elimine opções que fixam secrets no código, expõem endpoints publicamente ou sobrescrevem o histórico.
- [ ] Prefira o recurso gerenciado da Databricks ao código personalizado quando ambos atenderem ao requisito.
- [ ] Diferencie métricas de recuperação (precision@k, recall@k, MRR) de métricas de geração (groundedness, relevance, correctness).
- [ ] Sinalize as questões incertas e prossiga.

---

[← Voltar aos recursos](./README.md)
