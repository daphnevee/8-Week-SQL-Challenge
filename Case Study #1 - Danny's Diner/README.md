# Case Study #1: Danny's Diner

## Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny's Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem Statement
Danny wants to gather insights about his customers, pertaining to their visiting patterns, how much money they've spent and which menu items are their favourite in order to decide whether he should expand the existing customer loyalty program. 

## Entity Relationship Diagram
![Capture](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/9f6748eb-c8d7-41b5-9ecd-2dcd8ae827c7)

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
customer_id | total_amount
:---------: | :----------:
A | 76
B | 74
C | 36

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
customer_id | days_visited
:---------: | :----------:
A | 4
B | 6
C | 2

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
      y.product_name
   FROM ranked_rows x
   JOIN dannys_diner.menu y
   ON x.product_id=y.product_id
   ORDER BY x.customer_id, x.order_date
)

SELECT
   customer_id,
   order_date,
   product_id,
   product_name
FROM joined_tables 
WHERE rank_number = 1;
```
#### Explanation:
To determine the first items purchased from the menu by each customer, 2 CTEs were written to separate the operations of assigning ranks for each row and joining two tables to produce the desired results. 

In the first CTE labeled as ```ranked_rows```, a ```DENSE_RANK()``` window function was applied on the sales table and was used to assign ranks for each row partitioned by customer, with each partition sorted according to the dates they made an order from the restaurant. The ```DENSE_RANK()``` window function was used as opposed to the ```ROW_NUMBER()``` (which assigns continuous rank numbers) and ```RANK()``` (which similarly to ```DENSE_RANK()``` assigns the same rank number to duplicates but makes a jump in the sequence) because it has no gaps in ranking the values. Moreover, when ```ROW_NUMBER()``` is applied, it disregards results where the customer orders more than once on a particular date whereas ```DENSE_RANK()``` allows for duplicate rows. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. The results were then sorted by default in ascending order according to the Customer ID. The query then produced the following results:
customer_id | order_date | product_id | rank_number
:---------: | :--------: | :--------: | :---------:
A | 2021-01-01T00:00:00.000Z | 1 | 1
A | 2021-01-01T00:00:00.000Z | 2 | 1
A | 2021-01-07T00:00:00.000Z | 2 | 2
A | 2021-01-10T00:00:00.000Z | 3 | 3
A | 2021-01-11T00:00:00.000Z | 3 | 4
A | 2021-01-11T00:00:00.000Z | 3 | 4
B | 2021-01-01T00:00:00.000Z | 2 | 1 
B | 2021-01-02T00:00:00.000Z | 2 | 2
B | 2021-01-04T00:00:00.000Z | 1 | 3
B | 2021-01-11T00:00:00.000Z | 1 | 4
B | 2021-01-16T00:00:00.000Z | 3 | 5
B | 2021-02-01T00:00:00.000Z | 3 | 6
C | 2021-01-01T00:00:00.000Z | 3 | 1
C | 2021-01-01T00:00:00.000Z | 3 | 1
C | 2021-01-07T00:00:00.000Z | 3 | 2

In the second CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the resulting table of the first CTE (i.e. ```ranked_rows```) and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made the order, the ID of the product they ordered, and the name of the product they ordered from the menu table. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```ranked_rows``` table and ```y``` for the menu table, so as to make the query more readable. The results were then sorted by default in ascending order according to both the Customer ID and the Order Date. The query then produced the following results:
customer_id | order_date | product_id | product_name
:---------: | :--------: | :--------: | :----------:
A | 2021-01-01T00:00:00.000Z | 2 | curry
A | 2021-01-01T00:00:00.000Z | 1 | sushi
A | 2021-01-07T00:00:00.000Z | 2 | curry
A | 2021-01-10T00:00:00.000Z | 3 | ramen
A | 2021-01-11T00:00:00.000Z | 3 | ramen
A | 2021-01-11T00:00:00.000Z | 3 | ramen
B | 2021-01-01T00:00:00.000Z | 2 | curry
B | 2021-01-02T00:00:00.000Z | 2 | curry
B | 2021-01-04T00:00:00.000Z | 1 | sushi
B | 2021-01-11T00:00:00.000Z | 1 | sushi
B | 2021-01-16T00:00:00.000Z | 3 | ramen
B | 2021-02-01T00:00:00.000Z | 3 | ramen
C | 2021-01-01T00:00:00.000Z | 3 | ramen
C | 2021-01-01T00:00:00.000Z | 3 | ramen
C | 2021-01-07T00:00:00.000Z | 3 | ramen

Finally, to determine the first items purchased by each customer, a ```WHERE``` clause was used to filter the resulting records of the ```joined_tables``` CTE based on the condition where the rank number assigned to the row is equal to 1. This extracts the records when the customer made their first purchase from the restaurant.

#### Output:
customer_id | order_date | product_id | product_name
:---------: | :--------: | :--------: | :----------:
A | 2021-01-01T00:00:00.000Z | 1 | sushi
A | 2021-01-01T00:00:00.000Z | 2 | curry
B | 2021-01-01T00:00:00.000Z | 2 | curry
C | 2021-01-01T00:00:00.000Z | 3 | ramen
C | 2021-01-01T00:00:00.000Z | 3 | ramen

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
product_id | product_name | num_of_orders
:--------: | :----------: | :-----------:
3 | ramen | 8

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
customer_id | product_id | num_of_orders | rank_number
:---------: | :--------: | :-----------: | :---------:
A | 3 | 3 | 1
A | 2 | 2 | 2
A | 1 | 1 | 3
B | 3 | 2 | 1
B | 1 | 2 | 1
B | 2 | 2 | 1
C | 3 | 3 | 1

In the second CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the resulting table of the first CTE (i.e. ```ranked_rows```) and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the ID of the product they ordered, the name of the product from the menu table, the number of orders they made for a specific product, and the assigned rank numbers produced by the first CTE. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```ranked_rows``` table and ```y``` for the menu table, so as to make the query more readable. The results were then sorted by default in ascending order according to both the Customer ID and the Product ID. The query then produced the following results:
customer_id | product_id | product_name | num_of_orders | rank_number
:---------: | :--------: | :----------: | :-----------: | :---------:
A | 1 | sushi | 1 | 3
A | 2 | curry | 2 | 2
A | 3 | ramen | 3 | 1
B | 1 | sushi | 2 | 1
B | 2 | curry | 2 | 1
B | 3 | ramen | 2 | 1
C | 3 | ramen | 3 | 1

Finally, to determine the items that were most popular for each customer, a ```WHERE``` clause was used to filter the resulting records of the ```joined_tables``` CTE based on the condition where the rank number assigned to the row is equal to 1. This extracts the records concerning each customer's most ordered item from the menu.

#### Output:
customer_id | product_id | product_name | num_of_orders
:---------: | :--------: | :----------: | :-----------:
A | 3 | ramen | 3
B | 1 | sushi | 2
B | 2 | curry | 2
B | 3 | ramen | 2
C | 3 | ramen | 3

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

row_numbers_table AS (
   SELECT
      x.customer_id,
      x.order_date,
      y.join_date,
      x.product_id,
      x.product_name,
      ROW_NUMBER() OVER (PARTITION BY x.customer_id ORDER BY x.customer_id) AS "row_number"
   FROM joined_tables x
   JOIN dannys_diner.members y
   ON x.customer_id=y.customer_id
   WHERE x.order_date >= y.join_date
)

SELECT
   customer_id,
   order_date,
   join_date,
   product_id,
   product_name
FROM row_numbers_table
WHERE row_number = 1;
```
#### Explanation:
To determine the first purchased item by each member of Danny's Diner's loyalty program, 2 CTEs were written to separate the operations of joining two tables and assigning row numbers for each row to produce the desired results.

