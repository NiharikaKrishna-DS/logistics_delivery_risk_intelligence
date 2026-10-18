# Logistics Delivery Risk Intelligence

This project analyzes shipment and logistics risk data to understand delivery delays, validate the dataset for model readiness, and prepare it for machine learning workflows.

## Objective

The objective of this analysis is to evaluate route-wise shipment performance and support operational decision-making by:

1. Forecasting the expected delay hours for a shipment
2. Predicting whether a shipment should be flagged as delay-risk
3. Discovering operational patterns across the network and identifying risk-prone routes

## Task 1: Shipment Data Understanding and Validation

### Objective
Validate the shipment dataset, inspect the data quality, and confirm the target variables before building any predictive model.

### Activities
- Dataset inspection:
  - Record count and column count
  - Feature names and data types
  - Time coverage of `shipment_timestamp`
- Target analysis:
  - Regression target: `delivery_delay_hours`
  - Classification target: `delay_required`
- Missing value assessment
- Review of unusual and extreme observations
- Distribution and target behavior analysis

### Key findings
- The dataset contains shipment records spanning the full observation period for the logistics network.
- There are two prediction targets:
  - a regression target for delivery delay in hours
  - a classification target for whether delay is required
- The classification target is imbalanced, with delay-required cases accounting for about 30% of the observations.
- Missing values are present but manageable and should be handled carefully to avoid degrading model quality.
- Certain numerical features contain outliers, especially `package_weight_kg`, which may reflect genuinely heavy shipments rather than data errors.
- Feature distributions and target patterns provide a strong foundation for model development once the data is cleaned and transformed.

## Task 2: Data Preparation for Machine Learning

### Objective
Transform the raw shipment data into a machine learning-ready dataset while preserving the business meaning of the features.

### Activities
- Time feature engineering:
  - day of the week
  - hour of the day
- Missing value treatment:
  - median imputation for numeric variables
  - mode imputation for categorical variables
- Categorical encoding:
  - one-hot encoding for `origin_hub`, `destination_type`, `shipping_mode`, `carrier`, and `weather_condition`
  - ordinal encoding for `traffic_level` because its categories have a natural order
- Outlier review using the IQR method
- Scaling strategy for models that require standardized input
- Separation of target variables before modeling

### Key technical notes
- Numeric missing values were handled using the median because it is robust to skewed distributions and less sensitive to extreme values.
- Categorical variables were imputed using the mode to preserve the existing category distribution.
- Ordinal encoding was used for traffic level to retain the natural progression from Low to Medium to High.
- IQR-based analysis identified a small set of high-weight shipments as potential outliers, which should be evaluated with domain knowledge before deciding whether to cap, modify, or retain them.

## Technologies Used
- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-Learn

## Notebook Reference
The summary above reflects the work captured in `logistics_delivery_risk_intelligence.ipynb` and is intended to document the first two analytical tasks in a concise, readable form.
