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

-- 3. Evolution of E-commerce orders in the Brazil region:
-- 1. Get the month on month no. of orders placed in each state.


SELECT  EXTRACT(MONTH from order_purchase_timestamp)as month,
EXTRACT(YEAR from order_purchase_timestamp) as year,
COUNT(*) as num_orders,
 FROM `TARGET_SQL.orders`
 GROUP BY year,month
 ORDER BY year,month;
-- 2. How are the customers distributed across all the states?

SELECT customer_city,customer_state, 
COUNT(DISTINCT customer_id) as customer_count FROM `TARGET_SQL.customers`
GROUP BY customer_city,customer_state
ORDER BY customer_city,customer_count DESC;

-- 4. Impact on Economy: Analyze the money movement by e-commerce by looking
-- at order prices, freight and others.
-- 1. Get the % increase in the cost of orders from year 2017 to 2018
-- (include months between Jan to Aug only).
-- You can use the "payment_value" column in the payments table to get
-- the cost of orders.
With yearly_totals as (
SELECT EXTRACT(YEAR from o.order_purchase_timestamp) as year,
SUM(p.payment_value) as total_payment
 FROM `TARGET_SQL.payments` as p
  JOIN `TARGET_SQL.orders` as o
  ON p.order_id=o.order_id
  WHERE EXTRACT(YEAR from o.order_purchase_timestamp) in (2017,2018)
  AND EXTRACT(MONTH from o.order_purchase_timestamp) between 1 and 8
  GROUP BY EXTRACT(YEAR from o.order_purchase_timestamp)
),
yearly_comparisons AS (
SELECT year, total_payment, LEAD(total_payment) over (order by year desc) prev_year_payment
from yearly_totals
)
SELECT ROUND(((total_payment - prev_year_payment)/prev_year_payment) *100) FROM yearly_comparisons;
-- 2. Calculate the Total & Average value of order price for each state.
-- 3. Calculate the Total & Average value of order freight for each state.
SELECT c.customer_state,
AVG(price) as avg_price,
SUM(price) as sum_price,
AVG(freight_value) as avg_freight,
SUM(freight_value) as sum_freight
FROM `TARGET_SQL.orders` as o
JOIN `TARGET_SQL.order_items` as oi
ON o.order_id = oi.order_id
JOIN `TARGET_SQL.customers` as c
ON o.customer_id = c.customer_id
GROUP BY c.customer_state;
