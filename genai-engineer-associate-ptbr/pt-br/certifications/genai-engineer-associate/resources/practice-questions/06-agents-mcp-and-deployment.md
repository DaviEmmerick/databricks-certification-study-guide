---
title: "Questões de prática — Agentes, MCP e implantação (guia de 2026)"
type: practice-questions
tags: [genai-engineer-associate, practice-questions, application-development, assembling-and-deploying-applications, mcp]
status: published
---

# Questões de prática — Agentes, MCP e implantação (guia de 2026)

15 questões sobre itens do guia do exame de 2026 nas áreas Application Development e Assembling and Deploying Applications: MLflow + Agent Framework, Genie em sistemas multiagente, seleção de modelos, servidores MCP, controle de acesso de endpoints de serving, `ai_query()`, memória persistente, CI/CD, ciclo de vida de prompts, interfaces de usuário e chains pyfunc com pré e pós-processamento. Material de estudo: [Sistemas agênticos](../../01-application-development/05-agentic-systems-mlflow-and-genie.md), [Seleção de modelos](../../01-application-development/06-model-selection-and-response-quality.md), [Servidores MCP](../../02-assembling-and-deploying-apps/04-mcp-servers.md), [Controle de acesso e inferência em lote](../../02-assembling-and-deploying-apps/05-access-control-and-batch-inference.md), [Memória, CI/CD, prompts e interfaces](../../02-assembling-and-deploying-apps/06-memory-cicd-prompts-and-interfaces.md).

---

## Questão 1 *(Fácil)*: Interface de criação de agentes *(guia de 2026)*

**Questão**: Uma equipe está criando um novo agente que chama ferramentas, será registrado no MLflow e implantado na Databricks. Qual interface do MLflow a Databricks e o MLflow recomendam para novos agentes?

A) `mlflow.pyfunc.ChatModel`  
B) `mlflow.pyfunc.ChatAgent`  
C) `mlflow.pyfunc.PythonModel` com um schema JSON personalizado  
D) `mlflow.pyfunc.ResponsesAgent`  

> [!success]- Resposta
> **Resposta correta: D**
>
> `ResponsesAgent`.
>
> Desde o MLflow 3.0, o MLflow recomenda `ResponsesAgent` em vez de `ChatModel`, e a Databricks trata `ChatAgent` e `ChatModel` como interfaces legadas. `ResponsesAgent` oferece assinatura inferida, streaming e itens de saída de chamadas de ferramentas compatíveis com avaliação e monitoramento da Databricks.

---

## Questão 2 *(Média)*: Genie em um sistema multiagente *(guia de 2026)*

**Questão**: Um supervisor LangGraph precisa responder a perguntas sobre receita usando tabelas de vendas selecionadas, sem que o LLM escreva SQL bruto em tabelas desconhecidas. O que o engenheiro deve adicionar?

A) Um worker `GenieAgent` apontado para um Genie Space criado sobre as tabelas de vendas, declarado como recurso `DatabricksGenieSpace`  
B) Um índice Vector Search sobre as linhas das tabelas de vendas  
C) Uma função UC que retorne `SELECT *` de cada tabela de vendas  
D) Um LLM maior com os schemas das tabelas incluídos no system prompt  

> [!success]- Resposta
> **Resposta correta: A**
>
> Um worker Genie associado a um Genie Space selecionado.
>
> Genie transforma perguntas em linguagem natural em SQL sobre tabelas selecionadas, com instruções e consultas de exemplo, e aplica as permissões do Unity Catalog. Declarar `DatabricksGenieSpace` permite que a implantação provisione credenciais. Incorporar as linhas das tabelas ou despejar tabelas inteiras não é preciso nem eficiente.

---

## Questão 3 *(Média)*: Genie Conversation API *(guia de 2026)*

**Questão**: O backend de um bot do Slack usa a Genie Conversation API. Qual prática é recomendada pela Databricks?

