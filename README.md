# Policy Q&A Retrieval

A document-grounded retrieval-augmented generation (RAG) agent built in **n8n**. It chunks and embeds a 33-page policy manual into a vector database, then answers natural-language questions using only what it retrieves.

Unlike most RAG demos, the testing focuses on the **retrieval layer** rather than the model: if the right chunks never come back, no prompt can save the answer.

## How it works

```
policy manual ──► chunking ──► embeddings ──► vector database
                                                   │
question ──► embed query ──► semantic search ──────┘
                                   │
                         top-k chunks above similarity threshold
                                   │
                                   ▼
                      LLM answers, grounded in retrieved text only
```

## Retrieval test suite

The test questions target the ways retrieval actually fails:

| Test category | What it checks |
|---|---|
| **Vocabulary mismatch** | The question uses different words than the manual (synonyms, paraphrases) |
| **Distractor density** | Several sections look relevant but only one holds the answer |
| **Table-boundary integrity** | Answers that live in tables split across chunk boundaries |
| **Out-of-corpus queries** | Questions the manual can't answer; the agent should say so instead of guessing |
| **Conflicting clauses** | Separate sections that must be reconciled to get the right answer |

The out-of-corpus queries were used to calibrate the similarity threshold. In the conflicting-clause tests, the agent correctly reconciled clauses from separate sections and rejected a plausible but wrong answer that keyword matching would have returned.

## Tech stack

n8n workflow automation · embeddings + vector database · semantic retrieval · LLM answer generation
