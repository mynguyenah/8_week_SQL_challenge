# Pizza's runners Case Study #2
<image>
**Schema (PostgreSQL v13)**

    CREATE SCHEMA pizza_runner;
    SET search_path = pizza_runner;
    
    DROP TABLE IF EXISTS runners;
    CREATE TABLE runners (
      "runner_id" INTEGER,
      "registration_date" DATE
    );
    INSERT INTO runners
      ("runner_id", "registration_date")
    VALUES
      (1, '2021-01-01'),
      (2, '2021-01-03'),
      (3, '2021-01-08'),
      (4, '2021-01-15');
    
    
    DROP TABLE IF EXISTS customer_orders;
    CREATE TABLE customer_orders (
      "order_id" INTEGER,
      "customer_id" INTEGER,
      "pizza_id" INTEGER,
      "exclusions" VARCHAR(4),
      "extras" VARCHAR(4),
      "order_time" TIMESTAMP
    );
    
    INSERT INTO customer_orders
      ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
    VALUES
      ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
      ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
      ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
      ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
      ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
      ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
      ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
      ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
      ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
      ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
      ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
      ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
      ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
      ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');
    
    
    DROP TABLE IF EXISTS runner_orders;
    CREATE TABLE runner_orders (
      "order_id" INTEGER,
      "runner_id" INTEGER,
      "pickup_time" VARCHAR(19),
      "distance" VARCHAR(7),
      "duration" VARCHAR(10),
      "cancellation" VARCHAR(23)
    );
    
    INSERT INTO runner_orders
      ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
    VALUES
      ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
      ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
      ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
      ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
      ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
      ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
      ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
      ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
      ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
      ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');
    
    
    DROP TABLE IF EXISTS pizza_names;
    CREATE TABLE pizza_names (
      "pizza_id" INTEGER,
      "pizza_name" TEXT
    );
    INSERT INTO pizza_names
      ("pizza_id", "pizza_name")
    VALUES
      (1, 'Meatlovers'),
      (2, 'Vegetarian');
    
    
    DROP TABLE IF EXISTS pizza_recipes;
    CREATE TABLE pizza_recipes (
      "pizza_id" INTEGER,
      "toppings" TEXT
    );
    INSERT INTO pizza_recipes
      ("pizza_id", "toppings")
    VALUES
      (1, '1, 2, 3, 4, 5, 6, 8, 10'),
      (2, '4, 6, 7, 9, 11, 12');
    
    
    DROP TABLE IF EXISTS pizza_toppings;
    CREATE TABLE pizza_toppings (
      "topping_id" INTEGER,
      "topping_name" TEXT
    );
    INSERT INTO pizza_toppings
      ("topping_id", "topping_name")
    VALUES
      (1, 'Bacon'),
      (2, 'BBQ Sauce'),
      (3, 'Beef'),
      (4, 'Cheese'),
      (5, 'Chicken'),
      (6, 'Mushrooms'),
      (7, 'Onions'),
      (8, 'Pepperoni'),
      (9, 'Peppers'),
      (10, 'Salami'),
      (11, 'Tomatoes'),
      (12, 'Tomato Sauce');

---

**Query #1**

    DROP TABLE IF EXISTS updated_customer_orders;

There are no results to be displayed.

---
**Query #2**

    CREATE TEMP TABLE updated_customer_orders AS(
    SELECT
    	order_id,
        customer_id,
        pizza_id,
        CASE
          WHEN exclusions LIKE 'null' or exclusions = '' THEN null
          ELSE exclusions
          END AS exclusions,
         CASE
           WHEN extras LIKE 'null' or extras = '' THEN null
           ELSE extras
           END AS extras,
           order_time
      FROM pizza_runner.customer_orders
      );

There are no results to be displayed.

---
**Query #3**

    SELECT *
      FROM updated_customer_orders;

| order_id | customer_id | pizza_id | exclusions | extras | order_time               |
| -------- | ----------- | -------- | ---------- | ------ | ------------------------ |
| 1        | 101         | 1        |            |        | 2020-01-01T18:05:02.000Z |
| 2        | 101         | 1        |            |        | 2020-01-01T19:00:52.000Z |
| 3        | 102         | 1        |            |        | 2020-01-02T23:51:23.000Z |
| 3        | 102         | 2        |            |        | 2020-01-02T23:51:23.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 2        | 4          |        | 2020-01-04T13:23:46.000Z |
| 5        | 104         | 1        |            | 1      | 2020-01-08T21:00:29.000Z |
| 6        | 101         | 2        |            |        | 2020-01-08T21:03:13.000Z |
| 7        | 105         | 2        |            | 1      | 2020-01-08T21:20:29.000Z |
| 8        | 102         | 1        |            |        | 2020-01-09T23:54:33.000Z |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10T11:22:59.000Z |
| 10       | 104         | 1        |            |        | 2020-01-11T18:34:49.000Z |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11T18:34:49.000Z |

---
**Query #4**

    DROP TABLE IF EXISTS updated_runner_orders;

There are no results to be displayed.

---
**Query #5**

    CREATE TEMP TABLE updated_runner_orders AS(
        SELECT
        	order_id,
        	runner_id,
        	CASE 
        		WHEN pickup_time = 'null' THEN null
        		ELSE pickup_time
        		END :: TIMESTAMP as pickup_time,
     
        	NULLIF(REGEXP_REPLACE(distance, '[^0-9.]', '', 'g'), '') :: numeric as distance,
        	NULLIF(REGEXP_REPLACE(duration, '[^0-9.]', '', 'g'), '') :: numeric as duration,
        	CASE
        		WHEN cancellation IN ('null', 'NaN', '') THEN null
        		ELSE cancellation
        		END AS cancellation
     	FROM pizza_runner.runner_orders
    );

