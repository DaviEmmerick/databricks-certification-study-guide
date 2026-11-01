---
title: Questões de prática — Ferramentas GenAI da Databricks
type: practice-questions
tags: [genai-engineer-associate, practice-questions, foundation-model-apis, mlflow-genai]
status: published
---

# Questões de prática — Ferramentas GenAI da Databricks

7 questões sobre Foundation Model APIs, MLflow AI Gateway e implantação de aplicações GenAI na Databricks.

[← Desenvolvimento de aplicações com LLMs](./03-llm-application-development.md) | [← Voltar às questões de prática](./README.md)

---

## Questão 1 *(Média)*: Tipos de endpoint da Foundation Model API

**Questão**: Uma equipe implanta um chatbot RAG em produção e precisa de baixa latência garantida, com limite previsível de throughput de 100 solicitações por segundo. Que tipo de endpoint da Foundation Model API deve usar?

A) Endpoint Pay-per-token — serverless, com escalabilidade automática  
B) Endpoint Provisioned Throughput — capacidade dedicada com throughput garantido  
C) Endpoint External Model — encaminha solicitações a um provedor terceirizado, como a OpenAI  
D) Endpoint Batch Inference — otimizado para grandes cargas de trabalho offline  

> [!success]- Resposta
> **Resposta correta: B**
>
> Endpoints Provisioned Throughput oferecem capacidade de computação dedicada com throughput garantido (tokens por segundo) e latência mais previsível — adequados para SLAs de produção. Endpoints Pay-per-token usam infraestrutura compartilhada com latência variável, sendo indicados para desenvolvimento e baixo volume. Endpoints External Model encaminham solicitações a provedores terceirizados. Endpoints Batch Inference são voltados a cargas offline, não a serving em tempo real.

---

## Questão 2 *(Fácil)*: Pay-per-token versus Provisioned Throughput

**Questão**: Um cientista de dados está criando um protótipo de um novo recurso GenAI e espera menos de 10 consultas por dia durante o desenvolvimento. Que tipo de endpoint da Foundation Model API minimiza o custo?

A) Provisioned Throughput — sempre oferece a menor latência  
B) External Model — mais barato por usar infraestrutura de terceiros  
C) Pay-per-token — sem custo de reserva antecipada; cobra apenas os tokens consumidos  
D) Batch Inference — mais barato por processar várias solicitações de uma vez  

> [!success]- Resposta
> **Resposta correta: C**
>
> Endpoints Pay-per-token não têm custo mínimo de reserva — você paga apenas pelos tokens processados. Por isso, são ideais para prototipagem e experimentação de baixo volume. Provisioned Throughput reserva capacidade dedicada, usada ou não, o que torna a opção cara para cargas de baixo volume. O custo de External Model depende do preço do provedor. Batch Inference foi projetado para grandes cargas offline, não para desenvolvimento interativo.

---

## Questão 3 *(Fácil)*: Finalidade do MLflow AI Gateway

**Questão**: Uma organização usa três provedores de LLM diferentes (OpenAI, Anthropic e Databricks Foundation Models). Ela quer uma API unificada que abstraia diferenças entre provedores, aplique limites de taxa e centralize o gerenciamento de credenciais. Qual componente da Databricks atende a essa necessidade?

A) MLflow Model Registry — armazena e versiona artefatos de modelos LLM  
B) Databricks Vector Search — oferece pesquisa unificada em várias bases de conhecimento  
C) Unity Catalog — governa o acesso a endpoints de LLM por meio de ACLs de tabelas  
D) MLflow AI Gateway (proxy do AI Playground) — API unificada de LLM com limitação de taxa e gerenciamento de credenciais  

> [!success]- Resposta
> **Resposta correta: D**
>
> O MLflow AI Gateway oferece uma API REST unificada que abstrai as diferenças entre provedores de LLM, centraliza o gerenciamento de chaves de API (assim, aplicações cliente nunca precisam ter credenciais de provedores diretamente) e aceita limitação de taxa. Isso simplifica arquiteturas com vários provedores de LLM. O Model Registry versiona modelos, mas não atua como proxy de chamadas a LLMs. O Vector Search cuida da recuperação, não da orquestração de LLMs. O Unity Catalog governa ativos de dados, não o roteamento de APIs de LLM.

---

## Questão 4 *(Média)*: Implantar uma chain do LangChain como endpoint do Model Serving

**Questão**: Um desenvolvedor registrou uma chain `RetrievalQA` do LangChain com `mlflow.langchain.log_model(chain, "rag-chain")`. Qual é o PRÓXIMO passo para implantá-la como endpoint REST na Databricks?

A) Registrar o modelo no MLflow Model Registry e, em seguida, criar um endpoint do Model Serving que aponte para o modelo registrado  
B) Chamar `mlflow.pyfunc.serve(model_uri)` no driver do cluster  
C) Exportar a chain como arquivo `.pkl` e carregá-la no DBFS  
D) Salvar o código-fonte da chain em um Databricks Repo e iniciar um pipeline de CI/CD  

