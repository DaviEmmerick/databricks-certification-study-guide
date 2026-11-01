---
title: Questões do Simulado 1 — GenAI Engineer Associate
type: mock-exam-questions
tags: [genai-engineer-associate, mock-exam]
status: published
---

# Simulado 1 — Questões

Defina um cronômetro de 90 minutos e responda às 45 questões antes de conferir as respostas.

[← Voltar às instruções do simulado](./README.md)

---

## Projetar soluções RAG (Questões 1–14)

---

## Questão 1 *(Média)*

**Questão**: Uma empresa varejista quer permitir que clientes façam perguntas em linguagem natural sobre seu catálogo de produtos (50.000 produtos, atualizado diariamente). As respostas precisam refletir o estoque de hoje. Qual arquitetura é a MAIS adequada?

A) Fazer fine-tuning mensal de um LLM com o catálogo de produtos e implantá-lo como chatbot  
B) Criar um pipeline RAG que recupere produtos relevantes de um índice Vector Search atualizado diariamente  
C) Armazenar todo o catálogo no system prompt e enviá-lo em cada solicitação  
D) Criar um modelo de classificação que associe perguntas a categorias de produtos  

> [!success]- Resposta
> **Resposta correta: B**
>
> RAG com um índice vetorial atualizado diariamente é ideal para conhecimento dinâmico e frequentemente atualizado. O fine-tuning exige novo treinamento, caro, para cada alteração do catálogo. Incluir 50.000 produtos no system prompt é inviável do ponto de vista computacional — excederia muito a janela de contexto de qualquer LLM. Um modelo de classificação associa perguntas a categorias, mas não responde a perguntas abertas sobre produtos.

---

## Questão 2 *(Média)*

**Questão**: Um desenvolvedor escolhe entre a divisão recursiva de texto por caracteres e a divisão em chunks de tamanho fixo para um corpus de contratos jurídicos estruturados, com cláusulas numeradas e bem definidas. Qual opção é a MAIS adequada?

A) Divisão recursiva de texto por caracteres — respeita os limites dos parágrafos  
B) Divisão em chunks de tamanho fixo — os contratos têm tamanho médio de cláusula conhecido  
C) Divisão semântica em chunks nos limites das cláusulas — alinha-se naturalmente à estrutura do documento  
D) Divisão por página — documentos jurídicos têm formatação consistente em cada página  

> [!success]- Resposta
> **Resposta correta: C**
>
> Contratos jurídicos têm limites de cláusula claramente definidos (por exemplo, "1.1", "2.3") que delimitam semanticamente obrigações distintas. Dividir nos limites das cláusulas produz chunks autocontidos, mais fáceis de incorporar e recuperar do que divisões de tamanho arbitrário. A divisão recursiva por caracteres e a divisão de tamanho fixo ignoram a estrutura semântica dos contratos. A divisão por página pode separar uma cláusula em vários chunks.

---

## Questão 3 *(Média)*

**Questão**: Um desenvolvedor configura o pipeline RAG com recuperação top-k=10. O LLM produz respostas prolixas e pouco focadas, que incluem informações de muitos chunks sem relação entre si. Qual ajuste é o MAIS adequado?

A) Aumentar k para 20 para dar mais contexto ao LLM  
B) Diminuir k para 3–5 e adicionar um reranker para garantir a inclusão dos chunks mais relevantes  
C) Trocar a similaridade de cosseno pela distância L2  
D) Aumentar o tamanho dos chunks para reduzir o número total de chunks recuperados  

> [!success]- Resposta
> **Resposta correta: B**
>
> Quando o LLM produz respostas pouco focadas por causa do excesso de chunks recuperados, a solução é reduzir k para passar ao LLM menos chunks, porém mais relevantes, e adicionar reranking para melhorar a qualidade dos chunks mantidos. Aumentar k agrava o problema. A métrica de similaridade não determina quantos chunks são ruidosos. Aumentar o tamanho dos chunks não reduz a quantidade enviada ao LLM — apenas torna cada chunk maior.

---

## Questão 4 *(Difícil)*

**Questão**: Um pipeline RAG recupera consistentemente bons resultados para perguntas factuais concretas, mas falha em perguntas abstratas como "Quais são os riscos estratégicos discutidos nos relatórios anuais?". Qual técnica MAIS melhora os resultados para consultas abstratas?

A) Reduzir o tamanho dos chunks para 64 tokens  
B) Usar HyDE — fazer o LLM gerar uma resposta hipotética e, então, gerar um embedding e recuperar documentos com base nela  
C) Trocar a recuperação densa por recuperação esparsa (BM25)  
D) Adicionar filtros de metadados para a categoria `strategic_risk`  

> [!success]- Resposta
> **Resposta correta: B**
>
> Consultas abstratas costumam ser mal representadas no "espaço de perguntas" dos embeddings. HyDE preenche essa lacuna fazendo o LLM gerar uma resposta hipotética, mais semelhante a documentos de resposta reais no espaço de embeddings. Chunks menores pioram a cobertura do contexto. BM25 tem desempenho inferior em consultas abstratas/semânticas do que em consultas por palavras-chave. Filtros de metadados exigem uma categoria previamente rotulada, que talvez não exista.

---

## Questão 5 *(Média)*

**Questão**: Um desenvolvedor usa `mlflow.evaluate()` para pontuar o pipeline RAG e obtém uma pontuação de faithfulness igual a 0.4 (escala de 0 a 1). O que isso indica?

A) 40 % dos documentos recuperados são relevantes para a pergunta  
B) Em 40 % das vezes, o LLM produz uma resposta não vazia  
C) As respostas do LLM estão fundamentadas apenas no contexto recuperado em 40 % das vezes, o que indica alucinações frequentes  
D) A etapa de recuperação encontra o documento correto em 40 % das vezes  

> [!success]- Resposta
> **Resposta correta: C**
>
> Faithfulness mede se a resposta gerada tem sustentação no contexto recuperado. Uma pontuação de 0.4 significa que não é possível atribuir 60 % do conteúdo gerado aos documentos recuperados — o LLM alucina com frequência. Essa é uma métrica de qualidade de geração, não de recuperação. A qualidade da recuperação é medida por precision@k e recall@k.

---

## Questão 6 *(Fácil)*

**Questão**: Uma equipe usa RAG para alimentar um chatbot interno de RH. As políticas de RH são atualizadas trimestralmente. Qual abordagem trata CORRETAMENTE as atualizações das políticas?

A) Retreinar o LLM com os novos documentos de políticas a cada trimestre  
B) Reindexar os documentos atualizados de políticas no índice Vector Search; não é necessário alterar o LLM  
C) Atualizar o system prompt com uma cópia completa das novas políticas  
D) Fazer fine-tuning da camada de embeddings do LLM com os novos documentos  

> [!success]- Resposta
> **Resposta correta: B**
>
> RAG separa conhecimento (o índice vetorial) de raciocínio (o LLM). Quando as políticas mudam, basta reindexar os documentos atualizados no armazenamento vetorial. Não é necessário alterar o próprio LLM. Incluir todo o texto das políticas no system prompt é impraticável em escala. Fine-tuning serve para mudanças de estilo, não para atualizações rotineiras de conhecimento.

---

## Questão 7 *(Difícil)*

