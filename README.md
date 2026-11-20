# 📊 Data Engineering & Exploratory Analysis: Brazilian Parliamentary Expenses (CEAP)

> **Academic Context:** This repository showcases a practical university project developed for the **Big Data Architecture** Course at the **Data Science and Artificial Intelligence** undergraduate program at the **Pontifical Catholic University of Goiás (PUC Goiás)** (School of Polytechnic and Arts).

## 🎯 Project Proposal

The goal of this project is to simulate a real-world **Data Engineering and Data Analysis** workflow using public open data from the Brazilian Chamber of Deputies (Legislative Branch). Specifically, we analyze the historical series of the **CEAP** (_Cota para Exercício da Atividade Parlamentar_ — Quota for the Exercise of Parliamentary Activity) spanning from **2008 to 2026**.

Public datasets spanning nearly two decades naturally suffer from schema drift, formatting inconsistencies, and storage bloat. Therefore, this assignment challenges us to build an end-to-end automated pipeline that ingests raw historical files, diagnoses and resolves silent structural data corruption, optimizes storage using modern columnar formats, and extracts actionable visual intelligence regarding public spending.

## ⚙️ What the Code Does

The Jupyter Notebook (`AED_BigData.ipynb`) executes a complete, automated data lifecycle in Python:

1. **Automated Ingestion & Storage Management (`requests`, `zipfile`, `os`):**
   - Iteratively fetches compressed annual expense archives (`Ano-[YEAR].csv.zip`) directly from the Chamber of Deputies' official open data portal from 2008 to 2026.
   - Extracts the raw CSV files into a structured local workspace and immediately deletes the `.zip` archives to optimize disk usage.

2. **Resilient ETL & Schema Alignment (`pandas`, `numpy`):**
   - Reads nearly two decades of CSV files while handling structural anomalies (such as shifting delimiters and mixed encodings).
   - Adopts the most recent year (`2026`) as the master reference schema and dynamically aligns older datasets by injecting missing columns filled with `NaN` values, ensuring zero data loss across years.
   - Performs strict data type enforcement, converting currency strings into floating-point numbers (`float`) and parsing date strings into standardized `datetime` timestamps.
   - Consolidates the entire unified historical dataset into a single, high-performance **Apache Parquet** file (`ceap_consolidado.parquet`).

3. **Visual Intelligence & Exploratory Data Analysis (`matplotlib`, `seaborn`):**
   - Queries the optimized Parquet repository to generate six analytical visualizations covering annual spending evolution, top-spending political parties (overall and year-over-year), primary expense categories, geographic distribution across Brazilian states (UFs), and monthly seasonality.

## 🛠️ Technical Problem-Solving Dossier

### Error Diagnosis

During the iterative ingestion of the 2008–2026 historical series, processing certain raw CSV files triggered critical parsing failures, manifesting in two primary ways:

- **Field Misalignment:** Corrupted rows and unexpected line breaks inside free-text columns (such as supplier descriptions and expense details).
- **Delimiter and Encoding Mismatches:** Structural variations between older and newer files, alternating field separators between semicolons (`;`) and commas (`,`), alongside character encoding discrepancies (`utf-8` vs. `latin-1`).

### Root Cause Investigation

Line-by-line inspection of the raw text files revealed that older datasets (particularly between 2008 and 2012) contained text fields with unescaped quotation marks and native line breaks that violated the RFC 4180 standard. Standard parsers interpreted internal line breaks as the end of a record, breaking the tabular structure and shifting columns during `pandas.read_csv` execution.

### Implemented Solution

To handle these anomalies without discarding valid historical records, the ingestion pipeline incorporates defensive parsing strategies:

- **Corrupted Line Isolation:** Applied `on_bad_lines='skip'` to bypass structurally broken lines without halting the automated pipeline.
- **Dynamic Delimiter & Encoding Fallback:** Built a `try-except` mechanism that automatically tests fallback separators (`;` and `,`) and encodings (`utf-8` and `latin-1`).
- **Programmatic Schema Alignment:** Implemented a column-mapping loop that enforces the 2026 column structure across all historical DataFrames before serialization into Parquet.

### Visual Insights Analysis

- You can't see all the code and analysis explain in the Colab Notebook, i hope you enjoy this project, cheers!
