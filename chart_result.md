﻿


##  Exploratory Data Analysis

```python
# distritbution of total price
fig = px.histogram(df, x = "total_price" , nbins = 20, title = "Distribution of Total Price")
fig.show()
```
<img src="code_snapshot\dis_total_price.png" width="700" />

```python
# disttribution of unit price
fig = px.box(df, y = 'unit_price', title = "Distribution of Unit Price")
fig.show()
```
<img src="code_snapshot\dis_unit_price.png" width="700" />

```python
# relationship between quantity and total prices
fig = px.scatter(df, x = 'qty', y = 'total_price',trendline = 'ols', title = 'Relation of Quantity & Total Price')
fig.show()
```
<img src="code_snapshot\relation.png" width="700" />

```python
# bar plot of product category name and unit price
df_avg_unit_price = df.groupby('product_category_name', as_index = False).mean('unit_price')
fig = px.bar(df_avg_unit_price, x = 'product_category_name', y = 'unit_price', title = "Unit Price by Product Category")
fig.update_xaxes(title_text = 'Product Category')
fig.update_yaxes(title_text = 'Avg of Unit Price')
fig.show()
```
<img src="code_snapshot\unit_price_product.png" width="700" />

#### Time Series Analysis
```python
# distribution of total price by weekday
fig = px.box(df, y = 'total_price', x = 'weekday', title = "Total Price by Weekday")
fig.show()

```
<img src="code_snapshot\total_price_weekday.png" width="700" />

```python
# distribution of total price by weekend
fig = px.box(df, x = 'weekend', y = 'total_price', title = "Total Price by Weekend")
fig.show()
```
<img src="code_snapshot\total_price_weekend.png" width="700" />

**Data Aggregation across Month**

```python
df_month_year = df.groupby(['month_year']).agg({'qty':'sum',	'total_price':'sum',	'freight_price':'mean',
                                                'unit_price':'mean',	'weekday':'sum',	'weekend':'sum',	'customers':'sum'
                                                }).reset_index()
df_month_year['month_year']  = pd.to_datetime(df_month_year['month_year'], format = '%d-%m-%Y')
df_month = df_month_year.sort_values(['month_year'])
df_month.head()
```

```python
fig = px.scatter(df_month, x = 'weekend', y = 'total_price', title = 'Total Price by Total Weekend',
                  trendline = 'ols' )
fig.show()
```
<img src="code_snapshot\total_price_tt_weekend.png" width="700" />

```python
fig = px.bar(df_month, x = 'month_year', y = 'customers', title = "Total Customers by Month")
fig.show()

```

<img src="code_snapshot\total_cus_month.png" width="700" />

**Competitor Comparison**

*Price Comparison*


```python
df['comp_1_p_diff'] = df['unit_price'] - df['comp_1']
df['comp_2_p_diff'] = df['unit_price'] - df['comp_2']
df['comp_3_p_diff'] = df['unit_price'] - df['comp_3']

for i in range(1,4):
  comp = f"comp_{i}_p_diff"
  fig = px.bar(df,  x = df['product_category_name'], y = df[comp],
               title = f"Company {i} Price Difference per Unit",
               labels = {'product_category_name' : "product category",
                          comp : f" company {i} "})
  fig.show()
```
<img src="code_snapshot\com_1_p.png" width="700" />
<img src="code_snapshot\comp_2_p.png" width="700" />
<img src="code_snapshot\comp_3_p.png" width="700" />

*Freight Values Comparison*

```python
df['comp_1_fp_diff'] = df['freight_price'] - df['fp1']
df['comp_2_fp_diff'] = df['freight_price'] - df['fp2']
df['comp_3_fp_diff'] = df['freight_price'] - df['fp3']

for i in range(1,4):
  comp = f'comp_{i}_fp_diff'
  fig = px.bar(df, x = df['product_category_name'], y = df[comp],
               title = f'Company {i} Freight Price Difference',
               labels = {'product_category_name':'product category',
                         comp : f'company {i}'})
  fig.show()
```
<img src="code_snapshot\com_1_f.png" width="700" />
<img src="code_snapshot\com_2_f.png" width="700" />
<img src="code_snapshot\com_3_f.png" width="700" />

