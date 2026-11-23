# Cybersecurity Anomaly Detection Methods

## How do anomaly-detection models perform across controlled cybersecurity datasets?

### Takeaway

There is no single winner among One-Class SVM, Isolation Forest, and Local Outlier Factor across the studies reviewed. Performance changes with the dataset, feature representation, anomaly threshold, and false-positive tolerance, so the project should compare all three on one carefully documented dataset and report false positives as well as detection rates. [McGuan et al.](https://arxiv.org/abs/2308.16769) · [Alkhatib et al.](https://arxiv.org/abs/2202.00045) · [Uddin et al.](https://www.nature.com/articles/s41598-024-80021-0)

### Cited Findings

- McGuan, C., Yu, C., & Lin, Q. (2023), “Towards Low-Barrier Cybersecurity Research and Education for Industrial Control Systems,” IEEE International Conference on Intelligence and Security Informatics (ISI 2023), DOI [10.1109/ISI58743.2023.10297207](https://doi.org/10.1109/ISI58743.2023.10297207). The authors built an open framework on two simulated platforms, GRFICSv2 and Factory I/O, generating 54 attacks for GRFICSv2 and evaluating against 50 benign captures per platform; Factory I/O included seven attack cases. Their One-Class SVM-based MinTWin detector achieved 88.9% true-positive rate (TPR) with 0% false-positive rate (FPR) on GRFICSv2 and 100% TPR/0% FPR on Factory I/O. Under the same window and threshold settings, Isolation Forest reached 18.4% TPR/0% FPR on GRFICSv2 and 85.7%/0% on Factory I/O; LOF reached 100%/100% on GRFICSv2 and 100%/0% on Factory I/O. The authors' stated next step is a hardware-in-the-loop setup using PLCs. [Paper](https://arxiv.org/abs/2308.16769) · [DOI](https://doi.org/10.1109/ISI58743.2023.10297207)

- Alkhatib, N., Mushtaq, M., Ghauch, H., & Danger, J.-L. (2022), “Unsupervised Network Intrusion Detection System for AVTP in Automotive Ethernet Networks,” IEEE Intelligent Vehicles Symposium, pp. 1731–1738, DOI [10.1109/IV51971.2022.9827285](https://doi.org/10.1109/IV51971.2022.9827285). The authors compared One-Class SVM, LOF, Isolation Forest, and autoencoders using packet sequences from the Automotive Ethernet Intrusion Dataset. It contains benign and replay-attack captures from a physical in-vehicle testbed; the study used the indoor captures and 58 packet-byte features with sliding windows. The paper reports that the three traditional models performed poorly on both test datasets, attributing this to high dimensionality and irrelevant features masking anomalies; convolutional and LSTM autoencoders performed better. The authors explicitly limit generalization because only one dataset and replay attacks were available, and say hardware implementation remains future work. [Paper](https://arxiv.org/abs/2202.00045) · [DOI](https://doi.org/10.1109/IV51971.2022.9827285)

- Uddin, M. A., Aryal, S., Bouadjenek, M. R., Al-Hawawreh, M., & Talukder, M. A. (2024), “usfAD based effective unknown attack detection focused IDS framework,” *Scientific Reports*, 14, 29103, DOI [10.1038/s41598-024-80021-0](https://doi.org/10.1038/s41598-024-80021-0). The authors compared One-Class Classification methods, including usfAD, LOF, Isolation Forest, and One-Class SVM, across 10 public network-intrusion datasets. Their unsupervised usfAD model was trained on benign traffic; it achieved macro-F1 scores of 99.37% on ToN-IoT-Network and 97.65% on ToN-IoT-Linux, while results differed across datasets and LOF also performed strongly on some. The paper's future-work section identifies hierarchical multi-class attack identification and improved synthetic attack-noise generation as open work. These benchmarks are network-traffic datasets, not database audit-log sessions. [Full text](https://www.nature.com/articles/s41598-024-80021-0) · [DOI](https://doi.org/10.1038/s41598-024-80021-0)

### Inferences

- The controlled ICS study is a close methodological analogue for comparing the selected algorithms on generated scenarios: its contrasting LOF results across two simulators show why a result on one test environment should not be presented as a universal ranking. This is an inference from the platform-specific results reported by McGuan et al. [Paper](https://arxiv.org/abs/2308.16769)
- The AVTP study suggests keeping the initial feature set compact and behavior-focused; a much wider raw feature representation can obscure anomalies. This is an inference from the authors' high-dimensionality discussion and does not establish that compact features are always superior. [Paper](https://arxiv.org/abs/2202.00045)
- The broad IDS benchmark study supports reporting per-scenario results and more than accuracy: its findings vary across datasets, and it evaluates attack-class precision and recall in addition to aggregate measures. The user's study should report precision, recall, F1, FPR, and per-scenario performance, as already planned. [Full text](https://www.nature.com/articles/s41598-024-80021-0)

### Gaps

- None of these three studies evaluates the exact task of detecting unusual user sessions from PostgreSQL audit logs; transfer to database-access behavior remains untested.
- The ICS and automotive results are constrained to simulator or protocol-specific attack sets. Their reported scores should inform experimental design, not serve as expected performance for the database project.

## What do synthetic insider-threat data and generated scenarios imply for this study?

### Takeaway

Synthetic or controlled datasets are useful when real organizational logs are private and rare attacks are difficult to collect, but conclusions must be framed as performance on the constructed dataset. Generated scenarios need transparent labels, feature definitions, and careful evaluation because a small or incomplete feature set may not represent the full context of user behavior. [Gayathri et al.](https://arxiv.org/abs/2102.07277) · [McGuan et al.](https://arxiv.org/abs/2308.16769)

### Cited Findings

- Gayathri, R. G., Sajjanhar, A., Xiang, Y., & Ma, X. (2021), “Anomaly Detection for Scenario-based Insider Activities using CGAN Augmented Data,” *2021 IEEE 20th International Conference on Trust, Security and Privacy in Computing and Communications (TrustCom)*, pp. 718–725, DOI [10.1109/TrustCom53373.2021.00105](https://doi.org/10.1109/TrustCom53373.2021.00105). The study uses the synthetic CMU CERT v4.2 insider-threat dataset (1,000 users, 70 insiders, and activity spanning more than 500 days), then applies a conditional GAN to augment minority classes. Its supervised RF/XGBoost/MLP/1D-CNN experiments show improved multi-class results with CGAN augmentation; for example, the paper reports F-score 0.759 for CGAN+XGBoost and 0.763 for CGAN+1DCNN in its multi-class table. The authors state that their chosen feature set is simple and does not provide complete behavioral context, that conclusions cannot be proved using one feature/data set, and that the AUC scores were not useful for their comparison. [Paper](https://arxiv.org/abs/2102.07277) · [DOI](https://doi.org/10.1109/TrustCom53373.2021.00105)

- The CGAN paper reports 330,452 total preprocessed instances in its table, but the listed normal and malicious counts (329,487 and 966) sum to 330,453. The paper also reports 966 malicious instances in the text. Treat this as a one-instance internal count inconsistency rather than silently reconciling it. [Paper](https://arxiv.org/abs/2102.07277)

- McGuan et al. generate attacks in simulated ICS environments and describe their data-generation and detection pipeline as reproducible; their tested capture set includes 54 attacks and 50 benign captures on GRFICSv2. This demonstrates that scenario-driven generation can support controlled algorithm comparisons, while the paper's future hardware-in-the-loop work marks the boundary of its validation. [Paper](https://arxiv.org/abs/2308.16769)

### Inferences

- The project's proposed five labeled suspicious scenarios are defensible for a research prototype if they are explicitly treated as generated test cases, not as evidence of real insider intent or production readiness. This follows from the synthetic nature of CERT and the simulated nature of the ICS testbed. [CERT study](https://arxiv.org/abs/2102.07277) · [ICS study](https://arxiv.org/abs/2308.16769)
- To avoid an overly easy test, labels should be held out of model inputs, test examples should be separated from the normal activity used to establish user baselines, and features should be described precisely. This is a methodological recommendation inferred from the papers' emphasis on class imbalance, incomplete context, and threshold-dependent results. [CERT study](https://arxiv.org/abs/2102.07277) · [ICS study](https://arxiv.org/abs/2308.16769)

### Gaps

- The reviewed papers do not establish universal thresholds for query frequency, extraction volume, unusual access time, failed attempts, or unusual-table access. The project must define and justify its own thresholds for its generated normal-user profiles.
- The exact performance and false-positive tradeoff of the planned three models on session-level database audit features cannot be predicted from these non-database datasets; it must be measured in the project.
