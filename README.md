# Pizza Runner :  B.Runner and Customer Experience

## Prerequesite - Cleaning Runner Orders Table

```sql
--cancellation column

UPDATE runner_orders 
SET cancellation = null 
WHERe cancellation = 'null' OR  TRIM(cancellation) = '' OR 
cancellation = 'NaN' ;

--pickup_time
UPDATE runner_orders 
SET pickup_time = null 
WHERe pickup_time = 'null' OR  TRIM(pickup_time) = '' OR 
pickup_time = 'NaN' ;

--distance
UPDATE runner_orders 
SET distance = null 
WHERe distance = 'null' OR  TRIM(distance) = '' OR 
distance = 'NaN' ;
--duration 
UPDATE runner_orders 
SET duration = null 
WHERe duration = 'null' OR  TRIM(duration) = '' OR 
duration = 'NaN' ;

-- removing km and mins from distance and duration columns

UPDATE runner_orders
SET distance = REGEXP_REPLACE( distance, '[^0-9.]', '', 'g' ) ;


UPDATE runner_orders
SET duration = REGEXP_REPLACE (duration , '[^0-9.]','','g');


-- Changing the data types of pickup_time, distance and duration columns

ALTER TABLE runner_orders
ALTER COLUMN distance TYPE decimal USING distance :: decimal; 

ALTER TABLE runner_orders
ALTER duration TYPE integer USING duration ::integer;

ALTER TABLE runner_orders
ALTER pickup_time TYPE timestamp USING  pickup_time :: timestamp; 


SELECT * FROM runner_orders ORDER BY order_id ;

```

---

**Question 2:** What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

---

## SQL Code

```sql

-- epoch is a fixed starting point in time.For PostgreSQL (and most systems), the epoch is: 1970-01-01 00:00:00 UTC .This is called the Unix epoch.
WITH customer_orders_runner_orders AS 
(
SELECT co.order_id,customer_id,pn.pizza_id,pizza_name, exclusions,extras,order_time,runner_id,pickup_time,distance,duration,cancellation FROM customer_orders co LEFT JOIN runner_orders ro ON
co.order_id = ro.order_id 
LEFT JOIN pizza_names pn ON
co.pizza_id = pn.pizza_id
ORDER BY co.order_id
)
SELECT runner_id, 
FLOOR (AVG(EXTRACT(EPOCH FROM (pickup_time - order_time) / 60) ))
AS time_taken_to_arrive_at_pizza_hq 
FROM customer_orders_runner_orders
GROUP BY runner_id
ORDER BY runner_id

```

<img width="1248" height="226" alt="image" src="https://github.com/user-attachments/assets/cadaa3a6-2f54-4b0f-952a-1984c0cf04bc" />
