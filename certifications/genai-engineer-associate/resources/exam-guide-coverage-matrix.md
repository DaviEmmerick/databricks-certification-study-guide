---
title: "Exam-Guide Coverage Matrix — GenAI Engineer Associate"
type: reference
tags: [genai-engineer-associate, exam-guide, coverage, practice-questions]
status: published
---

# Exam-Guide Coverage Matrix — GenAI Engineer Associate

Which practice and mock-exam questions test each of the 56 bullets in the [official exam guide](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-gen-ai-associate-exam-guide-august-2026.pdf) (exam published March 18, 2026). Use it to turn wrong answers into a study list, and to see which bullets the question banks test thinly.

## How to use it

1. Take a timed diagnostic first: [Mock Exam 1](./mock-exam/README.md) plus practice sets [05–07](./practice-questions/README.md) and Q15–16 of set 03, which cover the 2026 bullets the mocks miss.
2. Count a lucky guess as wrong. For each wrong question, find its bullet below.
3. Open the domain folder's `README.md` → **Exam Guide Coverage** table to get the exact chapter section for that bullet. Read that section in full; for bullets you got right, read only the chapter's *Exam Tips* and *Key Takeaways*.
4. Hold back [Mock Exam 2](./mock-exam-2/README.md) as the final check.

## Reference key

- **PQ03 Q5** — [practice set](./practice-questions/README.md) `03-…`, question 5
- **M1 Q12** / **M2 Q12** — [Mock Exam 1](./mock-exam/questions.md) / [Mock Exam 2](./mock-exam-2/questions.md), question 12
- **M1 GOV-1**, **M2 DATA-1** — supplementary items after question 45 (not in the timed set)
- **Guide Q4** — sample question 4 in the [exam guide PDF](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-gen-ai-associate-exam-guide-august-2026.pdf)

## Section 1: Design Applications (14 %)

