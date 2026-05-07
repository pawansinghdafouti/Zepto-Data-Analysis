# 🛒 Zepto E-commerce SQL Data Analyst Portfolio Project

This is a complete real-world SQL Data Analyst portfolio project based on an e-commerce inventory dataset scraped from [Zepto](https://www.zeptonow.com/?utm_source=chatgpt.com) — one of India’s fastest-growing quick-commerce startups.
The project demonstrates practical SQL workflows used by data analysts, including database setup, data cleaning, exploratory data analysis (EDA), and business insight generation.

This project is ideal for:

* 📊 Data Analyst aspirants building portfolio projects for interviews and LinkedIn
* 📚 Beginners learning SQL through hands-on practice
* 💼 Students preparing for retail, e-commerce, or analytics interviews

# 📌 Project Overview

The main objective of this project is to simulate how data analysts work with raw e-commerce inventory datasets using SQL.

The project covers:

✅ Creating and managing a real-world inventory database

✅ Performing Exploratory Data Analysis (EDA)

✅ Cleaning inconsistent and invalid data

✅ Generating business insights related to pricing, discounts, inventory, and revenue

---

# 📁 Dataset Overview

The dataset was sourced from [Kaggle Zepto Inventory Dataset](https://www.kaggle.com/datasets/palvinder2006/zepto-inventory-dataset/data?select=zepto_v2.csv&utm_source=chatgpt.com) and contains product listings scraped from Zepto’s inventory catalog.

Each row represents a unique SKU (Stock Keeping Unit).
Duplicate product names exist because products may appear in different quantities, package sizes, or pricing combinations.

## Dataset Columns

* **sku_id** → Unique identifier for each product
* **name** → Product name
* **category** → Product category
* **mrp** → Maximum Retail Price
* **discountPercent** → Discount percentage on product
* **discountedSellingPrice** → Final discounted price
* **availableQuantity** → Units available in stock
* **weightInGms** → Product weight in grams
* **outOfStock** → Product stock availability status
* **quantity** → Quantity per package

---

# 🔧 Database Setup

## Create Table

```sql
DROP TABLE IF EXISTS zepto;

CREATE TABLE zepto(
  sku_id SERIAL PRIMARY KEY,
  category VARCHAR(120),
  name VARCHAR(150) NOT NULL,
  mrp NUMERIC(8,2),
  discountPercent NUMERIC(5,2),
  availableQuantity INTEGER,
  discountedSellingPrice NUMERIC(8,2),
  weightInGms INTEGER,
  outOfStock BOOLEAN,
  quantity INTEGER
);
```

---

# 📥 Data Import

You can import the CSV file using PostgreSQL pgAdmin import functionality.

If the import option does not work, use:

```sql
\copy zepto(category,name,mrp,discountPercent,availableQuantity,
discountedSellingPrice,weightInGms,outOfStock,quantity)
FROM 'data/zepto_v2.csv'
WITH (
FORMAT csv,
HEADER true,
DELIMITER ',',
QUOTE '"',
ENCODING 'UTF8'
);
```

### ⚠️ UTF-8 Issue

While importing the dataset, encoding issues may occur.
This can be fixed by saving the CSV file in **CSV UTF-8** format before importing.

---

# 🔍 Exploratory Data Analysis (EDA)

## 1. Count Total Rows

```sql
SELECT COUNT(*) FROM zepto;
```

---

## 2. View Sample Data

```sql
SELECT *
FROM zepto
LIMIT 20;
```

---

## 3. Check for Null Values

```sql
SELECT *
FROM zepto
WHERE name IS NULL
OR category IS NULL
OR mrp IS NULL
OR discountPercent IS NULL
OR discountedSellingPrice IS NULL
OR weightInGms IS NULL
OR availableQuantity IS NULL
OR outOfStock IS NULL
OR quantity IS NULL;
```

---

## 4. Find Distinct Product Categories

```sql
SELECT DISTINCT category
FROM zepto
ORDER BY category;
```

---

## 5. Count In-Stock vs Out-of-Stock Products

```sql
SELECT outOfStock,
COUNT(sku_id)
FROM zepto
GROUP BY outOfStock;
```

---

## 6. Find Products Listed Multiple Times

```sql
SELECT name,
COUNT(sku_id) AS "number of skus"
FROM zepto
GROUP BY name
HAVING COUNT(sku_id) > 1
ORDER BY COUNT(sku_id) ASC;
```

---

# 🧹 Data Cleaning

## 1. Find Products with Invalid Pricing

```sql
SELECT *
FROM zepto
WHERE mrp = 0
OR discountedSellingPrice = 0;
```

---

## 2. Remove Invalid Records

```sql
DELETE FROM zepto
WHERE mrp = 0;
```

---

## 3. Verify Cleaned Data

```sql
SELECT *
FROM zepto
WHERE mrp = 0
OR discountedSellingPrice = 0;
```

---

## 4. Convert Prices from Paise to Rupees

```sql
UPDATE zepto
SET mrp = mrp / 100.0,
discountedSellingPrice = discountedSellingPrice / 100.0;
```

---

## 5. Verify Updated Prices

```sql
SELECT mrp,
discountedSellingPrice
FROM zepto;
```

---

# 📊 Business Analysis Queries

## Q1. Top 10 Best-Value Products Based on Discount Percentage

```sql
SELECT DISTINCT name,
mrp,
discountPercent
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;
```

---

## Q2. High MRP Products Currently Out of Stock

```sql
SELECT DISTINCT name,
mrp
FROM zepto
WHERE outOfStock = TRUE
AND mrp > 300
ORDER BY mrp DESC;
```

---

## Q3. Estimated Revenue by Category

```sql
SELECT category,
SUM(discountedSellingPrice * availableQuantity) AS total_revenue
FROM zepto
GROUP BY category
ORDER BY total_revenue;
```

---

## Q4. Expensive Products with Low Discount

```sql
SELECT DISTINCT name,
mrp,
discountPercent
FROM zepto
WHERE mrp > 500
AND discountPercent < 10
ORDER BY mrp DESC,
discountPercent DESC;
```

---

## Q5. Top 5 Categories with Highest Average Discount

```sql
SELECT category,
ROUND(AVG(discountPercent),2) AS avg_discount
FROM zepto
GROUP BY category
ORDER BY avg_discount DESC
LIMIT 5;
```

---

## Q6. Find Best Value Products by Price Per Gram

```sql
SELECT DISTINCT name,
weightInGms,
discountedSellingPrice,
ROUND(discountedSellingPrice / weightInGms, 2) AS price_per_gram
FROM zepto
WHERE weightInGms >= 100
ORDER BY price_per_gram;
```

---

## Q7. Categorize Products by Weight

```sql
SELECT DISTINCT name,
weightInGms,
CASE
    WHEN weightInGms < 1000 THEN 'Low'
    WHEN weightInGms < 5000 THEN 'Medium'
    ELSE 'Bulk'
END AS weight_category
FROM zepto;
```

---

## Q8. Total Inventory Weight Per Category

```sql
SELECT category,
SUM(weightInGms * availableQuantity) AS total_weight
FROM zepto
GROUP BY category
ORDER BY total_weight;
```

---

# 🛠️ How to Run This Project

## Step 1 — Clone Repository

```bash
git clone https://github.com/amlanmohanty/zepto-SQL-data-analysis-project.git
cd zepto-SQL-data-analysis-project
```

---

## Step 2 — Open SQL File

The SQL file includes:

* Database creation
* Data exploration
* Data cleaning
* Business analysis queries

---

## Step 3 — Import Dataset into PostgreSQL

* Create a PostgreSQL database
* Run the SQL script
* Import the dataset CSV file
* Ensure file encoding is UTF-8

---

## Step 4 — Execute Queries

Run all SQL queries step-by-step to perform EDA, cleaning, and analysis.

---

# 📌 Skills Demonstrated

* SQL Query Writing
* Data Cleaning
* Exploratory Data Analysis (EDA)
* Aggregate Functions
* Filtering & Sorting
* CASE Statements
* GROUP BY & HAVING
* Business Insight Generation
* PostgreSQL

---

# 👨‍💻 About the Project

This project demonstrates practical SQL skills required in real-world Data Analyst roles.
It can be added directly to your:

* Resume
* LinkedIn Projects
* GitHub Portfolio

It is especially useful for freshers preparing for:

* Data Analyst roles
* Business Analyst interviews
* SQL technical rounds
* Retail & E-commerce analytics job