A) Reutilizar uma conversa para todos os usuários, compartilhando o contexto  
B) Iniciar uma conversa para cada sessão de usuário e consultar o status da mensagem com intervalos crescentes  
C) Chamar `start-conversation` para cada pergunta de acompanhamento dentro da mesma sessão  
D) Tentar novamente as solicitações com falha imediatamente, em um loop apertado  

> [!success]- Resposta
> **Resposta correta: B**
>
> Uma conversa nova por sessão; consulte o estado com backoff.
>
> Reutilizar threads entre sessões reduz a precisão devido à reutilização indesejada do contexto. Perguntas de acompanhamento na mesma sessão devem ser enviadas ao endpoint de mensagens da conversa existente. A API não tenta novamente as solicitações por você; use backoff exponencial, não loops apertados.

---

## Questão 4 *(Média)*: Avaliação versus monitoramento *(guia de 2026)*

**Questão**: Qual afirmação contrasta corretamente as fases de avaliação e monitoramento de uma aplicação GenAI?

A) A avaliação usa tráfego ativo; o monitoramento usa um conjunto de dados selecionado  
B) A avaliação e o monitoramento precisam usar scorers diferentes  
C) A avaliação é executada antes do lançamento em um conjunto de dados selecionado, que pode incluir ground truth; o monitoramento pontua uma amostra de traces ativos após o lançamento, em geral sem ground truth  
D) Depois que a aplicação é implantada, o monitoramento substitui a necessidade de avaliação  

> [!success]- Resposta
> **Resposta correta: C**
>
> Conjunto de dados selecionado antes do lançamento versus amostras de traces ativos depois.
>
> As fases diferem nos dados, na cobertura e na disponibilidade de ground truth, mas os mesmos scorers podem ser executados em ambas; problemas encontrados no monitoramento também alimentam novas linhas de avaliação.

---

## Questão 5 *(Média)*: Tamanho do contexto de embeddings *(guia de 2026)*

**Questão**: Os documentos são divididos em chunks de no máximo 512 tokens, e custo e latência são mais importantes que qualidade. Qual escolha de modelo de embeddings é a mais adequada?

A) Contexto 32768, modelo de 14 GB, 4096 dimensões  
B) Contexto 514, modelo de 0,44 GB, 768 dimensões  
C) Contexto 2048, modelo de 11 GB, 2560 dimensões  
D) Contexto 512, modelo de 0,13 GB, 384 dimensões  

> [!success]- Resposta
> **Resposta correta: D**
>
> O menor modelo, com contexto de 512 tokens e menor dimensionalidade.
>
> A janela só precisa comportar chunks de 512 tokens. Janelas, modelos e dimensões maiores aumentam o custo e a latência sem trazer benefícios para esses chunks (questão 4 do exemplo oficial).

---

## Questão 6 *(Fácil)*: Escolher o tipo de servidor MCP *(guia de 2026)*

**Questão**: Um agente precisa executar análises em linguagem natural sobre dados governados do workspace. A Databricks já fornece um servidor MCP gerenciado para isso. O que o engenheiro deve fazer?

A) Usar o servidor Genie MCP gerenciado fornecido pela Databricks  
B) Criar um servidor MCP personalizado no Databricks Apps que chame a SQL API  
C) Registrar a REST API do Databricks SQL como servidor MCP externo  
D) Fornecer ao agente um personal access token para que ele chame endpoints REST diretamente  

> [!success]- Resposta
> **Resposta correta: A**
>
> Use o servidor MCP gerenciado.
>
> Quando a Databricks já fornece o recurso necessário, um servidor MCP gerenciado exige menos manutenção e mantém a aplicação das permissões do Unity Catalog. Wrappers personalizados ou externos acrescentam código e credenciais que precisam ser mantidos.

---

## Questão 7 *(Difícil)*: Credenciais de MCP externo *(guia de 2026)*

**Questão**: Um serviço de busca terceirizado fornece seu próprio servidor MCP, que exige uma chave de API. Qual integração reduz a manutenção e protege a chave?

