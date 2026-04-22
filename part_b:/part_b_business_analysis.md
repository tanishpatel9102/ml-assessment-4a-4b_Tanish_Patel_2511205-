# B1. Problem Formulation

**(a) Formulate this as a machine learning problem. State clearly: what is the target variable, what are the candidate input features, and what type of ML problem is this? Justify your choice of problem type.**

1. Target Variable- items_sold

2. Candidate Input Features:-

a. Store Details- store_size, location_type
b. Promotion Details- promotion_type
c. Temporal Features- year, day_of_week, is_month_end, is_weekend, is_festival
d. Market Condition- competition_density
e. Historical Features- previous month sales

3. Type of Machine learning- Supervised Regression Problem

4. Justification- The goal is to predict a items sold for a given promotion and store. This supervised learning model since past observation with known results are available. A regression approach is suitable for this beacuse predicton target is measurable numeric value.

**(b) The company currently measures performance using total sales revenue. Explain why using items sold (sales volume) is a more reliable target variable for this problem. What broader principle does this illustrate about target variable selection in real-world ML projects?**

Using sales revenue as target can be misleading because revenue involves price variation, discounts and promotions. Sales revenue can be have different price values which can lead to inappropriate comparisons.
Sales volume (items sold) is good measurable to compare sales growth or future predictions as it is consistent and same across all stores and seasons. 

Broader Principle:
The target variable should directly align with the business objective and remain stable, interpretable, and minimally affected by external or confounding factors. A poorly chosen target can lead to misleading insights and suboptimal decisions.

**(c) A junior analyst suggests running one single global model across all 50 stores. Propose and justify an alternative modelling strategy that accounts for the fact that stores in different locations respond very differently to the same promotion.**

Alternate Modelling Strategy:
Instead of using single global model, a hierarchical model approach is appropriate.

For Example:-
1. Train models as per different location type.
2. Train as per interaction effects like promotion type and location type.

Why hierarchical model approach?
1. Captures local patterns accurately.
2. Enhanced prediction performance.
3. More understanding about market.





# B2. Data and EDA Strategy

**(a)The raw data arrives in four separate tables: transactions, store attributes, promotion details, and a calendar (with weekend and festival flags). Describe how you would join these tables. What is the grain of the final modelling dataset (one row = what?), and what aggregations would you perform before modelling?**

1. Data Joining

The four tables should merged as follows:-
1. Transactions- This table is a base table, which contains sales data and target variable which is items_sold.
2. Store Attributes- Key- store_id, use left join to get all transactions.
3. Promotion Details- Key- promo_id, data may have missing value which refers no promotion.
4. Calendar Table- Key- date, using temporal feature we can extract weekend, festival flags.

2. Grain of Final Modelling
One row = Store * Month * Promotion

3. Aggregation before modelling
Aggregate transaction-level data to store-month level:
Target variable- total_items_sold

1. Sales metrics- total_revenue, avg_basket_size and number_of_transactions.
2. Store features- store_size, store_footfall, local_competition_density and customer demographics. 
3. Promotion features- promo_type and discount_intensity (as per available data).
4. Calendar features- num_weekends and num_festival_days.

**(b)Describe the EDA you would perform before building a model. Specify at least four analyses or charts, what you would look for in each, and how the findings would influence your feature engineering or modelling decisions.**

Distribution of EDA strategy:-

1. Distribution of Sales:- 
Preferred Graph: Histogram.
Check: Skewness and outliers in item_sold.
Impact: Apply log transformation if highly skewed and handle outliers.

2. Time Series Analysis:- (Line plot of sales over time)
Preferred Graph: Line Plot.
Check: Trend seasonality.
Impact: Add features- month, season and festival period, time based validation.

3. Correlation Analysis:-
Preferred Graph- Heatmap.
Check: Relationship between numerical variables.
Impact: Select important features and Remove multicollinearity.

4. Store-level sales:-
Preferred Graph- Grouped Plot.
Check: Performance difference across region (like- urban, rural).
Impact: Encode store type and calculates region wise sales.

