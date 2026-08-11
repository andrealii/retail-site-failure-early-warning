# Retail Site Failure Early-Warning System

> Automated anomaly detection and root-cause diagnostics for e-commerce 
> conversion failures — reducing mean time to diagnosis from hours to minutes.

## Business Problem
When a retail site's conversion rate drops unexpectedly, revenue loss compounds 
every minute the cause goes unidentified. Standard monitoring tools flag *that* 
something broke — but not *which* user segment, device type, geography, or funnel 
stage is responsible. This project builds an early-warning system that detects 
anomalies automatically and decomposes the root cause across four dimensions 
simultaneously, generating a structured diagnostic report without human intervention.

## Key Findings
- **3 structural breaks** detected in the Nov 2020 – Jan 2021 conversion rate 
  time series using CUSUM changepoint detection
- **1 critical anomaly day** identified — conversion rate dropped to 0% vs 
  1.15% baseline, estimated revenue impact quantified automatically
- **27 days flagged** across severity tiers: 1 critical, 5 warning, 21 watch
- Root-cause decomposition pinpoints the highest-contributing segment per 
  dimension within seconds of anomaly detection

## Business Recommendation
Implement this pipeline on a daily schedule. When anomaly score exceeds the 
0.584 threshold, the diagnostic report generates automatically and routes to 
the retail operations team — eliminating the manual triage process that 
currently delays response by 2–4 hours on average.

## Approach
| Phase | What was done |
|-------|--------------|
| 1. Data pipeline | Pulled 500k GA4 events from BigQuery; engineered 222k session-level features across 6 funnel stages |
| 2. Anomaly detection | Isolation Forest (contamination=0.05) scores each day; CUSUM Pelt algorithm identifies structural breakpoints |
| 3. Root-cause engine | Segment contribution decomposition across device, geography, traffic source, and medium |
| 4. Output layer | Automated HTML diagnostic report generated on anomaly trigger |

## Model Decisions
**Why Isolation Forest over One-Class SVM:**
Outputs a continuous anomaly score (not just binary) — enabling severity 
ranking (normal / watch / warning / critical) that a retail ops team can 
act on proportionally. Contamination parameter maps directly to the expected 
anomaly rate, which is a business assumption, not a technical one.

**Why CUSUM + Isolation Forest together:**
Isolation Forest answers *is this day abnormal?*  
CUSUM answers *exactly when did the structural break begin?*  
Together they provide the complete diagnostic: yes there's an anomaly, 
and it started at this precise timestamp.

## Technical Stack
| Tool | Purpose |
|------|---------|
| `scikit-learn` IsolationForest | Unsupervised anomaly scoring |
| `ruptures` Pelt + RBF | CUSUM changepoint detection |
| `pandas` | Session-level feature engineering |
| `plotly` | Interactive diagnostic visualizations |
| `google-cloud-bigquery` | GA4 public dataset ingestion |
| `Jinja2` / HTML | Automated diagnostic report generation |

## Repository Structure

```
retail-site-failure-early-warning/
├── data/
│   └── raw/
│       ├── ga4_sessions.csv          # Full 222k session feature table
│       ├── ga4_sessions_sample.csv   # Stratified 5,500-row GitHub sample
│       └── ga4_daily_metrics.csv     # Daily conversion rate time series
├── notebooks/
│   ├── 01_data_pipeline.ipynb        # BigQuery ingestion + feature engineering
│   ├── 02_anomaly_detection.ipynb    # Isolation Forest + CUSUM model
│   └── 03_root_cause_decomposition.ipynb  # Segment analysis + diagnostic report
├── outputs/
│   ├── anomaly_detection_chart.html  # Interactive anomaly visualization
│   ├── anomaly_scores.csv            # All days scored with severity labels
│   ├── root_cause_chart.html         # 4-panel decomposition chart
│   ├── diagnostic_report.html        # Automated client-facing report
│   └── conversion_rate_baseline.html # Baseline time series chart
├── requirements.txt                  # Reproducible environment
└── README.md
```

## Data Source
Google Merchandise Store GA4 Public Dataset — BigQuery  
Real e-commerce event logs: device, geography, session, funnel, conversion  
Date range: Nov 2020 – Jan 2021 · 500k events · 222k sessions

**Note on data files:** `ga4_sessions_sample.csv` (5,500 rows) is a 
stratified sample preserving the 1.15% conversion rate of the full 
221k-session dataset. Full data reproducible by running 
`01_data_pipeline.ipynb` against the BigQuery public dataset.

## What I Would Do With More Time / Data
- Connect to a live BigQuery stream for real-time anomaly triggering
- Add Slack or email alerting when anomaly score exceeds threshold
- Expand causal impact model to estimate revenue loss per minute of downtime
- Train on labelled historical incidents to move from unsupervised to 
  supervised detection — improving precision on the warning/critical boundary
- Add funnel stage as a decomposition dimension for deeper diagnostic depth

## Reproducing This Project
```bash
pip install -r requirements.txt
# Set up Google Cloud credentials (see notebook 01 for instructions)
# Run notebooks in order: 01 → 02 → 03
```
