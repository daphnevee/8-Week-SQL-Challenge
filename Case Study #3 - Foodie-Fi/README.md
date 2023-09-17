# Case Study #3: Foodie-Fi

## Introduction
Subscription based businesses are super popular and Danny realised that there was a large gap in the market - he wanted to create a new streaming service that only had food related content - something like Netflix but with only cooking shows!

Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world!

Danny created Foodie-Fi with a data driven mindset and wanted to ensure all future investment decisions and new features were decided using data. This case study focuses on using subscription style digital data to answer important business questions.

## Problem Statement
Danny wants to gather insights about his customers, pertaining to their preferred subscription plan, when they churn, and the time their subscription plan starts in order to make better-informed decisions with regards to future investments and new features to be added to the Foodie-Fi streaming service platform.

## Entity Relationship Diagram
![Capture 3](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/a7391761-eac6-4620-bb3f-0c661bba4599)

## Table of Contents
1. [Customer Journey](#customer-journey)
2. [Data Analysis Questions](#data-analysis-questions)
3. [Challenge Payment Question](#challenge-payment-question)
4. [Outside The Box Questions](#outside-the-box-questions)

## Case Study Questions and Answers
<!--
CREATE SCHEMA foodie_fi;
SET search_path = foodie_fi;

CREATE TABLE plans (
  plan_id INTEGER,
  plan_name VARCHAR(13),
  price DECIMAL(5,2)
);

INSERT INTO plans
  (plan_id, plan_name, price)
VALUES
  ('0', 'trial', '0'),
  ('1', 'basic monthly', '9.90'),
  ('2', 'pro monthly', '19.90'),
  ('3', 'pro annual', '199'),
  ('4', 'churn', null);


CREATE TABLE subscriptions (
  customer_id INTEGER,
  plan_id INTEGER,
  start_date DATE
);

INSERT INTO subscriptions
  (customer_id, plan_id, start_date)
VALUES
  ('1', '0', '2020-08-01'),
  ('1', '1', '2020-08-08'),
  ('2', '0', '2020-09-20'),
  ('2',	'3', '2020-09-27'),
  ('11', '0', '2020-11-19'),
  ('11', '4', '2020-11-26'),
  ('13', '0', '2020-12-15'),
  ('13', '1', '2020-12-22'),
  ('13', '2', '2021-03-29'),
  ('15', '0', '2020-03-17'),
  ('15', '2', '2020-03-24'),
  ('15', '4', '2020-04-29'),
  ('16', '0', '2020-05-31'),
  ('16', '1', '2020-06-07'),
  ('16', '3', '2020-10-21'),
  ('18', '0', '2020-07-06'),
  ('18', '2', '2020-07-13'),
  ('19', '0', '2020-06-22'),
  ('19', '2', '2020-06-29'),
  ('19', '3', '2020-08-29');
-->
### A. Customer Journey <a href="anchor" id="customer-journey"></a>

Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey. Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!
#### Query:
```sql
SELECT
    x.customer_id,
    y.plan_name,
    y.price,
    x.start_date
FROM foodie_fi.subscriptions x
JOIN foodie_fi.plans y
ON x.plan_id=y.plan_id
WHERE x.customer_id BETWEEN 1 AND 8
ORDER BY x.customer_id, y.plan_id;
```
#### Explanation:
To get a clear picture of each customer's onboarding journey, a ```JOIN``` clause was used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the Customer ID and the Start Date details from the ```subscriptions``` table and the Plan Name and Price from the ```plans``` table. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table, so as to make the query more readable. In addition to that, a ```WHERE``` clause is applied to filter the results to show only the details for customers with the ID of 1 through 8 given that the new schema appears to have been modified to have a total number of customer records ranging up to 1000 compared to the given table in the instructions for the Case Study. An ```ORDER BY``` statement was then also used to organize the results by default in ascending order according to the Customer ID and the Plan ID.

#### Output:
| customer_id |   plan_name   |  price |        start_date        |
|:-----------:|:-------------:|:------:|:------------------------:|
|      1      |     trial     |  0.00  | 2020-08-01T00:00:00.000Z |
|      1      | basic monthly |  9.90  | 2020-08-08T00:00:00.000Z |
|      2      |     trial     |  0.00  | 2020-09-20T00:00:00.000Z |
|      2      |   pro annual  | 199.00 | 2020-09-27T00:00:00.000Z |
|      3      |     trial     |  0.00  | 2020-01-13T00:00:00.000Z |
|      3      | basic monthly |  9.90  | 2020-01-20T00:00:00.000Z |
|      4      |     trial     |  0.00  | 2020-01-17T00:00:00.000Z |
|      4      | basic monthly |  9.90  | 2020-01-24T00:00:00.000Z |
|      4      |     churn     |  null  | 2020-04-21T00:00:00.000Z |
|      5      |     trial     |  0.00  | 2020-08-03T00:00:00.000Z |
|      5      | basic monthly |  9.90  | 2020-08-10T00:00:00.000Z |
|      6      |     trial     |  0.00  | 2020-12-23T00:00:00.000Z |
|      6      | basic monthly |  9.90  | 2020-12-30T00:00:00.000Z |
|      6      |     churn     |  null  | 2021-02-26T00:00:00.000Z |
|      7      |     trial     |  0.00  | 2020-02-05T00:00:00.000Z |
|      7      | basic monthly |  9.90  | 2020-02-12T00:00:00.000Z |
|      7      |  pro monthly  |  19.90 | 2020-05-22T00:00:00.000Z |
|      8      |     trial     |  0.00  | 2020-06-11T00:00:00.000Z |
|      8      | basic monthly |  9.90  | 2020-06-18T00:00:00.000Z |
|      8      |  pro monthly  |  19.90 | 2020-08-03T00:00:00.000Z |

#### Answer:
Based from the output of the query, it can be observed Customer 1 chose to sign up for an initial 7 day free trial of Foodie-Fi's streaming service on the 1st of August. It appears that Customer 1 was satisified with the streaming service because on the 8th of August, after the 7 day free trial, they immediately opted to purchase a basic monthly plan at the price of $9.90. The same behavior can also be observed from Customers 3 and 5 who signed up for the free trial and later purchased a basic monthly plan. Customer 2 also signed up for the initial 7 day free trial on the 20th of September and after a week, at the end of the trial, chose to purchase a pro annual subscription at the price of $199. As for Customers 7 and 8, they signed up for the free trial and after a week, purchased a basic monthly plan. Given that basic monthly customers have limited access to Foodie-Fi's streaming services, both customers decided to upgrade their current subscription plan to a pro monthly plan at the price of $19.90. Pro plan customers, compared to basic monthly plan customers, have no watch time limits and can also download videos for offline viewing. On the other hand, it can be observed that Customers 4 and 6 initially signed up for a free trial and later purchased a basic monthly plan but after a couple of months, they decided to cancel their subscription plan based on their churn plan records.

- - - -
### B. Data Analysis Questions <a href="anchor" id="data-analysis-questions"></a>
1. How many customers has Foodie-Fi ever had?
#### Query:
```sql
SELECT  
  COUNT(DISTINCT customer_id) AS total_num_customers
FROM foodie_fi.subscriptions;
```

#### Explanation:
To determine the total number of customers Foodie-Fi has ever had, a ```COUNT``` aggregate function was used in conjunction with a ```DISTINCT``` clause to count only unique customer records, and to avoid duplicates, in the ```subscriptions``` table. An *alias* of ```total_num_customers``` was given to provide a more descriptive column name for the results.

#### Output:
| total_num_customers |
|:-------------------:|
|         1000        |

#### Answer:
Based from the output of the query, it can be observed that Foodie-Fi has had a total number of 1000 customers since the startup was launched in 2020.

- - - -

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
#### Query:
```sql
SELECT
    DATE_PART('Month', x.start_date) AS month_id,
    TO_CHAR(x.start_date, 'Month') AS month_name,
    COUNT(DISTINCT x.customer_id) AS monthly_distribution
FROM foodie_fi.subscriptions x
JOIN foodie_fi.plans y
ON x.plan_id=y.plan_id
WHERE x.plan_id = 0
GROUP BY month_id, month_name
ORDER BY month_id;
```

#### Explanation:

#### Output:
| month_id | month_name | monthly_distribution |
|:--------:|:----------:|:--------------------:|
|     1    |   January  |          88          |
|     2    |  February  |          68          |
|     3    |    March   |          94          |
|     4    |    April   |          81          |
|     5    |     May    |          88          |
|     6    |    June    |          79          |
|     7    |    July    |          89          |
|     8    |   August   |          88          |
|     9    |  September |          87          |
|    10    |   October  |          79          |
|    11    |  November  |          75          |
|    12    |  December  |          84          |

#### Answer:
Based from the output of the query, it can be observed that the month of March has the highest monthly distribution with a total of 94 customer sign-ups for Foodie-Fi's 7 day free trial. The month of July follows a close second with a total of 89 customer sign-ups, with the months of January, May, and August with a total of 88 customer sign-ups each. On the other hand, the month of February has the lowest monthly distribution with a total of 68 customer sign-ups.

- - - -

3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
#### Query:
```sql
SELECT
  x.plan_id,
  y.plan_name,
  COUNT(x.plan_id) AS count_of_events
FROM foodie_fi.subscriptions x
JOIN foodie_fi.plans y
ON x.plan_id=y.plan_id
WHERE DATE_PART('Year', x.start_date) > 2020
GROUP BY x.plan_id, y.plan_name
ORDER BY x.plan_id;
```

#### Explanation:

#### Output:
| plan_id |   plan_name   | count_of_events |
|:-------:|:-------------:|:---------------:|
|    1    | basic monthly |        8        |
|    2    |  pro monthly  |        60       |
|    3    |   pro annual  |        63       |
|    4    |     churn     |        71       |

#### Answer:
Based from the output, it can be observed that only 8 customers signed up after the year 2020 for the basic monthly plan of Foodie-Fi's streaming service, while 60 customers purchased the pro monthly subscription plan and 63 customers purchased the pro annual subscription plan. On the contrary, it can be seen that a large total of 71 customers opted to discontinue their subscription plan with Foodie-Fi which can be a cause for concern.

- - - -

4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
#### Query:
```sql
SELECT
    COUNT(DISTINCT customer_id) AS customer_churn_count,
    ROUND(100 * (COUNT(DISTINCT customer_id)::DECIMAL) / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 2) AS customer_churn_percentage
FROM foodie_fi.subscriptions
WHERE plan_id = 4;
```
#### Explanation:

#### Output:
| customer_churn_count | customer_churn_percentage |
|:--------------------:|:-------------------------:|
|          307         |           30.70           |

#### Answer:
Based from the output of the query, it can be observed that about 30.70% of customers, which is a total of 307 customers, churned from their subscription plan with Foodie-Fi's streaming service.

- - - -

5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
#### Query:
```sql
WITH customer_plan AS (
  SELECT
      customer_id,
      plan_id,
      start_date,
      LEAD(plan_id, 1) OVER(PARTITION BY customer_id ORDER BY customer_id) next_plan
  FROM foodie_fi.subscriptions
)

SELECT
  COUNT(DISTINCT customer_id) AS customer_churn_count,
  ROUND(100 * (COUNT(DISTINCT customer_id))::DECIMAL / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions)) AS customer_churn_percentage
FROM customer_plan
WHERE plan_id = 0 AND next_plan = 4;
```

#### Explanation:
<!--
https://www.postgresqltutorial.com/postgresql-window-function/postgresql-lead-function/#:~:text=PostgreSQL%20LEAD()%20function%20provide,next%20row%2C%20and%20so%20on.
-->

#### Output:
| customer_churn_count | customer_churn_percentage |
|:--------------------:|:-------------------------:|
|          92          |             9             |

#### Answer:
Based from the output of the query, it can be observed that about 9% of customers churned straight after their free trial with Foodie-Fi's streaming services.

- - - -

6. What is the number and percentage of customer plans after their initial free trial?
#### Query:
```sql
WITH customer_previous_plans AS (
  SELECT 
    x.customer_id,
    y.plan_name,
    x.start_date,
    LAG(x.plan_id, 1) OVER (PARTITION BY x.customer_id ORDER BY x.start_date) AS previous_plan
  FROM foodie_fi.subscriptions x
  JOIN foodie_fi.plans y
  ON x.plan_id=y.plan_id
  ORDER BY x.customer_id
)

SELECT
    plan_name,
    COUNT(DISTINCT customer_id) AS num_of_customers,
    ROUND(100 * (COUNT(DISTINCT customer_id))::DECIMAL / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 2) AS customer_percentage
FROM customer_previous_plans
WHERE previous_plan = 0
GROUP BY plan_name;
```
#### Explanation:

#### Output:
|   plan_name   | num_of_customers | customer_percentage |
|:-------------:|:----------------:|:-------------------:|
| basic monthly |        546       |        54.60        |
|     churn     |        92        |         9.20        |
|   pro annual  |        37        |         3.70        |
|  pro monthly  |        325       |        32.50        |

#### Answer:
Based from the output of the query, it can be observed that among the subscription plans offered by Foodie-Fi, the highest percentage of 54.60% of customers purchased a basic monthly subscription plan after their initial trial, followed by 32.50% of customers that purchased a pro monthly subscription plan. On the other hand, about 9.20% of customers churned after their trial. The least percentage of 3.70% of customers purchased a pro annual subscription plan after their trial.

- - - -

7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

8. How many customers have upgraded to an annual plan in 2020?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

### C. Challenge Payment Question <a href="anchor" id="challenge-payment-question"></a>
The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:

* monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
* upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
* upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
* once a customer churns they will no longer make payments

Example outputs for this table might look like the following:
| customer_id | plan_id |   plan_name   | payment_date | amount | payment_order |
|:-----------:|:-------:|:-------------:|:------------:|:------:|:-------------:|
|      1      |    1    | basic monthly |  2020-08-08  |  9.90  |       1       |
|      1      |    1    | basic monthly |  2020-09-08  |  9.90  |       2       |
|      1      |    1    | basic monthly |  2020-10-08  |  9.90  |       3       |
|      1      |    1    | basic monthly |  2020-11-08  |  9.90  |       4       |
|      1      |    1    | basic monthly |  2020-12-08  |  9.90  |       5       |
|      2      |    3    |   pro annual  |  2020-09-27  | 199.00 |       1       |
|      13     |    1    | basic monthly |  2020-12-22  |  9.90  |       1       |
|      15     |    2    |  pro monthly  |  2020-03-24  |  19.90 |       1       |
|      15     |    2    |  pro monthly  |  2020-04-24  |  19.90 |       2       |
|      16     |    1    | basic monthly |  2020-06-07  |  9.90  |       1       |
|      16     |    1    | basic monthly |  2020-07-07  |  9.90  |       2       |
|      16     |    1    | basic monthly |  2020-08-07  |  9.90  |       3       |
|      16     |    1    | basic monthly |  2020-09-07  |  9.90  |       4       |
|      16     |    1    | basic monthly |  2020-10-07  |  9.90  |       5       |
|      16     |    3    |   pro annual  |  2020-10-21  | 189.10 |       6       |
|      18     |    2    |  pro monthly  |  2020-07-13  |  19.90 |       1       |
|      18     |    2    |  pro monthly  |  2020-08-13  |  19.90 |       2       |
|      18     |    2    |  pro monthly  |  2020-09-13  |  19.90 |       3       |
|      18     |    2    |  pro monthly  |  2020-10-13  |  19.90 |       4       |
|      18     |    2    |  pro monthly  |  2020-11-13  |  19.90 |       5       |
|      18     |    2    |  pro monthly  |  2020-12-13  |  19.90 |       6       |
|      19     |    2    |  pro monthly  |  2020-06-29  |  19.90 |       1       |
|      19     |    2    |  pro monthly  |  2020-07-29  |  19.90 |       2       |
|      19     |    3    |   pro annual  |  2020-08-29  | 199.00 |       3       |

#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -
### D. Outside The Box Questions <a href="anchor" id="outside-the-box-questions"></a>
The following are open ended questions which might be asked during a technical interview for this case study - there are no right or wrong answers, but answers that make sense from both a technical and a business perspective make an amazing impression!
1. How would you calculate the rate of growth for Foodie-Fi?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

2. What key metrics would you recommend Foodie-Fi management to track over time to assess performance of their overall business?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

3. What are some key customer journeys or experiences that you would analyse further to improve customer retention?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

4. If the Foodie-Fi team were to create an exit survey shown to customers who wish to cancel their subscription, what questions would you include in the survey?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

5. What business levers could the Foodie-Fi team use to reduce the customer churn rate? How would you validate the effectiveness of your ideas?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:
