# Guia de estudo — Databricks Generative AI Engineer Associate

Este guia reúne as informações da prova, uma ordem de estudo e os materiais mais úteis deste repositório para você se preparar para a **Databricks Certified Generative AI Engineer Associate**. Use os capítulos vinculados como material de referência; este arquivo é o seu ponto de partida.

> [!important]
> A referência é o guia oficial da prova publicado em **18 de março de 2026** e reemitido como edição **agosto de 2026**. O conteúdo da plataforma muda rapidamente: estude o que o exame cobra e também reconheça os nomes e caminhos atuais indicados nas notas de atualização.

## 1. Informações da prova

| Item | Informação |
| :--- | :--- |
| Certificação | Databricks Certified Generative AI Engineer Associate |
| Questões pontuadas | 45 questões de múltipla escolha e múltipla seleção |
| Questões não pontuadas | Até 10; não são identificadas e o tempo já as considera |
| Duração | 90 minutos |
| Idiomas | Inglês, japonês, português do Brasil e coreano |
| Resultado | Aprovado/reprovado; o guia não publica uma nota mínima numérica |
| Recursos durante a prova | Nenhum |
| Experiência recomendada | Pelo menos 6 meses de experiência prática com os tópicos do guia |
| Preço listado | US$ 200, mais impostos aplicáveis |
| Aplicação | Online com fiscal, conforme o guia; consulte a página oficial para opções atuais |

