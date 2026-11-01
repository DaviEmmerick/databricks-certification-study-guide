---
title: Questões do Simulado 2 — GenAI Engineer Associate
type: mock-exam-questions
tags: [genai-engineer-associate, mock-exam]
status: published
---

# Simulado 2 — Questões

Defina um cronômetro de 90 minutos e responda às 45 questões antes de conferir as respostas.

[← Voltar às instruções do Simulado 2](./README.md)

---

## Projetar soluções RAG (Questões 1–14)

---

## Questão 1 *(Média)*

**Questão**: Uma empresa farmacêutica tem 10 milhões de páginas de dados de ensaios clínicos. Pesquisadores fazem perguntas sob demanda diariamente, e novos dados de ensaios são adicionados toda semana. A empresa precisa cumprir requisitos rigorosos de residência de dados — nenhum dado pode sair do ambiente Databricks. Qual arquitetura é a MAIS adequada?

A) Usar diretamente a API da OpenAI com um modelo GPT-4 ajustado por fine-tuning com os dados dos ensaios  
B) Criar um pipeline RAG usando Databricks Vector Search e um endpoint Foundation Model Provisioned Throughput, mantendo todos os dados e a inferência dentro da Databricks  
C) Exportar os dados para um banco de dados vetorial de terceiros para obter menor latência de recuperação  
D) Armazenar todas as 10 milhões de páginas no system prompt do LLM, usando um modelo com contexto de 1 milhão de tokens  

> [!success]- Resposta
> **Resposta correta: B**
>
> Databricks Vector Search e Foundation Model APIs mantêm todos os dados e a inferência no ambiente Databricks, atendendo aos requisitos de residência de dados. A API da OpenAI, bancos de dados vetoriais de terceiros e abordagens com contexto de 1 milhão de tokens violam as restrições de residência ou são computacionalmente inviáveis para 10 milhões de páginas.

---

## Questão 2 *(Média)*

**Questão**: Uma equipe processa artigos científicos em PDF para um pipeline RAG. Os artigos contêm seções longas e estruturadas (Abstract, Methods, Results, Discussion) com cabeçalhos consistentes. Qual estratégia de divisão em chunks MAIS preserva a integridade semântica de cada seção?

A) Chunks de tamanho fixo de 256 tokens com 32 tokens de sobreposição  
B) Divisão recursiva de texto por caracteres com sobreposição agressiva  
C) Divisão semântica em chunks nos limites das seções (usando os cabeçalhos como delimitadores)  
D) Divisão no nível das frases e reagrupar a cada 5 frases  

> [!success]- Resposta
> **Resposta correta: C**
>
> Quando os documentos têm uma estrutura consistente e significativa (cabeçalhos de seção), dividir em chunks nos limites estruturais preserva a integridade semântica de cada seção. Cada chunk (Abstract, Methods, Results) é uma unidade coerente que responde a diferentes tipos de perguntas. A divisão de tamanho fixo e a divisão recursiva ignoram a estrutura do documento e frequentemente cortam seções importantes no meio de um raciocínio.

---

## Questão 3 *(Difícil)*

**Questão**: Um desenvolvedor testa seu pipeline RAG e descobre que precision@5 = 0.40 na recuperação (somente 2 dos 5 chunks recuperados são relevantes). Apesar da recuperação ruim, o LLM produz respostas em sua maioria corretas. Qual é o MAIOR risco dessa situação em produção?

A) O pipeline é mais caro do que o necessário porque recupera chunks extras  
B) Às vezes o LLM pode responder corretamente por acaso com base em contexto irrelevante, mas será pouco confiável e poderá alucinar com mais frequência  
C) O índice vetorial exigirá mais armazenamento do que o necessário  
D) O modelo de embeddings não corresponde ao domínio das consultas  

> [!success]- Resposta
> **Resposta correta: B**
>
> Quando 60 % dos chunks recuperados são irrelevantes, o LLM trabalha com contexto ruidoso e, durante os testes, pode ocasionalmente produzir respostas corretas por acaso com base nesse ruído — mas a confiabilidade em produção sofre significativamente. Contexto irrelevante aumenta o risco de alucinações. Baixa precisão é principalmente um problema de correção, não de custo ou armazenamento.

---

## Questão 4 *(Difícil)*

**Questão**: Um desenvolvedor mede seu pipeline RAG e encontra faithfulness=0.92, mas answer_relevance=0.55. O que essa combinação indica?

A) O LLM fundamenta as respostas no contexto recuperado, mas esse contexto frequentemente não contém as informações que os usuários realmente procuram  
B) A etapa de recuperação é excelente, mas o LLM alucina em 45 % das vezes  
C) O pipeline tem bom desempenho nas duas dimensões  
D) O modelo de embeddings produz vetores de alta qualidade, mas o LLM é prolixo demais  

> [!success]- Resposta
> **Resposta correta: A**
>
> Faithfulness alta (0.92) significa que o LLM fundamenta corretamente as respostas no contexto recuperado — não está alucinando. Answer_relevance baixa (0.55) significa que o contexto recuperado (e, portanto, as respostas) frequentemente não atende à pergunta real do usuário. Trata-se de um problema de recall da recuperação: os documentos corretos não são recuperados. A solução é melhorar a etapa de recuperação (modelo de embeddings melhor, mais contexto, outra estratégia de divisão em chunks).

---

## Questão 5 *(Média)*

**Questão**: Uma equipe cria um pipeline RAG para suporte ao cliente. Ela quer garantir que as respostas citem o artigo de suporte específico utilizado. Qual abordagem permite gerar citações?

A) Definir temperature do LLM como 0 para formatar citações de forma determinística  
B) Armazenar metadados do documento de origem (ID do artigo, título, URL) junto aos embeddings; incluir os metadados no contexto do prompt e instruir o LLM a citar as fontes  
C) Usar um LLM com mais parâmetros — modelos maiores citam fontes automaticamente  
D) Fazer fine-tuning do LLM para sempre incluir citações, treinando-o com exemplos que contenham citações  

> [!success]- Resposta
> **Resposta correta: B**
>
> Gerar citações exige: (1) armazenar metadados da fonte com os embeddings, (2) retorná-los com os resultados, (3) incluí-los no prompt do LLM e (4) instruir o LLM a citar as fontes. Modelos maiores não citam fontes automaticamente; fine-tuning ajuda na formatação, não na identificação da fonte; temperature afeta a aleatoriedade, não as citações.

---

## Questão 6 *(Média)*

**Questão**: Um desenvolvedor está criando um assistente de código que responde a perguntas sobre uma grande base de código privada. A base usa nomes de variáveis fora do padrão e APIs de bibliotecas internas. Qual abordagem oferece a recuperação MAIS precisa de trechos de código relevantes?

