---
title: Questões de prática — Arquitetura RAG
type: practice-questions
tags: [genai-engineer-associate, practice-questions, rag-architecture]
status: published
---

# Questões de prática — Arquitetura RAG

15 questões sobre padrões de design RAG, estratégias de divisão em chunks, métodos de recuperação e avaliação de RAG.

[← Voltar às questões de prática](./README.md) | [Próximo: Vector Search e embeddings →](./02-vector-search-embeddings.md)

---

## Questão 1 *(Fácil)*: RAG versus fine-tuning

**Questão**: Uma empresa tem um grande repositório proprietário de documentos, atualizado semanalmente com novos relatórios. Ela quer um LLM que responda a perguntas usando os documentos mais recentes. Qual abordagem é a MELHOR?

A) Implementar um pipeline RAG que recupere documentos relevantes no momento da consulta  
B) Fazer fine-tuning mensal de um LLM com todo o corpus de documentos  
C) Incorporar todos os documentos na janela de contexto do LLM durante a inferência  
D) Treinar um modelo de embeddings personalizado com o corpus proprietário  

> [!success]- Resposta
> **Resposta correta: A**
>
> RAG é a escolha correta quando o conhecimento muda com frequência. O fine-tuning exige novo treinamento sempre que os dados mudam, o que é caro e demorado. Não é viável incorporar todos os documentos à janela de contexto, pois documentos atuais excedem em muito os limites dessa janela. Treinar um modelo de embeddings personalizado pode ser um aprimoramento opcional, mas não substitui um pipeline de recuperação.

---

## Questão 2 *(Média)*: Seleção da estratégia de divisão em chunks

**Questão**: Um engenheiro de dados divide relatórios financeiros em PDF, com 100 páginas, em chunks para um pipeline RAG. Os relatórios têm parágrafos densos e não apresentam cabeçalhos consistentes. Qual estratégia de divisão em chunks é a MAIS adequada?

A) Divisão em chunks de tamanho fixo sem sobreposição  
B) Divisão recursiva de texto por caracteres com sobreposição  
C) Divisão por página (um chunk por página do PDF)  
D) Divisão no nível da frase, usando apenas delimitadores de pontuação  

> [!success]- Resposta
> **Resposta correta: B**
>
> A divisão recursiva de texto por caracteres respeita limites naturais do texto (parágrafos, frases e palavras, por ordem de prioridade) e usa sobreposição para evitar a perda de contexto nos limites dos chunks. A divisão de tamanho fixo sem sobreposição pode cortar frases ao meio. A divisão por página gera chunks grandes demais para a maioria dos modelos de embeddings e janelas de contexto. A divisão apenas por frases pode gerar chunks curtos demais para conter contexto útil.

---

## Questão 3 *(Média)*: Compensações no tamanho dos chunks

**Questão**: Uma equipe está criando um pipeline RAG e discute se deve usar chunks pequenos (128 tokens) ou grandes (1024 tokens). Qual afirmação descreve MELHOR essa compensação?

A) Chunks menores sempre melhoram a qualidade das respostas porque reduzem o ruído  
B) Chunks maiores sempre melhoram a qualidade das respostas porque incluem mais contexto  
C) Chunks menores melhoram a precisão da recuperação; chunks maiores preservam mais contexto, mas podem incluir conteúdo irrelevante  
D) O tamanho dos chunks afeta apenas a velocidade de indexação e não influencia a qualidade da recuperação  

> [!success]- Resposta
> **Resposta correta: C**
>
> É mais fácil associar chunks menores a perguntas específicas com precisão (maior precisão de recuperação), mas eles talvez não contenham contexto suficiente para que o LLM gere uma resposta completa. Chunks maiores preservam mais contexto, mas podem incluir conteúdo sem relação com a consulta e confundir o LLM. O tamanho ideal depende da estrutura dos documentos e do tipo de pergunta.

---

## Questão 4 *(Fácil)*: Finalidade da sobreposição entre chunks

**Questão**: Uma equipe configura o pipeline de divisão em chunks com tamanho de 512 tokens e sobreposição de 50 tokens. Qual é a PRINCIPAL finalidade da sobreposição de 50 tokens?

A) Aumentar o throughput da indexação, reduzindo o número total de chunks  
B) Aumentar a dimensionalidade dos vetores de embeddings para melhorar a recuperação  
C) Armazenar metadados sobre o documento de origem para filtragem  
D) Garantir que o contexto nos limites dos chunks esteja disponível também nos chunks adjacentes  

> [!success]- Resposta
> **Resposta correta: D**
>
> A sobreposição garante que o conteúdo próximo ao fim de um chunk também apareça no início do chunk seguinte. Isso evita que informações importantes que cruzam o limite sejam perdidas ou separadas do contexto. A sobreposição não afeta a velocidade de indexação, a dimensionalidade nem os metadados.

---

## Questão 5 *(Média)*: Recuperação densa versus esparsa

**Questão**: Um sistema RAG para uma base de conhecimento médica precisa recuperar documentos que contenham tanto o nome específico de um medicamento (correspondência exata do termo) quanto condições semanticamente relacionadas. Qual abordagem de recuperação é a MELHOR?

A) Recuperação híbrida, combinando métodos densos e esparsos  
B) Somente recuperação esparsa — a correspondência de palavras-chave BM25 atende aos termos exatos  
C) Somente recuperação densa — a similaridade de embeddings captura todas as relações  
D) Filtragem por palavra-chave no campo de metadados antes da recuperação densa  

> [!success]- Resposta
> **Resposta correta: A**
>
> A recuperação híbrida combina recuperação densa (baseada em embeddings) para similaridade semântica com recuperação esparsa (BM25/palavras-chave) para correspondência exata de termos. É ideal quando é preciso corresponder termos exatos (nomes de medicamentos, IDs) com precisão e, ao mesmo tempo, compreender a semântica. A recuperação apenas densa pode não dar peso suficiente a termos exatos; a recuperação apenas esparsa não captura relações semânticas.

---

## Questão 6 *(Média)*: Precisão versus recall na recuperação

**Questão**: Um pipeline RAG usa recuperação top-k com k=3 e tem baixa qualidade de resposta porque o documento correto geralmente não está entre os três primeiros. Qual ajuste deve ser feito PRIMEIRO?

A) Diminuir k para 2 para reduzir o ruído no contexto  
B) Aumentar k para recuperar mais candidatos e, em seguida, aplicar reranking  
C) Trocar a similaridade de cosseno pela distância L2  
D) Reduzir o tamanho dos chunks para 64 tokens  

> [!success]- Resposta
> **Resposta correta: B**
>
> Quando o documento correto não é recuperado (baixo recall), aumentar k recupera mais candidatos. Adicionar um reranker melhora a precisão ao recalcular a pontuação do conjunto maior de candidatos. Diminuir k reduz ainda mais o recall. Mudar a métrica de similaridade não resolve o problema se o documento correto não estiver sendo recuperado. Chunks menores afetam a granularidade, mas não corrigem o problema de recall da recuperação.

---

## Questão 7 *(Fácil)*: Finalidade do reranking

**Questão**: Um pipeline RAG recupera os 20 documentos mais relevantes usando similaridade vetorial e, em seguida, aplica um reranker cross-encoder antes de enviar os 5 primeiros ao LLM. Qual é o PRINCIPAL benefício do reranking?

A) Converter embeddings vetoriais em sequências de tokens para o LLM  
B) Filtrar documentos com baixa dimensionalidade de embeddings  
C) Melhorar a precisão da recuperação usando um modelo de relevância mais preciso no conjunto de candidatos  
D) Reduzir o tempo de indexação pré-ordenando o banco de dados vetorial  

> [!success]- Resposta
> **Resposta correta: C**
>
> Rerankers cross-encoder são mais precisos que a similaridade de bi-encoder (usada na busca vetorial), pois codificam conjuntamente a consulta e cada documento. A abordagem em duas etapas (recuperação ampla → reranking preciso) equilibra recall e precisão. O reranking não afeta a indexação, a dimensionalidade nem a conversão de tokens.

---

## Questão 8 *(Fácil)*: Métrica de faithfulness em RAG

**Questão**: Depois de implantar um pipeline RAG, uma avaliação revela que o LLM frequentemente faz afirmações não sustentadas pelos documentos recuperados. Qual métrica representa MELHOR esse problema?

