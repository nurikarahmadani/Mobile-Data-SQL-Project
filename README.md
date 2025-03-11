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

Data cleansing to remove unwanted characters and adjust data types
```sql
-- mobile_weight_gram column
UPDATE mobile_2025
SET mobile_weight_gram = CAST(REPLACE(mobile_weight_gram, 'g', '') AS DECIMAL)
WHERE mobile_weight_gram LIKE '%g';

-- ram_gb column
UPDATE mobile_tb
SET ram_gb = REPLACE(REPLACE(REPLACE(REPLACE(ram_gb, 'GB', ''), '/', ''), ' ', ''), ',', '');

SELECT * FROM mobile_tb
WHERE ram_gb = '812'

UPDATE mobile_tb
SET ram_gb = '8'
WHERE ram_gb = '812'

ALTER TABLE mobile_tb
ALTER COLUMN ram_gb DECIMAL(10,2)

-- battery_capacity_maH column

UPDATE mobile_tb
SET battery_capacity_maH = REPLACE(REPLACE(REPLACE(REPLACE(battery_capacity_maH, 'maH', ''), '/', ''), ' ', ''), ',', '')
WHERE battery_capacity_maH LIKE '%maH';

ALTER TABLE mobile_tb
ALTER COLUMN battery_capacity_maH DECIMAL(10,2)

--screen_size_inches column
UPDATE mobile_2025
SET screen_size_inches = CAST(LEFT(screen_size_inches, CHARINDEX(' ', screen_size_inches) - 1) AS FLOAT)

-- launch_price_pkr column
DELETE FROM mobile_tb
WHERE launch_price_pkr = 'not available';
--
UPDATE mobile_tb
SET launch_price_pkr = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_pkr, 'PKR', ''), '/', ''), ' ', ''), ',', '')
--
ALTER TABLE mobile_table
ALTER COLUMN launch_price_pkr DECIMAL(10,2)

-- launch_price_inr column
UPDATE mobile_table
SET launch_price_inr = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_inr, 'INR', ''), '/', ''), ' ', ''), ',', '')
--
ALTER TABLE mobile_table
ALTER COLUMN launch_price_inr DECIMAL(10,2)

-- launch_price_cny column
UPDATE mobile_table
SET launch_price_cny = REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(launch_price_cny, '¥', ''), 'CNY', ''), '/', ''), ' ', ''), ',', '')
--
ALTER TABLE mobile_table
ALTER COLUMN launch_price_cny DECIMAL(10,2)

-- launch_price_usd column
UPDATE mobile_table
SET launch_price_usd = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_usd, 'USD', ''), '/', ''), ' ', ''), ',', '')
--
ALTER TABLE mobile_table
ALTER COLUMN launch_price_usd DECIMAL(10,2)

-- launch_price_aed column
UPDATE mobile_table
SET launch_price_aed = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_aed, 'AED', ''), '/', ''), ' ', ''), ',', '')
--
ALTER TABLE mobile_table
ALTER COLUMN launch_price_aed DECIMAL(10,2)

```

## Exploratory Data Analysis (EDA): Descriptive statistics, data visualization.
### 1. Companies that launched mobile phones in 2025
```sql
SELECT DISTINCT company_name
FROM mobile_table
```
### 2. The Number Of Mobile Phone Launched By Each Company
```sql
-- the number of mobile phone lauched by each company

SELECT 
	company_name,
	COUNT(model_name) AS total_mobile_launched
FROM mobile_table
GROUP BY company_name
```
### 3. Mobile Phone with Highest Launch Price (USD)
```SQL
SELECT TOP 10
	company_name,
	model_name,
	launch_price_usd
FROM mobile_table
ORDER BY launch_price_usd DESC
```
### 4. Mobile Phone with Cheapest Launch Price (USD)
```sql
SELECT TOP 10
company_name,
model_name,
launch_price_usd
FROM mobile_table
ORDER BY launch_price_usd ASC
```
### 5. Mobile Phones with the Highest Annual Prices
```sql
SELECT 
    m.launch_year,
    m.company_name,
    m.model_name,
    m.launch_price_usd AS highest_mobile_price
FROM 
    mobile_table m
WHERE 
    m.launch_price_usd = (
        SELECT 
            MAX(launch_price_usd)
        FROM 
            mobile_table
        WHERE 
            launch_year = m.launch_year
    )
ORDER BY 
    m.launch_year;

```
### 6. Mobile Phones with the Lowest Annual Prices
```sql
SELECT 
    m.launch_year,
    m.company_name,
    m.model_name,
    m.launch_price_usd AS lowest_mobile_price
FROM 
    mobile_table m
WHERE 
    m.launch_price_usd = (
        SELECT 
            MIN(launch_price_usd)
        FROM 
            mobile_table
        WHERE 
            launch_year = m.launch_year
    )
ORDER BY 
    m.launch_year;
```
### 7. Average Mobile Prize per Company
```sql
SELECT
	company_name,
	AVG(launch_price_usd) AS avg_price
FROM mobile_table
GROUP BY company_name
ORDER BY avg_price ASC
```
### 8. Average Selling Prices of Mobile Phones Based on Processor Type
```sql
SELECT 
	processor,
	AVG(launch_price_usd) AS avg_price
FROM mobile_table
GROUP BY processor
ORDER BY avg_price DESC
```
### 9. Highest Mobile Price By Company
```sql
SELECT 
	m.company_name,
	m.model_name,
	m.ram_gb,
	m.front_camera,
	m.back_camera,
	m.processor,
	m.battery_capacity_maH,
	m.screen_size_inches,
	m.launch_price_usd AS highest_price
FROM mobile_table m
WHERE launch_price_usd = (
	SELECT MAX(launch_price_usd)
	FROM mobile_table
	WHERE company_name = m.company_name)
ORDER BY m.launch_price_usd

```
### 10. Lowest Mobile Price By Company
```sql
SELECT 
	m.company_name,
	m.model_name,
	m.ram_gb,
	m.front_camera,
	m.back_camera,
	m.processor,
	m.battery_capacity_maH,
	m.screen_size_inches,
	m.launch_price_usd AS lowest_price
FROM mobile_table m
WHERE launch_price_usd = (
	SELECT MIN(launch_price_usd)
	FROM mobile_table
	WHERE company_name = m.company_name)
ORDER BY m.launch_price_usd

```
### 11. Average Mobile Price Per Company
```sql
SELECT 
	company_name,
	AVG(launch_price_usd) AS average_price
FROM mobile_table 
GROUP BY company_name
ORDER BY average_price 
```
### 12. Cheapest Mobile Phone Per Processor Type
```sql
WITH avg_temp AS(
	SELECT 
		processor,
		AVG(launch_price_usd) AS avg_price
	FROM mobile_table
	GROUP BY processor
)
SELECT 
	mt.processor, 
	av.avg_price AS avg_price_per_processor,
	mt.model_name AS cheapest_model_per_processor,
	mt.launch_price_usd AS cheapest_price_per_processor
FROM mobile_table mt
INNER JOIN avg_temp AS av ON av.processor = mt.processor
WHERE mt.launch_price_usd = (
    SELECT MIN(launch_price_usd) 
    FROM mobile_table 
    WHERE processor = mt.processor
)


```




