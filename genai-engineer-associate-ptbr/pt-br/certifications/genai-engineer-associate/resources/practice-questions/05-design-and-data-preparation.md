---
title: "Questões de prática — Design de aplicações e preparação de dados (guia de 2026)"
type: practice-questions
tags: [genai-engineer-associate, practice-questions, design-applications, data-preparation, agent-bricks]
status: published
---

# Questões de prática — Design de aplicações e preparação de dados (guia de 2026)

12 questões sobre itens adicionados ou enfatizados no guia do exame de 18 de março de 2026: Agent Bricks, seleção de tarefas para modelos, especificação de entrada/saída, ordem das ferramentas, pacotes de extração, seleção de fontes, filtragem, métricas de recuperação e reranking. Material de estudo: [Design de soluções](../../03-design-applications/02-solution-design-and-tool-planning.md), [Agent Bricks](../../03-design-applications/03-agent-bricks.md), [Documentos de origem e avaliação da recuperação](../../04-data-preparation/04-source-documents-extraction-and-retrieval-eval.md).

---

## Questão 1 *(Fácil)*: Adequação do Knowledge Assistant *(guia de 2026)*

**Questão**: Uma equipe de RH quer um chatbot que responda a perguntas de funcionários sobre 300 PDFs de políticas armazenados em um volume do Unity Catalog, com citações, pouco código e possibilidade de ajuste por especialistas de RH. Qual opção do Agent Bricks é a mais adequada?

A) Information Extraction  
B) Um agente LangGraph personalizado implantado com `agents.deploy()`  
C) Multi-Agent Supervisor com um único Genie Space  
D) Knowledge Assistant  

> [!success]- Resposta
> **Resposta correta: D**
>
> Knowledge Assistant.
>
> Knowledge Assistant cria um agente de perguntas e respostas sobre documentos, com citações de arquivos em um volume, e permite que SMEs o aprimorem com diretrizes em linguagem natural. Information Extraction gera linhas estruturadas, não conversas. Supervisor coordena vários agentes, e Genie responde a perguntas sobre dados estruturados, não sobre políticas em PDF. Um agente personalizado funcionaria, mas não é a opção que exige menos código.

---

## Questão 2 *(Média)*: Adequação do Multi-Agent Supervisor *(guia de 2026)*

**Questão**: Uma equipe de operações de vendas precisa de uma única interface de chat que responda a perguntas sobre métricas de pipeline em tabelas Delta e sobre políticas contratuais em documentos. Já existe um Genie Space para as tabelas e um Knowledge Assistant para os documentos. O que o engenheiro deve criar?

A) Um Multi-Agent Supervisor que delegue ao Genie Space e ao Knowledge Assistant  
B) Um novo Knowledge Assistant com as tabelas Delta exportadas para PDF  
C) Um agente Information Extraction sobre os contratos  
D) Um segundo Genie Space que inclua os PDFs dos contratos  

> [!success]- Resposta
> **Resposta correta: A**
>
> Um Multi-Agent Supervisor sobre os dois agentes existentes.
>
> O Supervisor coordena subagentes especializados — Genie para dados estruturados e Knowledge Assistant para documentos — e sintetiza uma única resposta, respeitando o acesso de cada usuário a cada subagente. Exportar tabelas para PDF elimina a estrutura; Information Extraction produz tabelas, não conversas; e Genie Spaces trabalham com tabelas, não PDFs.

---

## Questão 3 *(Fácil)*: Adequação do Information Extraction *(guia de 2026)*

**Questão**: Uma equipe jurídica precisa transformar 40.000 contratos em uma tabela Delta com as partes, a data de vigência e o prazo de renovação de cada contrato. Qual opção do Agent Bricks atende ao requisito?

A) Knowledge Assistant  
B) Information Extraction  
C) Multi-Agent Supervisor  
D) Genie Space  

> [!success]- Resposta
> **Resposta correta: B**
>
> Information Extraction.
>
> O resultado esperado é uma tabela estruturada com os mesmos campos para cada documento, exatamente o que Information Extraction (baseado em `ai_extract` na versão atual) produz. Knowledge Assistant e Supervisor retornam respostas conversacionais; Genie consulta tabelas que já existem.

---

## Questão 4 *(Média)*: Selecionar a categoria da tarefa *(guia de 2026)*

**Questão**: Uma equipe de produto quer classificar cada avaliação de cliente em uma de cinco categorias fixas (entrega, qualidade, preço, suporte, outros) para contabilizar os resultados em um dashboard. Em qual categoria de tarefa os modelos candidatos devem ser avaliados?