A) Usar um modelo de embeddings de texto de uso geral, como `databricks-bge-large-en`  
B) Usar um modelo de embeddings específico para código (por exemplo, `code-search-ada-002` ou similar), ajustado para tarefas de recuperação de código  
C) Usar somente busca por palavras-chave (BM25) — o código é estruturado demais para embeddings semânticos  
D) Indexar somente docstrings de funções, não os corpos do código  

> [!success]- Resposta
> **Resposta correta: B**
>
> Modelos de embeddings específicos para código são treinados com corpora de código e entendem semântica de linguagens de programação, padrões de nomes de variáveis e relações entre APIs melhor que modelos de texto de uso geral. Recuperação somente com BM25 não encontra relações semânticas entre códigos (por exemplo, implementações equivalentes). Indexar somente docstrings descarta a lógica real do código, que pode ser a informação mais relevante para perguntas de implementação.

---

## Questão 7 *(Difícil)*

**Questão**: Um desenvolvedor implementa recuperação de múltiplas consultas no pipeline RAG. O LLM gera 5 variantes de consulta, e cada uma recupera os 5 principais resultados. Após a deduplicação, restam 18 chunks exclusivos. Quantos chunks devem ser enviados à etapa final de geração do LLM?

A) Todos os 18 chunks — mais contexto é sempre melhor  
B) Um subconjunto de 3–5 chunks reranqueados — aplicar um reranker aos 18 candidatos e selecionar os mais relevantes  
C) Somente os 5 resultados mais bem classificados da primeira variante de consulta  
D) 1 chunk — selecionar o chunk com maior pontuação entre todas as 5 consultas  

> [!success]- Resposta
> **Resposta correta: B**
>
> O objetivo da recuperação de múltiplas consultas é melhorar o recall (não enviar todos os chunks recuperados ao LLM). Após a deduplicação, um reranker deve pontuar os 18 candidatos e selecionar os 3–5 mais relevantes para a etapa final de geração. Enviar os 18 chunks consome tokens em excesso da janela de contexto e introduz ruído. Usar somente os resultados da primeira consulta anula o propósito da recuperação de múltiplas consultas.

---

## Questão 8 *(Média)*

**Questão**: Um pipeline RAG de serviços financeiros recupera documentos da SEC. A equipe observa que perguntas sobre o documento de 2023 de uma empresa específica às vezes recuperam documentos de 2021 ou 2022. Como restringir a recuperação a anos fiscais específicos?

A) Pedir ao LLM que faça referência somente aos documentos mais recentes no contexto recuperado  
B) Adicionar metadados `year` e `company` aos documentos indexados e usar pré-filtragem por metadados na consulta de similaridade  
C) Criar um índice vetorial separado para cada ano fiscal e encaminhar consultas de acordo com a detecção de palavras-chave de ano  
D) Usar um modelo de embeddings maior para distinguir melhor documentos de anos diferentes  

> [!success]- Resposta
> **Resposta correta: B**
>
> A pré-filtragem por metadados é a opção correta: armazene os campos `year` e `company` junto aos embeddings e, depois, aplique o filtro na chamada de busca por similaridade. A filtragem feita pelo LLM não é confiável, índices separados por ano aumentam a sobrecarga operacional e o tamanho do modelo de embeddings não ajuda a distinguir temporalmente os documentos.

---

## Questão 9 *(Difícil)*

**Questão**: Um desenvolvedor cria um pipeline RAG em que cada consulta recupera 5 chunks. A avaliação mostra que a resposta correta quase sempre está totalmente contida nos chunks recuperados, mas o LLM ainda responde incorretamente. Qual é a causa raiz MAIS provável?

A) O modelo de embeddings está mal calibrado  
B) O LLM não está extraindo as informações corretas do contexto — o problema está na geração, não na recuperação  
C) A sobreposição dos chunks é grande demais e causa confusão por conteúdo duplicado  
D) O índice vetorial precisa ser recriado com menor dimensionalidade  

> [!success]- Resposta
> **Resposta correta: B**
>
> Quando se confirma que a recuperação é boa (a resposta está nos chunks recuperados), mas a geração está errada, o problema está na etapa de geração. Causas comuns: contexto ruidoso que encobre a resposta ou instruções de prompt inadequadas. A solução é aprimorar o prompt ou comprimir o contexto — não alterar a etapa de recuperação.

---

## Questão 10 *(Difícil)*

**Questão**: Um desenvolvedor implementa um pipeline RAG cujas respostas precisam ser atribuídas a frases específicas (não apenas a chunks de documentos). Qual alteração de arquitetura permite atribuição no nível das frases?

A) Reduzir o tamanho do chunk para uma frase  
B) Indexar chunks de documentos completos, armazenando deslocamentos no nível da frase nos metadados; após a recuperação, destacar quais frases foram citadas pelo LLM usando extração de trechos da resposta  
C) Usar um reranker cross-encoder para identificar a frase mais relevante  
D) Pós-processar a saída do LLM com uma biblioteca de NLP para identificar frases citadas  

> [!success]- Resposta
> **Resposta correta: B**
>
> A atribuição no nível da frase exige armazenar metadados posicionais (deslocamentos das frases) para cada chunk. Após a recuperação e a geração, uma etapa de extração de trechos identifica quais frases específicas nos chunks recuperados sustentam a resposta. Dividir em chunks no nível de cada frase (opção A) fragmenta o contexto e reduz a qualidade da recuperação. Reranking com cross-encoder melhora a precisão, mas não gera automaticamente atribuição no nível das frases.

---

## Questão 11 *(Média)*

**Questão**: Uma equipe descobre que as respostas do pipeline RAG perdem qualidade significativamente em perguntas que exigem raciocínio sobre informações de vários documentos (por exemplo, "Compare os perfis de segurança do Medicamento A e do Medicamento B"). Qual abordagem trata MELHOR o raciocínio entre documentos?

A) Aumentar a dimensionalidade do modelo de embeddings  
B) Usar um top-k maior (por exemplo, k=20) para garantir a recuperação de ambos os documentos e instruir o LLM a sintetizar informações dos chunks recuperados  
C) Reduzir o tamanho dos chunks para 64 tokens para recuperar informações mais granulares  
D) Trocar RAG por um modelo ajustado por fine-tuning  

> [!success]- Resposta
> **Resposta correta: B**
>
> O raciocínio entre documentos exige recuperar os documentos relevantes de todas as entidades comparadas (Medicamento A e Medicamento B). Aumentar k eleva a probabilidade de recuperar ambos. Uma instrução explícita no prompt orienta o LLM a sintetizar informações de vários chunks, em vez de responder usando uma única fonte. Chunks menores aumentam a granularidade, mas podem prejudicar o raciocínio entre documentos ao fragmentar o contexto. Fine-tuning não ajuda em comparações dinâmicas de documentos específicos.

---

## Questão 12 *(Média)*

