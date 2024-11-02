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
JOIN dannys_diner.menu m USING(product_id)
GROUP BY 1
```
| customer_id | num_of_visit_days |
| ----------- | ----------------- |
| A           | 4                 |
| B           | 6                 |
| C           | 2                 |