A) Sumarização  
B) Geração de texto para texto  
C) Classificação de texto  
D) Tradução  

> [!success]- Resposta
> **Resposta correta: C**
>
> Classificação de texto.
>
> Um conjunto fechado de rótulos caracteriza uma tarefa de classificação; avalie modelos (ou `ai_classify`) usando exemplos rotulados. Sumarização condensa o texto, e a geração de texto livre produz rótulos inconsistentes, difíceis de contabilizar.

---

## Questão 5 *(Média)*: Fatos estruturados versus documentos *(guia de 2026)*

**Questão**: Um assistente de suporte sobre pedidos responde bem a perguntas sobre políticas, mas frequentemente informa datas de entrega incorretas. As datas de entrega ficam armazenadas por pedido no sistema de pedidos. Que alteração de design resolveria o problema?

A) Adicionar uma exportação do sistema de pedidos em PDF ao índice vetorial  
B) Fazer fine-tuning do LLM com as datas de entrega do mês passado  
C) Instruir o modelo a adicionar sete dias à data do pedido  
D) Fornecer ao agente uma ferramenta de lookup ou tabela indexada pelo ID do pedido que retorne a data de entrega  

> [!success]- Resposta
> **Resposta correta: D**
>
> Use uma ferramenta de lookup ou tabela indexada por pedido para consultar esses fatos.
>
> Datas de entrega são fatos estruturados, específicos de cada entidade e que mudam com frequência. Devem ficar em uma tabela ou ferramenta consultada pelo ID, não em documentos com embeddings nem nos pesos do modelo. Uma regra fixa no prompt produz respostas incorretas sempre que o prazo de entrega varia.

---

## Questão 6 *(Difícil)*: Ordem das ferramentas *(guia de 2026)*

**Questão**: Um agente de TI tem três ferramentas: `lookup_employee` (retorna a etiqueta de ativo do notebook), `search_runbooks` e `open_ticket` (exige uma etiqueta de ativo). Qual ordem favorece um raciocínio confiável com várias etapas?

A) `lookup_employee`, depois `search_runbooks` e, por fim, `open_ticket` somente após a confirmação do usuário  
B) `search_runbooks`, depois `open_ticket` e, então, `lookup_employee` para confirmar a etiqueta do ativo  
C) Chamar `open_ticket` primeiro para fornecer rapidamente um número de chamado ao usuário e, depois, as outras ferramentas  
D) Chamar as três ferramentas em paralelo e deixar o modelo conciliar os resultados  

> [!success]- Resposta
> **Resposta correta: A**
>
> Identifique, recupere e, então, aja após a confirmação.
>
> `open_ticket` depende da etiqueta de ativo retornada por `lookup_employee`, e as operações de escrita devem ocorrer depois de todas as leituras. Deixar a ação irreversível para o final, condicionada à confirmação, evita agir com base em uma conclusão intermediária incorreta. Chamadas paralelas não respeitam essa dependência de dados.

---

## Questão 7 *(Fácil)*: OCR para imagens digitalizadas *(guia de 2026)*

**Questão**: Os documentos de origem de uma aplicação RAG são formulários digitalizados salvos como arquivos `.png`. Qual pacote Python extrai o texto com menos código?

A) beautifulsoup4  
B) pytesseract  
C) scrapy  
D) pyquery  

> [!success]- Resposta
> **Resposta correta: B**
>
> pytesseract.
>
> Imagens digitalizadas não têm camada de texto, portanto é necessário usar OCR; pytesseract encapsula o mecanismo de OCR Tesseract. BeautifulSoup, scrapy e pyquery analisam ou rastreiam HTML e não conseguem ler texto a partir de pixels.

---

## Questão 8 *(Média)*: Filtrar conteúdo supérfluo *(guia de 2026)*

**Questão**: A recuperação de um assistente de intranet retorna repetidamente chunks dominados por menus de navegação e rodapés com direitos autorais. Qual é a correção mais eficaz?

A) Aumentar `num_results` para que chunks úteis também sejam retornados  
B) Trocar para um modelo de embeddings maior  
C) Remover elementos de navegação, cabeçalho e rodapé antes da divisão em chunks e gerar novamente os embeddings do corpus  
D) Adicionar um reranker após a recuperação  

> [!success]- Resposta
> **Resposta correta: C**
>
> Remova o texto padrão antes de dividir o conteúdo em chunks e gere os embeddings novamente.
>
> O texto padrão que permanece nos chunks é incorporado aos embeddings junto ao conteúdo e polui a similaridade. Limpar os documentos durante a análise corrige a causa raiz. Aumentar o número de resultados adiciona ruído, um modelo de embeddings maior ainda incorpora o ruído e um reranker só pode reordenar candidatos ruidosos.