**Questão**: Um pipeline RAG usa `answer_relevance` como métrica principal de otimização. A equipe atinge `answer_relevance=0.95`, mas a equipe jurídica aponta que o chatbot fornece respostas confiantes que contradizem os documentos de origem. Qual métrica deve ser adicionada para detectar esse modo de falha?

A) `toxicity` — detecta linguagem nociva nas respostas  
B) `faithfulness` — mede se as respostas estão fundamentadas no contexto recuperado  
C) `precision@k` — garante a relevância dos documentos recuperados  
D) `perplexity` — mede a fluência linguística  

> [!success]- Resposta
> **Resposta correta: B**
>
> `faithfulness` (groundedness) mede diretamente se as respostas do LLM têm sustentação nos documentos recuperados. `answer_relevance` alto significa que as perguntas são respondidas, mas sem `faithfulness` o LLM pode gerar respostas plausíveis que contradizem ou acrescentam informações ao material de origem. Para casos jurídicos e de compliance, `faithfulness` é a métrica mais crítica. `toxicity` detecta linguagem nociva, não erros factuais. `precision@k` é uma métrica de recuperação.

---

## Questão 13 *(Média)*

**Questão**: O pipeline RAG de um desenvolvedor funciona bem para perguntas de correspondência exata (por exemplo, "Qual é nossa política de devolução?"), mas tem dificuldade com paráfrases (por exemplo, "Posso receber meu dinheiro de volta?"). Qual técnica MAIS melhora a recuperação para consultas parafraseadas?

A) Adicionar correspondência exata de frases usando BM25 junto à recuperação densa (busca híbrida)  
B) Usar recuperação de múltiplas consultas — gerar paráfrases da consulta do usuário antes de gerar embeddings  
C) Reduzir o tamanho dos chunks para melhorar a granularidade da recuperação  
D) Trocar a métrica de similaridade de cosseno por produto escalar  

> [!success]- Resposta
> **Resposta correta: B**
>
> A recuperação de múltiplas consultas gera várias paráfrases da consulta original, aumentando a probabilidade de que pelo menos uma variante corresponda de perto ao embedding do documento relevante. Isso é particularmente eficaz para o problema de cobertura de paráfrases. Busca híbrida adiciona recuperação esparsa, que é excelente para termos exatos — ajuda no caso oposto (termos exatos semanticamente distantes). O tamanho dos chunks e a métrica de similaridade não tratam a lacuna de cobertura de paráfrases.

---

## Questão 14 *(Difícil)*

**Questão**: Um pipeline RAG de produção tem `faithfulness=0.88` e `answer_relevance=0.82` na avaliação. A equipe quer melhorar as duas métricas. Qual é a ordem CORRETA de prioridades para investigar?

A) Melhorar primeiro faithfulness (qualidade da geração) e, depois, answer_relevance (qualidade da recuperação)  
B) Melhorar primeiro answer_relevance (qualidade da recuperação) e, depois, faithfulness (qualidade da geração) — a recuperação é a base  
C) Otimizar ambas simultaneamente retreinando o modelo de embeddings  
D) Essas métricas não podem ser otimizadas independentemente — somente o retreinamento completo do sistema ajuda  

> [!success]- Resposta
> **Resposta correta: B**
>
> A qualidade da recuperação é a base de RAG. Se os documentos errados forem recuperados (base fraca de answer_relevance), melhorar a qualidade da geração (faithfulness) traz ganhos limitados porque o LLM trabalha com contexto inadequado. Corrija primeiro a recuperação (modelo de embeddings, divisão em chunks, filtragem por metadados) e, depois, otimize a geração (prompting, ordenação do contexto, tipo de chain). É possível melhorar cada uma por meio de componentes diferentes do pipeline.

---

## Vector Search e embeddings (Questões 15–25)

---

## Questão 15 *(Média)*

**Questão**: Um desenvolvedor cria um índice Vector Search chamado `product_search_idx` no endpoint `my_vs_endpoint`. Quer consultá-lo com o texto "wireless noise-canceling headphones". Qual chamada do SDK está CORRETA?

A) `index = vsc.get_index("my_vs_endpoint", "product_search_idx")` e, então, `index.similarity_search(query_text="wireless noise-canceling headphones", columns=["product_id", "title", "description"], num_results=10)`  
B) `vsc.search(endpoint="my_vs_endpoint", index="product_search_idx", query="wireless noise-canceling headphones", top_k=10)`  
C) `index = vsc.get_index("product_search_idx")` e, então, `index.query(query_text="wireless noise-canceling headphones", limit=10)`  
D) `vsc.similarity_search("product_search_idx", "wireless noise-canceling headphones", k=10)`  

> [!success]- Resposta
> **Resposta correta: A**
>
> O padrão correto do SDK é: (1) `vsc.get_index(endpoint_name, index_name)`, (2) `index.similarity_search(query_text=..., columns=[...], num_results=...)`. A opção B (`vsc.search()`), C (sem parâmetro endpoint, usando `.query()`) e D (`vsc.similarity_search()`) usam métodos inexistentes.

---

## Questão 16 *(Fácil)*

**Questão**: Um desenvolvedor tem um índice Direct Access e quer atualizar o embedding do ID de produto `"prod-123"` com uma nova descrição. Qual abordagem está CORRETA?

A) Excluir `"prod-123"` do índice e inseri-lo novamente como novo registro  
B) Chamar `index.upsert()` com os novos dados de embedding para `"prod-123"` — upsert trata tanto inserções quanto atualizações  
C) Recriar todo o índice do zero com o embedding atualizado  
D) Chamar `index.update(id="prod-123", embedding=new_vector)` para atualizá-lo no lugar  

> [!success]- Resposta
> **Resposta correta: B**
>
> `index.upsert()` trata inserções (novos IDs) e atualizações (IDs existentes) em uma única operação. Se `"prod-123"` já existir, seu embedding será substituído. Excluir e inserir novamente funcionaria, mas é desnecessariamente complexo. Recriar todo o índice é extremamente ineficiente. `index.update()` não é um método válido do SDK do Databricks Vector Search.

---

## Questão 17 *(Média)*

**Questão**: Uma equipe indexa 2 milhões de documentos para um pipeline RAG na Databricks. Ela percebe que novos documentos adicionados à tabela Delta de origem levam mais de 30 minutos para aparecer no índice. Como reduzir o atraso da sincronização?

A) Recriar o índice do zero com um fator de replicação maior  
B) Trocar `pipeline_type="TRIGGERED"` por `pipeline_type="CONTINUOUS"` para habilitar a sincronização incremental automática  
C) Reduzir a dimensionalidade vetorial para acelerar a indexação  
D) Aumentar o número de endpoints do Vector Search  

