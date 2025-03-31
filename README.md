# Mobile Phone-Data-SQL-Project
## Project Overview
The Mobile Data SQL Project is a data analysis project that leverages SQL to extract insights from a dataset related to mobile phones. This project demonstrates SQL querying skills to process, filter, and analyze data, providing valuable insights into mobile phone trends, specifications, and market analysis.

### Objective
Analyze sales data to identify trends, patterns, and insights, and create a dynamic dashboard to visualize the results.

### Data
[Dataset](https://github.com/nurikarahmadani/Mobile-Data-SQL-Project/blob/main/Mobiles_Dataset_2025.csv)

### Steps
1. Database Preparation
2. Data Cleaning: Handle missing values, correct data types.
3. Exploratory Data Analysis (EDA): Descriptive statistics, data visualization.
4. Dashboard Creation: Develop an interactive dashboard using Power BI.
5. Insights and Recommendations: Summarize findings and provide actionable insights.

### Tools and Techniques
- Power BI
- SQL (Microsoft SQL Server Management)

### Dashboard
- `Sales_Analysis_Report.pbix`: Power BI report file.
- `sales_data.csv`: Raw data file.

## 1. Database Preparation
>```sql
>CREATE DATABASE mobile_db
>
>CREATE TABLE mobile_2025(
>	company_name VARCHAR(20),
>	model_name VARCHAR (50),
>	mobile_weight_gram VARCHAR (20),
>	ram_gb VARCHAR(20),
>	front_camera VARCHAR(50),
>	back_camera VARCHAR(100),
>	processor VARCHAR(50),
>	battery_capacity_maH VARCHAR(20),
>	screen_size_inches VARCHAR(50),
>	launch_price_pkr VARCHAR(20),
>	launch_price_inr VARCHAR(20),
>	launch_price_cny VARCHAR(20),
>	launch_price_usd VARCHAR(20),
>	launch_price_aed VARCHAR(20),
>	launch_year INT
>)
>
>BULK INSERT mobile_2025
>FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Datasets\Mobiles_Dataset_2025.CSV'
>WITH(
>	FORMAT = 'CSV',
>	FIRSTROW = 2,
>	FIELDTERMINATOR = ',',
>	ROWTERMINATOR = '\n',
>	CODEPAGE = '1252'
>)
>```
>**Sample Data**
>
>![image](https://github.com/user-attachments/assets/4edde339-2ec1-49a0-8c47-813ea65f9547)
>

## 2. Data Cleaning: Handle missing values, correct data types.
>Check null values
>```sql
>SELECT * FROM mobile_2025
>WHERE 
>	company_name IS NULL
>	or model_name IS NULL
>	or mobile_weight_gram IS NULL
>	or ram_gb IS NULL
>	or front_camera IS NULL
>	or back_camera IS NULL
>	or processor IS NULL
>	or battery_capacity_maH IS NULL
>	or screen_size_inches IS NULL
>	or launch_price_pkr IS NULL
>	or launch_price_inr IS NULL
>	or launch_price_cny IS NULL
>	or launch_price_usd IS NULL
>	or launch_price_aed IS NULL
>	or launch_year IS NULL
>
>```
>
>Data cleansing to remove unwanted characters and adjust data types
>```sql
>-- mobile_weight_gram column
>UPDATE mobile_2025
>SET mobile_weight_gram = CAST(REPLACE(mobile_weight_gram, 'g', '') AS DECIMAL)
>WHERE mobile_weight_gram LIKE '%g';
>
>-- ram_gb column
>UPDATE mobile_tb
>SET ram_gb = REPLACE(REPLACE(REPLACE(REPLACE(ram_gb, 'GB', ''), '/', ''), ' ', ''), ',', '');
>
>SELECT * FROM mobile_tb
>WHERE ram_gb = '812'
>
>UPDATE mobile_tb
>SET ram_gb = '8'
>WHERE ram_gb = '812'
>
>ALTER TABLE mobile_tb
>ALTER COLUMN ram_gb DECIMAL(10,2)
>
>-- battery_capacity_maH column
>
>UPDATE mobile_tb
>SET battery_capacity_maH = REPLACE(REPLACE(REPLACE(REPLACE(battery_capacity_maH, 'maH', ''), '/', ''), ' ', ''), ',', '')
>WHERE battery_capacity_maH LIKE '%maH';
>
>ALTER TABLE mobile_tb
>ALTER COLUMN battery_capacity_maH DECIMAL(10,2)
>
>--screen_size_inches column
>UPDATE mobile_2025
>SET screen_size_inches = CAST(LEFT(screen_size_inches, CHARINDEX(' ', screen_size_inches) - 1) AS FLOAT)
>
>-- launch_price_pkr column
>DELETE FROM mobile_tb
>WHERE launch_price_pkr = 'not available';
>--
>UPDATE mobile_tb
>SET launch_price_pkr = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_pkr, 'PKR', ''), '/', ''), ' ', ''), ',', '')
>--
>ALTER TABLE mobile_table
>ALTER COLUMN launch_price_pkr DECIMAL(10,2)
>
>-- launch_price_inr column
>UPDATE mobile_table
>SET launch_price_inr = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_inr, 'INR', ''), '/', ''), ' ', ''), ',', '')
>--
>ALTER TABLE mobile_table
>ALTER COLUMN launch_price_inr DECIMAL(10,2)
>
>-- launch_price_cny column
>UPDATE mobile_table
>SET launch_price_cny = REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(launch_price_cny, '¥', ''), 'CNY', ''), '/', ''), ' ', ''), ',', '')
>--
>ALTER TABLE mobile_table
>ALTER COLUMN launch_price_cny DECIMAL(10,2)
>
>-- launch_price_usd column
>UPDATE mobile_table
>SET launch_price_usd = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_usd, 'USD', ''), '/', ''), ' ', ''), ',', '')
>--
>ALTER TABLE mobile_table
>ALTER COLUMN launch_price_usd DECIMAL(10,2)
>
>-- launch_price_aed column
>UPDATE mobile_table
>SET launch_price_aed = REPLACE(REPLACE(REPLACE(REPLACE(launch_price_aed, 'AED', ''), '/', ''), ' ', ''), ',', '')
>--
>ALTER TABLE mobile_table
>ALTER COLUMN launch_price_aed DECIMAL(10,2)
>```
>**Transformasidan Normalisasi Data**
>```sql
>UPDATE mobile_tb
>SET processor = CONCAT('Mediatek ', processor)
>WHERE processor NOT LIKE 'Mediatek%' AND (processor LIKE '%Dimensity%' OR processor IS NULL);
>
>UPDATE mobile_tb
>SET processor = CONCAT('Mediatek ', processor)
>WHERE processor NOT LIKE 'Mediatek%' AND (processor LIKE '%Helio%' OR processor IS NULL);
>
>
>UPDATE mobile_tb
>SET processor = CONCAT('Bionic ', processor)
>WHERE processor NOT LIKE 'Bionic%' AND (processor LIKE '%Pro%' OR processor IS NULL);
>
>UPDATE mobile_tb
>SET Processor = REPLACE(Processor, 'Snapdragon Qualcomm ', 'Snapdragon ')
>WHERE Processor LIKE '%Snapdragon Qualcomm%';
>
>ALTER TABLE mobile_tb
>ADD processor_brand VARCHAR(50);
>
>UPDATE mobile_tb
>SET processor_brand = CASE
>    WHEN processor LIKE '%Bionic%' THEN 'Bionic'
>    WHEN processor LIKE '%Snapdragon%' THEN 'Snapdragon'
>	WHEN processor LIKE '%Exynos%' THEN 'Exynos'
>	WHEN processor LIKE '%Google Tensor%' THEN 'Google Tensor'
>	WHEN processor LIKE '%MediaTek%' THEN 'MediaTek'
>	WHEN processor LIKE '%Spreadtrum%' THEN 'Spreadtrum'
>	WHEN processor LIKE '%Kirin%' THEN 'Kirin'
>	WHEN processor LIKE '%Unisoc%' THEN 'Unisoc'
>    ELSE 'Other'
>END;
>
>alter table mobile_tb
>add backcamera_widelens varchar(50)
>
>alter table mobile_tb
>add backcamera_ultrawidelens varchar(50)
>
>alter table mobile_tb
>add backcamera_telephotolens varchar(50)
>
>alter table mobile_tb
>add backcamera_macrolens varchar(50)
>
>alter table mobile_tb
>add backcamera_periscopelens varchar(50)
>
>UPDATE mobile_tb
>SET backcamera_ultrawidelens = 
>    CASE 
>        WHEN CHARINDEX('ultra', lower(back_camera)) > 0 THEN 'Yes'
>        ELSE 'No'
>    END
>
>UPDATE mobile_tb
>SET backcamera_telephotolens = 
>    CASE 
>        WHEN CHARINDEX('telephoto', lower(back_camera)) > 0 THEN 'Yes'
>        ELSE 'No'
>    END
>
>UPDATE mobile_tb
>SET backcamera_macrolens = 
>    CASE 
>        WHEN CHARINDEX('macro', lower(back_camera)) > 0 THEN 'Yes'
>        ELSE 'No'
>    END
>
>UPDATE mobile_tb
>SET backcamera_periscopelens = 
>    CASE 
>        WHEN CHARINDEX('periscope', lower(back_camera)) > 0 THEN 'Yes'
>        ELSE 'No'
>    END
>
>
>alter table mobile_tb
>add frontcamera_widelens varchar(50)
>
>alter table mobile_tb
>add frontcamera_ultrawidelens varchar(50)
>
>alter table mobile_tb
>add frontcamera_telephotolens varchar(50)
>
>alter table mobile_tb
>add frontcamera_macrolens varchar(50)
>
>alter table mobile_tb
>add frontcamera_periscopelens varchar(50)
>
>UPDATE mobile_tb
>SET frontcamera_telephotolens = 
>   CASE 
>       WHEN CHARINDEX('telephoto', lower(front_camera)) > 0 THEN 'Yes'
>       ELSE 'No'
>   END
>
>UPDATE mobile_tb
>SET frontcamera_macrolens = 
>   CASE 
>       WHEN CHARINDEX('macro', lower(front_camera)) > 0 THEN 'Yes'
>       ELSE 'No'
>   END
>
>UPDATE mobile_tb
>SET frontcamera_periscopelens = 
>   CASE 
>       WHEN CHARINDEX('periscope', lower(front_camera)) > 0 THEN 'Yes'
>       ELSE 'No'
>   END
>
>alter table mobile_tb
add segm>entation varchar(20)
>
>update mobile_tb
>set segmentation =
>	case 
>		when launch_price_usd<150 then 'Low End'
>		when launch_price_usd BETWEEN 150 AND 500 THEN 'Mid Range'
>		WHEN launch_price_usd BETWEEN 500 AND 1000 THEN 'High End'
>        ELSE 'Ultra Premium'
>    END;
>```
>**Cleaned Data Sample**
>
>![image](https://github.com/user-attachments/assets/7d0023ce-3a59-4dbf-9014-d1092e6a22e8)
>

## Exploratory Data Analysis (EDA): Descriptive statistics, data visualization.
### 1. Companies that launched mobile phones in 2025
> ```sql
> SELECT DISTINCT company_name
> FROM mobile_table
> ```
> **Data Result Sample**

> ![image](https://github.com/user-attachments/assets/b279107a-5e89-40d5-a83b-040d6e880838)

### 2. The Number Of Mobile Phone Launched By Each Company
> ```sql
> -- the number of mobile phone lauched by each company
> SELECT 
>	company_name,
>	COUNT(model_name) AS total_mobile_launched
> FROM mobile_table
> GROUP BY company_name
> ```
> **Data Result Sample**

> ![image](https://github.com/user-attachments/assets/2eb5f4f5-d526-4733-a941-a20a2836b78d)


> **Visualization**
>
> <img src="https://github.com/user-attachments/assets/172f5e54-9ac6-4beb-be01-19ab0e7c9e56" width="800">

### 3. Mobile Phone with Highest Launch Price (USD)
>```SQL
>SELECT TOP 10
>	company_name,
>	model_name,
>	launch_price_usd
>FROM mobile_table
>ORDER BY launch_price_usd DESC
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/44c17b0a-b6c8-45cd-9b01-88f85f6cf073)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/4045993f-c51e-4e8c-9b98-d99ac5fc34f2)


### 4. Mobile Phone with Cheapest Launch Price (USD)
>```sql
>SELECT TOP 10
>company_name,
>model_name,
>launch_price_usd
>FROM mobile_table              
>ORDER BY launch_price_usd ASC
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/7ecb9f34-81e8-4932-a731-888a4c0709e4)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/3ecdaa59-91ff-4991-abde-88e10e1d7158)
>
### 5. Mobile Phones with the Highest Annual Prices
>```sql
>SELECT 
>    m.launch_year,
>    m.company_name,
>    m.model_name,
>    m.launch_price_usd AS highest_mobile_price
>FROM 
>    mobile_table m
>WHERE 
>    m.launch_price_usd = (
>        SELECT 
>            MAX(launch_price_usd)
>        FROM 
>            mobile_table
>        WHERE 
>            launch_year = m.launch_year
>    )
>ORDER BY 
>    m.launch_year;
>
>```
>
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/fcde1edc-ab82-470a-8cbc-b5523f375008)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/4fe0e98f-3e6e-47fe-a4d4-3a1c63b10c21)

### 6. Mobile Phones with the Lowest Annual Prices
>```sql
>SELECT 
>    m.launch_year,
>    m.company_name,
>    m.model_name,
>    m.launch_price_usd AS lowest_mobile_price
>FROM 
>    mobile_table m
>WHERE 
>    m.launch_price_usd = (
>        SELECT 
>            MIN(launch_price_usd)
>        FROM 
>            mobile_table
>        WHERE 
>            launch_year = m.launch_year
>    )
>ORDER BY 
>    m.launch_year;
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/69d64a20-da0f-4990-a85a-89c350fcf0f1)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/02941faf-9453-47b0-8993-015c8cadd2aa)

### 7. Average Mobile Prize per Company
>```sql
>SELECT
>	company_name,
>	AVG(launch_price_usd) AS avg_price
>FROM mobile_table
>GROUP BY company_name
>ORDER BY avg_price ASC
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/96eebc73-76db-41cb-afc4-d9a190973473)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/8b8e08b1-e167-4098-96fd-68440ab6a18b)

