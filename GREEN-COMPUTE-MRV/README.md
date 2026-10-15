# GREEN-COMPUTE-MRV

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![UI](https://img.shields.io/badge/UI-Gradio-orange)](https://www.gradio.app/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

**A scenario-analysis prototype for comparing compute-related electricity emissions across configured domestic and international grid regions, with an LLM-generated advisory and downloadable reporting artifacts.**

## Overview

GREEN-COMPUTE-MRV explores a practical question: how could compute power assumptions, workload duration, grid carbon-intensity data, and data-location considerations be presented together for a sustainability decision? The checked-in `carbon_finance.py` combines a simple emissions calculation, calls the Electricity Maps carbon-intensity API, queries a preconfigured Pinecone index for advisory context, asks OpenAI to draft an executive brief, and creates a PDF plus JSON evidence-pack ZIPs.

Despite the project name and its reporting format, the implementation should be treated as a **decision-support prototype**, not a certified Monitoring, Reporting and Verification system or a legal compliance determination. The included PDFs are example artifacts, not independent verification of the underlying assumptions.

## What it does

- Accepts a selected home region, a workload duration (1–48 hours), and one of three assumed compute-power profiles.
- Queries the latest carbon intensity for a configured list of domestic and international Electricity Maps zones.
- Calculates baseline, domestic-option, and international-option emissions using the current power × duration × intensity formula.
- Selects the lowest intensity among the configured domestic zones and among the configured international zones.
- Uses a Pinecone vector query (`top_k=2`) for a fixed advisory query and asks `gpt-4o-mini` to write a strategic explanation when the required keys are present.
- Generates a strategic assessment PDF and separate JSON evidence-pack ZIPs for the domestic and international calculations.

## Calculation model and assumptions

The source defines these example power inputs:

| UI option | Assumed power |
|---|---:|
| Standard Instance | 2.0 kW |
| High-Performance | 6.0 kW |
| AI Training Cluster | 20.0 kW |

For a zone with grid intensity `g` in gCO₂/kWh, assumed power `p` in kW, and duration `h` in hours, the implemented estimate is:

`estimated emissions (gCO₂) = g × p × h`

For each run, the baseline uses the selected home region's intensity; the domestic option uses the lowest observed/fallback intensity in the fixed domestic list; and the international option uses the lowest intensity in the fixed international list. This is a simplified scenario calculation. It does not measure actual server utilization, PUE, networking, cooling, hardware lifecycle emissions, workload migration constraints, temporal workload profiles, or data-transfer energy.

### Live-data fallback behavior

If the Electricity Maps request fails or does not return a usable response, the function can return a fixed fallback intensity of **736 gCO₂/kWh**. The fallback keeps the UI calculation available but means the result may not represent current grid conditions. The result should always be interpreted alongside the source, timestamp, zone mapping, and fallback status; the current implementation does not expose a robust per-zone provenance ledger for every value.

## Architecture

```text
User-selected region + power profile + duration
                     |
                     v
        Electricity Maps API (zone list)
                     |
          live values or fallback value
                     |
                     v
          Fixed-formula scenario math
             /                 \
      Domestic option      International option
             \                 /
                     v
        Pinecone context retrieval (top 2)
                     |
                     v
          OpenAI advisory (gpt-4o-mini)
                     |
                     v
      PDF report + two JSON evidence ZIPs
```

## Technology

| Layer | Implementation |
|---|---|
| UI | Gradio |
| Numerical scenario model | Python arithmetic over configured kW, hours, and gCO₂/kWh values |
| Grid intensity | Electricity Maps `carbon-intensity/latest` endpoint |
| Advisory retrieval | SentenceTransformers `all-MiniLM-L6-v2` and a fixed Pinecone host/index |
| Advisory generation | OpenAI `gpt-4o-mini` |
| Output | ReportLab PDF and JSON evidence packs |

## Configuration and execution

The source includes empty string placeholders for `ELECTRICITY_MAPS_KEY`, `OPENAI_API_KEY`, and `PINECONE_API_KEY`. It also hardcodes a Pinecone host and assumes that the remote index already exists and contains suitable metadata. These are environment-specific prerequisites, not resources provisioned by this repository.

1. Use a compatible Python/Linux or Colab environment and install the dependencies used by the source (`gradio`, `openai`, `pinecone`, `reportlab`, `requests`, `pandas`, and `sentence-transformers`).
2. Configure valid provider credentials securely. The checked-in script currently assigns key placeholders into environment variables; replace that configuration with a secrets manager or environment-variable reads before routine use. Never commit actual keys.
3. Verify the Pinecone endpoint and index are yours, accessible, and populated with the context expected by the advisory query. Do not use an unknown shared index.
4. Confirm the output directory expected by the code (`/content`) exists or adapt the paths for your environment.
5. Run the application in a compatible environment and compare the calculated outputs with the source intensity values and assumptions before using them in a decision.

The source invokes package installation from inside the script and writes report outputs to `/content`; local Windows execution requires configuration and path adaptation. A clean, pinned, reproducible environment is not supplied in this repository snapshot.

## Included artifacts

- [OUTPUT.pdf](./OUTPUT.pdf) — existing example MRV output.
- [Strategic_Assessment_Report.pdf](./Strategic_Assessment_Report.pdf) — existing strategic assessment artifact.
- [RAG Knowledge Base for Green Compute MRV.docx](./RAG%20Knowledge%20Base%20for%20Green%20Compute%20MRV.docx) — knowledge-base document included with the project.
- [carbon_finance.py](./carbon_finance.py) — application source.

## Limitations and governance

- **Not certification:** The software does not implement a formally accredited verification procedure, independently verify source data, or certify alignment with GHG Protocol, ISO, CCTS, Verra, or Gold Standard requirements.
- **Simplified emissions:** The calculation is a power-and-time scenario multiplied by grid intensity; it is not a complete lifecycle or data-centre emissions model.
- **Fallback sensitivity:** Unavailable live intensity data can silently become the fixed fallback value for calculations, so stale/fallback data may influence ranking.
- **Region selection:** The “best” regions are chosen from fixed lists and mappings. The UI does not establish that moving a workload to those regions is operationally feasible, legally permitted, or lower-carbon across the full service lifecycle.
- **Advisory grounding:** The code uses a fixed retrieval query rather than constructing separate standard-specific evidence queries for each calculation. The resulting advisory can be incomplete or wrong.
- **Credentials and private data:** Provider keys and service endpoints must be controlled by the operator. Do not upload confidential workload or location data without reviewing provider and applicable organizational terms.
- **No independent benchmark:** The existence of the PDFs demonstrates artifact generation, not independent correctness or statutory reporting readiness.

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
