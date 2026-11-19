# ClearMind Behavioral Health — Big Data Pipeline

An end-to-end big data pipeline for a fictional 15-provider outpatient behavioral health clinic. The clinic has a 20% appointment no-show rate and no way to catch patients in crisis in real time. This project tackles both problems with batch analytics, streaming, and machine learning on Databricks and AWS.

Built as the final project for ISM 6362 — Big Data and Cloud-Based Tools (MS Data Analytics, Seattle Pacific University, Spring 2026).

## What it does

**1. Batch analytics — 3 clinical KPIs**
- Monthly no-show rate by diagnosis category
- Average PHQ-9 score at no-show vs. attended appointments
- Provider utilization rate by therapy type

KPIs are computed in PySpark and saved to S3 as Parquet for the dashboard.

**2. Streaming — real-time crisis alerts**
- Patient mood check-in events stream in through Amazon Kinesis
- Spark Structured Streaming (with windowing and watermarks) fires an alert when `mood_rating < 4` or `crisis_flag = True`
- Alerts land in S3 for the provider dashboard

**3. Machine learning — no-show prediction**
- Random Forest classifier (PySpark MLlib) predicts whether a patient will miss an appointment
- Appointments with P(no-show) > 0.65 are flagged for proactive outreach
- **Results:** AUC-ROC 0.73, accuracy 79.8%

**4. Dashboard**
- Power BI dashboard with the three KPIs, crisis alerts, and flagged appointments

## Architecture

```
Kaggle CSV + Synthetic EHR ──► S3 (raw) ──► Databricks batch ──► S3 (KPIs) ──► Random Forest ──► S3 (flags) ──► Power BI
Patient mood check-ins ──► Kinesis ──► Spark Structured Streaming ──► S3 (crisis alerts) ──────────────────────► Power BI
```

Full diagram: `ClearMind_Architecture.drawio` (open at [app.diagrams.net](https://app.diagrams.net)).

## Data

- **Medical Appointment No Shows** — 110,527 public appointment records from [Kaggle](https://www.kaggle.com/datasets/joniarroba/noshowappointments)
- **Synthetic EHR layer** — diagnosis, PHQ-9 score, and therapy type generated with Python Faker + NumPy and joined on PatientId. The Kaggle data has no mental health fields, so this layer simulates what a real EHR would hold. No real patient data is used.

## Tech stack

Databricks · Apache Spark / PySpark · Spark Structured Streaming · PySpark MLlib · scikit-learn · AWS S3 · Amazon Kinesis · Parquet · Power BI · Python (pandas, NumPy, Faker, matplotlib, seaborn)

## Files

| File | What it is |
|---|---|
| `ClearMind.ipynb` | Databricks notebook with the full pipeline |
| `ClearMind_Dashboard.pbix` | Power BI dashboard |
| `ClearMind_Architecture.drawio` | Architecture diagram |
| `ClearMind_FinalReport.pdf` | Final project report |

## How to run

1. Download the Kaggle dataset and upload it to your own S3 bucket.
2. Import `ClearMind.ipynb` into Databricks and update `DataPath` to point to your bucket.
3. Run the notebook top to bottom.
4. Open `ClearMind_Dashboard.pbix` in Power BI Desktop and point it at the output Parquet files.

## Author

**Lekhya Chalasani** — [LinkedIn](https://www.linkedin.com/in/lekhyachalasani)