> [!success]- Resposta
> **Resposta correta: B**
>
> `pipeline_type="TRIGGERED"` exige que a sincronização seja iniciada manualmente, o que causa atrasos. Trocar para `pipeline_type="CONTINUOUS"` habilita a sincronização incremental automática e de baixa latência sempre que a tabela Delta muda. O atraso de 30 minutos é compatível com o comportamento de sincronização manual em lote. Dimensionalidade vetorial e quantidade de endpoints não afetam a latência de sincronização. Aumentar a replicação melhora o throughput das consultas, não a velocidade de sincronização.

---

## Questão 18 *(Difícil)*

**Questão**: Uma equipe armazena avaliações de clientes como embeddings em um índice Vector Search com colunas de metadados: `rating` (inteiro de 1 a 5), `product_category` (string) e `verified_purchase` (boolean). Um desenvolvedor quer encontrar avaliações mais semelhantes a "excellent battery life", em que rating >= 4 E verified_purchase = true. Qual dicionário de filtros está CORRETO?

A) `filters={"rating": ">=4", "verified_purchase": "true"}`  
B) `filters={"rating": {"$gte": 4}, "verified_purchase": True}`  
C) `filters="rating >= 4 AND verified_purchase = true"`  
D) `filters=[("rating", ">=", 4), ("verified_purchase", "=", True)]`  

> [!success]- Resposta
> **Resposta correta: B**
>
> Databricks Vector Search usa operadores de filtro no estilo MongoDB: `{"field": {"$gte": value}}` para comparações numéricas. Valores booleanos usam `True`/`False` do Python. Várias condições são expressas como chaves separadas no dicionário de filtros. Strings SQL e formatos de lista de tuplas não são formatos de filtro válidos no SDK.

---

## Questão 19 *(Média)*

**Questão**: Um desenvolvedor quer usar embeddings hospedados pela Databricks com o índice Delta Sync, em vez de embeddings autogerenciados. O que deve especificar durante a criação do índice para habilitar isso?

A) `embedding_source_column` — nome da coluna de texto da qual gerar embeddings  
B) `embedding_vector_column` — coluna que contém vetores pré-calculados  
C) `embedding_model` — URI do modelo de embeddings específico  
D) `embedding_source_column` E o nome do endpoint de modelo de embeddings hospedado  

> [!success]- Resposta
> **Resposta correta: D**
>
> Para usar embeddings hospedados pela Databricks com um índice Delta Sync, especifique: (1) `embedding_source_column` — a coluna de texto a ser convertida automaticamente em embeddings; e (2) o endpoint do modelo de embeddings (por exemplo, `embedding_model_endpoint_name="databricks-bge-large-en"`). Isso instrui a Databricks a calcular automaticamente os embeddings da coluna de texto de origem durante a sincronização. Se os embeddings forem autogerenciados, especifique `embedding_vector_column`.

---

## Questão 20 *(Média)*

**Questão**: Um desenvolvedor quer armazenar o texto principal de um artigo e um campo separado de resumo em seu índice Vector Search, permitindo consultas que correspondam a qualquer um dos campos. Qual abordagem está CORRETA?

A) Criar dois índices Vector Search separados — um para o texto completo e outro para os resumos  
B) Criar um índice que gere embeddings do resumo e armazene o texto completo como coluna de metadados retornada com os resultados  
C) Concatenar o resumo e o texto completo em um único campo antes de gerar embeddings  
D) Fazer duas consultas separadas e combinar os resultados em Python  

> [!success]- Resposta
> **Resposta correta: B**
>
> A abordagem mais comum é gerar embeddings do resumo (que representa melhor as consultas) e armazenar o texto completo do artigo como coluna de metadados. A recuperação se baseia na similaridade com o resumo, mas o LLM recebe o texto completo para gerar a resposta. Dois índices separados exigem duas consultas e a combinação de resultados. Concatenar texto longo e resumo antes de gerar embeddings dilui a qualidade dos embeddings e pode exceder o limite de tokens do modelo.

---

## Questão 21 *(Difícil)*

**Questão**: O índice Vector Search de uma equipe foi criado há 3 meses. A tabela Delta de origem não mudou, mas a qualidade da recuperação piorou à medida que a distribuição das consultas evoluiu. Qual é a ação MAIS adequada?

A) Recriar o índice usando um modelo de embeddings mais recente ou mais adaptado ao domínio  
B) Trocar a métrica de similaridade COSINE por L2  
C) Aumentar o parâmetro `num_results` para recuperar mais candidatos  
D) Adicionar mais nós de trabalho ao endpoint Vector Search  

> [!success]- Resposta
> **Resposta correta: A**
>
> Se os padrões de consulta evoluíram e o modelo de embeddings deixou de estar alinhado à forma como os usuários fazem perguntas, a solução correta é recriar o índice com um modelo mais recente ou adaptado ao domínio. Alterar a métrica de similaridade e ajustar parâmetros traz melhorias marginais. Adicionar workers ao endpoint melhora o throughput e a latência, não a qualidade da recuperação. O problema fundamental é o alinhamento entre modelo, consulta e documento.

---

## Questão 22 *(Média)*

**Questão**: Um desenvolvedor tem um índice Vector Search com 500.000 documentos. Precisa excluir todos os documentos em que `status = "archived"` (cerca de 50.000 documentos). Qual abordagem é a MAIS eficiente?

A) Consultar todos os IDs dos documentos arquivados e, depois, chamar `index.delete(ids=[...])` em lotes  
B) Excluir e recriar o índice inteiro a partir da tabela Delta de origem, já sem os registros arquivados  
C) Marcar os documentos com `status = "inactive"` e filtrá-los no momento da consulta usando filtros de metadados  
D) Usar `index.upsert()` para sobrescrever os documentos arquivados com vetores de embeddings vazios  

> [!success]- Resposta
> **Resposta correta: A**
>
> Para um índice Direct Access, a abordagem correta é excluir por ID em lotes. Em um índice Delta Sync, excluir as linhas da tabela Delta de origem e deixar que a sincronização propague as exclusões é equivalente. A opção C (filtragem no momento da consulta) é uma alternativa válida, mas não remove os documentos do índice (desperdiça armazenamento e computação). A opção B é eficiente somente para uma recriação completa; para 10 % de exclusões, excluir em lotes é mais rápido. Embeddings vazios não têm significado semântico.

---

## Questão 23 *(Média)*

**Questão**: Um desenvolvedor percebe que consultar o índice Vector Search com `num_results=100` demora 3 vezes mais do que com `num_results=10`. Qual afirmação explica MELHOR esse comportamento?

A) Um valor maior de `num_results` exige ler mais dados do armazenamento Delta Lake  
B) Um valor maior de `num_results` exige que o algoritmo ANN explore mais da estrutura de grafo para encontrar vizinhos adicionais  
C) O endpoint Vector Search tem núcleos de CPU insuficientes para lidar com conjuntos grandes de resultados  
D) O parâmetro `num_results` controla diretamente a quantidade de cálculos de similaridade realizados  

