# MAGNETAR-WEBSTACK

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![UI](https://img.shields.io/badge/UI-Gradio-orange)](https://www.gradio.app/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A website-generation workbench that creates a static HTML page from a prompt, can embed an optional document-grounded Q&A widget, and can upload the result to a preconfigured Google Cloud Storage bucket.**

## Overview

MAGNETAR-WEBSTACK combines three distinct steps: server-side HTML generation using OpenAI, optional document parsing and chunking for an in-page retrieval assistant, and optional deployment of the generated `index.html` to a Google Cloud Storage bucket. The UI can preview the generated page and package it into a ZIP.

The checked-in `websitecreation+rag+gcpdeployment.py` is a Colab-exported script and includes notebook shell syntax (`!pip`). It is not directly executable as a normal Python file without adapting the install cells and Colab-specific runtime assumptions.

## Capabilities

- Generate a single-page static website from a natural-language prompt using `gpt-5-nano`.
- Add an optional RAG widget to the generated HTML.
- Read supported uploaded text, Markdown, HTML, PDF, and DOCX files; split extracted text into overlapping word chunks (up to 120 chunks in the current function).
- Compute document/query embeddings in the browser using OpenAI's `text-embedding-3-small` API and rank chunks using cosine similarity.
- Let the user enter an OpenAI key when invoking the embedded bot; browser-side requests call OpenAI endpoints directly.
- Preview the page in the UI, download a ZIP containing `index.html`, or upload `index.html` to an existing GCS bucket when deployment is selected.

## Architecture

```text
Natural-language site request
            |
            v
OpenAI website generation (gpt-5-nano)
            |
            v
Generated HTML + Magnetar branding
            |
      +-----+------+
      |            |
   no RAG       RAG enabled
      |            |
      |     Parse uploaded docs -> chunks
      |            |
      |     Embed/query in browser via OpenAI API
      +-----+------+
            |
            v
 Save index.html + ZIP
            |
    Optional GCS bucket upload
```

The RAG widget performs vector matching in the generated page; it does not use Pinecone or another managed vector database. The document chunks are embedded into the generated HTML as text so that the static page can build vectors in the browser at first use.

## Technology

| Layer | Implementation |
|---|---|
| UI | Gradio |
| Website generation | OpenAI API using `gpt-5-nano` |
| Document extraction | `pypdf`, `python-docx`, standard text handling |
| Chunking / matching | Word-based overlapping chunks and JavaScript cosine similarity |
| Browser embeddings and Q&A | OpenAI API; `text-embedding-3-small` and configured chat model |
| Packaging | ZIP with `index.html` |
| Optional hosting | Google Cloud Storage Python client and service-account JSON |

## Run and configure

1. Open the exported source in Google Colab (or adapt it into a proper Python module by removing the `!pip` line and ensuring dependencies are installed separately).
2. Install the listed packages: `gradio`, `openai`, `sentence-transformers`, `numpy`, `google-cloud-storage`, `pypdf`, and `python-docx`.
3. Configure the source's `OPENAI_API_KEY` setting securely. It is an empty string in the committed source; the current server-side website-generation call will not work until valid credentials are supplied. Do not commit an API key.
4. Launch the Gradio app and enter the site request. Upload supported documents only when enabling RAG.
5. For deployment, select an existing GCS bucket and provide a service-account JSON file with the required write permissions. The script uploads `index.html`; it does not create or configure the bucket's access policy.
6. Test both the downloaded ZIP and any cloud URL before sharing.

## Demonstration assets

- [Main interface](./Interface.jpeg)
- [Full interface](./full_interface.jpeg)
- [Full website](./full_website.jpeg)
- [RAG enabled](./interface_withrag_selected.jpeg)
- [API key prompt](./RAG_api_keypopup.jpeg)
- [Example RAG output](./rag_output.jpeg)
- [RAG verification example](./ragoutput_verification.jpeg)
- [Cloud bucket deployment](./cloud_bucket_deployed.jpeg)
- [Cloud RAG verification](./cloud_rag_verification.jpeg)
- [Website deployed on cloud](./website_cloud.jpeg)

## Important security and privacy properties

- **Uploaded document content is included in the generated static HTML** when RAG is enabled. Anyone who can access that HTML/ZIP or publicly hosted page can inspect the embedded document chunks. Do not use confidential policies, personal records, or other restricted documents in a page that may be shared or publicly hosted.
- The RAG user's OpenAI key is used by JavaScript in the browser and is therefore visible to the user who enters it and to browser tooling. This design is appropriate only for a user-supplied demonstration key, not a server-owned secret or shared enterprise deployment.
- The uploaded Google Cloud service-account JSON is sensitive. Keep it out of source control and use a minimally privileged service account.
- The implementation assumes the destination bucket's permissions are already set correctly. It does not configure access controls and should not be used to publish private content unintentionally.
- Client-side calls require network connectivity and valid OpenAI credentials; the static page is not an offline RAG deployment.

## Limitations

- Generated HTML is model-produced and should be reviewed for correctness, accessibility, security, and responsive behavior before publication.
- RAG is simple semantic retrieval over a limited chunk list; the UI does not produce robust source citations, per-claim provenance, or an independently verified answer.
- An LLM can still make unsupported statements despite context-focused instructions.
- The code does not implement server-side identity/access control, private document storage, rate limiting, usage accounting, or secure multi-user API-key management.
- The checked-in source uses notebook shell syntax and `/content` output paths; adaptation is required for a conventional local deployment.

## Project files

The application source is [`websitecreation+rag+gcpdeployment.py`](./websitecreation%2Brag%2Bgcpdeployment.py). Existing generated examples and screenshots remain in this directory; the source generates its ZIP at runtime.

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
