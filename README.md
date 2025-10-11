# TARGET--SQLPORTFOLIOPROJECT
This is a project that I followed from a youtube channel where I learned more about how to do data analysis
-- Import the dataset and do usual exploratory analysis steps like checking the
-- structure & characteristics of the dataset:
-- 1. Data type of all columns in the "customers" table.
-- 2. Get the time range between which the orders were placed.
-- 3. Display the Cities & States of customers who ordered during the given
-- period
SELECT * FROM TARGET_SQL.customers LIMIT 10;

SELECT * FROM `TARGET_SQL.geolocation` LIMIT 10;

-- 2. Get the time range between which the orders were placed.
SELECT MIN(order_purchase_timestamp) as start_time,
MAX(order_purchase_timestamp) as end_time FROM `TARGET_SQL.orders`;

-- 3. Display Cities & States of customers who ordered during the given
-- period
SELECT c.customer_city, c.customer_state FROM `TARGET_SQL.orders` as o JOIN `TARGET_SQL.customers` as c ON o.customer_id = c.customer_id WHERE EXTRACT(YEAR FROM o.order_purchase_timestamp) = 2018 AND EXTRACT(MONTH FROM o.order_purchase_timestamp) BETWEEN 1 AND 3;
