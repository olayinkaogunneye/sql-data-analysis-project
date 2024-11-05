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

---

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

---

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

---
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
SELECT
    s.product_id,
    m.product_name,
    COUNT(*) as no_times_purchased
 FROM dannys_diner.sales s
 JOIN dannys_diner.menu m USING(product_id)
 GROUP BY 1,2
 ORDER BY no_times_purchased DESC
 LIMIT 1;
```
| product_name | num_of_times_purchase | Product_id
| ------------ | --------------------- |-----------|
| ramen        | 8                     | 3         |

---
### 5. Which item was the most popular for each customer?
```sql
WITH most_popular AS (
 SELECT
        s.customer_id,
        m.product_name,
        COUNT(product_name) AS num_of_times_product_ordered,
        ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY COUNT(product_name) DESC) AS row_num
    FROM dannys_diner.sales s
    JOIN dannys_diner.menu m USING(product_id)
    GROUP BY 1,2
    ORDER BY 1
)
 SELECT 
    customer_id, 
    product_name,
    num_of_times_product_ordered
   FROM most_popular
   WHERE row_num = 1
   GROUP BY 1,2,3
   ORDER BY 1
```
| customer_id | product_name | num_of_times_product_ordered |
| ----------- | ------------ | ---------------------------- |
| A           | ramen        | 3                            |
| B           | ramen        | 2                            |
| C           | ramen        | 3                            |

        
