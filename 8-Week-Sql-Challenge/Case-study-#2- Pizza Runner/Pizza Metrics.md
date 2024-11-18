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

###  3. How many successful orders were delivered by each runner?

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

###  4. How many of each type of pizza was delivered?

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
