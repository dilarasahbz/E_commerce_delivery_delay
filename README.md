# E-Commerce Delivery Delay Prediction Model

A machine learning project built with an Azure ML Pipeline using binary classification to predict delivery delays the moment an order is dispatched.

## Dataset

**Olist E-Commerce (Brazil)** — 96,470 orders | 2016–2018 | 8.1% delay rate

Average delivery time for delayed orders is 31.1 days versus 10.4 days for on-time deliveries — delayed orders take approximately 3 times longer.

Raw dataset: [`eticaret_data.csv`](eticaret_data.csv)

## Problem Definition

Predicting whether a delivery will be delayed at the moment an order is shipped creates value across the following areas:

- Proactive customer notifications
- Optimization of logistics operations
- Reduction of return and cancellation costs
- Preserving customer retention and loyalty
- Identification of supply chain bottlenecks

## Methodology

A chronological train/test split was implemented (training on historical data, testing on subsequent data) — mitigating data leakage risks and simulating real-world production conditions.

**Key features:** `city_late_rate`, `carrier_ratio`, `freight_per_item`, `log_freight`, `approval_ratio`, `price_freight_ratio`

**Target variable (`is_late`):** 1 if actual delivery date exceeds estimated delivery date, 0 otherwise.

Two models were trained in parallel on Azure ML Studio:

- **Two-Class Logistic Regression** — linear decision boundary, interpretable coefficients (baseline model)
- **Two-Class Boosted Decision Tree** — ensemble of sequential weak learners, captures non-linear relationships

![Model Pipeline 1](images/chronological_split_model1.png)
![Model Pipeline 2](images/chronological_split_model2.png)

## Results

| Metric | Logistic Regression | Boosted Decision Tree |
|---|---|---|
| Accuracy | 81.5% | 79.9% |
| Precision | 23.8% | 22.0% |
| Recall | 50.7% | 50.7% |
| F1 Score | 32.4% | 30.6% |
| AUC | 0.754 | 0.725 |
| Threshold | 0.49 | 0.37 |

Logistic Regression provided a more balanced and robust overall model with higher accuracy and AUC. Boosted Decision Tree matched LR's recall (50.7%) by lowering the classification threshold to 0.37.

### Logistic Regression Evaluation

![LR Evaluation](images/logistic_regression_chronological_evaluation_results.jpeg)

### Boosted Decision Tree Evaluation

![BDT Evaluation](images/boosted_decision_tree_chronological_evaluation_results.png)

## Feature Importance (Permutation Feature Importance)

Strongest predictive signals:

1. **log_freight (0.486)** — Higher freight charges correlate with bulkier/heavier packages, which are more prone to transit delays
2. **pickup_ratio (0.318)** — Carrier dispatch latency directly dictates overall delivery timelines
3. **city_late_rate (0.296)** — City-level historical delay rate acts as a strong signal across both models
4. **month_part (0.053)** — Intra-month segmentation (beginning/mid/end of month) captures peak logistical congestion periods

![LR Feature Importance 1](images/logistic_regression_chronological_pfi1.png)
![LR Feature Importance 2](images/logistic_regression_chronological_pfi2.png)
![BDT Feature Importance 1](images/boosted_decision_tree_chronological_pfi1.png)
![BDT Feature Importance 2](images/boosted_decision_tree_chronological_pfi2.png)

## Key Insights

- `carrier_ratio` ranks high in both LR and BDT: carrier selection directly impacts delay risk
- The pipeline is structured for production deployment via an Azure ML Real-Time Endpoint

## Next Steps

This project serves as a prototype. Future iterations will focus on:

- Benchmarking performance using gradient boosting algorithms such as XGBoost and LightGBM
- Incorporating additional contextual data sources such as regional weather conditions and public holiday calendars
- Deploying an Azure ML Real-Time Endpoint to trigger automated proactive notifications upon order dispatch

## Platform

Microsoft Azure Machine Learning Studio
