# US Candy Store Data Analysis

## Project Overview
This project focuses on analyzing sales and product data for the fictional "US Candy Store" to gain insights into customer behavior, product performance, and sales trends. The project utilizes SQL queries for data cleaning, aggregation, and advanced analysis using window functions, joins, and Common Table Expressions (CTEs).

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
1. **Data Cleaning**:
   - Identify and handle null values in the dataset dynamically.
2. **Basic Analysis**:
   - Count unique customers, products, and shipping modes.
   - Identify top customers and products.
3. **Advanced Analysis**:
   - Analyze sales trends by year, month, and division.
   - Rank products and analyze growth trends.
4. **Joins and Relationships**:
   - Combine data from multiple tables to extract comprehensive insights.

---

## SQL Techniques Used

### Data Cleaning
- Dynamically checking for null values using `INFORMATION_SCHEMA.COLUMNS`.

### Basic Queries
- Aggregate functions such as `COUNT`, `SUM`, and `AVG`.
- Grouping and sorting data using `GROUP BY` and `ORDER BY`.

### Advanced Analysis
- **Window Functions**:
  - Ranking: `RANK` and `DENSE_RANK`.
  - Cumulative totals and percentage contributions.
  - Growth analysis with `LAG` and `LEAD`.
- **CTEs (Common Table Expressions)**:
  - Simplify complex queries with reusable temporary tables.

### Joins
- Combine data from `Sales` and `Products` tables to analyze relationships.

---

## Key Insights
1. **Top Customers**: Identify customers with the highest number of orders and total sales.
2. **Top Products**: Highlight best-performing products by sales and profitability.
3. **Regional Analysis**: Analyze sales trends across different divisions.
4. **Sales Trends**: Track monthly and yearly sales performance.
5. **Growth Analysis**: Detect growth or decline in sales and profit for products.

---

## How to Run

1. **Set Up the Database**:
   - Create the database: `US Candy Store`.
   - Import the data into `Products` and `Sales` tables.

2. **Execute Queries**:
   - Use a SQL editor (e.g., SQL Server Management Studio, MySQL Workbench) to run the provided SQL script.

3. **Requirements**:
   - A compatible RDBMS such as SQL Server or MySQL.

---

## Full Code

