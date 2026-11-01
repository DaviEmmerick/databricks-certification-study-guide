---
title: Questões de prática — Vector Search e embeddings
type: practice-questions
tags: [genai-engineer-associate, practice-questions, vector-search, embeddings]
status: published
---

# Questões de prática — Vector Search e embeddings

12 questões sobre modelos de embeddings, tipos de índice do Databricks Vector Search, métricas de similaridade e APIs de consulta.

[← Arquitetura RAG](./01-rag-architecture.md) | [Próximo: Desenvolvimento de aplicações com LLMs →](./03-llm-application-development.md)

---

## Questão 1 *(Média)*: Consistência do modelo de embeddings

**Questão**: Uma equipe cria um índice do Databricks Vector Search usando embeddings `databricks-bge-large-en`. Seis meses depois, quer consultar o índice usando `databricks-gte-large-en`. O que acontecerá?

A) A consulta será bem-sucedida, pois os dois modelos produzem vetores de 1024 dimensões  
B) A consulta falhará com um erro de incompatibilidade de dimensões  
C) O Databricks Vector Search converterá automaticamente os embeddings entre os modelos  
D) A consulta retornará resultados sem sentido, pois os modelos produzem vetores em espaços semânticos diferentes  

> [!success]- Resposta
> **Resposta correta: D**
>
> Os modelos de embeddings precisam ser consistentes na indexação e na consulta. Mesmo que dois modelos produzam vetores com a mesma dimensionalidade, eles são treinados de maneiras diferentes e representam a semântica em espaços vetoriais distintos. Consultar com outro modelo retorna similaridades de cosseno sem sentido, pois os espaços são incompatíveis. O Databricks Vector Search não converte embeddings automaticamente.

---

## Questão 2 *(Fácil)*: Índice Delta Sync versus Direct Access

**Questão**: Uma equipe de engenharia de dados já tem uma tabela Delta gerenciada com descrições de produtos, atualizada todas as noites por um pipeline do Lakeflow Declarative Pipelines. Ela quer criar um índice do Vector Search sobre essa tabela com o mínimo de esforço operacional. Que tipo de índice deve usar?

A) Índice Delta Sync — sincroniza automaticamente com a tabela Delta de origem  
B) Índice Direct Access — oferece controle programático total sobre operações de upsert  
C) Managed Index — a Databricks seleciona automaticamente o tipo de índice com base no volume de dados  
D) Delta Merge Index — mescla atualizações vetoriais com alterações na tabela Delta em tempo real  

> [!success]- Resposta
> **Resposta correta: A**
>
> Um índice Delta Sync detecta automaticamente alterações na tabela Delta de origem e as sincroniza com o índice vetorial. É ideal quando os dados já estão em uma tabela Delta gerenciada, pois elimina a necessidade de escrever pipelines de sincronização personalizados. Um índice Direct Access exige que a aplicação chame explicitamente APIs de upsert para adicionar ou atualizar vetores. "Managed Index" e "Delta Merge Index" não são tipos válidos de índice do Databricks Vector Search.

---

## Questão 3 *(Fácil)*: Caso de uso do índice Direct Access

**Questão**: Uma equipe desenvolve um sistema de suporte ao cliente em tempo real, no qual os embeddings são calculados em um pipeline de pré-processamento personalizado antes de serem armazenados. Que tipo de índice do Databricks Vector Search deve ser usado?

A) Índice Delta Sync com sincronização contínua habilitada  
B) Índice Direct Access — permite upsert manual pelo SDK  
C) Índice Streaming — projetado para atualizações vetoriais em tempo real  
D) Índice Delta Sync com sincronização acionada habilitada  

> [!success]- Resposta
> **Resposta correta: B**
>
> Índices Direct Access permitem que aplicações façam upsert programático de vetores usando o SDK Python ou a REST API. Essa é a escolha correta quando os embeddings são calculados externamente (fora do Databricks Vector Search) e enviados ao serviço. Índices Delta Sync só funcionam quando a origem é uma tabela Delta gerenciada pela Databricks. "Streaming Index" não é um tipo válido.

---

## Questão 4 *(Difícil)*: Similaridade de cosseno versus distância L2

**Questão**: Uma equipe está avaliando duas métricas de similaridade vetorial: similaridade de cosseno e distância L2. O modelo de embeddings produz vetores unitários normalizados por L2. Qual métrica produzirá resultados EQUIVALENTES e por quê?

A) Distância L2 — a magnitude é irrelevante quando os vetores estão normalizados  
B) Similaridade de cosseno — sempre supera L2 para embeddings de texto  
C) Similaridade de cosseno e distância L2 são equivalentes para vetores unitários normalizados por L2  
D) Produto escalar — sempre é preferível às métricas de cosseno e L2  