**Questão**: Um desenvolvedor avalia dois pipelines RAG. O Pipeline A obtém precision@5 = 0.8, mas faithfulness = 0.5. O Pipeline B obtém precision@5 = 0.6, mas faithfulness = 0.9. Qual pipeline deve ser preferido para uma aplicação de produção voltada a clientes e por quê?

A) Pipeline A — maior precisão na recuperação significa melhores respostas  
B) Pipeline B — maior faithfulness significa que as respostas estão mais confiavelmente fundamentadas em fatos  
C) Os dois são equivalentes — precisão e faithfulness medem a mesma coisa  
D) Nenhum é aceitável — ambas as métricas precisam exceder 0.9 antes da implantação em produção  

> [!success]- Resposta
> **Resposta correta: B**
>
> Para uma aplicação voltada a clientes, faithfulness é a métrica mais importante, pois mede se as respostas estão fundamentadas em fatos, em vez de serem alucinações. As respostas do Pipeline B são mais confiáveis, mesmo que ele recupere menos documentos perfeitamente relevantes. Faithfulness baixa em uma aplicação para clientes traz risco de disseminação de informações incorretas. Precisão e faithfulness medem aspectos diferentes (qualidade da recuperação versus qualidade da geração).

---

## Questão 8 *(Média)*

**Questão**: Uma empresa quer criar um sistema RAG multimodal que responda a perguntas tanto sobre descrições textuais quanto sobre imagens de produtos. O que o modelo de embeddings precisa aceitar?

A) Quantização de cores RGB para representação consistente das imagens  
B) Embeddings multimodais capazes de codificar texto e imagens no mesmo espaço vetorial  
C) Modelos de embeddings separados para texto e imagens, armazenados em índices diferentes  
D) OCR para converter todas as imagens em texto antes de gerar embeddings  

> [!success]- Resposta
> **Resposta correta: B**
>
> RAG multimodal exige embeddings de texto e imagens no mesmo espaço vetorial para que ambos possam ser recuperados juntos por similaridade semântica. Modelos multimodais (por exemplo, CLIP) produzem espaços de embeddings compartilhados. Índices separados exigem consultas separadas e a combinação de resultados, o que é menos elegante e não permite recuperação entre modalidades. OCR perde informações visuais (gráficos, diagramas, estilo).

---

## Questão 9 *(Média)*

**Questão**: O pipeline RAG de um desenvolvedor responde a perguntas sobre a documentação interna da API de uma empresa. Usuários reclamam que as respostas são interrompidas no meio da explicação. Qual é a causa raiz MAIS provável?

A) O índice vetorial precisa ser recriado  
B) Os chunks recuperados são grandes demais, ocupam toda a janela de contexto e não deixam espaço para a geração  
C) O modelo de embeddings não é adequado à documentação técnica  
D) A temperature do LLM está baixa demais e causa a interrupção antecipada  

> [!success]- Resposta
> **Resposta correta: B**
>
> Se os chunks recuperados ocuparem a maior parte da janela de contexto, o LLM não terá tokens suficientes para gerar uma resposta completa e será forçado a truncá-la. A solução é usar chunks menores ou reduzir k, diminuindo os tokens consumidos pelo contexto recuperado e deixando mais espaço para a resposta gerada. Temperature afeta criatividade/aleatoriedade, não os limites do tamanho da saída.

---

## Questão 10 *(Média)*

**Questão**: Uma equipe implementa um pipeline RAG para um sistema jurídico de perguntas e respostas e quer garantir que os trechos recuperados venham de documentos carregados há no máximo 12 meses. Qual é a MELHOR forma de implementar essa restrição?

A) Filtrar os resultados em Python após a recuperação, verificando o campo `doc_date`  
B) Usar pré-filtragem por metadados na consulta do Vector Search com um filtro de intervalo de datas  
C) Criar índices vetoriais separados por mês e consultar somente os mais recentes  
D) Fazer fine-tuning do LLM para ignorar citações jurídicas desatualizadas  

> [!success]- Resposta
> **Resposta correta: B**
>
> A pré-filtragem por metadados no momento da consulta restringe o conjunto de candidatos antes do cálculo da similaridade, excluindo documentos antigos de forma eficiente. A filtragem posterior desperdiça recursos computacionais (recupera e depois descarta resultados). Índices mensais separados exigem consultar vários índices e combinar os resultados. Fazer fine-tuning do LLM não impede a recuperação de documentos antigos.

---

## Questão 11 *(Fácil)*

**Questão**: Qual é o PRINCIPAL motivo para preferir RAG ao fine-tuning para fundamentar respostas de LLMs em dados proprietários da empresa?

A) RAG produz texto de qualidade superior ao de modelos com fine-tuning  
B) RAG pode consultar dados que não existiam quando o LLM foi treinado, sem modificar os pesos do modelo  
C) RAG é sempre mais barato que fine-tuning durante a inferência  
D) RAG elimina a necessidade de um modelo de embeddings  

> [!success]- Resposta
> **Resposta correta: B**
>
> A principal vantagem de RAG é poder usar conhecimento privado e atualizado sem modificar os pesos do LLM. O limite temporal do conhecimento do LLM é irrelevante, pois o contexto pertinente é recuperado no momento da inferência. Fine-tuning é caro e não incorpora com facilidade informações atualizadas continuamente. RAG precisa de um modelo de embeddings — ele é um componente essencial. O custo de inferência do RAG depende da infraestrutura de recuperação; não é sempre menor.

---

## Questão 12 *(Fácil)*

**Questão**: Um desenvolvedor implementa divisão em chunks pai-filho. Os chunks filhos têm 128 tokens e 20 tokens de sobreposição. Os chunks pais têm 512 tokens. Durante a recuperação, quais chunks são enviados ao LLM?

A) Os chunks filhos pequenos que corresponderam à consulta  
B) Os chunks pais grandes que contêm os chunks filhos correspondentes  
C) Os chunks filhos e pais, concatenados  
D) O chunk intermediário (320 tokens) entre os tamanhos de filho e pai  

> [!success]- Resposta
> **Resposta correta: B**
>
> Na divisão pai-filho, chunks filhos pequenos são usados para uma recuperação precisa (localizar o trecho correto), mas os chunks pais maiores são enviados ao LLM para geração. Isso oferece contexto mais rico que o pequeno trecho correspondente, mantendo a precisão da recuperação. Somente os chunks pais são enviados ao LLM — enviar ambos duplicaria conteúdo e desperdiçaria tokens da janela de contexto.

---

## Questão 13 *(Difícil)*

**Questão**: A pontuação `answer_relevance` de um pipeline RAG é 0.9, mas sua pontuação `faithfulness` é 0.4. O que essa combinação indica?

A) A etapa de recuperação está funcionando bem, mas o LLM alucina conteúdo que não está nos documentos recuperados  
B) Os documentos recuperados são relevantes, mas o LLM produz respostas prolixas e fora do tema  
C) Tanto a recuperação quanto a geração estão funcionando corretamente  
D) O modelo de embeddings produz vetores de alta qualidade, mas o recall da recuperação é baixo  

> [!success]- Resposta
> **Resposta correta: A**
>
> `answer_relevance` alto (0.9) significa que as respostas geradas atendem bem às perguntas do usuário. `faithfulness` baixo (0.4) significa que elas contêm afirmações sem apoio no contexto recuperado — o LLM está alucinando. Esse é um modo de falha comum no qual o LLM produz respostas plausíveis, mas sem fundamento. A solução é aprimorar o prompt (instruir o LLM a usar somente o contexto fornecido) ou aplicar guardrails de saída mais rigorosos.