```sql
create database [US Candy Store]
use [US Candy Store]

select * from products
select * from sales

-- DATA CLEANING

--Check for Null Values

-- Constructing the SQL query dynamically
declare @TableName NVARCHAR(128) = 'Sales'
declare @SQL NVARCHAR(MAX) = ''

select @SQL = String_agg(
    'select ''' + COLUMN_NAME + ''' AS ColumnName, Count(*) AS NullCount From ' + Quotename(@TableName) + ' where ' + Quotename(Column_Name) + ' Is Null',
    ' Union All '
)
From INFORMATION_SCHEMA.COLUMNS
where Table_Name = @TableName

-- Execute the constructed SQL query
Exec sp_executesql @SQL

--Checking the PRODUCTS TABLE for Null Values
declare @TableName1 NVARCHAR(128) = 'Products'
declare @SQL1 NVARCHAR(MAX) = ''

-- Constructing the SQL query dynamically
select @SQL1 = STRING_AGG(
	'select''' + COLUMN_NAME + ''' As ColumnName, Count(*) AS NullCount From ' + QUOTENAME(@TableName1) + 'Where ' +QUOTENAME(Column_Name) + 'Is Null',
	' Union All '
)
From INFORMATION_SCHEMA.COLUMNS
where TABLE_NAME = @TableName1

-- Execute the constructed SQL query
Exec sp_executesql @SQL1


--Basic SQL & Data Manipulation

--The number of unique customers
select count(distinct Customer_id) as [Number of customer_ID] from Sales

--The number of shipping mode
select distinct Ship_mode from sales

--The number of divisions
select distinct Division from Sales

--The number of distinct products
select count(distinct Product_Name) as [Number of Distinct Product Names] from sales

--Top 10 Customers by number of orders
select top 10 Customer_id,count(order_id) as [Number of Orders] from sales
group by customer_id
order by [Number of Orders] desc

--The division which has more sales
select top 1 Division,sum(Sales) as [Sum of Sales Division] from sales
group by Division 


--The total sales for each Customer_ID
select Customer_ID, sum(Sales) as [Sum of Sales] from Sales
group by Customer_ID

--Top 5 average sales per customer ID
select top 5 Customer_ID, AVG(Sales) as [Average Sales]
from Sales
group by Customer_ID

--Data Aggregation & Grouping

--To get name of the factories by Product_Name
select Product_Name,Factory from Products

--The total sales for each year
select Year(Order_Date) as [Year], sum(Sales) as [Total Sales] from Sales
group by year(Order_Date)
order by year(Order_Date) desc

--The month with the highest sales
select month(Order_Date) as [Month], sum(Sales) as [Total Sales] from Sales
group by month(Order_Date)
order by month(Order_Date) desc

--Top 3 customers with the highest total sales
select top 3 Customer_ID,sum(Sales) as [Total Sales] from Sales
group by Customer_ID
order by [Total Sales]desc

--Number of orders placed by each customer
select Customer_ID,count(Order_ID) as [Number of Orders]
from sales
group by Customer_ID
order by [Number of Orders] desc

--Average order value for each product category
select Division, AVG(Sales) as [Average of Sales] from Sales
group by Division

--The customers who have placed more than 10 orders
select Customer_ID,count(Order_ID) as [Number of Orders]
from sales
group by Customer_ID
having count(Order_ID) >10
order by [Number of Orders] desc

--Top 5 products that were sold by order count
select Product_Name,count(Order_ID) as [Orders Count] from Sales
group by Product_Name
having count(Order_ID)>1
order by [Orders Count] desc

--Top 5 products by profit
select top 5 Product_Name,sum(Gross_Profit) as [Total Profit] from Sales
group by Product_Name
order by [Total Profit] desc


--Data Joins & Relationships

--Find the customers who have purchased a specific product
select distinct Customer_ID, Product_Name
from sales
where
Product_Name = 'Wonka Bar - Fudge Mallows'

--Find the number customers who have purchased a specific product
select count(Customer_id) as [Number of Customers]
from sales
where Product_Name = 'Laffy Taffy'

--Using Windows Functions

--Ranking products by sales
select Product_Name,sales, 
rank()over(order by sales desc) as SalesRank from Sales

--OR

--Using Dense Rank Function
select Product_Name,sales, 
dense_rank()over(order by sales desc) as SalesRank from Sales

--Cumulative sales per division
select Division,Product_Name,sales,sum(sales) 
over (partition by division order by Product_Name) as Cumulative_Sales
from Sales

--Percentage contribution of sales by division
select Division,Sales,Sales*100/sum(Sales) over() as Sales_Percentage
from Sales
order by Sales_Percentage asc

--Average Sales by City
select City,Product_Name,Sales, avg(Sales)over (Partition by city) as Average_Sales
from Sales

--Using Join function for Analysis

--Number of Products Per Factory
select Factory,count(Product_id) as Product_Count from
(select Products.Factory,Sales.Product_ID 
from sales
join Products
on Products.Product_ID = Sales.Product_ID)x
group by Factory
order by Product_Count desc


--Total Sales Per Factory
select Factory,sum(sales) as Total_Sales
from Sales
join Products on sales.product_id = Products.Product_ID
group by factory


--Using Windows function for Analysis

--Finding Top Products in Each Division
select*from(select Division,Product_Name,Sales,
rank()over(Partition by division order by sales desc)as rank_D from Sales)x
where rank_D<=3

--Rank Products Within each division
select Division,Product_Name,Sales, 
dense_rank()over(Partition by division order by sales desc) as Rank_D
from sales

--Find Top 2 products in each division
select * from(
select Division,Product_Name,Sales, 
dense_rank()over(Partition by division order by sales desc) as Rank_D
from sales)x
where Rank_D <= 2

--Identify Trend in sales for Each Division
select	Division,
		Product_Name,
		Sales,
lag(Sales)over(Partition by division order by sales desc) as PreviousSales,
lead(Sales)over(Partition by division order by sales desc) as NextSales
from Sales


--To find Sales growth or decline
select Product_Name,sales,
lag(Sales) over(Order by sales desc) as PreviousSales,
lead(Sales) over(order by sales desc) as NextSales,
case
	when sales>lag(Sales) over (order by sales desc) then 'Growth'
	when sales<lag(Sales) over (Order by sales desc) then 'Decline'
	else
	'No Change'
	end as Trend
from sales


--To find profit growth or decline
select Product_Name,Gross_Profit,
lag(Gross_Profit) over(Order by Gross_Profit desc) as PreviousSales,
lead(Gross_Profit) over(order by Gross_Profit desc) as NextSales,
case
	when Gross_Profit>lag(Gross_Profit) over (order by Gross_Profit desc) then 'Growth'
	when Gross_Profit<lag(Gross_Profit) over (Order by Gross_Profit desc) then 'Decline'
	else
	'No Change'
	end as Trend
from sales


--Using CTE Functions

--CTE to claculate total sales and profit
with CTE_1 as (
	select
		product_Name,
		sum(Sales) as TotalSales,
		sum(Gross_Profit) as TotalProfit
	from Sales
	Group by Product_Name
)
select
	Product_Name,
	TotalSales,
	TotalProfit
from CTE_1
order by TotalProfit desc


--CTE for Ranking Region by Sales
with RankbyRegion as (
	select
		Region,
		Sales,
		DENSE_RANK()over(order by sales desc) as SalesRank
	from Sales
)
select Region,
		Sales,
		SalesRank
from RankbyRegion
where SalesRank<=5 --will get top 5 products


--CTE for filtering Specific Products
with Product_CTE as (
	select
		Order_ID,
		Product_Name,
		Sales
	From Sales
	Where Product_Name = 'Wonka Bar - Nutty Crunch Surprise'
)
select
	Order_ID,
	Product_Name,
	Sales
from Product_CTE
order by Sales desc


--CTE for Repeated Analysis
with SalesCTE as(
	select
		Product_Name,
		sum(Sales) as TotalSales
	From Sales
	group by Product_Name
),
RankedSales as (
	select
		Product_Name,
		TotalSales,
		Rank()over(order by TotalSales Desc)
	as rank_1
		From SalesCTE
)
select
	Product_Name,
	TotalSales,
	rank_1
From RankedSales
where rank_1<=3
```

---

## Conclusion
This project demonstrates the power of SQL for data cleaning, analysis, and extracting actionable insights. It provides a robust foundation for advanced analytical techniques and decision-making processes in retail business contexts.

---
