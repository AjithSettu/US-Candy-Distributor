# US Candy Store Data Analysis

## Project Overview
This project analyzes sales and product data from the fictional "US Candy Store." The goal is to extract actionable insights regarding customer behavior, product performance, sales trends, and overall business efficiency. Advanced SQL techniques such as window functions, CTEs, and joins are used to perform the analysis.

---

## Dataset Description

### Sales Table
- **Order_ID**: Unique identifier for each order.
- **Customer_ID**: Unique identifier for each customer.
- **Product_ID**: Unique identifier for each product.
- **Order_Date**: Date of the order.
- **Sales**: Sales amount for each order.
- **Gross_Profit**: Profit for each order.
- **Division**: Product category or segment.
- **Ship_Mode**: Mode of shipment.

### Products Table
- **Product_ID**: Unique identifier for each product.
- **Product_Name**: Name of the product.
- **Factory**: Factory producing the product.

---

## Objectives

1. **Data Cleaning**
   - Identify and handle null values dynamically.
2. **Basic Analysis**
   - Calculate unique customers, products, and shipping modes.
   - Identify top customers and products.
3. **Advanced Analysis**
   - Sales trends by year, month, and division.
   - Rank products and analyze growth trends.
4. **Joins and Relationships**
   - Combine data from multiple tables to derive insights.

---

## SQL Techniques Used

### Data Cleaning
- Dynamic null value checks using `INFORMATION_SCHEMA.COLUMNS`.

### Basic Queries
- Aggregate functions: `COUNT`, `SUM`, `AVG`.
- Sorting and grouping with `GROUP BY` and `ORDER BY`.

### Advanced Analysis
- **Window Functions**:
  - Ranking: `RANK`, `DENSE_RANK`.
  - Cumulative totals.
  - Percentage contributions.
  - Growth analysis with `LAG` and `LEAD`.
- **CTEs**:
  - Simplify complex queries with reusable temporary tables.

### Joins
- Combine product and sales data to analyze relationships.

---

## Key Insights

1. **Top Customers**: Customers with the highest number of orders and total sales.
2. **Top Products**: Best-performing products by sales and profitability.
3. **Regional Analysis**: Sales trends across different divisions.
4. **Sales Trends**: Insights into monthly and yearly performance.
5. **Growth Analysis**: Trends in sales and profit growth or decline.

---

## How to Run

1. **Set Up the Database**:
   - Create a database: `US Candy Store`.
   - Import the data into `Products` and `Sales` tables.

2. **Execute Queries**:
   - Use a SQL editor (e.g., SQL Server Management Studio, MySQL Workbench) to run the provided SQL script.

3. **Requirements**:
   - SQL Server, MySQL, or a compatible RDBMS.

---

## Sample Queries

### Check for Null Values
```sql
DECLARE @TableName NVARCHAR(128) = 'Sales';
DECLARE @SQL NVARCHAR(MAX) = '';

SELECT @SQL = STRING_AGG(
    'SELECT ''' + COLUMN_NAME + ''' AS ColumnName, COUNT(*) AS NullCount FROM ' + QUOTENAME(@TableName) + ' WHERE ' + QUOTENAME(COLUMN_NAME) + ' IS NULL',
    ' UNION ALL '
)
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = @TableName;

EXEC sp_executesql @SQL;
```

### Top 5 Products by Profit
```sql
SELECT TOP 5 Product_Name, SUM(Gross_Profit) AS [Total Profit] 
FROM Sales 
GROUP BY Product_Name 
ORDER BY [Total Profit] DESC;
```

### CTE for Total Sales and Profit
```sql
WITH CTE_1 AS (
    SELECT 
        Product_Name, 
        SUM(Sales) AS TotalSales, 
        SUM(Gross_Profit) AS TotalProfit 
    FROM Sales 
    GROUP BY Product_Name
)
SELECT Product_Name, TotalSales, TotalProfit 
FROM CTE_1 
ORDER BY TotalProfit DESC;
```

---

## Conclusion
This project demonstrates the power of SQL in cleaning, analyzing(trends for sales), and extracting insights from business data. It provides a foundation for advanced analytical techniques and decision-making processes in the retail sector.

