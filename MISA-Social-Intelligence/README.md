MISA – Social Intelligence Advisor 📊🧠

A Metric-First, Data-Grounded Social & Competitive Intelligence System

📖 Overview

MISA (Magnetar Social Intelligence Advisor) is a self-built, analyst-grade social intelligence system designed to convert raw enterprise social listening data into strategic, executive-ready insights.

This project is not a chatbot, not a dashboard toy, and not a sentiment wrapper.

MISA ingests Sprinklr-exported JSON data, performs deterministic metric computation, and produces structured competitive audits, keyword strategy diagnostics, and dashboards, culminating in a PDF-based strategic output suitable for consulting decks, CXO reviews, and academic evaluation.

The showcased run performs a comparative social intelligence audit between Tata and Mahindra, using real social listening data.

🧠 What Makes MISA Different

MISA is metric-first, not language-first.

LLMs are used only after metrics are computed

No hallucinated insights

No narrative-before-numbers

No black-box “sentiment scores”

Every conclusion in the final PDF is grounded in computed aggregates derived from Sprinklr JSON exports.

📊 Data Source (Explicit Disclosure)
Primary Data Source: Sprinklr

Input data is derived from Sprinklr social listening exports

Format: JSON / JSONL

Typical fields used:

MENTIONS__SUM

POSITIVE_MENTIONS__SUM

NEGATIVE_MENTIONS__SUM

NEUTRAL_MENTIONS__SUM

WORD_CLOUD

COUNTRY

🔒 No scraping, no synthetic data, no prompt-generated numbers

All analytics originate from enterprise-grade Sprinklr data.

🛡️ Why This Is Real Social Intelligence (Not a Demo)
✅ Deterministic Metric Computation

Sentiment ratios are computed directly from mention aggregates

FGC vs UGC is calculated using token ownership logic, not model guesses

Crisis exposure is derived from negative-to-total sentiment ratios

✅ Pairwise Competitive Mode

One brand per JSON file

Automatic brand detection from word-cloud dominance

Forced apples-to-apples comparison

✅ Audit-Grade Output

Outputs are rendered into a PDF, not just UI text

Designed for:

Consulting reviews

Strategy classrooms

Internal leadership discussions

🚀 Core Capabilities

Comparative Strategic Audit

Social Strategy vs Digital Strategy

FGC vs UGC dominance

Crisis maturity assessment

Executive action recommendations

Keyword Strategy Diagnostics

Brand-owned vs community-driven narratives

Top word-cloud tokens per brand

Dashboard Visualizations

Sentiment distribution (Positive / Negative / Neutral)

Keyword dominance plots

Country-level mention distribution

PDF-Based Final Output

All results consolidated into a multi-page PDF

No manual editing or post-processing

🧠 System Architecture
Pipeline Overview
Sprinklr JSON
     ↓
Metric Aggregation Engine
     ↓
FGC / UGC & Sentiment Analytics
     ↓
Strategic Contrast Logic
     ↓
LLM (Advisory Layer Only)
     ↓
Plots + Tables
     ↓
PDF Output

Key Architectural Principle

Language explains the numbers — it does not create them.

🛠 Tech Stack

Language: Python

Data Processing: Pandas

Visualization: Matplotlib

UI Layer: Gradio

LLM Usage: OpenAI (Advisory-only, post-metrics)

Data Source: Sprinklr JSON exports

Output Format: PDF (multi-page)

📂 Project Structure
MISA-Social-Intelligence/
│── misa.py                         # Core social intelligence engine
│── misa_output_tatavsmahindra.pdf  # Final strategic audit output (PDF)
│── README.md                       # Documentation

📄 Output Artifact (Important)
misa_output_tatavsmahindra.pdf

This PDF contains:

Comparative Strategic Audit

Tata vs Mahindra across:

Social vs Digital Strategy

FGC vs UGC balance

Crisis exposure

Executive actions

Keyword Strategy Table

Narrative capture percentages

Community agency metrics

Dashboards

Sentiment bar charts

Word cloud dominance plots

Country-level distribution

📌 The PDF is the authoritative output of the system.
The UI is only a generation interface.

⚠️ System Constraints & Transparency
Dataset Scope

Analysis reflects only the provided Sprinklr exports

Results are bounded by the listening window and query scope used in Sprinklr

No Data Augmentation

No enrichment

No synthetic expansion

No external crawling

No Metric Reranking

No smoothing

No normalization beyond explicit ratios

Raw counts are preserved

This ensures auditability and reproducibility.

▶️ Usage
python misa.py


Steps:

Upload two Sprinklr JSON files (one per brand)

Click RUN

Review:

Strategic audit

Keyword strategy

Dashboards

Export / review the generated PDF output

🔮 Future Enhancements

Multi-brand portfolio analysis

Time-sliced trend analysis

Cross-market comparison

Automated board-ready slide generation

Integration with governance workflows

👨‍💻 Author

Shubham Singh


📜 License

MIT License