---

## Questão 14 *(Média)*

**Questão**: Um desenvolvedor quer implementar reranking no pipeline RAG para melhorar a precisão. Qual arquitetura está CORRETA?

A) Substituir o modelo de embeddings bi-encoder por um cross-encoder para a recuperação inicial  
B) Usar um bi-encoder para recuperar os 50 principais candidatos e, então, aplicar um cross-encoder para recalcular as pontuações e selecionar os 5 primeiros  
C) Usar o próprio LLM para gerar pontuações de relevância para todos os documentos do índice  
D) Aplicar BM25 como filtro pós-recuperação para remover documentos semanticamente diferentes  

> [!success]- Resposta
> **Resposta correta: B**
>
> A arquitetura padrão de recuperação em duas etapas usa um bi-encoder rápido para recuperar um conjunto amplo de candidatos (top-50) e, então, aplica um cross-encoder mais lento e preciso para recalcular as pontuações e selecionar os top-k para o LLM. Usar um cross-encoder na recuperação inicial sobre um corpus grande é inviável do ponto de vista computacional (seria preciso pontuar cada par de documentos). Usar o LLM para pontuar todos os documentos seria ainda mais caro.

---

## Vector Search e embeddings (Questões 15–25)

---

## Questão 15 *(Fácil)*

**Questão**: Uma equipe cria um Delta Sync Index em uma tabela Delta com `pipeline_type="CONTINUOUS"`. Quando uma nova linha é inserida na tabela de origem, quando o índice é atualizado?

A) Na próxima vez que um usuário consultar o índice  
B) Automaticamente, logo após a detecção da alteração na tabela Delta  
C) O índice precisa ser sincronizado manualmente pela interface da Databricks  
D) Em uma programação fixa, a cada 24 horas  

> [!success]- Resposta
> **Resposta correta: B**
>
> Um tipo de pipeline `CONTINUOUS` mantém o índice vetorial sincronizado automaticamente com a tabela Delta de origem. Quando linhas são inseridas, atualizadas ou excluídas, as alterações são propagadas ao índice sem intervenção manual. O modo `TRIGGERED` exige sincronização manual. Não há programação automática a cada 24 horas nem sincronização acionada pela consulta.

---

## Questão 16 *(Fácil)*

**Questão**: Uma equipe quer criar um índice do Databricks Vector Search sobre uma tabela que NÃO é gerenciada pela Databricks (por exemplo, um sistema externo grava embeddings calculados fora da plataforma). Qual tipo de índice está CORRETO?

A) Delta Sync Index com `pipeline_type="TRIGGERED"`  
B) Direct Access Index  
C) Delta Sync Index com `pipeline_type="CONTINUOUS"`  
D) Managed Sync Index  

> [!success]- Resposta
> **Resposta correta: B**
>
> Índices Direct Access permitem que aplicações externas enviem embeddings por upsert usando o SDK ou a REST API do Databricks Vector Search. Essa é a escolha correta quando os embeddings são calculados fora da Databricks ou quando os dados de origem não estão em uma tabela Delta gerenciada pela Databricks. Índices Delta Sync exigem uma tabela Delta da Databricks como origem. "Managed Sync Index" não é um tipo válido.

---

## Questão 17 *(Fácil)*

**Questão**: Um desenvolvedor cria um índice do Vector Search usando a métrica de similaridade `COSINE`. No momento da consulta, o que indica uma pontuação de similaridade de cosseno mais alta entre o vetor da consulta e o vetor de um documento?

A) O vetor do documento tem magnitude maior que o vetor da consulta  
B) Os vetores da consulta e do documento apontam em direções mais semelhantes — os documentos são semanticamente mais relacionados  
C) O documento foi indexado mais recentemente que os documentos com pontuação menor  
D) O chunk do documento é mais curto e, portanto, mais específico  

> [!success]- Resposta
> **Resposta correta: B**
>
> A similaridade de cosseno mede o ângulo entre dois vetores, independentemente de suas magnitudes. Uma pontuação maior (mais próxima de 1.0) significa que os vetores apontam na mesma direção, indicando que a consulta e o documento são semanticamente semelhantes. A similaridade de cosseno independe da magnitude; portanto, o comprimento do vetor e a data de indexação são irrelevantes.

---

## Questão 18 *(Média)*

**Questão**: Um desenvolvedor está criando um recurso de busca de produtos. Os usuários fazem buscas enviando imagens (não texto). Os documentos do catálogo de produtos estão armazenados como texto. Qual abordagem de embeddings permite a recuperação entre modalidades?

A) Dois índices separados: um para consultas por imagem e outro para documentos de texto  
B) Um modelo de embeddings multimodal que mapeie imagens e texto para um espaço vetorial compartilhado  
C) Converter imagens de produtos em descrições textuais com OCR e, então, usar um modelo de embeddings de texto  
D) Usar um modelo de embeddings somente de texto e converter imagens do usuário em descrições textuais com um modelo de legendagem antes de gerar embeddings  

> [!success]- Resposta
> **Resposta correta: B**
>
> Modelos de embeddings multimodais (por exemplo, CLIP) mapeiam imagens e texto para um espaço semântico unificado, permitindo a recuperação direta de imagem para texto. A opção D (geração de legendas → embeddings de texto) é uma solução alternativa válida, mas perde informações visuais e exige uma etapa adicional com outro modelo. A opção C só funciona para texto presente nas imagens, não para fotos de produtos. Dois índices separados não permitem recuperação entre modalidades diretamente.

---

## Questão 19 *(Média)*

**Questão**: Um desenvolvedor consulta um índice do Databricks Vector Search e recebe resultados com baixa similaridade, apesar de a consulta do usuário ser claramente relevante para o conteúdo indexado. Qual é a causa MAIS provável?

A) O índice usa COSINE em vez de similaridade L2  
B) Foram usados modelos de embeddings diferentes na indexação e na consulta  
C) O parâmetro `num_results` está alto demais  
D) O endpoint do Vector Search não foi iniciado  

> [!success]- Resposta
> **Resposta correta: B**
>
> Quando se usam modelos de embeddings diferentes na indexação e na consulta, os vetores resultantes ficam em espaços semânticos distintos. Mesmo documentos claramente relevantes terão baixa similaridade de cosseno com o vetor da consulta porque os espaços não estão alinhados. Esse é um dos erros mais comuns e impactantes na implementação de RAG. Se o endpoint não tivesse sido iniciado, a consulta falharia por completo, em vez de retornar pontuações baixas.

---

## Questão 20 *(Fácil)*

**Questão**: Um engenheiro de dados precisa adicionar 10.000 embeddings de produtos a um índice Direct Access existente. Qual método do SDK está correto?

A) `index.insert(data=new_embeddings_df)`  
B) `index.upsert(dataframe=new_embeddings_df)`  
C) `index.sync(source_df=new_embeddings_df)`  
D) `index.merge(embeddings=new_embeddings_df)`  

> [!success]- Resposta
> **Resposta correta: B**
>
> O SDK do Databricks Vector Search usa `index.upsert(dataframe=...)` para adicionar ou atualizar vetores em um índice Direct Access. `insert`, `sync` e `merge` não são métodos válidos do SDK Python do Databricks Vector Search. Upsert trata inserções (novos IDs) e atualizações (IDs existentes) em uma única operação.

---

## Questão 21 *(Média)*

