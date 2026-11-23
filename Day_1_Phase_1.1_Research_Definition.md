# Detecting Suspicious Database Access Using Machine Learning

## Research Problem

Database permissions and access-control rules can prevent many unauthorized actions, but an account with valid access may still behave unusually. For example, a user may suddenly run queries at a high rate, retrieve far more data than usual, access a sensitive table they do not normally use, or generate repeated failed attempts. Detecting these patterns with fixed rules can be difficult because normal behavior differs across users and activity signals may need to be considered together.

This project will examine whether machine-learning anomaly-detection methods can identify predefined suspicious database-access behavior from audit-log features in a controlled research prototype. The experiments will use a prepared dataset with known normal and suspicious scenarios. Results will therefore show how the methods perform in this test setting; they will not, by themselves, establish effectiveness in a live production environment.

## Research Question

How effectively can machine-learning anomaly-detection methods identify predefined suspicious database-access sessions from audit-log behavior in a controlled research prototype?

## Research Objective

To design and evaluate a reproducible prototype that analyzes database audit logs and flags suspicious access patterns. The project will create normal and suspicious activity scenarios, prepare relevant audit-log features, and compare Isolation Forest, Local Outlier Factor, and One-Class SVM. Their ability to detect the known suspicious sessions will be assessed using precision, recall, F1-score, and false-positive rate.

## Working Scope

The research will focus on five scenarios: unusually high query frequency, unusually large data extraction, access at unusual times, repeated failed access attempts, and access to sensitive or unfamiliar tables. The work is a research prototype focused on backend logging, dataset preparation, and model evaluation.