In the first CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the ID of the product they ordered, and the name of the product from each table, respectively.  In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. The query then produced the following results:
customer_id | order_date | product_id | product_name
:---------: | :--------: | :--------: | :----------:
B	| 2021-01-04T00:00:00.000Z | 1 | sushi
A	| 2021-01-01T00:00:00.000Z	| 1 | sushi
B	| 2021-01-11T00:00:00.000Z	| 1 | sushi
B	| 2021-01-01T00:00:00.000Z	| 2 | curry
B	| 2021-01-02T00:00:00.000Z	| 2 | curry
A	| 2021-01-01T00:00:00.000Z	| 2 | curry
A	| 2021-01-07T00:00:00.000Z	| 2 | curry
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen
A	| 2021-01-10T00:00:00.000Z	| 3 | ramen
B	| 2021-01-16T00:00:00.000Z	| 3 | ramen
B	| 2021-02-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-07T00:00:00.000Z	| 3 | ramen

In the second CTE labeled as ```row_numbers_table```, first, a ```ROW_NUMBER()``` window function was applied on the resulting table of the first CTE (i.e. ```joined_tables```) and was used to assign sequential integers for each row partitioned by Customer ID, with each partition sorted by default in ascending order according to the Customer ID. The ```ROW_NUMBER()``` window function was more applicable in acquiring the desired results as it assigns row numbers and increments irrespective of duplicate values. An *alias* of ```row_number``` was also given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used in this query to combine the resulting table of the first CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the date they made an order, the date they joined as a member from the members table, the ID of the product as well as the name of the product they ordered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Lastly, a ```WHERE``` clause was used to filter the records based on the condiition where the customer made an order *after* they joined as a member of the restaurant's loyalty program, inclusive of any orders they made on the date they joined as well. The query then produced the following results:
customer_id | order_date | join_date | product_id | product_name | row_number
:---------: | :--------: | :-------: | :--------: | :----------: | :--------:
A | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 2 | curry | 1
A | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 3 | ramen | 2
A | 2021-01-11T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 3 | ramen | 3
A | 2021-01-10T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 3 | ramen | 4
B | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 1 | sushi | 1
B | 2021-01-16T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 3 | ramen | 2
B | 2021-02-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 3 | ramen | 3

