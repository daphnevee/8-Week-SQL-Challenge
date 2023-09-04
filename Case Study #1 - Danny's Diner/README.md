# Case Study #1: Danny's Diner

## Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny's Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem Statement
Danny wants to gather insights about his customers, pertaining to their visiting patterns, how much money they've spent and which menu items are their favourite in order to decide whether he should expand the existing customer loyalty program. 

## Entity Relationship Diagram
![Capture](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/5746f6f2-85b4-4b85-ab69-cb5894993d19)

## Case Study Questions and Answers
1. What is the total amount each customer spent at the restaurant?
#### Query:
```sql
SELECT
   x.customer_id,
   SUM(y.price) AS total_amount
FROM dannys_diner.sales x
JOIN dannys_diner.menu y
ON x.product_id=y.product_id
GROUP BY x.customer_id
ORDER BY x.customer_id;
```
#### Explanation:
To determine the total amount each customer spent at the restaurant, first, a ```JOIN``` clause was used to combine the sales table and menu table based on their related column, ```product_id```, in order to display the Customer ID and the prices of the products from each table, respectively. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. Second, a ```SUM``` aggregate function was used on the prices column to calculate the total amount spent by each customer on the products. This column was also given an *alias* of ```total_amount``` to provide a more descriptive column name for the results. Third, a ```GROUP BY``` statement was used to arrange the customers into groups in conjunction with the result of the ```SUM``` aggregate function to display the total amount that each customer spent on products at the restaurant. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order.

#### Output:
| customer_id | total_amount |
|:-----------:|:------------:|
|      A      |      76      |
|      B      |      74      |
|      C      |      36      |

#### Answer:
Based from the output of the query, it can be observed that Customer A spent a total amount of $76, Customer B spent a total amount of $75, and Customer C spent a total amount of $36 on food at the restaurant.
- - - -

2. How many days has each customer visited the restaurant?
#### Query:
```sql
SELECT 
   customer_id,
   COUNT(DISTINCT(order_date)) AS days_visited
FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY customer_id;
```
#### Explanation:
To determine the number of days each customer has visited the restaurant, first, a ```COUNT``` aggregate function was used to count  each customer's number of visit times based on the dates they made an order at the restaurant. Second, a ```DISTINCT``` statement was also used together with the ```COUNT``` function to specifically count the number of unique visits each customer has made at the restaurant. A ```COUNT(DISTINCT)``` approach was used as opposed to ```SELECT DISTINCT``` because this would produce results based on unique rows in ```customer_id``` and not on ```order_date```. This column was also given an *alias* of ```days_visited``` to provide a more descriptive column name for the results. Third, a ```GROUP BY``` statement was used to arrange the customers into groups in conjunction with the result of the ```COUNT``` aggregate function to display the number of days each customer has visited the restaurant. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order.

#### Output:
| customer_id | days_visited |
|:-----------:|:------------:|
|      A      |       4      |
|      B      |       6      |
|      C      |       2      |

#### Answer:
Based from the output of the query, it can be observed that Customer A visited the restaurant for 4 days, Customer B visited for 6 days, and Customer C visited for 2 days.

- - - -
3. What was the first item from the menu purchased by each customer?
#### Query:
```sql
WITH ranked_rows AS 
(
   SELECT
      customer_id,
      order_date,
      product_id,
      DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS "rank_number"
   FROM dannys_diner.sales
   ORDER BY customer_id
),

joined_tables AS
(
   SELECT
      x.customer_id,
      x.order_date,
      x.product_id,
      y.product_name,
      x.rank_number
   FROM ranked_rows x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
   ORDER BY x.customer_id, x.order_date
)

SELECT
   customer_id,
   TO_CHAR(order_date :: DATE, 'yyyy-mm-dd') AS order_date,
   product_id,
   product_name
FROM joined_tables 
WHERE rank_number = 1;
```
#### Explanation:
To determine the first items purchased from the menu by each customer, 2 CTEs were written to separate the operations of assigning ranks for each row and joining two tables to produce the desired results. 

In the first CTE labeled as ```ranked_rows```, a ```DENSE_RANK()``` window function was applied on the sales table and was used to assign ranks for each row partitioned by customer, with each partition sorted according to the dates they made an order from the restaurant. The ```DENSE_RANK()``` window function was used as opposed to the ```ROW_NUMBER()``` (which assigns continuous rank numbers) and ```RANK()``` (which similarly to ```DENSE_RANK()``` assigns the same rank number to duplicates but makes a jump in the sequence) because it has no gaps in ranking the values. Moreover, when ```ROW_NUMBER()``` is applied, it disregards results where the customer orders more than once on a particular date whereas ```DENSE_RANK()``` allows for duplicate rows. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. The results were then sorted by default in ascending order according to the Customer ID. The query then produced the following results:

| customer_id |        order_date        | product_id | rank_number |
|:-----------:|:------------------------:|:----------:|:-----------:|
|      A      | 2021-01-01T00:00:00.000Z |      1     |      1      |
|      A      | 2021-01-01T00:00:00.000Z |      2     |      1      |
|      A      | 2021-01-07T00:00:00.000Z |      2     |      2      |
|      A      | 2021-01-10T00:00:00.000Z |      3     |      3      |
|      A      | 2021-01-11T00:00:00.000Z |      3     |      4      |
|      A      | 2021-01-11T00:00:00.000Z |      3     |      4      |
|      B      | 2021-01-01T00:00:00.000Z |      2     |      1      |
|      B      | 2021-01-02T00:00:00.000Z |      2     |      2      |
|      B      | 2021-01-04T00:00:00.000Z |      1     |      3      |
|      B      | 2021-01-11T00:00:00.000Z |      1     |      4      |
|      B      | 2021-01-16T00:00:00.000Z |      3     |      5      |
|      B      | 2021-02-01T00:00:00.000Z |      3     |      6      |
|      C      | 2021-01-01T00:00:00.000Z |      3     |      1      |
|      C      | 2021-01-01T00:00:00.000Z |      3     |      1      |
|      C      | 2021-01-07T00:00:00.000Z |      3     |      2      |