#### Price Elasticity of demand

Measures the relative percentage change in Quantity of a good and services relative to the percentage change in the price of the goods and services

*Log-Log plot of Regregularice against Quantity observations*



```python
# Apply log transformation
df_log = pd.DataFrame(df)
df_log['log_qty'] = np.log(df_log['qty'])
df_log['log_unit_price'] = np.log(df_log['unit_price'])
fig = px.scatter(df_log, x = 'log_qty', y = 'log_unit_price', title = 'Unit price and Quantity Observation')
fig.update_xaxes(title_text = 'Log of Qty')
fig.update_yaxes(title_text = 'Log of Unit price')
fig.show()

```
<img src="code_snapshot\log.png" width="600" />

The above Log-Log plot shows the observed volume at every price in a scattered graph. The two Axes display the logarithm values of the two variables. Though the scatter suggests it might not be strongly linear, but still indicates some level of relationship between these two variables.

**Building a linear regression model**


```python
ls_model = smf.ols(formula = 'qty ~ unit_price', data = df_log).fit()
print(ls_model.summary())
```
<img src="code_snapshot\summary.png" width="500" />

While the model shows statistical significance overall (F-statistic), the explanatory power (R-squared) is very low, suggesting that unit_price alone explains a small portion of the variability in quantity."""

```python
fig = sm.graphics.plot_ccpr_grid(ls_model)
plt.show()
```
<img src="code_snapshot\residual.png" width="400" />

The slight negative slope of the trend line remains, suggesting a weak negative linear relationship between unit_price and the residuals.

```python
df_demand_schedule= pd.DataFrame(data=df)
df_demand_schedule
```

```python
df_demand_schedule['Price_Elasticity_of_Demand for regular']=(df_demand_schedule.qty.pct_change() / df_demand_schedule.unit_price.pct_change())
df_demand_schedule
```

- If the price elasticity of demand is greater than 1 it is elastic, then the company should cut the price, because the percentage drop in price will result in a larger percentage increase in the quantity sold.
- If the price elasticity of demand is lesser than 1 it is inelastic


```python
elastic_demand = df_demand_schedule.query('`Price_Elasticity_of_Demand for regular` > 1')
elastic_product = elastic_demand['product_id']
top_elastic_product = elastic_product.value_counts().nlargest(10)
print('Top Elastic Products :\n')
print(top_elastic_product)
```

## Feature Engineering

**Correlation Analysis with Unit Price**


```python
corrs = df.corr(numeric_only = True)['unit_price'].sort_values(ascending = False)
fig = px.bar( x = corrs.keys(), y = corrs.values,
             title = 'Correlation with Unit Price',
             labels = {'x' : 'features',
                       'y' : 'unit price correlation'})
fig.show()
```
<img src="code_snapshot\correlation.png" width="900" />

```python
# dropping highly correlated columns
df_less_corr = df.drop(columns = ['lag_price','total_price', 'comp_1_p_diff','comp_2_p_diff','comp_3_p_diff','qty'], axis = 1)
```

```python
# encoding categorical variables
df_encoded = pd.get_dummies(df_less_corr, columns = ["product_category_name"])
numeric_cols = [column for column in df_encoded.columns if pd.api.types.is_numeric_dtype(df_encoded[column])]
```

```python
# preprocessing  model
df_scaled = df_encoded[numeric_cols].copy()
sc = StandardScaler()
df_scaled[numeric_cols] = sc.fit_transform(df_scaled[numeric_cols])
```

## Model Bulding

**Model 1**

In this model, I make use of DecisionTreeRegressor, *a supervised learning algorithm that belongs to the family of decision trees and is used for regression tasks*


```python
# split a dataset into training and testing sets.
X_1 = df_scaled.drop(['unit_price'], axis = 1)
y_1 = df_scaled['unit_price']
X_train_1, X_test_1, y_train_1, y_test_1 = train_test_split(X_1, y_1, test_size = 0.3, random_state = 42)
```

```python
model_1 = DecisionTreeRegressor(random_state=42)
model_1.fit(X_train_1, y_train_1)
y_pred_1 = model_1.predict(X_1)
r_sc = r2_score(y_1, y_pred_1)
mae = mean_absolute_error(y_1, y_pred_1)
print(f'R2 Score: {r_sc}')
print(f'Mean Absolute Error: {mae}')
```

```python
y_pred_1 = model_1.predict(X_test_1)
fig = go.Figure()
fig.add_trace(go.Scatter( x= y_pred_1, y = y_test_1, mode = 'markers',
                marker = dict(color='blue'), name = 'Actual values vs Predict value'))
fig.add_trace(go.Scatter(x = [min(y_test_1), max(y_test_1)], y = [min(y_test_1), max(y_test_1)],
                         mode = 'lines', marker = dict(color = 'red'),
                         name = 'Prediction'))
fig.update_layout(title = 'Actual vs Predict Unit Price')
fig.update_xaxes(title_text = 'Predict Values')
fig.update_yaxes(title_text = 'Actual Values')
fig.show()
```
<img src="code_snapshot\model_1.png" width="700" />

####  Model Explanatory

I used PermutationImportance technique to  measure the importance of features in a machine learning model, a feature which is important (*high weight value*)  might have a greater impact on the model's performance when its values are randomly permuted


```python
perm = PermutationImportance(model_1, random_state=1).fit(X_train_1, y_train_1)
eli5.show_weights(perm, feature_names = X_train_1.columns.tolist())
```
<img src="code_snapshot\weight_1.png" width="300" />

Features with higher weights (importance scores) such as comp_3, product_weight_g, and ps1 are more influential in predicting unit price.

**Model 2**

In this model, I made use of RandomForestRegressor, a supervised learning algorithm that belongs to the ensemble learning methods. It is especifically used for regression tasks, where the goal is to predict a continuous output variable (dependent variable) based on a set of independent variables (features)


```python
X_2, y_2 = df_scaled.drop(['unit_price'], axis = 1), df_scaled['unit_price']
model_2 = RandomForestRegressor(n_estimators = 50, random_state = 40)
model_2.fit(X_2,y_2)
y_pred_2 = model_2.predict(X_2)
```

```python
r_sc_2 = r2_score(y_2, y_pred_2)
mae_2 = mean_absolute_error(y_2, y_pred_2)
print(f'R2 Score: {r_sc_2}')
print(f'Mean Absolute Error: {mae_2}')
```

```python
fig = go.Figure()
fig.add_trace(go.Scatter(x = y_pred_2, y=y_2, mode = 'markers',
                marker = dict(color='blue'), name = 'Actual values vs Predict value'))
fig.add_trace(go.Scatter(x = [min(y_2), max(y_2)], y = [min(y_2), max(y_2)],
                         mode = 'lines', marker = dict(color = 'red'),
                         name = 'Prediction'))
fig.update_layout(title = 'Actual vs Predict Unit Price')
fig.update_xaxes(title_text = 'Predict Values')
fig.update_yaxes(title_text = 'Actual Values')
fig.show()
```
<img src="code_snapshot\model_2.png" width="700" />

#### Model Explanation

```python
perm = PermutationImportance(model_2, random_state = 1).fit(X_2,y_2)
eli5.show_weights(perm, feature_names = X_2.columns.tolist())
```
<img src="code_snapshot\weight_2.png" width="300" />

Features with higher importance scores (comp_2, product_weight_g, product_name_length) are more critical for predicting unit price"""
