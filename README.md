# Karini Data Risk Intelligence Framework (KDRIF) 🛡️

[![CI/CD Pipeline](https://github.com/karini-ai/kdrif/actions/workflows/ci.yml/badge.svg)](https://github.com/karini-ai/kdrif/actions)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%20%7C%203.11%20%7C%203.12-blue.svg)](https://www.python.org/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-green.svg)](https://opensource.org/licenses/Apache-2.0)
[![FastAPI](https://img.shields.io/badge/API-FastAPI-009688.svg)](https://fastapi.tiangolo.com)
[![Streamlit](https://img.shields.io/badge/Dashboard-Streamlit-FF4B4B.svg)](https://streamlit.io)

**KDRIF** is an enterprise-grade, explainable, and predictive Python open-source framework for continuous data risk assessment, telemetry observation, lineage blast-radius quantification, and controlled incident containment.

---

## 🎯 Core Mission & 9-Stage Intelligence Lifecycle

KDRIF continuously monitors enterprise data estates across structured, semi-structured, and streaming environments:

```text
DISCOVER ──► CLASSIFY ──► OBSERVE ──► SCORE ──► EXPLAIN ──► PREDICT ──► PRIORITIZE ──► RESPOND ──► LEARN
```

1. **DISCOVER**: Auto-profiles structured and semi-structured assets (CSV, JSON, Parquet, SQLite, PostgreSQL).
2. **CLASSIFY**: Discovers PII, Financial (PCI/PAN/IBAN), Secrets (API tokens, private keys), PHI, and Employee data.
3. **OBSERVE**: Analyzes behavioral telemetry, user query patterns, and baseline statistical boundaries.
4. **SCORE**: Computes a deterministic, mathematically grounded **0–100 Data Risk Intelligence Score**.
5. **EXPLAIN**: Generates natural language root-cause breakdowns, waterfall factor contributions, and audit trails.
6. **PREDICT**: Forecasts 7-day and 30-day risk trajectories, drift momentum, and incident probabilities.
7. **PRIORITIZE**: Ranks incidents using multi-criteria optimization incorporating blast radius and business criticality.
8. **RESPOND**: Orchestrates controlled incident containment (`ALERT`, `INVESTIGATE`, `RESTRICT`, `QUARANTINE`, `NOTIFY`, `ESCALATE`) with **strict simulation-by-default guardrails**.
9. **LEARN**: Persists risk timeline memory, decisions, and outcomes to calibrate future baselines.

---

## 📐 The 8-Factor Data Risk Score

The central capability is a normalized, weighted risk evaluation formula:

$$\text{DataRiskScore} = \sum_{i=1}^{8} w_i \times S_i$$

Where $\sum w_i = 1.0$, spanning:
- **Data Sensitivity ($w=0.20$)**: Density and severity of discovered PII, PCI, Secrets, and PHI.
- **Data Exposure ($w=0.15$)**: Public accessibility, missing encryption at rest/transit, and overly permissive IAM policies.
- **Access Behavior ($w=0.15$)**: Deviation from baseline query frequency, volume, and off-hours access.
- **Business Criticality ($w=0.10$)**: Organizational tier and operational dependency rank.
- **Data Quality ($w=0.10$)**: Missing value surge, duplication rate, schema drift, and type corruption.
- **Anomaly Level ($w=0.15$)**: Multivariate Isolation Forest and Z-score statistical anomalies.
- **Historical Risk ($w=0.05$)**: Historical incident frequency and persistent risk inertia.
- **Dependency Impact ($w=0.10$)**: Downstream blast radius across ML models, pipelines, and consumer systems.

---

## ⚡ Quick Start

### 1. Installation

```bash
# Clone the repository
git clone https://github.com/karini-ai/kdrif.git
cd kdrif

# Install in editable mode with dependencies
pip install -e .
```

### 2. Python Library Usage

```python
import pandas as pd
from kdrif.config.settings import get_default_config
from kdrif.monitoring.monitor import KDRIFPipeline

# Initialize KDRIF Pipeline
pipeline = KDRIFPipeline(get_default_config())

# Profile DataFrame
df = pd.read_csv("enterprise_customers.csv")
profile = pipeline.scanner.profile_dataframe(df, "enterprise_customers.csv")
profile.business_criticality = 0.85

# Execute continuous assessment
results = pipeline.run_assessment(
    asset_profile=profile,
    df=df,
    permissions={"public_read": False, "encryption": "AES-256"},
    historical_scores=[20.0, 22.0, 28.0],
)

risk = results["risk_score"]
print(f"Risk Score: {risk.risk_score:.1f}/100 [{risk.risk_level.value}]")
print(f"Recommended Action: {risk.recommended_action}")
print(f"Explanation: {results['explanation'].detailed_narrative}")
```

### 3. Command-Line Interface (CLI)

```bash
# Display system info
kdrif version
kdrif status

# Scan & profile a directory of datasets
kdrif scan ./examples/data/

# Classify sensitive columns
kdrif classify ./examples/data/customers.csv

# Run full continuous risk assessment
kdrif assess ./examples/data/customers.csv --criticality 0.85

# Simulate one of the 7 realistic security scenarios
kdrif simulate 3
```

---

## 🧪 7 Pre-Configured Synthetic Security Scenarios

KDRIF includes realistic synthetic data and access telemetry simulators for testing security workflows without confidential enterprise data:

| ID | Scenario Title | Threat Vector | Primary Response Action |
|---|---|---|---|
| **1** | **Normal Enterprise Access** | Routine daytime queries by authorized analysts | `ALERT` (Nominal) |
| **2** | **Excessive Access & Bulk Exfiltration** | Sudden 450,000 PII export from unverified IP | `RESTRICT` & Throttle |
| **3** | **Sensitive Financial Exposure** | Unencrypted cardholder transactions open to public internet | `QUARANTINE` & Encrypt |
| **4** | **Privilege Escalation Alert** | Read-only intern token executing administrative payroll edits | `RESTRICT` & Revoke Token |
| **5** | **Abnormal Application Behavior** | Storefront service querying HR compensation at 3:30 AM via Tor | `INVESTIGATE` & Network Block |
| **6** | **Data Quality Degradation** | 40% missing data surge, duplicate keys, dropped KYC columns | `ALERT` & Pipeline Halt |
| **7** | **Multi-Vector Risk Storm** | Leaked API secrets + High Blast Radius + Public S3 Exposure | `QUARANTINE` & P1 Escalate |

---

## 🛡️ Safe-by-Default Automated Incident Response

Containment actions operate under strict safety guardrails:
- **Simulation-First Execution**: Potentially destructive operations (`RESTRICT`, `QUARANTINE`) execute in **SIMULATION mode by default**.
- **Human-in-the-Loop Workflow**: Live execution requires explicit operator review (`approve_plan`) before applying live firewall or IAM changes.
- **Audit Logging**: Every simulation and execution is signed with an immutable execution record stored in SQLite.

---

## 🌐 FastAPI REST Service

Launch the REST server:

```bash
uvicorn kdrif.api.app:app --host 0.0.0.0 --port 8000
```

Key Endpoints:
- `POST /scan`: Profile assets from a local or networked path.
- `POST /classify`: Detect sensitive entities across tabular columns.
- `POST /assess`: Full end-to-end risk intelligence evaluation.
- `GET /graph`: Lineage topology, critical hubs, and blast-radius scores.
- `POST /simulate/{id}`: Trigger synthetic scenario simulation.
- `POST /respond/approve`: Human-in-the-loop plan authorization.
- `GET /export-zip`: Download complete clean source code archive.

---

## 📊 Streamlit Interactive Dashboard

Launch the visual dashboard:

```bash
streamlit run dashboard/streamlit_app.py --server.port 8501
```

Features:
- **Executive Risk Radar**: Live enterprise health KPIs, active threat alerts, and 30-day forecast curves.
- **Scenario Simulator**: Interactive dropdown to run any of the 7 security scenarios with visual waterfall explanations.
- **Lineage Graph Explorer**: Interactive dependency graph visualizing risk propagation across data assets, features, and ML models.
- **Response Console**: Real-time review queue for pending containment approvals and simulation logs.

---

## 🐳 Docker & Container Deployment

Run with Docker Compose:

```bash
docker-compose up --build
```

- FastAPI Server: `http://localhost:8000/docs`
- Streamlit Dashboard: `http://localhost:8501`

---

## 🧪 Running the Test Suite

```bash
pytest --cov=kdrif tests/
```

All 31 unit, integration, and end-to-end tests validate deterministic scoring, explainability generation, isolation forest anomaly detection, and safety response guardrails.

---

## 📜 License

Licensed under the [Apache License, Version 2.0](LICENSE).