There are no results to be displayed.

---
**Query #6**

    SELECT *
    FROM updated_runner_orders;

| order_id | runner_id | pickup_time              | distance | duration | cancellation            |
| -------- | --------- | ------------------------ | -------- | -------- | ----------------------- |
| 1        | 1         | 2020-01-01T18:15:34.000Z | 20       | 32       |                         |
| 2        | 1         | 2020-01-01T19:10:54.000Z | 20       | 27       |                         |
| 3        | 1         | 2020-01-03T00:12:37.000Z | 13.4     | 20       |                         |
| 4        | 2         | 2020-01-04T13:53:03.000Z | 23.4     | 40       |                         |
| 5        | 3         | 2020-01-08T21:10:57.000Z | 10       | 15       |                         |
| 6        | 3         |                          |          |          | Restaurant Cancellation |
| 7        | 2         | 2020-01-08T21:30:45.000Z | 25       | 25       |                         |
| 8        | 2         | 2020-01-10T00:15:02.000Z | 23.4     | 15       |                         |
| 9        | 2         |                          |          |          | Customer Cancellation   |
| 10       | 1         | 2020-01-11T18:50:20.000Z | 10       | 10       |                         |

---
**Query #7**

    SELECT
      table_name,
      column_name,
      data_type
    FROM information_schema.columns
    WHERE table_name = 'updated_customer_orders';

| table_name              | column_name | data_type                   |
| ----------------------- | ----------- | --------------------------- |
| updated_customer_orders | order_id    | integer                     |
| updated_customer_orders | customer_id | integer                     |
| updated_customer_orders | pizza_id    | integer                     |
| updated_customer_orders | order_time  | timestamp without time zone |
| updated_customer_orders | exclusions  | character varying           |
| updated_customer_orders | extras      | character varying           |

---
**Query #8**

    SELECT
      table_name,
      column_name,
      data_type
    FROM information_schema.columns
    WHERE table_name = 'updated_runner_orders';

| table_name            | column_name  | data_type                   |
| --------------------- | ------------ | --------------------------- |
| updated_runner_orders | order_id     | integer                     |
| updated_runner_orders | runner_id    | integer                     |
| updated_runner_orders | pickup_time  | timestamp without time zone |
| updated_runner_orders | distance     | numeric                     |
| updated_runner_orders | duration     | numeric                     |
| updated_runner_orders | cancellation | character varying           |

---
**Query #9**

    SELECT
    	COUNT(order_id) AS pizza_ordered
    FROM
    	updated_customer_orders;

| pizza_ordered |
| ------------- |
| 14            |

---
**Query #10**

    SELECT
    	COUNT (DISTINCT customer_id) as total_unique_customer
    FROM updated_customer_orders;

| total_unique_customer |
| --------------------- |
| 5                     |

---
**Query #11**

    SELECT
    	runner_id,
    	COUNT(order_id) as successful_orders
    FROM
    	updated_runner_orders
    WHERE
    	cancellation = 'null' OR cancellation NOT IN ('CUSTOMER CANCELLATION' , 'RESTAURANT CANCELLATION' )
    GROUP BY
    	runner_id;

| runner_id | successful_orders |
| --------- | ----------------- |
| 3         | 1                 |
| 2         | 1                 |

---
**Query #12**

    SELECT
    	pn.pizza_name,
        COUNT(co.pizza_id) total_pizza
    FROM
    	updated_customer_orders AS co
        JOIN pizza_runner.pizza_names AS pn
        USING(pizza_id)
    GROUP BY
    	pn.pizza_name;

| pizza_name | total_pizza |
| ---------- | ----------- |
| Meatlovers | 10          |
| Vegetarian | 4           |

---
**Query #13**

    SELECT
    	co.customer_id,
        COUNT(co.pizza_id) total_orders
    FROM
    	updated_customer_orders AS co
        JOIN pizza_runner.pizza_names AS pn USING(pizza_id)
    GROUP BY
    	co.customer_id;

| customer_id | total_orders |
| ----------- | ------------ |
| 101         | 3            |
| 103         | 4            |
| 104         | 3            |
| 105         | 1            |
| 102         | 3            |

---
**Query #14**

    SELECT
    	co.customer_id,
        SUM(CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1 ELSE 0 END) AS no_change,
        SUM(CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1 ELSE 0 END) AS change
    FROM
    	updated_customer_orders AS co
        JOIN updated_runner_orders AS ro
        USING(order_id)
    WHERE
    	ro.cancellation IS NULL
        OR ro.cancellation NOT IN ('Customer_cancellation', 'Customer_cancellation')
    GROUP BY
    	co.customer_id
    ORDER BY
    	co.customer_id;

| customer_id | no_change | change |
| ----------- | --------- | ------ |
| 101         | 3         | 0      |
| 102         | 3         | 0      |
| 103         | 0         | 4      |
| 104         | 1         | 2      |
| 105         | 0         | 1      |

---
**Query #15**

    SELECT
    	COUNT(co.pizza_id) valid_pizza
    FROM    
    	updated_customer_orders AS co
        INNER JOIN updated_runner_orders AS ro
        USING(order_id)
    WHERE
    	(co.exclusions IS NOT NULL AND co.extras IS NOT NULL) AND CANCELLATION IS NULL;

| valid_pizza |
| ----------- |
| 1           |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/7VcQKQwsS3CTkGRFG7vu98/65)