A) Answer relevance — mede se a resposta atende à pergunta  
B) Recall da recuperação — mede se os documentos corretos são recuperados  
C) Perplexity — mede a fluência do texto gerado  
D) Faithfulness — mede se a resposta está fundamentada no contexto recuperado  

> [!success]- Resposta
> **Resposta correta: D**
>
> Faithfulness (também chamada groundedness) mede se cada afirmação da resposta gerada pode ser atribuída ao contexto recuperado. Quando o LLM alucina fatos que não constam nos documentos recuperados, o valor de faithfulness é baixo. Answer relevance mede a correspondência com a pergunta, não a fundamentação factual. O recall da recuperação mede a qualidade da recuperação, não a qualidade da geração. Perplexity mede fluência, não precisão factual.

---

## Questão 9 *(Média)*: RAG versus fine-tuning — adaptação de estilo

**Questão**: Uma empresa quer que um LLM responda exclusivamente em linguagem jurídica formal e adote o formato de citação preferido pelo escritório. O conteúdo do conhecimento em si não mudou. Qual abordagem é a MAIS adequada?

A) Fazer fine-tuning do LLM com exemplos de respostas no estilo desejado  
B) Implementar RAG com uma coleção de exemplos de documentos jurídicos formais  
C) Aumentar o tamanho do system prompt com instruções detalhadas de estilo  
D) Usar um modelo de embeddings maior e com maior dimensionalidade  

> [!success]- Resposta
> **Resposta correta: A**
>
> Fine-tuning é a melhor opção para alterar o **estilo, tom e formato** do modelo quando o conhecimento subjacente já está presente nele. RAG adiciona conhecimento externo, mas não altera o formato das respostas de forma confiável. System prompts podem influenciar o estilo, mas são menos robustos que fine-tuning para requisitos rígidos de formato. O tamanho do modelo de embeddings não tem relação com o estilo de geração.

---

## Questão 10 *(Média)*: Gerenciamento da janela de contexto

**Questão**: Um pipeline RAG recupera os 20 chunks mais relevantes, cada um com 512 tokens. O LLM tem uma janela de contexto de 4096 tokens. Qual cenário fará o pipeline FALHAR?

A) Usar o mesmo modelo de embeddings para indexação e consulta  
B) Enviar os 20 chunks (10.240 tokens), além do system prompt, ao LLM  
C) Usar similaridade de cosseno para a recuperação em vez da distância L2  
D) Armazenar metadados dos chunks no índice vetorial junto aos embeddings  

> [!success]- Resposta
> **Resposta correta: B**
>
> 20 chunks × 512 tokens = 10.240 tokens, o que excede a janela de contexto de 4096 tokens. Isso faz com que a chamada à API do LLM falhe ou trunque a entrada sem aviso. A solução é reduzir k (por exemplo, para top-5), usar chunks menores ou usar um LLM com janela de contexto maior. A consistência do modelo de embeddings, a escolha da métrica de similaridade e o armazenamento de metadados não causam falhas na janela de contexto.

---

## Questão 11 *(Média)*: Recuperação com HyDE

**Questão**: Um sistema RAG usa Hypothetical Document Embeddings (HyDE). O que essa técnica envolve?

A) Gerar várias perguntas hipotéticas a partir de cada chunk do documento durante a indexação  
B) Criar dados sintéticos de treinamento por meio do aumento hipotético de documentos esparsos  
C) Usar o LLM para gerar uma resposta hipotética e, em seguida, gerar um embedding dessa resposta para recuperar documentos reais semelhantes  
D) Substituir metadados ausentes por valores hipotéticos inferidos do conteúdo do documento  

> [!success]- Resposta
> **Resposta correta: C**
>
> HyDE transforma a consulta do usuário pedindo ao LLM que gere um documento com uma resposta hipotética. Essa resposta hipotética é então convertida em embedding e usada como consulta de recuperação. Como a resposta hipotética está no "espaço de respostas", e não no "espaço de perguntas", ela pode ficar mais próxima dos documentos reais de resposta no espaço vetorial, melhorando a recuperação de consultas complexas.

---

## Questão 12 *(Fácil)*: Recuperação de múltiplas consultas

**Questão**: Uma equipe implementa recuperação de múltiplas consultas no pipeline RAG. O que essa técnica faz?

