# Case Study #2: Pizza Runner

## Introduction
Did you know that over **115 million kilograms** of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway...)

Danny was scrolling through his Instagram feed when something really caught his eye - "80s Retro Styling and Pizza Is The Future!"

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to *Uberize* it - and so Pizza Runner was launched!

Danny started by recruiting "runners" to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny's house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

## Problem Statement
Danny requires further assistance to clean the data he has collected and apply some basic calculations so he can better direct his runners and optimise Pizza Runner's operations. 

## Entity Relationship Diagram
![Capture 2](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/f55d5b54-fd9e-4db1-9dfc-4b269a93b969)

## Table of Contents
* [Data Cleaning](#data-cleaning)
	* [Table 2: customer_orders](#table-2)
	* [Table 3: runner_orders](#table-3)
	* [Table 5: pizza_recipes](#table-5)
* [Pizza Metrics](#pizza-metrics)
* [Runner and Customer Experience](#runner-customer-exp)
* [Ingredient Optimization](#ingredient-optimization)
* [Pricing and Ratings](#pricing-ratings)
* [Bonus Questions](#bonus-questions)

## Data Cleaning <a href="anchor" id="data-cleaning"></a>
### Table 2: customer_orders <a href="anchor" id="table-2"></a>
1. Replacing null and NaN values with empty strings

In the ```customer_orders``` table, it can be observed that both the ```exclusions``` and ```extras``` columns contain several null and NaN values. Thus, these columns will need to be cleaned up before being used for queries.

The data cleaning approach to be taken in this scenario would be to replace both the null and NaN values with empty strings. Empty strings are better suited in representing the data because these can imply that the customer did not specifically request for any exclusions or extra toppings on the pizza they ordered, in comparison to null or NaN values which imply a more ambiguous unknown value. 

#### Before Data Cleaning:
| order_id | customer_id | pizza_id | exclusions | extras |      order_time     |
|:--------:|:-----------:|:--------:|:----------:|:------:|:-------------------:|
|     1    |     101     |     1    |            |        | 2021-01-01 18:05:02 |
|     2    |     101     |     1    |            |        | 2021-01-01 19:00:52 |
|     3    |     102     |     1    |            |        | 2021-01-02 23:51:23 |
|     3    |     102     |     2    |            |   NaN  | 2021-01-02 23:51:23 |
|     4    |     103     |     1    |      4     |        | 2021-01-04 13:23:46 |
|     4    |     103     |     1    |      4     |        | 2021-01-04 13:23:46 |
|     4    |     103     |     2    |      4     |        | 2021-01-04 13:23:46 |
|     5    |     104     |     1    |    null    |    1   | 2021-01-08 21:00:29 |
|     6    |     101     |     2    |    null    |  null  | 2021-01-08 21:03:13 |
|     7    |     105     |     2    |    null    |    1   | 2021-01-08 21:20:29 |
|     8    |     102     |     1    |    null    |  null  | 2021-01-09 23:54:33 |
|     9    |     103     |     1    |      4     |  1, 5  | 2021-01-10 11:22:59 |
|    10    |     104     |     1    |    null    |  null  | 2021-01-11 18:34:49 |
|    10    |     104     |     1    |    2, 6    |  1, 4  | 2021-01-11 18:34:49 |

#### Query:
```sql
CREATE TEMPORARY TABLE cleaned_customer_orders AS
SELECT
    order_id,
    customer_id,
    pizza_id,
    CASE
        WHEN exclusions IS NULL OR exclusions = 'null' THEN ''
        ELSE exclusions
    END AS exclusions,
    CASE
        WHEN extras IS NULL OR extras = 'null' THEN ''
        ELSE extras
    END AS extras,
    order_time
FROM pizza_runner.customer_orders;
```
#### Explanation:
To replace both the null and NaN values with empty strings, first, a temporary table named ```cleaned_customer_orders``` was created whose structure is based on the already available ```customer_orders``` table in the database. Temporary tables are useful when processing and manipulating data without affecting the original data. Second, ```CASE``` expressions were used for replacing NULL values with empty strings in both the ```exclusions``` and ```extras``` columns. The first condition for both ```CASE``` expressions checks whether the specific data is a null value or is equal to the string "null" and if so, it would return an empty string. Otherwise, it would retain the original data. Initially, the condition only checks if the data **is** a null value however in producing the results, some of the "null" values were not registering as actual NULL values but rather as strings. Aliases of ```exclusions``` and ```extras``` were given for both ```CASE``` expressions to provide more descriptive column names for the results. 

#### After Data Cleaning:
| order_id | customer_id | pizza_id | exclusions | extras |        order_time        |
|:--------:|:-----------:|:--------:|:----------:|:------:|:------------------------:|
|     1    |     101     |     1    |            |        | 2020-01-01T18:05:02.000Z |
|     2    |     101     |     1    |            |        | 2020-01-01T19:00:52.000Z |
|     3    |     102     |     1    |            |        | 2020-01-02T23:51:23.000Z |
|     3    |     102     |     2    |            |        | 2020-01-02T23:51:23.000Z |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |
|     4    |     103     |     2    |      4     |        | 2020-01-04T13:23:46.000Z |
|     5    |     104     |     1    |            |    1   | 2020-01-08T21:00:29.000Z |
|     6    |     101     |     2    |            |        | 2020-01-08T21:03:13.000Z |
|     7    |     105     |     2    |            |    1   | 2020-01-08T21:20:29.000Z |
|     8    |     102     |     1    |            |        | 2020-01-09T23:54:33.000Z |
|     9    |     103     |     1    |      4     |  1, 5  | 2020-01-10T11:22:59.000Z |
|    10    |     104     |     1    |            |        | 2020-01-11T18:34:49.000Z |
|    10    |     104     |     1    |    2, 6    |  1, 4  | 2020-01-11T18:34:49.000Z |
- - - -
2. Converting comma-separated values into multiple rows

In the previously generated ```cleaned_customer_orders``` table, it can be observed that the pizza exclusions and extras requested by the customer for each order they make are initially stored in a comma-separated format. However, in solving the questions in the Ingredient Optimization section of the case study, this type of data is crucial and must be converted into separate rows to better gather insights from the data. 

2.1 Adding a primary key to the table

Prior to performing the conversion of the values in the ```exclusions``` and ```extras``` columns, there is a potential duplication of records that can occur because the ```order_id``` column in the table is not a unique identifier for each order record and allows for duplication. When a customer makes multiple orders at the same time, each order is recorded separately in the table. Therefore, a primary key must be established in the ```cleaned_customer_orders``` table to uniquely identify each of its records.

#### Before Data Cleaning:
| order_id | customer_id | pizza_id | exclusions | extras |        order_time        |
|:--------:|:-----------:|:--------:|:----------:|:------:|:------------------------:|
|     1    |     101     |     1    |            |        | 2020-01-01T18:05:02.000Z |
|     2    |     101     |     1    |            |        | 2020-01-01T19:00:52.000Z |
|     3    |     102     |     1    |            |        | 2020-01-02T23:51:23.000Z |
|     3    |     102     |     2    |            |        | 2020-01-02T23:51:23.000Z |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |
|     4    |     103     |     2    |      4     |        | 2020-01-04T13:23:46.000Z |
|     5    |     104     |     1    |            |    1   | 2020-01-08T21:00:29.000Z |
|     6    |     101     |     2    |            |        | 2020-01-08T21:03:13.000Z |
|     7    |     105     |     2    |            |    1   | 2020-01-08T21:20:29.000Z |
|     8    |     102     |     1    |            |        | 2020-01-09T23:54:33.000Z |
|     9    |     103     |     1    |      4     |  1, 5  | 2020-01-10T11:22:59.000Z |
|    10    |     104     |     1    |            |        | 2020-01-11T18:34:49.000Z |
|    10    |     104     |     1    |    2, 6    |  1, 4  | 2020-01-11T18:34:49.000Z |

#### Query:
#### Explanation:
#### After Data Cleaning:

2.2 Converting comma-separated values into multiple rows
#### Before Data Cleaning:

#### Query:

#### Explanation:

#### After Data Cleaning:

- - - -

### Table 3: runner_orders <a href="anchor" id="table-3"></a>
1. Cleaning the data in the table

In the ```runner_orders``` table, it can be observed that the ```pickup_time```, ```distance```, ```duration```, and ```cancellation``` columns contain several null and NaN values. Moreover, the ```distance``` column contains values with a unit of measurement of ```km``` and the ```duration``` column contains values with units of time of ```min``` or ```minutes```. Thus, these columns will need to be cleaned up before being used for queries.

The data cleaning approach to be taken would be to, first, collectively re-format the null data in the ```pickup_time```, ```distance```, and ```duration``` columns into consistent NULL values. As opposed to the data cleaning approach taken in the ```customer_orders``` table, NULL values are more applicable in representing data in terms of pickup time, distance, and duration, because there are varying instances when an existing order can later be cancelled by the customer or the restaurant itself and in the meantime, placeholder values can be indicated. Second, for the ```cancellation``` column however, the null and NaN values would have to be replaced with empty strings. For this particular column, empty strings are better suited in representing the data because these can imply that the customer successfully received their order which would no longer provide information in terms of cancellation. Third, remove the units of measurement (i.e. km, minute/mins) from both the ```distance``` and ```duration``` columns in order to maintain consistency in the data.

#### Before Data Cleaning:
| order_id | runner_id |     pickup_time     | distance |  duration  |       cancellation      |
|:--------:|:---------:|:-------------------:|:--------:|:----------:|:-----------------------:|
|     1    |     1     | 2021-01-01 18:15:34 |   20km   | 32 minutes |                         |
|     2    |     1     | 2021-01-01 19:10:54 |   20km   | 27 minutes |                         |
|     3    |     1     | 2021-01-03 00:12:37 |  13.4km  |   20 mins  |           NaN           |
|     4    |     2     | 2021-01-04 13:53:03 |   23.4   |     40     |           NaN           |
|     5    |     3     | 2021-01-08 21:10:57 |    10    |     15     |           NaN           |
|     6    |     3     |         null        |   null   |    null    | Restaurant Cancellation |
|     7    |     2     | 2020-01-08 21:30:45 |   25km   |   25mins   |           null          |
|     8    |     2     | 2020-01-10 00:15:02 |  23.4 km |  15 minute |           null          |
|     9    |     2     |         null        |   null   |    null    |  Customer Cancellation  |
|    10    |     1     | 2020-01-11 18:50:20 |   10km   |  10minutes |           null          |

#### Query:
```sql
CREATE TEMPORARY TABLE cleaned_runner_orders AS
SELECT
    order_id,
    runner_id,
    CASE
        WHEN pickup_time IS NULL OR pickup_time = 'null' THEN NULL
        ELSE pickup_time
    END AS pickup_time,
    CASE
        WHEN distance IS NULL OR distance = 'null' THEN NULL
        WHEN distance LIKE '%km' THEN TRIM('km' FROM distance)
        ELSE distance
    END AS distance,
    CASE
        WHEN duration IS NULL OR duration = 'null' THEN NULL
        WHEN duration LIKE '%minutes' THEN TRIM('minutes' FROM duration)
        WHEN duration LIKE '%mins' THEN TRIM('mins' FROM duration)
        WHEN duration LIKE '%minute' THEN TRIM('minute' FROM duration)
        ELSE duration
    END AS duration,
    CASE
        WHEN cancellation IS NULL OR cancellation = 'null' THEN ''
        ELSE cancellation
    END AS cancellation
FROM pizza_runner.runner_orders;
```

#### Explanation:
To perform data cleaning on the table, first, a temporary table named ```cleaned_runner_orders``` was created whose structure is also based on the already available ```runner_orders``` table in the database. Second, to collectively re-format the null data in the ```pickup_time```, ```distance```, and ```duration``` columns into consistent NULL values, ```CASE``` expressions were used to set the condition of checking whether the specific data is a null value or is equal to the string "null". If so, it would return a NULL value. Otherwise, it would retain the original data. Similar with the ```customer_orders``` table, some of the "null" values were not registering as actual NULL values but rather as strings. On the other hand, for the ```cancellation``` column, the condition set in the ```CASE``` expression replaces the null and NaN values with empty strings. Third, to remove the units of measurement (i.e. km, minute/mins) from both the ```distance``` and ```duration``` columns, ```CASE``` expressions were also used to set the condition of checking of whether the specific data contains either a unit of measurement (i.e. kilometer) or time (i.e. minute) through the use of the ```LIKE``` operator. If so, a ```TRIM``` function was used to remove the unit of measurement or time from the given data. Otherwise, it would retain the original data. Aliases of ```pickup_time```, ```distance```, ```duration```, and ```cancellation``` were given for each of the ```CASE``` expressions applied for each of the columns to provide more descriptive column names in the results.

#### After Data Cleaning:
| order_id | runner_id |     pickup_time     | distance | duration |       cancellation      |
|:--------:|:---------:|:-------------------:|:--------:|:--------:|:-----------------------:|
|     1    |     1     | 2020-01-01 18:15:34 |    20    |    32    |                         |
|     2    |     1     | 2020-01-01 19:10:54 |    20    |    27    |                         |
|     3    |     1     | 2020-01-03 00:12:37 |   13.4   |    20    |                         |
|     4    |     2     | 2020-01-04 13:53:03 |   23.4   |    40    |                         |
|     5    |     3     | 2020-01-08 21:10:57 |    10    |    15    |                         |
|     6    |     3     |                     |          |          | Restaurant Cancellation |
|     7    |     2     | 2020-01-08 21:30:45 |    25    |    25    |                         |
|     8    |     2     | 2020-01-10 00:15:02 |   23.4   |    15    |                         |
|     9    |     2     |                     |          |          |  Customer Cancellation  |
|    10    |     1     | 2020-01-11 18:50:20 |    10    |    10    |                         |

- - - -

2. Checking and modifying incorrect data types in the schema

It was mentioned that there were some known data issues with the ```runner_orders``` table and upon checking the schema, it can be observed that the columns ```pickup_time```, ```distance```, and ```duration``` have incorrect data types assigned. Therefore, it is important that the data types of these columns must be modified in order to improve data integrity and ensure that the correct data is stored within the database.

#### Before Data Cleaning:
* #### Query for Checking Data Types:
  
    ```sql
    SELECT
        column_name,
        data_type AS data_typ
    FROM
        information_schema.columns
    WHERE
        table_name = 'cleaned_runner_orders' AND
        column_name IN ('pickup_time', 'distance', 'duration');
    ```

* #### Output:
  
    | column_name |     data_type     |
    |:-----------:|:-----------------:|
    |   distance  | character varying |
    |   duration  | character varying |
    | pickup_time | character varying |

#### Query:
```sql
ALTER TABLE cleaned_runner_orders
ALTER COLUMN pickup_time TYPE TIMESTAMP USING pickup_time::TIMESTAMP,
ALTER COLUMN distance TYPE FLOAT USING distance::FLOAT,
ALTER COLUMN duration TYPE INT USING duration::INT;
```

#### Explanation:
To modify the incorrect data types in the schema, the ```ALTER``` keyword was applied. First, the ```ALTER TABLE``` command modifies the previously created temporary table named ```cleaned_runner_orders```. Second, the ```ALTER COLUMN``` command modifies the data type of each of the columns. For the ```pickup_time``` column, its original data type of ```VARCHAR(19)``` was changed to the type ```TIMESTAMP``` (also known as ```TIMESTAMP WITHOUT TIME ZONE```) because it consists of data representing the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the ordered pizzas. As for the ```distance``` column, its original data type of ```VARCHAR(7)``` was changed to the type ```FLOAT``` because it consists of continuous data representing the distance the runner had to travel to deliver the order to the customer. Lastly, for the ```duration``` column, its original data type of ```VARCHAR(10)``` was changed to the type ```INT``` because it consists of discrete data representing the runner's travel time in delivering the customer's order.

#### After Data Cleaning:
* #### Query for Checking Data Types:
  
    ```sql
    SELECT
        column_name,
        data_type
    FROM
        information_schema.columns
    WHERE
        table_name = 'cleaned_runner_orders' AND
        column_name IN ('pickup_time', 'distance', 'duration');
    ```
    
* #### Output:
  
    | column_name |          data_type          |
    |:-----------:|:---------------------------:|
    |   distance  |       double precision      |
    |   duration  |           integer           |
    | pickup_time | timestamp without time zone |
- - - -
### Table 5: pizza_recipes <a href="anchor" id="table-5"></a>
1. Converting comma-separated values into multiple rows

In the ```pizza_recipes``` table, it can be observed that the ingredients listed for each type of pizza are initially stored in a comma-separated format. However, in solving the questions in the Ingredient Optimization section of this case study, this type of data is crucial and must be converted into separate rows to better gather insights from the data.

#### Before Data Cleaning:
| pizza_id |         toppings        |
|:--------:|:-----------------------:|
|     1    | 1, 2, 3, 4, 5, 6, 8, 10 |
|     2    |    4, 6, 7, 9, 11, 12   |

#### Query:
```sql
CREATE TEMPORARY TABLE cleaned_pizza_recipes AS
SELECT
    pizza_id,
    UNNEST(STRING_TO_ARRAY(toppings, ',')) AS topping_id
FROM pizza_runner.pizza_recipes
ORDER BY pizza_id;
```

#### Explanation:
To convert the comma-separated values into separate rows, first, a temporary table named ```cleaned_pizza_recipes``` was creted whose structure is based on the already available ```pizza_recipes``` table in the database. Second, the ```STRING_TO_ARRAY``` function was used to split the strings in the ```toppings``` column into array elements using the supplied delimeter which is a comma. Third, in conjunction with the ```STRING_TO_ARRAY``` function, the ```UNNEST``` function was used to then expand the resulting split array into a set of separate rows. An *alias* of ```topping_id``` was used to appropriately label the column name in the results. Lastly, an ```ORDER BY``` statement was used to arrange the results by default in ascending order according to the Pizza ID.

#### After Data Cleaning:
| pizza_id | topping_id |
|:--------:|:----------:|
|     1    |      1     |
|     1    |      2     |
|     1    |      3     |
|     1    |      4     |
|     1    |      5     |
|     1    |      6     |
|     1    |      8     |
|     1    |     10     |
|     2    |      4     |
|     2    |      6     |
|     2    |      7     |
|     2    |      9     |
|     2    |     11     |
|     2    |     12     |

- - - - 
2. Checking and modifying incorrect data types in the schema

After converting the comma-separated values in the ```toppings``` column into separate rows, it can be observed that the assigned data type for the values of the ```topping_id``` column is incorrect. In reference to the ```pizza_toppings``` table, the ```topping_id``` column is of ```INTEGER``` data type. Both the ```topping_id``` columns in the now ```cleaned_pizza_recipes``` table and the ```pizza_toppings``` table refer to the same data and can be used in combining both tables for further analysis, and in order to effectively perform a join between the two tables, both columns should have the same data type. 

#### Before Data Cleaning:
* #### Query for Checking Data Types:
```sql
SELECT
    column_name,
    data_type AS data_type
FROM
    information_schema.columns
WHERE
    table_name = 'cleaned_pizza_recipes';
```
* #### Output:
| column_name | data_type |
|:-----------:|:---------:|
|   pizza_id  |  integer  |
|  topping_id |    text   |

#### Query:
```sql
ALTER TABLE cleaned_pizza_recipes
ALTER COLUMN topping_id TYPE INT USING topping_id::INT;
```
#### Explanation:
To modify the incorrect data type in the schema, the ```ALTER``` keyword was applied. First, the ```ALTER TABLE``` command modifies the previously created temporary table named ```cleaned_pizza_recipes```. Second, the ```ALTER COLUMN``` command modifies the data type of the ```topping_id``` column, whose original data type of ```TEXT``` was changed to the type ```INTEGER``` in order to match the automatically generated unique identifier or ID of the pizza toppings in the ```pizza_toppings``` table. 

#### After Data Cleaning:
* #### Query for Checking Data Types:
```sql
SELECT
    column_name,
    data_type AS data_type
FROM
    information_schema.columns
WHERE
    table_name = 'cleaned_pizza_recipes';
```
* #### Output:
| column_name | data_type |
|:-----------:|:---------:|
|   pizza_id  |  integer  |
|  topping_id |  integer  |

- - - -

## Case Study Questions and Answers
<!--
CREATE TEMPORARY TABLE cleaned_customer_orders AS
SELECT
    order_id,
    customer_id,
    pizza_id,
    CASE
        WHEN exclusions IS NULL OR exclusions = 'null' THEN ''
        ELSE exclusions
    END AS exclusions,
    CASE
        WHEN extras IS NULL OR extras = 'null' THEN ''
        ELSE extras
    END AS extras,
    order_time
FROM pizza_runner.customer_orders;

CREATE TEMPORARY TABLE cleaned_runner_orders AS
SELECT
    order_id,
    runner_id,
    CASE
        WHEN pickup_time IS NULL OR pickup_time = 'null' THEN NULL
        ELSE pickup_time
    END AS pickup_time,
    CASE
        WHEN distance IS NULL OR distance = 'null' THEN NULL
        WHEN distance LIKE '%km' THEN TRIM('km' FROM distance)
        ELSE distance
    END AS distance,
    CASE
        WHEN duration IS NULL OR duration = 'null' THEN NULL
        WHEN duration LIKE '%minutes' THEN TRIM('minutes' FROM duration)
        WHEN duration LIKE '%mins' THEN TRIM('mins' FROM duration)
        WHEN duration LIKE '%minute' THEN TRIM('minute' FROM duration)
        ELSE duration
    END AS duration,
    CASE
        WHEN cancellation IS NULL OR cancellation = 'null' THEN ''
        ELSE cancellation
    END AS cancellation
FROM pizza_runner.runner_orders;

ALTER TABLE cleaned_runner_orders
ALTER COLUMN pickup_time TYPE TIMESTAMP USING pickup_time::TIMESTAMP,
ALTER COLUMN distance TYPE FLOAT USING distance::FLOAT,
ALTER COLUMN duration TYPE INT USING duration::INT;

CREATE TEMPORARY TABLE cleaned_pizza_recipes AS
SELECT
    pizza_id,
    UNNEST(STRING_TO_ARRAY(toppings, ',')) AS topping_id
FROM pizza_runner.pizza_recipes
ORDER BY pizza_id;

ALTER TABLE cleaned_pizza_recipes
ALTER COLUMN topping_id TYPE INT USING topping_id::INT;
-->
### A. Pizza Metrics <a href="anchor" id="pizza-metrics"></a>
1. How many pizzas were ordered?
#### Query:
```sql
SELECT
    COUNT(pizza_id) AS num_of_ordered_pizzas
FROM cleaned_customer_orders;
```
#### Explanation:
To determine the number of pizzas ordered, a ```COUNT``` aggregate function was used to count the total number of pizza orders at Pizza Runner in the ```cleaned_customer_orders``` table. An *alias* of ```num_of_ordered_pizzas``` was also given to provide a more descriptive column name in the results.

#### Output:
| num_of_ordered_pizzas |
|:---------------------:|
|           14          |

#### Answer:
Based from the output of the query, it can be observed that a total of 14 pizzas were ordered by customers from Pizza Runner.

- - - -

2. How many unique customer orders were made?
#### Query:
```sql
SELECT
    COUNT(DISTINCT order_id) AS num_of_unique_customer_orders
FROM cleaned_customer_orders;
```
#### Explanation:
To deermine the number of unique customer orders, a ```COUNT``` aggregate function was used in conjunction with the ```DISTINCT``` clause in order to extract and count only the unique order records from the ```cleaned_customer_orders``` table. An *alias* of ```num_of_unique_customer_orders``` was given to provide a more descriptive column name for the results.

#### Output:
| num_of_unique_customer_orders |
|:-----------------------------:|
|               10              |

#### Answer:
Based from the output of the query, it can be observed that there was a total number of 10 unique customer orders made at Pizza Runner.

- - - -

3. How many successful orders were delivered by each runner?
#### Query:
```sql
SELECT
    runner_id,
    COUNT(order_id) AS num_of_successful_deliveries
FROM cleaned_runner_orders
WHERE cancellation = ''
GROUP BY runner_id;
```
#### Explanation:
To determine the number of successful orders delivered by each runner, first, a ```COUNT``` aggregate function was used to count the total number of successful deliveries. An *alias* of ```num_of_successful_deliveries``` was given to provide a more descriptive column name for the results. Second, a ```WHERE``` clause was used to filter the resulting records based on the condition when the order was *not* cancelled by either the customer or the restaurant. Lastly, a ```GROUP BY``` statement was used to arrange the counted number of successful deliveries made by each runner into groups according to the Runner ID.

#### Output:
| runner_id | num_of_successful_deliveries |
|:---------:|:----------------------------:|
|     1     |               4              |
|     2     |               3              |
|     3     |               1              |

#### Answer:
Based from the output of the query, it can be observed that Runner 1 was able to successfully deliver 4 pizzas, while Runner 2 was able to successfully deliver 3 pizzas. On the other hand, Runner 3 was able to successfully deliver only 1 pizza.

- - - -

4. How many of each type of pizza was delivered?
#### Query:
```sql
WITH order_deliver_status AS (
   SELECT
      x.order_id,
      x.pizza_id,
      CASE
	  WHEN y.cancellation = '' THEN 'successful'
	  ELSE 'unsuccessful'
      END AS status
  FROM cleaned_customer_orders x
  JOIN cleaned_runner_orders y
  ON x.order_id=y.order_id
)

SELECT
    y.pizza_name,
    COUNT(x.pizza_id) AS num_of_successful_deliveries
FROM order_deliver_status x
JOIN pizza_runner.pizza_names y
ON x.pizza_id=y.pizza_id
WHERE x.status = 'successful'
GROUP BY y.pizza_name;
```
#### Explanation:

To determine the number of successful deliveries for each type of pizza, first, a CTE labeled ```order_deliver_status``` consisting of a CASE expression was used to set the conditions in identifying whether an order was delivered successfully or not based on the ```cleaned_runner_orders``` table. The first condition states that when an order was not cancelled by either the customer or the restaurant, then the order was delivered successfully by the runner. Otherwise, if the cancellation column has a value for that particular order, then the order delivery was unsuccessful. An alias of ```status``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used in the CTE to combine the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column ```order_id``` in order to display the Order ID, the Pizza ID, and the status of the order delivery. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. The query then produced the following results:

| order_id | pizza_id |    status    |
|:--------:|:--------:|:------------:|
|     1    |     1    |  successful  |
|     2    |     1    |  successful  |
|     3    |     2    |  successful  |
|     3    |     1    |  successful  |
|     4    |     2    |  successful  |
|     4    |     1    |  successful  |
|     4    |     1    |  successful  |
|     5    |     1    |  successful  |
|     6    |     2    | unsuccessful |
|     7    |     2    |  successful  |
|     8    |     1    |  successful  |
|     9    |     1    | unsuccessful |
|    10    |     1    |  successful  |
|    10    |     1    |  successful  |

Following that, a ```COUNT``` aggregate function was used to count the number of orders made for each type of pizza based on the resulting table of the ```order_delivery_status``` CTE. Together with the ```COUNT``` aggregate function, a ```WHERE``` clause was then used to filter the records according to which order was delivered successfully. After that, a ```JOIN``` clause was used to combine the ```order_delivery_status``` table from the CTE and the ```pizza_names``` table to display the name of the ordered pizza rather than the ID and the total number of successful deliveries made for each type of pizza. An *alias* of ```num_of_successful_deliveries``` was given to provide a more descriptive column name for the results. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the type of pizza. 

#### Output:
| pizza_name | num_of_successful_deliveries |
|:----------:|:----------------------------:|
| Vegetarian |               3              |
| Meatlovers |               9              |

#### Answer:
Based from the results of the query, it can be observed that the there were 3 orders of the Vegetarian type of pizza from Pizza Runner that were successfully delivered to the customers. As for the Meatlovers type of pizza, 9 orders were made and successfully delivered to the customers.
- - - -

5. How many Vegetarian and Meatlovers were ordered by each customer?
#### Query:
```sql
SELECT
    x.customer_id,
    y.pizza_name,
    COUNT(x.pizza_id) AS total_orders
FROM cleaned_customer_orders x
JOIN pizza_runner.pizza_names y
ON x.pizza_id=y.pizza_id
GROUP BY x.customer_id, y.pizza_name
ORDER BY x.customer_id, y.pizza_name;
```
#### Explanation:
To determine the number of Vegetarian and Meatlovers pizza ordered by each customer, first, a ```COUNT``` aggregate function was used to count the total number of orders made by each customer of both types of pizza. An *alias* of ```total_orders``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine the ```cleaned_customer_orders``` table and the ```pizza_names``` table based on their related column ```pizza_id``` in order to display the ID of the customer that made the order, the name of the pizza they ordered, and the number of times they made an order for that particular type of pizza. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to both the Customer ID and the name of the pizza. Lastly, an ```ORDER BY``` statement was also used to organize the results by default in ascending order according to the Customer ID and the name of the pizza.

#### Output:
| customer_id | pizza_name | total_orders |
|:-----------:|:----------:|:------------:|
|     101     | Meatlovers |       2      |
|     101     | Vegetarian |       1      |
|     102     | Meatlovers |       2      |
|     102     | Vegetarian |       1      |
|     103     | Meatlovers |       3      |
|     103     | Vegetarian |       1      |
|     104     | Meatlovers |       3      |
|     105     | Vegetarian |       1      |

#### Answer:
Based from the output of the query, it can be observed that Customer 101 ordered 2 of the Meatlovers pizza and 1 of the Vegeterian pizza. As for Customer 102, they ordered 2 of the Meatlovers pizza as well and 1 of the Vegetarian pizza. Customer 103 on the other hand ordered 3 of the Meatlovers pizza and 1 of the Vegetarian pizza. Customer 104 only ordered 3 of the Meatlovers pizza and Customer 105 only ordered 1 of the Vegetarian pizza.

- - - -
  
6. What was the maximum number of pizzas delivered in a single order?
#### Query:
```sql
WITH successful_deliveries AS (
    SELECT
	x.order_id,
	COUNT(x.pizza_id) AS num_of_successful_deliveries
    FROM cleaned_customer_orders x
    JOIN cleaned_runner_orders y
    ON x.order_id=y.order_id
    WHERE y.cancellation = ''
    GROUP BY x.order_id
    ORDER BY x.order_id
)

SELECT
    order_id,
    num_of_successful_deliveries AS max_deliveries
FROM successful_deliveries
ORDER BY max_deliveries DESC
LIMIT 1;
```
#### Explanation:
To determine the maximum number of pizzas delivered in a single order, first, a CTE labeled ```successful_deliveries``` consisting of a ```COUNT``` aggregate function, a ```JOIN``` clause, a ```WHERE``` clause, a ```GROUP BY``` statement as well as an ```ORDER BY``` statement was used. The ```COUNT``` aggregate function was used to count the total number of deliveries made for each order in conjunction with a ```WHERE``` clause in order to filter the records according to only successful deliveries. The condition set was if the cancellation column has no record for a particular record, if the order was not cancelled by either the restaurant or the customer, then the order was delivered successfully to the customer. An *alias* of ```num_of_successful_deliveries``` was given to provide a more descriptive column name for the results. The ```JOIN``` clause was also applied to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table to display the Order ID and the total number of successful deliveries. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. The ```GROUP BY``` statement was then used to arrange the results into groups according to the Order ID. Lastly, the ```ORDER BY``` statement was used to organize the results by default in ascending order based on the Order ID. This query then produces the following results:
| order_id | num_of_successful_deliveries |
|:--------:|:----------------------------:|
|     1    |               1              |
|     2    |               1              |
|     3    |               2              |
|     4    |               3              |
|     5    |               1              |
|     7    |               1              |
|     8    |               1              |
|    10    |               2              |

Following that, first, an *alias* of ```max_deliveries``` was given to provide a more descriptive column name for the results. Second, an ```ORDER BY``` statement was used to organize the total number of successful deliveries made in descending order and place the highest number of successful deliveries made at the top. Finally, the ```LIMIT``` clause was used to limit the results to only the first record. 

#### Output:
| order_id | max_deliveries |
|:--------:|:--------------:|
|     4    |        3       |

#### Answer:
Based from the output of the query, it can be observed that the maximum number of pizzas delivered in a single order was for Order 4 with 3 successful deliveries made by Pizza Runner.

- - - -

7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
#### Query:
```sql
WITH successful_deliveries AS (
  SELECT
      x.customer_id,
      x.order_id,
      x.exclusions,
      x.extras
  FROM cleaned_customer_orders x
  JOIN cleaned_runner_orders y
  ON x.order_id=y.order_id
  WHERE y.cancellation = ''
)

SELECT
    customer_id,
    SUM(
	CASE
	  WHEN exclusions != '' OR extras != '' THEN 1
	  ELSE 0
	END
    ) AS with_changes,
    SUM(
	CASE
	  WHEN exclusions = '' AND extras = '' THEN 1
	  ELSE 0
	END
    ) AS no_changes
FROM successful_deliveries
GROUP BY customer_id
ORDER BY customer_id;
```
#### Explanation:

#### Output:
| customer_id | with_changes | no_changes |
|:-----------:|:------------:|:----------:|
|     101     |       0      |      2     |
|     102     |       0      |      3     |
|     103     |       3      |      0     |
|     104     |       2      |      1     |
|     105     |       1      |      0     |

#### Answer:
Based from the output of the query, it can be observed that for Customer 101, both of their successfully delivered orders had no requested changes. Similarly, Customer 102 also had no changes on all 3 of their successfully delivered orders. On the other hand, Customer 103 requested changes in all 3 of their orders, while Customer 104 requested changes in only 2 of their orders and 1 without. Lastly, Customer 105 requested changes in their single order.

- - - -

8. How many pizzas were delivered that had both exclusions and extras?
#### Query:
```sql
WITH successful_deliveries AS (
  SELECT
      x.order_id,
      x.exclusions,
      x.extras
  FROM cleaned_customer_orders x
  JOIN cleaned_runner_orders y
  ON x.order_id=y.order_id
  WHERE y.cancellation = ''
)

SELECT
    COUNT(order_id) AS with_exclusions_and_extras
FROM successful_deliveries
WHERE exclusions != '' AND extras != '';
```
#### Explanation:

#### Output:
| with_exclusions_and_extras |
|:--------------------------:|
|              1             |

#### Answer:
Based from the output of the query, it can be observed that only 1 of the successfully delivered orders had both requested exclusions and extras in their ordered pizza.

- - - -

9. What was the total volume of pizzas ordered for each hour of the day?
#### Query:
```sql
SELECT
    EXTRACT(HOUR FROM order_time) AS hour_of_the_day,
    COUNT(pizza_id) AS total_pizza_orders
FROM cleaned_customer_orders
GROUP BY hour_of_the_day
ORDER BY hour_of_the_day
```
#### Explanation:
<!--
PostgreSQL provides a built-in date function named the EXTRACT() function that extracts a specific field from a date, time, or timestamp. It allows us to pull out any date/time field, such as a day, month, hour, minute, etc., from any specific DateTime. 
-->

#### Output:
| hour_of_the_day | total_pizza_orders |
|:---------------:|:------------------:|
|        11       |          1         |
|        13       |          3         |
|        18       |          3         |
|        19       |          1         |
|        21       |          3         |
|        23       |          3         |

#### Answer:
Based from the output of the query, it can be observed that there is only a small volume of pizza orders being made at the hours 11 and 19 o'clock. On the other hand, there is a higher volume of pizza orders, at most 3, from customers at hours 13, 18, 21, and 23 o'clock. It can be deduced that Pizza Runner receives from orders around lunch time at 13 o'clock, around dinner time at 18 o'clock, or much later in the evening at 21 or 23 o'clock. 
- - - -

10. What was the volume of orders for each day of the week?
#### Query:
```sql
SELECT
    TO_CHAR(order_time, 'Day') AS day_of_the_week,
    COUNT(pizza_id) AS total_pizza_orders
FROM cleaned_customer_orders
GROUP BY day_of_the_week
ORDER BY day_of_the_week;
```
#### Explanation:
<!--
In PostgreSQL, the EXTRACT() function is used to get a day, month, year, etc., from a DATE, TIME, or TIMESTAMP. We can use this function to get/extract the day from the specified date or time stamp. However, it retrieves the specific part/field from the specified date as a number. If we have to get/extract the day names from a specific date, then we must use the TO_CHAR() function.

valid day format -- "Day"
-->

#### Output:
| day_of_the_week | total_pizza_orders |
|:---------------:|:------------------:|
|      Friday     |          1         |
|     Saturday    |          5         |
|     Thursday    |          3         |
|    Wednesday    |          5         |

#### Answer:
Based from the output of the query, it can be observed that Pizza Runner received only 1 order on Friday, 5 orders on Saturday, 3 orders on Thursday, and 5 orders on Wednesday. 

- - - -

### B. Runner and Customer Experience <a href="anchor" id="runner-customer-exp"></a>
1. How many runners signed up for each 1 week period? (i.e. week starts ```2021-01-01```)
#### Query:
```sql
WITH runner_registration_dates AS (
   SELECT
       runner_id,
       registration_date,
       EXTRACT(DAY FROM registration_date) AS day,
       EXTRACT(MONTH FROM registration_date) AS month
   FROM pizza_runner.runners
)

SELECT
     CASE
	WHEN day < 8 THEN 'Week 1'
	WHEN day < 15 THEN 'Week 2'
	WHEN day < 22 THEN 'Week 3'
	ELSE 'Week 4'
    END AS registration_week,
    COUNT(runner_id) AS num_of_registered_runners
FROM runner_registration_dates
GROUP BY registration_week
```
#### Explanation:


#### Output:
| registration_week | num_of_registered_runners |
|:-----------------:|:-------------------------:|
|       Week 1      |             2             |
|       Week 2      |             1             |
|       Week 3      |             1             |

#### Answer:
Based from the output, it can be observed that in Week 1 of January 2021, two people signed up to be runners. For Week 2, only one person signed up to be a runner. Similarly, for Week 3, only one person signed up to be a runner as well for Pizza Runner.

- - - -

2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
#### Query:
```sql
SELECT
    y.runner_id,
    ROUND(
	AVG(DATE_PART('minute', y.pickup_time - x.order_time))::NUMERIC,
    0) AS avg_pickup_time
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = ''
GROUP BY y.runner_id
ORDER BY y.runner_id;
```
#### Explanation:
<!--
PostgreSQL does not define round(double precision, integer). You must cast the value to be rounded to numeric to use the two-argument form of round. Just append ::numeric for the shorthand cast, like round(val::numeric,2).

PostgreSQL does not provide DATEDIFF function similar to SQL Server DATEDIFF, but you can use various expressions or UDF to get the same results.
-->

#### Output:
| runner_id | avg_pickup_time |
|:---------:|:---------------:|
|     1     |        15       |
|     2     |        23       |
|     3     |        10       |

#### Answer:
Based from the output of the query, it can be observed that Runner 1 took an average time of 15 minutes to arrive at the Pizza Runner HQ to pickup the order. As for Runner 2, it took them an average time of 23 minutes, while Runner 3 took 10 minutes. 

- - - -

3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
#### Query:
```sql
WITH prep_time_per_order AS (
  SELECT
      x.order_id,
      COUNT(x.pizza_id) AS num_of_pizza,
      DATE_PART('minute', y.pickup_time - x.order_time) AS total_prep_time
  FROM cleaned_customer_orders x
  JOIN cleaned_runner_orders y
  ON x.order_id=y.order_id
  WHERE y.cancellation = ''
  GROUP BY x.order_id, y.pickup_time, x.order_time
)

SELECT
    num_of_pizza,
    AVG(total_prep_time) AS avg_total_prep_time
FROM prep_time_per_order
GROUP BY num_of_pizza;
```
#### Explanation:

#### Output:
| num_of_pizza | avg_total_prep_time |
|:------------:|:-------------------:|
|       3      |          29         |
|       2      |          18         |
|       1      |          12         |

#### Answer:
Based from the output of the query, it can be observed that the average total preparation time taken increases as the number of pizza orders increases. With 3 pizza orders, it takes an average total preparation time of 29 minutes. As for 2 pizza orders, it takes 18 minutes, while for 1 pizza order, it takes an average total preparation time of 12 minutes.

- - - -

4. What was the average distance travelled for each customer?
#### Query:
```sql
SELECT
    x.customer_id,
    ROUND(AVG(y.distance)::NUMERIC, 1) AS avg_distance_travel
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = ''
GROUP BY x.customer_id
ORDER BY x.customer_id;
```
#### Explanation:

#### Output:
| customer_id | avg_distance_travel |
|:-----------:|:-------------------:|
|     101     |         20.0        |
|     102     |         16.7        |
|     103     |         23.4        |
|     104     |         10.0        |
|     105     |         25.0        |

#### Answer:
Based from the output of the query, it can be observed that for Customer 101, an average distance of 20 km was travelled by the runners to deliver their orders. As for Customer 102, an average distance of 16.7 km was travelled to deliver their orders, while for Customer 103, an average distance of 23.4 was travelled by the runners. An average distance of 10 km was travelled for Customer 104 and 25 km was travelled for Customer 105 by the runners. It can be deduced that Customer 104 resides in a location nearby Pizza Runner because it takes the least amount of distance to deliver their orders from the Pizza Runner HQ while Customer 105 resides in a location farthest from the headquarters compared to the other customers.

- - - -

5. What was the difference between the longest and shortest delivery times for all orders?
#### Query:
```sql
SELECT
    MAX(duration) AS max_delivery_time,
    MIN(duration) AS min_delivery_time,
    MAX(duration) - MIN(duration) AS delivery_time_difference
FROM cleaned_runner_orders
WHERE cancellation = '';
```
#### Explanation:

#### Output:
| max_delivery_time | min_delivery_time | delivery_time_difference |
|:-----------------:|:-----------------:|:------------------------:|
|         40        |         10        |            30            |

#### Answer:
Based from the output of the query, it can be observed that from the overall delivery times of all the orders delivered by runners, there is a 30-minute time difference between the longest time of 40 minutes and the shortest time of 10 minutes taken in delivering a customer's order.

- - - -

6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
#### Query:
```sql
SELECT
    y.runner_id,
    x.order_id,
    x.customer_id,
    y.distance AS distance_km,
    ROUND((y.duration / 60.0)::NUMERIC, 2) AS duration_hr,
    ROUND((y.distance / (y.duration / 60.0))::NUMERIC, 2) AS avg_speed_kph
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = ''
GROUP BY y.runner_id, x.order_id, x.customer_id, y.duration, y.distance
ORDER BY y.runner_id
```
#### Explanation:

#### Output:
| runner_id | order_id | customer_id | distance_km | duration_hr | avg_speed_kph |
|:---------:|:--------:|:-----------:|:-----------:|:-----------:|:-------------:|
|     1     |     1    |     101     |      20     |     0.53    |     37.50     |
|     1     |     2    |     101     |      20     |     0.45    |     44.44     |
|     1     |     3    |     102     |     13.4    |     0.33    |     40.20     |
|     1     |    10    |     104     |      10     |     0.17    |     60.00     |
|     2     |     4    |     103     |     23.4    |     0.67    |     35.10     |
|     2     |     7    |     105     |      25     |     0.42    |     60.00     |
|     2     |     8    |     102     |     23.4    |     0.25    |     93.60     |
|     3     |     5    |     104     |      10     |     0.25    |     40.00     |

#### Answer:
Based from the output of the query, it can be observed that Runner 1 travels at a speed within the range of 37.50 to 60.00 kph in delivering orders to customers. Notice that for Runner 1, Customer 101 was the farthest distance they had to travel to deliver their order compared to the two other customers and the maximum speed he was able to travel to deliver their second order was 44.44 kph, increasing from the first order. Customer 102, on the other hand, is a little bit closer in location to the headquarters and so Runner 1 was able to travel at a speed of 40.20 kph in delivering their order. For Customer 104, considering that they are the closest to the headquarters, Runner 1 was able to travel at the fastest speed of 60.00 kph in delivering their order. 

For Runner 2, it can be observed that they travel at a speed within the range of 35.10 to 93.60 kph in delivering orders to customers. Notice that for Runner 2, both Customer 103 and 102 cover the same distance yet Runner 2 had varying speed in delivering their orders. Runner 2 probably had slower delivery time for Customer 103's order due to several factors such as traffic, weather conditions, etc. while in delivering Customer 102's order, Runner 2 was able to travel faster at a maximum speed of 93.60 kph. On the other hand, Customer 105 had the farthest distance to be travelled compared to the other two customers and Runner 2 was able to travel at a speed of 60.00 kph in delivering their order. 

For Runner 3, they only have one record so far in delivering orders to customers which denotes that they were able to travel at a speed of 40.00 kph in delivering the order to Customer 104, who appears to be located nearby the headquarters given the distance travelled by the runner.

- - - -

7. What is the successful delivery percentage for each runner?
#### Query:
```sql
SELECT
    runner_id,
    100 * SUM(
        CASE
             WHEN cancellation = '' THEN 1
             ELSE 0
        END
    ) / COUNT(order_id) AS success_delivery_percentage
FROM cleaned_runner_orders
GROUP BY runner_id
ORDER BY runner_id;
```
#### Explanation:

#### Output:
| runner_id | success_delivery_percentage |
|:---------:|:---------------------------:|
|     1     |             100             |
|     2     |              75             |
|     3     |              50             |

#### Answer:
Based from the output of the query, it can be observed that Runner 1 had a 100% success rate in delivering orders to customers. Runner 2, on the other hand, had a 75% success rate and Runner 3 only had a 50% success rate.

- - - -

### C. Ingredient Optimization <a href="anchor" id="ingredient-optimization"></a>
1. What are the standard ingredients for each pizza?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. What was the most commonly added extra?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. What was the most common exclusion?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

4. Generate an order item for each record in the ```customers_orders``` table in the format of one of the following:
  * ```Meat Lovers```
  * ```Meat Lovers - Exclude Beef```
  * ```Meat Lovers - Extra Bacon```
  * ```Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers```
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the ```customer_orders``` table and add a 2x in front of any relevant ingredients
  * For example: ```"Meat Lovers: 2xBacon, Beef, ... , Salami"```
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

### D. Pricing and Ratings <a href="anchor" id="pricing-ratings"></a>
1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. What if there was an additional $1 charge for any pizza extras?
  * Add cheese is $1 extra
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
  * ```customer_id```
  * ```order_id```
  * ```runner_id```
  * ```rating```
  * ```order_time```
  * ```pickup_time```
  * Time between order and pickup
  * Delivery duration
  * Average speed
  * Total number of pizzas
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

## Bonus Questions <a href="anchor" id="bonus-questions"></a>
1. If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:
