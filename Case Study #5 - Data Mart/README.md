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
WITH week_range_in_sales AS (
  SELECT DISTINCT week_number
  FROM clean_weekly_sales
),

week_count_in_year AS (
  SELECT GENERATE_SERIES(1, 52) AS week_no
)

SELECT 
     y.week_no AS missing_weeks
FROM week_range_in_sales x
RIGHT JOIN week_count_in_year y
ON x.week_number = y.week_no
WHERE x.week_number IS NULL;
```
#### Explanation:
To determine the range of week numbers missing from the dataset, 2 CTEs were used. The 1st CTE labeled ```week_range_in_sales``` returns all unique records of week numbers from the ```clean_weekly_sales```. This returns all the week numbers that are currently present in the dataset. On the other hand, the 2nd CTE labeled ```week_count_in_year``` utilizes a ```GENERATE_SERIES``` function. The ```GENERATE_SERIES``` function generates a sequence of numbers within a given interval. This function is then used in the 2nd CTE to produce a result set of the average number of weeks in a normal year based on the interval 1 to 52. In the final query, a ```RIGHT JOIN``` is then used to combine both the resulting tables of the CTEs, returning *all* the records from the ```week_count_in_year``` table and the matching records in the ```week_range_in_sales``` table. Aliases were also given, i.e. ```x``` for ```week_range_in_sales``` table and ```y``` for ```week_count_in_year``` table, to make the query more readable. Lastly, a ```WHERE``` clause was used to filter out all the records of week numbers in the ```week_range_in_sales``` table with a value of ```NULL```. The final result set then displays all the week numbers that were not present in the ```week_range_in_sales``` which are missing week numbers from the dataset.

#### Output:
| missing_weeks |
|:-------------:|
|       1       |
|       2       |
|       3       |
|       4       |
|       5       |
|       6       |
|       7       |
|       8       |
|       9       |
|       10      |
|       11      |
|       12      |
|       37      |
|       38      |
|       39      |
|       40      |
|       41      |
|       42      |
|       43      |
|       44      |
|       45      |
|       46      |
|       47      |
|       48      |
|       49      |
|       50      |
|       51      |
|       52      |

#### Answer:
Based from the output of the query, it can be observed that a total of 28 week numbers are missing from the dataset, ranging from weeks 1-12 and 37-52.

- - - -

3. How many total transactions were there for each year in the dataset?
#### Query:
```sql
SELECT
    calendar_year,
    SUM(transactions) AS total_transactions
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year;
```
#### Explanation:
To determine the total number of transactions that customers of Data Mart have made per year, a ```SUM``` aggregate function was used to 
calculate the total count of transactions. A ```GROUP BY``` statement is then used to arrange the results into groups according to each year. Lastly, an ```ORDER BY``` statement is used to sort the results in chronological order.

#### Output:
| calendar_year | total_transactions |
|:-------------:|:------------------:|
|      2018     |      346406460     |
|      2019     |      365639285     |
|      2020     |      375813651     |

#### Answer:
Based from the output of the query, it can be observed that Data Mart had a total number of 346,406,460 transactions in 2018, 365,639,285 in 2019, and 375,813,651 in 2020.

- - - -

4. What is the total sales for each region for each month?
#### Query:
```sql
SELECT
     region,
     TO_CHAR(TO_DATE(month_number::TEXT, 'MM'), 'Month') AS month_name,
     SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY region, month_number
ORDER BY region, month_number;
```

#### Explanation:
To determine the total sales for each region for each month, first, a ```TO_CHAR``` function, together with a ```TO_DATE``` function, was used to convert the ```month_number``` attribute to its corresponding month name to make the results more easily understandable. An *alias* of ```month_name``` was also given to provide a more descriptive column name for the results. Second, a ```SUM``` aggregate function was then used to calculate the total sales based on the ```sales``` column. Third, a ```GROUP BY``` statement is then used to arrange the results into groups according to the region and the month. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the region name and the month number.

#### Output:
|     region    | month_name | total_sales |
|:-------------:|:----------:|:-----------:|
|     AFRICA    |    March   |  567767480  |
|     AFRICA    |    April   |  1911783504 |
|     AFRICA    |     May    |  1647244738 |
|     AFRICA    |    June    |  1767559760 |
|     AFRICA    |    July    |  1960219710 |
|     AFRICA    |   August   |  1809596890 |
|     AFRICA    |  September |  276320987  |
|      ASIA     |    March   |  529770793  |
|      ASIA     |    April   |  1804628707 |
|      ASIA     |     May    |  1526285399 |
|      ASIA     |    June    |  1619482889 |
|      ASIA     |    July    |  1768844756 |
|      ASIA     |   August   |  1663320609 |
|      ASIA     |  September |  252836807  |
|     CANADA    |    March   |  144634329  |
|     CANADA    |    April   |  484552594  |
|     CANADA    |     May    |  412378365  |
|     CANADA    |    June    |  443846698  |
|     CANADA    |    July    |  477134947  |
|     CANADA    |   August   |  447073019  |
|     CANADA    |  September |   69067959  |
|     EUROPE    |    March   |   35337093  |
|     EUROPE    |    April   |  127334255  |
|     EUROPE    |     May    |  109338389  |
|     EUROPE    |    June    |  122813826  |
|     EUROPE    |    July    |  136757466  |
|     EUROPE    |   August   |  122102995  |
|     EUROPE    |  September |   18877433  |
|    OCEANIA    |    March   |  783282888  |
|    OCEANIA    |    April   |  2599767620 |
|    OCEANIA    |     May    |  2215657304 |
|    OCEANIA    |    June    |  2371884744 |
|    OCEANIA    |    July    |  2563459400 |
|    OCEANIA    |   August   |  2432313652 |
|    OCEANIA    |  September |  372465518  |
| SOUTH AMERICA |    March   |   71023109  |
| SOUTH AMERICA |    April   |  238451531  |
| SOUTH AMERICA |     May    |  201391809  |
| SOUTH AMERICA |    June    |  218247455  |
| SOUTH AMERICA |    July    |  235582776  |
| SOUTH AMERICA |   August   |  221166052  |
| SOUTH AMERICA |  September |   34175583  |
|      USA      |    March   |  225353043  |
|      USA      |    April   |  759786323  |
|      USA      |     May    |  655967121  |
|      USA      |    June    |  703878990  |
|      USA      |    July    |  760331754  |
|      USA      |   August   |  712002790  |
|      USA      |  September |  110532368  |

#### Answer:
Based from the output of the query, it can be observed that each of the 7 regions (Africa, Asia, Canada, Europe, Oceania, South America, USA) all have records of total sales throughout the months of March to September.

- - - -

5. What is the total count of transactions for each platform?
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
