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
