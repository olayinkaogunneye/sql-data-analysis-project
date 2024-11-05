## Solutions
### 1. What is the total amount each customer spent at the restaurant?

```sql

SELECT
    DISTINCT s.customer_id,
    SUM(m.price) AS total_amount
FROM dannys_diner.sales s
JOIN dannys_diner.menu m USING(product_id)
GROUP BY 1
ORDER BY 1
```

| customer_id | total_amount |
| ----------- | ------------ |
| A           | 76           |
| B           | 74           |
| C           | 36           |


### 2. How many days has each customer visited the restaurant?
```sql
SELECT
    DISTINCT s.customer_id,
    COUNT(DISTINCT s.order_date) AS num_of_visit_days
FROM dannys_diner.sales s
GROUP BY 1;
```
| customer_id | num_of_visit_days |
| ----------- | ----------------- |
| A           | 4                 |
| B           | 6                 |
| C           | 2                 |


### 3. What was the first item from the menu purchased by each customer?
```sql
WITH purchased_item AS (
    SELECT 
        s.customer_id,
        s.order_date,
        m.product_name,
  		ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date) as purchased_rank
FROM dannys_diner.sales s
    JOIN dannys_diner.menu m USING(product_id))                              
SELECT
    DISTINCT customer_id,
    product_name,
    order_date
FROM purchased_item
WHERE purchased_rank =1
ORDER BY order_date, customer_id
```
| order_date | customer_id | product_name |
| -----------| ----------- | ------------ |
| 2021-01-01 | A           | curry        |
| 2021-01-01 | B           | curry        |
| 2021-01-01 | C           | ramen        |