**Questão**: Para um chatbot de suporte ao cliente, um desenvolvedor pode escolher entre `databricks-bge-large-en` (pontuação MTEB 63.6, 1024 dimensões) e `databricks-gte-large-en` (pontuação MTEB 63.1, 1024 dimensões). Qual fator mais influencia a decisão?

A) Sempre escolher o modelo com maior pontuação MTEB  
B) Escolher com base no tipo de tarefa específica — BGE e GTE têm diferentes ênfases nos dados de treinamento  
C) Sempre usar modelos de 1024 dimensões em vez de modelos com menos dimensões  
D) Os modelos são equivalentes; escolha por ordem alfabética  

> [!success]- Resposta
> **Resposta correta: B**
>
> Mesmo quando as pontuações MTEB gerais são semelhantes, o desempenho dos modelos de embeddings varia entre categorias de tarefas (recuperação, classificação, agrupamento, similaridade semântica). A escolha correta depende do caso de uso específico — a recuperação de suporte ao cliente pode favorecer a distribuição de treinamento de um modelo em vez do outro. A dimensionalidade, por si só (ambos têm 1024 dimensões), não determina a qualidade em uma tarefa específica.

---

## Questão 22 *(Difícil)*

**Questão**: Um desenvolvedor quer filtrar resultados do Vector Search para documentos em que o campo `status` seja igual a `"active"` E o campo `priority` seja maior que 3. Qual dicionário de filtros está CORRETO?

A) `filters={"status": "active", "priority >": 3}`  
B) `filters={"status": "active", "priority": {"$gt": 3}}`  
C) `filters={"status": "active AND priority > 3"}`  
D) `filters="status = 'active' AND priority > 3"`  

> [!success]- Resposta
> **Resposta correta: B**
>
> O SDK do Databricks Vector Search aceita operadores de filtro no estilo MongoDB para comparações numéricas. O operador `$gt` significa "maior que". Várias condições são expressas como chaves separadas no dicionário de filtros. Expressões SQL em strings e condições combinadas em uma única string não são formatos de filtro válidos no SDK do Vector Search.

---

## Questão 23 *(Difícil)*

**Questão**: Uma equipe indexa 1 milhão de embeddings de produtos e percebe que a latência das consultas aumenta significativamente à medida que o índice cresce. Qual alteração na configuração do índice provavelmente melhorará mais a latência das consultas?

A) Trocar o índice Direct Access por um índice Delta Sync  
B) Recriar o índice com busca por approximate nearest neighbor (ANN) habilitada  
C) Reduzir o parâmetro `num_results` de 10 para 5  
D) Trocar a métrica de similaridade COSINE por L2  

> [!success]- Resposta
> **Resposta correta: B**
>
> Índices VectorSearch usam algoritmos de approximate nearest neighbor (ANN), como HNSW, para trocar uma pequena quantidade de precisão por um aumento expressivo na velocidade das consultas em escala. Garantir que os parâmetros ANN estejam configurados corretamente para o tamanho do índice é a principal alavanca para otimizar a latência. Reduzir `num_results` ajuda um pouco, mas não resolve a complexidade algorítmica. O tipo do índice (Direct ou Delta Sync) e a métrica de similaridade não alteram fundamentalmente as características de desempenho das consultas.

---

## Questão 24 *(Média)*

**Questão**: Uma equipe armazena embeddings de 500.000 documentos com `databricks-bge-large-en` (1024 dimensões, float32). Aproximadamente quanto espaço os vetores brutos exigem?

A) ~500 MB  
B) ~2 GB  
C) ~20 GB  
D) ~200 GB  

> [!success]- Resposta
> **Resposta correta: B**
>
> Cada valor float32 ocupa 4 bytes. 500.000 documentos × 1024 dimensões × 4 bytes = 2.048.000.000 bytes ≈ 2 GB para os vetores brutos. Índices vetoriais acrescentam sobrecarga (metadados e estruturas de grafo para ANN), portanto o tamanho real do índice será maior. Conhecer as necessidades aproximadas de armazenamento ajuda no planejamento de capacidade e na escolha da configuração do índice.

---

## Questão 25 *(Fácil)*

**Questão**: Um desenvolvedor exclui um documento da tabela Delta de origem. O que acontece com o embedding correspondente no índice vetorial quando o índice Delta Sync está no modo `CONTINUOUS`?

A) O embedding permanece no índice até ser excluído manualmente  
B) O embedding é removido automaticamente do índice vetorial quando a exclusão é detectada  
C) O embedding é marcado como "arquivado", mas permanece pesquisável  
D) É necessário recriar todo o índice para remover o embedding excluído  

> [!success]- Resposta
> **Resposta correta: B**
>
> Um índice Delta Sync em modo `CONTINUOUS` acompanha inserções, atualizações E exclusões na tabela Delta de origem. Quando uma linha é excluída da origem, o embedding correspondente é removido automaticamente do índice vetorial. Isso mantém o índice consistente com a fonte de verdade sem intervenção manual.

---

## Desenvolvimento de aplicações com LLMs (Questões 26–38)

---

## Questão 26 *(Fácil)*

**Questão**: Um desenvolvedor quer que todas as chamadas a chains do LangChain em seu notebook sejam rastreadas e registradas automaticamente no MLflow. Qual única linha de código faz isso?

A) `mlflow.langchain.log_model(chain, "chain")`  
B) `mlflow.langchain.autolog()`  
C) `mlflow.start_run(run_name="langchain-trace")`  
D) `mlflow.set_tracking_uri("databricks")`  

> [!success]- Resposta
> **Resposta correta: B**
>
> `mlflow.langchain.autolog()` habilita o registro automático no MLflow das entradas, saídas e etapas intermediárias das chains do LangChain. Deve ser chamado uma vez, antes da execução das chains. `log_model` salva uma chain como artefato de modelo implantável (não serve para tracing). `start_run` abre o contexto de uma execução, mas não habilita autolog específico do LangChain. `set_tracking_uri` configura o destino dos logs, não o que será registrado.

---

## Questão 27 *(Difícil)*

**Questão**: Um desenvolvedor passa o system prompt: "Você é um assistente prestativo. Responda somente com base no contexto fornecido. Se a resposta não estiver no contexto, diga 'Não sei.'" — mas o LLM continua gerando respostas com base em seu conhecimento paramétrico. Qual é a causa MAIS provável?

A) O system prompt é curto demais para ser eficaz  
B) O LLM ignora o system prompt porque não foi ajustado por fine-tuning para seguir instruções  
C) O contexto recuperado está vazio ou é insuficiente, fazendo o LLM recorrer ao conhecimento paramétrico  
D) O parâmetro temperature está alto demais  

> [!success]- Resposta
> **Resposta correta: C**
>
> Quando a etapa de recuperação não retorna contexto relevante (recuperação vazia ou de baixa qualidade), o LLM frequentemente "preenche as lacunas" usando seu conhecimento de treinamento, apesar das instruções para "responder somente com base no contexto". A solução é melhorar a qualidade da recuperação ou adicionar uma verificação explícita para contexto vazio. Em geral, LLMs modernos ajustados para seguir instruções obedecem ao system prompt; o problema normalmente é um contexto vazio, não o tamanho do prompt nem temperature.

---

## Questão 28 *(Média)*

**Questão**: Um desenvolvedor cria um chatbot com várias interações usando LangChain e `ConversationBufferMemory`. Após 50 turnos de conversa, a API do LLM retorna um erro de limite de tamanho do contexto excedido. Qual é a MELHOR solução?

