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
WHERE x.customer_id IN (1, 2, 11, 13, 15, 16, 18, 19)
ORDER BY x.customer_id, y.plan_id;
```
#### Explanation:
To get a clear picture of each customer's onboarding journey, a ```JOIN``` clause was used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the Customer ID and the Start Date details from the ```subscriptions``` table and the Plan Name and Price from the ```plans``` table. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table, so as to make the query more readable. In addition to that, a ```WHERE``` clause is applied to filter the results to show only details for customers with specifically the IDs of 1, 2, 11, 13, 15, 16, 18, and 19 to match the given table in the instructions for the Case Study. An ```ORDER BY``` statement was then also used to organize the results by default in ascending order according to the Customer ID and the Plan ID.

#### Output:
| customer_id |   plan_name   |  price |        start_date        |
|:-----------:|:-------------:|:------:|:------------------------:|
|      1      |     trial     |  0.00  | 2020-08-01T00:00:00.000Z |
|      1      | basic monthly |  9.90  | 2020-08-08T00:00:00.000Z |
|      2      |     trial     |  0.00  | 2020-09-20T00:00:00.000Z |
|      2      |   pro annual  | 199.00 | 2020-09-27T00:00:00.000Z |
|      11     |     trial     |  0.00  | 2020-11-19T00:00:00.000Z |
|      11     |     churn     |  null  | 2020-11-26T00:00:00.000Z |
|      13     |     trial     |  0.00  | 2020-12-15T00:00:00.000Z |
|      13     | basic monthly |  9.90  | 2020-12-22T00:00:00.000Z |
|      13     |  pro monthly  |  19.90 | 2021-03-29T00:00:00.000Z |
|      15     |     trial     |  0.00  | 2020-03-17T00:00:00.000Z |
|      15     |  pro monthly  |  19.90 | 2020-03-24T00:00:00.000Z |
|      15     |     churn     |  null  | 2020-04-29T00:00:00.000Z |
|      16     |     trial     |  0.00  | 2020-05-31T00:00:00.000Z |
|      16     | basic monthly |  9.90  | 2020-06-07T00:00:00.000Z |
|      16     |   pro annual  | 199.00 | 2020-10-21T00:00:00.000Z |
|      18     |     trial     |  0.00  | 2020-07-06T00:00:00.000Z |
|      18     |  pro monthly  |  19.90 | 2020-07-13T00:00:00.000Z |
|      19     |     trial     |  0.00  | 2020-06-22T00:00:00.000Z |
|      19     |  pro monthly  |  19.90 | 2020-06-29T00:00:00.000Z |
|      19     |   pro annual  | 199.00 | 2020-08-29T00:00:00.000Z |

#### Answer:
Based from the output of the query, it can be observed Customer 1 chose to sign up for an initial 7 day free trial of Foodie-Fi's streaming service on the 1st of August. It appears that Customer 1 was satisified with the streaming service because on the 8th of August, after the 7 day free trial, they immediately opted to purchase a basic monthly plan at the price of $9.90. Customer 2 also signed up for the initial 7 day free trial on the 20th of September and after a week, at the end of the trial, chose to purchase a pro annual subscription at the price of $199. As for Customers 13 and 16, they each signed up for the free trial and after a week, purchased a basic monthly plan. Given that basic monthly customers have limited access to Foodie-Fi's streaming services, both customers decided to upgrade their current subscription plan to a pro monthly plan at the price of $19.90 and a pro annual plan at the price of $199, respectively. Pro plan customers, compared to basic monthly plan customers, have no watch time limits and can also download videos for offline viewing. Both Customers 18 and 19 also subscribed to a pro monthly plan after their 7 day free trial, but Customer 19 further upgraded to a pro annual plan. On the other hand, it can be observed that Customer 15 also subscribed to a pro monthly plan after their 7 day free trial but after a month, they decided to cancel their subscription plan, while Customer 11 immediately churned after their 7 day free trial based on their churn plan records.

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
To determine the monthly distribution of customers joining Foodie-Fi's trial plan, first, a ```DATE_PART``` function was used to extract the month values from the starting date records of each of the customers in order to set ID numbers for each month. An *alias* of ```month_id``` was given to provide a more descriptive column name for the results. Second, a ```TO_CHAR``` function was used to convert the month values from the starting date records of each of the customers to string in order to display the name of the months. An *alias* of ```month_name``` was given to provide a more descriptive column name for the results. Third, a ```COUNT``` aggregate function, in conjunction with a ```DISTINCT``` statement, was then used to count the total number of unique customers that joined Foodie-Fi's trial plan for each month. An *alias* of ```monthly_distribution``` was given to provide a more descriptive column name for the results. Fourth, a ```JOIN``` clause was used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the extracted months from the starting date records and the total number of customers that joined the trial plan for each month. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table. Fifth, a ```WHERE``` clause was also used to filter the results according to only the customers that have joined the trial plan, with a plan ID of 0. Sixth, a ```GROUP BY``` statement was used to arrange the results into groups according to the IDs and the names of the months. Lastly, an ```ORDER BY``` statement was used to organize the results by default in ascending order according the IDs of the month.

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
To determine the event distribution for each plan after the year 2020, first, a ```COUNT``` aggregate function was used to count the total number of events for each plan. Second, a ```JOIN``` clause was used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the IDs and names of Foodie-Fi's subscription plans as well as the event distribution for each plan. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table. Third, a ```WHERE``` clause, in conjunction with a ```DATE_PART``` function, was used to extract and filter the records according to the condition that the event occurred after the year 2020. Fourth, a ```GROUP BY``` statement was used to arrange the results into groups according to the IDs and names of the subscription plans. Lastly, an ```ORDER BY``` statement was used to organize the results by default in ascending order according to the IDs of the plans.

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
    ROUND(100 * (COUNT(DISTINCT customer_id)::NUMERIC) / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 1) AS customer_churn_percentage
FROM foodie_fi.subscriptions
WHERE plan_id = 4;
```
#### Explanation:
To determine the customer count and percentage of those who have churned, first, a ```COUNT``` aggregate function, together with a ```DISTINCT``` statement, was used to count the total number of unique customers that have churned. An *alias* of ```customer_churn_count``` was given to provide a more descriptive column name for the results. Second, the customer churn percentage was calculated by dividing the total number of unique customers that have churned by the overall total number of unique customers of Foodie-Fi, and then multipled by 100. A ```ROUND``` function was also used to round off the results to 1 decimal place. An *alias* of ```customer_churn_percentage``` was given to provide a more descriptive column name for the results. Lastly, a ```WHERE``` clause was used to filter the records according to only customers that have churned which are assigned the Plan ID of 4.