### 8. Highest Mobile Price By Company
>```sql
>SELECT 
>	m.company_name,
>	m.model_name,
>	m.ram_gb,
>	m.front_camera,
>	m.back_camera,
>	m.processor,
>	m.battery_capacity_maH,
>	m.screen_size_inches,
>	m.launch_price_usd AS highest_price
>FROM mobile_table m
>WHERE launch_price_usd = (
>	SELECT MAX(launch_price_usd)
>	FROM mobile_table
>	WHERE company_name = m.company_name)
>ORDER BY m.launch_price_usd
>
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/4e7ece7f-bee3-4940-87fd-167381d230a8)
>
>**Visualization**
>
>![image](https://github.com/user-attachments/assets/34f19cdf-e5e0-4d61-8e91-4b5981120d08)

### 9. Lowest Mobile Price By Company
>```sql
>SELECT 
>	m.company_name,
>	m.model_name,
>	m.ram_gb,
>	m.front_camera,
>	m.back_camera,
>	m.processor,
>	m.battery_capacity_maH,
>	m.screen_size_inches,
>	m.launch_price_usd AS lowest_price
>FROM mobile_table m
>WHERE launch_price_usd = (
>	SELECT MIN(launch_price_usd)
>	FROM mobile_table
>	WHERE company_name = m.company_name)
>ORDER BY m.launch_price_usd
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/ad725245-e344-4536-96c3-34fa578979e7)
>
>**Data Visualization**
>
>![image](https://github.com/user-attachments/assets/7c8dbd74-481f-4e11-9dce-1256a3bd8c49)

### 10. Cheapest Mobile Phone Per Processor Type
>```sql
>WITH avg_temp AS(
>	SELECT 
>		processor,
>		AVG(launch_price_usd) AS avg_price
>	FROM mobile_table
>	GROUP BY processor
>)
>SELECT 
>	mt.processor, 
>	av.avg_price AS avg_price_per_processor,
>	mt.model_name AS cheapest_model_per_processor,
>	mt.launch_price_usd AS cheapest_price_per_processor
>FROM mobile_table mt
>INNER JOIN avg_temp AS av ON av.processor = mt.processor
>WHERE mt.launch_price_usd = (
>    SELECT MIN(launch_price_usd) 
>    FROM mobile_table 
>    WHERE processor = mt.processor
>)
>```
>**Data Result Sample**
>
>![image](https://github.com/user-attachments/assets/48f9877c-4218-4557-bc1c-53aaeb31bab8)
>
>**Visualization**
>
>