A) Aumentar a janela de contexto do LLM trocando para um modelo maior  
B) Trocar para `ConversationBufferWindowMemory` com uma janela fixa de 10 turnos ou para `ConversationSummaryMemory`, que comprime o histórico  
C) Desabilitar totalmente a memória — cada turno deve ser independente  
D) Reduzir o parâmetro `max_tokens` do LLM  

> [!success]- Resposta
> **Resposta correta: B**
>
> `ConversationBufferMemory` acumula todo o histórico indefinidamente e acaba excedendo a janela de contexto. A solução correta é usar um tipo de memória que limite o crescimento do contexto: `ConversationBufferWindowMemory` mantém os últimos k turnos, enquanto `ConversationSummaryMemory` comprime os turnos antigos em um resumo. Trocar para um modelo maior apenas adia o problema, sem resolvê-lo permanentemente. Desabilitar a memória prejudica a coerência da conversa. Reduzir `max_tokens` limita o tamanho da saída, não o do contexto de entrada.

---

## Questão 29 *(Média)*

**Questão**: Um desenvolvedor usa `mlflow.evaluate()` para pontuar um pipeline RAG com `model_type="question-answering"`. O conjunto de dados de avaliação contém `inputs`, `outputs` (respostas geradas pelo modelo) e `contexts` (chunks recuperados). Qual coluna fornece o contexto recuperado à métrica faithfulness?

A) `inputs`  
B) `outputs`  
C) `contexts`  
D) `targets`  

> [!success]- Resposta
> **Resposta correta: C**
>
> A coluna `contexts` do conjunto de dados de avaliação contém os trechos recuperados que foram fornecidos ao LLM. A métrica `faithfulness` usa a coluna `contexts` para verificar se `outputs` (as respostas geradas) estão fundamentadas no contexto fornecido. `inputs` contém as perguntas do usuário. `targets` contém respostas de referência (ground truth), usadas por métricas como `exact_match`.

---

## Questão 30 *(Fácil)*

**Questão**: Uma aplicação LangChain usa `ConversationChain` com `ConversationSummaryMemory`. O que `ConversationSummaryMemory` armazena entre turnos?

A) O texto completo de todos os turnos anteriores da conversa  
B) Um resumo contínuo da conversa, regenerado após cada turno com o LLM  
C) Um embedding vetorial de cada turno anterior para consulta semântica  
D) Somente a última mensagem do usuário e a resposta mais recente do LLM  

> [!success]- Resposta
> **Resposta correta: B**
>
> `ConversationSummaryMemory` resume progressivamente a conversa após cada turno usando uma chamada ao LLM, substituindo todo o histórico por um resumo condensado. Isso limita o uso da janela de contexto, preservando as informações principais. `ConversationBufferMemory` armazena o histórico completo. Memória com embeddings vetoriais é outro tipo (`VectorStoreRetrieverMemory`). `ConversationBufferWindowMemory` mantém literalmente somente os últimos k turnos.

---

## Questão 31 *(Fácil)*

**Questão**: Um desenvolvedor quer criar um agente LangChain capaz de usar três ferramentas: `search_database`, `call_weather_api` e `lookup_inventory`. Qual componente decide que ferramenta chamar para uma determinada entrada do usuário?

A) A `SequentialChain` executa todas as ferramentas em ordem e retorna o resultado final  
B) O mecanismo de raciocínio LLM dentro do agente, que usa nomes e descrições das ferramentas para selecionar a opção adequada  
C) Um roteador baseado em regras que associa por padrão as entradas do usuário aos nomes das ferramentas  
D) A chain `RetrievalQA` encaminha automaticamente a solicitação ao retriever adequado  

> [!success]- Resposta
> **Resposta correta: B**
>
> Agentes LangChain usam um LLM como mecanismo de raciocínio (seguindo o padrão ReAct ou similar) para decidir qual ferramenta chamar com base na entrada do usuário e no nome e na descrição de cada ferramenta. O LLM gera iterativamente um "pensamento" e uma "ação" (seleção da ferramenta + argumentos). Não há um roteador integrado baseado em regras para ferramentas arbitrárias — o LLM faz a escolha dinâmica.

---

## Questão 32 *(Média)*

**Questão**: Um desenvolvedor registra uma chain `RetrievalQA` do LangChain com `mlflow.langchain.log_model()` e quer carregá-la novamente para inferência. Qual código está correto?

A) `chain = mlflow.langchain.load_model(model_uri)`  
B) `chain = mlflow.pyfunc.load_model(model_uri)`  
C) `chain = mlflow.sklearn.load_model(model_uri)`  
D) `chain = pickle.load(open(model_uri, "rb"))`  

> [!success]- Resposta
> **Resposta correta: B**
>
> Modelos registrados com qualquer flavor do MLflow (inclusive `mlflow.langchain`) podem ser carregados como modelos `pyfunc` usando `mlflow.pyfunc.load_model(model_uri)`. Isso retorna um objeto com um método `.predict()` que encapsula a chain. `mlflow.langchain.load_model()` também funciona, mas é específico do flavor. `mlflow.sklearn.load_model()` é para modelos scikit-learn. Usar `pickle` diretamente não é recomendado e não funciona com o armazenamento de artefatos do MLflow.

---

## Questão 33 *(Fácil)*

**Questão**: Um desenvolvedor configura uma chain `RetrievalQA` do LangChain com `chain_type="stuff"`. O que essa configuração faz com os documentos recuperados?

A) Resume cada documento recuperado individualmente antes de enviá-lo ao LLM  
B) Seleciona o documento mais relevante e ignora os demais  
C) Concatena todos os documentos recuperados em um único bloco de contexto e o envia ao LLM em uma chamada  
D) Divide os documentos recuperados em chunks menores antes de enviá-los ao LLM  

> [!success]- Resposta
> **Resposta correta: C**
>
> O tipo de chain `"stuff"` concatena todos os documentos recuperados ("stuffing") no prompt do LLM em uma única chamada. Essa é a abordagem mais simples e funciona bem quando k é pequeno e os chunks recuperados são curtos. Outros tipos incluem `"map_reduce"` (resume cada documento e combina os resultados), `"refine"` (refina a resposta iterativamente) e `"map_rerank"` (pontua a resposta de cada documento e escolhe a melhor).

---

## Questão 34 *(Difícil)*

**Questão**: Um desenvolvedor precisa impedir que o agente LangChain chame a ferramenta `delete_database`, a menos que a mensagem do usuário contenha explicitamente a palavra "delete". Qual é a implementação MAIS adequada?

A) Remover a ferramenta `delete_database` da lista de ferramentas do agente  
B) Adicionar uma validação à função da ferramenta `delete_database` que gere um erro se "delete" não estiver na entrada original  
C) Adicionar uma camada de guardrails que intercepte a ação do agente antes da execução e a valide em relação à entrada original do usuário  
D) Definir temperature do LLM como 0 para evitar chamadas inesperadas a ferramentas  

> [!success]- Resposta
> **Resposta correta: C**
>
> Uma camada interceptora de guardrails fica entre a etapa de raciocínio do agente e a execução da ferramenta. Ela pode validar, registrar ou bloquear qualquer ação proposta antes de executá-la. Isso oferece uma verificação de segurança determinística que complementa (mas não substitui) o julgamento do LLM. Adicionar a verificação dentro da função da ferramenta (B) é uma defesa válida em profundidade, mas não é o principal local para guardrails. Temperature afeta a aleatoriedade, não a lógica de seleção de ferramentas.

