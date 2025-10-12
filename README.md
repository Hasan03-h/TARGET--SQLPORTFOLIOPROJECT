# TARGET--SQLPORTFOLIOPROJECT
This is a project that I followed from a youtube channel where I learned more about how to do data analysis
\n
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


-- 2. In-depth Exploration:
--  1. Is there a growing trend in the no. of orders placed over the past years?
--  2. Can we see some kind of monthly seasonality in terms of the no. of
-- orders being placed?
--  3. During what time of the day, do the Brazilian customers mostly place
-- their orders? (Dawn, Morning, Afternoon or Night)
-- ■ 0-6 hrs : Dawn
-- ■ 7-12 hrs : Mornings
-- ■ 13-18 hrs : Afternoon
-- ■ 19-23 hrs : Night


-- 1. Is there a growing trend in the no. of orders placed over the past years?
--  2. Can we see some kind of monthly seasonality in terms of the no. of
-- orders being placed?
SELECT EXTRACT(Month from order_purchase_timestamp) as Month, COUNT(order_id) as order_num
 FROM `TARGET_SQL.orders`
 GROUP BY EXTRACT(MONTH from order_purchase_timestamp)
 ORDER BY order_num DESC;


--  3. During what time of the day, do the Brazilian customers mostly place
-- their orders? (Dawn, Morning, Afternoon or Night)
-- ■ 0-6 hrs : Dawn
-- ■ 7-12 hrs : Mornings
-- ■ 13-18 hrs : Afternoon
-- ■ 19-23 hrs : Night
SELECT EXTRACT(hour from order_purchase_timestamp) as time,
COUNT(order_id) as order_num FROM `TARGET_SQL.orders`
GROUP BY EXTRACT(hour from order_purchase_timestamp)
ORDER BY order_num desc;
-- Brazilians mostly buy in Afternoon and Mornings
