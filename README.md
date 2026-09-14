# PipelineGuard

## 🚀 Milestone 1: End-to-End Pipeline

Implemented and validated an end-to-end automated data pipeline where **Azure Data Factory** orchestrates a **Databricks Serverless Job** running a PySpark transformation to remove duplicate sales records and write the processed dataset to **Azure Data Lake Storage (ADLS)**.

### 🏗️ Architecture & Workflow

1. **Orchestration:** Azure Data Factory (ADF) triggers the pipeline execution.
2. **Compute & Processing:** Databricks Serverless Job executes a PySpark script to deduplicate sales records.
3. **Storage:** Refined output dataset is persisted directly to ADLS.

### 📊 Results & Validation

| Metric / Check | Status / Details |
| :--- | :--- |
| **ADF Pipeline Status** | `Succeeded` |
| **Databricks Job Status** | `Succeeded` |
| **Input Record Count** | 21 |
| **Output Record Count** | 20 |
| **Duplicates Removed** | 1 |
| **Processed Output** | Successfully written to ADLS |