In the second CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the resulting table of the first CTE (i.e. ```ranked_rows```) and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made the order, the ID of the product they ordered, the name of the product they ordered from the menu table, and their rank number. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```ranked_rows``` table and ```y``` for the menu table, so as to make the query more readable. The results were then sorted by default in ascending order according to both the Customer ID and the Order Date. The query then produced the following results:

| customer_id |        order_date        | product_id | product_name | rank_number |
|:-----------:|:------------------------:|:----------:|:------------:|:-----------:|
|      A      | 2021-01-01T00:00:00.000Z |      2     |     curry    |      1      |
|      A      | 2021-01-01T00:00:00.000Z |      1     |     sushi    |      1      |
|      A      | 2021-01-07T00:00:00.000Z |      2     |     curry    |      2      |
|      A      | 2021-01-10T00:00:00.000Z |      3     |     ramen    |      3      |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |      4      |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |      4      |
|      B      | 2021-01-01T00:00:00.000Z |      2     |     curry    |      1      |
|      B      | 2021-01-02T00:00:00.000Z |      2     |     curry    |      2      |
|      B      | 2021-01-04T00:00:00.000Z |      1     |     sushi    |      3      |
|      B      | 2021-01-11T00:00:00.000Z |      1     |     sushi    |      4      |
|      B      | 2021-01-16T00:00:00.000Z |      3     |     ramen    |      5      |
|      B      | 2021-02-01T00:00:00.000Z |      3     |     ramen    |      6      |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |      1      |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |      1      |
|      C      | 2021-01-07T00:00:00.000Z |      3     |     ramen    |      2      |

Finally, to determine the first items purchased by each customer, first, a ```TO_CHAR()``` function was used to display the ```order_date``` in a specific format. Second, a ```WHERE``` clause was used to filter the resulting records of the ```joined_tables``` CTE based on the condition where the rank number assigned to the row is equal to 1. This extracts the records when the customer made their first purchase from the restaurant.

#### Output:
| customer_id | order_date | product_id | product_name |
|:-----------:|:----------:|:----------:|:------------:|
|      A      | 2021-01-01 |      1     |     sushi    |
|      A      | 2021-01-01 |      2     |     curry    |
|      B      | 2021-01-01 |      2     |     curry    |
|      C      | 2021-01-01 |      3     |     ramen    |
|      C      | 2021-01-01 |      3     |     ramen    |

#### Answer:
Based from the output of the query, it can be observed that the first items purchased from the menu by Customer A were sushi and curry. As for Customer B, the first item they purchased was curry. Lastly, for Customer C, the first item they purchased from the menu was ramen.

- - - -
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
#### Query:
```sql
SELECT
   x.product_id,
   y.product_name,
   COUNT(x.product_id) AS num_of_orders
FROM dannys_diner.sales x
JOIN dannys_diner.menu y
ON x.product_id=y.product_id
GROUP BY x.product_id, y.product_name
ORDER BY num_of_orders DESC
LIMIT 1;
```
#### Explanation:
To determine the most purchased item on the menu and the number of times it was purchased, first, a ```COUNT``` aggregate function was used to count the total number of each type of product sold based on the sales table. An *alias* of ```num_of_orders``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine the sales table and the menu table in order to display in the results both the Product ID and the name of the product. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```ranked_rows``` table and ```y``` for the menu table, so as to make the query more readable. Third, a ```GROUP BY``` statement was used to arrange the counted number of orders made for each product into groups according to the Product ID and the name of the product. Fourth, an ```ORDER BY``` statement was used to sort the results in descending order to put the highest value at the top. Lastly, a ```LIMIT``` clause was used to display only the first record which is the most purchased item on the menu from the results.

#### Output:
| product_id | product_name | num_of_orders |
|:----------:|:------------:|:-------------:|
|      3     |     ramen    |       8       |

#### Answer:
Based from the output of the query, it can be observed that the most purchased item on the menu is ramen and it was purchased 8 times by all customers at the restaurant.
- - - -
5. Which item was the most popular for each customer?
#### Query:
```sql
WITH ranked_rows AS (
   SELECT
      customer_id,
      product_id,
      COUNT(product_id) AS num_of_orders,
      DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) AS "rank_number"
   FROM dannys_diner.sales
   GROUP BY customer_id, product_id
),

joined_tables AS (
   SELECT
      x.customer_id,
      x.product_id,
      y.product_name,
      x.num_of_orders,
      x.rank_number
   FROM ranked_rows x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
   ORDER BY x.customer_id, x.product_id
)

SELECT
   customer_id,
   product_id,
   product_name,
   num_of_orders
FROM joined_tables
WHERE rank_number = 1;
```
#### Explanation:
To determine the items that were most popular for each customer, 2 CTEs were written to separate the operations of assigning ranks for each row and joining two tables to produce the desired results. 

In the first CTE labeled as ```ranked_rows```, first, a ```COUNT``` aggregate function was used to count the total number of orders each customer made for each item on the menu based on the sales table. An *alias* of ```num_of_orders``` was given to provide a more descriptive column name for the results. Second, a ```DENSE_RANK()``` window function was applied on the sales table and was used to assign ranks for each row partitioned by customer, with each partition sorted in descending order according to the total number of orders each customer made for each item on the menu in order to put the highest values at the top. Similarly with the approach taken in Question #3, the ```DENSE_RANK()``` window function was the most applicable in acquiring the desired results as there are no gaps in ranking the values and it allows for duplicate rows. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID and the Product ID. The query then produced the following results:

| customer_id | product_id | num_of_orders | rank_number |
|:-----------:|:----------:|:-------------:|:-----------:|
|      A      |      3     |       3       |      1      |
|      A      |      2     |       2       |      2      |
|      A      |      1     |       1       |      3      |
|      B      |      3     |       2       |      1      |
|      B      |      1     |       2       |      1      |
|      B      |      2     |       2       |      1      |
|      C      |      3     |       3       |      1      |

In the second CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the resulting table of the first CTE (i.e. ```ranked_rows```) and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the ID of the product they ordered, the name of the product from the menu table, the number of orders they made for a specific product, and the assigned rank numbers produced by the first CTE. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```ranked_rows``` table and ```y``` for the menu table, so as to make the query more readable. The results were then sorted by default in ascending order according to both the Customer ID and the Product ID. The query then produced the following results:

| customer_id | product_id | product_name | num_of_orders | rank_number |
|:-----------:|:----------:|:------------:|:-------------:|:-----------:|
|      A      |      1     |     sushi    |       1       |      3      |
|      A      |      2     |     curry    |       2       |      2      |
|      A      |      3     |     ramen    |       3       |      1      |
|      B      |      1     |     sushi    |       2       |      1      |
|      B      |      2     |     curry    |       2       |      1      |
|      B      |      3     |     ramen    |       2       |      1      |
|      C      |      3     |     ramen    |       3       |      1      |

Finally, to determine the items that were most popular for each customer, a ```WHERE``` clause was used to filter the resulting records of the ```joined_tables``` CTE based on the condition where the rank number assigned to the row is equal to 1. This extracts the records concerning each customer's most ordered item from the menu.

#### Output:
| customer_id | product_id | product_name | num_of_orders |
|:-----------:|:----------:|:------------:|:-------------:|
|      A      |      3     |     ramen    |       3       |
|      B      |      1     |     sushi    |       2       |
|      B      |      2     |     curry    |       2       |
|      B      |      3     |     ramen    |       2       |
|      C      |      3     |     ramen    |       3       |

#### Answer:
Based from the output of the query, it can be observed that ramen was the most popular item from the menu for both Customer A and C, each having ordered it three times, while for Customer B, all items on the menu (i.e. sushi, curry, ramen) were equally popular and each were ordered twice.
- - - -
6. Which item was purchased first by the customer after they became a member?
#### Query:
```sql
WITH joined_tables AS (
   SELECT
      x.customer_id,
      x.order_date,
      x.product_id,
      y.product_name
   FROM dannys_diner.sales x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
),

ranked_rows AS (
   SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      x.product_name,
      DENSE_RANK() OVER (PARTITION BY x.customer_id ORDER BY x.order_date) AS "rank_number"
   FROM joined_tables x
   JOIN dannys_diner.members y
   ON x.customer_id=y.customer_id
   WHERE x.order_date >= y.join_date
)

SELECT
   customer_id,
   TO_CHAR(order_date :: DATE, 'yyyy-mm-dd') AS order_date,
   TO_CHAR(join_date :: DATE, 'yyyy-mm-dd') AS join_date,
   product_id,
   product_name