> [!success]- Resposta
> **Resposta correta: B**
>
> Algoritmos ANN como HNSW pesquisam uma estrutura de grafo. Retornar mais resultados exige explorar mais nós e arestas, aumentando a computação proporcionalmente. A quantidade de núcleos de CPU afeta o throughput com um `num_results` fixo, não essa relação de escalabilidade. ANN não calcula exaustivamente todos os documentos independentemente de k.

---

## Questão 24 *(Média)*

**Questão**: Um desenvolvedor gera embeddings com `databricks-gte-large-en` e consulta o índice com uma pergunta em francês, apesar de todos os documentos indexados estarem em inglês. Qual é o resultado MAIS provável?

A) A consulta falha porque `databricks-gte-large-en` aceita somente entradas em inglês  
B) A consulta retorna documentos relevantes em inglês se `databricks-gte-large-en` oferecer suporte a embeddings multilíngues; caso contrário, os resultados serão ruins  
C) O Databricks Vector Search traduz automaticamente a consulta em francês para inglês antes de gerar embeddings  
D) A consulta retorna documentos com a maior sobreposição de tokens com os termos em francês  

> [!success]- Resposta
> **Resposta correta: B**
>
> A recuperação entre idiomas depende inteiramente de o modelo de embeddings ter sido treinado com dados multilíngues. Alguns modelos (por exemplo, multilingual-e5) oferecem suporte a recuperação entre idiomas; outros, somente em inglês, não. `databricks-gte-large-en` é principalmente um modelo de inglês; a qualidade da recuperação entre idiomas seria baixa. Databricks Vector Search não traduz automaticamente. A busca não usa sobreposição de tokens — Vector Search é totalmente baseado em vetores.

---

## Questão 25 *(Média)*

**Questão**: Uma equipe usa um índice Delta Sync com `pipeline_type="CONTINUOUS"`. Ela trunca e recarrega a tabela Delta de origem com 500.000 novas linhas (substituindo as 500.000 linhas originais). O que acontece com o índice Vector Search?

A) O índice mantém os embeddings antigos e somente adiciona os 500.000 documentos novos  
B) A sincronização contínua detecta as exclusões e inserções e atualiza o índice para refletir o novo estado da tabela  
C) O índice é corrompido e precisa ser recriado manualmente  
D) O modo contínuo processa somente novas inclusões; truncar exige mudar para o modo Triggered  

> [!success]- Resposta
> **Resposta correta: B**
>
> Um índice Delta Sync `CONTINUOUS` usa o change data feed do Delta Lake para acompanhar todas as operações (inserções, atualizações, exclusões). Truncar e recarregar gera registros de exclusão para as linhas antigas e de inserção para as novas. A sincronização processa ambos automaticamente, fazendo com que o índice corresponda ao novo estado da tabela.

---

## Desenvolvimento de aplicações com LLMs (Questões 26–38)

---

## Questão 26 *(Difícil)*

**Questão**: Um desenvolvedor usa prompting few-shot com 10 exemplos no system prompt. Em entradas complexas, o LLM começa a ignorar o formato demonstrado nos exemplos. Qual intervenção tem MAIS chance de ajudar?

A) Aumentar para 50 o número de exemplos few-shot  
B) Mover os exemplos few-shot para perto do fim do system prompt (mais perto da entrada do usuário) para aproveitar o viés de recência  
C) Trocar prompting few-shot por zero-shot  
D) Reduzir o system prompt à instrução mais importante  

> [!success]- Resposta
> **Resposta correta: B**
>
> LLMs apresentam viés de recência — o conteúdo próximo ao fim do contexto tem mais influência no formato da saída. Colocar os exemplos mais perto da entrada do usuário aumenta a aderência ao formato. Adicionar mais exemplos aumenta o contexto, mas não corrige a influência da posição. Zero-shot remove as demonstrações.

---

## Questão 27 *(Difícil)*

**Questão**: Um desenvolvedor cria um agente LangChain para um sistema de reservas de viagens. O agente tem três ferramentas: `search_flights`, `search_hotels`, `book_trip`. Um usuário pergunta "Encontre opções de voo para Paris na próxima semana". O agente chama incorretamente `book_trip` em vez de `search_flights`. Qual é a causa raiz MAIS provável?

A) A descrição da ferramenta `book_trip` é parecida demais com a de `search_flights`, fazendo o LLM confundi-las  
B) A temperature do LLM está alta demais, causando seleção aleatória de ferramentas  
C) O agente não tem um limite `max_iterations`  
D) A consulta do usuário é curta demais para o LLM interpretar a intenção  

> [!success]- Resposta
> **Resposta correta: A**
>
> Agentes LangChain usam os nomes e as descrições das ferramentas para selecionar a opção adequada. Se `book_trip` tiver uma descrição ambígua (por exemplo, "Trata de todas as solicitações relacionadas a viagens"), o LLM poderá selecioná-la para consultas que deveriam ser encaminhadas a `search_flights`. A solução é escrever descrições claras, distintas e sem sobreposição, especificando explicitamente o que cada ferramenta faz e NÃO faz. Temperature afeta a aleatoriedade, mas não o roteamento sistematicamente incorreto. `max_iterations` evita loops infinitos, não erros de roteamento.

---

## Questão 28 *(Fácil)*

**Questão**: Um desenvolvedor cria um chatbot compatível com inglês e espanhol. O system prompt está em inglês. Um usuário escreve em espanhol, mas o LLM responde em inglês. Como o desenvolvedor pode garantir de forma confiável que o LLM responda no idioma do usuário?

A) Treinar um classificador de detecção de idioma para pré-processar entradas e encaminhá-las a prompts específicos por idioma  
B) Adicionar ao system prompt a instrução: "Sempre responda no mesmo idioma da mensagem do usuário"  
C) Implantar endpoints separados para usuários de inglês e espanhol  
D) Definir `language="auto"` na chamada à Foundation Model API  

> [!success]- Resposta
> **Resposta correta: B**
>
> Adicionar ao system prompt uma instrução para espelhar o idioma ("sempre responda no mesmo idioma em que o usuário escrever") é a solução mais simples e eficaz para LLMs modernos que seguem instruções. Prompts específicos por idioma acrescentam complexidade operacional. Um classificador de pré-processamento adiciona latência e outro modo de falha. Não existe parâmetro `language="auto"` nas Foundation Model APIs.

---

## Questão 29 *(Média)*

**Questão**: Um desenvolvedor registra uma chain RAG com `mlflow.langchain.log_model(chain, "rag-chain", input_example={"query": "What is RAG?"})`. Qual é o PRINCIPAL benefício de fornecer `input_example`?

A) Permite testar o modelo durante a chamada de registro  
B) É usado para inferir e registrar o schema de entrada/saída (assinatura) do modelo, permitindo validação melhor no momento do serving  
C) Armazena uma consulta de exemplo no artefato do modelo somente para fins de documentação  
D) Aquece previamente o cache do modelo para acelerar a primeira consulta durante o serving  

