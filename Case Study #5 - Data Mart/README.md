# Case Study #5: Data Mart

## Introduction
Data Mart is Danny’s latest venture and after running international operations for his online supermarket that specialises in fresh produce - Danny is asking for your support to analyse his sales performance.

In June 2020 - large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.

Danny needs your help to quantify the impact of this change on the sales performance for Data Mart and its separate business areas.

## Problem Statement
Danny wants to analyze the impact of the large scale supply changes he implemented on June 2020 on the sales performance for Data Mart. He wants to answer the following *key business questions*:
* What was the quantifiable impact of the changes introduced in June 2020?
* Which platform, region, segment and customer types were the most impacted by this change?
* What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?

## Entity Relationship Diagram
![Capture 5](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/3261706f-ed32-4807-b9e6-1d2efb4ee27d)

## Table of Contents
1. [Data Cleaning](#data-cleaning)
2. [Data Exploration](#data-exploration)
3. [Before & After Analysis](#before-after-analysis)
4. [Bonus Question](#bonus-question)

## Data Cleaning <a href="anchor" id="data-cleaning"></a>

Prior to data cleaning, the following query was executed to get a glimpse of the original ```weekly_sales``` table and its records.
#### Query:
```sql
SELECT *
FROM data_mart.weekly_sales
LIMIT 10;
```

#### Before Data Cleaning:
| week_date | region | platform | segment | customer_type | transactions |   sales  |
|:---------:|:------:|:--------:|:-------:|:-------------:|:------------:|:--------:|
|  31/8/20  |  ASIA  |  Retail  |    C3   |      New      |    120631    |  3656163 |
|  31/8/20  |  ASIA  |  Retail  |    F1   |      New      |     31574    |  996575  |
|  31/8/20  |   USA  |  Retail  |   null  |     Guest     |    529151    | 16509610 |
|  31/8/20  | EUROPE |  Retail  |    C1   |      New      |     4517     |  141942  |
|  31/8/20  | AFRICA |  Retail  |    C2   |      New      |     58046    |  1758388 |
|  31/8/20  | CANADA |  Shopify |    F2   |    Existing   |     1336     |  243878  |
|  31/8/20  | AFRICA |  Shopify |    F3   |    Existing   |     2514     |  519502  |
|  31/8/20  |  ASIA  |  Shopify |    F1   |    Existing   |     2158     |  371417  |
|  31/8/20  | AFRICA |  Shopify |    F2   |      New      |      318     |   49557  |
|  31/8/20  | AFRICA |  Retail  |    C3   |      New      |    111032    |  3888162 |

- - - -
In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

To generate a new table in the data_mart schema named ```clean_weekly_sales```, the following query was executed:
#### Query:
```sql
CREATE TEMPORARY TABLE clean_weekly_sales AS
SELECT *
FROM data_mart.weekly_sales;
```
#### Explanation:
A temporary table named ```clean_weekly_sales``` was created whose structure is based on the already available ```weekly_sales``` table in the ```data_mart``` schema. Temporary tables are useful when processing and manipulating data without affecting the original data.

- - - -
The following operations are then performed to clean the data:

1. Convert the week_date to a DATE format
#### Before Data Cleaning:
* #### Query for checking data types:
```sql
SELECT
     column_name,
     data_type
 FROM
     information_schema.columns
 WHERE
     table_name = 'weekly_sales';
```
* #### Output:
|  column_name  |     data_type     |
|:-------------:|:-----------------:|
|  transactions |      integer      |
|     sales     |      integer      |
|    platform   | character varying |
|   week_date   | character varying |
| customer_type | character varying |
|    segment    | character varying |
|     region    | character varying |

#### Query:
```sql

```

#### Explanation:

#### After Data Cleaning:
* #### Query for checking data types:
```sql

```
* #### Output:

- - - -

2. Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

3. Add a month_number with the calendar month for each week_date value as the 3rd column
#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

4. Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values
#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

5. Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value
| segment | age_band     |
|---------|--------------|
| 1       | Young Adults |
| 2       | Middle Aged  |
| 3 or 4  | Retirees     |

#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

6. Add a new demographic column using the following mapping for the first letter in the segment values:
| segment | demographic |
|---------|-------------|
| C       | Couples     |
| F       | Families    |

#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

7. Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -

8. Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record
#### Query:
```sql

```
#### Explanation:

#### After Data Cleaning:

- - - -
## Case Study Questions and Answers
### A. Data Exploration <a href="anchor" id="data-exploration"></a>
1. What day of the week is used for each week_date value?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. What range of week numbers are missing from the dataset?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. How many total transactions were there for each year in the dataset?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

4. What is the total sales for each region for each month?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

5. What is the total count of transactions for each platform
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

6. What is the percentage of sales for Retail vs Shopify for each month?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

7. What is the percentage of sales by demographic for each year in the dataset?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

8. Which age_band and demographic values contribute the most to Retail sales?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

### B. Before & After Analysis <a href="anchor" id="before-after-analysis"></a>
This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the week_date value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.

We would include all week_date values for 2020-06-15 as the start of the period after the change and the previous week_date values would be before

Using this analysis approach - answer the following questions:
1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. What about the entire 12 weeks before and after?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?
#### Query:
```sql

```
#### Explanation:

#### Output:

#### Answer:

- - - -

## Bonus Question <a href="anchor" id="bonus-question"></a>
Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?
* region
* platform
* age_band
* demographic
* customer_type

Do you have any further recommendations for Danny’s team at Data Mart or any interesting insights based off this analysis?
