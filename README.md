# Revenue Analytics Tools

Self-serve tools for NB MRR forecasting and scenario planning.

**Live site:** https://intuit-rtuosto.github.io/public/

## Tools

| Tool | Description |
|---|---|
| [NB MRR Pacing Calculator](https://intuit-rtuosto.github.io/public/tools/nb-mrr-calculator.html) | Model low/high scenarios for new booking MRR — funnel inputs, monthly pacing, exit run-rate, workstream splits, recognized revenue |
| [Understanding NB MRR Pacing](https://intuit-rtuosto.github.io/public/tutorials/nb-mrr-tutorial.html) | Step-by-step guide: baseline, ramp model, 54% rule, scenario modes, recognized revenue |

## Running locally

```bash
cd tools && python3 -m http.server
```

Chart.js loads from CDN, so an HTTP server is required (opening via `file://` shows fallback text instead of charts). The tutorial has no external dependencies and opens directly.
