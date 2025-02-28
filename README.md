﻿# Price-Optimization
### Introduction
Price optimization is the process of setting prices for products sold by retailers to maximize their profits. The goal is to find the optimal price point that will attract customers and generate sales, while also maximizing profit margins. Retailers use various techniques to optimize their prices, such as competitor analysis, customer segmentation, and price testing. Competitor analysis involves monitoring the prices of similar products offered by competitors and adjusting prices accordingly. Customer segmentation involves dividing customers into groups based on their buying behavior and setting prices for each group accordingly. Price testing involves experimenting with different price points to determine the price that maximizes profits. It can help retailers to increase their profits and improve their competitiveness in the marketplace. It requires a deep understanding of customer behavior, market trends, and pricing strategies, as well as the ability to collect and analyze data on sales and pricing. Retailers who can successfully optimize their prices can gain a significant competitive advantage, while also providing customers with products at fair and reasonable prices.


  ### Dataset
  [ Retail Price Optimization ](https://www.kaggle.com/code/harshsingh2209/retail-price-optimization)

  
The dataset contains 676 entries of monthly sales data for a product category over approximately two year (2017-2018) period. Key features include quantity sold, total sales price, freight price, unit price, customer ratings, and various competitive pricing metrics. Temporal features such as month, year, and lag price are also included, providing a comprehensive overview of the product's sales performance and market dynamics.

To effectively optimize price, my project used these approaches below :
- Demand Analysis: Analyze historical sales data (qty, total_price) to identify demand patterns across different time periods (months, years).
- Price Elasticity: Calculate price elasticity of demand using unit_price and qty data to understand how changes in price affect sales volume
- Competitor Analysis: Compare product's prices (unit_price) and associated costs (freight_price) with competitor prices (comp_1, comp_2, comp_3)
- Optimization Models: Develop pricing optimization models using machine learning algorithms to integrate various factors (demand, costs, competition)
