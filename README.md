# AI Systems Lab

[![Python](https://img.shields.io/badge/Language-Python-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![AI Engineering](https://img.shields.io/badge/Focus-AI%20Systems%20Engineering-34495E)](https://github.com/shubham-61291/ai-systems-lab)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A portfolio of seven applied AI systems exploring multimodal interaction, local language-model training, retrieval-augmented workflows, control and authorization patterns, sustainability analysis, and vector-store evaluation.**

AI Systems Lab focuses on the engineering around models: data flow, component boundaries, runtime assumptions, failure handling, measurement, and deployment trade-offs. Each subdirectory is an independent project and should be assessed against its own code, prerequisites, and limitations; there is no shared runtime or single application entry point.

## Project index

| Project | What it demonstrates | Main entry point / evidence |
|---|---|---|
| [ELARA — Agentic Avatar](./ELARA-Agentic-Avatar/README.md) | Voice/text interaction connected to OpenAI responses, Google Cloud speech services, and an external PyTorch talking-head pipeline. | [`elara.py`](./ELARA-Agentic-Avatar/elara.py), interface and video examples |
| [GREEN-COMPUTE-MRV](./GREEN-COMPUTE-MRV/README.md) | Compute-energy scenario estimates using configured grid-intensity data, an advisory retrieval step, and reporting artifacts. | [`carbon_finance.py`](./GREEN-COMPUTE-MRV/carbon_finance.py), [example output](./GREEN-COMPUTE-MRV/OUTPUT.pdf) |
| [MAGNETAR-SOVEREIGN](./MAGNETAR-SOVEREIGN/README.md) | Custom causal-transformer architecture and a local training/inference experiment. | [`model.py`](./MAGNETAR-SOVEREIGN/model.py), [`self_made_gpt_local.py`](./MAGNETAR-SOVEREIGN/self_made_gpt_local.py), [example output](./MAGNETAR-SOVEREIGN/output.png) |
| [MAGNETAR-WEBSTACK](./MAGNETAR-WEBSTACK/README.md) | Prompt-based static website generation, an optional client-side document-Q&A widget, ZIP packaging, and optional GCS upload. | [`websitecreation+rag+gcpdeployment.py`](./MAGNETAR-WEBSTACK/websitecreation%2Brag%2Bgcpdeployment.py), interface and deployment screenshots |
| [MCP-AGENTIC-CONTROL-LAB](./MCP-AGENTIC-CONTROL-LAB/README.md) | A comparison of UI/application-level role routing and a tool-bound authorization pattern in a policy RAG prototype. | [`mcp_inspired_rag.py`](./MCP-AGENTIC-CONTROL-LAB/mcp_inspired_rag.py), [`mcp_native.py`](./MCP-AGENTIC-CONTROL-LAB/mcp_native.py), test screenshots |
| [MISA — Social Intelligence](./MISA-Social-Intelligence/README.md) | Pairwise analytics over supplied Sprinklr-style JSON exports, transparent heuristic metrics, plots, and an LLM advisory. | [`misa.py`](./MISA-Social-Intelligence/misa.py), [example report](./MISA-Social-Intelligence/misa_output_tatavsmahindra.pdf) |
| [Local vs Managed Vector Store](./vectorstore-comparison/README.md) | An exploratory comparison of NumPy cosine retrieval and Pinecone on one policy document and a fixed question set. | [`custom_pinecone_comparison.py`](./vectorstore-comparison/custom_pinecone_comparison.py), metrics and latency plots |

## Engineering themes

### System design around model calls

The projects illustrate different ways to compose model calls with surrounding systems: multimodal input/output pipelines, locally defined transformer blocks, deterministic arithmetic, document parsing, vector retrieval, client-side static HTML, and UI/tool authorization boundaries.

### Retrieval and grounding

The lab includes document-grounded Q&A paths using embeddings and vector stores, plus an empirical local-versus-managed retrieval comparison. Grounding prompts and retrieval do not guarantee factual correctness; each project README describes the retrieval design and its limitations.

### Measurement and artifacts

Several projects create or include screenshots, reports, PDFs, ZIPs, or latency/quality plots. Artifacts demonstrate that a workflow has been exercised at least once; they are not, on their own, proof of general performance, compliance, security, or reproducibility.

### Resource and deployment trade-offs

Projects include a CPU/local training experiment, a PyTorch avatar path intended for a Colab-style environment, managed retrieval, browser-side RAG, and optional Google Cloud Storage deployment. Check the individual README before assuming a project is self-contained or locally runnable.

## Repository layout

```text
ai-systems-lab/
├── ELARA-Agentic-Avatar/
├── GREEN-COMPUTE-MRV/
├── MAGNETAR-SOVEREIGN/
├── MAGNETAR-WEBSTACK/
├── MCP-AGENTIC-CONTROL-LAB/
├── MISA-Social-Intelligence/
├── vectorstore-comparison/
├── README.md
└── LICENSE
```

Each project directory now has its own README and standalone MIT `LICENSE`. Project documentation embeds the same full license text and links to the adjacent file using a relative `./LICENSE` path. The root repository also has a root-level MIT license.

## Getting started

1. Choose a project from the table above.
2. Read its full README, including required secrets, data inputs, platform assumptions, and limitations.
3. Check the source file before running commands; several committed `.py` files are exports from Google Colab notebooks and include `!pip` syntax, `google.colab` imports, or `/content` paths. Those files need a Colab notebook workflow or adaptation before they can run as conventional local Python scripts.
4. Configure API keys and cloud credentials in a secure runtime. Some existing scripts contain empty key placeholders and may need their credential initialization updated. Never commit real keys.
5. Start with non-sensitive example inputs and verify outputs before considering real data or deployments.

There is no repository-wide `requirements.txt`, pinned dependency lock, automated test suite, CI validation, or common command that runs all seven projects as a single application in the current repository snapshot.

## Security, privacy, and scope

This repository is a portfolio of applied systems and experiments, not a set of production-hardened products. Hosted LLM, embedding, speech, vector-store, and cloud services may receive user content depending on the selected project. Some projects embed uploaded document chunks into generated static HTML; some use hard-coded demo identities for role demonstrations; and some calculate carbon or social-intelligence metrics using project-specific assumptions. Read the relevant project’s security and limitations sections before using real, confidential, regulated, legal, medical, financial, or operational data.

The projects are not certified as secure, legally compliant, carbon-accounting compliant, or suitable for high-stakes decisions. Any claims in example outputs should be independently reviewed and validated for the intended use.

## Author

**Shubham Kumar Singh**

[GitHub profile](https://github.com/shubham-61291) · [LinkedIn profile](https://linkedin.com/in/shubhamkumarsingh61291)

## License

The repository uses the MIT License for its authored code and documentation. The complete license is provided in [`LICENSE`](./LICENSE). Third-party code, models, documents, datasets, and services referenced by a project may be subject to their own licenses or terms, which are not replaced by this repository license.

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
