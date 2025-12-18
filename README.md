# aml-phd-prep

PaySim fraud models: precision–recall and cost analysis
This repo uses the PaySim synthetic mobile money dataset, which simulates transactional behaviour for a mobile money network and injects realistic fraud patterns. After exploratory analysis, the modelling focuses on the high‑risk CASH_OUT and TRANSFER transactions, where fraud prevalence is roughly 0.30%, a level consistent with rare‑event financial crime data.​

Three baseline models are trained on a simple feature set (amount, oldbalanceOrg, newbalanceOrig, type_code) using a stratified train–test split:

Logistic regression (unweighted).

Logistic regression with class_weight="balanced" (evaluated at two thresholds).

RandomForestClassifier (200 trees, default 0.5 threshold).​

Performance is assessed with ROC‑AUC, PR‑AUC (average precision), confusion matrices, and a simple cost model that assigns a cost of 1,000 units to each missed fraud (FN) and 10 units to each false alert (FP), reflecting the higher impact of undetected fraud in AML settings.​

Model comparison on CASH_OUT / TRANSFER subset
text
model                      ROC_AUC   PR_AUC   precision   recall     FP       FN     total_cost
RandomForest (0.5)         0.9971    0.9631   0.9128      0.8917     138      178    179,400
Logistic (unweighted, 0.5) 0.9758    0.5493   0.9219      0.4169      58      958    589,580
Logistic (weighted, 0.8)   0.9690    0.4495   0.0699      0.7267   15,885     449 496,078,500
Logistic (weighted, 0.5)   0.9690    0.4495   0.0315      0.8685   43,846     216 216,654,460
Key observations:

RandomForest (0.5) delivers the best PR‑AUC (0.963) and very high ROC‑AUC (0.997), with fraud precision ≈ 0.91 and recall ≈ 0.89, generating only 138 false positives and 178 false negatives on ~550k test transactions.​

Under the toy cost model, RandomForest minimises expected cost (≈179k), substantially outperforming both unweighted and class‑weighted logistic baselines that either miss most frauds or trigger tens of thousands of alerts.​

Naïve use of class_weight="balanced" on logistic regression demonstrates the classic AML trade‑off: high recall can be achieved, but at the expense of extremely low precision and an operationally infeasible alert volume.​

These results illustrate the importance of combining PR‑AUC with cost‑sensitive analysis and operating‑point selection when evaluating fraud and AML models, rather than optimising a single metric in isolation.
