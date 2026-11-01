---
title: Seleção de modelos e qualidade das respostas
type: study-material
tags:
  - genai-engineer-associate
  - application-development
  - model-selection
  - model-cards
  - embeddings
  - response-quality
status: published
---

# Seleção de modelos e qualidade das respostas

## Visão geral

Seis itens de Desenvolvimento de aplicações tratam de *escolhas*: o melhor LLM para uma aplicação,
o comprimento de contexto de um modelo de embedding, um modelo de hub ou marketplace escolhido com
base em seu model card, o melhor modelo de acordo com métricas de experimentos e uma estratégia de
chunking com base nos resultados da avaliação — além de identificar qualitativamente problemas de
qualidade e segurança nas respostas. Todos seguem o mesmo método: **declare a restrição mais
importante, examine as evidências (model card, métricas, exemplos de saída) e escolha a menor opção
que atenda ao requisito.**

> [!abstract]
>
> - **Seleção de LLM** — considere adequação à tarefa, qualidade, latência, custo, janela de contexto, licença e opção de implantação (pagamento por token, throughput provisionado, modelo externo)
> - **Comprimento de contexto de embeddings** — compatibilize a entrada máxima do modelo com o tamanho dos seus chunks; uma janela maior que os chunks acrescenta custo e latência sem benefício
> - **Model cards** — metadados que informam tarefa, idiomas, comprimento do contexto, licença, dados de treinamento, resultados de avaliação e limitações
> - **Métricas de experimentos** — compare candidatos no mesmo conjunto de avaliação e com os mesmos scorers do MLflow; escolha pela métrica associada ao requisito e, em caso de empate, considere custo e latência
> - **Análise qualitativa** — leia saídas reais para encontrar alucinações, irrelevância, erros de formato, conteúdo inseguro e vazamento de dados antes de automatizar a pontuação

> [!tip] O que o exame avalia
>
> - Escolher um modelo quando o enunciado explicita uma prioridade (*“custo e latência importam mais que qualidade”*, *“precisa ser executado on-premises com licença aberta”*, *“precisa de contexto de 100 mil tokens”*)
> - Questão de exemplo oficial: chunks limitados a 512 tokens + prioridade para custo/latência → modelo de embedding de **512 tokens, menor tamanho e menor dimensão**
> - Ler o model card para confirmar tarefa, licença e limites antes da adoção
> - Identificar o modo de falha visível em uma resposta de exemplo (alucinação, fora do tópico, insegurança, violação de formato)

---

## Selecionar o melhor LLM para a aplicação

Item do guia do exame: *"Select the best LLM based on the attributes of the application to be developed."*

| Atributo da aplicação | O que implica para o modelo |
| :--- | :--- |
| **Tipo de tarefa** (sumarizar, classificar, extrair, conversar, programar) | Escolha um modelo treinado/ajustado por instruções para a tarefa; modelos pequenos geralmente bastam para classificação e extração |
| **Padrão de qualidade** | Modelos com maior capacidade (geralmente maiores) para raciocínio complexo; verifique por meio de avaliação, não apenas pelo tamanho |
| **SLA de latência** | Modelos menores, prompts mais curtos, streaming; throughput provisionado para latência previsível |
| **Throughput e custo** | Modelos menores; processamento em lote com `ai_query()`; pagamento por token para baixo volume com picos, throughput provisionado para alto volume constante |
| **Contexto necessário** (documentos longos, muitos chunks recuperados) | Um modelo cuja janela de contexto comporte prompt system + contexto recuperado + histórico + resposta |
| **Saída estruturada / uso de ferramentas** | Um modelo compatível com function calling e saídas estruturadas |
| **Residência de dados, licença, controle** | Modelos com licença aberta servidos no Databricks ou modelos externos roteados pelo gateway, se a política permitir |
| **Idiomas** | Confira no model card quais idiomas são compatíveis |

> [!warning] Common Mistake
> “Use o maior modelo” raramente é a resposta do exame. Quando o enunciado indicar que custo ou latência é a prioridade, escolha o **menor modelo que atenda ao padrão de qualidade** e comprove isso com uma execução de avaliação.

### Compromissos de tamanho e arquitetura

O domínio de Avaliação e monitoramento aborda novamente essa habilidade como *"Select an LLM choice (size and architecture) based on a set of quantitative evaluation metrics."*