> [!success]- Resposta
> **Resposta correta: B**
>
> O MLflow usa `input_example` para inferir a assinatura do modelo (schema de entrada e saída). A assinatura inferida é armazenada com o artefato do modelo e usada pelo Model Serving para validar os formatos de entrada durante o serving, oferecendo mensagens de erro mais claras para solicitações malformadas. Ela não executa o modelo durante o registro nem serve apenas para documentação — a assinatura afeta funcionalmente a validação do serving.

---

## Questão 30 *(Difícil)*

**Questão**: Um desenvolvedor está criando um pipeline RAG e precisa escolher entre `chain_type="stuff"` e `chain_type="map_reduce"` em `RetrievalQA` do LangChain. A consulta típica recupera 10 chunks de 512 tokens cada (5120 tokens no total). O LLM tem uma janela de contexto de 4096 tokens. Qual tipo de chain é NECESSÁRIO?

A) `"stuff"` — concatenar todos os chunks recuperados em uma chamada ao LLM  
B) `"map_reduce"` — processar cada chunk individualmente e, depois, reduzir os resultados  
C) `"refine"` — refinar a resposta iterativamente  
D) Tanto `"stuff"` quanto `"map_reduce"` — ambos cabem na janela de contexto  

> [!success]- Resposta
> **Resposta correta: B**
>
> Com 10 chunks × 512 tokens = 5120 tokens de contexto recuperado, além do system prompt e da pergunta, o total excede a janela de contexto de 4096 tokens. `"stuff"` exige todo o conteúdo em um único prompt e falharia. `"map_reduce"` processa cada chunk em uma chamada separada ao LLM (etapa map) e, então, combina as respostas intermediárias (etapa reduce), cabendo na janela de contexto. `"refine"` é outra opção para contextos longos, mas processa os chunks sequencialmente.

---

## Questão 31 *(Difícil)*

**Questão**: Um desenvolvedor quer garantir que cada chamada de chain do LangChain registre um campo de metadados personalizado `session_id` no MLflow para rastreamento de solicitações. Qual abordagem é a MAIS adequada?

A) Chamar `mlflow.set_tag("session_id", value)` antes de cada invocação da chain  
B) Usar um `MLflowCallbackHandler` personalizado para registrar a tag `session_id` durante a execução da chain  
C) Adicionar `session_id` como chave extra ao dicionário de entrada da chain  
D) Definir a variável de ambiente `MLFLOW_RUN_ID` com o valor de session_id  

> [!success]- Resposta
> **Resposta correta: B**
>
> Um `BaseCallbackHandler` personalizado (ou `MLflowCallbackHandler`) pode ser passado como callback nas invocações da chain para registrar metadados personalizados durante a execução. Callbacks têm acesso ao contexto da execução e podem chamar `mlflow.set_tag()` nos eventos apropriados do ciclo de vida. Chamar `mlflow.set_tag()` antes da chain também funciona dentro de um contexto de execução ativo, mas um callback é o padrão mais robusto para tracing por invocação. Adicionar `session_id` ao dicionário de entrada altera o schema de entrada da chain.

---

## Questão 32 *(Difícil)*

**Questão**: Um desenvolvedor usa uma chain `RetrievalQA` do LangChain e, após depuração, descobre que a qualidade das respostas depende muito da ORDEM dos chunks recuperados no prompt. Especificamente, o chunk mais relevante no meio do contexto produz respostas piores do que quando aparece no início. O que isso representa?

A) Um problema de configuração do índice vetorial  
B) O problema "lost in the middle" — LLMs prestam menos atenção ao conteúdo no meio de contextos longos  
C) Uma incompatibilidade de dimensões do modelo de embeddings  
D) Um problema de calibração de temperature  

> [!success]- Resposta
> **Resposta correta: B**
>
> O fenômeno "lost in the middle" é bem documentado: LLMs tendem a prestar mais atenção ao conteúdo no início e no fim de contextos longos, e às vezes dão menos peso ao conteúdo no meio. A solução é posicionar os chunks recuperados mais relevantes no início ou no fim do contexto, não no meio. Trata-se de uma estratégia de ordenação do contexto, não de um problema de indexação ou configuração do modelo.

---

## Questão 33 *(Média)*

**Questão**: Um desenvolvedor chama uma API externa de clima em uma ferramenta de agente LangChain. A API ocasionalmente retorna erros HTTP 429 (limite de taxa excedido). Como a ferramenta deve tratar isso?

A) Deixar a exceção chegar ao agente — o LLM tentará novamente automaticamente  
B) Implementar lógica de repetição com backoff exponencial dentro da função da ferramenta; se as tentativas se esgotarem, retornar uma mensagem informativa ao LLM  
C) Definir um timeout muito longo para a chamada à API para evitar o erro  
D) Armazenar em cache para sempre todas as respostas da API de clima para evitar chamadas repetidas  

> [!success]- Resposta
> **Resposta correta: B**
>
> As funções de ferramenta devem implementar tratamento robusto de erros, incluindo lógica de repetição com backoff exponencial para erros transitórios, como limites de taxa. Se as tentativas se esgotarem, a ferramenta deve retornar uma mensagem de erro descritiva ao LLM para que ele possa informar o usuário sobre a falha. Agentes LangChain não repetem automaticamente chamadas de ferramenta com falha. Timeouts longos não evitam erros de limite de taxa. Armazenar dados meteorológicos em cache para sempre retornaria resultados desatualizados.

---

## Questão 34 *(Difícil)*

**Questão**: Um desenvolvedor avalia seu pipeline RAG com `mlflow.evaluate()`. O conjunto de dados de avaliação tem 200 linhas, mas `mlflow.evaluate()` termina em 5 segundos — rápido demais para métricas baseadas em juízes LLM. Qual é a explicação MAIS provável?

A) A avaliação está sendo executada em hardware com GPU  
B) A avaliação usou resultados armazenados em cache de uma execução anterior  
C) As métricas baseadas em juízes LLM não foram especificadas corretamente, e somente métricas sem LLM foram usadas  
D) A função predict do modelo está retornando respostas armazenadas em cache  

> [!success]- Resposta
> **Resposta correta: C**
>
> Métricas que usam LLM como juiz precisam chamar um LLM para cada linha de avaliação, o que leva vários segundos por linha — pelo menos alguns minutos para 200 linhas. Se `mlflow.evaluate()` termina em 5 segundos, provavelmente executou somente métricas sem LLM (por exemplo, correspondência exata, similaridade de strings). Isso significa que as métricas baseadas em juízes LLM não foram configuradas corretamente ou que o parâmetro `extra_metrics` não foi especificado adequadamente. Verifique as métricas registradas para confirmar se faithfulness, answer_relevance etc. aparecem.

---

## Questão 35 *(Média)*

