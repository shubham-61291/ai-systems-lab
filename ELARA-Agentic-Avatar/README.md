# ELARA — Agentic Multimodal Avatar

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Interface](https://img.shields.io/badge/UI-Gradio-orange)](https://www.gradio.app/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A resource-aware voice/text assistant that connects speech recognition, hosted language-model responses, speech synthesis, and talking-head animation in a single Gradio interface.**

## Overview

ELARA demonstrates an end-to-end multimodal pipeline rather than a standalone chat interface. The checked-in `elara.py` is a Google Colab-oriented source export that orchestrates Google Cloud Speech-to-Text, an OpenAI response (with a fallback model path), Google Cloud Text-to-Speech, and the PyTorch inference path from the external [Ditto talking-head project](https://github.com/antgroup/ditto-talkinghead). It includes a separate audio-output path when animation does not complete successfully.

The application is an engineering demonstration. It is not a general-purpose autonomous agent, a real-time streaming avatar, or a production deployment package.

## Capabilities

- Accept a typed message or a microphone recording through Gradio.
- Convert speech to text using Google Cloud Speech-to-Text.
- Generate conversational responses using `gpt-5-nano`, with a fallback to `gpt-4o-mini` when the first request fails.
- Synthesize speech with the Google Cloud `en-US-Wavenet-F` voice.
- Invoke the external Ditto PyTorch animation pipeline with an avatar image and generated audio.
- Display animated video or audio-only output and conversation history.
- Validate expected configuration/checkpoint assets during setup.

## Processing flow

```text
Typed text or microphone audio
              |
              v
      Speech-to-text (audio path)
              |
              v
   OpenAI response + conversation history
              |
              v
  Google Cloud Text-to-Speech (WaveNet)
              |
              v
External Ditto talking-head inference
              |
        +-----+------+
        |            |
   Video output   Audio fallback
```

Typed input skips speech recognition; the audio path uses FFmpeg to normalize audio before the speech request. The avatar image is resized to 512 × 512 by the current handler when an image is supplied.

## Technology and external dependencies

| Area | Implementation in the checked-in code |
|---|---|
| UI | Gradio |
| Response generation | OpenAI API; `gpt-5-nano`, fallback `gpt-4o-mini` |
| Speech recognition | Google Cloud Speech-to-Text |
| Speech generation | Google Cloud Text-to-Speech, WaveNet voice |
| Animation | External Ditto talking-head repository, PyTorch inference path |
| Media / checkpoint setup | FFmpeg, Git, Git LFS, PyTorch, ONNX Runtime and auxiliary checkpoints |

## Run and configure

The source is an exported Colab workflow, not a portable, self-contained local application. It uses `/content` paths, invokes `apt-get`, clones an external repository and model checkpoints, and expects a Google Cloud service-account JSON file. The repository contains `elara.py` and demo media, but it does not vendor the Ditto repository or its model checkpoints.

1. Open the source in a compatible Linux/Google Colab environment and follow the setup instructions and validation steps near the top of `elara.py` in sequence. The source notes that the runtime must be restarted after setup before the app-launch section is run.
2. Provide an OpenAI API key in the password field when using the assistant.
3. Provide a Google Cloud service-account JSON file with the required Speech-to-Text and Text-to-Speech permissions. Enable the relevant Google Cloud APIs and billing as required by your Google Cloud account.
4. Upload an avatar image or use the demo’s default image behavior, then select typed or voice input.
5. Review status messages. Animation depends on compatible external checkpoints, FFmpeg, PyTorch/ONNX dependencies, and available compute.

Running `python elara.py` in an arbitrary desktop environment is **not guaranteed to work unchanged** because of the Colab-oriented paths, system setup assumptions, and external model assets.

## Demonstrations

The following media files are committed in this folder:

- **Interface:** [interface_modular.png](./interface_modular.png)
- **Welcome screen:** [welcome_note_1.png](./welcome_note_1.png)
- **Welcome animation:** [welcome_note_animation.mp4](./welcome_note_animation.mp4)
- **Black-hole explanation image:** [blackhole_explaination.png](./blackhole_explaination.png)
- **Black-hole animation:** [blackhole_animation.mp4](./blackhole_animation.mp4)

These assets illustrate previously captured behavior; they are not a guarantee that a fresh environment will reproduce the same output or latency.

## Limitations and safety notes

- **Hosted processing:** Audio transcripts and prompts go to the configured Google/OpenAI services; review their terms and data handling before submitting sensitive information.
- **Credentials:** The app accepts a Google Cloud credential file and copies it to a path under `/content`. Never commit a real service-account key or upload it to an untrusted runtime.
- **External model assets:** Ditto source/checkpoints are fetched from external locations and are not bundled in this repository. Version drift or upstream changes may affect reproducibility.
- **Runtime and resources:** PyTorch animation can be slow and can fail due to incompatible libraries, missing checkpoints, or insufficient memory. The interface has an audio path, but it does not eliminate every possible failure.
- **Conversation behavior:** A hosted model generates the text; output is not guaranteed factual, deterministic, or suitable for high-stakes decisions.
- **Not production-hardened:** This repository does not implement production authentication, session isolation, enterprise secrets management, or an operational monitoring system.

## Project files

```text
ELARA-Agentic-Avatar/
├── elara.py
├── interface_modular.png
├── welcome_note_1.png
├── welcome_note_animation.mp4
├── blackhole_explaination.png
├── blackhole_animation.mp4
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
