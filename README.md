## Name: Niharika N K 
## Project : # Ecommerce Infrastructure Scaling Analysis
## Batch Code : 302602 ( Skillfyme )
# Logistics Delivery Risk Intelligence

This project analyzes shipment and logistics risk data to support proactive operational decisions for route planning and delay management.

## Objective

The overall objective is to evaluate shipment performance and support planners by:

1. Forecasting expected delivery delay hours
2. Predicting whether a shipment should be marked as delay-risk
3. Discovering repeating shipment operating patterns using clustering
4. Reducing dimensionality to visualize operational structure and decision signals

## Task 1: Shipment Data Understanding and Validation

### Objective
Validate the shipment dataset, inspect data quality, and confirm the target variables before building predictive models.

### Activities
- Dataset inspection:
  - row and column counts
  - feature names and data types
  - time coverage of `shipment_timestamp`
- Target analysis:
  - regression target: `delivery_delay_hours`
  - classification target: `delay_required`
- Missing value assessment
- Review of unusual and extreme observations
- Distribution and target behavior analysis

### Key findings
- The dataset contains shipment records across the operational time window.
- The project has two prediction targets: delay hours and delay-risk flag.
- The classification target is imbalanced, with delay-required cases representing approximately 30% of observations.
- Missing values are limited but still require careful handling.
- Several numerical features include outliers, especially `package_weight_kg`, which may reflect realistic heavy shipments rather than data errors.

## Task 2: Data Preparation for Machine Learning

### Objective
Transform the raw shipment data into a machine learning-ready dataset while preserving operational meaning.

### Activities
- Time-based feature engineering:
  - `day_of_the_week`
  - `hour_of_the_day`
- Missing value treatment:
  - median imputation for numeric variables
  - mode imputation for categorical variables
- Encoding:
  - one-hot encoding for `origin_hub`, `destination_type`, `shipping_mode`, `carrier`, and `weather_condition`
  - ordinal encoding for `traffic_level`
- Outlier review using the IQR method
- Standardization for models that need scale-sensitive inputs
- Separation of target variables before model training

### Key technical notes
- Median imputation was chosen for numeric variables because it is robust to skewed distributions and less sensitive to extreme values.
- Mode imputation preserves category distributions for categorical variables.
- Ordinal encoding was used for `traffic_level` to keep the natural ordering from Low to Medium to High.
- High-weight shipments were reviewed as possible outliers, but they were not automatically removed because they may reflect real operational conditions.

## Task 3: Feature Engineering and Selection

### Objective
Create predictive features that capture shipment complexity and route intensity while avoiding data leakage and redundant signals.

### Activities
- Route intensity feature engineering using:
  - `distance_km / expected_delivery_hours`
- Review of predictors available before outcome realization
- Target and post-outcome fields excluded to reduce leakage risk
- Correlation analysis across numerical variables
- Feature relevance and redundancy review

### Key findings
- `distance_km` showed a strong positive relationship with delay risk, approximately 0.73 with `delay_required`.
- `dispatch_delay_hours` and `warehouse_processing_hours` also showed meaningful positive correlations with delay risk.
- `carrier_rating` had a negative correlation with delay risk, suggesting better carrier performance reduces risk exposure.
- `route_intensity` was useful as an engineered signal, though it needed validation as a model feature.
- Strong correlations such as `expected_delivery_hours` and `actual_delivery_hours` indicate redundancy and highlight the need to select features carefully.

## Task 4: Demand Forecasting (Regression)

### Objective
Predict the expected delivery delay hours for shipments so planners can intervene earlier instead of reacting to late disruption.

### Models evaluated
- Linear Regression
- Random Forest Regressor

### Evaluation metrics
- MAE
- RMSE

### Key findings
- A chronological train/test split was used to preserve real-world ordering.
- The Linear Regression model achieved an MAE of approximately 1.33 hours and an RMSE of approximately 1.68 hours on the test set.
- The model performed reasonably as a baseline decision-support tool but tends to overpredict low delays and underpredict high delays in more extreme shipment conditions.
- This indicates solid baseline value for planning, but not enough to replace human operational judgment in highly volatile scenarios.

### Business value
The regression model offers early visibility into delays so planners can re-route or expedite shipments before delays spread through the network.

## Task 5: Delay-Risk Classification

### Objective
Predict whether a shipment needs planner intervention because it should be flagged as delay-risk.

### Target variable
- `delay_required`

### Models evaluated
- Decision Tree Classifier
- Random Forest Classifier

### Evaluation metrics
- Accuracy
- Precision
- Recall
- F1-score
- Confusion matrix

### Key findings
- Random Forest outperformed the Decision Tree across all primary evaluation metrics.
- Random Forest achieved approximately:
  - Accuracy: 95.14%
  - Precision: 93.52%
  - Recall: 100%
  - F1-score: 96.65%