**Questão**: Uma organização implanta um chatbot RAG para funcionários internos e usa `ConversationSummaryMemory` para manter o contexto durante uma sessão longa. Após 30 turnos, um usuário pergunta sobre um assunto mencionado no turno 3. Qual problema tem MAIS chance de ocorrer?

A) A chain gera um erro de falta de memória  
B) Detalhes específicos do turno 3 podem ter sido perdidos durante a sumarização, impedindo o LLM de recordá-los com precisão  
C) O LLM gera um erro porque o resumo excede a janela de contexto  
D) O modelo de embeddings reindexa automaticamente o histórico da conversa  

> [!success]- Resposta
> **Resposta correta: B**
>
> `ConversationSummaryMemory` comprime o histórico em resumos. Detalhes específicos e formulações precisas dos primeiros turnos (como o turno 3) frequentemente se perdem. O LLM talvez se lembre do assunto geral, mas não dos fatos exatos. Para recordação precisa, `ConversationBufferMemory` ou memória baseada em vetores é mais adequada.

---

## Questão 36 *(Média)*

**Questão**: Um desenvolvedor cria um pipeline LangChain com várias etapas, em que a Etapa 1 gera consultas de busca e a Etapa 2 recupera documentos usando essas consultas. Ele quer que as saídas de cada etapa sejam registradas separadamente no MLflow para depuração. Qual abordagem permite isso?

A) Chamar `mlflow.log_param()` após cada etapa com a saída correspondente  
B) Usar `mlflow.langchain.autolog()` com `log_traces=True` para capturar cada etapa da chain como um span de trace  
C) Registrar duas execuções MLflow separadas — uma para cada etapa  
D) Usar `mlflow.set_tag("step1_output", ...)` para armazenar resultados intermediários  

> [!success]- Resposta
> **Resposta correta: B**
>
> `mlflow.langchain.autolog(log_traces=True)` habilita MLflow Tracing, que captura cada etapa da chain como um span separado com entradas e saídas. Isso oferece observabilidade completa de pipelines com várias etapas sem modificar o código da chain. Execuções separadas rompem o agrupamento lógico do pipeline. `log_param` serve para valores de configuração, não para dados estruturados de trace. Tags são pares de chave-valor de string, não são adequadas para saídas intermediárias estruturadas.

---

## Questão 37 *(Média)*

**Questão**: Um desenvolvedor quer carregar uma chain LangChain registrada com `mlflow.langchain.log_model()` e fazer previsões programaticamente. O URI do modelo é `runs:/abc123/rag-chain`. Qual código fornece uma interface chamável `.predict()`?

A) `chain = mlflow.langchain.load_model("runs:/abc123/rag-chain")` e, então, `chain.invoke({"query": "What is RAG?"})`  
B) `model = mlflow.pyfunc.load_model("runs:/abc123/rag-chain")` e, então, `model.predict({"query": "What is RAG?"})`  
C) `model = mlflow.models.load_model("runs:/abc123/rag-chain")` e, então, `model({"query": "What is RAG?"})`  
D) Tanto A quanto B são válidas  

> [!success]- Resposta
> **Resposta correta: D**
>
> Ambas são abordagens válidas. `mlflow.langchain.load_model()` carrega o objeto nativo de chain do LangChain (use `.invoke()` ou `.run()` conforme a API do LangChain). `mlflow.pyfunc.load_model()` carrega-o como wrapper pyfunc (use `.predict()` conforme a API MLflow pyfunc). Ambos funcionam com o mesmo URI do artefato. A opção C usa `mlflow.models.load_model()`, método que não existe na API Python do MLflow.

---

## Questão 38 *(Média)*

**Questão**: Um desenvolvedor quer comparar duas estratégias de prompting RAG em 50 perguntas de teste usando MLflow. A Estratégia A usa um system prompt conciso; a Estratégia B usa um system prompt detalhado, com vários parágrafos. Qual é o padrão MLflow CORRETO?

A) Registrar as duas estratégias como tags em uma única execução e compará-las nela  
B) Criar duas execuções MLflow separadas (uma por estratégia) no mesmo experimento, executar `mlflow.evaluate()` para cada uma e, então, comparar as execuções na interface do experimento  
C) Criar dois experimentos separados, um para cada estratégia  
D) Registrar as estratégias como versões de modelo no Model Registry e comparar as métricas de avaliação  

> [!success]- Resposta
> **Resposta correta: B**
>
> Cada estratégia deve ter uma execução MLflow separada no mesmo experimento, com `mlflow.evaluate()` registrando métricas padronizadas (faithfulness, answer_relevance etc.) para cada uma. A interface de comparação do experimento permite comparar diretamente as métricas lado a lado. Experimentos separados não podem ser comparados diretamente na interface MLflow. Registrar as duas estratégias como tags em uma execução confunde os resultados. Model Registry serve para versionar modelos implantáveis, não para comparar estratégias A/B.

---

## Ferramentas GenAI da Databricks (Questões 39–45)

---

## Questão 39 *(Média)*

**Questão**: Um desenvolvedor chama o endpoint `databricks-meta-llama-3-1-70b-instruct` usando o cliente Python da OpenAI apontado para o host da Databricks. A chamada funciona durante o desenvolvimento, mas falha em produção com erro 401 Unauthorized. Qual é a causa MAIS provável?

A) O nome do modelo está incorreto para o endpoint Databricks  
B) O ambiente de produção está usando um token de autenticação Databricks expirado ou ausente  
C) O endpoint não oferece suporte à biblioteca cliente da OpenAI  
D) O modelo não está disponível na região do workspace de produção  

> [!success]- Resposta
> **Resposta correta: B**
>
> Databricks Foundation Model APIs exigem autenticação por meio de um PAT da Databricks (personal access token) ou token OAuth de service principal. Um erro 401 Unauthorized indica credenciais ausentes ou expiradas. Em produção, o parâmetro `api_key` do cliente OpenAI precisa receber um token Databricks válido. Endpoints Databricks Foundation Model são compatíveis com OpenAI e oferecem suporte ao cliente OpenAI. Problemas de nome do endpoint e disponibilidade regional produzem outros códigos de erro.

---

## Questão 40 *(Média)*

**Questão**: Uma equipe implanta uma chain RAG do LangChain em um endpoint Databricks Model Serving. Os logs do endpoint mostram latências frequentes de cold start de 60–90 segundos na primeira solicitação após períodos de inatividade. Qual solução é a MAIS adequada para uma aplicação de produção voltada a clientes?

A) Trocar para um endpoint Provisioned Throughput, que não escala para zero  
B) Aumentar a quantidade de tokens na janela de contexto do LLM  
C) Recriar o índice vetorial com menor dimensão de embeddings  
D) Reduzir a complexidade da chain LangChain removendo o reranker  

