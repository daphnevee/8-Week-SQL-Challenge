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
```
#### Explanation:
#### Output:
#### Answer:

- - - -
### Data Analysis Questions <a href="anchor" id="data-analysis-questions"></a>
1. How many customers has Foodie-Fi ever had?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

6. What is the number and percentage of customer plans after their initial free trial?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

- - - -

7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
#### Query:
```sql
```
#### Explanation:
#### Output:
#### Answer:

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
### B. Challenge Payment Question <a href="anchor" id="challenge-payment-question"></a>
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
### C. Outside The Box Questions <a href="anchor" id="outside-the-box-questions"></a>
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
