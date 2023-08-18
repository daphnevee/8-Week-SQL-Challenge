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

## Data Cleaning
### Table 2: customer_orders

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

### Table 3: runner_orders
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

SELECT
	pizza_id,
	pizza_name,
FROM cleaned_runner_orders
WHERE cancellation = ''
GROUP BY runner_id;
-->
### A. Pizza Metrics
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
```
#### Explanation:

#### Output:

#### Answer:

- - - -

5. How many Vegetarian and Meatlovers were ordered by each customer?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -
  
6. What was the maximum number of pizzas delivered in a single order?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

8. How many pizzas were delivered that had both exclusions and extras?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

9. What was the total volume of pizzas ordered for each hour of the day?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

10. What was the volume of orders for each day of the week?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

### B. Runner and Customer Experience
1. How many runners signed up for each 1 week period? (i.e. week starts ```2021-01-01```)
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

4. What was the average distance travelled for each customer?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

5. What was the difference between the longest and shortest delivery times for all orders?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

7. What is the successful delivery percentage for each runner?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

### C. Ingredient Optimisation
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

### D. Pricing and Ratings
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

## Bonus Questions
1. If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:
