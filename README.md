# 2024 Alcohol Sales Analysis for Wal-Mart in Iowa

### Why this projec

This project was designed to simulate a real-world BI request from multiple stakeholders. I built it to demonstrate my ability to move from raw public datasets through to business-ready dashboards and insights through the use of SQL, Power BI, and stakeholder-aligned documentation.


### Overview

This project analyzes and visualizes alcohol sales data for Crown Royal and Tito's Vodka in Wal-Mart stores across Iowa during 2024. The insights will support key business decisions related to inventory management, sales strategy, and marketing efforts.

### Project Workflow
The project follows a structured workflow:
  1. Building Business Intelligence Documentation
  2. Accessing Public Dataset in BigQuery
  3. Cleaning, Transforming, and Aggregating Data in BigQuery
  4. Pulling Data into Power BI
  5. Using DAX to Adjust Columns and Metrics in Power BI

## 1. Business Intelligence Documentation
I created a BI Documentation Report simulating and outlining stakeholder requirements, objectives, data sources, and deliverables.

### Stakeholder Requirements
Sales Managers
  - View total alcohol sales by location to help with inventory and sales planning
Marketing Team
  - View % breakdown of Crown Royal vs. Tito's to help tailor marketing campaigns
Business Analysts
  - Analyze sales trends to help support forecasting and strategy

### Project Requirements
Data Source: BigQuery (Iowa Liquor Sales Dataset)
Visualization Tool: Power BI
Key Metrics:
  - Total Sales by store, product, and time
  - Sales Trends (daily, weekly, monthly)
  - Top 10 Performing Stores
  - Product Sales Breakdown (Crown Royal vs. Tito’s Vodka)

### Deliverables
1. Power BI dashboard displaying total sales, trends, and top-performing locations.
2. Business Intelligence report documenting methodology and insights.
3. User guide for navigating the Power BI dashboard.

## 2. Accessing Public Dataset in BigQuery
We use the Iowa Liquor Sales Public Dataset available in Google BigQuery to extract relevant alcohol sales data. The dataset contains information on sales transactions, store details, and product descriptions.

```
SELECT * 
FROM `bigquery-public-data.iowa_liquor_sales.sales` 
LIMIT 10;
```

## 3. Creating a Target Table and Aggregating Data
Once the dataset is accessed we create a target table, filter for only Crown Royal and Tito's Vodka, and aggregate the data to make it usable for business intelligence reporting.

```
CREATE OR REPLACE TABLE `bi-portfolio-spirit.iowa_spirit_sales.cleaned_sales_data` AS
SELECT 
    DATE(date) AS sale_date,
    store_number,
    store_name,
    city,
    county,
    category_name,
    item_description,
    SUM(bottles_sold) AS total_bottles_sold,
    SUM(sale_dollars) AS total_sales
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE sale_dollars IS NOT NULL
GROUP BY sale_date, store_number, store_name, city, county, category_name, item_description;
```

```
CREATE OR REPLACE TABLE `bi-portfolio-spirit.iowa_spirit_sales.aggregated_sales_walmart_filtered`
AS
SELECT
  sale_date,
  store_name,
  item_description,
  SUM(total_sales) AS total_sales,
  COUNT(*) AS total_transactions
FROM `bi-portfolio-spirit.iowa_spirit_sales.cleaned_sales_data`
WHERE sale_date BETWEEN '2024-01-01' AND '2024-12-31'
  AND TRIM(UPPER(store_name)) LIKE 'WAL-MART%'
  AND TRIM(UPPER(item_description)) IN (
      'CROWN ROYAL', 
      'CROWN ROYAL REGAL APPLE', 
      'CROWN ROYAL PEACH', 
      'CROWN ROYAL WHISKY SOUR BLACK CHERRY COCKTAIL', 
      'TITOS HANDMADE VODKA', 
      'FIREBALL CINNAMON WHISKEY'
  )
GROUP BY sale_date, store_name, item_description;
```

## 4. Pulling Data into Power BI
Steps:
1. Connect Power BI to BigQuery using the Google BigQuery Connector.
2. Import the cleaned dataset into Power BI.
3. Ensure data types are correctly set (e.g., dates as DateTime, sales as Currency).

## 5. Using DAX to Adjust Columns and Metrics in Power BI

Once the data is in Power BI, we use DAX (Data Analysis Expressions) to create calculated columns and measures for better reporting.

### Key DAX Calculations:

Total Sales Calculation:
```
Total Sales = SUM('SalesData'[total_sales])
```

Sales Trend Percentage Change:
```
Sales Change % =
VAR PrevMonth = CALCULATE(SUM('SalesData'[total_sales]),
        PREVIOUSMONTH('SalesData'[sales_month])
    )
RETURN
    IF(NOT(ISBLANK(PrevMonth)),
       (SUM('SalesData'[total_sales]) - PrevMonth) / PrevMonth,
       BLANK()
    )
```

Percentage Breakdown by Product:
```
Crown Royal % =
DIVIDE(
    CALCULATE(SUM('SalesData'[total_sales]), 'SalesData'[item_description] = "Crown Royal"),
    SUM('SalesData'[total_sales]),
    0
)
```

This ensures that the Power BI dashboard is interactive and provides meaningful insights.
![Portfolio Dashboard](https://github.com/user-attachments/assets/6422c07b-6ff9-4880-b034-c8cbc752753e)


## Conclusion
This project provides valuable sales insights for Wal-Mart’s alcohol sales in Iowa, focusing on Crown Royal and Tito’s Vodka. The combination of BigQuery, Power BI, and DAX enables interactive analysis and data-driven decision-making.

Key Insights Delivered:”
- Crown Royal outperforms Tito’s in rural counties, but Tito’s dominates urban Wal-Mart locations
- Sales peaks around major holidays with notable spikes in June and December
- One store accounted for over 15% of Crown Royal sales <- a strong candidate for focused inventory planning

Future improvements may include:
- Expanding analysis to other product categories.
- Adding real-time data updates for better forecasting.
- Integrating with inventory management systems for automated decision support.
