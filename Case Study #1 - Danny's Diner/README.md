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
----------- | ------------
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
----------- | ------------
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
      x.rank_number,
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
To determine the first items purchased from the menu by each customer, first, a ```DENSE_RANK()``` window function was used to assign ranks for each row partitioned by customer, with each partition sorted according to the dates they made an order from the restaurant. The ```DENSE_RANK()``` window function was used as opposed to the ```ROW_NUMBER()``` (which assigns continuous rank numbers) and ```RANK()``` (which similarly to ```DENSE_RANK()``` assigns the same rank number to duplicates but makes a jump in the sequence) because it has no gaps in ranking the values. Moreover, when ```ROW_NUMBER()``` is applied, it disregards results where the customer orders more than once on a particular date whereas ```DENSE_RANK()``` allows for duplicate rows. An *alias* of ```rank_number``` was also given to provide a more descriptive column name for the results.
- MIN vs ROW_NUMBER vs RANK vs DENSE_RANK
- 2 WITH CTE

#### Output:
customer_id | order_date | product_id | product_name
----------- | ---------- | ---------- | ------------
A | 2021-01-01T00:00:00.000Z | 1 | sushi
A | 2021-01-01T00:00:00.000Z | 2 | curry
B | 2021-01-01T00:00:00.000Z | 2 | curry
C | 2021-01-01T00:00:00.000Z | 3 | ramen
C | 2021-01-01T00:00:00.000Z | 3 | ramen

#### Answer:
Based from the output of the query, it can be observed that the first items purchased from the menu by Customer A were sushi and curry. As for Customer B, the first item they purchased was curry. Lastly, for Customer C, the first item they purchased from the menu was ramen.

- - - -
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql

```
- - - -
5. Which item was the most popular for each customer?
```sql

```
- - - -
6. Which item was purchased first by the customer after they became a member?
```sql

```
- - - -
7. Which item was purchased just before the customer became a member?
```sql

```
- - - -
8. What is the total items and amount spent for each member before they became a member?
```sql

```
- - - -
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql

```
- - - -
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql

```
- - - -
## Bonus Questions
1. Join All The Things

2. Rank All The Things
