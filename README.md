# CloudGuard ML: Real-Time Threat Detection for AWS

CloudGuard ML is a big‑data security analytics pipeline that detects malicious activity in AWS CloudTrail logs using distributed machine learning and threat intelligence. It combines unsupervised anomaly detection with supervised attack classification to surface high‑value, MITRE ATT&CK‑mapped alerts in near real time.

## Key Features

- **End‑to‑end Spark pipeline**: Ingestion, cleaning, feature engineering, clustering, anomaly scoring, and reporting over millions of CloudTrail events.
- **Unsupervised anomaly detection**: KMeans clustering plus statistical anomaly scores (z‑scores, source diversity, rarity) to highlight previously unseen behaviors.
- **Supervised attack classification**: Random Forest model trained on Invictus‑IR CloudTrail attacks to recognize known adversary techniques.
- **Threat intelligence enrichment**: Custom MITRE ATT&CK mapping for 40+ high‑risk AWS APIs (privilege escalation, exfiltration, credential access, defense evasion).
- **Streaming‑style processing**: Structured Streaming simulation achieving sub‑second latency per 100K events and 100K+ events/second throughput on a single node.
- **Scalability analysis**: Designed to scale from millions to 100M+ events with partitioned storage, sampling strategies, and performance benchmarking.

## Architecture

1. **Data sources**
   - flAWS.cloud CloudTrail dataset (1.9M+ events).
   - Invictus‑IR CloudTrail dataset with labeled attacks.
   - Optional personal AWS account logs for benign baseline.

2. **Processing pipeline**
   - Batch ingestion from compressed JSON → Spark DataFrames.
   - Schema validation, null handling, and normalization.
   - Feature engineering:
     - Temporal (hour, day, off‑hours).
     - User behavior (unique IPs, regions, actions per principal).
     - Binary flags (root usage, errors, rare events).
   - Unsupervised KMeans clustering and cluster‑level statistics.
   - Composite anomaly scoring and threshold calibration.
   - Threat‑intel enrichment using MITRE ATT&CK mappings.
   - Random Forest classifier for labeled attacks (Invictus‑IR).

3. **Outputs**
   - Ranked anomaly lists with severity and MITRE techniques.
   - Detection metrics (precision, recall, F1, ROC, PR).
   - Cluster quality metrics and ablation studies.
   - Streaming demo over mixed flAWS/Invictus/personal logs.

## Tech Stack

- **Distributed compute**: Apache Spark (PySpark, Spark SQL, Spark MLlib)
- **Machine learning**: KMeans, Random Forest, Optuna for hyperparameter tuning
- **Storage**: JSON input, partitioned Parquet intermediates
- **Language / tooling**: Python, Jupyter/Colab, Pandas, Matplotlib/Seaborn

## Getting Started

> This repo is organized as a Jupyter notebook that can be run end‑to‑end on a single beefy instance or a managed Spark cluster.

1. Clone the repository:

git clone https://github.com/.... 
cd CloudGuard


2. Open the notebook in Jupyter or Google Colab.

3. Obtain datasets:
- Download the flAWS.cloud CloudTrail dataset.
- Download the Invictus‑IR CloudTrail dataset.
- (Optional) Export your own AWS CloudTrail logs.

4. Update the dataset paths in the notebook and run cells in order:
- Data ingestion and cleaning.
- Feature engineering and KMeans training.
- Anomaly scoring and MITRE enrichment.
- Random Forest training and evaluation.
- Streaming simulation.

> **Note:** This project is research/educational and should be hardened, tuned, and integrated with your organization’s CI/CD and incident‑response playbooks before production use.

## Results (Sample Highlights)

- Processed ~1.9M CloudTrail events with a Spark pipeline.
- Flagged the top ~5% most anomalous events and mapped 100+ high‑confidence threats to MITRE ATT&CK techniques.
- Demonstrated 70%+ detection of real Invictus‑IR attacks via unsupervised methods, improved with a supervised Random Forest ensemble.
- Achieved ~100K+ events/second throughput in streaming simulation on a single node.

## Roadmap

- Integrate with managed Spark (e.g., EMR/Databricks) and AWS EventBridge / Lambda for live response.
- Expand threat‑intel mappings and add rule‑based detectors.
- Add dashboard front‑end (e.g., Streamlit or Grafana) for SOC analysts.

## License

This project is intended for research and educational purposes. Please review individual dataset licenses before using them in a commercial environment.