Finally, to determine the first purchased item by each member of Danny's Diner's loyalty program, a ```WHERE``` clause was also used to filter the resulting records of the ```row_numbers_table``` CTE based on the condition where the assigned row number is equal to 1. This extracts the records concerning each customer's first purchased item after they became a member of the restaurant's loyalty program.

#### Output:
customer_id | order_date | join_date | product_id | product_name
:---------: | :--------: | :-------: | :--------: | :----------:
A | 2021-01-07T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 2 | curry
B | 2021-01-11T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 1 | sushi

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
   order_date,
   join_date,
   product_id,
   product_name
FROM ranked_rows
WHERE rank_number = 1;
```
#### Explanation:
To determine the item that was purchased by each customer just before they became a member, 2 CTEs were written to separate the operations of joining two tables and assigning rank numbers for each row to produce the desired results.

In the first CTE labeled as ```joined_tables```, a ```JOIN``` clause was used to combine the sales table and the menu table, based on their related column ```product_id```, in order to display the ID of the Customer, the date they made an order, the ID of the product they ordered, and the name of the product from each table, respectively.  In joining the two tables, *aliases* were also given, i.e. ```x``` for the sales table and ```y``` for the menu table, so as to make the query more readable. The query then produced the following results:
customer_id | order_date | product_id | product_name
:---------: | :--------: | :--------: | :----------:
B	| 2021-01-04T00:00:00.000Z | 1 | sushi
A	| 2021-01-01T00:00:00.000Z	| 1 | sushi
B	| 2021-01-11T00:00:00.000Z	| 1 | sushi
B	| 2021-01-01T00:00:00.000Z	| 2 | curry
B	| 2021-01-02T00:00:00.000Z	| 2 | curry
A	| 2021-01-01T00:00:00.000Z	| 2 | curry
A	| 2021-01-07T00:00:00.000Z	| 2 | curry
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen
A	| 2021-01-10T00:00:00.000Z	| 3 | ramen
B	| 2021-01-16T00:00:00.000Z	| 3 | ramen
B	| 2021-02-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen
C	| 2021-01-07T00:00:00.000Z	| 3 | ramen

In the second CTE labeled as ```ranked_rows```, first, a ```DENSE_RANK()``` window function was applied on the resulting table of the first CTE (i.e. ```joined_tables```) and was used to assign ranks for each row partitioned by Customer ID, with each partition sorted in descending order according to the date they made the order to put the highest values (i.e. the latest order date) at the top. The ```DENSE_RANK()``` window function was more applicable in acquiring the desired results as there are no gaps in ranking the values and it allows for duplicate rows, especially with the ```order_date``` column since it only displays the date and not the specific time the order was made which would useful in uniquely distinguishing each record. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used in this query to combine the resulting table of the first CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the date they made an order, the date they joined as a member from the members table, the ID of the product as well as the name of the product they ordered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Lastly, a ```WHERE``` clause was used to filter the records based on the condition where the customer made an order *before* they joined as a member of the restaurant's loyalty program. The query then produced the following results:
customer_id | order_date | join_date | product_id | product_name | rank_number
:---------: | :--------: | :-------: | :--------: | :----------: | :---------:
A | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 1 | sushi | 1
A | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 2 | curry | 1
B | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 1 | sushi | 1
B | 2021-01-02T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 2 | curry | 2
B | 2021-01-01T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 2 | curry | 3

Finally, to determine the item that was purchased by each customer just before they became a member, a ```WHERE``` clause was also used to filter the resulting records of the ```ranked_rows``` CTE based on the condition where the assigned rank number is equal to 1. This extracts the records concerning each customer's last purchased item before they became a member of the restaurant's loyalty program.

#### Output:
customer_id | order_date | join_date | product_id | product_name
:---------: | :--------: | :-------: | :--------: | :----------:
A | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 1 | sushi
A | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | 2 | curry
B | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z | 1 | sushi

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

customer_id | order_date | product_id | product_name | price
:---------: | :--------: | :--------: | :----------: | :---:
B	| 2021-01-04T00:00:00.000Z | 1 | sushi | 10
A	| 2021-01-01T00:00:00.000Z	| 1 | sushi | 10
B	| 2021-01-11T00:00:00.000Z	| 1 | sushi | 10
B	| 2021-01-01T00:00:00.000Z	| 2 | curry | 15
B	| 2021-01-02T00:00:00.000Z	| 2 | curry | 15
A	| 2021-01-01T00:00:00.000Z	| 2 | curry | 15
A	| 2021-01-07T00:00:00.000Z	| 2 | curry | 15
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen | 12
A	| 2021-01-11T00:00:00.000Z	| 3 | ramen | 12
A	| 2021-01-10T00:00:00.000Z	| 3 | ramen | 12
B	| 2021-01-16T00:00:00.000Z	| 3 | ramen | 12
B	| 2021-02-01T00:00:00.000Z	| 3 | ramen | 12
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen | 12
C	| 2021-01-01T00:00:00.000Z	| 3 | ramen | 12
C	| 2021-01-07T00:00:00.000Z	| 3 | ramen | 12

Following that, a ```COUNT``` aggregate function was used to calculate the total number of items each customer bought prior to becoming a member. An *alias* of ```num_items_bought``` was given to provide a more descriptive column name for the results. Second, a ```SUM``` aggregate function was also used to calculate the total amount spent by each customer on products prior to becoming a member. An *alias* of ```total_amount_spent``` was also given to provide a more descriptive column name for the results. Third, a ```JOIN``` clause was then used to combine the resulting table of the CTE and the members table, based on their related column ```customer_id```, in order to display the ID of the customer, the total number of items and the total amount spent by each customer prior to becoming a member. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```joined_tables``` table and ```y``` for the members table, so as to make the query more readable. Fourth, a ```WHERE``` clause was used to filter the records based on the condition where the customer made an order *before* they joined as a member of the restaurant's loyalty program. Fifth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order.

#### Output:
customer_id	| num_items_bought | total_amount_spent
:---------: | :--------------: | :----------------:
A | 2 | 25
B | 3 | 40

#### Answer:
Based from the output of the query, it can observed that Customer A spent a total of $25 on 2 items before becoming a member of the restaurant's loyalty program, while Customer B spent a total of $40 on 3 items.

- - - -
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -
## Bonus Questions
1. Join All The Things

2. Rank All The Things