> [!success]- Resposta
> **Resposta correta: A**
>
> O fluxo padrão de implantação na Databricks é: (1) registrar o modelo com `mlflow.langchain.log_model()`, (2) adicioná-lo ao Model Registry com `mlflow.register_model()`, (3) criar um endpoint do Model Serving na interface da Databricks ou pela REST API, apontando para a versão registrada do modelo. `mlflow.pyfunc.serve()` executa um servidor local, não um endpoint de produção da Databricks. Exportar como `.pkl` ou usar CI/CD com Repos não é o fluxo padrão de serving da Databricks.

---

## Questão 5 *(Difícil)*: MLflow Evaluate para GenAI

**Questão**: Um desenvolvedor chama `mlflow.evaluate()` para avaliar seu pipeline RAG em um conjunto de dados de teste. Qual argumento especifica que a avaliação deve usar métricas baseadas em juízes LLM, como faithfulness e answer relevance?

A) `model_type="regressor"`  
B) `extra_metrics=[faithfulness(), answer_relevance()]` com `model_type="question-answering"`  
C) `evaluator_config={"faithfulness": True}` com `model_type="classifier"`  
D) `model_type="question-answering"` com `evaluators="default"`  

> [!success]- Resposta
> **Resposta correta: B**
>
> Para usar métricas específicas de GenAI baseadas em juízes LLM em `mlflow.evaluate()`, informe `model_type="question-answering"` e adicione as métricas desejadas em `extra_metrics=[faithfulness(), answer_relevance()]`. Elas usam uma abordagem de LLM como juiz para pontuar as respostas. `model_type="regressor"` ou `"classifier"` habilita métricas de ML tabular, não métricas de GenAI. Não existe o parâmetro `evaluator_config={"faithfulness": True}`.

---

## Questão 6 *(Fácil)*: Tabelas de inferência para monitoramento de LLMs

**Questão**: Uma equipe quer registrar automaticamente todas as solicitações e respostas de um endpoint do Databricks Model Serving em uma tabela Delta para monitoramento. Qual recurso permite fazer isso?

A) MLflow autolog — captura automaticamente entradas e saídas do modelo em um experimento  
B) Logs de auditoria do Unity Catalog — registram todos os eventos de acesso a dados no sistema  
C) Tabelas de inferência — registram automaticamente payloads de solicitação/resposta do endpoint em uma tabela Delta  
D) Lakehouse Monitoring — cria perfis estatísticos de tabelas Delta segundo uma programação  

> [!success]- Resposta
> **Resposta correta: C**
>
> Tabelas de inferência são um recurso do Model Serving que grava automaticamente todas as solicitações e respostas em uma tabela Delta especificada. Isso permite monitorar saídas de LLMs, detectar desvio de dados e executar fluxos de avaliação subsequentes. MLflow autolog captura dados de treinamento, não o tráfego de serving. Os logs de auditoria do Unity Catalog acompanham metadados de acesso a dados, não o conteúdo dos payloads do endpoint. Lakehouse Monitoring cria perfis de tabelas Delta existentes, mas não captura o tráfego de serving.

---

## Questão 7 *(Difícil)*: Consultar Foundation Model APIs

**Questão**: Um desenvolvedor consulta a Databricks Foundation Model API usando Python. Qual código envia corretamente uma solicitação de conclusão de chat para `databricks-meta-llama-3-3-70b-instruct`?

A) `mlflow.models.predict(model_name="databricks-meta-llama-3-3-70b-instruct", messages=[{"role": "user", "content": "Explain RAG in one sentence."}])`  
B) `WorkspaceClient().serving_endpoints.query(name="databricks-meta-llama-3-3-70b-instruct", messages=[...])` usando o SDK da Databricks  
C) `OpenAI(api_key=<databricks-token>, base_url=f"{DATABRICKS_HOST}/serving-endpoints").chat.completions.create(model="databricks-meta-llama-3-3-70b-instruct", messages=[...])`  
D) B e C estão corretas  

> [!success]- Resposta
> **Resposta correta: D**
>
> As Databricks Foundation Model APIs são compatíveis com OpenAI; portanto, tanto o SDK da Databricks (`WorkspaceClient().serving_endpoints.query()`) quanto o cliente Python da OpenAI (apontado para o host da Databricks) são formas válidas de consultá-las. A opção A usa `mlflow.models.predict()`, que não é a API correta para consultar diretamente um endpoint de serving. A opção D identifica corretamente que B e C funcionam.

---

**[← Anterior: Questões de prática — Desenvolvimento de aplicações com LLMs](./03-llm-application-development.md) | [↑ Voltar às questões de prática — GenAI Engineer Associate](./README.md) | [Próximo: Design de aplicações e preparação de dados](./05-design-and-data-preparation.md) →**