5. Promotion effect analysis:-
Preferred Graph: Bar Plot
Check: Average sales under each promotion vs no promotion
Impact: Helps identify promotion effectiveness and create interaction features.

**(c)You notice that 80% of transactions in the dataset occurred without any promotion. Describe how this imbalance could affect your model and what steps you would take to address it.**

Handling Imbalance:-

Problem:
1. 80% of data has no promotion.
2. Modelling can become biased towards predicting baseline means no promo effect.

Impact:
1. Underestimation of promotion impact.
2. Poor performance when promotion are applied.

Solution:
1. Resampling techniques- Oversample promotion case or undersample no promo case
2. Robust Models- Use tree-based models.
3. Use smaple weights- Assign higher importance to promotion observation.





 # B3. Model Evaluation and Deployment

**(a) You have monthly store-level data spanning three years across 50 stores. Describe how you would set up the train-test split. Why is a random split inappropriate here? Which evaluation metrics would you use, and how would you interpret each in the context of this business problem?**

a. Train-test Split 
The data is time-series (monthly over 3 years):-
1. Train set: First 24 months.
2. Test set: Latest 12 months.

b. Why random split is inappropriate:-
1. Breaks temporal order.
2. Ignores seaseasonality and trends.
3. Causes data leakage.

c. Evaluation Metrics
1. Mean Absolute Error
--- Average absolute error.
--- Interpretation: Average deviation in items sold per store-month.
--- Useful for operational planning.

2. R²
--- Variance explained.
--- Interpretation: Overall model fit and explanatory power.

3. Root Mean Squared Error
--- Penalizes large error.
--- Interpretation: Captures risk of large forecasting mistakes.
--- Important for invesntory risk.

**(b) After training, the model recommends the Loyalty Points Bonus for Store 12 in December and the Flat Discount for Store 12 in March. Using the concept of feature importance, explain how you would investigate and communicate to the marketing team why the model makes different recommendations for the same store in different months.**

Model suggests:
1. December: Loyalty Points Bonus
2. March: Flat Discount

a. Feature importance approach:-
1. Global feature importance: Identify key drivers such as month, festival flag, footfall, store type, and past sales.
2. Local explanation: Using methods like feature contribution analysis, compare values of both the months.

b. Investigation:-
1. Seasonality effects: December may have festival/holiday demand.
2. Customer behaviour: Understand which promotion scheme effective during customer purchase, for example- loyalty points scheme during high spending periods.

c. Communication to marketing team:-
1. In December, due to festive season- Loyalty incentives can increases repeat purchases.
2. In March, lower demend period- price reduction can drive sales.

**Note: The model adapts recommendations based on contextual factors (demand pattern, seasonality) rather than giving static strategies.**

**(c)The trained model needs to generate recommendations at the start of every month for all 50 stores without being retrained each time. Describe the end-to-end deployment process: how you would save the model, how new monthly data would be prepared and fed in, and what monitoring you would put in place to detect when the model's performance has degraded and retraining is needed.**

a. Model saving:-
1. Save trained model using pickle or joblib.
2. Provide version while saving.

b. Monthly data pipeline:-
1. Collect new data: Recent sales, latest store attributes.
2. Preprocess data: Apply same transformations like encoding, scaling and feature engineering.
3. Generate features: Store-month level dataset.
4. Model inference: Predict expected sales as per promotion and select promotion with highest sales.

c. Automation:-
1. Schedule pipeline.
2. Store output in dasboards or reports in for marketing team.

d. Monitoring  and performance tracking:-
1. Prediction and actual monitoring: track mean absolute errors or root mean squared error over time.
2. Data drift detection: Monitor changes in footfall, customer behaviour and promotion response.
3. Concept drift: Monitor relationship between features and sales.

e. Retraining Strategy:-
Retrain periodically by span of 3-6 months.

f. Logging and alerts:-
1. Log predictions and inputs.
2. Set alerts for abnormal errors and unsual prediction.

g. Data consistency checks:
Ensure incoming data schema matches training data.