---

## Questão 35 *(Média)*

**Questão**: Uma equipe quer fazer um teste A/B com dois system prompts diferentes para seu chatbot RAG. A avaliação MLflow será executada com o mesmo conjunto de 100 perguntas de teste para ambos os prompts. Qual recurso do MLflow permite uma comparação sistemática?

A) Model Registry — registrar cada variante de prompt como uma versão de modelo separada  
B) Resultados da execução de `mlflow.evaluate()` armazenados em um experimento MLflow — comparar as execuções na interface do experimento  
C) MLflow Autolog — gerar automaticamente resultados do teste A/B para cada prompt  
D) Lakeflow Jobs — agendar dois jobs, um por prompt  

> [!success]- Resposta
> **Resposta correta: B**
>
> Executar `mlflow.evaluate()` para cada variante de prompt registra as métricas como execuções separadas dentro do mesmo experimento. A interface de comparação de experimentos permite comparar métricas lado a lado (faithfulness, answer relevance etc.) entre as variantes. Model Registry serve para versionar modelos implantáveis, não configurações de avaliação. Autolog captura dados de treinamento, não comparações A/B de avaliação. Lakeflow Jobs orquestra cargas de trabalho, mas não compara resultados de avaliações.

---

## Questão 36 *(Fácil)*

**Questão**: Um desenvolvedor define `temperature=0.0` ao chamar um endpoint da Foundation Model API. Qual é o efeito?

A) O modelo sempre se recusa a responder perguntas sobre as quais não tem certeza  
B) O modelo produz saídas determinísticas com decodificação gulosa (o token mais provável a cada etapa)  
C) O modelo gera respostas mais curtas para reduzir custos  
D) O modelo ignora a janela de contexto e usa somente o conhecimento paramétrico  

> [!success]- Resposta
> **Resposta correta: B**
>
> Temperature controla a aleatoriedade da amostragem de tokens. Com `temperature=0`, o modelo usa decodificação gulosa, sempre selecionando o token de maior probabilidade. Isso produz saídas determinísticas (reprodutíveis). Não afeta o tamanho da resposta, o comportamento de recusa nem a fonte do conhecimento. Temperature próxima de 1.0 produz saídas mais variadas/criativas; próxima de 0 produz saídas focadas e determinísticas.

---

## Questão 37 *(Média)*

**Questão**: Um desenvolvedor cria um pipeline LangChain em que a Etapa 1 extrai entidades importantes da consulta do usuário e a Etapa 2 usa essas entidades para formular uma consulta ao banco de dados. Qual construção do LangChain é a MAIS adequada?

A) Um agente LangChain com duas ferramentas: extração de entidades e consulta ao banco de dados  
B) Uma `SequentialChain` em que a saída da Etapa 1 é passada automaticamente como entrada para a Etapa 2  
C) Uma chain `RetrievalQA` com um retriever personalizado que faz a extração de entidades  
D) Dois objetos `LLMChain` separados, chamados de forma independente e combinados com formatação de strings em Python  

> [!success]- Resposta
> **Resposta correta: B**
>
> Uma `SequentialChain` foi projetada para pipelines em que a saída de uma etapa da chain é passada como entrada para a etapa seguinte. Isso cria um fluxo de trabalho direcionado em que cada etapa se baseia na anterior. Agentes são adequados a decisões dinâmicas (qual ferramenta chamar), não a pipelines sequenciais fixos. É possível combinar chains manualmente com Python, mas usar `SequentialChain` é mais fácil de manter.

---

## Questão 38 *(Difícil)*

**Questão**: Um desenvolvedor usa `mlflow.evaluate()` com um modelo juiz personalizado para avaliar a qualidade das respostas. O modelo juiz é um endpoint da Foundation Model API. Qual parâmetro especifica o modelo juiz?

A) `judge_model="databricks/dbrx-instruct"`  
B) `evaluator_config={"openai_model": "gpt-4"}`  
C) `extra_metrics=[answer_relevance(model="endpoints:/databricks-dbrx-instruct")]`  
D) `model_type="llm-judge"` com `judge_endpoint="databricks-dbrx-instruct"`  

> [!success]- Resposta
> **Resposta correta: C**
>
> No MLflow, métricas baseadas em juízes LLM aceitam um parâmetro `model` para especificar qual modelo usar na avaliação. Para endpoints Databricks Foundation Model, o formato é `"endpoints:/endpoint-name"`. Esse valor é passado na chamada da função da métrica em `extra_metrics`. Não existe um parâmetro de nível superior `judge_model` nem uma opção `model_type="llm-judge"` em `mlflow.evaluate()`.

---

## Ferramentas GenAI da Databricks (Questões 39–45)

---

## Questão 39 *(Média)*

**Questão**: O chatbot RAG de produção de uma equipe processa 500 solicitações por minuto e tem requisitos rígidos de latência p99 inferior a 2 segundos. Que tipo de endpoint da Foundation Model API é o MAIS adequado?

A) Endpoint Pay-per-token — serverless e com escalabilidade automática  
B) Endpoint External Model — usa a infraestrutura da OpenAI  
C) Endpoint Provisioned Throughput — capacidade dedicada com throughput garantido  
D) Endpoint Batch Inference — processa todas as 500 solicitações em paralelo  

> [!success]- Resposta
> **Resposta correta: C**
>
> Endpoints Provisioned Throughput oferecem capacidade de computação dedicada com throughput garantido (tokens por segundo) e latência mais consistente — essencial para SLAs de produção. Endpoints Pay-per-token compartilham a infraestrutura com outros usuários e têm latência variável, que pode exceder 2 segundos sob carga. Endpoints External Model acrescentam saltos de rede para provedores terceirizados, aumentando a variação da latência. Endpoints Batch são para cargas offline, não para serving em tempo real.

---

## Questão 40 *(Média)*

**Questão**: Um desenvolvedor registra uma chain LangChain como modelo MLflow. Qual formato de entrada de `mlflow.evaluate()` mede corretamente o desempenho da chain em um conjunto de dados de teste?

A) Uma lista de perguntas de usuários passada como `data=[q1, q2, q3]`  
B) Um pandas DataFrame com colunas `inputs` e `targets` (e, opcionalmente, `contexts`)  
C) O nome de uma tabela Delta passado como `data="main.catalog.test_table"`  
D) Um dicionário com as chaves `questions` e `expected_answers`  

> [!success]- Resposta
> **Resposta correta: B**
>
> `mlflow.evaluate()` aceita um pandas DataFrame com nomes de coluna padronizados: `inputs` (consultas do usuário), `targets` (respostas de ground truth, opcional) e `contexts` (trechos recuperados, opcional). Esse é o formato padrão de dados de avaliação. Uma lista de strings, o nome direto de uma tabela Delta ou um formato de dicionário personalizado não são aceitos nativamente por `mlflow.evaluate()`.

---

## Questão 41 *(Fácil)*

**Questão**: Uma equipe implanta um endpoint Model Serving para sua chain RAG e habilita tabelas de inferência. Onde os payloads de solicitação/resposta registrados são armazenados?

A) No experimento MLflow associado ao endpoint  
B) Em uma tabela Delta do Unity Catalog, conforme especificado na configuração do endpoint  
C) No DBFS em `/dbfs/serving-endpoints/logs/`  
D) No log de auditoria da Databricks, acessível por `system.access.audit`  

