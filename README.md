# retail-site-failure-early-warning
Anomaly detection and automated root-cause diagnostics for e-commerce site failures — applied to real GA4 event log data
# Retail Site Failure Early-Warning System

> Automated anomaly detection and root-cause diagnostics for e-commerce 
> conversion failures — reducing mean time to diagnosis from hours to minutes.

## Business Problem
When a retail site's conversion rate drops unexpectedly, revenue loss compounds 
every minute the cause goes unidentified. Standard monitoring tools flag *that* 
something broke — but not *which* user segment, device type, geography, or funnel 
stage is responsible. This project builds an early-warning system that detects 
anomalies in real time and automatically decomposes the root cause across five 
dimensions simultaneously.

## Key Finding
*(To be completed after analysis)* — Placeholder: anomaly detected on [date], 
traced to [segment] accounting for [X]% of the conversion drop within 4 minutes 
of occurrence.

## Approach
1. **Data pipeline** — Ingest GA4 e-commerce event logs; build session-level 
   feature table by device, geography, SKU category, and funnel stage
2. **Anomaly detection** — Isolation Forest for multivariate anomaly scoring; 
   CUSUM for changepoint timestamp identification
3. **Root-cause engine** — Automated segment decomposition ranking contributing 
   factors by magnitude
4. **Output layer** — Structured diagnostic report generated automatically on 
   anomaly trigger

## Technical Stack
| Tool | Purpose |
|------|---------|
| `scikit-learn` IsolationForest | Unsupervised anomaly detection |
| `ruptures` | CUSUM changepoint detection |
| `pandas` | Session-level feature engineering |
| `plotly` | Interactive diagnostic visualizations |
| `statsmodels` | Causal impact estimation |
| `Jinja2` | Automated PDF report generation |

## Data Source
Google Merchandise Store GA4 Public Dataset — hosted on BigQuery free tier.  
Real e-commerce event logs with device, geography, session, and conversion fields.
**Note on data files:** `ga4_sessions_sample.csv` (5,500 rows) is a stratified 
sample preserving the 1.15% conversion rate of the full 221,838-session dataset. 
Full data reproducible by running `notebooks/01_data_pipeline.ipynb` against the 
BigQuery public dataset linked above.

## Repository Structure