---

## Questão 9 *(Difícil)*: Gravar chunks para um índice Delta Sync *(guia de 2026)*

**Questão**: Um engenheiro criará um índice Delta Sync em um endpoint padrão do Vector Search a partir de texto dividido em chunks. Qual sequência está correta?

A) Criar o índice, depois criar a tabela Delta e, então, habilitar Change Data Feed  
B) Gerar embeddings dos chunks e gravá-los somente no índice usando a API Direct Vector Access  
C) Gravar os chunks em um arquivo JSON em um volume e apontar o índice para o volume  
D) Analisar e dividir o texto em chunks, gravá-los com uma chave primária em uma tabela Delta do Unity Catalog com Change Data Feed habilitado e, então, criar ou sincronizar o índice  

> [!success]- Resposta
> **Resposta correta: D**
>
> Divida em chunks, grave em uma tabela Delta com chave primária e CDF habilitado, e depois crie ou sincronize o índice.
>
> Um índice Delta Sync lê as alterações da tabela de origem, portanto a tabela Delta com chave primária e Change Data Feed precisa existir primeiro. Índices não são criados a partir de arquivos em um volume; a API Direct Vector Access é outro tipo de índice, preenchido diretamente por você.

---

## Questão 10 *(Média)*: Escolher a métrica de recuperação *(guia de 2026)*

**Questão**: A avaliação mostra que o chunk relevante aparece nos 20 primeiros resultados em 95 % das perguntas, mas geralmente fica na oitava posição ou abaixo. Qual métrica identifica esse problema e qual técnica o resolve?

A) Mean reciprocal rank (MRR); adicionar uma etapa de reranking  
B) Recall@20; aumentar a sobreposição dos chunks  
C) Faithfulness; reforçar o system prompt  
D) Precision@20; reduzir o número de documentos no índice  

> [!success]- Resposta
> **Resposta correta: A**
>
> MRR, corrigido com reranking.
>
> Um recall@20 alto mostra que o chunk foi encontrado; um MRR baixo indica que ele ficou em uma posição muito abaixo. Um reranker pontua conjuntamente a consulta e cada candidato e move o melhor chunk para o topo. Sobreposição e tamanho do índice não resolvem problemas de ordenação; faithfulness mede o gerador.

---

## Questão 11 *(Média)*: Ground truth para juízes de recuperação *(guia de 2026)*

**Questão**: Qual juiz integrado do MLflow precisa de expectativas (ground truth) para avaliar a recuperação?

A) RetrievalRelevance  
B) RetrievalSufficiency  
C) RetrievalGroundedness  
D) RelevanceToQuery  

> [!success]- Resposta
> **Resposta correta: B**
>
> RetrievalSufficiency.
>
> RetrievalSufficiency verifica se o contexto recuperado contém todos os fatos esperados e, portanto, precisa de expectativas. RetrievalRelevance, RetrievalGroundedness e RelevanceToQuery avaliam a solicitação, o contexto e a resposta sem ground truth.

---

## Questão 12 *(Difícil)*: Vector Search com latência crítica *(guia de 2026)*

**Questão**: Um varejista atende até 80 buscas por segundo em 100 milhões de itens; latência é a métrica mais importante e os custos iniciais de desenvolvimento são aceitáveis. Qual configuração é a mais adequada?

A) Endpoint otimizado para armazenamento com busca híbrida e reranking habilitados  
B) Endpoint padrão com busca híbrida e reranking habilitados  
C) Modelo de embeddings personalizado com fine-tuning em endpoint padrão, busca híbrida e reranking desabilitados  
D) Modelo de embeddings personalizado com fine-tuning em endpoint otimizado para armazenamento, busca híbrida e reranking desabilitados  

> [!success]- Resposta
> **Resposta correta: C**
>
> Embeddings ajustados por fine-tuning, endpoint padrão, busca híbrida e reranking desabilitados.
>
> Endpoints padrão oferecem a menor latência de consulta, enquanto endpoints otimizados para armazenamento adicionam cerca de 250 ms. Busca híbrida e reranking aumentam a latência, portanto devem ficar desabilitados. A equipe aceita o custo inicial, então um modelo de embeddings ajustado por fine-tuning melhora a precisão sem prejudicar a latência (questão 6 do exemplo oficial).

---

**[← Anterior: Ferramentas GenAI da Databricks](./04-databricks-genai-tools.md) | [↑ Voltar às questões de prática](./README.md) | [Próximo: Agentes, MCP e implantação](./06-agents-mcp-and-deployment.md) →**