- [Página oficial da certificação](https://www.databricks.com/learn/certification/genai-engineer-associate)
- [Guia oficial da prova — PDF, edição de agosto de 2026](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-gen-ai-associate-exam-guide-august-2026.pdf)
- [Informações da prova e recursos neste repositório](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/README.md)

## 2. Domínios e prioridades

As quantidades de questões abaixo são **estimativas proporcionais** dos 45 itens pontuados, não uma distribuição garantida. Comece pelos dois domínios de maior peso, mas não ignore os tópicos novos ou as áreas menores.

| Domínio do guia oficial | Peso | Estimativa de questões | Material principal |
| :--- | :---: | :---: | :--- |
| Application Development | 30 % | ~13–14 | [Aplicação](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/README.md) |
| Assembling and Deploying Applications | 22 % | ~10 | [Montagem e implantação](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/README.md) |
| Design Applications | 14 % | ~6 | [Design de aplicações](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/03-design-applications/README.md) |
| Data Preparation | 14 % | ~6 | [Preparação de dados](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/04-data-preparation/README.md) |
| Evaluation and Monitoring | 12 % | ~5–6 | [Avaliação e monitoramento](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/05-evaluation-and-monitoring/README.md) |
| Governance | 8 % | ~3–4 | [Governança](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/06-governance/README.md) |

Cada índice de domínio contém uma tabela **Exam Guide Coverage**, que relaciona as habilidades listadas no guia oficial aos capítulos que as explicam. Consulte essas tabelas para conferir a cobertura completa, não apenas os resumos deste roteiro.

## 3. Roteiro de estudo

O cronograma abaixo distribui aproximadamente **32–40 horas** em quatro semanas. Ajuste o ritmo à sua disponibilidade e avance para simulados quando conseguir explicar as decisões, não apenas reconhecer os termos.

### Semana 1 — Desenvolvimento de aplicações (10–12 horas)

- [Prompt engineering](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/01-prompt-engineering.md): estrutura de prompts, exemplos, formato de saída e injeção de prompt.
- [Chains e agents](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/02-chains-agents.md): fluxo fixo versus ciclo de decisão do agente, ferramentas e chamadas de função.
- [Estratégias de RAG](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/03-retrieval-augmentation-strategies.md) e [Vector Search em runtime](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/04-vector-search-runtime.md).
- [Sistemas agentic com MLflow e Genie](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/05-agentic-systems-mlflow-and-genie.md) e [seleção de modelos e qualidade](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/01-application-development/06-model-selection-and-response-quality.md).

### Semana 2 — Montagem e implantação (8–10 horas)

- [Foundation Model APIs](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/01-mosaic-ai-and-foundation-models.md): compare cobrança pay-per-token e provisioned throughput.
- [MLflow para GenAI](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/02-mlflow-for-genai.md) e [compound AI apps](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/03-compound-ai-apps.md).
- [Servidores MCP](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/04-mcp-servers.md): saiba distinguir opções gerenciadas, externas e customizadas.
- [Controle de acesso e inferência em lote](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/05-access-control-and-batch-inference.md).
- [Memória, CI/CD, prompts e interfaces](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/02-assembling-and-deploying-apps/06-memory-cicd-prompts-and-interfaces.md).

### Semana 3 — Design e preparação de dados (8–10 horas)

- [Design de RAG](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/03-design-applications/01-rag-design-patterns.md): RAG versus fine-tuning, recuperação e re-ranking.
- [Design de solução e planejamento de ferramentas](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/03-design-applications/02-solution-design-and-tool-planning.md): traduza o objetivo de negócio em entradas, saídas e componentes.
- [Agent Bricks](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/03-design-applications/03-agent-bricks.md): saiba escolher a solução adequada para cada cenário do exame.
- [Processamento e chunking](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/04-data-preparation/01-document-processing-chunking.md), [embeddings](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/04-data-preparation/02-embeddings-models.md), [índices](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/04-data-preparation/03-databricks-vector-search.md) e [extração e avaliação da recuperação](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/04-data-preparation/04-source-documents-extraction-and-retrieval-eval.md).

### Semana 4 — Avaliação, monitoramento, governança e revisão (6–8 horas)

- [Avaliação de aplicações LLM](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/05-evaluation-and-monitoring/01-evaluation-llm-apps.md), [monitoramento online](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/05-evaluation-and-monitoring/02-online-monitoring.md) e [judges, scorers e feedback](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/05-evaluation-and-monitoring/03-mlflow-scorers-judges-and-feedback.md).
- [AI Gateway, uso e custos](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/05-evaluation-and-monitoring/04-ai-gateway-usage-and-cost-control.md). No guia da prova, esse conteúdo está em **Evaluation and Monitoring**, não em Governance.
- [Governança](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/06-governance/01-governance-overview.md): mascaramento, proteção contra entradas maliciosas, licenças e tratamento de conteúdo problemático.
- Faça as [questões de prática](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/practice-questions/README.md), depois os simulados da seção [Prática e simulados](#6-prática-e-simulados), e revise os assuntos errados.

## 4. Conceitos que você precisa saber explicar

Use esta lista para testar sua compreensão; os links de cada domínio acima levam ao conteúdo completo.

- **RAG e recuperação:** etapas de ingestão, extração, limpeza, chunking, embeddings, indexação, recuperação, re-ranking, montagem do contexto e geração. Saiba quando RAG é mais adequado que fine-tuning.
- **Chunks e embeddings:** escolha tamanho e sobreposição conforme documento, janela de contexto, qualidade e custo; use o mesmo modelo de embedding para indexar e consultar.
- **Vector Search:** compare índice Delta Sync, sincronizado a partir de uma tabela Delta, com Direct Vector Access; entenda compromissos de latência, escala e custo.
- **Chains, agents e ferramentas:** chain segue etapas definidas; agent escolhe ações e ferramentas durante um ciclo. A ordem das ferramentas deve deixar ações irreversíveis para o final e protegê-las.
- **Seleção de modelos:** escolha conforme tarefa, qualidade, latência, custo, contexto e licença; use model cards e resultados de avaliação, não apenas o tamanho do modelo.
- **Implantação:** entenda MLflow, `ResponsesAgent`, registro de modelos no Unity Catalog, recursos declarados no log do modelo, controle de acesso e `ai_query()` para inferência em lote.
- **MCP e agentes:** saiba quando usar servidores MCP gerenciados, externos ou customizados; reconheça os cenários do Agent Bricks e o uso de Genie para perguntas sobre dados estruturados.
- **Avaliação versus monitoramento:** avaliação mede uma aplicação com conjuntos de dados e scorers antes da publicação; monitoramento observa tráfego e traces em produção. Saiba quais judges exigem ground truth.
- **Operação e custo:** conheça tracing, inference tables, usage tables, limites de taxa, feedback de especialistas (SMEs), Prompt Registry, aliases e memória persistente.
- **Segurança e governança:** trate PII antes da criação de embeddings; aplique least privilege, guardrails, requisitos de licença e mitigação de conteúdo problemático na fonte.

## 5. Materiais de base e conteúdos complementares

Você não precisa concluir as outras certificações para fazer esta prova. Use somente os fundamentos que complementam o domínio GenAI.

### Fundamentos compartilhados

- [RAG e Vector Search Basics](./shared/fundamentals/rag-vector-search-basics.md)
- [MLflow Basics](./shared/fundamentals/mlflow-basics.md)
- [Unity Catalog Basics](./shared/fundamentals/unity-catalog-basics.md)
- [Databricks Workspace](./shared/fundamentals/databricks-workspace.md)
- [Python Essentials](./shared/fundamentals/python-essentials.md)
- [Trilha de aprendizagem GenAI](./learning-paths/genai-path.md)

### O que vale consultar nas demais certificações

| Trilha | Conteúdo útil para esta prova |
| :--- | :--- |
| [Data Analyst Associate](./certifications/data-analyst-associate/README.md) | [Genie Spaces/Agents](./certifications/data-analyst-associate/04-developing-sharing-maintaining-genie-spaces/README.md) — conhecimento útil para Genie em sistemas multi-agent; não substitui o foco GenAI em integração e API. |
| [Data Engineer Associate](./certifications/data-engineer-associate/README.md) | [Governança e segurança](./certifications/data-engineer-associate/07-governance-and-security/README.md) — reforça Unity Catalog e controle de acesso. |
| [Data Engineer Professional](./certifications/data-engineer-professional/README.md) | [Manipulação de dados](./certifications/data-engineer-professional/03-data-manipulation/README.md) — referência adicional para SQL, PySpark e `ai_query()`. |
| [ML Associate](./certifications/ml-associate/README.md) | [Databricks Machine Learning](./certifications/ml-associate/01-databricks-machine-learning/README.md) e [deploy de modelos](./certifications/ml-associate/04-model-deployment/README.md) — MLflow, registro no Unity Catalog e serving. |
| [ML Professional](./certifications/ml-professional/README.md) | Consulta opcional para aprofundar MLOps, MLflow e operação de modelos; não é pré-requisito para a prova GenAI Associate. |

## 6. Prática e simulados

1. Faça as [questões por tópico](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/practice-questions/README.md). Os conjuntos 05–07 enfatizam habilidades do guia de 2026; as questões deste repositório são de resposta única, enquanto a prova real também inclui múltipla seleção.
2. Pratique o fluxo completo de RAG no [Lab 05 — Vector Search/AI Search e RAG](./labs/05-mosaic-ai-rag-demo.md). Para revisar permissões e ativos no Unity Catalog, consulte também o [Lab 02 — Unity Catalog](./labs/02-unity-catalog-setup.md).
3. Faça o [Simulado 1](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/mock-exam/README.md) e o [Simulado 2](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/mock-exam-2/README.md), cada um com 45 questões e limite de 90 minutos. Os simulados são de resposta única; pratique múltipla seleção separadamente.
4. Após cada sessão, use os respectivos [debriefs do Simulado 1](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/mock-exam/debrief.md) e do [Simulado 2](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/mock-exam-2/debrief.md) para voltar aos capítulos das questões erradas.
5. Na reta final, leia as [dicas de prova](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/exam-tips.md) e a [revisão final de 20 minutos](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/final-review.md).

Para a prova, as dicas do repositório sugerem **60 minutos** para a primeira passagem, **20 minutos** para revisar questões sinalizadas e **10 minutos** para uma conferência final. Em questões de múltipla seleção, selecione exatamente a quantidade pedida e avalie cada alternativa individualmente.

## 7. Laboratório prático: requisitos e atualização de nomes

O [Lab 05](./labs/05-mosaic-ai-rag-demo.md) percorre preparação de documentos, embeddings, índice, recuperação, `ResponsesAgent`, registro e implantação. Ele requer um workspace Databricks com Unity Catalog e disponibilidade de AI Search/Vector Search e Foundation Model APIs. A [Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition) pode ser usada para prática, mas confira suas [limitações](https://docs.databricks.com/aws/en/getting-started/free-edition-limitations) antes de começar.

> [!note] Nomes atuais versus nomes cobrados
> O material da prova acompanha a superfície do exame de março de 2026. A documentação de outubro de 2026 informa mudanças posteriores: **Vector Search** passou a se chamar **AI Search**; **Genie Spaces**, **Genie Agents**; e o caminho de agents no **Model Serving**, além de Knowledge Assistant e Multi-Agent Supervisor, foi marcado como legacy. Para responder à prova, reconheça os nomes do guia; para projetos novos, consulte as notas atualizadas em cada capítulo e a documentação atual.

## 8. Links oficiais e de referência

- [Todos os links oficiais e documentação curada para a certificação](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/resources/official-links.md)
- [Desenvolvimento de agents no Databricks](https://docs.databricks.com/aws/en/agents/)
- [Databricks AI Search (antigo Vector Search)](https://docs.databricks.com/aws/en/ai-search/ai-search)
- [Avaliação e monitoramento GenAI com MLflow](https://docs.databricks.com/aws/en/mlflow3/genai/eval-monitor/)
- [AI Gateway / Unity Gateway](https://docs.databricks.com/aws/en/ai-gateway/)
- [Treinamentos Databricks Academy](https://www.databricks.com/learn/training)

---

**[← Voltar ao README do repositório](./README.md) | [Abrir o guia completo da certificação](./genai-engineer-associate-ptbr/pt-br/certifications/genai-engineer-associate/README.md)**