| Escolha | Efeito típico |
| :--- | :--- |
| **Modelo denso maior** | Melhor raciocínio e seguimento de instruções; custo por token e latência maiores |
| **Modelo denso menor** | Mais barato e rápido; pode bastar para tarefas específicas — confirme com métricas |
| **Mixture-of-experts (MoE)** | Ativa um subconjunto de parâmetros por token, portanto a inferência custa menos que em um modelo denso de mesmo tamanho total |
| **Ajustado por instruções vs. base** | Modelo ajustado por instruções para chat e RAG; modelos base precisam de fine-tuning ou prompting intenso |
| **Modelo pequeno ajustado** | Pode igualar um modelo maior em uma tarefa específica com menor custo de serving; exige dados de treinamento e manutenção |

Decida com uma tabela **métrica de qualidade × latência × custo**, obtida no mesmo conjunto de
dados de avaliação, e escolha o modelo mais barato que ultrapasse o limite de qualidade.

## Selecionar o comprimento de contexto de um modelo de embedding

Item do guia do exame: *"Select an embedding model context length based on source documents, expected queries, and optimization strategy."*

O modelo de embedding deve aceitar o **chunk mais longo** que você vai converter (e as consultas).
Qualquer conteúdo além dessa janela é truncado e, portanto, perdido no vetor. Uma janela muito maior
que seus chunks custa mais memória e latência e, geralmente, implica uma dimensão de embedding maior,
sem benefício para a recuperação.

| Modelo hospedado pelo Databricks | Janela de embedding | Dimensão | Observações |
| :--- | :---: | :---: | :--- |
| `databricks-bge-large-en` | 512 tokens | 1024 | Retorna embeddings normalizados |
| `databricks-gte-large-en` | 8192 tokens | 1024 | Não retorna embeddings normalizados |
| `databricks-qwen3-embedding-0-6b` *(Public Preview)* | ~32K tokens | até 1024 (configurável) | Mais de 100 idiomas, compatível com instruções |

