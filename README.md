# Mobile-Data-SQL-Project
## Project Overview
This project demonstrates various data analysis skills and techniques using Power BI for reporting and dashboard creation, and SQL for data analysis. The goal is to showcase proficiency in data cleaning, exploration, visualization, and statistical analysis.

## Project 1: Sales Data Analysis and Dashboard
### Objective
Analyze sales data to identify trends, patterns, and insights, and create a dynamic dashboard to visualize the results.

### Data
- `sales_data.csv`: Contains sales transactions with fields for date, item, quantity, price, and total sales.

### Steps
1. Database Preparation
2. Data Cleaning: Handle missing values, correct data types.
3. Exploratory Data Analysis (EDA): Descriptive statistics, data visualization.
4. Dashboard Creation: Develop an interactive dashboard using Power BI.
5. Insights and Recommendations: Summarize findings and provide actionable insights.

### Tools and Techniques
- Power BI
- SQL
- Python (Pandas, Matplotlib, Seaborn) - Optional for initial analysis

### Files
- `Sales_Analysis_Report.pbix`: Power BI report file.
- `sales_data.csv`: Raw data file.

## 1. Database Preparation
```sql
CREATE DATABASE mobile_db

CREATE TABLE mobile_2025(
	company_name VARCHAR(20),
	model_name VARCHAR (50),
	mobile_weight_gram VARCHAR (20),
	ram_gb VARCHAR(20),
	front_camera VARCHAR(50),
	back_camera VARCHAR(100),
	processor VARCHAR(50),
	battery_capacity_maH VARCHAR(20),
	screen_size_inches VARCHAR(50),
	launch_price_pkr VARCHAR(20),
	launch_price_inr VARCHAR(20),
	launch_price_cny VARCHAR(20),
	launch_price_usd VARCHAR(20),
	launch_price_aed VARCHAR(20),
	launch_year INT
)

BULK INSERT mobile_2025
FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Datasets\Mobiles_Dataset_2025.CSV'
WITH(
	FORMAT = 'CSV',
	FIRSTROW = 2,
	FIELDTERMINATOR = ',',
	ROWTERMINATOR = '\n',
	CODEPAGE = '1252'
)
```

## 2. Data Cleaning: Handle missing values, correct data types.
Check null values
```sql
SELECT * FROM mobile_2025
WHERE 
	company_name IS NULL
	or model_name IS NULL
	or mobile_weight_gram IS NULL
	or ram_gb IS NULL
	or front_camera IS NULL
	or back_camera IS NULL
	or processor IS NULL
	or battery_capacity_maH IS NULL
	or screen_size_inches IS NULL
	or launch_price_pkr IS NULL
	or launch_price_inr IS NULL
	or launch_price_cny IS NULL
	or launch_price_usd IS NULL
	or launch_price_aed IS NULL
	or launch_year IS NULL

```
Character Cleaning & Data Type Transformation
```sql
-- mobile_weight_gram column
UPDATE mobile_2025
SET mobile_weight_gram = CAST(REPLACE(mobile_weight_gram, 'g', '') AS DECIMAL)
WHERE mobile_weight_gram LIKE '%g';

-- ram_gb column
UPDATE mobile_2025
SET mobile_weight_gram = REPLACE(mobile_weight_gram, 'g', '')
WHERE mobile_weight_gram LIKE '%g';

UPDATE mobile_2025
SET ram_gb = CAST(SUBSTRING(ram_gb, 1, CHARINDEX('/', ram_gb) - 1) AS DECIMAL)
WHERE ram_gb LIKE '%/%';

ALTER TABLE mobile_2025
ALTER COLUMN ram_gb DECIMAL(10, 2);

-- battery_capacity_maH column
UPDATE mobile_2025
SET battery_capacity_maH = REPLACE(battery_capacity_maH, 'maH', '') 
WHERE battery_capacity_maH LIKE '%maH';

UPDATE mobile_2025
SET battery_capacity_maH = REPLACE(battery_capacity_maH, '.', '')

UPDATE mobile_2025
SET battery_capacity_maH = REPLACE(battery_capacity_maH, ',', '')

ALTER TABLE mobile_2025
ALTER COLUMN battery_capacity_maH DECIMAL(10,2)

```