FROM ranked_rows
WHERE rank_number = 1;
```
#### Explanation:
To determine the first purchased item by each member of Danny's Diner's loyalty program, 2 CTEs were written to separate the operations of joining two tables and assigning row numbers for each row to produce the desired results.

In the first CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the ID of the product they ordered, and the name of the product from each table, respectively.  In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. The query then produced the following results:

| customer_id |        order_date        | product_id | product_name |
|:-----------:|:------------------------:|:----------:|:------------:|
|      B      | 2021-01-04T00:00:00.000Z |      1     |     sushi    |
|      A      | 2021-01-01T00:00:00.000Z |      1     |     sushi    |
|      B      | 2021-01-11T00:00:00.000Z |      1     |     sushi    |
|      B      | 2021-01-01T00:00:00.000Z |      2     |     curry    |
|      B      | 2021-01-02T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-01T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-07T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |
|      A      | 2021-01-10T00:00:00.000Z |      3     |     ramen    |
|      B      | 2021-01-16T00:00:00.000Z |      3     |     ramen    |
|      B      | 2021-02-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-07T00:00:00.000Z |      3     |     ramen    |

In the second CTE labeled as ```ranked_rows```, first, a ```DENSE_RANK()``` window function was applied on the resulting table of the first CTE (i.e. ```joined_tables```) and was used to assign ranks for each row partitioned by Customer ID, with each partition sorted by default in ascending order according to the date the customer made the order. Similarly with the approach taken in Questions #3 and #5, the ```DENSE_RANK()``` window function was most applicable in acquiring the desired results as not only are there no gaps in ranking the values but rather, it allows for duplicate rows since the ```order_date``` only contains the date information and no specific timestamp is provided which can be used to further distinguish each of the records. With that, there's no certainty of whether a particular product was ordered first by a customer on a particular date. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used in this query to combine the resulting table of the first CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the date they made an order, the date they joined as a member from the members table, the ID of the product as well as the name of the product they ordered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Lastly, a ```WHERE``` clause was used to filter the records based on the condiition where the customer made an order *after* they joined as a member of the restaurant's loyalty program, inclusive of any orders they made on the date they joined as well. The query then produced the following results:

| customer_id |        order_date        |         join_date        | product_id | product_name | rank_number |
|:-----------:|:------------------------:|:------------------------:|:----------:|:------------:|:-----------:|
|      A      | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |     curry    |      1      |
|      A      | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     ramen    |      2      |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     ramen    |      3      |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     ramen    |      3      |
|      B      | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |     sushi    |      1      |
|      B      | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |     ramen    |      2      |
|      B      | 2021-02-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |     ramen    |      3      |

Finally, to determine the first purchased item by each member of Danny's Diner's loyalty program, first, a ```TO_CHAR()``` function was used to display both the ```order_date``` and the ```join_date``` in specific formats. Second, a ```WHERE``` clause was also used to filter the resulting records of the ```row_numbers_table``` CTE based on the condition where the assigned row number is equal to 1. This extracts the records concerning each customer's first purchased item after they became a member of the restaurant's loyalty program.

#### Output:
| customer_id | order_date |  join_date | product_id | product_name |
|:-----------:|:----------:|:----------:|:----------:|:------------:|
|      A      | 2021-01-07 | 2021-01-07 |      2     |     curry    |
|      B      | 2021-01-11 | 2021-01-09 |      1     |     sushi    |

#### Answer:
Based from the output of the query, it can be observed that since Customer A became a member of the beta version of Danny's Diner loyalty program, the item they first purchased from the menu was curry, which they ordered on the same day the joined. As for Customer B, the item the first purchased from the menu was sushi, which they ordered 2 days after they joined.
- - - -
7. Which item was purchased just before the customer became a member?
#### Query:
```sql
WITH joined_tables AS (
   SELECT
      x.customer_id,
      x.order_date,
      x.product_id,
      y.product_name
   FROM dannys_diner.sales x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
),

ranked_rows AS (
   SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      x.product_name,
      DENSE_RANK() OVER (PARTITION BY x.customer_id ORDER BY x.order_date DESC) AS "rank_number"
   FROM joined_tables x
   JOIN dannys_diner.members y
   ON x.customer_id=y.customer_id
   WHERE x.order_date < y.join_date
)

SELECT
   customer_id,
   TO_CHAR(order_date :: DATE, 'yyyy-mm-dd') AS order_date,
   TO_CHAR(join_date :: DATE, 'yyyy-mm-dd') AS join_date,
   product_id,
   product_name
FROM ranked_rows
WHERE rank_number = 1;
```
#### Explanation:
To determine the item that was purchased by each customer just before they became a member, 2 CTEs were written to separate the operations of joining two tables and assigning rank numbers for each row to produce the desired results.

In the first CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the ID of the product they ordered, and the name of the product from each table, respectively.  In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. The query then produced the following results:

| customer_id |        order_date        | product_id | product_name |
|:-----------:|:------------------------:|:----------:|:------------:|
|      B      | 2021-01-04T00:00:00.000Z |      1     |     sushi    |
|      A      | 2021-01-01T00:00:00.000Z |      1     |     sushi    |
|      B      | 2021-01-11T00:00:00.000Z |      1     |     sushi    |
|      B      | 2021-01-01T00:00:00.000Z |      2     |     curry    |
|      B      | 2021-01-02T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-01T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-07T00:00:00.000Z |      2     |     curry    |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |
|      A      | 2021-01-10T00:00:00.000Z |      3     |     ramen    |
|      B      | 2021-01-16T00:00:00.000Z |      3     |     ramen    |
|      B      | 2021-02-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |
|      C      | 2021-01-07T00:00:00.000Z |      3     |     ramen    |

In the second CTE labeled as ```ranked_rows```, first, a ```DENSE_RANK()``` window function was applied on the resulting table of the first CTE (i.e. ```joined_tables```) and was used to assign ranks for each row partitioned by Customer ID, with each partition sorted in descending order according to the date they made the order to put the highest values (i.e. the latest order date) at the top. The ```DENSE_RANK()``` window function was more applicable in acquiring the desired results as there are no gaps in ranking the values and it allows for duplicate rows, especially with the ```order_date``` column since it only displays the date and not the specific time the order was made which would useful in uniquely distinguishing each record. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used in this query to combine the resulting table of the first CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the date they made an order, the date they joined as a member from the members table, the ID of the product as well as the name of the product they ordered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Lastly, a ```WHERE``` clause was used to filter the records based on the condition where the customer made an order *before* they joined as a member of the restaurant's loyalty program. The query then produced the following results:

| customer_id |        order_date        |         join_date        | product_id | product_name | rank_number |
|:-----------:|:------------------------:|:------------------------:|:----------:|:------------:|:-----------:|
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      1     |     sushi    |      1      |
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |     curry    |      1      |
|      B      | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |     sushi    |      1      |
|      B      | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |     curry    |      2      |
|      B      | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |     curry    |      3      |

Finally, to determine the item that was purchased by each customer just before they became a member, first, a ```TO_CHAR()``` function was used to display both the ```order_date``` and the ```join_date``` in specific formats. Second, a ```WHERE``` clause was also used to filter the resulting records of the ```ranked_rows``` CTE based on the condition where the assigned rank number is equal to 1. This extracts the records concerning each customer's last purchased item before they became a member of the restaurant's loyalty program.

#### Output:
| customer_id | order_date |  join_date | product_id | product_name |
|:-----------:|:----------:|:----------:|:----------:|:------------:|
|      A      | 2021-01-01 | 2021-01-07 |      1     |     sushi    |
|      A      | 2021-01-01 | 2021-01-07 |      2     |     curry    |
|      B      | 2021-01-04 | 2021-01-09 |      1     |     sushi    |

#### Answer:
Based from the output of the query, it can be observed that Customer A ordered both sushi and curry on the same day prior to becoming a member of the restaurant's loyalty program. On the other hand, Customer B only ordered sushi prior to becoming a member.

- - - -
8. What is the total items and amount spent for each member before they became a member?
#### Query:
```sql
WITH joined_tables AS (
   SELECT
      x.customer_id,
      x.order_date,
      x.product_id,
      y.product_name,
      y.price
   FROM dannys_diner.sales x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
)