#### Output:
| customer_churn_count | customer_churn_percentage |
|:--------------------:|:-------------------------:|
|          307         |           30.7            |

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
  ROUND(100 * (COUNT(DISTINCT customer_id))::NUMERIC / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions)) AS customer_churn_percentage
FROM customer_plan
WHERE plan_id = 0 AND next_plan = 4;
```

#### Explanation:
To determine the customer count and percentage of those that have churned straight after their initial free trial, first, a CTE labeled ```customer_plan``` was established to show each of the customers current subscription plan followed by their next plan with the use of a ```LEAD``` function. The ```LEAD``` function allows access to the data of next rows at a specified physical offset, so as to determine which of the customers churned straight after their initial free trial plan. An *alias* of ```next_plan``` was also given to provide a more descriptive column name for the results. 

Followed by this, first, a ```COUNT``` aggregate function, together with a ```DISTINCT``` statement, was used to count the total number of unique customers that have churned straight after their initial free trial plan. An *alias* of ```customer_churn_count``` was given to provide a more descriptive column name for the results. Second, to calculate the customer percentage, the total number of unique customers that churned after their trial plan was divided by the overall total number of unique customers of Foodie-Fi, then multipled by 100. A ```ROUND``` function was then used to round off the result to the nearest whole number. An *alias* of ```customer_churn_percentage``` was given to provide a more descriptive column name for the results. Lastly, a ```WHERE``` clause was used to filter the results according to customers that churned straight after their initial free trial plan, with the Plan IDs of 0 and 4.


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
    ROUND(100 * (COUNT(DISTINCT customer_id))::NUMERIC / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 2) AS customer_percentage
FROM customer_previous_plans
WHERE previous_plan = 0
GROUP BY plan_name;
```
#### Explanation:
To determine the count and percentage of customer plans after their initial free trial, first, a CTE labeled ```customer_previous_plans``` was established to display each of the customers current subscription plan as well as their previous plan with the use of a ```LAG``` function. The ```LAG``` function allows access to the data of previous rows at a specified physical offset. An *alias* of ```previous_plan``` was also given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the IDs of the customers, the name of their current and previous subscription plans, as well as the starting date they purchased the plan. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table. Lastly, an ```ORDER BY``` statement was used to organize the results by default in ascending order according to the Customer ID.

Followed by this, a ```COUNT``` aggregate function was used to count the total number of unique customers for each plan that initially joined Foodie-Fi's trial plan. An *alias* of ```num_of_customers``` was given to provide a more descriptive column name for the results. Then, to calculate the percentage of customer plans after their initial free trial, the total number of unique customers whose previous plan was the offered free trial divided by the overall total number of unique customers of Foodie-Fi, then multiplied by 100. A ```ROUND``` function was then used to round off the result by 2 decimal places. An *alias* of ```customer_percentage``` was given to provide a more descriptive column name for the results. A ```WHERE``` clause was also used to filter the results according to customers whose previous plan was Foodie-Fi's initial free trial plan. A ```GROUP BY``` statement was used to arrange the results into groups according to the subscription plans.

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
WITH customer_plan AS (
  SELECT
      x.customer_id,
      x.plan_id,
      y.plan_name,
      x.start_date,
      ROW_NUMBER() OVER (PARTITION BY x.customer_id ORDER BY x.start_date DESC) AS most_recent_plan
  FROM foodie_fi.subscriptions x
  JOIN foodie_fi.plans y
  ON x.plan_id=y.plan_id
  WHERE x.start_date <= '2020-12-31'
)

SELECT
  plan_id,
  plan_name,
  COUNT(DISTINCT customer_id) AS most_recent_plan,
  ROUND(100 * (COUNT(DISTINCT customer_id))::NUMERIC / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 2) AS customer_percentage
FROM customer_plan
WHERE latest_plan = 1
GROUP BY plan_id, plan_name;
```

#### Explanation:
To determine the customer count and percentage breakdown of all 5 subscription plans by the 31st of December in the year 2020, first, a CTE labeled ```customer_plans``` was established to show customer records pertaining to all of their purchased subscription plans, sequentially ordered from most recent to earlier plans up until the 31st of December in 2020. A ```ROW_NUMBER``` window function was used to assign the sequential integer to each purchased subscription plan, sorted in descending order from the latest plan to the earliest, for each customer record. An *alias* of ```most_recent_plan``` was given to provide a more descriptive column name for the results. A ```JOIN``` clause was also used to combine both the ```subscriptions``` table and the ```plans``` table based on their related column, ```plan_id```, to display the IDs of the customers, the IDs and names of all their purchased subscription plans, as well as the starting date for each of their purchased plan. *Aliases* were also given, i.e. ```x``` for the ```subscriptions``` table and ```y``` for the ```plans``` table. A ```WHERE``` clause was also used to filter the records based on the condition that the plan was purchased by the customer by the specified date.

Followed by this, a ```COUNT``` aggregate function, in conjunction with a ```DISTINCT``` statement, was used to count the total number of unique customers that has a record of purchasing a subscription plan by the specified date. To calculate the percentage of customer distribution, the total number of unique customers that purchased a subscription plan by the specified date was divided by the overall total number of unique customers of Foodie-Fi, then multiplied by 100. A ```ROUND``` function was used to round off the result by 2 decimal places. An *alias* of ```customer_percentage``` was given to provide a more descriptive column name for the results. Moreover, a ```WHERE``` clause was also used to filter the records based on only their current latest purchased plan. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the IDs and names of the subscription plans.

#### Output:
| plan_id |   plan_name   | num_of_customers | customer_percentage |
|:-------:|:-------------:|:----------------:|:-------------------:|
|    0    |     trial     |        19        |         1.90        |
|    1    | basic monthly |        224       |        22.40        |
|    2    |  pro monthly  |        326       |        32.60        |
|    3    |   pro annual  |        195       |        19.50        |
|    4    |     churn     |        236       |        23.60        |

#### Answer:
Based from the output of the query, it can be observed that on or before the 31st of December of 2020, the highest percentage of 32.60% of Foodie-Fi customers were subscribed to a pro monthly plan. However, the second highest percentage of 23.60% is associated with churned customers. Following closely behind is a percentage of about 22.40% of Foodie-Fi customers were subscribed to a basic monthly plan. The least percentage of 1.90% of Foodie-Fi customers were signed up for the initial 7 day free trial plan of the streaming platform. 

- - - -

8. How many customers have upgraded to an annual plan in 2020?
#### Query:
```sql
SELECT
     COUNT(DISTINCT customer_id) AS annual_plan_upgrade_count
FROM foodie_fi.subscriptions
WHERE plan_id = 3 AND DATE_PART('Year', start_date) = '2020';
```

#### Explanation:
To determine the number of customers that upgraded to an annual plan in the year 2020, first, a ```COUNT``` aggregate function, in conjunction with a ```DISTINCT``` statement, was used to count the total number of unique customers that upgraded to an annual plan in 2020. An *alias* of ```annual_plan_upgrade_count``` was given to provide a more descriptive column name for the results. Second, a ```WHERE``` clause, together with the ```DATE_PART``` function, was used to filter the records according to those who upgraded to an annual plan, with the ID of 3, in the year 2020.

#### Output:
| annual_plan_upgrade_count |
|:-------------------------:|
|            195            |

#### Answer:
Based from the output of the query, it can be observed that a total of 195 customers upgraded to an annual subscription plan in 2020.

- - - -
<!-- continue here! -->
9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
#### Query:
```sql
WITH trial_plan AS (
  SELECT *
  FROM foodie_fi.subscriptions
  WHERE plan_id = 0
),

annual_plan AS (
  SELECT *
  FROM foodie_fi.subscriptions
  WHERE plan_id = 3
)

SELECT
  ROUND(AVG(y.start_date - x.start_date), 0) AS avg_time_upgrade
FROM trial_plan x
JOIN annual_plan y
ON x.customer_id=y.customer_id;
```

#### Explanation:

#### Output:
| avg_time_upgrade |
|:----------------:|
|        105       |

#### Answer:
Based from the output of the query, it takes about an average of 105 days for a customer to upgrade to an annual plan subscription since they signed up for Foodie-Fi.

- - - -

10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
#### Query:
```sql
WITH trial_plan AS (
  SELECT *
  FROM foodie_fi.subscriptions
  WHERE plan_id = 0
),

annual_plan AS (
  SELECT *
  FROM foodie_fi.subscriptions
  WHERE plan_id = 3
),

thirty_day_period_plans AS (
  SELECT 
      x.customer_id,
      x.plan_id AS trial_plan_id,
      x.start_date AS trial_plan_start_date,
      y.plan_id AS annual_plan_id_upgrade,
      y.start_date AS annual_plan_start_date,
      WIDTH_BUCKET(y.start_date - x.start_date, 0, 365, 12) AS thirty_day_period_upgrade
  FROM trial_plan x
  JOIN annual_plan y
  ON x.customer_id=y.customer_id
  ORDER BY x.customer_id
)

SELECT
  CONCAT((thirty_day_period_upgrade - 1) * 30, '-', (thirty_day_period_upgrade * 30)) AS thirty_day_period_buckets,
  COUNT(DISTINCT customer_id) AS customer_count_upgrade
