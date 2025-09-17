# SQL Retail Sales Analysis
This project is an end-to-end analysis of retail sales data using SQL to answer key business questions. The process included setting up a database, performing exploratory data analysis (EDA), and writing complex queries to extract actionable insights on sales performance and customer trends. It demonstrates core data analysis skills in data cleaning, aggregation, and analytical querying within a SQL environment.

# Data Exploration & Cleaning
This initial phase focuses on data exploration and cleaning to ensure the dataset is ready for analysis. The first step was to profile the data to understand its basic dimensions, including counting the total number of sales records, determining the number of unique customers, and listing all distinct product categories.

Following this exploration, a query was run to check for data integrity by identifying any rows with missing values in critical columns. Since these incomplete records could skew the analysis, they were subsequently removed from the table to maintain data quality for the upcoming analysis.

# Data Analysis & Findings

With the data cleaned, this section uses SQL to answer specific business questions and uncover key insights. The analysis covers sales performance, customer behavior, and product trends through a series of targeted queries.

The investigation delves into several key areas:

Sales Performance: Queries calculate the total sales for each product category and identify high-value transactions. A more advanced query using a window function (RANK()) was built to determine the month with the highest average sales for each year.

Customer Behavior: The analysis identifies the top 5 customers by total spending and calculates the number of unique customers for each category. It also explores purchasing patterns across different times of the day (Morning, Afternoon, Evening), which was achieved using a Common Table Expression (CTE) and a CASE statement.

Product & Demographics: Queries were used to explore demographic insights, such as the average age of customers for the 'Beauty' category and a breakdown of transactions by gender within each product line.

These queries showcase a range of SQL techniques—from basic aggregations (SUM, AVG, COUNT) to more complex operations like CTEs and window functions—to extract meaningful business intelligence from raw data.

### 01. Sales made on '2022-11-05

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/01.jpg)

### 02. Retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022

```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/02.jpg)

### 03. Calculate the total sales (total_sale) for each category

```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/03.jpg)

### 04. Find the average age of customers who purchased items from the 'Beauty' category

```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/04.jpg)

### 05. Find all transactions where the total_sale is greater than 1000

```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/05.jpg)

### 06. Find the total number of transactions (transaction_id) made by each gender in each category

```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/06.jpg)

### 07. Calculate the average sale for each month. Find out best selling month in each year

```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/07.jpg)

### 08. Find the top 5 customers based on the highest total sales

```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/08.jpg)

### 09. Find the number of unique customers who purchased items from each category

```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/09.jpg)

### 10. Create each shift and number of orders (Morning <12, Afternoon Between 12 & 17, Evening >17)

```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```

![image alt](https://github.com/maraperera/SQL_Retail_Sales_Analysis/blob/13131d5fd3f045a532661a8011784b37435da0a5/results/10.jpg)
