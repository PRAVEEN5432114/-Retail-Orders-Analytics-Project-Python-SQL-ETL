📦 Retail Orders Analysis Project
A data analysis project using Python, SQL, and Powerful ETL techniques to extract insights from a retail orders dataset. This project focuses on sales trends, revenue patterns, product performance, and regional growth analysis.

📂 Dataset
Source: Kaggle – Retail Orders Dataset

File: orders.csv from the ZIP archive

License: CC0-1.0 (Open Data)

🧠 Key Skills & Concepts Used
ETL (Extract, Transform, Load)

Data Cleaning & Feature Engineering (Pandas)

SQL Analytics & Window Functions

Subqueries & CTEs (Common Table Expressions)

Data Loading to SQL Server with SQLAlchemy

API-based Dataset Download (Kaggle CLI)

⚙️ Installation & Setup
🐍 Python Requirements
pip install kaggle pandas sqlalchemy pyodbc

🛠️ Environment Variables Setup
Before using Kaggle CLI or connecting to a database, set the following securely:

# For Kaggle API (kaggle.json key setup)
# Store it at ~/.kaggle/kaggle.json or set manually if using scripts

# For SQL Server (use a config file or environment variables in practice)
import sqlalchemy as sal
engine = sal.create_engine(
    "mssql+pyodbc://<username>:<password>@<server_name>/<database_name>?driver=ODBC+Driver+17+for+SQL+Server"
)
conn = engine.connect()
⚠️ Note: Avoid hardcoding credentials — use .env files or secure storage in production.

🔁 ETL Process in Python
1.Download Dataset:

!kaggle datasets download ankitbansal06/retail-orders

2.Unzip and Load:

import zipfile
with zipfile.ZipFile("retail-orders.zip", 'r') as zip_ref:
    zip_ref.extractall("retail_orders_data")

import pandas as pd
df = pd.read_csv("retail_orders_data/orders.csv", na_values=["unknown", "Not Available"])

3.Transformations:

df.columns = df.columns.str.lower().str.replace(' ', '_')
df['discount'] = df['list_price'] * df['discount_percent'] * 0.01
df['sale_price'] = df['list_price'] - df['discount']
df['profit'] = df['sale_price'] - df['cost_price']
df['order_date'] = pd.to_datetime(df['order_date'])
df.drop(columns=['list_price', 'cost_price', 'discount_percent'], inplace=True)

4.Load to SQL Server:
df.to_sql('df_orders', con=conn, index=False, if_exists='append')

🧮 SQL Analysis Performed
✅ Sample Queries:
.Top 10 revenue-generating products

.Top 5 products by region (using RANK() window function)

.Month-over-month sales comparison for 2022 vs 2023

.Category-wise best-performing month

.Sub-category with highest growth (in percentage & value)

📊 Example:

WITH cte AS (
    SELECT YEAR(order_date) AS order_year, MONTH(order_date) AS order_month,
           SUM(sale_price) AS sales
    FROM df_orders
    GROUP BY YEAR(order_date), MONTH(order_date)
)
SELECT 
    order_month,
    SUM(CASE WHEN order_year = 2022 THEN sales ELSE 0 END) AS sales_2022,
    SUM(CASE WHEN order_year = 2023 THEN sales ELSE 0 END) AS sales_2023
FROM cte
GROUP BY order_month
ORDER BY order_month;

📌 Project Highlights
.Converted raw transactional data into a SQL-ready schema

.Created KPIs like sale_price, profit, discount

.Leveraged SQL analytics (window functions, CTEs) for business insights

.Modular ETL pipeline using Pandas + SQLAlchemy

📁 Folder Structure

📦 Retail Orders Project
├── retail_orders_data/

│   └── orders.csv

├── retail-orders.zip

├── etl_script.py

└── README.md