Valores de [Modelos foundation compatíveis](https://docs.databricks.com/aws/en/machine-learning/foundation-model-apis/supported-models).

### Regras de decisão

| Situação | Escolha |
| :--- | :--- |
| Chunks ≤ 512 tokens, com prioridade para custo e latência | O modelo de **512 tokens** com o menor tamanho/dimensão (resposta da questão de exemplo oficial) |
| Seções longas precisam permanecer intactas (cláusulas legais, FAQs longas) | Um modelo com janela longa *ou* refaça o chunking com recuperação parent-child |
| Corpus multilíngue | Um modelo multilíngue, confirmado pelo model card |
| Consultas muito menores que os chunks | Considere HyDE ou expansão de consultas em vez de uma janela maior |

> [!important]
> Use o **mesmo modelo de embedding** para indexação e consultas. Trocar de modelo exige gerar novamente os embeddings de todo o corpus (consulte [Modelos de embedding](../04-data-preparation/02-embeddings-models.md)).

## Selecionar um modelo de um hub ou marketplace usando model cards

Item do guia do exame: *"Select a model from a model hub or marketplace for a task based on model metadata/model cards."*

Origens de modelos no Databricks:

- **Foundation models hospedados pelo Databricks** — disponíveis como model services no schema `system.ai` e acessíveis pelas Foundation Model APIs ([model services do Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/))
- **Databricks Marketplace** — instale no Unity Catalog listagens de modelos de provedores externos e, depois, sirva-os ([modelos pré-treinados](https://docs.databricks.com/aws/en/generative-ai/pretrained-models))
- **Hugging Face Hub e outros hubs** — registre com MLflow (por exemplo, o flavor `transformers`), registre no Unity Catalog e sirva no Model Serving
- **Provedores externos** — roteie OpenAI, Anthropic e outros provedores pelo gateway com governança

O que verificar em um model card antes de escolher:

| Campo do model card | Por que é importante |
| :--- | :--- |
| **Uso previsto / tarefa** | Confirma se o modelo foi criado para sua tarefa (por exemplo, *summarization* vs. *text-generation*) |
| **Comprimento do contexto** | Deve comportar seus chunks (embedding) ou prompt + contexto + resposta (LLM) |
| **Idiomas** | Deve atender aos idiomas dos usuários e documentos |
| **Licença e restrições de uso** | Uso comercial, atribuição, redistribuição, uso das saídas — ignorar esses pontos cria risco jurídico (consulte [Governança](../06-governance/01-governance-overview.md)) |
| **Dados de treinamento** | Adequação ao domínio e possíveis questões de procedência dos dados |
| **Resultados de avaliação / benchmarks** | Um ponto de partida; confirme no *seu* conjunto de avaliação |
| **Tamanho e requisitos de hardware** | Determinam custo de serving e latência |
| **Limitações e viés** | Modos de falha conhecidos que devem ser testados e mitigados |

## Selecionar o melhor modelo com base em métricas de experimentos

Item do guia do exame: *"Select the best model for a given task based on common metrics generated in experiments."*

Execute cada candidato no **mesmo conjunto de dados de avaliação e com os mesmos scorers**, registre
cada execução no mesmo experimento MLflow e compare os resultados.

```python
import mlflow
from mlflow.genai.scorers import Correctness, RelevanceToQuery, Safety

candidates = {
    "small": "databricks-meta-llama-3-1-8b-instruct",
    "large": "databricks-claude-sonnet-4-5",
}

for label, endpoint in candidates.items():
    with mlflow.start_run(run_name=f"candidate-{label}"):
        mlflow.log_param("endpoint", endpoint)
        mlflow.genai.evaluate(
            data=eval_dataset,                      # same rows for every candidate
            predict_fn=make_predict_fn(endpoint),  # your app, parameterised by model
            scorers=[Correctness(), RelevanceToQuery(), Safety()],
        )
```

Como interpretar os resultados:

| Tipo de métrica | Exemplos | Use para… |
| :--- | :--- | :--- |
| **Qualidade (LLM judges)** | Correção, fundamentação, relevância, taxa de aprovação de diretrizes | Decidir se um candidato atende ao padrão |
| **Métricas clássicas de NLP** | Correspondência exata, F1, ROUGE (sumarização), BLEU (tradução), precisão (classificação) | Tarefas com uma resposta de referência e uma métrica bem definida |
| **Segurança** | Taxa de aprovação do safety judge, toxicidade | Gate obrigatório — um candidato reprovado é eliminado, independentemente da qualidade |
| **Operacional** | Latência (P50/P95), tokens por solicitação, custo por solicitação | Desempatar candidatos que atendem ao padrão de qualidade |

Regra: **filtre pela métrica principal do requisito e, depois, minimize custo e latência.**

## Selecionar uma estratégia de chunking com base nos resultados da avaliação

Item do guia do exame: *"Select chunking strategy based on model & retrieval evaluation."*
Trate o chunking como um hiperparâmetro e deixe as métricas de recuperação e resposta orientarem a decisão.

| Sinal da avaliação | Possível alteração no chunking |
| :--- | :--- |
| **Recall** do contexto / suficiência de recuperação baixos — fatos necessários não são recuperados | Chunks menores, mais sobreposição ou divisão de acordo com a estrutura de títulos |
| **Precisão** / relevância do contexto baixas — chunks recuperados são ruidosos | Chunks menores e mais focados; filtros de metadados; adicionar um re-ranker |
| As respostas não têm contexto ao redor mesmo quando o chunk certo é encontrado | Chunking parent-child (recupere o trecho pequeno, retorne a seção principal) |
| Chunks excedem a janela de embedding (truncamento) | Reduza o tamanho dos chunks para abaixo da janela do modelo ou troque para um modelo com janela maior |
| Há embeddings demais para o índice ou orçamento | Chunks maiores e menos sobreposição (questão de exemplo oficial para reduzir 150 milhões de embeddings) |

Os detalhes de cada estratégia estão em [Processamento e chunking de documentos](../04-data-preparation/01-document-processing-chunking.md); as métricas de recuperação estão em [Documentos de origem, extração e avaliação da recuperação](../04-data-preparation/04-source-documents-extraction-and-retrieval-eval.md).

## Avaliar respostas qualitativamente

Item do guia do exame: *"Qualitatively assess responses to identify common issues such as quality and safety."* Antes de automatizar a avaliação, **leia uma amostra de saídas reais** e rotule os problemas — os rótulos se tornarão suas diretrizes e judges personalizados.

| Problema | Como se manifesta | Causa típica | Correção típica |
| :--- | :--- | :--- | :--- |
| **Alucinação** | Afirmações confiantes que não constam do contexto recuperado | Instrução fraca de fundamentação, contexto ausente | “Answer only from context”, judge de fundamentação, melhor recuperação |
| **Irrelevância** | Responde a outra pergunta | Recuperação ruim ou consulta ambígua | Reescrita da consulta, judge de relevância |
| **Resposta incompleta** | Não aborda parte de uma pergunta com várias partes | Contexto truncado, chunk pequeno demais | Decomposição, `k` maior, chunks parent-child |
| **Violação de formato** | JSON inválido, tamanho incorreto, citação ausente | Instruções de formato flexíveis | Saída estruturada, parser + nova tentativa, few-shot |
| **Conteúdo inseguro** | Saída tóxica, prejudicial ou em desacordo com a política | Jailbreak, prompt inseguro | Guardrails de entrada/saída, safety judge |
| **Vazamento de dados** | PII ou secrets na resposta | Dados de origem sem redação | Redigir antes de gerar embeddings, guardrails de PII, mascaramento |
| **Desvio de tom / persona** | Voz inconsistente ou fora da marca | Prompt system fraco | Prompt system mais forte, judge de diretrizes |

## Casos de uso

- **Troca de modelo motivada por custo** — a avaliação mostra que um modelo 8B iguala a precisão de um modelo 70B em uma tarefa de classificação, com diferença de apenas um ponto; a equipe serve o modelo menor e reduz custos
- **Escolha de embeddings para chunks curtos** — chunks de 512 tokens e um SLA de latência levam à escolha de um modelo com janela de 512 e baixa dimensão, em vez de um modelo com janela de 32K
- **Verificação de licença** — a licença do model card proíbe o uso comercial, então a equipe escolhe outro modelo antes de adotá-lo
- **Variação do tamanho dos chunks** — três tamanhos de chunk são avaliados; o tamanho intermediário vence em suficiência de recuperação sem aumentar demais o índice

## Problemas e erros comuns

### Truncamento silencioso em embeddings

**Cenário:** o recall é baixo para chunks longos, mesmo que os documentos corretos estejam indexados.
**Correção:** os chunks excedem a janela do modelo de embedding, portanto o final de cada chunk nunca é convertido em embedding. Divida os chunks novamente para que fiquem abaixo do limite ou escolha um modelo com janela maior.

### Modelo vencedor do benchmark tem desempenho pior nos seus dados

**Cenário:** o modelo com os melhores resultados em benchmarks públicos tem desempenho inferior no conjunto de avaliação da equipe.
**Correção:** benchmarks do model card são apenas um ponto de partida. Sempre compare candidatos com seu próprio conjunto de dados e scorers no MLflow.

### Comparação de execuções com dados diferentes

**Cenário:** o modelo B parece melhor, mas foi avaliado em um conjunto de dados mais fácil.
**Correção:** fixe o mesmo conjunto de dados de avaliação (de preferência, um conjunto de dados de avaliação versionado no MLflow) e os mesmos scorers para todos os candidatos.

## Dicas para o exame

> [!tip] Dicas para o exame
>
> - A prioridade declarada é determinante: **custo/latência → menor modelo adequado**; **qualidade → modelo mais capaz que passe na avaliação**.
> - Janela de embedding ≥ tamanho do chunk, mas não muito maior quando o custo importa. **Chunks de 512 tokens → modelo de 512 tokens.**
> - Lista de verificação do model card: **tarefa, comprimento do contexto, idiomas, licença, dados de treinamento, avaliação, limitações**.
> - Compare modelos com o **mesmo conjunto de dados de avaliação e os mesmos scorers**; em caso de empate, considere latência e custo.
> - Recall baixo na recuperação → altere o **chunking**; fundamentação baixa com recall bom → ajuste o **prompt ou o modelo**.

## Principais conclusões

- Escolha LLMs conforme a adequação à tarefa, o padrão de qualidade, a latência, o custo, a janela de contexto, a licença e a opção de implantação — não apenas pelo tamanho
- Compatibilize a janela de embedding com o tamanho do chunk; `bge-large-en` = 512 tokens, `gte-large-en` = 8192 tokens, ambos com 1024 dimensões
- Model cards contêm as informações necessárias antes da adoção, inclusive licença e limitações
- Métricas de experimentos orientam escolhas de modelo e chunking quando todos os candidatos usam os mesmos dados e scorers
- A análise qualitativa de saídas reais identifica modos de falha que os scorers automatizados devem medir posteriormente

## Tópicos relacionados

- [Engenharia de prompts](./01-prompt-engineering.md)
- [Modelos de embedding](../04-data-preparation/02-embeddings-models.md)
- [Mosaic AI e Foundation Models](../02-assembling-and-deploying-apps/01-mosaic-ai-and-foundation-models.md)
- [Scorers, judges e feedback de SMEs do MLflow](../05-evaluation-and-monitoring/03-mlflow-scorers-judges-and-feedback.md)

## Documentação oficial

- [Modelos foundation compatíveis](https://docs.databricks.com/aws/en/machine-learning/foundation-model-apis/supported-models)
- [Modelos pré-treinados no Unity Catalog e Marketplace](https://docs.databricks.com/aws/en/generative-ai/pretrained-models)
- [LLM judges integrados](https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/concepts/judges/)
- [Tutorial: avaliar e aprimorar um agent](https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/evaluate-app)
- [Governança de IA com Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/)

---

**[← Anterior: Sistemas agentic com MLflow, Agent Framework e Genie](./05-agentic-systems-mlflow-and-genie.md) | [↑ Voltar para Desenvolvimento de aplicações](./README.md)**
