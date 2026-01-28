MAGNETAR-SOVEREIGN 🧠⚡

A Self-Built, Offline, Sovereign Language Intelligence System

📖 Overview

MAGNETAR-SOVEREIGN is a self-authored GPT-class language intelligence system built entirely from first principles using PyTorch.
It is not a wrapper, not API-dependent, and not fine-tuned from proprietary services.

The system is designed to train, reason, retrieve, and respond locally, preserving data sovereignty, execution transparency, and architectural control.
It demonstrates how modern language intelligence can be engineered as a standalone system, not a cloud dependency.

🛡️ Why Sovereign?

MAGNETAR-SOVEREIGN qualifies as sovereign AI due to the following non-negotiable properties:

✅ Fully Offline Execution

No external inference APIs

No cloud calls

No third-party runtime dependencies

All inference occurs locally on the host machine

✅ Self-Built Transformer Architecture

Custom GPT-style causal transformer

Hand-written attention, MLP, residual blocks

Explicit architectural configuration (layers, heads, embeddings)

No AutoModel, no black-box loaders

✅ Self-Owned Training Pipeline

Custom data ingestion engine

Deterministic seed control

Native PyTorch training loop

Gradient clipping, AMP, validation, HPO

No hosted training services

✅ Data Sovereignty

Trained on locally owned JSON corpora

No data leaves the system

No telemetry, logging, or vendor visibility

Suitable for policy, compliance, and internal documents

✅ Transparent Outputs

No reranking

No post-processing

No answer rewriting

Outputs reflect true model behavior under constraints

Sovereign ≠ bigger
Sovereign = owned, inspectable, controllable

🚀 Key Capabilities

✅ GPT-class causal language modeling
✅ Structured policy ingestion (documents, sections, subsections)
✅ Multi-turn conversation learning
✅ Assessment & Q&A understanding
✅ Local semantic memory vault (Sentence-Transformer based)
✅ Deterministic inference behavior
✅ Zero external dependency at runtime

🧠 System Architecture

Core Components

Magnetar-Sovereign Core

Causal Self-Attention Transformer

Residual + LayerNorm architecture

Quantum Data Engine

Policy-aware JSON ingestion

Multi-format semantic flattening

Global Vault

Dense embeddings (BGE-Large)

Local vector storage (.npy)

Inference Shell

CLI-based interaction

No UI abstraction or filtering

🛠 Tech Stack

Framework: PyTorch

Architecture: Custom GPT-style Transformer

Tokenizer: GPT-2 tokenizer (locally extended)

Embeddings: BAAI / BGE-Large

Training: Native PyTorch (AdamW, AMP, clipping)

Runtime: CPU-only (GPU optional)

📂 Project Structure
MAGNETAR-SOVEREIGN/
│── app.py                     # Local inference shell
│── model.py                   # Transformer architecture
│── self_made_gpt_local.py     # Training + data engine
│── sony_json.json             # Sample policy corpus
│── output.png                 # Real, unfiltered output
│── README.md                  # Documentation

⚠️ Model Limitations & Output Disclosure

This project intentionally does not mask constraints.

Small Dataset

Trained on a limited policy corpus

Some sections may dominate retrieval when semantically dense

CPU-Only Inference

No GPU acceleration

Conservative decoding to avoid latency spikes

Reduced effective context window

No Fine-Tuning or Reranking

No domain fine-tuning

No cross-encoder reranking

No intent routing

This is by design — to preserve raw model behavior visibility.

Real, Unfiltered Outputs

No manual correction

No summarization layer

No demo polishing

What you see is what the model actually produces.

▶️ Usage
python app.py


You will see:

🧠 Magnetar ready. Type 'exit' to quit.


Ask natural language questions directly against the trained corpus.

🖼 Demo Output

See output.png for unaltered, real system output generated during local inference.

🔮 Future Enhancements

🔹 GPU acceleration support
🔹 Cross-encoder reranking (optional)
🔹 Intent-aware routing
🔹 Extended memory hierarchies
🔹 Multi-agent orchestration
🔹 Formal evaluation benchmarks

👨‍💻 Author

Shubham Singh


📜 License

MIT License
