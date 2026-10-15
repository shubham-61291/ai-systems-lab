# MCP-AGENTIC-CONTROL-LAB

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![RAG](https://img.shields.io/badge/Pattern-RAG-5B6DAA)](https://en.wikipedia.org/wiki/Retrieval-augmented_generation)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A side-by-side experiment comparing application/UI-level role routing with a tool-bound authorization pattern for policy-document retrieval and updates.**

## Overview

MCP-AGENTIC-CONTROL-LAB explores where authorization checks should sit in a retrieval-augmented policy assistant. The directory contains two implementations of a similar workflow:

- **MCP-Style variant** — `mcp_inspired_rag.py`, with role-oriented UI flows and Pinecone-backed policy retrieval.
- **Tool-bound / “MCP-Native” variant** — `mcp_native.py`, with a local `MCPServer` class exposing retrieval and update operations and checking an email string before updates.

These are prototypes for discussing authority placement. **The checked-in code does not import or implement the official MCP SDK or MCP wire protocol.** “MCP-Native” is the project’s name for a tool-oriented control pattern, not a claim that it is a conformant MCP server.

## Comparison

| Dimension | MCP-Style (`mcp_inspired_rag.py`) | Tool-bound (`mcp_native.py`) |
|---|---|---|
| Main idea | UI/application routes the user to role-specific views | Application calls a local tool façade for retrieval and updates |
| Retrieval | SentenceTransformer embeddings + Pinecone | SentenceTransformer embeddings + Pinecone |
| Q&A model | OpenAI `gpt-4.1-mini` in the current source | OpenAI `gpt-4o-mini` in the current source |
| Update rule | UI is intended to expose updates to the admin view; the update handler itself only validates that text is non-empty | `update_policy_context` compares the submitted email with a hard-coded admin email before updating |
| What it demonstrates | UX-level authority separation | A central update function with an explicit authorization condition |
| Security conclusion | UI hiding alone is not a security boundary | Hard-coded, self-asserted email is not real authentication |

Both scripts use a Pinecone index named `mcp-rag-index` and 384-dimensional embeddings from `all-MiniLM-L6-v2`. The scripts can update the same index, so test data and updates can persist across runs and between variants unless the operator manages index contents separately.

## High-level flow

```text
Uploaded policy document
          |
          v
Chunking + sentence embeddings
          |
          v
       Pinecone
          |
       Query path
          |
          v
Retrieved chunks -> hosted OpenAI response

Update path:
MCP-Style: UI route -> application handler -> Pinecone upsert
Tool-bound: UI -> local tool façade -> email-string check -> Pinecone upsert
```

## Run prerequisites

Both source files are exported from Google Colab and contain `!pip` syntax and/or `google.colab.files` uploads. They should be run as notebook code in Colab or adapted before use as ordinary Python modules.

Prerequisites include:

- Python with `openai`, `sentence-transformers`, `pinecone`, `tiktoken`, `gradio`, `numpy`, `python-docx`, and `PyPDF2` installed.
- An OpenAI API key and a Pinecone API key.
- A Pinecone index named `mcp-rag-index`, configured for 384-dimensional vectors and cosine similarity if created by the script.
- A policy source document for initial ingestion. The tool-bound variant calls `google.colab.files.upload()` directly; the style variant has a local-path fallback configured to `/mnt/data/Sony Bangalore Office Policy Creation.docx` and otherwise includes upload logic.

In the committed versions, both API-key variables are empty strings. Configure credentials securely and never store real secrets in the source file or screenshots. The style variant’s local fallback path refers to a runtime file path, not a file guaranteed to exist in this repository directory.

## Demonstration assets

### MCP-Style variant

- [Main interface](./interface.png)
- [Admin interface](./admin_interface.png)
- [User interface](./user_interface.png)
- [Unknown email denied](./unknowemail_denied.png)
- [Admin update flow](./information_update_admin_acc.png)
- [User reads updated content](./after_update_retrieval_user_acc.png)
- [Missing information response](./non_available_info.png)

### Tool-bound variant

- [Tool-bound interface](./mcp_native_interface.png)
- [Answer example](./mcp_native_answer.png)
- [Admin update accepted](./mcp_native_admin_udate_accepted.png)
- [Retrieval after update](./mcp_native_answer_retrieved_afterupdate.png)
- [User update blocked](./mcp_native_user_update_blocked.png)
- [Authorization check example](./mcp_native_authorization_check_fail.png)
- [Missing-information example](./mcp_native_lackofinfo.png)

## Security analysis and limitations

- **The email field is not authentication.** Users can type a known email into the UI; neither script verifies identity through an identity provider or signed session.
- **The MCP-Style UI is not a backend access-control boundary.** Its admin update handler checks for empty text but does not itself authenticate the caller. Hiding the admin UI does not prove the update endpoint is protected.
- **Tool-bound authorization is a useful architectural illustration but still weak.** It compares a submitted email string with a constant; it is not a production authorization system.
- **No production deployment controls:** no backend identity provider, secret rotation, audit trail with tamper protection, tenant isolation, policy rollback, or threat-model-validated security boundary is implemented.
- **Index state persists:** updates are upserts into a remote Pinecone index; old chunks are not automatically removed when policy text changes. Stale or duplicate content can affect later answers.
- **LLM output:** responses may be wrong or incomplete even when retrieval provides context. The current code uses nonzero temperature for the tool-bound variant.
- **External services:** uploaded document chunks and questions are sent to Pinecone/OpenAI. Use only data approved for those services.

Use this repository for controlled testing and architectural comparison, not to enforce access to real corporate policies or confidential information.

## Project files

```text
MCP-AGENTIC-CONTROL-LAB/
├── mcp_inspired_rag.py
├── mcp_native.py
├── interface and test screenshots (*.png)
├── README.md
└── LICENSE
```

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