FROM thirty_day_period_plans
GROUP BY thirty_day_period_upgrade
ORDER BY thirty_day_period_upgrade;
```

#### Explanation:
<!-- https://www.sqliz.com/postgresql-ref/width_bucket/ -->

#### Output:
| thirty_day_period_buckets | customer_count_upgrade |
|:-------------------------:|:----------------------:|
|            0-30           |           49           |
|           30-60           |           24           |
|           60-90           |           35           |
|           90-120          |           35           |
|          120-150          |           43           |
|          150-180          |           37           |
|          180-210          |           24           |
|          210-240          |            4           |
|          240-270          |            4           |
|          270-300          |            1           |
|          300-330          |            1           |
|          330-360          |            1           |

#### Answer:
Based from the output of the query, it can be observed that about 49 customers chose to upgrade from a basic monthly plan to an annual plan within the 1st 30-day period, while 24 customers purchased an upgrade in the 2nd 30-day period. There was a slight increase in upgrading from a basic monthly plan to an annual plan among customers within the 3rd and 4th 30-day periods, each with a total of 35 customers. It further increased by the 5th 30-day period with a total of 43 customers. Followed by that, 37 customers upgraded within the 6th 30-day period and 24 customers upgraded within the 7th 30-day period. However, there was a huge decline of subscription plan upgrade around the latter part of the year. 
- - - -

11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
#### Query:
```sql
WITH customer_plans AS (
  SELECT
      *,
      LEAD(plan_id, 1) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_plan
  FROM foodie_fi.subscriptions
  WHERE DATE_PART('Year', start_date) = '2020'
)

SELECT
   COUNT(DISTINCT customer_id) AS downgrade_plan_count
FROM customer_plans
WHERE plan_id = 2 AND next_plan = 1;
```

#### Explanation:

#### Output:
| downgrade_plan_count |
|:--------------------:|
|           0          |

#### Answer:
Based from the output of the query, it can be observed that none of the customers downgraded from a pro monthly plan to a basic monthly plan in 2020.

- - - -

### C. Challenge Payment Question <a href="anchor" id="challenge-payment-question"></a> *-skipped for now-*
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

-- DROP TABLE IF EXISTS payments_2020; 

-- CREATE TABLE payments_2020 (
--   customer_id INTEGER,
--   plan_id INTEGER,
--   plan_name VARCHAR(13),
--   payment_date DATE,
--   amount DECIMAL(5,2),
--   payment_order INTEGER
-- );

-- INSERT INTO foodie_fi.payments_2020 (customer_id, plan_id, plan_name, payment_date, amount, payment_order)
-- SELECT
-- 	x.customer_id,
--     x.plan_id,
--     y.plan_name,
--     x.start_date AS payment_date,
--     y.price AS amount,
--     ROW_NUMBER() OVER (PARTITION BY x.customer_id ORDER BY x.start_date) AS payment_order
-- FROM foodie_fi.subscriptions x
-- JOIN foodie_fi.plans y
-- ON x.plan_id=y.plan_id
-- WHERE DATE_PART('Year', x.start_date) = 2020 AND x.plan_id IN (1, 2, 3) AND x.customer_id = 1
-- ORDER BY x.customer_id;



-- INSERT INTO foodie_fi.payments_2020 (customer_id, plan_id, plan_name, payment_date, amount, payment_order)
-- SELECT
-- 	x.customer_id,
--     x.plan_id,
--     x.plan_name,
--     CASE
--     	WHEN x.payment_order = 1 THEN x.payment_date
--         ELSE x.payment_date + INTERVAL '1 month'
--     END AS payment_date,
--     x.amount,
--     x.payment_order
-- FROM foodie_fi.payments_2020 x                        
-- WHERE x.customer_id = 1;

-- SELECT *
-- FROM foodie_fi.payments_2020;


-- progress! (actual recursive function)
-- WITH RECURSIVE date_cte AS (
--   SELECT
--   	customer_id,
--   	plan_id,
--   	start_date AS payment_date
--   FROM foodie_fi.subscriptions
--   
--   UNION ALL
--   
--   SELECT
--   	customer_id,
--   	plan_id,
--   	(payment_date + INTERVAL '1 month')::DATE
--   FROM date_cte
--   WHERE DATE_PART('Year', payment_date) = '2020'
-- )

-- SELECT *
-- FROM date_cte
-- WHERE customer_id = 1 AND plan_id IN (1, 2, 3);

```
#### Explanation:
#### Output:
#### Answer:

- - - -
### D. Outside The Box Questions <a href="anchor" id="outside-the-box-questions"></a>
The following are open ended questions which might be asked during a technical interview for this case study - there are no right or wrong answers, but answers that make sense from both a technical and a business perspective make an amazing impression!
1. How would you calculate the rate of growth for Foodie-Fi?
#### Answer:
To calculate the rate of growth for Foodie-Fi, there are 2 possible metrics that can be considered: user acquisition growth rate and revenue growth rate. First, keeping track of the business' user acquisition growth rate is beneficial because it can help assess whether there is an upward trend or a declining trend overtime in the number of customers subscribing to Foodie-Fi's streaming service. This determines whether people are enjoying their usage of Foodie-Fi's streaming platform or not. Second, it is also important to assess whether Foodie-Fi's revenue has grown or shrunk over time, whether there is an increase or decrease of sales with the business. This can help determine the business' financial health. Overall, both growth rate metrics can help the Foodie-Fi team gain insight into the success of their business.

- - - -

2. What key metrics would you recommend Foodie-Fi management to track over time to assess performance of their overall business?
#### Answer:
Some key metrics I would recommend Foodie-Fi management to keep track of for performance assessment of their business include their monthly recurring revenue (MRR), annual recurring revenue (ARR), churn rate, revenue churn, and customer lifetime value (CLV). First, keeping track of their monthly recurring revenue (MRR) would be beneficial in gaining insight about the business' monthly revenue based on all active subscriptions. Second, keeping track of their annual recurring revenue (ARR) would also help the business obtain an overview of their yearly revenue based on all active subscriptions and see year-over-year trends in order to set goals for business growth. Third, churn rate would also be important to assess which would help Foodie-Fi management gain insight on the percentage of customers that terminate their subscription plans in a given billing period. This would also help Foodie-Fi identify whether their streaming platform has been gaining more subscribers or losing them, which can be a cause for concern. Foodie-Fi could also try to gather data on customers' reasons for churning to get an idea of key factors that contribute to their decision and further improve the business' future customer retention actions. Fourth, revenue churn can also be assessed by the Foodie-Fi management to determine the percentage of lost revenue from existing subscribers in a given period. This would help Foodie-Fi management gain an overview of the financial impact of customers that terminate their subscription plans with their service. Lastly, Foodie-Fi management can also assess customer lifetime value (CLV) which essentially determines customer loyalty and maximizes the value of every customer relationship with the business.

- - - -

3. What are some key customer journeys or experiences that you would analyse further to improve customer retention?
#### Answer:
I think it would be important to analyze customer journeys pertaining to those that churn or rather, those that terminate their subscription plans with Foodie-Fi's streaming service. It would be helpful for Foodie-Fi to improve customer retention by identifying why customers choose to churn after a particular subscription plan, whether they were currently subscribed to a trial plan, a basic plan, or a pro plan. This would help them determine what certain features that customers liked or disliked with each subscription plan in order to improve their overall customer experience and retention.

- - - -

4. If the Foodie-Fi team were to create an exit survey shown to customers who wish to cancel their subscription, what questions would you include in the survey?
#### Answer:
Some of the questions I would include in the exit survey are:
* How was your onboarding experience?
* Based on your overall experience, how satisfied are you with Foodie-Fi's streaming platform?
* How likely will you recommend Foodie-Fi to your friends and colleagues?
* What issues did you experience with Foodie-Fi's streaming service that prompted you to cancel your subscription plan?
* How can we make our streaming services better? Please share any suggestions to improve the product.

- - - -

5. What business levers could the Foodie-Fi team use to reduce the customer churn rate? How would you validate the effectiveness of your ideas?
#### Answer:
Some business levers that the Foodie-Fi team could use to reduce the customer churn rate would be to, first, conduct regular surveys of their subscriber base to gain insights on how customers are using their product, what features they would like to be added, and what kind of issues or challenges have they experienced with the platform. Second, send exit surveys via e-mail to customers to gain feedback on their overall experience with the product. This helps the Foodie-Fi team to analyze why the subscriber churn is happening. Third, it would also be useful if the Foodie-Fi team could host product demos or video tutorials to assist customers that are experiencing difficulties with their platform, especially during a customer's onboarding process. It would be helpful on the customer's end to have any documentation or tutorials they can refer to for setting up the product and navigating through the platform. Fourth, in conjunction with the product demos and video tutorials, it would also be effective to have an active customer service that subscribers can reach out for support. Fifth, Foodie-Fi could also employ a loyalty program to offer its most valuable customers rewards, discounts, or other special incentives to encourage repeat business. 
