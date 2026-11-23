# Project Scope

## Purpose

This project is a small, reproducible research prototype for studying whether machine-learning anomaly-detection models can flag unusual database-access behavior from audit logs. It focuses on backend logging, controlled activity generation, dataset preparation, and model evaluation.

## In Scope

- Build a test environment using a small PostgreSQL database and a minimal FastAPI backend.
- Generate controlled activity for simulated users and record database access in audit logs.
- Study exactly five predefined suspicious scenarios:
  1. Unusually high query frequency.
  2. Unusually large data extraction.
  3. Database access at unusual times.
  4. Repeated failed access attempts.
  5. Access to sensitive or unfamiliar tables.
- Use audit-log information such as user or session, timestamp, query type, table accessed, rows or data volume, and failed attempts. Derive additional features when needed.
- Compare Isolation Forest, Local Outlier Factor, and One-Class SVM on the same prepared dataset.
- Evaluate results against the known scenario labels using precision, recall, F1-score, false-positive rate, and scenario-level detection results.
- Save the code, dataset, preprocessing steps, model settings, results, figures, and run instructions so the experiment can be reproduced.

## Out of Scope

- A production-ready security platform or deployment in a live organization.
- A frontend, dashboard, mobile app, microservices, or user-facing product features.
- Large-scale data collection or use of real customer, employee, or production database logs.
- Large language models, deep learning, or additional anomaly-detection models beyond the three selected for comparison.
- SQL injection detection, malware detection, or general network intrusion detection.
- Proving that a person intended harm. In this study, “suspicious” means that an activity matches a predefined test scenario; it does not establish malicious intent.

## Data and Evaluation Boundary

The experiments will use controlled activity generated in the test environment, with normal and suspicious sessions identified by their scenario labels. Findings will describe performance on this dataset and these five scenarios. They will not show how well the models generalize to every database, organization, or real-world attack.

## Scope Rule

If time becomes limited, prioritize the audit-log dataset, the three-model comparison, and the evaluation. Do not add product features or new attack categories during the initial study.
