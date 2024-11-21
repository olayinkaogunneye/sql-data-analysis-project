# :pizza: Case Study #2: Pizza runner - Pizza Metrics
### Q1. How many pizzas were ordered?

```sql
SELECT count(pizza_id) AS "Total Number Of Pizzas Ordered"
FROM customer_orders_temp;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606099-9ea969f1-928e-4bbd-90cd-5211aaed7e89.png)

***

###  Q2. How many unique customer orders were made?

```sql
SELECT 
  COUNT(DISTINCT order_id) AS 'Number Of Unique Orders'
FROM customer_orders_temp;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606186-2b5465ef-69df-4fbb-9a2d-cd50afd49c7a.png)

###  Q3. How many successful orders were delivered by each runner?

```sql
SELECT runner_id,
       count(order_id) AS 'Number Of Successful Orders'
FROM pizza_runner.runner_orders_temp
WHERE cancellation IS NULL
GROUP BY runner_id;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606290-b70ee6e3-ed23-417a-9e86-e8555d9e55c3.png)

***

###  Q4. How many of each type of pizza was delivered?

```sql

SELECT pizza_id,
       pizza_name,
       count(pizza_id) AS 'Number Of Pizzas Delivered'
FROM pizza_runner.runner_orders_temp
INNER JOIN customer_orders_temp USING (order_id)
INNER JOIN pizza_names USING (pizza_id)
WHERE cancellation IS NULL
GROUP BY pizza_id;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606389-9128a4e0-90e9-467b-a593-c18c62ca007e.png)

***

###  Q5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT customer_id,
       pizza_name,
       count(pizza_id) AS 'Number Of Pizzas Ordered'
FROM customer_orders_temp
INNER JOIN pizza_names USING (pizza_id)
GROUP BY customer_id,
         pizza_id
ORDER BY customer_id ;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606480-326c416f-a909-49e8-8bda-8055ee247fd1.png)

- The counts of the Meat lover and Vegetarian pizzas ordered by the customers is not discernible.

```sql
SELECT customer_id,
       SUM(CASE
               WHEN pizza_id = 1 THEN 1
               ELSE 0
           END) AS 'Meat lover Pizza Count',
       SUM(CASE
               WHEN pizza_id = 2 THEN 1
               ELSE 0
           END) AS 'Vegetarian Pizza Count'
FROM customer_orders_temp
GROUP BY customer_id
ORDER BY customer_id;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164606848-8980ebb9-a8e5-4b2b-a612-b86b19f4df08.png)

***


###  Q6. What was the maximum number of pizzas delivered in a single order?

```sql
SELECT customer_id,
       order_id,
       count(order_id) AS pizza_count
FROM customer_orders_temp
GROUP BY order_id
ORDER BY pizza_count DESC
LIMIT 1;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164608353-a577858f-1d1c-46ed-b1f2-05644b756604.png)

***
###  Q7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
- at least 1 change -> either exclusion or extras 
- no changes -> exclusion and extras are NULL

```sql
SELECT customer_id,
       SUM(CASE
               WHEN (exclusions IS NOT NULL
                     OR extras IS NOT NULL) THEN 1
               ELSE 0
           END) AS change_in_pizza,
       SUM(CASE
               WHEN (exclusions IS NULL
                     AND extras IS NULL) THEN 1
               ELSE 0
           END) AS no_change_in_pizza
FROM customer_orders_temp
INNER JOIN runner_orders_temp USING (order_id)
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY customer_id;
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164609444-9b7453ed-2477-4ce0-b7f7-39768a0ce808.png)

###  Q8. How many pizzas were delivered that had both exclusions and extras?

```sql

SELECT customer_id,
       SUM(CASE
               WHEN (exclusions IS NOT NULL
                     AND extras IS NOT NULL) THEN 1
               ELSE 0
           END) AS both_change_in_pizza
FROM customer_orders_temp
INNER JOIN runner_orders_temp USING (order_id)
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY customer_id;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/77529445/164609941-c2a6f1f8-38c2-4e1c-ab64-a9dd557077e5.png)

###  Q9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT
    EXTRACT(hour FROM order_time) AS hour_of_day,
    COUNT(order_id) AS num_of_order
FROM pizza_runner.customer_orders
GROUP BY 1;

```
![image](https://user-images.githubusercontent.com/120476961/226347241-8a89fca2-c4be-420c-a389-16d776807c1c.png)

###  Q10. What was the volume of orders for each day of the week?

```sql
SELECT
    TO_CHAR(order_time, 'Day') AS day,
    EXTRACT(dow FROM order_time) AS week_day,
    COUNT(order_id) AS num_of_orders
FROM pizza_runner.customer_orders
GROUP BY 1,2
ORDER BY 3 DESC;

| day       | num_of_orders |
| --------- | ------------- |
| Saturday  | 5             |
| Wednesday | 5             |
| Thursday  | 3             |
| Friday    | 1             |