A) Inserir a chave diretamente no arquivo Python do agente  
B) Registrar o servidor como MCP externo e manter a chave em um armazenamento governado de secrets ou conexão do Unity Catalog referenciada pela configuração  
C) Colocar a chave no system prompt para que o modelo possa passá-la à ferramenta  
D) Recriar as ferramentas do fornecedor em um servidor MCP personalizado no Databricks Apps  

> [!success]- Resposta
> **Resposta correta: B**
>
> Registre-o como MCP externo, com a chave em um armazenamento governado.
>
> Registrar o servidor existente evita reescrevê-lo, e armazenar a credencial em um local governado a mantém fora do código e dos prompts. Depois, os chamadores recebem `EXECUTE` no serviço MCP, não acesso à chave.

---

## Questão 8 *(Média)*: Acesso a dados por usuário *(guia de 2026)*

**Questão**: Um agente implantado consulta tabelas protegidas por filtros de linha, e cada funcionário deve ver somente as linhas da própria região. Que método de autenticação o agente deve usar para essas consultas?

A) Passthrough automático de autenticação  
B) Token de service principal armazenado como secret  
C) Autenticação on-behalf-of-user com escopos de API declarados  
D) Personal access token do administrador do workspace  

> [!success]- Resposta
> **Resposta correta: C**
>
> Autenticação on-behalf-of-user.
>
> O OBO executa chamadas com a identidade do usuário final, portanto os filtros de linha e máscaras de coluna são aplicados por usuário. Passthrough e service principals dão o mesmo acesso a todos os usuários, e tokens de administrador violam o princípio do privilégio mínimo.

---

## Questão 9 *(Fácil)*: Declarar recursos *(guia de 2026)*

**Questão**: Um agente funciona em um notebook, mas retorna erros de permissão no Vector Search após ser implantado com passthrough automático de autenticação. Qual é a causa mais provável?

A) O MLflow autologging foi habilitado  
B) O endpoint usa cobrança Pay-per-token  
C) O modelo foi registrado no Unity Catalog  
D) O índice não foi declarado em `resources` quando o modelo foi registrado  

> [!success]- Resposta
> **Resposta correta: D**
>
> O índice não estava em `resources`.
>
> O passthrough automático provisiona credenciais de curta duração somente para recursos declarados no momento do registro, após verificar que o criador do endpoint tem acesso a eles. Registre novamente o modelo com o índice declarado e faça uma nova implantação.

---

## Questão 10 *(Média)*: Inferência em lote *(guia de 2026)*

**Questão**: Uma equipe precisa resumir 2 milhões de chamados de suporte todas as noites e gravar os resultados em uma tabela Delta; ninguém precisa aguardar respostas individuais. Qual é a abordagem mais adequada?

A) Executar `ai_query()` (ou `ai_summarize`) sobre a tabela de chamados em um job agendado  
B) Chamar um endpoint de agente em tempo real uma vez por chamado em um loop Python  
C) Implantar um novo endpoint Provisioned Throughput para cada lote de chamados  
D) Usar o Review App para processar os chamados  

> [!success]- Resposta
> **Resposta correta: A**
>
> Use `ai_query()` ou uma AI Function específica para a tarefa em um job agendado.
>
> Trata-se de uma carga de trabalho em lote: alto volume, execução agendada e sem usuários esperando. AI Functions aplicam o modelo a uma tabela via SQL ou pipeline, e `failOnError => false` impede que uma única linha com erro faça o job falhar.

---

## Questão 11 *(Média)*: Memória de conversação *(guia de 2026)*

**Questão**: Um agente servido por várias réplicas esquece os turnos anteriores quando as solicitações chegam a réplicas diferentes. O que resolve isso?

A) Aumentar o tamanho da carga de trabalho do endpoint  
B) Armazenar o estado da sessão em um armazenamento durável externo, indexado pelo ID da sessão, como um session store com suporte do Lakebase  
C) Aumentar a temperature do modelo  
D) Armazenar conversas em uma variável global do Python  