Study material: [`03-design-applications/`](../03-design-applications/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S1.1 | Design a prompt that elicits a specifically formatted response | M1 Q42 | 1 |
| S1.2 | Select model tasks to accomplish a given business requirement | PQ01 Q1, PQ01 Q9, PQ05 Q4, M1 Q1, M1 Q11 | 5 |
| S1.3 | Select chain components for a desired model input and output | M1 Q8, M1 Q18, M1 Q37, M2 Q11 | 4 |
| S1.4 | Translate business use case goals into a description of the desired inputs and outputs for the AI pipeline | M2 Q1 | 1 |
| S1.5 | Define and order tools that gather knowledge or take actions for multi-stage reasoning | PQ03 Q6, PQ03 Q11, PQ05 Q5, PQ05 Q6, M1 Q31, M2 Q27, M2 Q33 | 7 |
| S1.6 | Determine how and when to use Agent Bricks (Knowledge Assistant, Multiagent Supervisor, Information Extraction) to solve problems | PQ05 Q1, PQ05 Q2, PQ05 Q3 | 3 |

## Section 2: Data Preparation (14 %)

Study material: [`04-data-preparation/`](../04-data-preparation/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S2.1 | Apply a chunking strategy for a given document structure and model constraints | PQ01 Q2, PQ01 Q3, PQ01 Q4, PQ01 Q10, M1 Q2, M1 Q9, M2 Q2, Guide Q1 | 8 |
| S2.2 | Filter extraneous content in source documents that degrades quality of a RAG application | PQ05 Q8 | 1 |
| S2.3 | Choose the appropriate Python package to extract document content from provided source data and format | PQ05 Q7, Guide Q3 | 2 |
| S2.4 | Define operations and sequence to write given chunked text into Delta Lake tables in Unity Catalog | PQ05 Q9, M2 DATA-1 | 2 |
| S2.5 | Identify needed source documents that provide necessary knowledge and quality for a given RAG application | Guide Q2 | 1 |
| S2.6 | Use tools and metrics to evaluate retrieval performance | PQ01 Q14, PQ05 Q10, M2 Q3 | 3 |
| S2.7 | Design retrieval systems using advanced chunking strategies | PQ01 Q13, M1 Q12, M2 Q10 | 3 |
| S2.8 | Explain the role of re-ranking in the information retrieval process | PQ01 Q6, PQ01 Q7, PQ02 Q11, M1 Q3, M1 Q14, M2 Q7 | 6 |

## Section 3: Application Development (30 %)

Study material: [`01-application-development/`](../01-application-development/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S3.1 | Select Langchain/similar tools for use in a Generative AI application | PQ03 Q4, PQ03 Q5, M2 Q30 | 3 |
| S3.2 | Qualitatively assess responses to identify common issues such as quality and safety | M1 Q27, M2 Q9, M2 Q32 | 3 |
| S3.3 | Select chunking strategy based on model & retrieval evaluation | PQ03 Q15, PQ03 Q16 | 2 |
| S3.4 | Augment a prompt with additional context from a user's input based on key fields, terms, and intents | PQ02 Q10, M2 Q5, M2 Q13 | 3 |
| S3.5 | Create a prompt that adjusts an LLM's response from a baseline to a desired output | PQ03 Q1, PQ03 Q2, M1 Q36, M2 Q26, M2 Q28 | 5 |
| S3.6 | Implement LLM guardrails to prevent negative outcomes | PQ03 Q3, PQ03 Q14, M1 Q34 | 3 |
| S3.7 | Select the best LLM based on the attributes of the application to be developed | Guide Q5 | 1 |
| S3.8 | Select an embedding model context length based on source documents, expected queries, and optimization strategy | PQ06 Q5, M2 Q24, Guide Q4 | 3 |
| S3.9 | Select a model from a model hub or marketplace for a task based on model metadata/model cards | M1 Q21 | 1 |
| S3.10 | Select the best model for a given task based on common metrics generated in experiments | M2 APPDEV-1, M2 Q38 | 2 |
| S3.11 | Utilize MLflow and Agent Framework for developing agentic systems | PQ06 Q1, M1 AGENT-1, M1 Q26 | 3 |
| S3.12 | Compare the evaluation and monitoring phases of the Gen AI application life cycle | PQ06 Q4, M2 APPDEV-2 | 2 |
| S3.13 | Enable multi-agent systems to leverage Genie Spaces or conversational API to retrieve data | PQ06 Q2, PQ06 Q3 | 2 |

## Section 4: Assembling and Deploying Applications (22 %)

Study material: [`02-assembling-and-deploying-apps/`](../02-assembling-and-deploying-apps/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S4.1 | Code a chain using a pyfunc model with pre- and post-processing | PQ06 Q14, PQ06 Q15 | 2 |
| S4.2 | Control access to resources from model serving endpoints | PQ06 Q8, PQ06 Q9, M1 Q43 | 3 |
| S4.3 | Code a simple chain according to requirements | PQ03 Q9, M1 Q33 | 2 |
| S4.4 | Choose the basic elements needed to create a RAG application: model flavor, embedding model, retriever, dependencies, input examples, model signature | PQ03 Q10, M1 Q32, M2 Q6, M2 Q29 | 4 |
| S4.5 | Register the model to Unity Catalog using MLflow | PQ04 Q4, M1 Q44, M2 Q37, M2 Q43 | 4 |
| S4.6 | Create and query a Vector Search index | PQ01 Q15, PQ02 Q5, PQ02 Q7, M1 Q10, M1 Q19, M1 Q20, M1 Q22, M2 Q8, M2 Q15, M2 Q16, M2 Q18, M2 Q19, M2 Q22 | 13 |
| S4.7 | Identify how to serve an LLM application that leverages Foundation Model APIs | PQ03 Q13, PQ04 Q1, PQ04 Q2, PQ04 Q7, M1 Q39, M2 Q39, M2 Q40 | 7 |
| S4.8 | Explain the key concepts and components of Mosaic AI Vector Search | PQ01 Q5, PQ02 Q1, PQ02 Q2, PQ02 Q3, PQ02 Q4, PQ02 Q8, PQ02 Q9, PQ02 Q12, M1 Q15, M1 Q16, M1 Q17, M1 Q25, M2 Q20, M2 Q25 | 14 |
| S4.9 | Identify batch inference workloads and apply ai_query() appropriately | PQ06 Q10, M2 BATCH-1 | 2 |
| S4.10 | Configure vector search for a particular solution based on number of embeddings, update frequency, latency, and cost requirements | PQ02 Q6, PQ05 Q12, M1 Q23, M1 Q24, M2 Q17, M2 Q21, M2 Q23, Guide Q6 | 8 |
| S4.11 | Configure a persistent datastore to store and retrieve intermediate memory or structured information | PQ06 Q11, M1 Q28, M1 Q30, M2 Q35 | 4 |
| S4.12 | Apply CI/CD best practices such as updating a Vector Search index, promoting prompts across environments, and testing individual components of an agent | M1 GW-1, M1 Q6, Guide Q7 | 3 |
| S4.13 | Integrate managed, external, and custom MCP servers based on a given application requirements | PQ06 Q6, PQ06 Q7, Guide Q9 | 3 |
| S4.14 | Apply prompt version control and manage prompt lifecycle | PQ06 Q12, M2 PROMPT-1 | 2 |
| S4.15 | Develop an appropriate interactive user facing interface for an agent usage scenario (Apps, Slack, Teams, etc.) | PQ06 Q13, Guide Q8 | 2 |

## Section 5: Governance (8 %)

Study material: [`06-governance/`](../06-governance/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S5.1 | Use masking techniques as guard rails to meet a performance objective | PQ07 Q10, M1 GOV-1 | 2 |
| S5.2 | Select guardrail techniques to protect against malicious user inputs to a Gen AI application | PQ03 Q12, PQ07 Q11 | 2 |
| S5.3 | Use legal/licensing requirements for data sources to avoid legal risk | PQ07 Q12 | 1 |
| S5.4 | Recommend an alternative for problematic text mitigation in a data source feeding a GenAI application | PQ07 Q17, PQ07 Q18 | 2 |

## Section 6: Evaluation and Monitoring (12 %)

Study material: [`05-evaluation-and-monitoring/`](../05-evaluation-and-monitoring/README.md)

| ID | Exam-guide bullet | Questions | # |
| :--- | :--- | :--- | :---: |
| S6.1 | Select an LLM choice (size and architecture) based on a set of quantitative evaluation metrics | PQ07 Q13, PQ07 Q14 | 2 |
| S6.2 | Select key metrics to monitor for a specific LLM deployment scenario | PQ01 Q8, M2 Q12 | 2 |
| S6.3 | Evaluate agent performance using MLflow scoring and tracing | PQ03 Q7, PQ03 Q8, PQ04 Q5, M1 EVAL-1, M1 Q5, M1 Q7, M1 Q13, M1 Q29, M1 Q35, M1 Q40, M1 Q45, M2 Q4, M2 Q14, M2 Q31, M2 Q34, M2 Q36 | 16 |
| S6.4 | Use inference logging to assess deployed RAG application performance | PQ07 Q15, PQ07 Q16 | 2 |
| S6.5 | Use Databricks features to control LLM costs | PQ07 Q9 | 1 |
| S6.6 | Use inference tables and Agent Monitoring to track a live LLM endpoint | PQ04 Q6, PQ07 Q6, M1 GOV-2, M1 Q41, M2 Q41, M2 Q44 | 6 |
| S6.7 | Identify evaluation judges that require ground truth | PQ05 Q11, PQ07 Q1, PQ07 Q2 | 3 |
| S6.8 | Use AI Gateway (Inference Tables, Usage Tables, and rate limiting) to track an LLM or agent deployed via Agent Framework | PQ04 Q3, PQ07 Q7, PQ07 Q8, M2 Q42, M2 Q45 | 5 |
| S6.9 | Use Databricks custom Scorers for evaluating agents and LLMs | PQ07 Q3, PQ07 Q4, M1 Q38 | 3 |
| S6.10 | Incorporate SME feedback to improve agent performance | PQ07 Q5, Guide Q10 | 2 |

## Thinly tested bullets

Bullets with one question across all banks and the guide samples: S1.1, S1.4, S2.2, S2.5, S3.7, S3.9, S5.3, S6.5.

## Questions outside the exam guide

These questions test a technique no bullet names. They are still good RAG practice; do not count them in the diagnostic: PQ01 Q11, PQ01 Q12, M1 Q4.

## How this matrix was built

- Each question was mapped to the one bullet whose knowledge picks the correct answer (what it *tests*, not the keywords in its scenario). Secondary bullets are not listed.
- The mapping is heuristic: 180 questions were classified with AI assistance and spot-checked; 29 were low-confidence, so a bullet's count can be off by one.
- Questions added in the November 2026 coverage series: PQ03 Q15–16, PQ06 Q14–15, PQ07 Q13–18, and the Mock Exam 2 supplementary block.
- Mapped on November 4, 2026, against the exam guide published March 18, 2026. Re-map when Databricks issues a new exam version.

---

**[← Back to Exam Resources](./README.md)**
