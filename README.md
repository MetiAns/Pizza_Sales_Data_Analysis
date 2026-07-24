# Pizza Sales Analysis — SQL & Power BI
A comprehensive data analysis project focused on uncovering key performance indicators (KPIs) and sales trends for a pizza restaurant. The project transitions raw transactional data into actionable insights through robust **SQL querying** and dynamic **Power BI dashboards**.

## Problem Statement

The goal was to answer two sets of questions:

**KPIs**
- Total Revenue
- Average Order Value
- Total Pizzas Sold
- Total Orders
- Average Pizzas per Order


**Trends & breakdowns**
- Daily and monthly order trends
- Sales share by pizza category and by size
- Total pizzas sold per category
- Top 5 / Bottom 5 pizzas by revenue, quantity, and orders


- ## Tools

- SQL (MySQL) for querying and aggregating the raw data
- Power BI for the dashboard


## SQL Queries

All queries run against a single `pizza_sales` table. Grouped by the questions above:

**KPIs**
```sql
SELECT SUM(total_price) AS total_revenue
FROM pizza_sales;

SELECT SUM(total_price) / COUNT(DISTINCT order_id) AS avg_order_value
FROM pizza_sales;

SELECT SUM(quantity) AS total_pizza_sold
FROM pizza_sales;

SELECT COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales;

SELECT CAST(SUM(quantity) / COUNT(DISTINCT order_id) AS DECIMAL(10,2)) AS avg_pizza_per_order
FROM pizza_sales;
```


**Daily & monthly trends**
```sql
SELECT DAYNAME(STR_TO_DATE(order_date, '%d-%m-%Y')) AS order_day,
       COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY order_day;

SELECT MONTHNAME(STR_TO_DATE(order_date, '%d-%m-%Y')) AS order_month,
       COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY order_month;
```


**Category & size breakdown**
```sql
SELECT pizza_category, ROUND(SUM(total_price), 1) AS total_revenue,
       ROUND(SUM(total_price) * 100.0 / (SELECT SUM(total_price) FROM pizza_sales), 1) AS pct
FROM pizza_sales
GROUP BY pizza_category;

SELECT pizza_size, ROUND(SUM(total_price), 1) AS total_revenue,
       ROUND(SUM(total_price) * 100.0 / (SELECT SUM(total_price) FROM pizza_sales), 1) AS pct
FROM pizza_sales
GROUP BY pizza_size
ORDER BY pct DESC;

SELECT pizza_category, SUM(quantity) AS total_quantity_sold
FROM pizza_sales
GROUP BY pizza_category;
```


**Top / bottom sellers**
```sql
-- Top 5 by revenue
SELECT pizza_name, SUM(total_price) AS total_revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue DESC
LIMIT 5;


-- Bottom 5 by revenue
SELECT pizza_name, ROUND(SUM(total_price), 2) AS total_revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue ASC
LIMIT 5;


-- Top 5 by quantity
SELECT pizza_name, SUM(quantity) AS total_quantity
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_quantity DESC
LIMIT 5;


-- Top 5 by total orders
SELECT pizza_name, COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_orders DESC
LIMIT 5;


-- Bottom 5 by total orders
SELECT pizza_name, COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_orders ASC
LIMIT 5;
```


## Dashboard

**KPI overview & top/bottom sellers**
![Top and bottom sellers]()

**Trends & category/size breakdown**
![Trends and category breakdown]()


## Key Insights

- **817.86K** total revenue from **21.35K** orders (**50K** pizzas sold), averaging **$38.31** per order.
- **Friday** is the busiest day (3.5K orders); **Monday** is the slowest (2.8K).
- **The Thai Chicken** and **Barbecue Chicken** pizzas are the top revenue drivers (43K each), while **The Brie Carre Pizza** consistently sits at the bottom across revenue, quantity, and orders.
- **Classic** is the leading category (26.9% of sales), with Chicken, Supreme, and Veggie close behind — no single category dominates.
- **Medium and Large** sizes together make up over 75% of sales; XL is negligible (1.7%).


## How to Reproduce

1. Load the pizza sales CSV into a MySQL table named `pizza_sales`.
2. Run the queries above.
3. Connect Power BI to the same table (or import the query results) and rebuild the visuals.