> [!success]- Resposta
> **Resposta correta: C**
>
> Para vetores unitários normalizados por L2 (magnitude = 1), a similaridade de cosseno e a distância L2 medem a mesma relação geométrica. Para vetores unitários, similaridade de cosseno = 1 − (L2² / 2), portanto as duas métricas geram classificações equivalentes. Muitos modelos modernos de embeddings produzem vetores normalizados, tornando as duas métricas intercambiáveis na prática.

---

## Questão 5 *(Fácil)*: Consultar um índice do Vector Search

**Questão**: Um desenvolvedor quer recuperar as 5 descrições de produtos mais semelhantes a uma consulta do usuário usando o SDK Python do Databricks Vector Search. Qual chamada de método está correta?

A) `index.search(query_text="user query", top_k=5)`  
B) `index.find_similar(input="user query", limit=5)`  
C) `index.query(text="user query", k=5, return_fields=["id"])`  
D) `index.similarity_search(query_text="user query", columns=["id", "text"], num_results=5)`  

> [!success]- Resposta
> **Resposta correta: D**
>
> O SDK Python do Databricks Vector Search usa `index.similarity_search(query_text=..., columns=[...], num_results=...)`. O parâmetro `columns` especifica quais colunas de metadados devem ser retornadas junto às pontuações de similaridade. As outras assinaturas de método (`search`, `query`, `find_similar`) não são métodos válidos do SDK do Vector Search.

---

## Questão 6 *(Média)*: Dimensionalidade dos embeddings

**Questão**: Uma equipe compara dois modelos de embeddings: o Modelo A produz vetores de 768 dimensões e o Modelo B produz vetores de 1536 dimensões. Qual afirmação é a MAIS precisa?

A) Maior dimensionalidade aumenta os custos de armazenamento e computação; a qualidade depende do treinamento do modelo, não apenas da quantidade de dimensões  
B) O Modelo B sempre produz melhor qualidade de recuperação, pois dimensões maiores capturam mais informações  
C) O Modelo A sempre oferece buscas mais rápidas, independentemente do hardware ou do tipo de índice  
D) A dimensionalidade precisa corresponder ao tamanho oculto do LLM para haver compatibilidade  

> [!success]- Resposta
> **Resposta correta: A**
>
> Maior dimensionalidade não implica automaticamente maior qualidade. Alguns modelos de 768 dimensões bem treinados superam modelos de 1536 dimensões mal treinados. Dimensões maiores aumentam os custos de armazenamento (tamanho do índice) e a latência das consultas. A dimensionalidade dos embeddings não precisa corresponder ao tamanho oculto do LLM; são componentes independentes de uma arquitetura RAG.

---

## Questão 7 *(Média)*: Filtragem por metadados no Vector Search

**Questão**: Um desenvolvedor consulta um índice do Vector Search para encontrar artigos de suporte semelhantes à pergunta de um usuário, mas somente na categoria `billing`. Qual chamada do SDK aplica corretamente esse filtro?

A) `index.similarity_search(query_text="...", num_results=5, filters="category = 'billing'")`  
B) `index.similarity_search(query_text="...", num_results=5, filters={"category": "billing"})`  
C) `index.similarity_search(query_text="...", num_results=5, where="category = billing")`  
D) `index.similarity_search(query_text="...", num_results=5, metadata_filter=["category:billing"])`  

> [!success]- Resposta
> **Resposta correta: B**
>
> O SDK do Databricks Vector Search aceita filtros de metadados como um dicionário: `filters={"column_name": "value"}`. Isso pré-filtra o índice antes do cálculo da similaridade e é mais eficiente que a filtragem posterior. Os outros formatos (`filters` como string SQL, `where`, `metadata_filter` como lista) não são parâmetros válidos do SDK do Databricks Vector Search.

---

## Questão 8 *(Média)*: Embeddings esparsos versus densos

**Questão**: Um desenvolvedor está avaliando BM25 (esparso) versus modelos de embeddings de texto (densos) para um sistema de busca de documentação técnica. Em que tipo de consulta BM25 tem uma vantagem distinta sobre embeddings densos?

A) Consultas sobre relações conceituais (por exemplo, "como funciona o gradiente descendente?")  
B) Consultas que exigem compreensão entre idiomas  
C) Consultas que contêm identificadores técnicos raros (por exemplo, "código de erro ERR_SOCKET_TIMEOUT_4092")  
D) Consultas que exigem raciocínio entre várias seções do documento  

> [!success]- Resposta
> **Resposta correta: C**
>
> BM25 é excelente para correspondência exata de termos, sendo ideal para tokens raros, códigos de produto, códigos de erro e nomes próprios que provavelmente não apareceram no treinamento do modelo de embeddings. Embeddings densos são melhores para consultas semânticas e conceituais. Para consultas que exigem raciocínio entre seções ou compreensão entre idiomas, em geral os embeddings densos são preferíveis.

---

