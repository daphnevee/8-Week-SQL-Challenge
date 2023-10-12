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
In a single query, perform the following operations and generate a new table in the data_mart schema named ```clean_weekly_sales```:
* Convert the week_date to a DATE format
* Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
* Add a month_number with the calendar month for each week_date value as the 3rd column
Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values
* Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

    | segment | age_band     |
    |---------|--------------|
    | 1       | Young Adults |
    | 2       | Middle Aged  |
    | 3 or 4  | Retirees     |

* Add a new demographic column using the following mapping for the first letter in the segment values:

    | segment | demographic |
    |---------|-------------|
    | C       | Couples     |
    | F       | Families    |

* Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
* Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

#### Before Data Cleaning: 
To get a glimpse of the original ```weekly_sales``` table prior to data cleaning, the number of records to be displayed were only limited to 10.

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

#### Query:
```sql
DROP TABLE IF EXISTS clean_weekly_sales;

CREATE TEMPORARY TABLE clean_weekly_sales AS
SELECT 
    TO_DATE(week_date, 'DD/MM/YY') AS week_date,
    DATE_PART('week', TO_DATE(week_date, 'DD/MM/YY')) AS week_number,
    DATE_PART('month',TO_DATE(week_date, 'DD/MM/YY')) AS month_number,
    DATE_PART('year', TO_DATE(week_date, 'DD/MM/YY')) AS calendar_year,
    region,
    platform,
    CASE
        WHEN segment IS NULL OR segment = 'null' THEN 'Unknown'
        ELSE segment
    END AS segment,
    CASE
        WHEN SUBSTRING(segment, 2, 1) = '1' THEN 'Young Adults'
        WHEN SUBSTRING(segment, 2, 1) = '2' THEN 'Middle Aged'
        WHEN SUBSTRING(segment, 2, 1) IN ('3', '4') THEN 'Retirees'
        ELSE 'Unknown'
    END AS age_band,
    CASE
        WHEN SUBSTRING(segment, 1, 1) = 'C' THEN 'Couples'
        WHEN SUBSTRING(segment, 1, 1) = 'F' THEN 'Families'
        ELSE 'Unknown'
    END AS demographic,
    customer_type,
    transactions,
    sales,
    ROUND((sales::NUMERIC / transactions), 2) AS avg_transaction
FROM data_mart.weekly_sales;
```

#### Explanation:
Prior to performing the data cleaning operations, the new table named ```clean_weekly_sales``` is initially generated. First, a ```DROP TABLE IF EXISTS``` statement was used to drop or delete a table named ```clean_weekly_sales``` from the ```data_mart``` database if it exists. Second, a temporary table named ```clean_weekly_orders``` was then created (now checked that there is no available table with the same name) whose structure is based on the already available ```weekly_sales``` table in the database. Temporary tables are useful when processing and manipulating data without affecting the original data. 

Now to proceed with the necessary operations for the data cleaning process, first, the ```TO_DATE``` function was used to convert the data type of the ```week_date``` from ```VARCHAR(7)``` or string to a date value based on the given format, ```DD/MM/YY```. Second, to extract the week number, month number, and year from the ```week_date``` column, the ```DATE_PART``` function was used. *Aliases* were also given, i.e. ```week_number``` for the extracted week value, ```month_number``` for the extracted month value, and ```calendar_year``` for the extracted year. Third, a ```CASE``` expression, in conjunction with a ```SUBSTRING``` function, was used to map each value from the ```segment``` column to its equivalent age band. The ```SUBSTRING``` function extracts a string containing a specific number of characters from a particular position of a given string. The first condition checks if the segment value is 1 and maps it to the age band of "Young Adults", a segment value of 2 is then mapped to the age band of "Middle Aged" and lastly, a segment value of either 3 or 4 is mapped to the age band of "Retirees". Otherwise, if there is no specified segment value, it would simply equate to "Unknown". Similarly, a ```CASE``` expression along with the ```SUBSTRING``` function was used to map each letter from the ```segment``` column to its equivalent demographic. The first condition checks if the segment letter is C and maps it to the demographic of "Couples" while a segment letter of F is mapped to the demographic of "Families". Otherwise, if there is no specified segment letter, it would equate to "Unknown". For the ```segment``` column, since there are records without a specified value, a ```CASE``` expression is also used to transform all the null values to the string value, "Unknown". This operation was also applied to the newly added columns for ```age_band``` and ```demographic```. Lastly, the values in the ```sales``` column are divided by the values in the ```transactions``` column in order to calculate the average transactions for each record. A ```ROUND``` function is used to round off the result to 2 decimal places. An *alias* of ```avg_transaction``` is assigned as specified in the instructions to provide a more descriptive column name for the results.

#### After Data Cleaning:
To get a glimpse of the ```clean_weekly_sales``` table after data cleaning, the number of records to be displayed were only limited to 10.

|         week_date        | week_number | month_number | calendar_year | region | platform | segment |   age_band   | demographic | customer_type | transactions |   sales  | avg_transaction |
|:------------------------:|:-----------:|:------------:|:-------------:|:------:|:--------:|:-------:|:------------:|:-----------:|:-------------:|:------------:|:--------:|:---------------:|
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     |  ASIA  |  Retail  |    C3   |   Retirees   |   Couples   |      New      |    120631    |  3656163 |      30.31      |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     |  ASIA  |  Retail  |    F1   | Young Adults |   Families  |      New      |     31574    |  996575  |      31.56      |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     |   USA  |  Retail  | Unknown |    Unknown   |   Unknown   |     Guest     |    529151    | 16509610 |      31.20      |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | EUROPE |  Retail  |    C1   | Young Adults |   Couples   |      New      |     4517     |  141942  |      31.42      |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | AFRICA |  Retail  |    C2   |  Middle Aged |   Couples   |      New      |     58046    |  1758388 |      30.29      |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | CANADA |  Shopify |    F2   |  Middle Aged |   Families  |    Existing   |     1336     |  243878  |      182.54     |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | AFRICA |  Shopify |    F3   |   Retirees   |   Families  |    Existing   |     2514     |  519502  |      206.64     |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     |  ASIA  |  Shopify |    F1   | Young Adults |   Families  |    Existing   |     2158     |  371417  |      172.11     |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | AFRICA |  Shopify |    F2   |  Middle Aged |   Families  |      New      |      318     |   49557  |      155.84     |
| 2020-08-31T00:00:00.000Z |      36     |       8      |      2020     | AFRICA |  Retail  |    C3   |   Retirees   |   Couples   |      New      |    111032    |  3888162 |      35.02      |

- - - -
## Case Study Questions and Answers
### A. Data Exploration <a href="anchor" id="data-exploration"></a>
1. What day of the week is used for each week_date value?
#### Query:
```sql
SELECT DISTINCT
    TO_CHAR(week_date, 'Day') AS day_of_the_week
FROM clean_weekly_sales;
```

#### Explanation:
To determine the day of the week commonly used for each of the records in the ```week_date``` column, first, a ```TO_CHAR``` function was used to convert the timestamp value, in this case the name of the day, to a string according to the specified format. Second, a ```DISTINCT``` keyword was also used to filter out duplicates and return only unique records. An *alias* of ```day_of_the_week``` was also given to provide a more descriptive column name for the results.

#### Output:
| day_of_the_week |
|:---------------:|
|      Monday     |

#### Answer:
Based from the output of the query, it can be observed that they chose Monday to be the start of every sales week for Data Mart.

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
