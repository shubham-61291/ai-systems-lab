# Local vs Managed Vector Store — RAG Benchmark

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Embeddings](https://img.shields.io/badge/Embeddings-BGE--Large-5B6DAA)](https://huggingface.co/BAAI/bge-large-en)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**An exploratory benchmark comparing NumPy in-memory cosine retrieval with Pinecone retrieval for a single policy document and a fixed question set.**

## Overview

This project compares a small custom vector store with a managed Pinecone index within a document-grounded Q&A workflow. The current source, `custom_pinecone_comparison.py`, loads one uploaded document (DOCX, PDF, or TXT), splits it into overlapping word chunks, embeds each chunk with `BAAI/bge-large-en`, then executes the same 15 fixed policy questions against both stores.

For each store, the script measures retrieval latency and LLM response latency. It uses an embedding-similarity threshold to compute retrieval proxies and asks `gpt-4.1-mini` to score answer correctness and faithfulness. The repository contains plots and a copy of the sample Sony Bangalore office-policy DOCX.

This is a **single-corpus, fixed-query engineering experiment**, not a broad retrieval benchmark. Its outputs should not be generalized to all document sizes, providers, workloads, embedding models, or cost structures.

## Experimental design

| Element | Current implementation |
|---|---|
| Corpus | One uploaded policy document; the committed example is `Sony Bangalore Office Policy Creation.docx` |
| Chunking | 400 words per chunk with 50-word overlap by default |
| Embeddings | `BAAI/bge-large-en`, normalized vectors, 1,024 dimensions |
| Local retrieval | Custom `VectorDB` class with NumPy cosine similarity; top 3 chunks |
| Managed retrieval | Pinecone query, top 3 chunks, same embedded chunks |
| Question set | 15 policy-related questions hard-coded in the script |
| Retrieval proxies | Similarity threshold of 0.40 is used for recall/precision-like calculations, MRR, and nDCG-like score |
| Answer evaluation | `gpt-4.1-mini` judge asks for correctness and context-faithfulness scores between 0 and 1 |
| Latency | Retrieval latency, LLM latency, and their sum recorded per question |

The source’s “ROC-style correctness curve” plots the fraction of judged answers above a range of correctness-score thresholds. It is not a classical ROC curve derived from labeled positive/negative examples. The retrieval metrics also use a similarity threshold instead of a human-labeled relevance set, so they should be read as **proxies** rather than definitive information-retrieval scores.

## Pipeline

```text
Upload policy file
       |
       v
Extract text -> split into overlapping chunks
       |
       v
BAAI/bge-large-en embeddings
       |
       +-------------------------+
       |                         |
       v                         v
NumPy in-memory VectorDB     Pinecone index
       |                         |
       +------------+------------+
                    v
          Same fixed question set
                    |
                    v
      GPT-4.1-mini answer generation
                    |
                    v
       LLM-as-judge + latency metrics
                    |
                    v
      Charts and per-question results
```

## Setup and execution

The committed file is a Google Colab export: it contains `!pip` shell commands and imports `google.colab.files`. As written, it is not a normal terminal-executable Python module. It also has empty placeholders for the OpenAI key, Pinecone key, and Pinecone index name.

### Prerequisites

- Python with `numpy`, `sentence-transformers`, `python-docx`, `PyPDF2`, `openai`, `tqdm`, `pinecone`, and `matplotlib`.
- OpenAI API access for answer generation and LLM judging.
- A Pinecone API key and a valid index name configured for 1,024 dimensions and cosine similarity (or a deliberately adapted provisioning step).
- Permission to use the document that is uploaded.

### Run

1. Open the source in Colab or adapt it: remove `!pip` lines, replace `files.upload()` with local file selection/path handling, and load credentials from environment variables or a secrets manager.
2. Set the Pinecone index name and keys in secure runtime configuration; do not commit tokens.
3. Upload the test policy document.
4. Run the workflow and capture both console summaries and plots.
5. Record corpus version, question set, model IDs, similarity threshold, hardware, region, and network conditions before comparing runs.

## Included results

- [RAG metric comparison](./ragmetric_comparison.png)
- [Retrieval latency heatmap](./retrival_heatmap.png)
- [Total latency heatmap](./latency_heatmap.png)
- [ROC-style correctness threshold curve](./rocstyle_correctness_curve.png)
- [Example policy source document](./Sony%20Bangalore%20Office%20Policy%20Creation.docx)
- [`custom_pinecone_comparison.py`](./custom_pinecone_comparison.py)

The images are committed result artifacts. Reproducing them depends on API response variation, provider/network conditions, hardware, and configuration; the script includes an LLM judge, so the evaluation itself is not fully deterministic.

## How to interpret results responsibly

- **Local versus managed trade-off:** local in-memory retrieval avoids a network request for each query, but local execution consumes host RAM/CPU and is not automatically the right choice for every system. Pinecone provides managed storage/operations that this small local vector store does not.
- **Quality validity:** there is one document and a fixed set of 15 questions. A broader study should use multiple corpora, more queries, gold relevance labels, repeat runs, confidence intervals, and documented infrastructure.
- **LLM-as-judge:** correctness/faithfulness scores are model-generated, not human-ground-truth labels; they can be biased or inconsistent.
- **Threshold dependence:** similarity `0.40` is a code constant. Metric values depend on this threshold and are not directly comparable with results obtained using a different embedding model or threshold.
- **Cost claims:** the script does not calculate total infrastructure cost or model token costs. Cost conclusions should be measured rather than inferred from retrieval latency alone.
- **Credentials and private data:** source documents are submitted to the hosted LLM for answers and evaluation; Pinecone receives document chunk vectors/metadata. Use only data approved for those services.

## Author

**Shubham Kumar Singh**

## License

The following MIT License text is also provided as a standalone [`LICENSE`](./LICENSE) file in this project directory.

MIT License

Copyright (c) 2026 Shubham Kumar Singh

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
