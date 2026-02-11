# 🏏 Scalable IPL Analytics Engine: Distributed Big Data Processing

[![Apache Spark](https://img.shields.io/badge/Apache%20Spark-3.5-E25A1C?logo=apachespark&style=flat-square)](https://spark.apache.org/)
[![Databricks](https://img.shields.io/badge/Databricks-Runtime-FF3621?logo=databricks&style=flat-square)](https://databricks.com/)
[![AWS S3](https://img.shields.io/badge/AWS-S3%20Storage-569A31?logo=amazon-s3&style=flat-square)](https://aws.amazon.com/s3/)
[![Python](https://img.shields.io/badge/Python-3.9+-3776AB?logo=python&style=flat-square)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

> **A high-performance distributed data pipeline built to ingest, transform, and analyze large-scale Indian Premier League (IPL) datasets using Apache Spark on Databricks and AWS S3.**

---

## 🚀 Project Overview

This project demonstrates an **end-to-end Big Data Engineering workflow**. Moving beyond simple analysis, it focuses on the architectural rigor of processing complex datasets in a distributed environment. 

By leveraging **Apache Spark's** lazy evaluation and in-memory processing, the system efficiently handles complex joins across millions of records (Ball-by-Ball data) stored in **AWS S3**, performing advanced transformations and SQL analytics within a **Databricks** cluster.

### 🎯 Key Engineering Goals
*   **Distributed Computing:** distinct separation of Compute (Databricks/Spark) and Storage (AWS S3).
*   **Schema Enforcement:** Implementation of explicit `StructType` schemas to ensure data consistency and type safety (overcoming the risks of `inferSchema`).
*   **Hybrid Analysis:** Seamless integration of PySpark DataFrames for ETL and Spark SQL for analytical queries.

---

## 🏗️ System Architecture

The pipeline follows a modern Lakehouse-style architecture:

1.  **Storage Layer (AWS S3):** Acts as the raw landing zone for heterogenous CSV datasets.
2.  **Compute Layer (Databricks):** 
    *   **Driver Node:** Manages metadata and task scheduling.
    *   **Executor Nodes:** Performs parallel processing of tasks.
3.  **Processing Layer (Apache Spark):** Handles ingestion, cleaning, transformation (Window functions), and aggregation.
4.  **Presentation Layer:** Matplotlib & Pandas for visualizing insights.

---

## 🛠️ Tech Stack

| Component | Technology Used | Description |
| :--- | :--- | :--- |
| **Compute Engine** | **Apache Spark (PySpark)** | Core engine for distributed data processing. |
| **Platform** | **Databricks** | Managed platform for cluster management and notebook orchestration. |
| **Storage** | **Amazon S3** | Scalable object storage for raw input files. |
| **Query Language** | **Spark SQL** | Used for complex analytical queries on temporary views. |
| **Visualization** | **Matplotlib / Pandas** | converting aggregated Spark results for graphical representation. |

---

## 📂 The Dataset

The analysis utilizes a relational schema consisting of 5 interconnected CSV files:
Link: s3://ipl-data-analysis-project
1.  **🔴 Ball-by-Ball:** Granular data (every delivery, runs, extras, wickets).
2.  **🏟️ Match Data:** Summary of toss, venue, results, and margins.
3.  **👤 Player Data:** Personal details, batting/bowling styles.
4.  **📊 Player-Match:** Performance metrics per specific match.
5.  **🛡️ Team:** Unique identifiers and franchise names.

---

## ⚙️ Key Technical Implementations

### 1. Robust Data Ingestion
Instead of relying on `inferSchema=True` (which is computationally expensive and error-prone), this project utilizes **Explicit Schema Definition**:
```python
# Example: Enforcing Type Safety
schema = StructType([
    StructField("match_id", IntegerType(), True),
    StructField("inning", IntegerType(), True),
    StructField("batting_team", StringType(), True),
    ...
])
```
### 2. Advanced Transformations
* **Window Functions:** Calculated running totals of runs per match and over partition.
* **Conditional Logic:** Flagging "High Impact" deliveries (Wickets or 6s).
* **Lazy Evaluation:** Designing transformations (Filter, Map) that are optimized by the Catalyst Optimizer before the final Action (Count, Show).

### 3. Spark SQL Integration
Transforming DataFrames into Temporary Views enables complex SQL querying:
```sql
-- Example: Finding Top Scorers joining multiple tables
SELECT p.player_name, SUM(b.runs_scored) as total_runs
FROM ball_by_ball b
JOIN player p ON b.striker_id = p.player_id
GROUP BY p.player_name
ORDER BY total_runs DESC
```
## 📊 Analytics & Insights
The engine derived actionable insights visualized via Matplotlib:
* **Toss Impact:** Statistical correlation between winning the toss and winning the match.
* **Powerplay Efficiency:** Identifying the most economical bowlers during the first 6 overs.
* **Venue Analysis:** Average scoring trends across different stadiums.

## 🧠 Core Concepts Mastered
* **RDD vs DataFrame:** Understanding the shift towards the high-level DataFrame API for optimization.
* **Lazy Evaluation:** How Spark constructs a DAG (Directed Acyclic Graph) and executes only when an Action is triggered.
* **Cluster Management:** Understanding the interplay between Driver (Manager) and Executors (Workers).

## 🤝 Future Enhancements
* **Streaming:** Implement Spark Structured Streaming for real-time match data processing.
* **MLlib:** Build a Win Probability Predictor using logistic regression on historical match data.
* **Delta Lake:** Implement ACID transactions for data reliability.

## 📜 License
Distributed under the MIT License. See `LICENSE` for more information.