> [!success]- Resposta
> **Resposta correta: B**
>
> Tabelas de inferência gravam dados de solicitação e resposta do endpoint em uma tabela Delta do Unity Catalog. O local da tabela é especificado ao criar ou configurar o endpoint. Isso permite fluxos subsequentes de monitoramento, avaliação e fine-tuning com tráfego real de produção. Experimentos MLflow acompanham execuções de treinamento, não tráfego de serving. Caminhos de logs do DBFS e logs de auditoria (`system.access.audit`) têm outras finalidades.

---

## Questão 42 *(Média)*

**Questão**: Um desenvolvedor consulta um endpoint Foundation Model `databricks-meta-llama-3-1-70b-instruct` e precisa que a resposta esteja em formato JSON com campos específicos. Qual abordagem é a MAIS confiável?

A) Definir `temperature=0` para forçar uma saída estruturada  
B) Usar o modo JSON se o endpoint oferecer suporte ou incluir instruções explícitas de formato JSON e um exemplo no system prompt  
C) Pós-processar a resposta com `json.loads()` — todas as Foundation Model APIs retornam JSON automaticamente  
D) Definir `max_tokens=100` para impedir que o modelo gere texto extra  

> [!success]- Resposta
> **Resposta correta: B**
>
> Para obter saída JSON estruturada, as abordagens mais confiáveis são: (1) usar o modo JSON (parâmetro de formato da resposta), se o endpoint oferecer suporte; ou (2) fornecer instruções claras de formato JSON e um exemplo no system prompt. Nem todos os endpoints garantem saída JSON automaticamente — `json.loads()` falhará com respostas em texto livre. Temperature=0 garante determinismo, não formato JSON. `max_tokens` limita o tamanho, não o formato.

---

## Questão 43 *(Média)*

**Questão**: Uma organização quer usar as Databricks Foundation Model APIs internamente, mas impedir que equipes armazenem chaves de API em seus notebooks. Qual recurso da Databricks permite o gerenciamento centralizado de credenciais para endpoints Foundation Model?

A) Listas de controle de acesso do MLflow Model Registry  
B) Políticas de mascaramento de colunas do Unity Catalog  
C) Databricks Secrets com autenticação de service principal — ou MLflow AI Gateway para modelos externos  
D) Variáveis de ambiente no nível do cluster definidas pelos administradores do workspace  

> [!success]- Resposta
> **Resposta correta: C**
>
> Databricks Secrets armazena credenciais de forma segura e pode restringir o acesso por grupo, impedindo que usuários individuais acessem as chaves de API brutas. Para endpoints de modelos externos (OpenAI, Anthropic), MLflow AI Gateway centraliza o gerenciamento de credenciais para que as aplicações cliente usem autenticação da Databricks, em vez de chaves de API específicas do provedor. ACLs do Model Registry controlam o acesso a versões de modelos, não a credenciais de API. Mascaramento de colunas é um recurso de governança de dados do Unity Catalog.

---

## Questão 44 *(Média)*

**Questão**: Um desenvolvedor registra uma chain LangChain no MLflow e quer implantá-la como endpoint REST no Databricks Model Serving. Qual é a sequência EXATA e correta de etapas?

A) Registrar o modelo → criar endpoint de serving → registrar no Model Registry  
B) Registrar no Model Registry → registrar o modelo → criar endpoint de serving  
C) Registrar o modelo → registrar no Model Registry → criar endpoint de serving  
D) Criar endpoint de serving → registrar o modelo → registrar no Model Registry  

> [!success]- Resposta
> **Resposta correta: C**
>
> O fluxo correto de implantação na Databricks é: (1) **registrar** o modelo em uma execução MLflow com `mlflow.langchain.log_model()`, (2) **registrar** o modelo salvo no MLflow Model Registry com `mlflow.register_model()`, (3) **criar** um endpoint Model Serving que aponte para a versão registrada do modelo. O Model Registry é a fonte de verdade para serving; um modelo registrado como artefato, mas não no Registry, não pode ser servido pelo Databricks Model Serving.

---

## Questão 45 *(Difícil)*

**Questão**: Uma equipe usa `mlflow.evaluate()` para pontuar sua chain RAG e obtém os seguintes resultados: `faithfulness=0.85`, `answer_relevance=0.90`, `toxicity=0.02`. Qual conclusão é a MAIS precisa?

A) O pipeline está abaixo do esperado — as três métricas devem superar 0.95 para uso em produção  
B) O pipeline gera respostas em sua maioria fundamentadas no contexto recuperado, relevantes às perguntas dos usuários e raramente produz conteúdo nocivo — uma referência inicial razoável para produção  
C) A pontuação de toxicity de 0.02 indica que 2 % das respostas são tóxicas e isso precisa ser corrigido antes da implantação  
D) A pontuação de faithfulness de 0.85 indica que a etapa de recuperação falha em 15 % das vezes  

> [!success]- Resposta
> **Resposta correta: B**
>
> Faithfulness=0.85 é forte (85 % das afirmações estão fundamentadas no contexto). Answer relevance=0.90 é excelente (as respostas atendem às perguntas dos usuários em 90 % das vezes). Toxicity=0.02 é muito baixa (a frequência de 2 % pode se referir a pontuações baixas, não a 2 % de respostas nocivas). Em conjunto, isso indica um pipeline com bom desempenho. Limites de prontidão para produção dependem do domínio — não há exigência universal de 0.95. Faithfulness é uma métrica de geração, não de recuperação.

---

**Fim do Simulado 1 — 45 questões**

[← Voltar às instruções do simulado](./README.md) | [Tentar o Simulado 2 →](../mock-exam-2/README.md)

---

## Novas questões para a atualização do blueprint de março de 2026

As questões abaixo abordam o domínio **Governance**, cuja importância foi elevada (8 %).

### Questão GOV-1 *(Média — Governance)*

**Cenário**: Uma equipe está se preparando para implantar uma aplicação RAG que recupera dados de um índice vetorial criado sobre uma tabela Delta com chamados de suporte ao cliente. Os chamados incluem endereços de e-mail e números de conta. A equipe de compliance exige que PII nunca apareça nos chunks recuperados exibidos aos usuários.

**Questão**: Em que etapa do pipeline deve ocorrer a redação de PII?

A) No momento da recuperação — aplicar uma máscara de coluna UC à coluna de embeddings para ocultar o vetor quando PII for detectada  
B) Após a geração — pós-processar a saída do LLM com uma regex para remover PII antes de responder ao usuário  
C) No momento da divisão em chunks/geração de embeddings — redigir PII *antes* de gerar embeddings, para que o índice vetorial nunca contenha PII reconstruível  
D) Na camada Unity AI Gateway — habilitar a política "PII detection" no endpoint de serving  

> [!success]- Resposta
> **Resposta correta: C**
>
> PII precisa ser redigida **antes da geração dos embeddings**. Depois que uma string com PII vira embedding, o vetor fica no índice — e embeddings preservam informações suficientes para que o conteúdo original possa, às vezes, ser reconstruído. Máscaras de coluna UC (A) funcionam para acesso SQL, mas não para a representação vetorial. Regex após a geração (B) é frágil e causa perda de conteúdo. Guardrails de PII do Unity AI Gateway (D) são uma camada útil de defesa em profundidade, mas não resolvem a PII *dentro do próprio índice*; o padrão seguro é redigir no momento da divisão em chunks E aplicar filtros de gateway/saída.

