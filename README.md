# 8_week_SQL_challenge
CASE STUDY #1 : DANNY'S DINER
/\* --------------------

Case Study Questions

--------------------\*/

-- 1. What is the total amount each customer spent at the restaurant?

-- 2. How many days has each customer visited the restaurant?

-- 3. What was the first item from the menu purchased by each customer?

-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

-- 5. Which item was the most popular for each customer?

-- 6. Which item was purchased first by the customer after they became a member?

-- 7. Which item was purchased just before the customer became a member?

-- 8. What is the total items and amount spent for each member before they became a member?

-- 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

-- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Answering

-- 1. What is the total amount each customer spent at the restaurant?

SELECT

SUM(m.price) AS total\_amount,

    s.customer\_id

FROM dannys\_diner.sales AS s

JOIN dannys\_diner.menu AS m ON s.product\_id = m.product\_id

GROUP BY customer\_id;

| Customer ID | Total amount |
| --- | --- |
| B | 74 |
| C | 36 |
| A | 76 |

/\* Answer:

The total amount each customer spent at the restaurant is

- A = 76

- B = 74

- C = 36 \*/

-- 2. How many days has each customer visited the restaurant?

SELECT

COUNT (DISTINCT order\_date) AS visited\_day,

customer\_id

FROM dannys\_diner.sales

GROUP BY

customer\_id;

| Visited\_day | Customer\_id |
| --- | --- |
| 4 | A |
| 6 | B |
| 2 | C |

/\*Answer

Customer A has visited in 4 days

Customer B has vistited in 6 days

Customer C has visisted in 2 days \*/

-- 3. What was the first item from the menu purchased by each customer

WITH Rank as (

SELECT

s.customer\_id,

s.order\_date,

m.product\_name,

RANK () OVER (

PARTITION BY s.customer\_id ORDER BY s.order\_date ASC ) as Rank

FROM

dannys\_diner.sales s

JOIN dannys\_diner.menu m ON s.product\_id = m.product\_id

)

SELECT

customer\_id,

product\_name

FROM Rank

WHERE Rank = 1

GROUP BY

customer\_id,

product\_name;

| Customer\_id | Product\_name |
| --- | --- |
| A | Curry |
| A | Sushi |
| B | Curry |
| C | Ramen |

/\* The first purchased item for

Customer A is sushi and curry ( on the same day)

Customer B is curry

Customer C is ramen\*/

-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers ?

SELECT

m.product\_name,

COUNT(s.product\_id) as total\_item

FROM

dannys\_diner.sales AS s

JOIN dannys\_diner.menu AS m USING(product\_id)

GROUP BY

m.product\_name

ORDER BY total\_item DESC

LIMIT 1;

| Product\_name | Total\_time |
| --- | --- |
| ramen | 8 |

/\* Ramen was the most purchased item on the menu and it was ordered 8 times by all customers \*/

-- 5. Which item was the most popular for each customer?

WITH Rank as (

  SELECT

   s.customer\_id,

   m.product\_name,

   COUNT(s.product\_id) as total\_item,

   RANK() OVER(

      PARTITION BY s.customer\_id ORDER BY COUNT(s.product\_id) DESC ) AS rank

  FROM

   dannys\_diner.sales s

   JOIN dannys\_diner.menu m USING(product\_id)

  GROUP BY s.customer\_id, m.product\_name

  )

 SELECT r.customer\_id, r.product\_name as popular\_item

 FROM Rank r

 WHERE Rank = 1;

| Customer\_id | Popolar\_item |
| --- | --- |
| A | Ramen |
| B | Ramen |
| B | Curry |
| B | Sushi |
| C | ramen |

/\* Ramen was the most popular for customer A

All three items ramen, curry, sushi was eaqual popular for customer B

Ramen was the most popular for customer C \*/

-- 6. Which item was purchased first by the customer after they became a member?

With Rank as

(

  SELECT

   s.customer\_id,

   m.product\_name,

   DENSE\_RANK() OVER(

      PARTITION BY s.customer\_id

      ORDER BY s.order\_date ASC) as Rank

  FROM

   dannys\_diner.sales s

   JOIN dannys\_diner.menu m USING(product\_id)

   JOIN dannys\_diner.members mem USING(customer\_id)

  WHERE s.order\_date \&gt;= mem.join\_date

  )

  SELECT \*

  FROM Rank

  Where Rank = 1;

| Customer\_id | Product\_name | Rank |
| --- | --- | --- |
| A | Curry | 1 |
| B | sushi | 1 |

/\* Item was first purchased by customer after they become a member:

Customer A is curry

Customer B is sushi \*/

-- 7. Which item was purchased just before the customer became a member?

With Rank as(

  SELECT

   s.customer\_id,

   m.product\_name,

   DENSE\_RANK()OVER(

      PARTITION BY s.customer\_id ORDER BY s.order\_date DESC) as Rank

  FROM

   dannys\_diner.sales s

   JOIN dannys\_diner.menu m ON s.product\_id = m.product\_id

   JOIN dannys\_diner.members mem ON s.customer\_id = mem.customer\_id

  WHERE s.order\_date \&lt; mem.join\_date

  )

  SELECT \*

  FROM Rank

  WHERE Rank = 1;

| Customer\_id | Product\_name | Rank |
| --- | --- | --- |
| A | Sushi | 1 |
| A | Curry | 1 |
| B | sushi | 1 |

/\* Here&#39;s items which are purchased by customer before they became a member

Customer A : sushi, curry

Customer B: sushi \*/

-- 8. What is the total items and amount spent for each member before they became a member?

SELECT

s.customer\_id,

    COUNT(s.product\_id) as quantity,

    SUM(s.product\_id \* m.price) as total\_spent

FROM

dannys\_diner.sales s

    JOIN dannys\_diner.menu m USING(product\_id)

    JOIN dannys\_diner.members mem USING(customer\_id)

WHERE

s.order\_date \&lt; mem.join\_date

GROUP BY

s.customer\_id;

| Customer\_id | Quantity | Total\_spent |
| --- | --- | --- |
| B | 3 | 70 |
| A | 2 | 40 |

/\* Before come a member:

Customer A spent total 40$ for 2 items

Customer B spent total 70$ for 3 items \*/

-- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

SELECT

s.customer\_id,

    SUM( CASE

        WHEN m.product\_name = &#39;sushi&#39;

        THEN (m.price\*20)

        ELSE (m.price\*10)

        END) AS total\_point

FROM

dannys\_diner.sales s

    JOIN dannys\_diner.menu m USING(product\_id)

GROUP BY s.customer\_id;

| Customer\_id | Total\_point |
| --- | --- |
| B | 940 |
| C | 360 |
| A | 860 |

/\* Customer B has 940 points

Customer C has 360 points

Customer A has 860 points \*/

-- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
