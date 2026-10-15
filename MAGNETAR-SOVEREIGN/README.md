# MAGNETAR-SOVEREIGN

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Framework](https://img.shields.io/badge/Framework-PyTorch-ee4c2c?logo=pytorch&logoColor=white)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A small, custom causal-transformer language-model experiment with a local training pipeline and a command-line inference shell.**

## Overview

MAGNETAR-SOVEREIGN is an experiment in building and inspecting a GPT-style causal language model using PyTorch modules defined in the repository. The architecture implements token and positional embeddings, causal self-attention, MLP blocks, residual/normalization layers, and a tied language-model output head. The training script converts a structured JSON corpus into text sequences, uses a GPT-2 tokenizer with additional special tokens, trains a custom model, and packages the trained weights and tokenizer assets.

“Local” and “sovereign” describe the intended inference arrangement after required artifacts are present. They do not imply GPT-class performance, production readiness, a verified privacy certification, or the absence of dependencies during setup/training. The project does not provide a benchmark establishing parity with large hosted language models.

## Components

| File | Role |
|---|---|
| [`self_made_gpt_local.py`](./self_made_gpt_local.py) | Reads a JSON training corpus, builds training examples, runs a small configuration-search pass and a 100-epoch final training loop, then saves a package ZIP. |
| [`model.py`](./model.py) | Defines the custom PyTorch transformer and loads the saved configuration, tokenizer, and weights. |
| [`app.py`](./app.py) | Interactive terminal loop; uses greedy next-token selection for up to 40 generated tokens per input. |
| [`sony_json.json`](./sony_json.json) | Example JSON policy corpus included with the repository. |
| [`output.png`](./output.png) | Previously captured local inference example. |

## Architecture

```text
Structured JSON corpus
        |
        v
Data flattening / conversation and assessment examples
        |
        v
GPT-2 tokenizer + extra conversation tokens
        |
        v
Custom PyTorch causal transformer
        |
        v
Training + validation loop; final training
        |
        v
Weights + model config + tokenizer package
        |
        v
Local CLI inference (greedy token selection)
```

The code defines a custom transformer; it does not load an off-the-shelf pretrained GPT language model. The tokenizer is based on the Hugging Face GPT-2 tokenizer, extended during training. The training corpus and the quality and quantity of its examples therefore materially affect what the small model learns.

## Runtime requirements and setup

The repository listing currently contains `app.py`, `model.py`, `self_made_gpt_local.py`, `sony_json.json`, and a sample output image. It does **not** contain the inference artifacts that `model.py` opens by default: `magnetar_config.json`, `magnetar_weights.pth`, and the local `Magnetar_Tokenizer/` directory. Consequently, **`python app.py` will not work from a fresh clone until the trained artifact package has been generated and extracted into the expected working directory**.

### Train and create the model package

1. Create an environment with `torch`, `transformers`, `sentence-transformers`, and `numpy` installed. Training may require substantial time and memory; CUDA is used if PyTorch detects it.
2. Run `self_made_gpt_local.py` with an appropriate corpus. The current script first checks for `deepseek_json_20251217_51e8ca.json`, then `THE_QUANTUM_MBA_COMPLETE.json`; if unavailable, it attempts a Google Colab file upload. `sony_json.json` is present in the project but is not the default filename requested by the training code. Either provide the expected file name or adapt the input selection deliberately.
3. The training script writes a `Magnetar_Brain.zip` package. Its contents include `magnetar_weights.pth`, `magnetar_config.json`, `Magnetar_Tokenizer/`, plus a local vector-vault artifact and corresponding text JSON.
4. Extract the package contents into the same working directory from which `app.py` / `model.py` are run, so the relative paths in `load_model()` resolve.
5. Start `python app.py`, enter a prompt, and type `exit` to stop the loop.

The training file is notebook-export oriented because its missing-data path uses `google.colab.files.upload()`. A normal local run is possible only when the expected JSON file already exists (or the upload path is adapted). Model/tokenizer downloads may require network access during setup; after assets are present locally, `app.py` is designed to run inference locally.

## Model configuration in the current trainer

- Embedding width: 768.
- Context/block length: 1,024 tokens.
- Head count: 8.
- Tested depths: 4 and 6 transformer blocks.
- Batch size: 2.
- Final training loop: 100 epochs.
- Optimizer: AdamW; gradient clipping is used; mixed precision is enabled when CUDA is available.
- Generation in `app.py`: greedy argmax for 40 new tokens.

These settings describe the source code, not a claim about measured model quality or an optimized training recipe. The “HPO” pass is a very small exploratory comparison, capped to 10 training batches per candidate configuration.

## Limitations and safe use

- **Small experimental model:** Do not describe the model as equivalent to GPT-class hosted systems without evaluation evidence. It can repeat text, answer incorrectly, or fail to follow instructions.
- **Training input:** The code may serialize conversational and assessment content into training sequences. Ensure you have rights to use the corpus and exclude confidential, personal, or regulated data unless authorized.
- **Artifact requirement:** Model weights, configuration, and tokenizer artifacts are generated outputs and are absent from the current repository snapshot.
- **Resource needs:** The trainer uses 1,024-token sequences and a 100-epoch loop; execution cost varies widely by dataset and hardware.
- **No safety layer:** The CLI does not implement content moderation, retrieval citations, authentication, or an evaluation-based quality gate.
- **Reproducibility:** Seeds are set, but practical determinism can vary by framework, hardware, and numerical operations.
- **Embeddings are ancillary:** The trainer produces a BGE-based local vector vault, but the current `app.py` inference loop does not query that vault for RAG.

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
