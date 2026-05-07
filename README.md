# 2024 Iowa Alcohol Sales Analysis — Wal-Mart BI Dashboard

## Overview

This project simulates a real-world Business Intelligence request involving multiple stakeholders, public datasets, SQL transformation pipelines, and executive-facing reporting.

Using the Iowa Liquor Sales public dataset in Google BigQuery, I designed an end-to-end analytics workflow to analyze 2024 alcohol sales trends for Crown Royal and Tito’s Vodka across Wal-Mart locations in Iowa.

The project demonstrates my ability to:

- Translate stakeholder requirements into actionable reporting solutions
- Build scalable SQL transformation workflows
- Design business-ready Power BI dashboards
- Create meaningful insights from large public datasets
- Structure technical findings into clear business recommendations

---

# Project Goals

The primary goal of this project was to simulate a realistic BI engagement from intake through delivery.

Key objectives included:

- Analyzing sales performance across Wal-Mart locations
- Comparing product performance between Crown Royal and Tito’s Vodka
- Identifying seasonal and geographic sales trends
- Supporting inventory planning and marketing decisions through data visualization

---

# Business Scenario

Stakeholders requested visibility into alcohol sales performance to support operational and strategic decision-making.

## Stakeholder Requirements

### Sales Managers
- Monitor total alcohol sales by location
- Support inventory planning and regional forecasting

### Marketing Teams
- Analyze product share between Crown Royal and Tito’s Vodka
- Identify opportunities for targeted campaigns

### Business Analysts
- Track sales trends over time
- Support forecasting and performance analysis

---

# Technology Stack

| Tool | Purpose |
|---|---|
| Google BigQuery | Data extraction, transformation, aggregation |
| SQL | Data cleaning and business logic |
| Power BI | Dashboard creation and visualization |
| DAX | KPI calculations and trend analysis |

---

# Project Workflow

## 1. Business Intelligence Planning

Created BI documentation to simulate stakeholder alignment and project scoping.

Documentation included:
- Business objectives
- Stakeholder requirements
- KPI definitions
- Data source identification
- Dashboard deliverables

---

## 2. Data Extraction with BigQuery

The Iowa Liquor Sales public dataset was accessed through Google BigQuery.

```sql
SELECT * 
FROM `bigquery-public-data.iowa_liquor_sales.sales`
LIMIT 10;
```

The dataset contains:
- sales transactions
- store information
- product descriptions
- pricing and volume metrics

---

## 3. Data Cleaning & Transformation

Data was cleaned, standardized, and aggregated into reporting-ready tables.

### Key Transformation Tasks

- Converted transaction dates into reporting-friendly formats
- Removed null sales values
- Aggregated total sales and bottle counts
- Filtered for:
  - Wal-Mart locations
  - Crown Royal product lines
  - Tito’s Handmade Vodka
- Structured tables for Power BI ingestion

### Example Aggregation Query

```sql
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
GROUP BY sale_date, store_name, item_description;
```

---

# 4. Power BI Dashboard Development

The transformed dataset was imported into Power BI using the Google BigQuery connector.

Dashboard features included:

- Sales trend analysis
- Product performance breakdowns
- Top-performing store identification
- Interactive filtering by location and product
- KPI visualizations for executive review

---

# 5. DAX Metrics & Calculations

Custom DAX measures were created to support trend analysis and reporting.

## Total Sales

```DAX
Total Sales = SUM('SalesData'[total_sales])
```

## Month-over-Month Sales Change

```DAX
Sales Change % =
VAR PrevMonth =
    CALCULATE(
        SUM('SalesData'[total_sales]),
        PREVIOUSMONTH('SalesData'[sales_month])
    )
RETURN
    IF(
        NOT(ISBLANK(PrevMonth)),
        (SUM('SalesData'[total_sales]) - PrevMonth) / PrevMonth,
        BLANK()
    )
```

## Product Share Breakdown

```DAX
Crown Royal % =
DIVIDE(
    CALCULATE(
        SUM('SalesData'[total_sales]),
        'SalesData'[item_description] = "Crown Royal"
    ),
    SUM('SalesData'[total_sales]),
    0
)
```

---

# Key Insights

The analysis produced several actionable business insights:

- Crown Royal performed more strongly in rural counties
- Tito’s Vodka showed stronger sales in urban Wal-Mart locations
- Sales volume increased significantly around major holidays
- June and December showed the largest sales spikes
- One Wal-Mart location accounted for over 15% of Crown Royal sales, making it a strong candidate for targeted inventory planning

![Portfolio Dashboard](https://github.com/user-attachments/assets/6422c07b-6ff9-4880-b034-c8cbc752753e)

---

# Future Improvements

Potential future enhancements include:

- Expanding analysis to additional alcohol categories
- Implementing near real-time data refresh pipelines
- Adding predictive forecasting models
- Integrating inventory optimization recommendations
- Automating scheduled dashboard refresh workflows

---

# What This Project Demonstrates

This project highlights my ability to:

- Build end-to-end BI workflows
- Translate business needs into technical solutions
- Structure ambiguous requirements into actionable reporting
- Design scalable data transformation pipelines
- Communicate analytical findings clearly to stakeholders
- Combine SQL, Power BI, and DAX into business-ready analytics solutions

---

# Repository Structure

```bash
/project-files
  /sql
  /powerbi
  /documentation
  /screenshots
README.md
```

---

# Author

Dustin Na  
Business Analyst | Systems & Process Optimization | BI & Operational Analytics
Business Analyst | Systems & Process Optimization | BI & Operational Analytics
This project was created as part of my portfolio to demonstrate practical business intelligence, analytics, and reporting workflows using real-world public datasets.