> [!success]- Resposta
> **Resposta correta: B**
>
> Persista o estado da sessão externamente, usando o ID da sessão como chave.
>
> A memória do processo se perde durante reinicializações e não é compartilhada entre réplicas. Um armazenamento durável indexado pelo ID da sessão ou thread permite que qualquer réplica carregue a conversa.

---

## Questão 12 *(Média)*: Promoção de prompts *(guia de 2026)*

**Questão**: Os prompts precisam ser atualizados em desenvolvimento, validados em staging, promovidos para produção após aprovação, manter o histórico completo e permitir rollback. Qual abordagem atende a esses requisitos?

A) Mesclar o branch de staging em production após a aprovação dos testes  
B) Sobrescrever um arquivo JSON no executor de CI a cada execução  
C) Registrar os prompts como versões do MLflow Prompt Registry e promovê-los usando aliases  
D) Sobrescrever uma tabela Delta de prompts a cada implantação  

> [!success]- Resposta
> **Resposta correta: C**
>
> Use versões de prompts do MLflow e aliases.
>
> O Prompt Registry mantém versões imutáveis e aliases móveis, como `staging` e `production`; promover e reverter versões significa mover aliases, e a aplicação carrega `prompts:/catalog.schema.name@production` (questão 7 do exemplo oficial).

---

## Questão 13 *(Difícil)*: Interface de usuário do agente *(guia de 2026)*

**Questão**: A equipe de suporte precisa fazer perguntas fundamentadas em PDFs internos por meio de uma aplicação web com autenticação corporativa, sem expor tokens de longa duração no navegador e respeitando as permissões de cada usuário. Qual design atende a todos esses requisitos?

A) Incluir um personal access token no JavaScript da aplicação e chamar o agente pelo navegador  
B) Copiar os PDFs para um bucket público para que o agente possa lê-los sem verificar a identidade  
C) Tornar o endpoint do agente público e protegê-lo com uma chave de API no front-end  
D) Usar um Databricks App cujo backend chame o endpoint do agente com as credenciais da aplicação ou do usuário autenticado  

> [!success]- Resposta
> **Resposta correta: D**
>
> O backend de um Databricks App chama o agente.
>
> O App autentica os usuários com a identidade corporativa e mantém as credenciais no servidor; a autorização do usuário aplica as permissões individuais. Todas as outras opções expõem secrets ou dados (questão 8 do exemplo oficial).

---

## Questão 14 *(Média)*: Pós-processamento em um modelo pyfunc *(guia de 2026)*

**Questão**: Um engenheiro encapsula uma chamada à Foundation Model API em um `mlflow.pyfunc.PythonModel` personalizado (os imports e `PROMPT` foram omitidos). O prompt pede ao LLM que responda com um JSON contendo `category` e `priority`. Um job downstream precisa que o modelo servido retorne uma tabela com exatamente essas duas colunas, mas recebe as strings brutas das respostas, algumas envolvidas em texto extra. Onde a correção deve ficar?

```python
class TicketClassifier(mlflow.pyfunc.PythonModel):
    def load_context(self, context):
        self.client = get_deploy_client("databricks")

    def predict(self, context, model_input, params=None):
        replies = []
        for ticket in model_input["ticket"]:
            prompt = PROMPT.format(ticket=ticket)
            resp = self.client.predict(
                endpoint="databricks-claude-sonnet-4-5",
                inputs={"messages": [{"role": "user", "content": prompt}]},
            )
            replies.append(resp["choices"][0]["message"]["content"])
        return replies
```

A) Em `load_context`, que o MLflow executa sobre a saída de cada predição antes de devolvê-la a quem chamou  
B) Na `signature` passada a `mlflow.pyfunc.log_model()`, declarando `category` e `priority` como saídas para que o MLflow converta cada resposta  
C) Na solicitação, definindo `temperature` como 0 para que toda resposta seja um JSON válido com as duas chaves  
D) No final de `predict`, extraindo o JSON de cada resposta, usando valores padrão quando a análise falhar e retornando um DataFrame com as duas colunas  

