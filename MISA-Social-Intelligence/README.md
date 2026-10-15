# MISA — Metric Intelligence & Signal Analysis

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Analytics](https://img.shields.io/badge/Approach-Metric--first-26734D)](https://pandas.pydata.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A pairwise social-listening analysis workbench that calculates brand metrics from Sprinklr-style JSON exports, visualizes comparative signals, and adds an OpenAI-generated advisory.**

## Overview

MISA is a data-processing and comparison prototype intended to make structured social-listening exports easier to inspect. The checked-in `misa.py` loads JSON arrays or JSON Lines, derives a brand label from `WORD_CLOUD` rows, reads aggregate sentiment and country/topic fields, calculates a small set of comparison metrics, creates four charts, and requests an LLM-generated strategic advisory.

The current workflow uses the **first two uploaded files only**. The included `misa_output_tatavsmahindra.pdf` is a pre-existing presentation artifact; the current Python source renders charts and text in Gradio but does not automatically build that PDF.

## What the current code computes

- **Brand detection:** sums `MENTIONS__SUM` over qualifying `WORD_CLOUD` tokens and chooses the most frequent tokens as brand labels.
- **Sentiment totals:** reads `POSITIVE_MENTIONS__SUM`, `NEGATIVE_MENTIONS__SUM`, and `NEUTRAL_MENTIONS__SUM` from a compatible aggregate row; if none is found, the current fallback initializes these totals to zero.
- **Narrative capture (FGC heuristic):** calculates the share of mentions in the word-cloud rows whose token contains the detected brand string, excluding tokens containing `/` or `&`.
- **Community agency (UGC):** reports `100 − FGC` based on that heuristic.
- **Crisis exposure (heuristic):** calculates negative mentions divided by positive plus negative mentions; neutral mentions are not included in this denominator.
- **Visualizations:** creates sentiment bars, each brand’s top word-cloud terms, and a combined top-country chart.
- **Advisory:** submits the two brands’ sentiment aggregate dictionaries to `gpt-4o-mini` using a strategic-consultant prompt.

These calculations are the definitions implemented in this source snapshot. In particular, “FGC,” “UGC,” and “Crisis Exposure” are project-defined proxies; they should not be presented as independently validated industry-standard metrics without further specification and validation.

## Pipeline

```text
Two JSON / JSONL files
          |
          v
Load rows + detect one brand from each file
          |
          v
Aggregate supplied sentiment / word-cloud / country fields
          |
          +---------------------+
          |                     |
          v                     v
Metric comparison + plots   OpenAI advisory call
          |                     |
          +----------+----------+
                     v
               Gradio results
```

## Input contract

Upload at least two files. The current parser accepts a JSON array or one JSON object per line (JSONL). For meaningful output, data should include fields used by the functions, such as:

- `WORD_CLOUD` and numeric `MENTIONS__SUM` for brand detection and term aggregation.
- A compatible aggregate row with `POSITIVE_MENTIONS__SUM`, `NEGATIVE_MENTIONS__SUM`, and `NEUTRAL_MENTIONS__SUM`.
- Optional `COUNTRY` with numeric `MENTIONS__SUM` for country charts.

Each file is treated as a single-brand dataset. Extra uploaded files beyond the first two are not processed by the current `run_pipeline()` implementation. Validate brand derivation and the meaning of each source field before comparing files; malformed schemas can produce zero-filled values or raise errors.

## Setup and execution

The code imports `pandas`, `matplotlib`, `gradio`, and `openai`. The source currently initializes the OpenAI client with `OpenAI(api_key="")`; it will not work until that placeholder is replaced by a secure credential-loading approach. For a conventional local setup, adapt the initialization to read `OPENAI_API_KEY` from the environment (for example, use `OpenAI()` with the environment variable configured) rather than putting a real key in the file.

```bash
pip install pandas matplotlib gradio openai
```

After correcting the key initialization without committing secrets, run:

```bash
python misa.py
```

Use the Gradio UI to upload two compatible exports, click **RUN**, and review the advisory, metric table, and plots. This code snapshot does not bundle raw Sprinklr JSON input files, so provide authorized exports yourself.

## Included artifact

- [misa_output_tatavsmahindra.pdf](./misa_output_tatavsmahindra.pdf) — prior comparative report artifact. It is not generated automatically by the current source file.
- [`misa.py`](./misa.py) — current application source.

## Interpretation, privacy, and limitations

- **Data scope:** results can represent only the supplied export, including its collection window, filters, tagging quality, and sampling choices. This code does not fetch social data itself.
- **Advisory is not offline:** core aggregations and plots run locally, but the strategic advisory calls the OpenAI API. The source therefore does not support a blanket “no data leaves the local system” claim.
- **Limited advisory input:** the LLM receives the per-brand aggregate sentiment dictionary, not the full raw dataset or every chart. It may add generalizations beyond the supplied metrics; review its recommendations.
- **Heuristic semantics:** word-token containment is a proxy for narrative/brand mention, not a validated attribution model. Negative/(positive + negative) is a ratio over the supplied aggregate labels, not a complete crisis-risk metric.
- **Schema-sensitive behavior:** missing sentiment aggregates default to zero. Zero can mean “not supplied” rather than “no sentiment”; distinguish those cases before interpretation.
- **Credential handling:** do not commit API keys or upload personally identifiable or confidential social-listening exports unless permitted by the data owner and applicable policies.
- **Not a business decision oracle:** no causal analysis, uncertainty intervals, independent validation, or ground-truth evaluation is implemented.

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