SELECT
   x.customer_id,
   COUNT(x.product_id) AS num_items_bought,
   SUM(x.price) AS total_amount_spent
FROM joined_tables x
JOIN dannys_diner.members y
ON x.customer_id=y.customer_id
WHERE x.order_date < y.join_date
GROUP BY x.customer_id
ORDER BY x.customer_id;
```
#### Explanation:
To determine the total number of items and amount spent by each customer before they became a member of the restaurant's loyaly program, first, a CTE labeled as ```joined_tables``` consisting of a ```JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the ID of the product they ordered, the name of the product, and the price from each table, respectively. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. The query then produced the following results:

| customer_id |        order_date        | product_id | product_name | price |
|:-----------:|:------------------------:|:----------:|:------------:|:-----:|
|      B      | 2021-01-04T00:00:00.000Z |      1     |     sushi    |   10  |
|      A      | 2021-01-01T00:00:00.000Z |      1     |     sushi    |   10  |
|      B      | 2021-01-11T00:00:00.000Z |      1     |     sushi    |   10  |
|      B      | 2021-01-01T00:00:00.000Z |      2     |     curry    |   15  |
|      B      | 2021-01-02T00:00:00.000Z |      2     |     curry    |   15  |
|      A      | 2021-01-01T00:00:00.000Z |      2     |     curry    |   15  |
|      A      | 2021-01-07T00:00:00.000Z |      2     |     curry    |   15  |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |   12  |
|      A      | 2021-01-11T00:00:00.000Z |      3     |     ramen    |   12  |
|      A      | 2021-01-10T00:00:00.000Z |      3     |     ramen    |   12  |
|      B      | 2021-01-16T00:00:00.000Z |      3     |     ramen    |   12  |
|      B      | 2021-02-01T00:00:00.000Z |      3     |     ramen    |   12  |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |   12  |
|      C      | 2021-01-01T00:00:00.000Z |      3     |     ramen    |   12  |
|      C      | 2021-01-07T00:00:00.000Z |      3     |     ramen    |   12  |

Following that, a ```COUNT``` aggregate function was used to calculate the total number of items each customer bought prior to becoming a member. An *alias* of ```num_items_bought``` was given to provide a more descriptive column name for the results. Second, a ```SUM``` aggregate function was also used to calculate the total amount spent by each customer on products prior to becoming a member. An *alias* of ```total_amount_spent``` was also given to provide a more descriptive column name for the results. Third, a ```JOIN``` clause was then used to combine the resulting table of the CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the total number of items and the total amount spent by each customer prior to becoming a member. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Fourth, a ```WHERE``` clause was used to filter the records based on the condition where the customer made an order *before* they joined as a member of the restaurant's loyalty program. Fifth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order.

#### Output:
| customer_id | num_items_bought | total_amount_spent |
|:-----------:|:----------------:|:------------------:|
|      A      |         2        |         25         |
|      B      |         3        |         40         |

#### Answer:
Based from the output of the query, it can observed that Customer A spent a total of $25 on 2 items before becoming a member of the restaurant's loyalty program, while Customer B spent a total of $40 on 3 items.

- - - -
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### Query:
```sql
WITH points_table AS (
   SELECT
      product_id,
      CASE
         WHEN product_id = 1 THEN price * 20
         ELSE price * 10
      END AS points
   FROM dannys_diner.menu
)

SELECT
   x.customer_id,
   SUM(y.points) AS total_points
FROM dannys_diner.sales x
JOIN points_table y
ON x.product_id=y.product_id
GROUP BY x.customer_id
ORDER BY x.customer_id;
```
#### Explanation:
To determine the accumulated points of each customer based on products they bought, first, a CTE labeled ```points_table``` consisting of a ```CASE``` expression was used to set the conditions in calculating the points of each product based on the menu table. The first condition states that when the given product is sushi (i.e. having a Product ID of 1), then the calculated points would be equal to the price multiplied by 20 since sushi has a 2x points multiplier. Otherwise, if the given product is *not* sushi, the calculated points would be equal to the price multiplied only by 10 since each $1 spent equates to 10 points. An *alias* of ```points``` was given to provide a more descriptive column name for the results. The query then produced the following results:

| product_id | points |
|:----------:|:------:|
|      1     |   200  |
|      2     |   150  |
|      3     |   120  |

