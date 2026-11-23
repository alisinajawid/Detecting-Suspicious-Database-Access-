# Insider Activity and Audit Log Papers

## Which primary studies are most relevant to audit-log anomaly detection?

### Takeaway

Recent insider-threat studies commonly aggregate broad user-activity logs into per-user day, session, or time-window features, then learn deviations from historical behavior. The closest methodological matches to this project use unsupervised models and compare anomaly scores with labeled CERT scenarios, but the CERT data are synthetic and do not contain database-query audit trails.

### Cited Findings

- Li, Dongyang, Lin Yang, Hongguang Zhang, Xiaolei Wang, Linru Ma, and Junchao Xiao (2021), “Image-Based Insider Threat Detection via Geometric Transformation,” *Security and Communication Networks*, Article 1777536, DOI [10.1155/2021/1777536](https://doi.org/10.1155/2021/1777536). On CERT r4.2, the authors transform user behavior vectors into grayscale images and use geometric transformations to form a self-labeled classification task. They aggregate time, assigned-computer, and activity features at week, day, and session levels (40, 28, and 16 features respectively). The paper reports a 4% improvement in instance-level AUROC and a 2% improvement in user-level AUROC over its autoencoder comparator. The authors identify scenario-specific feature engineering as a weakness: a threat may be missed if the relevant behavior is absent from the feature vector; they also list theoretical grounding and temporal modeling as future work. [Publisher full text](https://onlinelibrary.wiley.com/doi/10.1155/2021/1777536)

- Yuan, Lun-Pin, Euijin Choo, Ting Yu, Issa Khalil, and Sencun Zhu (2021), “Time-Window Based Group-Behavior Supported Method for Accurate Detection of Anomalous Users,” *Proceedings of the 51st IEEE/IFIP International Conference on Dependable Systems and Networks (DSN 2021)*, pp. 250–262, DOI [10.1109/DSN48987.2021.00038](https://doi.org/10.1109/DSN48987.2021.00038). ACOBE combines per-user and group behavior across multi-day windows using an ensemble of autoencoders. On a synthesized CERT insider-threat set with four abnormal users among 929, it reports 99.99% ROC-AUC. Its separate enterprise case study uses seven months of anonymized Windows-server and web-proxy logs from 246 employees; the authors found no attack in that data with Sigma signatures, then injected controlled botnet and ransomware traces. They explicitly say the hand-designed feature set is imperfect and the enterprise case study demonstrates applicability to logs, rather than validated insider detection with naturally occurring ground truth. [Author-hosted paper](https://www.cse.psu.edu/~sxz16/papers/AnomalyDetectionDSN2021.pdf)

- Bertrand, Simon, Josée Desharnais, and Nadia Tawbi (2023), “Unsupervised User-Based Insider Threat Detection Using Bayesian Gaussian Mixture Models,” *Proceedings of the 20th Annual International Conference on Privacy, Security and Trust (PST 2023)*, DOI [10.1109/PST58708.2023.10320169](https://doi.org/10.1109/PST58708.2023.10320169). The method trains per-user Bayesian Gaussian Mixture Models on daily Word2Vec summaries of ordered activity types. The CERT v4.2 data are synthetic: 1,000 simulated employees over 502 days and 32,770,227 events across logon, email, file, HTTP, and device logs; the authors report 70 malicious users and 966 days containing at least one malicious activity. Their 100-run mean is 88.38% recall, 93.08% accuracy, 93.10% true-negative rate, 6.9% false-positive rate, and 0.958 AUC. The paper states that 100% of the data is used for training and detection in the unsupervised experiment. The authors propose adding time, weekday, and activity content and improving the embedding process for real-time use. [Full text on arXiv](https://arxiv.org/html/2211.14437v1)

- Al-Shehari, Taher, Muna S. Al-Razgan, Taha Alfakih, Rakan A. Alsowail, and Saravanan Pandiaraj (2023), “Insider Threat Detection Model Using Anomaly-Based Isolation Forest Algorithm,” *IEEE Access*, 11, 118170–118185, DOI [10.1109/ACCESS.2023.3326750](https://doi.org/10.1109/ACCESS.2023.3326750). The study uses CERT r4.2 and Isolation Forest with varied contamination settings for a data-leakage scenario involving after-hours USB use and employee departure. It reports best results at contamination 0.02: 98% accuracy, 98% detection rate, and 99% F-score. Its Decision Tree baseline has 99% accuracy but 64% detection and 66% F-score, illustrating why accuracy alone is misleading for imbalanced data. The authors recommend further evaluation on other datasets and real-world scenarios, tuning and feature selection, and additional metrics. A reproducibility caveat: the paper calls the true contamination ratio 0.03, but its stated 80/20 train-test counts (277 + 69 malicious versus 1,846,773 + 461,694 normal) imply an overall malicious share of about 0.015%; the stated ratio and counts appear inconsistent. [IEEE record](https://ieeexplore.ieee.org/document/10290890/); [author-posted full text](https://www.academia.edu/118034677/Insider_Threat_Detection_Model_Using_Anomaly_Based_Isolation_Forest_Algorithm)

- Elbasheer, Mohamed, and Adewale Akinfaderin (2025), “User-Based Sequential Modeling with Transformer Encoders for Insider Threat Detection,” arXiv:2506.23446. This arXiv preprint builds user/day/session sequences with 35 features, trains a Transformer on benign activity, and applies LOF, One-Class SVM, and Isolation Forest to reconstruction errors. On its combined CERT test set of 210 benign and 174 malicious users, the Isolation Forest configuration reports 96.61% accuracy, 93.51% precision, 99.43% recall, 96.38% F1, 95.00% AUROC, and 5.71% false-positive rate. The authors state that all CERT releases used are synthetic, that malicious cases are highly imbalanced in some versions, and that real-world validation and improved Transformer explainability are needed. The source reviewed is an arXiv preprint, not treated here as a peer-reviewed publication. [arXiv full text, v2](https://arxiv.org/html/2506.23446v2)

### Inferences

- The proposed PostgreSQL/FastAPI work can borrow a tractable session-level feature design from these studies while making the event source database-specific. A direct comparison of Isolation Forest, LOF, and One-Class SVM on the same controlled database audit-log features is a defensible applied question; these five papers do not establish that no such database-specific comparison exists.
- The published Isolation Forest result is a useful reason to test contamination carefully, but its inconsistent ratio/count reporting means the reported 98%/99% scores should not be treated as a reproducible target without checking the article's data preparation and split.

### Gaps

- The reviewed studies use broad organizational activity logs (logons, email, files, web, and devices) rather than PostgreSQL query audit records with table names, rows returned, query rates, and database response volume. This observation is limited to the five papers above; a separate database-security search is needed before making a literature-wide novelty claim.
- The papers do not provide a direct, per-scenario comparison for the five planned database behaviors: high query rate, large extraction, unusual time, repeated failures, and sensitive or unfamiliar table access.

## What evidence and dataset limitations should constrain interpretation?

### Takeaway

Strong metrics on CERT show that behavior-log anomaly detection is feasible, but they do not establish performance on real database traffic. Synthetic labels, benchmark-specific features, rare positive cases, and inconsistent evaluation setups make direct score comparisons unsafe.

### Cited Findings

- CERT versions in the reviewed studies simulate organizations and inject malicious activity; the logs cover general user activity rather than database queries. The BGMM paper describes 32.77 million synthetic events and notes only 966 user-days with malicious activity; IGT reports 70 malicious versus 930 normal users in r4.2 and only five malicious users in r6.2. [BGMM paper](https://arxiv.org/html/2211.14437v1); [IGT paper](https://onlinelibrary.wiley.com/doi/10.1155/2021/1777536)
- IGT’s r4.2 results vary with granularity: its reported AUROC is about 0.86 for user-week, 0.88 for user-day, and 0.82 for user-session. The authors explain that finer granularity raises the imbalance ratio and can leave fewer training examples. [IGT paper](https://onlinelibrary.wiley.com/doi/10.1155/2021/1777536)
- ACOBE’s very high ROC-AUC is based on four abnormal users among 929 in its synthesized CERT evaluation. Its enterprise case study has no naturally observed attacks; two controlled malware cases are inserted into otherwise attack-free logs. The authors caution that their handcrafted features are not fully adequate. [ACOBE paper](https://www.cse.psu.edu/~sxz16/papers/AnomalyDetectionDSN2021.pdf)
- The Transformer paper's combined test set has a much more balanced benign/malicious user mix than the natural CERT releases; the authors themselves identify synthetic data, imbalance in individual releases, and missing real-world validation as limitations. [Transformer paper](https://arxiv.org/html/2506.23446v2)
- The BGMM study reports a false-positive rate of 6.9%, and its confusion matrix includes 22,960 benign user-days flagged as anomalous in one execution. It also states that the full dataset was used in its unsupervised training/detection procedure. [BGMM paper](https://arxiv.org/html/2211.14437v1)
- The Isolation Forest study itself warns that accuracy can hide the quality of minority-class detection: a Decision Tree achieved 99% accuracy but only 64% detection rate and 66% F-score. Its authors call for further real-world and cross-dataset evaluation and additional metrics. [Isolation Forest paper](https://doi.org/10.1109/ACCESS.2023.3326750)

### Inferences

- For the project's own results, report precision, recall, F1, false-positive rate, and per-scenario outcomes beside accuracy. Keep the test-label generation rules separate from model features, and state clearly that controlled scenarios test feasibility rather than production readiness.
- Since the user-generated data will be simpler than CERT's multi-source logs, performance should be framed as results on this prototype dataset, not as a claim that it detects real insider intent or generalizes to organizations.

### Gaps

- The reviewed papers do not provide a shared dataset, identical preprocessing, or identical train/test protocol, so their metrics cannot be ranked as a fair head-to-head leaderboard.
- The sources do not establish that CERT behavior features transfer to database-query-level patterns; the project must measure its own results and limitations.

## What research gap can this project credibly address?

### Takeaway

The strongest defensible gap is narrow and applied: evaluate common unsupervised outlier detectors on a reproducible, session-level PostgreSQL audit-log dataset with explicit database-access scenarios and transparent false-positive reporting. Do not claim that machine learning or insider-threat logging has never been studied.

### Cited Findings

- The closest reviewed studies aggregate broad user activities and often rely on scenario-specific engineered features: IGT includes time, assigned computer, and activity features from logon, file, web, email, and removable-device logs; the Isolation Forest paper selects fields tied to its specific USB/data-leakage scenario. [IGT paper](https://onlinelibrary.wiley.com/doi/10.1155/2021/1777536); [Isolation Forest paper](https://doi.org/10.1109/ACCESS.2023.3326750)
- ACOBE demonstrates the value of multi-day and group context for low-signal threats, while the 2025 Transformer study reports a comparison that includes Isolation Forest, LOF, and One-Class SVM but adds a sequential Transformer representation and evaluates on synthetic CERT user data. [ACOBE paper](https://doi.org/10.1109/DSN48987.2021.00038); [Transformer paper](https://arxiv.org/html/2506.23446v2)
- The reviewed database-relevant feature gap is visible in the source descriptions: CERT tracks logon, HTTP, email, file, and device activity; the papers do not describe database query/table/rows-returned logs. [BGMM paper](https://arxiv.org/html/2211.14437v1); [IGT paper](https://onlinelibrary.wiley.com/doi/10.1155/2021/1777536)

### Inferences

- A study that records FastAPI-to-PostgreSQL access events, aggregates them into user sessions, and evaluates the same three models on the same feature set can contribute a transparent baseline for database-specific behavior. Its contribution would be the dataset design, controlled scenario definitions, model comparison, and per-scenario analysis—not claiming a new anomaly-detection algorithm.
- Because each generated suspicious session is assigned one primary scenario, the planned experiment can identify which behaviors the models catch more or less often. This is useful for interpreting model results, though it omits overlapping real incidents and should be described as a controlled design choice.

### Gaps

- These papers alone cannot support a global novelty claim about database audit-log anomaly detection. The main report should combine this strand with papers specifically about database audit logs and access-control or SQL activity before finalizing the article's related-work claim.