> [!success]- Resposta
> **Resposta correta: D**
>
> Faça o pós-processamento no final de `predict`.
>
> O pós-processamento transforma cada saída bruta do modelo no formato de que os consumidores precisam, por isso é executado a cada solicitação dentro de `predict`, depois da chamada ao LLM; o padrão de pyfunc personalizado da Databricks chama um helper `format_outputs()` logo antes do retorno. `load_context` é executado uma única vez, quando o modelo é carregado, e nunca sobre cada saída. Uma assinatura (`signature`) descreve a interface do modelo, e o MLflow valida as entradas com base nela, mas não valida nem converte as saídas. `temperature` 0 torna as respostas mais repetíveis, mas não garante o formato, então a análise com um valor padrão continua necessária.

---

## Questão 15 *(Difícil)*: Empacotar um arquivo de consulta para o pós-processamento *(guia de 2026)*

**Questão**: Um modelo pyfunc personalizado encaminha tickets de suporte. `redact()` mascara endereços de e-mail antes de o prompt ser montado, e o rótulo de categoria retornado pelo LLM é depois convertido em um ID de fila por meio de um arquivo JSON de consulta (os imports, `PROMPT`, `redact()` e `ask_llm()` estão definidos no mesmo arquivo). Um revisor exige que cada versão registrada do modelo mantenha exatamente o mapeamento com que foi avaliada, mesmo que alguém edite depois o arquivo no volume. O que o engenheiro deve mudar?

```python
class TicketRouter(mlflow.pyfunc.PythonModel):
    def load_context(self, context):
        self.client = get_deploy_client("databricks")
        with open("/Volumes/main/support/config/queues.json") as f:
            self.queues = json.load(f)

    def predict(self, context, model_input, params=None):
        queue_ids = []
        for ticket in model_input["ticket"]:
            prompt = PROMPT.format(ticket=redact(ticket))
            label = ask_llm(self.client, prompt).strip().lower()
            queue_ids.append(self.queues.get(label, "TRIAGE"))
        return pd.DataFrame({"queue_id": queue_ids})
```

A) Passar `artifacts={"queues": "/Volumes/main/support/config/queues.json"}` para `mlflow.pyfunc.log_model()` e abrir `context.artifacts["queues"]` em `load_context`  
B) Declarar o arquivo JSON no argumento `resources` de `mlflow.pyfunc.log_model()` para que a implantação o provisione junto com o modelo  
C) Mover a leitura do arquivo para `predict`, para que cada solicitação use o mapeamento mais recente do volume  
D) Colocar o mapeamento dentro do `input_example` passado a `mlflow.pyfunc.log_model()`, porque o exemplo de entrada é salvo com o modelo  

> [!success]- Resposta
> **Resposta correta: A**
>
> Inclua o arquivo como artefato do modelo e carregue-o de `context.artifacts`.
>
> Do jeito que está, o mapeamento fica fora do modelo registrado, então o comportamento de uma versão depende do conteúdo do volume no momento em que o modelo é carregado, e não do que foi avaliado. Os arquivos passados em `artifacts` são baixados para o diretório do modelo quando ele é registrado com `log_model()`, e `context.artifacts["queues"]` fornece o caminho local dessa cópia; carregá-lo em `load_context` mantém essa etapa única, enquanto `redact()` e a consulta ficam em `predict` porque dependem de cada solicitação. `resources` declara recursos da Databricks, como endpoints de serving e índices Vector Search, para que a implantação provisione o acesso; não empacota arquivos. Ler o volume em `predict` faz o oposto do requisito e acrescenta leitura de arquivo a cada solicitação, e um exemplo de entrada é uma solicitação de amostra, não uma configuração que `predict` possa ler.

---

**[← Anterior: Design de aplicações e preparação de dados](./05-design-and-data-preparation.md) | [↑ Voltar às questões de prática](./README.md) | [Próximo: Avaliação, monitoramento e governança](./07-evaluation-monitoring-and-governance.md) →**