Following that, a ```SUM``` aggregate function was used to calculate the total points each customer has. An *alias* of ```total_points``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine the resulting table of the CTE, ```points_table```, and the sales table, based on their related column ```product_id```, in order to display the Customer ID and the total points they accumulated based on the products they bought. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the ```points_table``` table, so as to make the query more readable. Third, a ```GROUP BY``` statement was also used to arrange the results into groups according to the Customer ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order.

#### Output:
| customer_id | total_points |
|:-----------:|:------------:|
|      A      |      860     |
|      B      |      940     |
|      C      |      360     |

#### Answer:
Based from the output of the query, it can be observed that Customer A acquired a total of 860 points based from the products he bought, Customer B acquired a total of 940 points, and Customer C acquired a total of 360 points.

- - - -
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### Query:
```sql
WITH member_table AS (
  SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      CASE
	  WHEN x.order_date < y.join_date  THEN 'before membership'
	  WHEN x.order_date >= y.join_date AND x.order_date <= (y.join_date + 6) THEN 'first week'
	  WHEN x.order_date > (y.join_date + 6) AND x.order_date <= '2021-01-31' THEN 'within membership'
	  ELSE 'not within range'
      END AS member_status
  FROM dannys_diner.sales x
  JOIN dannys_diner.members y
  ON x.customer_id=y.customer_id
),

points_table AS (
  SELECT
      x.customer_id,
      x.product_id,
      x.order_date,
      x.join_date,
      x.member_status,
      CASE
	WHEN x.member_status = 'first week' THEN y.price * 20
	WHEN x.product_id = 1 AND (x.member_status = 'before membership' OR x.member_status = 'within membership') THEN y.price * 20
	ELSE y.price * 10
      END AS points
  FROM member_table x
  JOIN dannys_diner.menu y
  ON x.product_id=y.product_id
  WHERE member_status IN ('first week', 'before membership', 'within membership')
)

SELECT
    customer_id,
    SUM(points) AS total_points
FROM points_table
GROUP BY customer_id
ORDER BY customer_id;
```
#### Explanation:
To determine the total amount of points both Customer A and B accumulated by the end of January, 2 CTEs were used to separate the operations of identifying when an order was made by a member of the restaurant's loyalty program and the total amount of accumulated points based on specific durations of time to produce the desired results.

In the first CTE labeled as ```member_table```, first, a ```CASE``` expression was used to identify when an order was made by a member of the restaurant's loyalty program. The first condition checks whether an order was made before the customer joined as a member. If so, then it would return a result indicating that an order was made *before membership*. The second condition checks whether an order was made within the first week the customer joined as a member. As stated in the question, within the first week a customer joins the program, they earn 2x points on all items hence it is important to note this particular duration for calculating the points. If so, then it would return a result indicating that an order was made in the *first week*. The third condition checks whether an order was made *after* the first week the customer joined as a member up until the end of January (i.e. January 31, 2021). If so, then it would return a result indicating that an order was made *within membership* duration. Otherwise, if the order was made past January, then it would return a result of *not within range*. An *alias* of ```member_status``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine the sales table and the members table, based on their related column ```customer_id```, in order to display the ID of the Customer, the date they made the order, the date they joined as a member, and the ID of the product they ordered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the members table, so as to make the query more readable. The query then produced the following results:

| customer_id |        order_date        |         join_date        | product_id |   member_status   |
|:-----------:|:------------------------:|:------------------------:|:----------:|:-----------------:|
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      1     | before membership |
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     | before membership |
|      A      | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |     first week    |
|      A      | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     first week    |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     first week    |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |     first week    |
|      B      | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     | before membership |
|      B      | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     | before membership |
|      B      | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     | before membership |
|      B      | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |     first week    |
|      B      | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     | within membership |
|      B      | 2021-02-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |  not within range |

In the second CTE labeled as ```points_table```, first, a ```CASE``` expression was used to calculate the points for each product based on when it was ordered. Based on the conditions set in the first CTE, the first condition checks whether an order was made in the first week. If so, then the member would then earn 2x points on all items within this duration. On the other hand, the second condition checks whether the item that was ordered was sushi and if it was ordered before the customer joined as a member or *after* the first week they joined as a member. If so, then the customer/member would earn 2x points on *only* sushi items ordered. Otherwise, if the ordered item was not sushi, the customer/member would earn 10 points for every $1 spent. An *alias* of ```points``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine the resulting table of the first CTE (i.e. ```member_table```) and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the ID of the product they ordered, the date they made the order, the date they joined as a member, and the resulting status of the first CTE. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the members table, so as to make the query more readable. Lastly, a ```WHERE``` clause was used to filter the records based on only the conditions of whether the order was made before the customer joined as a member, within the first week the customer joined as a member, or after the first week up until the end of January. This then disregards any records with a status of *not within range* because the only results needed should not exceed the end of January. The query then produced the following results:

| customer_id | product_id |        order_date        |         join_date        |   member_status   | points |
|:-----------:|:----------:|:------------------------:|:------------------------:|:-----------------:|:------:|
|      B      |      1     | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z |     first week    |   200  |
|      B      |      1     | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z | before membership |   200  |
|      A      |      1     | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | before membership |   200  |
|      B      |      2     | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z | before membership |   150  |
|      B      |      2     | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z | before membership |   150  |
|      A      |      2     | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z |     first week    |   300  |
|      A      |      2     | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | before membership |   150  |
|      B      |      3     | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z | within membership |   120  |
|      A      |      3     | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |     first week    |   240  |
|      A      |      3     | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |     first week    |   240  |
|      A      |      3     | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z |     first week    |   240  |

Finally, to determine the total amount of points both Customer A and B accumulated by the end of January, first, a ```SUM``` aggregate function was used to calculate the total points based on the conditions set in the second CTE (i.e. ```points_table```). An *alias* of ```total_points``` was given to provide a more descriptive column name for the results. Second, a ```GROUP BY``` statement was used to arrange the results into groups based on their Customer ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order based on the Customer ID.

#### Output:
| customer_id | total_points |
|:-----------:|:------------:|
|      A      |     1370     |
|      B      |      820     |

#### Answer:
Based from the output of the query, it can be observed that Customer A accumulated a total of 1,370 points and Customer B accumulated a total of 820 points by the end of January.

- - - -
## Bonus Questions
1. Join All The Things
#### Query:
```sql
WITH member_table AS (
   SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      CASE
         WHEN x.order_date < y.join_date THEN 'N'
         WHEN x.order_date >= y.join_date THEN 'Y'
         ELSE 'N'
      END AS membership
   FROM dannys_diner.sales x
   LEFT JOIN dannys_diner.members y
   ON x.customer_id=y.customer_id
)