## Questão 9 *(Fácil)*: Modos de sincronização de índices do Vector Search

**Questão**: Um índice Delta Sync está configurado com `pipeline_type="TRIGGERED"`. O que isso significa para o comportamento de sincronização do índice?

A) O índice é atualizado em tempo real à medida que linhas são inseridas na tabela Delta  
B) O índice é atualizado somente quando a Databricks detecta um problema de qualidade de dados na tabela de origem  
C) O índice é recriado do zero a cada 24 horas, em uma programação fixa  
D) A sincronização do índice precisa ser acionada manualmente pela API ou pela interface; não há atualização automática  

> [!success]- Resposta
> **Resposta correta: D**
>
> Um pipeline `TRIGGERED` exige que o usuário inicie explicitamente uma sincronização (pela interface da Databricks, REST API ou SDK) sempre que quiser atualizar o índice. Em contraste, o tipo de pipeline `CONTINUOUS` mantém o índice sincronizado automaticamente sempre que a tabela Delta muda. O modo acionado é adequado quando os requisitos de atualização são flexíveis e o controle de custos é importante.

---

## Questão 10 *(Difícil)*: Modo de falha da recuperação baseada em embeddings

**Questão**: Um pipeline RAG retorna documentos irrelevantes quando usuários fazem perguntas com abreviações técnicas desconhecidas para o modelo de embeddings (por exemplo, "Qual é o SLA para incidentes P1?"). Qual é a mitigação MAIS eficaz?

A) Expandir as abreviações na consulta antes de gerar os embeddings (pré-processamento/expansão da consulta)  
B) Aumentar o top-k de 3 para 20 para melhorar o recall  
C) Trocar a similaridade de cosseno pela similaridade de produto escalar  
D) Reduzir o tamanho do chunk para 64 tokens para obter correspondências mais granulares  

> [!success]- Resposta
> **Resposta correta: A**
>
> Expandir ou pré-processar a consulta — substituindo abreviações pelas formas completas ou adicionando sinônimos — melhora a qualidade dos embeddings para termos fora do vocabulário. Modelos de embeddings têm dificuldade com abreviações raras porque talvez não as tenham visto durante o treinamento. Aumentar k ajuda o recall, mas não a precisão de consultas semanticamente incompatíveis. Alterar a métrica de similaridade ou o tamanho dos chunks não resolve problemas de termos fora do vocabulário (OOV).

---

## Questão 11 *(Média)*: Cross-encoder versus bi-encoder

**Questão**: Um desenvolvedor compara modelos bi-encoder (dual encoder), usados na busca vetorial, a modelos cross-encoder, usados no reranking. Qual afirmação está CORRETA?

A) Bi-encoders processam conjuntamente pares de consulta e documento para obter maior precisão; cross-encoders codificam cada um separadamente para ganhar velocidade  
B) Cross-encoders codificam conjuntamente pares de consulta e documento para obter maior precisão; bi-encoders codificam cada um separadamente para ganhar velocidade  
C) As duas arquiteturas produzem resultados com a mesma qualidade; a escolha depende apenas do custo de armazenamento  
D) Cross-encoders são usados na indexação; bi-encoders são usados no reranking durante a consulta  

> [!success]- Resposta
> **Resposta correta: B**
>
> Bi-encoders codificam consultas e documentos de forma independente, produzindo vetores separados que podem ser comparados por produto escalar ou similaridade de cosseno. Isso permite recuperar resultados rapidamente em índices grandes. Cross-encoders recebem pares consulta-documento como entrada conjunta e produzem uma pontuação de relevância bem mais precisa — mas é necessário pontuar individualmente cada par candidato. Por isso, cross-encoders são usados no reranking (aplicado a um conjunto pequeno de candidatos), não na recuperação inicial em grande escala.

---

## Questão 12 *(Fácil)*: Endpoint do Vector Search

**Questão**: Antes de criar um índice do Databricks Vector Search, qual recurso precisa existir?

A) Um experimento MLflow para acompanhar a criação do índice  
B) Um schema do Unity Catalog para armazenar metadados vetoriais  
C) Um endpoint do Vector Search para servir o índice  
D) Uma tabela Feature Store para servir de base ao índice  

> [!success]- Resposta
> **Resposta correta: C**
>
> Um endpoint do Databricks Vector Search é um recurso computacional que hospeda um ou mais índices do Vector Search. O endpoint precisa ser criado antes de criar ou consultar qualquer índice e gerencia a infraestrutura de serving. Experimentos MLflow e tabelas Feature Store não são pré-requisitos do Vector Search.

---

**[← Anterior: Questões de prática — Arquitetura RAG](./01-rag-architecture.md) | [↑ Voltar às questões de prática — GenAI Engineer Associate](./README.md) | [Próximo: Questões de prática — Desenvolvimento de aplicações com LLMs](./03-llm-application-development.md) →**
