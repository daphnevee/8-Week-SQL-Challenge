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
1. [Data Cleaning](#data-cleaning)
	* [Table 2: customer_orders](#table-2)
	* [Table 3: runner_orders](#table-3)
	* [Table 5: pizza_recipes](#table-5)
2. [Pizza Metrics](#pizza-metrics)
3. [Runner and Customer Experience](#runner-customer-exp)
4. [Ingredient Optimization](#ingredient-optimization)
5. [Pricing and Ratings](#pricing-ratings)
6. [Bonus Questions](#bonus-questions)

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

2.1 Adding an identity column to the table

Prior to performing the conversion of the values in the ```exclusions``` and ```extras``` columns, there is a potential duplication of records that can occur because the ```order_id``` column in the table is not a unique identifier for each order record and allows for duplication. When a customer makes multiple orders at the same time, each order is recorded separately in the table. Therefore, an identity column must be added to the ```cleaned_customer_orders``` table to uniquely identify each of its records.

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
```sql
ALTER TABLE cleaned_customer_orders
ADD customer_order_id INT GENERATED ALWAYS AS IDENTITY;
```

#### Explanation:
To add an identity column to the ```cleaned_customer_orders``` table, the ```ALTER``` keyword was applied. First, the ```ALTER TABLE``` command modifies the previously created temporary table named ```cleaned_customer_orders```. Second, the ```ADD``` command adds the new identity column named ```customer_order_id``` of ```INTEGER``` data type to the table established with the constraint ```GENERATED ALWAYS AS IDENTITY``` to automatically generate a unique number for each record.

#### After Data Cleaning:
| order_id | customer_id | pizza_id | exclusions | extras |        order_time        | customer_order_id |
|:--------:|:-----------:|:--------:|:----------:|:------:|:------------------------:|:-----------------:|
|     1    |     101     |     1    |            |        | 2020-01-01T18:05:02.000Z |         1         |
|     2    |     101     |     1    |            |        | 2020-01-01T19:00:52.000Z |         2         |
|     3    |     102     |     1    |            |        | 2020-01-02T23:51:23.000Z |         3         |
|     3    |     102     |     2    |            |        | 2020-01-02T23:51:23.000Z |         4         |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |         5         |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |         6         |
|     4    |     103     |     2    |      4     |        | 2020-01-04T13:23:46.000Z |         7         |
|     5    |     104     |     1    |            |    1   | 2020-01-08T21:00:29.000Z |         8         |
|     6    |     101     |     2    |            |        | 2020-01-08T21:03:13.000Z |         9         |
|     7    |     105     |     2    |            |    1   | 2020-01-08T21:20:29.000Z |         10        |
|     8    |     102     |     1    |            |        | 2020-01-09T23:54:33.000Z |         11        |
|     9    |     103     |     1    |      4     |  1, 5  | 2020-01-10T11:22:59.000Z |         12        |
|    10    |     104     |     1    |            |        | 2020-01-11T18:34:49.000Z |         13        |
|    10    |     104     |     1    |    2, 6    |  1, 4  | 2020-01-11T18:34:49.000Z |         14        |

2.2 Converting comma-separated values into multiple rows

Now that the identity column has been established in the ```cleaned_customer_orders``` table, the values in the ```exclusions``` and ```extras``` columns can now be converted from comma-separated values into separate rows. 

#### Before Data Cleaning:
| order_id | customer_id | pizza_id | exclusions | extras |        order_time        | customer_order_id |
|:--------:|:-----------:|:--------:|:----------:|:------:|:------------------------:|:-----------------:|
|     1    |     101     |     1    |            |        | 2020-01-01T18:05:02.000Z |         1         |
|     2    |     101     |     1    |            |        | 2020-01-01T19:00:52.000Z |         2         |
|     3    |     102     |     1    |            |        | 2020-01-02T23:51:23.000Z |         3         |
|     3    |     102     |     2    |            |        | 2020-01-02T23:51:23.000Z |         4         |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |         5         |
|     4    |     103     |     1    |      4     |        | 2020-01-04T13:23:46.000Z |         6         |
|     4    |     103     |     2    |      4     |        | 2020-01-04T13:23:46.000Z |         7         |
|     5    |     104     |     1    |            |    1   | 2020-01-08T21:00:29.000Z |         8         |
|     6    |     101     |     2    |            |        | 2020-01-08T21:03:13.000Z |         9         |
|     7    |     105     |     2    |            |    1   | 2020-01-08T21:20:29.000Z |         10        |
|     8    |     102     |     1    |            |        | 2020-01-09T23:54:33.000Z |         11        |
|     9    |     103     |     1    |      4     |  1, 5  | 2020-01-10T11:22:59.000Z |         12        |
|    10    |     104     |     1    |            |        | 2020-01-11T18:34:49.000Z |         13        |
|    10    |     104     |     1    |    2, 6    |  1, 4  | 2020-01-11T18:34:49.000Z |         14        |

#### Query:
```sql
CREATE TEMPORARY TABLE exclusions AS
SELECT
    customer_order_id,
    UNNEST(STRING_TO_ARRAY(exclusions, ',')) AS topping_id
FROM cleaned_customer_orders
ORDER BY customer_order_id;

CREATE TEMPORARY TABLE extras AS
SELECT
    customer_order_id,
    UNNEST(STRING_TO_ARRAY(extras, ',')) AS topping_id
FROM cleaned_customer_orders
ORDER BY customer_order_id;
```

#### Explanation:
To convert the comma-separated values in both the ```exclusions``` and ```extras``` columns, two temporary tables were created to split up the exclusions and extras records from the main ```cleaned_customer_orders``` table across two separate tables. This process of normalization was applied in order to prevent duplication of records in the main table and improve data integrity. First, both temporary tables were labeled based on the existing columns in the main table, ```exclusions``` and ```extras``` respectively. Second, to apply the conversion of the values, the ```STRING_TO_ARRAY``` function was used to split the strings in both the ```exclusions``` and the ```extras``` columns into array elements using the supplied comma delimeter. Then, the ```UNNEST``` function was used in conjunction with the ```STRING_TO_ARRAY``` function to expand the resulting split array into a set of separate rows. An *alias* of ```topping_id``` was given similarly to both columns to match the records in the ```pizza_toppings``` table. Lastly, an ```ORDER BY``` statement was used to arrange the results by default in ascending order according to the ```customer_order_id``` identity column.

#### After Data Cleaning:
* #### Exclusions Table
	| customer_order_id | topping_id |
	|:-----------------:|:----------:|
	|         5         |      4     |
	|         6         |      4     |
	|         7         |      4     |
	|         12        |      4     |
	|         14        |      2     |
	|         14        |      6     |

* #### Extras Table
	| customer_order_id | topping_id |
	|:-----------------:|:----------:|
	|         8         |      1     |
	|         10        |      1     |
	|         12        |      1     |
	|         12        |      5     |
	|         14        |      1     |
	|         14        |      4     |

- - - -
3. Checking and modifying incorrect data types in the schema

After converting the comma-separated values in both the ```exclusions``` and ```extras``` columns into rows in their newly-created temporary tables, it can be observed that the assigned data type for the values of the ```topping_id``` in both tables is incorrect. In reference to the ```pizza_toppings``` table, the ```topping_id``` column is of ```INTEGER``` data type. Both the ```topping_id``` columns in the ```exclusions``` and ```extras``` tables and the ```topping_id``` column in the ```pizza_toppings``` table refer to the same data and can be used in combining both tables for further analysis, and in order to effectively perform a join between the tables, each of these columns should have the same data type.

#### Before Data Cleaning:
* #### Query for Checking Data Types in the Exclusions table:
	```sql
	SELECT
	    column_name,
	    data_type
	FROM
	    information_schema.columns
	WHERE
	    table_name = 'exclusions';
	```
* #### Output for Exclusions table:
	|    column_name    | data_type |
	|:-----------------:|:---------:|
	| customer_order_id |  integer  |
	|     topping_id    |    text   |

* #### Query for Checking Data Types in the Extras table:
	```sql
	SELECT
	    column_name,
	    data_type
	FROM
	    information_schema.columns
	WHERE
	    table_name = 'extras';
	```
* #### Output for Extras table:
	|    column_name    | data_type |
	|:-----------------:|:---------:|
	| customer_order_id |  integer  |
	|     topping_id    |    text   |

#### Query for Exclusions table:
```sql
ALTER TABLE exclusions
ALTER COLUMN topping_id TYPE INT USING topping_id::INT;
```

#### Query for Extras table:
```sql
ALTER TABLE extras
ALTER COLUMN topping_id TYPE INT USING topping_id::INT;
```

#### Explanation:
To modify the incorrect data type in the schema, the ```ALTER``` keyword was applied in both the ```exclusions``` and ```extras``` tables. First, the ```ALTER TABLE``` command modifies the previously created temporary tables, ```exclusions``` and ```extras```. Second, the ```ALTER COLUMN``` command modifies the data type of the ```topping_id``` column in both tables, whose original data type of ```TEXT``` was changed to the type ```INTEGER``` in order to match the automatically generated unique identifier or ID of the pizza toppings in the ```pizza_toppings``` table. 

#### After Data Cleaning:
* #### Query for Checking Data Types in the Exclusions table:
	```sql
	SELECT
	    column_name,
	    data_type
	FROM
	    information_schema.columns
	WHERE
	    table_name = 'exclusions';
	```
* #### Output for Exclusions table:
	|    column_name    | data_type |
	|:-----------------:|:---------:|
	| customer_order_id |  integer  |
	|     topping_id    |  integer  |

* #### Query for Checking Data Types in the Extras table:
	```sql
	SELECT
	    column_name,
	    data_type
	FROM
	    information_schema.columns
	WHERE
	    table_name = 'extras';
	```
* #### Output for Extras table:
	|    column_name    | data_type |
	|:-----------------:|:---------:|
	| customer_order_id |  integer  |
	|     topping_id    |  integer  |

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
        data_type
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
DROP TABLE IF EXISTS runner_ratings;

CREATE TABLE runner_ratings (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "rating" INTEGER,
  "comment" TEXT
);

INSERT INTO runner_ratings
  ("order_id", "runner_id", "rating", "comment")
VALUES
  (1, 1, 4, 'Good service.'),
  (2, 1, 5, 'Excellent service! Very accommodating, would definitely recommend.'),
  (3, 1, 5, 'Delivered on time. Delivery man was very polite and courteous. Would recommend!'),
  (4, 2, 1, 'Poor service. Took an hour to get food. Would not recommend.'),
  (5, 3, 5, 'Great service. Picked up and delivered securely and on time.'),
  (7, 2, 5, 'Arrived on time and delivered quickly. Great service.'),
  (8, 2, 5, 'Good service. Efficient and reliable.'),
  (10, 1, 5, 'Excellent and professional service. Highly recommend.');
-->

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

ALTER TABLE cleaned_customer_orders
ADD customer_order_id INT GENERATED ALWAYS AS IDENTITY;

CREATE TEMPORARY TABLE exclusions AS
SELECT
    customer_order_id,
    UNNEST(STRING_TO_ARRAY(exclusions, ',')) AS topping_id
FROM cleaned_customer_orders
ORDER BY customer_order_id;

CREATE TEMPORARY TABLE extras AS
SELECT
    customer_order_id,
    UNNEST(STRING_TO_ARRAY(extras, ',')) AS topping_id
FROM cleaned_customer_orders
ORDER BY customer_order_id;

ALTER TABLE exclusions
ALTER COLUMN topping_id TYPE INT USING topping_id::INT;
    
ALTER TABLE extras
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
To determine the number of pizzas ordered, a ```COUNT``` aggregate function was used to count the total number of orders received by Pizza Runner for their pizzas from the ```cleaned_customer_orders``` table. An *alias* of ```num_of_ordered_pizzas``` was also given to provide a more descriptive column name in the results.

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
To determine the number of unique customer orders, a ```COUNT``` aggregate function was used in conjunction with the ```DISTINCT``` clause in order to extract and count only the unique order records from the ```cleaned_customer_orders``` table. An *alias* of ```num_of_unique_customer_orders``` was given to provide a more descriptive column name for the results.

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
To determine the number of successful orders delivered by each runner, first, a ```COUNT``` aggregate function was used to count the total number of orders delivered by each runner. An *alias* of ```num_of_successful_deliveries``` was given to provide a more descriptive column name for the results. Second, a ```WHERE``` clause was used to filter the results based on the condition when the order was successfully delivered and did not undergo any cancellation. Lastly, a ```GROUP BY``` statement was used to arrange the counted number of successful deliveries made by each runner into groups according to the Runner ID.

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
SELECT
    z.pizza_name,
    COUNT(x.pizza_id) AS num_of_successful_deliveries
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
JOIN pizza_runner.pizza_names z
ON x.pizza_id=z.pizza_id
WHERE y.cancellation = ''
GROUP BY z.pizza_name;
```
#### Explanation:

To determine the number of successful deliveries for each type of pizza, first, a ```COUNT``` aggregate function was used to count the number of orders made for each type of pizza. Second, 2 ```JOIN``` clauses were used where the first was to combine the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column ```order_id``` to utilize the Pizza ID in the ```COUNT``` aggregate function. The second was then used to also combine the ```cleaned_customer_orders``` table with the ```pizza_names``` table to display the name of each type of pizza based on their related column, ```pizza_id```. In joining the tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table, ```y``` for the ```cleaned_runner_orders```, and ```z``` for the ```pizza_names``` table, so as to make the query more readable. Third, a ```WHERE``` clause was then used to filter the records according to which order was delivered successfully. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the type of pizza. 

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
SELECT
    x.order_id,
    COUNT(x.pizza_id) AS max_deliveries
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = ''
GROUP BY x.order_id
ORDER BY max_deliveries DESC
LIMIT 1;
```
#### Explanation:

To determine the maximum number of pizzas delivered in a single order, first, a ```COUNT``` aggregate function was used to count the total number of deliveries made for each order in conjunction with a ```WHERE``` clause in order to filter the records according to only successful deliveries. The condition set was if the cancellation column has no record for a particular record, if the order was not cancelled by either the restaurant or the customer, then the order was delivered successfully to the customer. An *alias* of ```max_deliveries``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also applied to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table to display the Order ID and the total number of successful deliveries. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Third, a ```GROUP BY``` statement was then used to arrange the results into groups according to the Order ID. Fourth, the ```ORDER BY``` statement was used to organize the total number of successful deliveries made in descending order and place the highest number of successful deliveries made at the top. Lastly, the ```LIMIT``` clause was used to limit the results to only the first record. With that, the query would then return only the highest number of pizzas delivered in a single order.

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
SELECT
    x.customer_id,
    SUM(
        CASE
          WHEN x.exclusions != '' OR x.extras != '' THEN 1
          ELSE 0
        END
    ) AS with_changes,
    SUM(
        CASE
          WHEN x.exclusions = '' AND x.extras = '' THEN 1
          ELSE 0
        END
    ) AS no_changes
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = ''
GROUP BY customer_id
ORDER BY customer_id;
```
#### Explanation:
To determine the number of delivered pizzas that had either requested changes or none, first, two ```SUM``` aggregate functions were used to calculate the total number of delivered pizzas for both conditions. In the first ```SUM``` aggregate function, it consists of a ```CASE``` expression that evaluates whether the given order had requested changes according to the condition that either or both the ```exclusions``` and ```extras``` columns had values and were *not* empty, which would equate to a value of 1. Otherwise, it would equate to a value of 0. The second ```SUM``` aggregate function also consists of a ```CASE``` expression that evaluates whether the given order did *not* have any requested changes according to the condition that either or both the ```exclusions``` and ```extras``` columns had no values and were empty, which would equate to a value of 1. Otherwise, it would equate to a value of 0. *Aliases* of ```with_changes``` and ```no_changes``` were given for both aggregate functions to provide more descriptive column names for the results, respectively. Second, a ```JOIN``` clause was used to combine both the ```cleaned_customer_orders``` and ```cleaned_runner_orders``` tables based on their related column of ```order_id``` to display the Customer ID, evaluate the conditions for the exclusions and extras in the ```CASE``` expressions from the ```cleaned_customer_orders``` table and to later filter the results with the ```cancellation``` column from the ```cleaned_runner_orders```. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Third, a ```WHERE``` clause was used to filter the results based on orders that were only delivered successfully and did not undergo any cancellation. Fourth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID since the results would show the number of requested changes each customer made in their orders from Pizza Runner. Lastly, an ```ORDER BY``` statement was also used to sort the results by default in ascending order based on the Customer ID.

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
SELECT
     COUNT(x.order_id) AS with_exclusions_and_extras
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE (x.exclusions != '' AND x.extras != '') AND y.cancellation = '';
```
#### Explanation:
To determine the number of delivered pizzas that had both requested exclusions and extras, first, a ```COUNT``` aggregate function was used to count the total number of orders. An *alias* of ```with_exclusions_and_extras``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Order ID and to later set the conditions of the ```exclusions``` and ```extras``` columns, as well as the ```cancellation``` column to filter the results. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Third, with that, a ```WHERE``` clause was used to accomplish the filtering of the results based on two conditions where both the ```exclusions``` and ```extras``` columns had values to indicate that the order had both requested exclusions and extras at the same time, and that the given order was successfully delivered and did not undergo any cancellation. 

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
ORDER BY hour_of_the_day;
```
#### Explanation:
To determine the total volume of pizzas ordered for each hour of the day, first, an ```EXTRACT``` function was used to extract a specific field, in this case the hour of the day, from the ```order_time``` column which consists of the specific DateTime value of each order. An *alias* of ```hour_of_the_day``` was given to provide a more descriptive column name for the results. Second, a ```COUNT``` aggregate function was used to count the total number of pizzas according to the Pizza ID. An *alias* of ```total_pizza_orders``` was given to provide a more descriptive column name for the results. Third, a ```GROUP BY``` statement was then used to arrange the results into groups according to the hours of the day. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the hours of the day.

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
To determine the total volume of orders for each day of the week, first, the ```TO_CHAR``` function was used this time as opposed to the ```EXTRACT``` function when getting the values of the days of the week. This is because the ```EXTRACT``` function is particularly used for extracting the numerical value from a specific date or time stamp. The ```TO_CHAR``` function would be more appropriate in displaying the results consisting of the days of the week as it in turn extracts the *names* of the day from a specific date. The valid day format of ```Day``` was applied to display the results according to that specific text format. The values for the days of the week would then be extracted from the ```order_time``` column which consists of both the date and the time when a particular order was made. An *alias* of ```day_of_the_week``` was given to provide a more descriptive column name for the results. Second, a ```COUNT``` aggregate function was used to count the total number of pizza orders according to the Pizza ID. An *alias* of ```total_pizza_orders``` was given to provide a more descriptive column name for the results. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to their corresponding day of the week. Lastly, an ```ORDER BY``` statement was used to sort the results by default according to the day of the week.

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
To determine the number of runners that signed up for each 1 week, first, a CTE labeled ```runner_registration_dates``` was used consiting of two ```EXTRACT``` functions to extract both the day and the month of the runner's registration date. *Aliases* of ```day``` and ```month``` were also given to provide more descriptive column names for the results. This query then displays a clearer view of the exact day and month of when a particular runner signed up, as shown below:

| runner_id |     registration_date    | day | month |
|:---------:|:------------------------:|:---:|:-----:|
|     1     | 2021-01-01T00:00:00.000Z |  1  |   1   |
|     2     | 2021-01-03T00:00:00.000Z |  3  |   1   |
|     3     | 2021-01-08T00:00:00.000Z |  8  |   1   |
|     4     | 2021-01-15T00:00:00.000Z |  15 |   1   |

Following that, first, a ```CASE``` expression was used to determine what week for the month of January did a particular runner sign up. The first condition identifies whether the runner signed up within the first week period, the second condition identifies whether the runner signed up within the second week period, the third condition identifies whether the runner signed up within the third week period, and otherwise, if the runner signed up within the fourth or last week period in the month of January. An *alias* of ```registration_week``` was given to provide a more descriptive column name for the results. Second, a ```COUNT``` aggregate function was used to count the total number of registered runners for each given week. Lastly, a ```GROUP BY``` was then used to arrange the results into groups according to the registration week.

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
To determine the average time (in minutes) it took for each runner to arrive at the Pizza Runner HQ to pickup an order, first, a ```DATE_PART``` function was used to extract the minutes from the resulting difference between a customer's order time and a runner's pickup time. Second, an ```AVG``` (average) aggregate function was used to calculate the average time in minutes. Third, since  PostgreSQL does not define ```ROUND(double precision, integer)```, the resulting average value is casted to be rounded to numeric in order to use the two-argument form of the ```ROUND``` function, which then rounds the results to a whole number. An *alias* of ```avg_pickup_time``` was given to provide a more descriptive column name for the results. Fourth, a ```JOIN``` clause was used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Runner ID, the average difference between the order time and the pickup time, and to filter the results according to successful deliveries. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Fifth, a ```WHERE``` clause was then used to filter the results according to whether a runner was able to successfully deliver the order or not. Sixth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Runner ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the Runner ID.

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
To determine if there is any relationship between pizza quantity and the length of order preparation time, a CTE labeled ```prep_time_per_order``` consisting of a ```COUNT``` aggregate function, a ```DATE_PART``` function, a ```JOIN``` clause, a ```WHERE``` clause, and a ```GROUP BY``` statement was used. First, a ```COUNT``` aggregate function was used to count the total quantity of pizza per order. An *alias* of ```num_of_pizza``` was given to provide a more descriptive column name. Second, a ```DATE_PART``` function was used to extract the minutes from the results of the difference between the time the customer made the order and the time the runner arrived to the Pizza Runner HQ to pick up the order. An *alias* of ```total_prep_time``` was given to provide a more descriptive column name for the results. Third, a ```JOIN``` clause was then used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Order ID, Pizza ID, the length of order preparation time using the Order Time and Pickup Time records, and to later filter the results based on orders that were successfully delivered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Fourth, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the Order ID, Pickup Time, and Order Time. This query then produces the following results:

| order_id | num_of_pizza | total_prep_time |
|:--------:|:------------:|:---------------:|
|     8    |       1      |        20       |
|    10    |       2      |        15       |
|     5    |       1      |        10       |
|     3    |       2      |        21       |
|     7    |       1      |        10       |
|     4    |       3      |        29       |
|     1    |       1      |        10       |
|     2    |       1      |        10       |

Following that, first, an ```AVG``` (average) aggregate function was then used to calculate the average of the total prep time resulting from the CTE to determine the average length of order preparation time that it takes for each pizza quantity. An *alias* of ```avg_total_prep_time``` was given to provide a more descriptive column name for the results. Second, a ```GROUP BY``` statement was used as well to arrange the results into groups according to the quantity of pizza.

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
To determine the average distance runners travelled for each customer, first, an ```AVG``` (average) aggregate function was used to calculate the average distance. Second, given that PostgreSQL does not define ```ROUND(double precision, integer)```, the resulting average needs to be casted to numeric. Third, with that, the ```ROUND``` function was then used to round the results to 1 decimal point. An *alias* of ```avg_distance_travel``` was then given to provide a more descriptive column name for the results. Fourth, a ```JOIN``` clause was then used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Customer ID, the average distance travelled by each runner to deliver the customers' orders, and to later filter the results according to orders that were only successfully delivered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Fifth, a ```WHERE``` clause was then used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Sixth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID. Lastly, an ```ORDER BY``` statement was also used to sort the results by default in ascending order according to the Customer ID.

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
To determine the difference between the longest and shortest delivery times for all orders, first, a ```MAX``` function was used to return the largest value from the selected ```duration``` column which corresponds to the longest delivery time taken for an order. An *alias* of ```max_delivery_time``` was given to provide a more descriptive column name for the results. Second, a ```MIN``` function was then used to return the smallest value from the ```duration``` column which corresponds to the shortest delivery time taken for an order. An *alias* of ```min_delivery_time``` was given to provide a more descriptive column name for the results. Third, the results from both the ```MAX``` and ```MIN``` functions are then subtracted from each other to identify the difference between the longest and shortest delivery times. An *alias* of ```delivery_time_difference``` was given to provide a more descriptive column name for the results. Lastly, a ```WHERE``` clause was also used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation.

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
ORDER BY y.runner_id;
```
#### Explanation:
To determine the average speed for each runner for each delivery, first, the values in the ```duration``` column were converted from minutes to hours as measurements of time by dividing them by 60.0. This conversion is required in order to calculate the average speed. The result of the division was then casted to numeric so it can be rounded off to 2 decimal places using the ```ROUND``` function. An *alias* of ```duration_hr``` was given to provide a more descriptive column name for the results. Second, to calculate the average speed, the distance in kilometers must be divided by the duration in hours. The result is then also casted to numeric so it can be rounded off to 2 decimal places using the ```ROUND``` function as well. An *alias* of ```avg_speed_kph``` was then given to provide a more descriptive column name for the results. Third, a ```JOIN``` clause was then used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Runner ID, the Order ID, the Customer ID, the distance travelled by the runner in delivering the order, the resulting conversion of the duration of the delivery from minutes to hours, the calculated average speed of each runner, and to later filter the results according to orders that were only successfully delivered. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders```, so as to make the query more readable. Fourth, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Fifth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Runner ID, Order ID, Customer ID, duration, and distance. Lastly, an ```ORDER BY``` statement was also used to sort the results by default in ascending order according to the Runner ID.

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
To determine the successful delivery percentage for each runner, first, a ```CASE``` expression was used to set the condition where an order was successfully delivered or not. If it was successfully delivered, it would return the value of 1. Otherwise, it would return 0. Second, given those conditions, a ```SUM``` aggregate function would then be used to count the total number of orders that were successfully delivered. Third, the resulting sum would then be divided by the total number of orders which are counted using the ```COUNT``` aggregate function. The result of the division would then be multipled by 100 to convert the answer to percentage. An *alias* of ```success_delivery_percentage``` was then given to provide a more descriptive column name for the results. Fourth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Runner ID. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the Runner ID.

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
SELECT
    x.pizza_id,
    y.pizza_name,
    STRING_AGG(z.topping_name, ', ') AS pizza_ingredients
FROM cleaned_pizza_recipes x
JOIN pizza_runner.pizza_names y
ON x.pizza_id=y.pizza_id
JOIN pizza_runner.pizza_toppings z
ON x.topping_id=z.topping_id
GROUP BY x.pizza_id, y.pizza_name
ORDER BY x.pizza_id;
```
#### Explanation:
To determine the standard ingredients for each pizza, first, a ```STRING_AGG``` aggregate function was used to concatenate all the names of the toppings available for each type of pizza into a single string. An *alias* of ```pizza_ingredients``` was given to provide a more descriptive column name for the results. Second, two ```JOIN``` clauses were used to first combine the ```cleaned_pizza_recipes``` table with the ```pizza_names``` table based on their related column, ```pizza_id```, to display the Pizza ID and the name of the pizza, and second, to combine the ```cleaned_pizza_recipes``` table with the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the names of the toppings available for each type of pizza. In the joining the tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_pizza_recipes``` table, ```y``` for the ```pizza_names``` table, and ```z``` for the ```pizza_toppings``` table, so as to make the query more readable. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the Pizza ID and the name of the pizza. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the Pizza ID.

#### Output:
| pizza_id | pizza_name |                           pizza_ingredients                           |
|:--------:|:----------:|:---------------------------------------------------------------------:|
|     1    | Meatlovers | BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, Beef |
|     2    | Vegetarian |       Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, Tomatoes      |

#### Answer:
Based from the output of the query, it can be observed that the standard ingredients for the Meatlovers pizza include BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, and Beef. For Vegetarian pizza, it includes Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, and Tomatoes.

- - - -

2. What was the most commonly added extra?
#### Query:
```sql
SELECT
    x.topping_id,
    y.topping_name,
    COUNT(x.topping_id) AS num_of_requests
FROM extras x
JOIN pizza_runner.pizza_toppings y
ON x.topping_id=y.topping_id
GROUP BY x.topping_id, y.topping_name
ORDER BY num_of_requests DESC
LIMIT 1;
```
#### Explanation:
To determine the most commonly added extra topping on pizzas, first, a ```COUNT``` aggregate function was used to count the total number of times a topping was requested by a customer to be added to their order based on the topping ID. An *alias* of ```num_of_requests``` was used to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine both the ```extras``` table and the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the topping ID, the name of the topping, and to count the occurrence of each topping. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```extras``` table and ```y``` for the ```pizza_toppings``` table, so as to make the query more readable. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the topping ID and the topping name. Fourth, an ```ORDER BY``` statement was also used to sort the results in descending order according to the counted number of requests in order to put the largest value at the top. Lastly, the ```LIMIT``` keyword was used to filter the results in displaying only the first record and since the results are sorted in descending order, this would display only the topping record that had the highest number of requests.

#### Output:
| topping_id | topping_name | num_of_requests |
|:----------:|:------------:|:---------------:|
|      1     |     Bacon    |        4        |

#### Answer:
Based from the output of the query, it can be observed that the most commonly added extra topping that customers choose in their pizza order is Bacon, with a total number of 4 requests. It appears Bacon is a popular choice of topping on pizza among customers of Pizza Runner.

- - - -

3. What was the most common exclusion?
#### Query:
```sql
SELECT
    x.topping_id,
    y.topping_name,
    COUNT(x.topping_id) AS num_of_requests
FROM exclusions x
JOIN pizza_runner.pizza_toppings y
ON x.topping_id=y.topping_id
GROUP BY x.topping_id, y.topping_name
ORDER BY num_of_requests DESC
LIMIT 1;
```
#### Explanation:
To determine the most commonly requested topping to be excluded on pizzas, first, a ```COUNT``` aggregate function was used to count the total number of times a topping was requested by a customer to be excluded from their order based on the topping ID. An *alias* of ```num_of_requests``` was used to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was used to combine both the ```exclusions``` table and the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the topping ID, the name of the topping, and to count the occurrence of each topping. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```exclusions``` table and ```y``` for the ```pizza_toppings``` table, so as to make the query more readable. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the topping ID and the topping name. Fourth, an ```ORDER BY``` statement was also used to sort the results in descending order according to the counted number of requests in order to put the largest value at the top. Lastly, the ```LIMIT``` keyword was used to filter the results in displaying only the first record and since the results are sorted in descending order, this would display only the topping record that had the highest number of requests for exclusion.

#### Output:
| topping_id | topping_name | num_of_requests |
|:----------:|:------------:|:---------------:|
|      4     |    Cheese    |        4        |

#### Answer:
Based from the output of the query, it can be observed that the most common topping that customers request to excluded from their pizza order is Cheese, with a total of 4 requests. It appears that in comparison to Bacon, Cheese is not a favorable topping on pizza among customers of Pizza Runner.

- - - -

4. Generate an order item for each record in the ```customers_orders``` table in the format of one of the following:
  * ```Meat Lovers```
  * ```Meat Lovers - Exclude Beef```
  * ```Meat Lovers - Extra Bacon```
  * ```Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers```
#### Query:
```sql
WITH pizza_order AS (
  SELECT
      x.customer_order_id,
      x.customer_id,
      y.pizza_name
  FROM cleaned_customer_orders x
  JOIN pizza_runner.pizza_names y
  ON x.pizza_id=y.pizza_id
  ORDER BY x.customer_order_id
),

exclusions AS (
  SELECT
      x.customer_order_id,
      STRING_AGG(y.topping_name, ', ') AS exclusions
  FROM exclusions x
  JOIN pizza_runner.pizza_toppings y
  ON x.topping_id=y.topping_id
  GROUP BY x.customer_order_id
  ORDER BY x.customer_order_id
),

extras AS (
  SELECT
      x.customer_order_id,
      STRING_AGG(y.topping_name, ', ') AS extras
  FROM extras x
  JOIN pizza_runner.pizza_toppings y
  ON x.topping_id=y.topping_id
  GROUP BY x.customer_order_id
  ORDER BY x.customer_order_id
)

SELECT
    x.customer_order_id,
    x.customer_id,
    CASE
        WHEN y.exclusions IS NULL AND z.extras IS NOT NULL THEN CONCAT(x.pizza_name, ' - Extra ', z.extras)
        WHEN z.extras IS NULL AND y.exclusions IS NOT NULL THEN CONCAT(x.pizza_name, ' - Exclude ', y.exclusions)
        WHEN y.exclusions IS NOT NULL AND z.extras IS NOT NULL THEN CONCAT(x.pizza_name, ' - Exclude ', y.exclusions, ' - Extra ', z.extras)
        ELSE x.pizza_name
    END AS order_details
FROM pizza_order x
LEFT JOIN exclusions y
ON x.customer_order_id=y.customer_order_id
LEFT JOIN extras z
ON x.customer_order_id=z.customer_order_id
GROUP BY x.customer_order_id, x.customer_id, x.pizza_name, y.exclusions, z.extras
ORDER BY x.customer_order_id;
```
#### Explanation:
To generate the order items in the ```customer_orders``` table in the given format, first, 3 CTEs were utilized. The first CTE labeled ```pizza_order``` was used consisting of a ```JOIN``` clause that combines both the ```cleaned_customer_orders``` table and the ```pizza_names``` table based on their related column, ```pizza_id```, to display the ```customer_order_id```, the Customer ID, and the name of the pizza. In joining the two tables, *aliases* were also given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```pizza_names``` table. Second, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column. This CTE then generates a table that shows the name of the pizzas ordered by each customer. This query then produces the following results:

| customer_order_id | customer_id | pizza_name |
|:-----------------:|:-----------:|:----------:|
|         1         |     101     | Meatlovers |
|         2         |     101     | Meatlovers |
|         3         |     102     | Meatlovers |
|         4         |     102     | Vegetarian |
|         5         |     103     | Meatlovers |
|         6         |     103     | Meatlovers |
|         7         |     103     | Vegetarian |
|         8         |     104     | Meatlovers |
|         9         |     101     | Vegetarian |
|         10        |     105     | Vegetarian |
|         11        |     102     | Meatlovers |
|         12        |     103     | Meatlovers |
|         13        |     104     | Meatlovers |
|         14        |     104     | Meatlovers |

The second CTE labeled ```exclusions``` was used consisting of a ```STRING_AGG``` aggregate function, a ```JOIN``` clause, a ```GROUP BY``` statement, and an ```ORDER BY``` statement. First, the ```STRING_AGG``` aggregate function was used to concatenate the names of the toppings that were requested by customers to be excluded from their pizza orders into a single string. An *alias* of ```exclusions``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was then used to combine both the ```exclusions``` table and the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the ```customer_order_id``` and the names of the toppings. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the ```customer_order_id``` column. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column. This CTE then generates a table that shows the name of the toppings that were requested to be excluded by customers from their ordered pizzas. This query then produces the following results:

| customer_order_id |      exclusions      |
|:-----------------:|:--------------------:|
|         5         |        Cheese        |
|         6         |        Cheese        |
|         7         |        Cheese        |
|         12        |        Cheese        |
|         14        | BBQ Sauce, Mushrooms |

The third and final CTE labeled ```extras``` was used consisting of a ```STRING_AGG``` aggregate function, a ```JOIN``` clause, a ```GROUP BY``` statement, and an ```ORDER BY``` statement. First, the ```STRING_AGG``` aggregate function was used to concatenate the names of the toppings that were requested by customers to be added to their pizza orders into a single string. An *alias* of ```extras``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was then used to combine both the ```extras``` table and the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the ```customer_order_id``` and the names of the toppings. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the ```customer_order_id``` column. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column. This CTE then generates a table that shows the name of the toppings that were requested by customers to be added to their ordered pizzas. This query then produces the following results:

| customer_order_id |     extras     |
|:-----------------:|:--------------:|
|         8         |      Bacon     |
|         10        |      Bacon     |
|         12        | Bacon, Chicken |
|         14        |  Bacon, Cheese |

Following that, to now generate the order items in the given specific format, first, a ```CASE``` expression was used to set the conditions of whether a particular order had any requests of extra or excluded toppings in their ordered pizzas. The first condition determines if an order has any requested extra toppings based on the ```exclusions``` column having no value and the ```extras``` column having a value. With that, the ```CONCAT``` function is used to concatenate the name of the pizza, along with the string ``` - Extra```, and the requested extra toppings into a single string to follow the given format. The second condition then determines if an order has any requested toppings to be excluded from their pizza based on the ```extras``` column having no value and the ```exclusions``` column having a value. With that, the ```CONCAT``` function is also used to similarly concatenate the name of the pizza, along with the string ``` - Exclude```, and the requested excluded toppings into a single string to follow the given format. The third condition then determines if an order has both requested extra or excluded toppings from their pizza based on both the ```exclusions``` and ```extras``` columns having a value. With that, the ```CONCAT``` function is used to concatenate the name of the pizza, along with the string ``` - Exclude``` for the requested excluded toppings, and the string ``` - Extra``` for the requested extra toppings into a single string to follow the given format. If a particular order has neither any exclusions or extras, it would only display the name of the pizza. An *alias* of ```order_details``` was given to provide a more descriptive column name for the results. Second, two ```LEFT JOIN``` clauses were used to first combine the resulting table of the ```pizza_order``` CTE and the resulting table of the ```exclusions``` CTE based on their related column, ```customer_order_id```, and second, to also combine the resulting table of the ```pizza_order``` CTE and the resulting table of the ```extras``` CTE based on their related column, ```customer_order_id```, to collectively display the ```customer_order_id```, the Customer ID, and to utilize the exclusions and extras in the ```CASE``` expressions. Third, a ```GROUP BY``` statement was used to arrange the results into groups according to the ```customer_order_id``` column, the Customer ID, the name of the pizza, the exclusions, and extras. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column.

#### Output:
| customer_order_id | customer_id |                          order_details                          |
|:-----------------:|:-----------:|:---------------------------------------------------------------:|
|         1         |     101     |                            Meatlovers                           |
|         2         |     101     |                            Meatlovers                           |
|         3         |     102     |                            Meatlovers                           |
|         4         |     102     |                            Vegetarian                           |
|         5         |     103     |                   Meatlovers - Exclude Cheese                   |
|         6         |     103     |                   Meatlovers - Exclude Cheese                   |
|         7         |     103     |                   Vegetarian - Exclude Cheese                   |
|         8         |     104     |                     Meatlovers - Extra Bacon                    |
|         9         |     101     |                            Vegetarian                           |
|         10        |     105     |                     Vegetarian - Extra Bacon                    |
|         11        |     102     |                            Meatlovers                           |
|         12        |     103     |        Meatlovers - Exclude Cheese - Extra Bacon, Chicken       |
|         13        |     104     |                            Meatlovers                           |
|         14        |     104     | Meatlovers - Exclude BBQ Sauce, Mushrooms - Extra Bacon, Cheese |

#### Answer:
The output of the query displays the list of orders received by Pizza Runner, the ID of the Customer that made the order, and the details of their order such the name of the pizza and if they have any requested exclusions or extra toppings in their pizza order.

- - - -

5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the ```customer_orders``` table and add a 2x in front of any relevant ingredients
  * For example: ```"Meat Lovers: 2xBacon, Beef, ... , Salami"```
#### Query:
```sql
WITH ingredients AS (
  SELECT
      x.pizza_id,
      y.pizza_name,
      x.topping_id,
      z.topping_name
  FROM cleaned_pizza_recipes x
  JOIN pizza_runner.pizza_names y
  ON x.pizza_id=y.pizza_id
  JOIN pizza_runner.pizza_toppings z
  ON x.topping_id=z.topping_id
  ORDER BY x.pizza_id, x.topping_id
),

order_records AS (
  SELECT
      x.customer_order_id,
      x.customer_id,
      x.pizza_id,
      y.pizza_name,
      CASE
          WHEN y.topping_id IN (SELECT b.topping_id FROM extras b WHERE x.customer_order_id=b.customer_order_id) THEN CONCAT('2x', y.topping_name)
          ELSE y.topping_name
      END AS pizza_order_ingredients
  FROM cleaned_customer_orders x
  JOIN ingredients y
  ON x.pizza_id=y.pizza_id
  WHERE y.topping_id NOT IN (SELECT b.topping_id FROM exclusions b WHERE x.customer_order_id=b.customer_order_id)
  ORDER BY x.customer_order_id, x.customer_id, y.topping_name
)

SELECT
    customer_order_id,
    customer_id,
    CONCAT(pizza_name, ': ', STRING_AGG(pizza_order_ingredients, ', ')) AS pizza_order_ingredients
FROM order_records
GROUP BY customer_order_id, customer_id, pizza_name
ORDER BY customer_order_id;

```
#### Explanation:

To generate the alphabetically ordered comma separated ingredient list for each pizza order from the ```customer_orders``` table and to add the ```2x``` in front of any relevant ingredients, 2 CTEs were used. First, a CTE labeled ```ingredients``` was used consisting of 2 ```JOIN``` clauses and an ```ORDER BY``` statement. The first ```JOIN``` clause was used to combine both the ```cleaned_pizza_recipes``` table and the ```pizza_names``` table based on their related column, ```pizza_id```, to display the Pizza ID, the name of the pizza, and the topping ID. The second ```JOIN``` clause was used to also combine the ```cleaned_pizza_recipes``` table with the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the name of the topping. In joining the tables, *aliases* were given, i.e. ```x``` for the ```cleaned_pizza_recipes``` table, ```y``` for the ```pizza_names``` table, and ```z``` for the ```pizza_toppings``` table. An ```ORDER BY``` statement was also used to sort the results by default in ascending order according to the Pizza ID and the topping ID. This CTE then generates a list of ingredients available for each type of pizza. This query then produces the following results:

| pizza_id | pizza_name | topping_id | topping_name |
|:--------:|:----------:|:----------:|:------------:|
|     1    | Meatlovers |      1     |     Bacon    |
|     1    | Meatlovers |      2     |   BBQ Sauce  |
|     1    | Meatlovers |      3     |     Beef     |
|     1    | Meatlovers |      4     |    Cheese    |
|     1    | Meatlovers |      5     |    Chicken   |
|     1    | Meatlovers |      6     |   Mushrooms  |
|     1    | Meatlovers |      8     |   Pepperoni  |
|     1    | Meatlovers |     10     |    Salami    |
|     2    | Vegetarian |      4     |    Cheese    |
|     2    | Vegetarian |      6     |   Mushrooms  |
|     2    | Vegetarian |      7     |    Onions    |
|     2    | Vegetarian |      9     |    Peppers   |
|     2    | Vegetarian |     11     |   Tomatoes   |
|     2    | Vegetarian |     12     | Tomato Sauce |

The second CTE labeled ```order_records``` was used consisting of a ```CASE``` expression, a ```JOIN``` clause, a ```WHERE``` clause, and an ```ORDER BY``` statement was used. First, a ```CASE``` expression was used to set the condition of adding the "2x" string to any extra topping that was requested by the customer to be added to their pizza. If a particular topping matches a topping ID record from the ```extras``` table, then the ```CONCAT``` function is used to concatenate the "2x" and the name of the topping to indicate that the customer requested extra of that particular topping. Otherwise, it would simply return the name of the topping. An *alias* of ```pizza_order_ingredients``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was then used to combine both the ```cleaned_customer_orders``` table and the resulting table of the ```ingredients``` CTE based on their related column, ```pizza_id```, to display the ```customer_order_id``` column, the Customer ID, the Pizza ID, the name of the pizza, and to utilize the topping ID and the name of the topping in the conditions for the ```CASE``` expression. In joining the two tables, *aliases* were given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the resulting table of the ```ingredients``` CTE. Third, a ```WHERE``` clause was then used to filter the results only according to extra toppings and not excluded toppings. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column, the Customer ID, and the name of the topping. This CTE then generates a table where the orders that have additional requested toppings have a "2x" indication for whichever topping it is applicable. This query then produces the following results:

| customer_order_id | customer_id | pizza_id | pizza_name | pizza_order_ingredients |
|:-----------------:|:-----------:|:--------:|:----------:|:-----------------------:|
|         1         |     101     |     1    | Meatlovers |        BBQ Sauce        |
|         1         |     101     |     1    | Meatlovers |          Bacon          |
|         1         |     101     |     1    | Meatlovers |           Beef          |
|         1         |     101     |     1    | Meatlovers |          Cheese         |
|         1         |     101     |     1    | Meatlovers |         Chicken         |
|         1         |     101     |     1    | Meatlovers |        Mushrooms        |
|         1         |     101     |     1    | Meatlovers |        Pepperoni        |
|         1         |     101     |     1    | Meatlovers |          Salami         |
|         2         |     101     |     1    | Meatlovers |        BBQ Sauce        |
|         2         |     101     |     1    | Meatlovers |          Bacon          |
|         2         |     101     |     1    | Meatlovers |           Beef          |
|         2         |     101     |     1    | Meatlovers |          Cheese         |
|         2         |     101     |     1    | Meatlovers |         Chicken         |
|         2         |     101     |     1    | Meatlovers |        Mushrooms        |
|         2         |     101     |     1    | Meatlovers |        Pepperoni        |
|         2         |     101     |     1    | Meatlovers |          Salami         |
|         3         |     102     |     1    | Meatlovers |        BBQ Sauce        |
|         3         |     102     |     1    | Meatlovers |          Bacon          |
|         3         |     102     |     1    | Meatlovers |           Beef          |
|         3         |     102     |     1    | Meatlovers |          Cheese         |
|         3         |     102     |     1    | Meatlovers |         Chicken         |
|         3         |     102     |     1    | Meatlovers |        Mushrooms        |
|         3         |     102     |     1    | Meatlovers |        Pepperoni        |
|         3         |     102     |     1    | Meatlovers |          Salami         |
|         4         |     102     |     2    | Vegetarian |          Cheese         |
|         4         |     102     |     2    | Vegetarian |        Mushrooms        |
|         4         |     102     |     2    | Vegetarian |          Onions         |
|         4         |     102     |     2    | Vegetarian |         Peppers         |
|         4         |     102     |     2    | Vegetarian |       Tomato Sauce      |
|         4         |     102     |     2    | Vegetarian |         Tomatoes        |
|         5         |     103     |     1    | Meatlovers |        BBQ Sauce        |
|         5         |     103     |     1    | Meatlovers |          Bacon          |
|         5         |     103     |     1    | Meatlovers |           Beef          |
|         5         |     103     |     1    | Meatlovers |         Chicken         |
|         5         |     103     |     1    | Meatlovers |        Mushrooms        |
|         5         |     103     |     1    | Meatlovers |        Pepperoni        |
|         5         |     103     |     1    | Meatlovers |          Salami         |
|         6         |     103     |     1    | Meatlovers |        BBQ Sauce        |
|         6         |     103     |     1    | Meatlovers |          Bacon          |
|         6         |     103     |     1    | Meatlovers |           Beef          |
|         6         |     103     |     1    | Meatlovers |         Chicken         |
|         6         |     103     |     1    | Meatlovers |        Mushrooms        |
|         6         |     103     |     1    | Meatlovers |        Pepperoni        |
|         6         |     103     |     1    | Meatlovers |          Salami         |
|         7         |     103     |     2    | Vegetarian |        Mushrooms        |
|         7         |     103     |     2    | Vegetarian |          Onions         |
|         7         |     103     |     2    | Vegetarian |         Peppers         |
|         7         |     103     |     2    | Vegetarian |       Tomato Sauce      |
|         7         |     103     |     2    | Vegetarian |         Tomatoes        |
|         8         |     104     |     1    | Meatlovers |        BBQ Sauce        |
|         8         |     104     |     1    | Meatlovers |         2xBacon         |
|         8         |     104     |     1    | Meatlovers |           Beef          |
|         8         |     104     |     1    | Meatlovers |          Cheese         |
|         8         |     104     |     1    | Meatlovers |         Chicken         |
|         8         |     104     |     1    | Meatlovers |        Mushrooms        |
|         8         |     104     |     1    | Meatlovers |        Pepperoni        |
|         8         |     104     |     1    | Meatlovers |          Salami         |
|         9         |     101     |     2    | Vegetarian |          Cheese         |
|         9         |     101     |     2    | Vegetarian |        Mushrooms        |
|         9         |     101     |     2    | Vegetarian |          Onions         |
|         9         |     101     |     2    | Vegetarian |         Peppers         |
|         9         |     101     |     2    | Vegetarian |       Tomato Sauce      |
|         9         |     101     |     2    | Vegetarian |         Tomatoes        |
|         10        |     105     |     2    | Vegetarian |          Cheese         |
|         10        |     105     |     2    | Vegetarian |        Mushrooms        |
|         10        |     105     |     2    | Vegetarian |          Onions         |
|         10        |     105     |     2    | Vegetarian |         Peppers         |
|         10        |     105     |     2    | Vegetarian |       Tomato Sauce      |
|         10        |     105     |     2    | Vegetarian |         Tomatoes        |
|         11        |     102     |     1    | Meatlovers |        BBQ Sauce        |
|         11        |     102     |     1    | Meatlovers |          Bacon          |
|         11        |     102     |     1    | Meatlovers |           Beef          |
|         11        |     102     |     1    | Meatlovers |          Cheese         |
|         11        |     102     |     1    | Meatlovers |         Chicken         |
|         11        |     102     |     1    | Meatlovers |        Mushrooms        |
|         11        |     102     |     1    | Meatlovers |        Pepperoni        |
|         11        |     102     |     1    | Meatlovers |          Salami         |
|         12        |     103     |     1    | Meatlovers |        BBQ Sauce        |
|         12        |     103     |     1    | Meatlovers |         2xBacon         |
|         12        |     103     |     1    | Meatlovers |           Beef          |
|         12        |     103     |     1    | Meatlovers |        2xChicken        |
|         12        |     103     |     1    | Meatlovers |        Mushrooms        |
|         12        |     103     |     1    | Meatlovers |        Pepperoni        |
|         12        |     103     |     1    | Meatlovers |          Salami         |
|         13        |     104     |     1    | Meatlovers |        BBQ Sauce        |
|         13        |     104     |     1    | Meatlovers |          Bacon          |
|         13        |     104     |     1    | Meatlovers |           Beef          |
|         13        |     104     |     1    | Meatlovers |          Cheese         |
|         13        |     104     |     1    | Meatlovers |         Chicken         |
|         13        |     104     |     1    | Meatlovers |        Mushrooms        |
|         13        |     104     |     1    | Meatlovers |        Pepperoni        |
|         13        |     104     |     1    | Meatlovers |          Salami         |
|         14        |     104     |     1    | Meatlovers |         2xBacon         |
|         14        |     104     |     1    | Meatlovers |           Beef          |
|         14        |     104     |     1    | Meatlovers |         2xCheese        |
|         14        |     104     |     1    | Meatlovers |         Chicken         |
|         14        |     104     |     1    | Meatlovers |        Pepperoni        |
|         14        |     104     |     1    | Meatlovers |          Salami         |

Following that, to generate the necessary results, first, a ```CONCAT``` function was used to concatenate the name of the pizza and ingredients available for each ordered pizza with an indication for extra toppings. A ```STRING_AGG``` aggregate function was used in conjunction with the ```CONCAT``` function to concatenate the list of ingredients into a single string alongside the name of the pizza. An *alias* of ```pizza_order_ingredients``` was given to provide a more descriptive column anme for the results. Second, a ```GROUP BY``` statement was used to arrange the results into groups according to the ```customer_order_id``` column, the Customer ID, and the name of the pizza. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column. 

#### Output:
| customer_order_id | customer_id |                               pizza_order_ingredients                               |
|:-----------------:|:-----------:|:-----------------------------------------------------------------------------------:|
|         1         |     101     |  Meatlovers: BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami  |
|         2         |     101     |  Meatlovers: BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami  |
|         3         |     102     |  Meatlovers: BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami  |
|         4         |     102     |        Vegetarian: Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes       |
|         5         |     103     |      Meatlovers: BBQ Sauce, Bacon, Beef, Chicken, Mushrooms, Pepperoni, Salami      |
|         6         |     103     |      Meatlovers: BBQ Sauce, Bacon, Beef, Chicken, Mushrooms, Pepperoni, Salami      |
|         7         |     103     |            Vegetarian: Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes           |
|         8         |     104     | Meatlovers: BBQ Sauce, 2xBacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami |
|         9         |     101     |        Vegetarian: Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes       |
|         10        |     105     |        Vegetarian: Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes       |
|         11        |     102     |  Meatlovers: BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami  |
|         12        |     103     |    Meatlovers: BBQ Sauce, 2xBacon, Beef, 2xChicken, Mushrooms, Pepperoni, Salami    |
|         13        |     104     |  Meatlovers: BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami  |
|         14        |     104     |           Meatlovers: 2xBacon, Beef, 2xCheese, Chicken, Pepperoni, Salami           |
|                   |             |                                                                                     |

#### Answer:
The output of the query displays the list of orders received by Pizza Runner, the ID of the customer that made the order, and the details of their order such as the type of pizza, the ingredients included which vary according to their requested exclusions and extra toppings in their pizza order. A pizza topping with the indication of '2x' represents an extra request of the topping in the customer's order.

- - - -

6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
#### Query:
```sql
WITH ingredients AS (
  SELECT
      x.pizza_id,
      y.pizza_name,
      x.topping_id,
      z.topping_name
  FROM cleaned_pizza_recipes x
  JOIN pizza_runner.pizza_names y
  ON x.pizza_id=y.pizza_id
  JOIN pizza_runner.pizza_toppings z
  ON x.topping_id=z.topping_id
  ORDER BY x.pizza_id, x.topping_id
),

order_records AS (
  SELECT
      x.customer_order_id,
      x.customer_id,
      x.pizza_id,
      y.pizza_name,
      y.topping_name,
      CASE
          WHEN y.topping_id IN (SELECT b.topping_id FROM extras b WHERE x.customer_order_id=b.customer_order_id) THEN 2
          WHEN y.topping_id IN (SELECT b.topping_id FROM exclusions b WHERE x.customer_order_id=b.customer_order_id) THEN 0
          ELSE 1
      END AS ingredient_count
  FROM cleaned_customer_orders x
  JOIN ingredients y
  ON x.pizza_id=y.pizza_id
  JOIN cleaned_runner_orders z
  ON x.order_id=z.order_id
  WHERE z.cancellation = ''
  ORDER BY x.customer_order_id, x.customer_id
)

SELECT
    topping_name,
    SUM(ingredient_count) AS ingredient_frequency
FROM order_records
GROUP BY topping_name
ORDER BY ingredient_frequency DESC;
```
#### Explanation:

To determine the total quantity of each ingredient used in all delivered pizzas, 2 CTEs were used. First, a CTE labeled ```ingredients``` was used consisting of 2 ```JOIN``` clauses and an ```ORDER BY``` statement. The first ```JOIN``` clause was used to combine both the ```cleaned_pizza_recipes``` table and the ```pizza_names``` table based on their related column, ```pizza_id```, to display the Pizza ID, the name of the pizza, and the topping ID. The second ```JOIN``` clause was used to also combine the ```cleaned_pizza_recipes``` table with the ```pizza_toppings``` table based on their related column, ```topping_id```, to display the name of the topping. In joining the tables, *aliases* were given, i.e. ```x``` for the ```cleaned_pizza_recipes``` table, ```y``` for the ```pizza_names``` table, and ```z``` for the ```pizza_toppings``` table. An ```ORDER BY``` statement was also used to sort the results by default in ascending order according to the Pizza ID and the topping ID. This CTE then generates a list of ingredients available for each type of pizza. This query then produces the following results:

| pizza_id | pizza_name | topping_id | topping_name |
|:--------:|:----------:|:----------:|:------------:|
|     1    | Meatlovers |      1     |     Bacon    |
|     1    | Meatlovers |      2     |   BBQ Sauce  |
|     1    | Meatlovers |      3     |     Beef     |
|     1    | Meatlovers |      4     |    Cheese    |
|     1    | Meatlovers |      5     |    Chicken   |
|     1    | Meatlovers |      6     |   Mushrooms  |
|     1    | Meatlovers |      8     |   Pepperoni  |
|     1    | Meatlovers |     10     |    Salami    |
|     2    | Vegetarian |      4     |    Cheese    |
|     2    | Vegetarian |      6     |   Mushrooms  |
|     2    | Vegetarian |      7     |    Onions    |
|     2    | Vegetarian |      9     |    Peppers   |
|     2    | Vegetarian |     11     |   Tomatoes   |
|     2    | Vegetarian |     12     | Tomato Sauce |

The second CTE labeled ```order_records``` was used consisting of a ```CASE``` expression, two ```JOIN``` clauses, a ```WHERE``` clause, and an ```ORDER BY``` statement was used. First, a ```CASE``` expression was used to set the condition of when an extra topping is requested, it would return a value of 2. If a topping is requested to be excluded from their order, it would return a value of 0. Otherwise, a particular topping ingredient would only return a value of 1. An *alias* of ```ingredient_count``` was given to provide a more descriptive column name for the results. Second, 2 ```JOIN``` clauses were used to first combine the ```cleaned_customer_orders``` table with the resulting table of the ```ingredients``` CTE based on their related column, ```pizza_id```, and second, to combine the ```cleaned_customer_orders``` table with the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the ```customer_order_id``` column, Customer ID, Pizza ID, name of the pizza, name of the topping, and to utilize the topping ID in the conditions in the ```CASE``` expression. In joining the tables, *aliases* were given, i.e. ```x``` for the ```cleaned_customer_orders``` table, ```y``` for the resulting table of the ```ingredients``` CTE, and ```z``` for the ```cleaned_runner_orders``` table. Third, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according to the ```customer_order_id``` column and the Customer ID. This CTE then generates the total number of times a topping was to be added to an ordered pizza. This query then produces the following results: 

| customer_order_id | customer_id | pizza_id | pizza_name | topping_name | ingredient_count |
|:-----------------:|:-----------:|:--------:|:----------:|:------------:|:----------------:|
|         1         |     101     |     1    | Meatlovers |    Cheese    |         1        |
|         1         |     101     |     1    | Meatlovers |     Beef     |         1        |
|         1         |     101     |     1    | Meatlovers |    Salami    |         1        |
|         1         |     101     |     1    | Meatlovers |   Pepperoni  |         1        |
|         1         |     101     |     1    | Meatlovers |   Mushrooms  |         1        |
|         1         |     101     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         1         |     101     |     1    | Meatlovers |     Bacon    |         1        |
|         1         |     101     |     1    | Meatlovers |    Chicken   |         1        |
|         2         |     101     |     1    | Meatlovers |    Chicken   |         1        |
|         2         |     101     |     1    | Meatlovers |     Beef     |         1        |
|         2         |     101     |     1    | Meatlovers |     Bacon    |         1        |
|         2         |     101     |     1    | Meatlovers |    Salami    |         1        |
|         2         |     101     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         2         |     101     |     1    | Meatlovers |   Pepperoni  |         1        |
|         2         |     101     |     1    | Meatlovers |    Cheese    |         1        |
|         2         |     101     |     1    | Meatlovers |   Mushrooms  |         1        |
|         3         |     102     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         3         |     102     |     1    | Meatlovers |     Bacon    |         1        |
|         3         |     102     |     1    | Meatlovers |     Beef     |         1        |
|         3         |     102     |     1    | Meatlovers |    Cheese    |         1        |
|         3         |     102     |     1    | Meatlovers |    Chicken   |         1        |
|         3         |     102     |     1    | Meatlovers |   Mushrooms  |         1        |
|         3         |     102     |     1    | Meatlovers |   Pepperoni  |         1        |
|         3         |     102     |     1    | Meatlovers |    Salami    |         1        |
|         4         |     102     |     2    | Vegetarian | Tomato Sauce |         1        |
|         4         |     102     |     2    | Vegetarian |    Peppers   |         1        |
|         4         |     102     |     2    | Vegetarian |   Mushrooms  |         1        |
|         4         |     102     |     2    | Vegetarian |    Cheese    |         1        |
|         4         |     102     |     2    | Vegetarian |    Onions    |         1        |
|         4         |     102     |     2    | Vegetarian |   Tomatoes   |         1        |
|         5         |     103     |     1    | Meatlovers |     Bacon    |         1        |
|         5         |     103     |     1    | Meatlovers |   Pepperoni  |         1        |
|         5         |     103     |     1    | Meatlovers |   Mushrooms  |         1        |
|         5         |     103     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         5         |     103     |     1    | Meatlovers |    Chicken   |         1        |
|         5         |     103     |     1    | Meatlovers |    Cheese    |         0        |
|         5         |     103     |     1    | Meatlovers |    Salami    |         1        |
|         5         |     103     |     1    | Meatlovers |     Beef     |         1        |
|         6         |     103     |     1    | Meatlovers |     Beef     |         1        |
|         6         |     103     |     1    | Meatlovers |    Salami    |         1        |
|         6         |     103     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         6         |     103     |     1    | Meatlovers |    Cheese    |         0        |
|         6         |     103     |     1    | Meatlovers |    Chicken   |         1        |
|         6         |     103     |     1    | Meatlovers |     Bacon    |         1        |
|         6         |     103     |     1    | Meatlovers |   Mushrooms  |         1        |
|         6         |     103     |     1    | Meatlovers |   Pepperoni  |         1        |
|         7         |     103     |     2    | Vegetarian |   Tomatoes   |         1        |
|         7         |     103     |     2    | Vegetarian |    Peppers   |         1        |
|         7         |     103     |     2    | Vegetarian |    Cheese    |         0        |
|         7         |     103     |     2    | Vegetarian |    Onions    |         1        |
|         7         |     103     |     2    | Vegetarian | Tomato Sauce |         1        |
|         7         |     103     |     2    | Vegetarian |   Mushrooms  |         1        |
|         8         |     104     |     1    | Meatlovers |     Bacon    |         2        |
|         8         |     104     |     1    | Meatlovers |     Beef     |         1        |
|         8         |     104     |     1    | Meatlovers |   Mushrooms  |         1        |
|         8         |     104     |     1    | Meatlovers |    Cheese    |         1        |
|         8         |     104     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         8         |     104     |     1    | Meatlovers |    Salami    |         1        |
|         8         |     104     |     1    | Meatlovers |   Pepperoni  |         1        |
|         8         |     104     |     1    | Meatlovers |    Chicken   |         1        |
|         10        |     105     |     2    | Vegetarian |    Onions    |         1        |
|         10        |     105     |     2    | Vegetarian | Tomato Sauce |         1        |
|         10        |     105     |     2    | Vegetarian |   Tomatoes   |         1        |
|         10        |     105     |     2    | Vegetarian |    Cheese    |         1        |
|         10        |     105     |     2    | Vegetarian |   Mushrooms  |         1        |
|         10        |     105     |     2    | Vegetarian |    Peppers   |         1        |
|         11        |     102     |     1    | Meatlovers |   Pepperoni  |         1        |
|         11        |     102     |     1    | Meatlovers |    Cheese    |         1        |
|         11        |     102     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         11        |     102     |     1    | Meatlovers |    Chicken   |         1        |
|         11        |     102     |     1    | Meatlovers |   Mushrooms  |         1        |
|         11        |     102     |     1    | Meatlovers |     Bacon    |         1        |
|         11        |     102     |     1    | Meatlovers |    Salami    |         1        |
|         11        |     102     |     1    | Meatlovers |     Beef     |         1        |
|         13        |     104     |     1    | Meatlovers |    Salami    |         1        |
|         13        |     104     |     1    | Meatlovers |     Bacon    |         1        |
|         13        |     104     |     1    | Meatlovers |   Pepperoni  |         1        |
|         13        |     104     |     1    | Meatlovers |   Mushrooms  |         1        |
|         13        |     104     |     1    | Meatlovers |    Chicken   |         1        |
|         13        |     104     |     1    | Meatlovers |   BBQ Sauce  |         1        |
|         13        |     104     |     1    | Meatlovers |    Cheese    |         1        |
|         13        |     104     |     1    | Meatlovers |     Beef     |         1        |
|         14        |     104     |     1    | Meatlovers |   BBQ Sauce  |         0        |
|         14        |     104     |     1    | Meatlovers |     Beef     |         1        |
|         14        |     104     |     1    | Meatlovers |   Pepperoni  |         1        |
|         14        |     104     |     1    | Meatlovers |    Cheese    |         2        |
|         14        |     104     |     1    | Meatlovers |     Bacon    |         2        |
|         14        |     104     |     1    | Meatlovers |   Mushrooms  |         0        |
|         14        |     104     |     1    | Meatlovers |    Salami    |         1        |
|         14        |     104     |     1    | Meatlovers |    Chicken   |         1        |

Following that, first, a ```SUM``` aggregate function was used to calculate the total quantity of each ingredient used in all delivered pizzas. An *alias* of ```ingredient_frequency``` was given to provide a more descriptive column name. Second, a ```GROUP BY``` statement was used to arrange the results into groups according to the name of the topping. Lastly, an ```ORDER BY``` statement was used to sort the results according to the frequency of each ingredient in descending order which puts the most used ingredient at the top.

#### Output:
| topping_name | ingredient_frequency |
|:------------:|:--------------------:|
|     Bacon    |          11          |
|   Mushrooms  |          11          |
|    Cheese    |          10          |
|   Pepperoni  |           9          |
|    Salami    |           9          |
|    Chicken   |           9          |
|     Beef     |           9          |
|   BBQ Sauce  |           8          |
|   Tomatoes   |           3          |
|    Onions    |           3          |
|    Peppers   |           3          |
| Tomato Sauce |           3          |

#### Answer:
Based from the output of the query, it can be observed that the most frequent ingredient used in all delivered pizzas are either Bacon or Mushrooms, followed by Cheese. The least used ingredients, on the other hand, are Tomatoes, Onions, Peppers, and Tomato Sauce.

- - - -

### D. Pricing and Ratings <a href="anchor" id="pricing-ratings"></a>
1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
#### Query:
```sql
SELECT
    SUM(
      CASE
          WHEN x.pizza_id = 1 THEN 12
          ELSE 10
      END ) AS total_profit
FROM cleaned_customer_orders x
JOIN cleaned_runner_orders y
ON x.order_id=y.order_id
WHERE y.cancellation = '';
```
#### Explanation:
To determine Pizza Runner's current total profit based on the given prices, first, a ```CASE``` expression was used to set the condition of associating the type of pizza with an ID of 1 with the price of $12 and the type of pizza with an ID of 2 with the price of $10. An *alias* of ```total_profit``` was given to provide a more descriptive column name for the results. Second, a ```SUM``` aggregate function is then used to calculate the total amount for each type of pizza ordered by customers. Third, a ```JOIN``` clause was used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to utilize the Pizza ID in the condition for the ```CASE``` expression and to later filter the results according to the ```cancellation``` column. Lastly, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation.

#### Output:
| total_profit |
|:------------:|
|      138     |

#### Answer:
Based from the output of the query, it can be observed that Pizza Runner had attained a total profit of $138 so far.

- - - -

2. What if there was an additional $1 charge for any pizza extras?
  * Add cheese is $1 extra
#### Query:
```sql
WITH extra_count AS (
  SELECT
      customer_order_id,
      COUNT(topping_id) AS num_extras
  FROM extras
  GROUP BY customer_order_id
)



SELECT
    SUM(
      CASE
          WHEN x.pizza_id = 1 THEN
              CASE
                  WHEN x.customer_order_id IN (SELECT customer_order_id FROM extra_count) THEN 12 + y.num_extras
                  ELSE 12
              END
          ELSE
              CASE
                  WHEN x.customer_order_id IN (SELECT customer_order_id FROM extra_count) THEN 10 + y.num_extras
                  ELSE 10
              END
       END
      ) AS total_profit
FROM cleaned_customer_orders x
LEFT JOIN extra_count y
ON x.customer_order_id=y.customer_order_id
JOIN cleaned_runner_orders z
ON x.order_id=z.order_id
WHERE z.cancellation = '';
```
#### Explanation:

To determine Pizza Runner's current total profit based on the change that there was an additional $1 for any extra toppings, first, a CTE labeled ```extra_count``` was used consisting of a ```COUNT``` aggregate function and a ```GROUP BY``` statement. First, a ```COUNT``` aggregate function was used to count the total number of times a particular topping was requested to be added on their order. An *alias* of ```num_extras``` was given to provide a more descriptive column name for the results. Second, a ```GROUP BY``` statement was then used to arrange the results into groups according to the ```customer_order_id``` column. This CTE then generates a list of the total number of times a topping was requested to be added in their order. This query then produces the following results:

| customer_order_id | num_extras |
|:-----------------:|:----------:|
|         10        |      1     |
|         14        |      2     |
|         12        |      2     |
|         8         |      1     |

Following that, first, a nested ```CASE``` expression was used to set the condition of calculating the associated price and the additional charges for each extra topping for each ordered pizza. The outer ```CASE``` expression sets the first condition of when the Pizza ID has a value of 1, then it would proceed to an inner ```CASE``` expression that sets the condition of when an order ID matches an Order ID record in the resulting table of the ```extra_count``` CTE, it would then calculate the sum of the associated price of $12 and the additional $1 charge for each extra topping. Otherwise, if the order has no extra toppings, it would only return its default price of $12. When the Pizza ID has a value of 2, it would also be evaluated with similar conditions. Second, a ```SUM``` aggregate function was then used to calculate the total amount for each type of pizza ordered by customers. An *alias* of ```total_profit``` was given to provide a more descriptive column anme for the results. Third, a ```LEFT JOIN``` was used to combine both the ```cleaned_customer_orders``` table and the resulting table of the ```extra_count``` CTE based on their related column ```customer_order_id``` to display the ```customer_order_id```column and the number of times a topping was requested to be added to an order. This is to ensure that all results will be displayed even if there were no requested extra toppings. Another ```JOIN``` clause was also used to combine the ```cleaned_customer_orders``` table with the ```cleaned_runner_orders``` table to later filter the results using the ```cancellation``` column. In contrast to the ```LEFT JOIN``` clause, an ```INNER JOIN``` was used to ensure that only records that match the condition in the ```WHERE``` clause would be displayed. Lastly, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation.

#### Output:
| total_profit |
|:------------:|
|      142     |

#### Answer:
Based from the output of the query, it can be observed that Pizza Runner earned a total profit of $142 so far with the additional $1 charge for extra toppings.

- - - -

3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
#### Query:
```sql
DROP TABLE IF EXISTS runner_ratings;

CREATE TABLE runner_ratings (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "rating" INTEGER,
  "comment" TEXT
);

INSERT INTO runner_ratings
  ("order_id", "runner_id", "rating", "comment")
VALUES
  (1, 1, 4, 'Good service.'),
  (2, 1, 5, 'Excellent service! Very accommodating, would definitely recommend.'),
  (3, 1, 5, 'Delivered on time. Delivery man was very polite and courteous. Would recommend!'),
  (4, 2, 1, 'Poor service. Took an hour to get food. Would not recommend.'),
  (5, 3, 5, 'Great service. Picked up and delivered securely and on time.'),
  (7, 2, 5, 'Arrived on time and delivered quickly. Great service.'),
  (8, 2, 5, 'Good service. Efficient and reliable.'),
  (10, 1, 5, 'Excellent and professional service. Highly recommend.');
```
#### Explanation:
To create a new table for the runner rating system, first, the ```DROP TABLE IF EXISTS``` statement was used to initially check if a ```runner_ratings``` table already exists. If it does, it would be dropped. Otherwise, the action of dropping the table would simply be skipped. Second, the ```CREATE TABLE``` statement was then used to create a new table named ```runner_ratings``` in the database. The specified parameters include an Order ID column of integer data type, a Runner ID column of integer data type, a Rating column of integer data type to accommodate the "1-5" rating system, and a Comment column of text data type which would include any additional comments that customers would want to deliver to the Pizza Runner HQ. Third, the ```INSERT INTO``` statement was then used to insert new records into the ```runner_ratings``` table. Note that orders with the ID of 6 and 9 do not have any ratings nor comments since they were cancelled.

#### Output:
| order_id | runner_id | rating |                                     comment                                     |
|:--------:|:---------:|:------:|:-------------------------------------------------------------------------------:|
|     1    |     1     |    4   |                                  Good service.                                  |
|     2    |     1     |    5   |        Excellent service! Very accommodating, would definitely recommend.       |
|     3    |     1     |    5   | Delivered on time. Delivery man was very polite and courteous. Would recommend! |
|     4    |     2     |    1   |           Poor service. Took an hour to get food. Would not recommend.          |
|     5    |     3     |    5   |           Great service. Picked up and delivered securely and on time.          |
|     7    |     2     |    5   |              Arrived on time and delivered quickly. Great service.              |
|     8    |     2     |    5   |                      Good service. Efficient and reliable.                      |
|    10    |     1     |    5   |              Excellent and professional service. Highly recommend.              |

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
SELECT
    x.customer_id,
    x.order_id,
    y.runner_id,
    y.rating,
    x.order_time,
    z.pickup_time,
    DATE_PART('minute', z.pickup_time - x.order_time) AS total_prep_time_mins,
    z.duration AS delivery_duration_mins,
    ROUND((z.distance / (z.duration / 60.0))::NUMERIC, 2) AS avg_speed_kph,
    COUNT(x.pizza_id) AS total_num_pizza
FROM cleaned_customer_orders x
JOIN pizza_runner.runner_ratings y
ON x.order_id=y.order_id
JOIN cleaned_runner_orders z
ON x.order_id=z.order_id
WHERE z.cancellation = ''
GROUP BY x.customer_id, x.order_id, y.runner_id, y.rating, x.order_time, z.pickup_time, z.duration, z.distance
ORDER BY x.order_id;
```
#### Explanation:
To combine all the necessary information, first, a ```DATE_PART``` function was used to extract the resulting minutes of the calculated difference between the time the customer made the order and the time the order was picked up by the runner from the Pizza Runner HQ. An *alias* of ```total_prep_time_mins``` was given to provide a more descriptive column name for the results. Second, the average speed was calculated by dividing the distance (in kilometers) travelled by the runner and the duration of the travel which was also converted from minutes to hours. The result of the division was then casted to numeric in order to round off the value into 2 decimal places. An *alias* of ```avg_speed_kph``` was given to provide a more descriptive column name for the results. Third, a ```COUNT``` aggregate function was used to count the total number of times a type of pizza was ordered. Fourth, 2 ```JOIN``` clauses were used where the first ```JOIN``` clause combines the ```cleaned_customer_orders``` table and the ```runner_ratings``` table based on their related column, ```order_id```, to display the Customer ID, Order ID, Runner ID, Rating, Order Time, Pickup Time, and to utilize the Pizza ID in the ```COUNT``` aggregate function. The second ```JOIN``` clause then combines the ```cleaned_customer_orders``` table with the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Duration and to also utilize the Pickup Time in the ```DATE_PART``` function, the Distance and Duration in calculating the average speed and the ```cancellation``` column in the ```WHERE``` clause. Fifth, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Sixth, a ```GROUP BY``` statement was used to arrange the results into groups according to the Customer ID, Order ID, Runner ID, Rating, Order Time, Pickup Time, Duration, and Distance columns. Lastly, an ```ORDER BY``` statement was used to sort the results by default in ascending order according the Order ID. 

#### Output:
| customer_id | order_id | runner_id | rating |        order_time        |        pickup_time       | total_prep_time_mins | delivery_duration_mins | avg_speed_kph | total_num_pizza |
|:-----------:|:--------:|:---------:|:------:|:------------------------:|:------------------------:|:--------------------:|:----------------------:|:-------------:|:---------------:|
|     101     |     1    |     1     |    4   | 2020-01-01T18:05:02.000Z | 2020-01-01T18:15:34.000Z |          10          |           32           |     37.50     |        1        |
|     101     |     2    |     1     |    5   | 2020-01-01T19:00:52.000Z | 2020-01-01T19:10:54.000Z |          10          |           27           |     44.44     |        1        |
|     102     |     3    |     1     |    5   | 2020-01-02T23:51:23.000Z | 2020-01-03T00:12:37.000Z |          21          |           20           |     40.20     |        2        |
|     103     |     4    |     2     |    1   | 2020-01-04T13:23:46.000Z | 2020-01-04T13:53:03.000Z |          29          |           40           |     35.10     |        3        |
|     104     |     5    |     3     |    5   | 2020-01-08T21:00:29.000Z | 2020-01-08T21:10:57.000Z |          10          |           15           |     40.00     |        1        |
|     105     |     7    |     2     |    5   | 2020-01-08T21:20:29.000Z | 2020-01-08T21:30:45.000Z |          10          |           25           |     60.00     |        1        |
|     102     |     8    |     2     |    5   | 2020-01-09T23:54:33.000Z | 2020-01-10T00:15:02.000Z |          20          |           15           |     93.60     |        1        |
|     104     |    10    |     1     |    5   | 2020-01-11T18:34:49.000Z | 2020-01-11T18:50:20.000Z |          15          |           10           |     60.00     |        2        |

- - - -

5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
#### Query:
```sql
WITH costs AS (
   SELECT
      x.order_id,
      SUM(
        CASE
            WHEN x.pizza_id = 1 THEN 12
            ELSE 10
        END
      ) AS total_profit,
      y.runner_id,
      (y.distance * 0.30) AS runner_profit
  FROM cleaned_customer_orders x
  JOIN cleaned_runner_orders y
  ON x.order_id=y.order_id
  WHERE y.cancellation = ''
  GROUP BY x.order_id, y.runner_id, y.distance
)

SELECT
     ROUND(SUM(total_profit - runner_profit::NUMERIC), 2) AS remaining_total_profit
FROM costs;
```
#### Explanation

To determine Pizza Runner's current total profit given that each runner is paid $0.30 per kilometre traveled for deliveries, first, a CTE labeled ```costs``` consisting of a ```SUM``` aggregate function with a ```CASE``` expression, a ```JOIN``` clause, a ```WHERE``` clause, and a ```GROUP BY``` statement. First, the ```CASE``` expression was used to set the condition of when the ordered pizza has an ID value of 1, then it would be associated with the price of $12. If the ordered pizza has an ID value of 2 otherwise, it would be associated with the price of $10. Second, a ```SUM``` aggregate function was then used to calculate the total amount for each type of pizza for each order. An *alias* of ```total_profit``` was given to provide a more descriptive column name for the results. Third, to calculate the runner's profit, the distance travelled by each runner is multipled by 0.30. An *alias* of ```runner_profit``` was given to provide a more descriptive column name for the results. Fourth, a ```JOIN``` clause was used to combine both the ```cleaned_customer_orders``` table and the ```cleaned_runner_orders``` table based on their related column, ```order_id```, to display the Order ID, Runner ID, and to utilize the Pizza ID for the ```CASE``` expression and the distance column in calculating the total profit each runner was able to accumulate. In joining the two tables, *aliases* were given, i.e. ```x``` for the ```cleaned_customer_orders``` table and ```y``` for the ```cleaned_runner_orders``` table. Fifth, a ```WHERE``` clause was used to filter the results according to orders that were only successfully delivered and did not undergo any cancellation. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the Order ID, Runner ID, and Distance. This CTE then generates a table that calculates the total cost for each type of pizza and the total profit each runner was able to accumulate based on the distance they travelled to deliver each order. This query then produces the following results:

Following that, a ```SUM``` aggregate function was then used to calculate the sum of the total profit deducted by the amount paid to each runner. Then, the result was casted into numeric to use the ```ROUND()``` function to round off the value up to 2 decimal places. An *alias* of ```remaining_total_profit``` was given to provide a more descriptive column name.

#### Output:
| remaining_total_profit |
|:----------------------:|
|          94.44         |

#### Answer:
Based from the output of the query, it can be observed that Pizza Runner has a remaining total profit of $94.44 after the deliveries.
- - - -

## Bonus Questions <a href="anchor" id="bonus-questions"></a>
1. If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
#### Query:
```sql
INSERT INTO pizza_names
 ("pizza_id", "pizza_name")
VALUES
 (3, 'Supreme');

INSERT INTO pizza_recipes
 ("pizza_id", "toppings")
VALUES
 (3, '1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12');
```
#### Explanation:
To add a new type of pizza, the Supreme pizza, to the Pizza Runner menu, first, an ```INSERT INTO``` statement was used to insert a new record into the ```pizza_names``` table. The new record is given an ID of 3 under the name "Supreme". Second, an ```INSERT INTO``` statement was also used to insert a new record as well into the ```pizza_recipes``` table. The new type of pizza was said to have *all* of the toppings so all the available toppings were listed as its ingredients.

#### Output:
* Query to display results for ```pizza_names``` table:
```sql
SELECT *
FROM pizza_runner.pizza_recipes;
```
* Results:
| pizza_id |                toppings               |
|:--------:|:-------------------------------------:|
|     1    |        1, 2, 3, 4, 5, 6, 8, 10        |
|     2    |           4, 6, 7, 9, 11, 12          |
|     3    | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 |

* Query to display results for ```cleaned_pizza_recipes``` table:
```sql
SELECT *
FROM cleaned_pizza_recipes;
```
* Results:
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
|     3    |      1     |
|     3    |      2     |
|     3    |      3     |
|     3    |      4     |
|     3    |      5     |
|     3    |      6     |
|     3    |      7     |
|     3    |      8     |
|     3    |      9     |
|     3    |     10     |
|     3    |     11     |
|     3    |     12     |