SELECT
   x.customer_id,
   TO_CHAR(x.order_date :: DATE, 'yyyy-mm-dd') AS order_date,
   y.product_name,
   y.price,
   x.membership AS member
FROM member_table AS x
JOIN dannys_diner.menu y
ON x.product_id=y.product_id
ORDER BY x.customer_id, x.order_date;
```
#### Explanation:
In this scenario, Danny wants to gather information on his customers and the products that they ordered before and if applicable, after they joined as members of his restaurant's loyalty program. To derive such insights, first, a CTE labeled ```members_table``` consisting of a ```CASE``` expression was used to set the conditions in determining whether a particular customer had joined as a member of the restaurant's loyalty program or not for a particular order record in the sales table. The first condition states that when the order was made before the customer joined as a member, then it would return an output of 'N' indicating that the customer was not a member. On the other hand, the second condition states that when the order was made on the day they joined or after they joined as a member, then it would return an output of 'Y' indicating that the customer was already a member. Otherwise, for customers that did not join the loyalty program, it would simply return an output of 'N' indicating that there's no record of the customer joining as a member when they made the order. An *alias* of ```membership``` was given to provide a more descriptive column name for the results. Second, a ```LEFT JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```customer_id```, in order to *all* records of customers that made an order, whether they are a member of the loyalty program or not. The query then produced the following results:

| customer_id |        order_date        |         join_date        | product_id | membership |
|:-----------:|:------------------------:|:------------------------:|:----------:|:----------:|
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      1     |      N     |
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |      N     |
|      A      | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |      Y     |
|      A      | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      B      | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |      N     |
|      B      | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |      N     |
|      B      | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |      N     |
|      B      | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |      Y     |
|      B      | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |      Y     |
|      B      | 2021-02-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |      Y     |
|      C      | 2021-01-01T00:00:00.000Z |           null           |      3     |      N     |
|      C      | 2021-01-01T00:00:00.000Z |           null           |      3     |      N     |
|      C      | 2021-01-07T00:00:00.000Z |           null           |      3     |      N     |

Following that, first, a ```TO_CHAR()``` function was used to display the ```order_date``` in a specific format. Second, a ```JOIN``` clause was used to combine the resulting table of the CTE, ```member_table```, and the menu table, based on their related column ```product_id```, in order to display the Customer ID, the date they made the order, the name of the product they ordered, the price of the product, and whether they had or had not joined as a member when they made the order. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```member_table``` and ```y``` for the menu table, so as to make the query more readable. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the Customer ID and the Order Date.

#### Output:
| customer_id | order_date | product_name | price | member |
|-------------|------------|--------------|-------|--------|
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

- - - - 

2. Rank All The Things
#### Query:
```sql
WITH member_table AS (
   SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      CASE
         WHEN x.order_date < y.join_date THEN 'N'
         WHEN x.order_date >= y.join_date THEN 'Y'
         ELSE 'N'
      END AS membership
   FROM dannys_diner.sales x
   LEFT JOIN dannys_diner.members y
   ON x.customer_id=y.customer_id
),

joined_tables AS (
   SELECT
      x.customer_id,
      TO_CHAR(x.order_date :: DATE, 'yyyy-mm-dd') AS order_date,
      y.product_name,
      y.price,
      x.membership AS member
   FROM member_table AS x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
   ORDER BY x.customer_id, x.order_date
)

SELECT
   *,
   CASE
      WHEN member = 'N' THEN NULL
      ELSE DENSE_RANK() OVER (PARTITION BY customer_id, member ORDER BY order_date)
   END AS "ranking"
FROM joined_tables
```
#### Explanation:
In continuation from the previous question, Danny now requires further information about the ```ranking``` of his products but he purposely does not need the ranking for non-member purchases so he expects null ```ranking``` values for the records when customers are not yet part of the loyalty program. To derive such insights, 2 CTEs were written to separate the operations of determining whether a customer made the order as a member of the loyalty program or not and joining two tables to produce the desired results.

