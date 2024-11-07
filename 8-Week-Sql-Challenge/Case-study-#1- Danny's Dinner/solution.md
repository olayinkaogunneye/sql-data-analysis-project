### Solutions
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

---
### 6. Which item was purchased first by the customer after they became a member?
```sql
WITH first_purchased AS (
   SELECT
        s.customer_id,
        m.product_name,
        me.join_date,
        s.order_date,
        COUNT(product_name) AS num_of_times_product_ordered,
        ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date) AS row_num
    FROM dannys_diner.sales s
    JOIN dannys_diner.menu m USING(product_id)
    LEFT JOIN  dannys_diner.members me USING(customer_id)
    WHERE me.join_date < s.order_date
    GROUP BY 1, 2, 3, 4
    ORDER BY 1)
	SELECT 
		customer_id,
        product_name,
        num_of_times_product_ordered
	FROM first_purchased
    WHERE row_num =1;

```
| customer_id | product_name | num_of_times_product_ordered |
| ----------- | ------------ | ---------------------------- |
| A           | ramen        | 1                            |
| B           | sushi        | 1                            |

---
### 7. Which item was purchased just before the customer became a member?
```sql
WITH last_purchased AS (
   SELECT
        s.customer_id,
        m.product_name,
        me.join_date,
        s.order_date,
        COUNT(product_name) AS num_of_times_product_ordered,
        ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date DESC) AS row_num
    FROM dannys_diner.sales s
    JOIN dannys_diner.menu m USING(product_id)
    LEFT JOIN  dannys_diner.members me USING(customer_id)
    WHERE me.join_date < s.order_date
    GROUP BY 1, 2, 3, 4
    ORDER BY 1)
	SELECT 
		customer_id,
        product_name,
        num_of_times_product_ordered
	FROM last_purchased
    WHERE row_num =1;

```
| customer_id | product_name | num_of_times_product_ordered |
| ----------- | ------------ | ---------------------------- |
| A           | sushi        | 1                            |
| B           | sushi        | 1                            |

---
### 8. What is the total items and amount spent for each member before they became a member?
```sql
SELECT
    	customer_id,
        SUM(price) as amount_spent,
        COUNT(product_id) as total_amount
	FROM dannys_diner.sales s
	JOIN dannys_diner.menu m USING(product_id)
	LEFT JOIN dannys_diner.members me USING(customer_id)
WHERE s.order_date < me.join_date
GROUP BY 1
ORDER BY 1;

```
| customer_id | num_of_items | total_amount |
| ----------- | ------------ | ------------ |
| A           | 2            | 25           |
| B           | 3            | 40           |

---

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
WITH multiplier AS (
    SELECT
        s.customer_id,
        m.product_name,
        m.price,
        CASE WHEN product_id = 1 THEN m.price*20 ELSE m.price*10 END AS points
    FROM dannys_diner.menu m
    JOIN dannys_diner.sales s USING(product_id)
)
SELECT
    customer_id,
    SUM(points) AS total_price
FROM multiplier
GROUP BY 1
ORDER BY 2 DESC
```
| customer_id | total_price |
| ----------- | ----------- |
| B           | 940         |
| A           | 860         |
| C           | 360         |

---

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql
WITH earnings AS (
    SELECT
        customer_id,
        s.product_id,
        s.order_date,
        m.join_date
    FROM dannys_diner.sales s
    JOIN dannys_diner.members m USING(customer_id)
    WHERE m.join_date <= s.order_date
)
SELECT
    customer_id,
    SUM(total_point) AS total_point
FROM(
    SELECT
        customer_id,
        product_id,
        SUM(CASE WHEN order_date < join_date + interval '7 DAY' THEN price * 20 ELSE price*10 END) AS total_point
    FROM earnings ea
    JOIN dannys_diner.menu me USING(product_id)
    WHERE extract(MONTH FROM order_date) = 01
    GROUP BY 1,2
) AS stopp
GROUP BY 1
```

| customer_id | total_point |
| ----------- | ----------- |
| A           | 1020        |
| B           | 320         |


## Bonus Questions
### Joining All The Things
```sql
-- The members column  with value N stands for NO and Y as YES.
SELECT
    customer_id,
    order_date,
    product_name,
    price,
    CASE WHEN join_date <= order_date THEN 'Y' ELSE 'N' END AS member
FROM dannys_diner.sales s
JOIN dannys_diner.menu m USING(product_id)
LEFT JOIN dannys_diner.members me USING(customer_id)
ORDER BY 1,2
```

| customer_id | order_date               | product_name | price | member |
| ----------- | ------------------------ | ------------ | ----- | ------ |
| A           | 2021-01-01T00:00:00.000Z | sushi        | 10    | N      |
| A           | 2021-01-01T00:00:00.000Z | curry        | 15    | N      |
| A           | 2021-01-07T00:00:00.000Z | curry        | 15    | Y      |
| A           | 2021-01-10T00:00:00.000Z | ramen        | 12    | Y      |
| A           | 2021-01-11T00:00:00.000Z | ramen        | 12    | Y      |
| A           | 2021-01-11T00:00:00.000Z | ramen        | 12    | Y      |
| B           | 2021-01-01T00:00:00.000Z | curry        | 15    | N      |
| B           | 2021-01-02T00:00:00.000Z | curry        | 15    | N      |
| B           | 2021-01-04T00:00:00.000Z | sushi        | 10    | N      |
| B           | 2021-01-11T00:00:00.000Z | sushi        | 10    | Y      |
| B           | 2021-01-16T00:00:00.000Z | ramen        | 12    | Y      |
| B           | 2021-02-01T00:00:00.000Z | ramen        | 12    | Y      |
| C           | 2021-01-01T00:00:00.000Z | ramen        | 12    | N      |
| C           | 2021-01-01T00:00:00.000Z | ramen        | 12    | N      |
| C           | 2021-01-07T00:00:00.000Z | ramen        | 12    | N      |