- Recall was especially important because missing a true delay-risk shipment is more costly than generating a false alarm.
- The model provided strong coverage for late-delivery risk detection while keeping unnecessary intervention manageable.

### Operational interpretation
The Random Forest model was selected as the preferred classifier because it successfully identified all delay-risk shipments in the test set and therefore offered the best practical support for proactive planning.

## Task 6: Discover Shipment Operating Patterns using Clustering

### Objective
Identify recurring operating patterns using only information available before the delivery outcome is known.

### Method
- K-Means clustering
- Evaluation using:
  - Elbow method
  - Silhouette score

### Key findings
- K=2 was selected as the best cluster count because it provided the clearest and most interpretable operating pattern split.
- The model separated shipments into two broad operational groups:
  - Cluster 0: Lower-complexity shipments
  - Cluster 1: Higher-complexity long-haul shipments
- Cluster 1 contained longer-distance shipments, higher traffic levels, lower carrier ratings, and longer expected delivery windows.
- These patterns indicate greater operational complexity and a higher exposure to disruption.

### Operational attention
Shipments in Cluster 1 should receive earlier review and closer monitoring because they represent the more risk-prone operating conditions.

## Task 7: Reduce Dimensions and Visualize Results

### Objective
Reduce the feature space into a lower-dimensional representation and visualize the shipment operating patterns in a way that is easier to interpret.

### Method
- Principal Component Analysis (PCA)
- 2D visualization of clustered shipment behavior
- Delay distribution comparison by traffic condition

### Key findings
- PCA retained a meaningful share of the total variance in the first two principal components, allowing the structure of the data to be visualized in two dimensions.
- The cluster separation was visible in the PCA projection, confirming that the selected operational groups were not random.
- Low-traffic shipments showed significantly lower delivery delays than medium- and high-traffic shipments.
- Traffic level emerged as an important delivery-risk signal, but it should be interpreted alongside other operational features rather than in isolation.

### Output artifacts
- Correlation heatmap: `outputs/tasks3/correlation_heatmap.png`
- Actual vs predicted delay plot: `outputs/tasks4/actual_vs_predicted_delay_visualization.png`
- Decision tree confusion matrix: `outputs/tasks5/Confusion_matrix_DT.png`
- Random forest confusion matrix: `outputs/tasks5/Confusion_matrix_RF.png`
- Elbow curve: `outputs/tasks6/elbow_curve.png`
- Silhouette curve: `outputs/tasks6/silhouette_curve.png`
- PCA cluster plot: `outputs/tasks7/pca_clusters.png`
- Delay by traffic level plot: `outputs/tasks7/delivery_delay_by_traffic.png`

## Task 8: Validation, Testing, and Operational Recommendation

### Objective
Validate the final modeling workflow on a held-out chronological evaluation set and translate the results into operational recommendations for logistics planning.

### Validation steps
- Held-out sample review for both regression and classification pipelines
- Missing-value verification across the final training and test datasets
- Target leakage checks to ensure post-outcome fields were not used as predictors
- Chronological split validation to confirm model evaluation reflects future conditions only
- Final regression and classification metric regeneration on the evaluation dataset

### Key findings
- A small held-out sample from the evaluation period was run through the fitted preprocessing and prediction pipeline without refitting preprocessing components on evaluation data.
- All predictor datasets were verified to contain no missing values before model fitting.
- No target or post-outcome fields were found in the regression, classification, or clustering feature sets, confirming the workflow did not leak target information.
- The training period occurred entirely before the evaluation period, so the model testing setup was valid for time-based forecasting.
- The regression model produced an evaluation MAE of approximately 1.33 hours and RMSE of around 1.68 hours on the held-out period.
- The Random Forest classifier produced the best classification performance, including 100% recall on the evaluation data.
- The confusion matrix confirmed the evaluation-set size matched the reported classification performance, indicating the model was evaluated cleanly on unseen data.

### Operational recommendations
RouteWise should prioritize planner intervention when shipments:
1. Are identified as delay-risk by the Random Forest model
2. Fall into the higher-complexity long-haul cluster
3. Operate under medium or high traffic conditions
4. Have long travel distances or lower-rated carriers

The predicted delay duration from the regression model should be used as a supporting estimate of delay magnitude, not as an exact ETA forecast.

### Limitations
- Historical shipment patterns may not fully capture new routes, carriers, or unexpected disruptions.
- The regression model is less accurate in extreme delay conditions and should be treated as a decision-support forecast rather than a precise arrival system.
- The clustering solution has a moderate silhouette score, which indicates some overlap between operating patterns and suggests operational groups should be interpreted as broad patterns rather than strict categories.

## Technologies Used
- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-Learn

## Notebook Reference
This README summarizes the work captured in `logistics_delivery_risk_intelligence.ipynb`, covering Tasks 1 through 8 in the project workflow.