In the first CTE labeled as ```member_table```, a ```CASE``` expression was used to set the conditions in determining whether a  customer had joined as a member of the restaurant's loyalty program or not for a particular order record in the sales table. The first condition states that when the order was made before the customer joined as a member, then it would return an output of 'N' indicating that the customer was not a member. On the other hand, the second condition states that when the order was made on the day they joined or after they joined as a member, then it would return an output of 'Y' indicating that the customer was already a member. Otherwise, for customers that did not join the loyalty program, it would simply return an output of 'N' indicating that there's no record of the customer joining as a member when they made the order. An *alias* of ```membership``` was given to provide a more descriptive column name for the results. Second, a ```LEFT JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```customer_id```, in order to *all* records of customers that made an order, whether they are a member of the loyalty program or not. The query then produced the following results:

| customer_id |        order_date        |         join_date        | product_id | membership |
|:-----------:|:------------------------:|:------------------------:|:----------:|:----------:|
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      1     |      N     |
|      A      | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |      N     |
|      A      | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      2     |      Y     |
|      A      | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      A      | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z |      3     |      Y     |
|      B      | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |      N     |
|      B      | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      2     |      N     |
|      B      | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |      N     |
|      B      | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      1     |      Y     |
|      B      | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |      Y     |
|      B      | 2021-02-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z |      3     |      Y     |
|      C      | 2021-01-01T00:00:00.000Z |           null           |      3     |      N     |
|      C      | 2021-01-01T00:00:00.000Z |           null           |      3     |      N     |
|      C      | 2021-01-07T00:00:00.000Z |           null           |      3     |      N     |

In the second CTE labeled as ```joined_tables```, first, a ```TO_CHAR()``` function was used to display the ```order_date``` in a specific format. Second, a ```JOIN``` clause was used to combine the sales table and the members table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the name of the product they ordered, the price of the product, and whether the customer was a member of the loyalty program or not. In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the members table, so as to make the query more readable. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the Customer ID and the Order Date. The query then produced the following results:

| customer_id | order_date | product_name | price | member |
|:-----------:|:----------:|:------------:|:-----:|:------:|
|      A      | 2021-01-01 |     sushi    |   10  |    N   |
|      A      | 2021-01-01 |     curry    |   15  |    N   |
|      A      | 2021-01-07 |     curry    |   15  |    Y   |
|      A      | 2021-01-10 |     ramen    |   12  |    Y   |
|      A      | 2021-01-11 |     ramen    |   12  |    Y   |
|      A      | 2021-01-11 |     ramen    |   12  |    Y   |
|      B      | 2021-01-01 |     curry    |   15  |    N   |
|      B      | 2021-01-02 |     curry    |   15  |    N   |
|      B      | 2021-01-04 |     sushi    |   10  |    N   |
|      B      | 2021-01-11 |     sushi    |   10  |    Y   |
|      B      | 2021-01-16 |     ramen    |   12  |    Y   |
|      B      | 2021-02-01 |     ramen    |   12  |    Y   |
|      C      | 2021-01-01 |     ramen    |   12  |    N   |
|      C      | 2021-01-01 |     ramen    |   12  |    N   |
|      C      | 2021-01-07 |     ramen    |   12  |    N   |

Finally, to provide the ```ranking``` of Danny's Diner's products, first, a ```CASE``` expression was used to set the conditions in determining whether a customer had made a particular order as a member of the loyalty program or not. The first condition states that for a particular order, when the customer is not a member of the loyalty program, it would return an output of null for the ```ranking``` value. Otherwise, a ```DENSE_RANK()``` window function is applied on a particular record from the resulting table of the second CTE (i.e. ```joined_tables```) and is used to assign ranks for each row partitioned by Customer ID, with each partition sorted by default in ascending order according to the date they made the order. The ```DENSE_RANK()``` window function was more applicable in acquiring the desired results since it allows for duplicate rows, especially in terms of the ```order_date``` column which displays only the date and not the specific timestamp the order was made. An *alias* of ```ranking``` was also given for the ```CASE``` expression to provide a more descriptive column name for the results. This query then provides Danny with information on the ranking of his restaurant's products that were ordered by members of his loyalty program.

#### Output:
| customer_id | order_date | product_name | price | member | ranking |
|-------------|------------|--------------|-------|--------|---------|
| A           | 2021-01-01 | sushi        | 10    | N      | null    |
| A           | 2021-01-01 | curry        | 15    | N      | null    |
| A           | 2021-01-07 | curry        | 15    | Y      | 1       |
| A           | 2021-01-10 | ramen        | 12    | Y      | 2       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| B           | 2021-01-01 | curry        | 15    | N      | null    |
| B           | 2021-01-02 | curry        | 15    | N      | null    |
| B           | 2021-01-04 | sushi        | 10    | N      | null    |
| B           | 2021-01-11 | sushi        | 10    | Y      | 1       |
| B           | 2021-01-16 | ramen        | 12    | Y      | 2       |
| B           | 2021-02-01 | ramen        | 12    | Y      | 3       |
| C           | 2021-01-01 | ramen        | 12    | N      | null    |
| C           | 2021-01-01 | ramen        | 12    | N      | null    |
| C           | 2021-01-07 | ramen        | 12    | N      | null    |

#### Assumptions:
Based from the output produced by both queries, it can be observed that Customer A must have enjoyed the curry item from the menu because a week later, they joined the restaurant's loyalty program as a member and ordered the curry item again. Customer A then must have also liked the ramen item from the menu because they consecutively ordered it at most three times in two days. As for Customer B, they must have enjoyed the sushi item because a week later, they joined the loyalty program as a member and ordered the sushi item again. Similar with Customer A, they also liked the ramen item from the menu as they ordered it twice. 