A) Envia a mesma consulta a vários bancos de dados vetoriais e combina os resultados  
B) Indexa cada documento várias vezes usando diferentes modelos de embeddings  
C) Divide a consulta do usuário em várias subconsultas usando delimitadores de pontuação  
D) Usa o LLM para gerar várias paráfrases da consulta original, recupera resultados para cada uma e une os resultados  

> [!success]- Resposta
> **Resposta correta: D**
>
> A recuperação de múltiplas consultas usa o LLM para reescrever a consulta original sob diferentes perspectivas (por exemplo, com formulações ou subperguntas diferentes). Cada variante é usada separadamente na recuperação e a união dos documentos recuperados proporciona uma cobertura mais ampla. Isso melhora o recall de perguntas complexas que podem ser formuladas de maneiras diferentes.

---

## Questão 13 *(Média)*: Divisão em chunks pai-filho

**Questão**: Uma equipe implementa a divisão em chunks pai-filho. Chunks filhos pequenos são usados na recuperação; chunks pais maiores são retornados ao LLM. Qual é a PRINCIPAL vantagem em relação à divisão em chunks de tamanho uniforme?

A) Melhora a precisão da recuperação com chunks pequenos e, ao mesmo tempo, fornece contexto mais rico com chunks pais grandes  
B) Reduz o tamanho do índice vetorial armazenando vetores menores  
C) Elimina a necessidade de um modelo de embeddings, usando correspondência exata de palavras-chave  
D) Permite que vários usuários compartilhem o mesmo chunk pai ao mesmo tempo  

> [!success]- Resposta
> **Resposta correta: A**
>
> É mais fácil associar chunks filhos pequenos a perguntas específicas com precisão (alta precisão na recuperação). Depois de encontrar o chunk filho relevante, seu chunk pai maior é recuperado e enviado ao LLM, fornecendo contexto mais rico que o chunk pequeno sozinho. Isso combina a precisão da recuperação de chunks pequenos com a riqueza contextual de chunks maiores.

---

## Questão 14 *(Média)*: Dimensões da avaliação RAG

**Questão**: Uma equipe quer avaliar seu pipeline RAG de forma abrangente. Qual combinação de métricas abrange tanto a qualidade da recuperação QUANTO a qualidade da geração?

A) Pontuações BLEU e ROUGE  
B) Precision@k e faithfulness  
C) Perplexity e contagem de tokens  
D) Similaridade de embeddings e contagem de chunks  

> [!success]- Resposta
> **Resposta correta: B**
>
> Precision@k mede a qualidade da recuperação — que fração dos k principais documentos recuperados é realmente relevante. Faithfulness mede a qualidade da geração — se a resposta está fundamentada no contexto recuperado. Juntas, elas abrangem os dois principais modos de falha do RAG: recuperar documentos errados e alucinar conteúdo ausente nos documentos recuperados. BLEU/ROUGE medem a similaridade superficial do texto. Perplexity mede fluência, não precisão factual.

---

## Questão 15 *(Fácil)*: Filtragem por metadados em RAG

**Questão**: Um pipeline RAG indexa documentos de vários departamentos. Uma consulta do usuário deve recuperar somente documentos do departamento `legal`. Qual é a melhor forma de implementar isso?

A) Filtrar os resultados recuperados em Python, verificando os metadados dos documentos após a recuperação  
B) Treinar um modelo de embeddings separado para cada departamento  
C) Usar filtragem por metadados na consulta de busca vetorial para restringir os candidatos pelo departamento antes da busca semântica  
D) Criar um índice vetorial separado para cada departamento e consultar todos os índices simultaneamente  

> [!success]- Resposta
> **Resposta correta: C**
>
> A maioria dos bancos de dados vetoriais (incluindo Databricks Vector Search) aceita pré-filtragem por metadados, que restringe o conjunto de candidatos por atributos de metadados antes da busca semântica. É mais eficiente que a filtragem posterior (que desperdiça computação com documentos irrelevantes) e mais simples do que manter índices separados por departamento.

---

**[↑ Voltar às questões de prática — GenAI Engineer Associate](./README.md) | [Próximo: Questões de prática — Vector Search e embeddings](./02-vector-search-embeddings.md) →**
