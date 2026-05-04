# Repsol × Zunder — M&A Intelligence Agent
**Agentic Competitive Intelligence System**  
Corporate Intelligence Course · Universidad Francisco de Vitoria · 2025–2026 · Grupo 5

> **Strategic question:** Should Repsol acquire Zunder (Spain's leading independent HPC ultra-fast charging operator) before Q2 2026?

---

## Team

| Name | Role |
|------|------|
| Emma Alonso | OSINT & Data Collection |
| Kerit Arévalo | Agent Developer & Project Integration |
| Jaime Domingo | CI Lead & Strategic Analysis |
| Jorge Figueroa | Agent Developer |
| Eduardo López | Frontend & Deployment |

---

## Live Dashboard

**[https://eduloopezzz.github.io/repsol-ci-agent/](https://eduloopezzz.github.io/repsol-ci-agent/)**

The dashboard displays the agent's latest run: strategic verdict, 8 intelligence findings with confidence scores, AMC matrix, Shell Method scenarios, and Early Warning System indicators.

---

## AI Tools Used

| Tool | Used For |
|------|----------|
| **Google Gemini 2.5-flash** | Core reasoning model powering the ReAct agent loop and CI analysis |
| **Google AI Studio** | Obtaining the free Gemini API key |
| **Google Colab** | Runtime environment for executing the notebook |
| **Claude (Anthropic)** | Pair programming — helped draft and debug agent architecture, system prompt design |
| **ChatGPT** | Helped structure the CI report sections and format the ECOMO analysis |

All AI tool usage is documented and disclosed in individual reflections as required by the course AI policy.

---

## Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Google Colab                           │
│                                                          │
│   repsol_ci_agent_APIGEM.ipynb                          │
│                                                          │
│   ┌──────────────┐      ┌─────────────────────────┐    │
│   │  ReAct Agent │ ───▶ │   Gemini 2.5-flash       │    │
│   │  (Python)    │ ◀─── │ (Automatic Func. Call)   │    │
│   └──────┬───────┘      └─────────────────────────┘    │
│          │                                               │
│   ┌──────▼──────────────────────────────────────┐      │
│   │              Tool Registry (3 tools)          │      │
│   │  ┌───────────┐ ┌────────────┐ ┌───────────┐ │      │
│   │  │ web_fetch │ │ read_csv_  │ │fetch_all_ │ │      │
│   │  │ (9 URLs)  │ │electromaps │ │osint_srcs │ │      │
│   │  └───────────┘ └────────────┘ └───────────┘ │      │
│   └──────────────────────┬──────────────────────┘      │
│                           │                              │
│                  ┌────────▼────────┐                    │
│                  │  last_run.json  │  ← structured output│
│                  └────────┬────────┘                    │
└───────────────────────────┼──────────────────────────────┘
                            │  git push docs/
                            ▼
             ┌──────────────────────────┐
             │     GitHub Repository     │
             │  docs/last_run.json       │
             │  docs/index.html          │
             └─────────────┬────────────┘
                           │  GitHub Pages
                           ▼
             ┌──────────────────────────┐
             │    Browser Dashboard      │
             │  eduloopezzz.github.io/  │
             │  repsol-ci-agent/        │
             └──────────────────────────┘
```

**What runs where:**
- **Python (Colab):** Agent logic, tool calls, Gemini API, JSON generation
- **GitHub repo:** Stores code + pre-computed output (`last_run.json`)
- **Browser (GitHub Pages):** Fetches and renders `last_run.json` — no API calls from the browser

---

## Installation

### Requirements
- Python 3.10+
- Google Colab account (free) or local Jupyter environment
- Gemini API key (free from [Google AI Studio](https://aistudio.google.com))

### Dependencies
```bash
pip install google-generativeai>=0.8.0 google-genai pandas>=2.0.0 beautifulsoup4>=4.12.0 requests>=2.31.0
```

Or open the notebook directly in Google Colab — dependencies are installed in the first cell.

---

## How to Run

1. **Get a free API key** at [https://aistudio.google.com](https://aistudio.google.com) → "Get API key"

2. **Open the notebook** in Google Colab:
   - Upload `repsol_ci_agent_APIGEM.ipynb` to Colab, or open directly from GitHub

3. **Set the API key** in Colab Secrets (left sidebar → key icon):
   - Name: `GEMINI_API_KEY`
   - Value: your key from step 1

4. **Upload the HPC data CSV** when prompted:
   - File: `puntos_recarga_madrid_1.csv` (included in this repo)
   - The notebook will ask for it in cell 2

5. **Run all cells** (Runtime → Run all)
   - The agent will fetch OSINT sources, read the CSV, and produce a full CI analysis
   - Expected runtime: ~60–90 seconds

6. **Download** `output/last_run.json` from the Colab file browser

7. **Update the dashboard** (optional): replace `docs/last_run.json` in this repo with the new file and push to GitHub

---

## API Keys Required

| Key | Where to Get | Cost |
|-----|-------------|------|
| `GEMINI_API_KEY` | [aistudio.google.com](https://aistudio.google.com) → Get API key | Free tier available |

**Never commit API keys to the repository.** Use Colab Secrets or a `.env` file (add `.env` to `.gitignore`).

---

## Expected Output

The agent produces a `last_run.json` file with the following structure:

```json
{
  "verdict": "CONDITIONAL GO",
  "confidence_level": "MEDIUM",
  "summary": "...",
  "findings": [
    {
      "claim": "...",
      "confidence": "HIGH",
      "sources": ["url or file"],
      "human_review": false
    }
  ],
  "dashboard_metrics": {
    "amc_scores": [...],
    "scenarios": [...],
    "ews_alerts": [...],
    "financial_summary": {...}
  }
}
```

**How to interpret the dashboard:**
- **CONDITIONAL GO / GO / NO-GO** — the agent's strategic recommendation
- **HIGH / MEDIUM / LOW confidence** — based on source quality and triangulation (TN12 rubric)
- **Human review flag** — LOW confidence findings are automatically flagged for analyst review
- **AMC scores** — Awareness / Motivation / Capability ratings for each competitor (TN01)
- **Scenarios** — Shell Method 2×2 matrix with probability-weighted NPV (TN02)
- **EWS indicators** — Early Warning System KITs with thresholds (TN03)

---

## Known Limitations

| Limitation | Impact |
|-----------|--------|
| **Paywalled sources** | Crunchbase and LinkedIn return no content — findings from these sources are rated LOW confidence and flagged for human review |
| **Zunder financial data** | Official accounts (SABI) not available — revenue and EBITDA figures are estimates based on eInforma and press releases from Santander/EIB |
| **Static dashboard** | The GitHub Pages dashboard shows pre-computed results from the last run. It does not call the agent in real time |
| **CSV scope** | The HPC points CSV covers the Madrid region — national market share figures are extrapolated |
| **No persistent memory** | Each agent run is independent — the agent does not retain context from previous runs |
| **Rate limiting** | Some OSINT sources may throttle requests; the agent implements exponential backoff (up to 3 retries) |

---

## CI Frameworks Automated

The agent automates the following phases of the CI pathway:

| Phase | Framework | Automated |
|-------|-----------|-----------|
| 2. Data Collection | TN03 OSINT | ✅ Fetches 9 OSINT sources |
| 3. AMC Analysis | TN01 Chen & Miller | ✅ Scores competitors from fetched data |
| 4a. Scenario Planning | TN02 Shell Method | ✅ Generates 2×2 matrix with NPV |
| 6. Early Warning | TN03 Gilad | ✅ Monitors KIT indicators |
| 7. Recommendation | Integration | ✅ GO/NO-GO with confidence scoring |

Phases 1, 4b, 4c, and 5 were completed manually in the CI report.

---

*Universidad Francisco de Vitoria · Corporate Intelligence 2025–26 · TN10–TN12 · Powered by Google Gemini 2.5-flash*
