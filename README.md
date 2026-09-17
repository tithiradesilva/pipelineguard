# PipelineGuard

### Azure Data Pipeline Monitoring & Incident Recovery

PipelineGuard is a focused Azure-based **LiveOps monitoring and incident recovery project** built around a cloud data pipeline.

The project demonstrates how a data pipeline can be operated as a reliable service through **pipeline monitoring, SLA tracking, controlled failure testing, automatic retry, alerting, troubleshooting, and recovery verification**.

---

## 🏗️ Architecture

**Azure Data Lake Storage Gen2 → Azure Data Factory → Azure Databricks Serverless → PySpark → ADLS Gen2**

> Architecture diagram coming soon.

### Technology Stack

* **Azure Data Lake Storage Gen2** — Raw and processed data storage
* **Azure Data Factory** — Pipeline orchestration and execution
* **Azure Databricks Serverless** — Data processing
* **PySpark** — Transformation and deduplication
* **Azure Monitor** — Failure and execution monitoring
* **Azure Action Groups** — Operational notifications
* **Azure RBAC/IAM** — Service access control

---

# 1. End-to-End Data Pipeline

Azure Data Factory orchestrates a Databricks Serverless Job that executes a PySpark transformation.

### Pipeline Flow

```text
Raw Sales Data
      ↓
ADLS Gen2 / raw
      ↓
Azure Data Factory
      ↓
Databricks Serverless
      ↓
PySpark Transformation
      ↓
ADLS Gen2 / processed
```

### Transformation

The Databricks notebook:

1. Reads the sales dataset from ADLS Gen2.
2. Identifies and removes duplicate records using `order_id`.
3. Calculates `total_amount` using quantity × price.
4. Writes the processed dataset back to ADLS Gen2.

### Initial Validation

| Metric / Check        |                       Result |
| --------------------- | ---------------------------: |
| ADF Pipeline Status   |                  `Succeeded` |
| Databricks Job Status |                  `Succeeded` |
| Input Records         |                         `21` |
| Output Records        |                         `20` |
| Duplicates Removed    |                          `1` |
| Processed Output      | Successfully written to ADLS |

---

# 2. LiveOps Monitoring

PipelineGuard adds an operational monitoring layer on top of the data pipeline.

The monitoring layer tracks:

* Pipeline and activity status
* Execution duration
* Pipeline failures
* Databricks execution errors
* SLA performance
* Pipeline run IDs and timestamps
* Recovery status

### Monitoring & Alerting

Azure Monitor was configured with:

* **Pipeline failure alert**
* **Long-running pipeline/SLA monitoring**
* **Action Group notifications**

ADF activity retry was configured with **1 automatic retry**.

This creates a first level of automated recovery before persistent failures are escalated to an operator.

---

# 3. Incident Detection & Recovery Scenarios

Each scenario follows:

```text
Detect → Diagnose → Recover → Verify
```

---

## 3.1 Missing Input

### Failure

The expected `sales.csv` input was intentionally made unavailable.

### Detection

ADF reported a failed Databricks activity.

Databricks identified the root cause:

```text
[PATH_NOT_FOUND]
Path does not exist:
abfss://raw@stpipelineguard01.dfs.core.windows.net/sales.csv
```

### Recovery

The missing input was restored and the pipeline was manually rerun.

### Result

`Pipeline Succeeded`

**Demonstrates:** failure detection, root-cause analysis, Databricks troubleshooting, recovery and verification.

---

## 3.2 Invalid Schema

### Failure

The input schema was intentionally changed so that `quantity` was unavailable and `qty` was provided instead.

### Detection

Databricks reported:

```text
[UNRESOLVED_COLUMN.WITH_SUGGESTION]
```

The error identified the available `qty` column.

### Automated Response

ADF automatically retried the failed activity.

The failure persisted, triggering the Azure Monitor pipeline failure alert.

### Recovery

The schema was corrected and the pipeline was manually rerun.

### Result

`Pipeline Succeeded`

**Demonstrates:** data-quality troubleshooting, automatic retry, alert escalation and recovery.

---

## 3.3 SLA Breach

PipelineGuard defines a **2-minute business SLA** for pipeline execution.

A controlled delay was introduced to validate the SLA behavior.

| Execution            | Duration | Result          |
| -------------------- | -------: | --------------- |
| Normal execution     |    `59s` | ✅ SLA Met       |
| Controlled execution | `3m 20s` | ⚠️ SLA Breached |
| Recovery execution   |    `59s` | ✅ SLA Met       |

The controlled execution exceeded the 2-minute business target while the pipeline itself successfully completed.

ADF's native elapsed-time metric was also configured for long-running execution monitoring. The native metric requires a minimum threshold of **10 minutes**, so the 2-minute value is treated as the project's business SLA rather than directly enforced by the native metric.

**Demonstrates:** SLA definition, execution monitoring, KPI interpretation, controlled performance testing and recovery verification.

---

## 3.4 Notebook/Application Failure

### Failure

A controlled application failure was introduced into the Databricks notebook:

```text
Exception:
PipelineGuard simulated application failure
```

### Detection & Automated Recovery

```text
Notebook Failure
      ↓
ADF detects failure
      ↓
Automatic Retry
      ↓
Retry fails
      ↓
Azure Monitor Alert
      ↓
Operator Diagnosis
```

### Recovery

The simulated failure was removed.

The pipeline was manually rerun to verify recovery.

### Result

`Pipeline Succeeded`

### Recovery Workflow

```text
Detect
  ↓
Automatic Retry
  ↓
Alert
  ↓
Diagnose
  ↓
Fix
  ↓
Manual Rerun
  ↓
Verify
```

**Demonstrates:** application-level failure detection, automated retry, alerting, troubleshooting, manual recovery and verification.

---

# 4. Operational Takeaways

PipelineGuard demonstrates practical LiveOps concepts rather than only successful pipeline execution:

* **Monitoring:** Observing pipeline health and execution behavior.
* **SLA/KPI Management:** Measuring execution against a defined service target.
* **Incident Detection:** Identifying failures through ADF and Azure Monitor.
* **Root-Cause Analysis:** Using pipeline activity details and Databricks execution output.
* **Automated Recovery:** Using ADF retry for the first recovery attempt.
* **Incident Escalation:** Alerting operators when failures persist.
* **Service Recovery:** Applying a structured diagnose → fix → rerun → verify workflow.
* **Service Improvement:** Turning repeated operational handling into a documented and partially automated process.
* **IAM/RBAC:** Applying controlled service-to-service access.

---

# 5. Conclusion

PipelineGuard demonstrates how a cloud data pipeline can be treated as an operational service rather than only a data-processing workflow.

Through controlled failure scenarios, SLA testing, monitoring, automatic retry, alerting and recovery verification, the project follows a practical:

**Monitor → Detect → Diagnose → Recover → Verify**

approach to cloud service reliability.

The project intentionally remains lightweight and focused, prioritizing **operational visibility, troubleshooting and recovery** over unnecessary monitoring infrastructure or dashboards.

---

## 📚 Documentation

Detailed project documentation covers:

* Pipeline creation
* Architecture and workflow
* Monitoring configuration
* SLA definition
* Failure scenarios
* Troubleshooting
* Recovery procedures
* IAM/RBAC decisions
* Lessons learned

---

## 👨‍💻 Project Focus

**Cloud Operations · LiveOps · Azure · Data Engineering · Incident Recovery · SLA Monitoring · Observability · Troubleshooting**