> [!success]- Resposta
> **Resposta correta: A**
>
> Endpoints Model Serving podem escalar para zero quando estão ociosos, causando latência de cold start quando o tráfego recomeça. Endpoints Provisioned Throughput mantêm réplicas mínimas (não escalam para zero), eliminando atrasos de cold start. Essa é a solução padrão para aplicações de produção sensíveis à latência. O tamanho da janela de contexto, a dimensionalidade vetorial e a complexidade da chain não têm relação com cold starts.

---

## Questão 41 *(Difícil)*

**Questão**: Uma equipe quer monitorar o chatbot RAG implantado para detectar desvio de dados — especificamente, detectar quando os assuntos das consultas dos usuários se afastam significativamente da distribuição de treinamento. Qual abordagem da Databricks está CORRETA?

A) Usar Databricks Lakehouse Monitoring na tabela de inferência com um monitor `TimeSeries` para detectar ao longo do tempo o desvio na distribuição das consultas  
B) Criar um alerta de experimento MLflow que seja acionado quando `faithfulness` cair abaixo de 0.80  
C) Habilitar a atualização automática do índice Vector Search para detectar novos padrões de consulta  
D) Usar um Databricks Job para calcular semanalmente a similaridade de cosseno entre novas consultas e consultas de treinamento  

> [!success]- Resposta
> **Resposta correta: A**
>
> Tabelas de inferência registram todas as solicitações e respostas em uma tabela Delta. Databricks Lakehouse Monitoring com um monitor `TimeSeries` pode detectar desvio estatístico na distribuição das consultas (entradas) ao longo do tempo usando métricas como o population stability index (PSI). Essa é a solução da Databricks projetada especificamente para monitorar desvios de dados durante o serving. Alertas MLflow não são um mecanismo de monitoramento em tempo real. Atualizar o índice Vector Search e calcular similaridade manualmente não são soluções de monitoramento de desvio.

---

## Questão 42 *(Média)*

**Questão**: Um engenheiro precisa permitir que várias equipes chamem o mesmo endpoint Foundation Model, cada uma com limites de taxa diferentes. Qual componente da Databricks oferece esse recurso?

A) Políticas de cluster — limitam a computação por equipe  
B) Permissões do Unity Catalog — restringem o acesso ao endpoint por equipe  
C) MLflow AI Gateway — configura limites de taxa por rota para equipes ou chaves de API diferentes  
D) Databricks Secrets — armazena chaves de API por equipe com tempos de expiração diferentes  

> [!success]- Resposta
> **Resposta correta: C**
>
> MLflow AI Gateway oferece configuração por rota, inclusive limites de taxa (solicitações por minuto, tokens por minuto). É possível configurar rotas para equipes ou casos de uso diferentes, cada um com suas próprias políticas de limites. Políticas de cluster controlam a configuração de clusters, não o acesso a APIs LLM. Unity Catalog governa o acesso a dados e modelos, mas não define limites de taxa LLM por equipe. Secrets armazena credenciais, mas não aplica limites de taxa.

---

## Questão 43 *(Média)*

**Questão**: Uma equipe registra sua chain RAG e os resultados de avaliação no MLflow. Quer registrar a chain como uma versão de modelo e adicionar uma descrição que explique a estratégia de divisão em chunks utilizada. Qual API do MLflow faz isso?

A) `mlflow.log_param("chunking_strategy", "recursive-512-overlap-50")`  
B) `MlflowClient().update_model_version(name="rag-chain", version=1, description="Recursive chunking, 512 tokens, 50-token overlap")`  
C) `mlflow.set_tag("chunking_strategy", "recursive-512-overlap-50")`  
D) `mlflow.register_model(model_uri, "rag-chain", description="...")`  

> [!success]- Resposta
> **Resposta correta: B**
>
> `MlflowClient.update_model_version()` atualiza a descrição de uma versão específica do modelo no Model Registry. Essa é a API correta para adicionar descrições legíveis a modelos registrados. `mlflow.log_param()` e `mlflow.set_tag()` registram informações em execuções, não em versões do Model Registry. `mlflow.register_model()` não aceita o parâmetro `description` na API Python (a descrição é definida separadamente após o registro).

---

## Questão 44 *(Difícil)*

**Questão**: Um desenvolvedor implantou uma chain RAG no Model Serving e habilitou tabelas de inferência. Ele consulta a tabela de inferência e vê linhas com `response` igual a `null` e `status_code` igual a `500`. O que isso indica e como investigar?

A) O endpoint retornou HTTP 200, mas o corpo da resposta estava vazio  
B) O endpoint encontrou erros internos (falhas no servidor) nessas solicitações — examine os logs do endpoint para encontrar stack traces  
C) O schema da tabela de inferência está configurado incorretamente  
D) O cliente enviou solicitações malformadas com payloads nulos  

> [!success]- Resposta
> **Resposta correta: B**
>
> Códigos HTTP 500 indicam erros no lado do servidor do endpoint model serving. A tabela de inferência registra corretamente o código de status junto à resposta nula das solicitações com falha. Para diagnosticar, examine os logs do endpoint Model Serving (na guia de eventos do endpoint na interface Databricks ou via REST API) em busca de mensagens de exceção e stack traces. Isso pode indicar problemas no código da chain, falhas de dependência (por exemplo, conectividade com Vector Search) ou esgotamento de recursos.

---

## Questão 45 *(Difícil)*

**Questão**: Uma equipe está decidindo entre MLflow AI Gateway e chamadas diretas à Foundation Model API para sua organização com várias equipes. Qual cenário torna MLflow AI Gateway claramente a melhor escolha?

A) Um único cientista de dados prototipando um pipeline RAG em um notebook  
B) Uma organização com 10 equipes, vários provedores de LLM, requisitos de gerenciamento centralizado de credenciais e políticas de limites de taxa por equipe  
C) Um job de inferência em lote que é executado uma vez por dia em um conjunto de dados fixo  
D) Um desenvolvedor que precisa da menor latência possível para um único endpoint de tráfego intenso  

> [!success]- Resposta
> **Resposta correta: B**
>
> MLflow AI Gateway agrega mais valor em cenários com várias equipes e vários provedores, nos quais são necessários gerenciamento centralizado de credenciais, abstração de API unificada e limites de taxa por equipe. Um cientista de dados trabalhando sozinho em um notebook não precisa dos recursos organizacionais do Gateway. Jobs em lote se beneficiam de chamadas diretas à API (sem sobrecarga do Gateway). O Gateway adiciona um salto de rede que aumenta a latência — chamadas diretas à API são mais rápidas para aplicações com um endpoint e latência crítica.

---

**Fim do Simulado 2 — 45 questões**

[← Voltar às instruções do Simulado 2](./README.md) | [← Simulado 1](../mock-exam/questions.md)

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

**[← Voltar às instruções do Simulado 2](./README.md)** | **[← Voltar aos recursos](../README.md)** | **[← Voltar a GenAI Engineer Associate](../../README.md)**
