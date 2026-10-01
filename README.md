# S3 Security Auditor

A Python security tool that audits AWS S3 buckets for misconfigurations using boto3. It scans every bucket in an AWS account and writes a JSON risk report with each finding classified by severity.

## What It Checks

| Check | Severity | Description |
|---|---|---|
| Public ACL | CRITICAL | Bucket or objects accessible to anyone on the internet |
| Public Access Block Missing | CRITICAL | No public access block configuration set |
| Encryption Disabled | HIGH | Server-side encryption not enabled |
| Public Access Block Incomplete | HIGH | Some public access block settings are missing |
| Versioning Disabled | MEDIUM | Object versioning not enabled |
| Logging Disabled | MEDIUM | Server access logging not configured |

## Screenshots

![Audit Output](screenshots/01_s3_audit_output.png)
*Terminal output from an audit run against two buckets.*

![JSON Report](screenshots/02_s3_audit_report_json.png)
*The generated `s3_audit_report.json`.*

![Script Code](screenshots/03_s3_auditor_script.png)
*Part of the auditor script.*

## Sample Output

```
===== S3 SECURITY AUDIT REPORT =====
Scan Time: 2026-06-28T05:13:54.708267
Total Buckets: 2
CRITICAL: 0
HIGH:     0
MEDIUM:   3
PASSED:   0

--- Bucket Details ---

Bucket: aws-cloudtrail-logs-123456789012-434b9f4f
  [MEDIUM] Versioning Disabled: FAIL
  [MEDIUM] Logging Disabled: FAIL

Bucket: cloudtrail-logs-ronan
  [MEDIUM] Logging Disabled: FAIL

Full report saved to s3_audit_report.json
```

## Sample JSON Report

```json
{
  "scan_time": "2026-06-28T05:13:54.708267",
  "account": "123456789012",
  "buckets": [
    {
      "name": "aws-cloudtrail-logs-123456789012-434b9f4f",
      "findings": [
        {"check": "Versioning Disabled", "severity": "MEDIUM", "status": "FAIL"},
        {"check": "Logging Disabled", "severity": "MEDIUM", "status": "FAIL"}
      ]
    }
  ],
  "summary": {
    "total": 2,
    "critical": 0,
    "high": 0,
    "medium": 3,
    "passed": 0
  }
}
```

In this run, both buckets (CloudTrail log buckets) lacked server access logging and one also had versioning off. Nothing was public or unencrypted.

## Setup

### Prerequisites
- Python 3.x
- AWS account with S3 buckets
- IAM user with `AmazonS3ReadOnlyAccess` policy

### Install dependencies

```bash
pip install boto3
```

### Configure AWS credentials

```bash
aws configure
```

Enter your AWS Access Key ID, Secret Access Key, region (`us-east-1`), and output format (`json`).

### Run the auditor

```bash
python s3_auditor.py
```

The script:
1. Lists all S3 buckets in your account
2. Runs the checks in the table above against each bucket
3. Prints a summary to the terminal
4. Saves the full report to `s3_audit_report.json`
