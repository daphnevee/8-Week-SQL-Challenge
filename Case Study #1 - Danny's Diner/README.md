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
To determine the number of days each customer has visited the restaurant, first, a 

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
```sql

```
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql

```
5. Which item was the most popular for each customer?
```sql

```
6. Which item was purchased first by the customer after they became a member?
```sql

```
7. Which item was purchased just before the customer became a member?
```sql

```
8. What is the total items and amount spent for each member before they became a member?
```sql

```
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql

```
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql

```

## Bonus Questions
1. Join All The Things
   
2. Rank All The Things