---

### Questão GOV-2 *(Média — Governance)*

**Cenário**: Uma equipe tem um endpoint Model Serving que hospeda uma aplicação GenAI voltada a clientes. As equipes de segurança e compliance precisam de uma trilha de auditoria: cada prompt e resposta, com timestamps e IDs de solicitação, retidos por pelo menos 90 dias e consultáveis por SQL.

**Questão**: Qual recurso da Databricks atende ao requisito com menos código personalizado?

A) Adicionar um logging callback dentro da chain LLM que grave cada solicitação/resposta em uma tabela Delta  
B) Habilitar **Inference Tables** no endpoint Model Serving — a Databricks captura automaticamente `request`, `response`, `timestamp_ms` e `databricks_request_id` em uma tabela Delta no UC  
C) Enviar stderr do endpoint para um destino CloudWatch / Cloud Logging e analisar os logs todas as noites  
D) Configurar uma política "audit" do Unity AI Gateway para enviar um resumo diário por e-mail à equipe de compliance  

> [!success]- Resposta
> **Resposta correta: B**
>
> Inference Tables são o registro de auditoria oficial documentado para endpoints Model Serving. Ao habilitá-las, cada solicitação/resposta é capturada automaticamente em uma tabela Delta no UC. O schema exato depende do tipo de endpoint: **tabelas de inferência do Model Serving** usam `request`, `response`, `timestamp_ms`, `databricks_request_id`; **tabelas de inferência do AI Gateway** (schema unificado para endpoints encapsulados pelo Gateway) usam `request_time` e um layout um pouco diferente — ambas oferecem a mesma garantia de auditoria e retenção consultável por SQL pronta para uso. A opção A é a versão "faça você mesmo" e é desnecessária. A opção C é frágil e externa ao sistema. A opção D não corresponde ao funcionamento do Unity AI Gateway. A política de logging do Gateway encaminha automaticamente os dados às Inference Tables (que é exatamente a resposta correta, apresentada de outra forma); não existe um recurso integrado de resumo por e-mail.

---

### Questão AGENT-1 *(Média — Assembling and Deploying Apps)*

**Cenário**: Uma equipe criou uma chain RAG no LangChain. Precisa implantá-la como um único endpoint Model Serving com respostas em streaming, MLflow tracing e captura automática em Inference Tables.

**Questão**: Qual fluxo de implantação atende melhor ao padrão documentado?

A) Criar uma subclasse de `mlflow.pyfunc.ResponsesAgent`, encapsular a chain em `predict()`, registrar com `mlflow.pyfunc.log_model`, registrar no UC e, então, chamar `databricks.agents.deploy(uc_model_name, version)`  
B) Serializar a chain com pickle e escrever uma aplicação Flask personalizada para executar em Docker no workspace  
C) Registrar a chain com `mlflow.langchain.log_model` e criar manualmente um endpoint Model Serving pela interface do workspace  
D) Usar uma tarefa de notebook Databricks agendada para executar a chain  

> [!success]- Resposta
> **Resposta correta: A**
>
> `ResponsesAgent` do Mosaic AI Agent Framework + `databricks.agents.deploy()` é o fluxo documentado em uma etapa que oferece streaming, MLflow tracing e ativação automática de Inference Tables — este terceiro pilar habilita diretamente o monitoramento on-line EVAL-1. `databricks.agents.deploy()` provisiona o endpoint Model Serving, registra a versão e conecta os três recursos em uma chamada. A opção B é o caminho "faça você mesmo" e perde os benefícios do framework. A opção C funciona, mas requer duas etapas (não oferece streaming ou tracing automático sem encapsulamento). A opção D serve para processamento em lote, não para serving.

---

### Questão GW-1 *(Média — Assembling and Deploying Apps)*

**Cenário**: Uma equipe quer implantar a versão v3 de uma aplicação RAG, mantendo a v2 ativa como fallback. Precisa encaminhar 10 % do tráfego à v3 e 90 % à v2, com possibilidade de aumentar gradualmente o tráfego da v3 para 100 % com base em métricas de qualidade das Inference Tables.

**Questão**: Qual recurso de um endpoint Mosaic AI Model Serving implementa esse padrão canário?

A) Limites de taxa no Unity AI Gateway — definir um limite para a v2 para que o tráfego passe naturalmente à v3  
B) **Configuração de tráfego de endpoint multi-modelo** — registrar `v2` e `v3` como `served_entities` no mesmo endpoint e definir `traffic_config = {"routes": [{"served_model_name": "v3", "traffic_percentage": 10}, {"served_model_name": "v2", "traffic_percentage": 90}]}`  
C) Guardrails do Unity AI Gateway — bloquear solicitações à v2 quando a v3 estiver saudável  
D) Registro de payloads em Inference Tables — registrar cada solicitação e deixar que o consumidor escolha a versão  

> [!success]- Resposta
> **Resposta correta: B**
>
> **Divisão de tráfego é um recurso do Mosaic AI Model Serving**, não uma política do Unity AI Gateway. Registre várias `served_entities` em um único endpoint e atribua pesos a `routes` usando `traffic_config`. Os pesos precisam somar 100; atualize as porcentagens ao longo do tempo para aumentar o tráfego gradualmente. Unity AI Gateway encapsula o endpoint para governança (limites de taxa, registro de payloads, guardrails, rastreamento de uso, fallbacks) — ele observa a divisão, mas não a define. A opção A usa limites de taxa incorretamente. A opção C usa guardrails incorretamente. A opção D não tem relação com a seleção de versão.

---

### Questão EVAL-1 *(Média — Evaluation and Monitoring)*

**Cenário**: Uma equipe quer medir o desvio de qualidade de uma aplicação RAG em produção. Tem 30 dias de dados de Inference Tables (uma linha por solicitação, com prompt e resposta completos). Quer calcular *semanalmente* uma pontuação de groundedness em uma amostra do tráfego de produção, usando um LLM mais avançado como juiz.

**Questão**: Qual abordagem corresponde ao padrão documentado de Agent Evaluation da Databricks?

A) Amostrar N linhas recentes da Inference Table em um Spark DataFrame; executar `mlflow.evaluate(data=sample, model_type="databricks-agent")` para que os juízes RAG integrados da Databricks (groundedness, relevance, correctness, safety) pontuem a amostra; agendar por meio de um Lakeflow Job  
B) Ler manualmente 100 conversas aleatórias toda semana  
C) Calcular uma pontuação de groundedness com uma regex sobre o texto da resposta  
D) Treinar um classificador personalizado com dados de produção  

> [!success]- Resposta
> **Resposta correta: A**
>
> O framework Agent Evaluation da Databricks integra-se a `mlflow.evaluate(...)` usando `model_type="databricks-agent"`. Os juízes RAG integrados incluem **groundedness** (a resposta decorre do contexto recuperado?), **answer relevance**, **answer correctness** e **safety**. Cada chamada de `mlflow.evaluate` cria uma execução MLflow, gerando uma série temporal rastreável da qualidade ao longo das semanas. Agende a execução com um Lakeflow Job. A opção B não escala. A opção C é frágil. A opção D exige meses de trabalho e é excessiva.

---

**[← Voltar às instruções do simulado](./README.md)** | **[← Voltar aos recursos](../README.md)** | **[← Voltar a GenAI Engineer Associate](../../README.md)**
