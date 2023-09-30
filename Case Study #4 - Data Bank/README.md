# Case Study #4: Data Bank

## Introduction
There is a new innovation in the financial industry called Neo-Banks: new aged digital only banks without physical branches.

Danny thought that there should be some sort of intersection between these new age banks, cryptocurrency and the data world…so he decides to launch a new initiative - Data Bank!

Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!

## Problem Statement
The Data Bank management team, led by Danny, wants to increase their total customer base of their digital bank as well as determine how much data storage their customers need. Overall, the Data Bank management team wants to analyze their data in a smart way in order to better forecast and plan for their future developments.

## Entity Relationship Diagram
![Capture 4](https://github.com/daphnevee/8-Week-SQL-Challenge/assets/127839925/21917626-b235-45ba-9f1a-b4231b546e6c)

## Table of Contents
1. [Customer Nodes Exploration](#customer-nodes-exploration)
2. [Customer Transactions](#customer-transactions)
3. [Data Allocation Challenge](#data-allocation-challenge)
4. [Extra Challenge](#extra-challenge)
5. [Extension Request](#extension-request)

## Case Study Questions and Answers

### A. Customer Nodes Exploration <a href="anchor" id="customer-nodes-exploration"></a>
1. How many unique nodes are there on the Data Bank system?
#### Query:
```sql
SELECT
   COUNT(DISTINCT node_id) AS unique_nodes
FROM data_bank.customer_nodes;
```

#### Explanation:
To determine the number of unique nodes existing in the Data Bank system, a ```COUNT``` aggregate function, together with a ```DISTINCT``` clause, was used to count the total number of unique nodes from the ```customer_nodes``` table records. An *alias* of ```unique_nodes``` was given to provide a more descriptive column name for the results.

#### Output:
| unique_nodes |
|:------------:|
|       5      |

#### Answer: 
Based from the output of the query, it can be observed that there is a total of 5 unique nodes existing in the Data Bank system.

- - - -

2. What is the number of nodes per region?
#### Query:
```sql
SELECT
   y.region_name,
   COUNT(DISTINCT x.node_id) AS unique_nodes
FROM data_bank.customer_nodes x
JOIN data_bank.regions y
ON x.region_id=y.region_id
GROUP BY y.region_name;
```

#### Explanation:
To determine the number of nodes per region, first, a ```COUNT``` aggregate function, together with a ```DISTINCT``` clause, was used to count the total number of unique nodes that are available per region. An *alias* of ```unique_nodes``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used to combine both the ```customer_nodes``` table and the ```regions``` table based on their related column, ```region_id```, to display the names of the regions along with the total count of nodes available per region. *Aliases* were also given, i.e. ```x``` for the ```customer_nodes``` table and ```y``` for the ```regions``` table to make the query more readable. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to region.

#### Output:
| region_name | unique_nodes |
|:-----------:|:------------:|
|    Africa   |       5      |
|   America   |       5      |
|     Asia    |       5      |
|  Australia  |       5      |
|    Europe   |       5      |

#### Answer: 
Based from the output of the query, it can be observed that each of the 5 regions have a total of 5 unique nodes available.

- - - -

3. How many customers are allocated to each region?
#### Query:
```sql
SELECT
   y.region_name,
   COUNT(DISTINCT x.customer_id) AS total_customers
FROM data_bank.customer_nodes x
JOIN data_bank.regions y
ON x.region_id=y.region_id
GROUP BY y.region_name;
```

#### Explanation:
To determine the total number of customers allocated to each region, first, a ```COUNT``` aggregate function, together with a ```DISTINCT``` clause, was used to count the total number of unique customers from each region. An *alias* of ```total_customers``` was given to provide a more descriptive column name for the results. Second, a ```JOIN``` clause was also used to combine both the ```customer_nodes``` table and the ```regions``` table based on their related column, ```region_id```, to display the names of the regions along with the total count of customers allocated to each region. *Aliases* were also given, i.e. ```x``` for the ```customer_nodes``` table and ```y``` for the ```regions``` table to make the query more readable. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to region.

#### Output:
| region_name | total_customers |
|:-----------:|:---------------:|
|    Africa   |       102       |
|   America   |       105       |
|     Asia    |        95       |
|  Australia  |       110       |
|    Europe   |        88       |

#### Answer: 
Based from the output of the query, it can be observed that a total of 102 customers are allocated in Africa, 105 customers are allocated in America, 95 customers are allocated in Asia, 110 customers are allocated in Australia, and 88 customers are allocated in Europe.

- - - -

4. How many days on average are customers reallocated to a different node?
#### Query:
```sql
WITH customer_reallocation AS (
  SELECT
      customer_id,
      node_id,
      start_date,
      end_date,
      LEAD(node_id, 1) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_node,
      LEAD(start_date, 1) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_node_start_date
  FROM data_bank.customer_nodes
)

SELECT
   ROUND(AVG(next_node_start_date - start_date)) AS avg_node_reallocation_in_days
FROM customer_reallocation
WHERE node_id != next_node;
```

#### Explanation:
To determine the number of days on average it takes for a customer to be reallocated to a different node, first, a CTE labeled ```customer_reallocation``` was established to identify each customer's next assigned node by using the ```LEAD``` function. The ```LEAD``` function allows access to succeeding rows based on a specified index, which in this case is 1 which accesses the following immediate row to identify both the ID and the starting date of the next node. *Aliases* were also given, i.e. ```next_node``` for the next node ID column and ```next_node_start_date``` for the next node starting date column, to display more descriptive column names for the results.

Following that, an ```AVG``` aggregate function was then applied on the resulting difference between the starting date of the customer's current node and the starting date of the next node they are reallocated to. A ```ROUND``` function was also used to round off the result to the nearest whole number. An *alias* of ```avg_node_reallocation_in_days``` was also given to provide a more descriptive column name for the results. Furthermore, a ```WHERE``` clause was used to filter out the records where the next node ID a customer is reallocated to is the same as their previously assigned node. Given the reallocation is done randomly, there is a possibility that they are reassigned to the same node and since we are looking for the average time it takes for a customer to be reallocated to a *different* node, such records are then disregarded for the time being.

#### Output:
| avg_node_reallocation_in_days |
|:-----------------------------:|
|               16              |

#### Answer: 
Based from the output of the query, it can be observed that it takes an average of 16 days for each customer to be reallocated to a different node.

- - - -

5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
#### Query:
```sql
WITH customer_reallocation_per_region AS (
  SELECT
      x.customer_id,
      y.region_name,
      x.node_id,
      x.start_date,
      x.end_date,
      LEAD(x.node_id, 1) OVER (PARTITION BY x.customer_id ORDER BY x.start_date) AS next_node,
      LEAD(x.start_date, 1) OVER (PARTITION BY x.customer_id ORDER BY x.start_date) AS next_node_start_date
  FROM data_bank.customer_nodes x
  JOIN data_bank.regions y
  ON x.region_id=y.region_id
),

customer_reallocation_duration AS (
  SELECT
      customer_id,
      region_name,
      node_id,
      next_node,
      (next_node_start_date - start_date) AS node_reallocation_in_days
  FROM customer_reallocation_per_region
  WHERE node_id != next_node
)

SELECT
    region_name,
    PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY node_reallocation_in_days) AS node_reallocation_median,
    PERCENTILE_DISC(0.8) WITHIN GROUP (ORDER BY node_reallocation_in_days) AS node_reallocation_percentile_80,
    PERCENTILE_DISC(0.95) WITHIN GROUP (ORDER BY node_reallocation_in_days) AS node_reallocation_percentile_95
FROM customer_reallocation_duration
GROUP BY region_name;
```

#### Explanation:
To determine the median, 80th percentile, and 95th percentile for the same reallocation days metric per region, 2 CTEs were established. The 1st CTE labeled ```customer_reallocation_per_region``` is similar with the CTE established in the previous question, since this question branches from that, except each customer's region is now identified. A ```JOIN``` clause is used to combine both the ```customer_nodes``` table and ```regions``` table based on their related column, ```region_id```, to display the Customer ID, the name of the region of their location, the Node ID they are currently assigned to, the starting and ending dates of their assignment to the current node, and the succeeding node they are assigned to which is identified using the ```LEAD``` function. *Aliases* were also given, i.e. ```x``` for the ```customer_nodes``` table and ```y``` for the ```regions``` table, to make the query more readable. The 2nd CTE labeled ```customer_reallocation_duration``` then calculates the amount of time (in days) it takes for a customer to be reallocated to a different node by subtracting the starting date of the current node they are assigned to and the starting date of the succeeding node they will be assigned to. An *alias* of ```node_reallocation_in_days``` is also given to provide a more descriptive column name for the results. A ```WHERE``` clause is also used similarly with the previous question in order to filter out the records where the current node that a customer is assigned to is also the same as the next node they are reallocated to. This is because the reallocation operation is done randomly so there is possibility for the customer to be reassigned to the same node. 

Followed by this, to calculate the median, 80th percentile, and 95th percentile, the ```PERCENTILE_DISC``` aggregate function was used. The ```PERCENTILE_DISC``` aggregate function was used as opposed to the ```PERCENTILE_CONT``` function because the former calculates for discrete values while the latter calculates for continuous values. The 1st ```PERCENTILE_DISC``` function accepts a value of ```0.5``` to calculate the median of the amount of time (in days) customer-node reallocation takes per region. The 2nd ```PERCENTILE_DISC``` function accepts a value of ```0.8``` to calculate the 80th percentile of the amount of time (in days) taken for customer-node reallocation per region. The 3rd ```PERCENTILE_DISC``` function accepts a value of ```0.95``` to calculate the 95th percentile of the amount of time (in days) taken for customer-node reallocation per region. *Aliases* were also given, i.e. ```node_reallocation_median``` for the median calculation, ```node_reallocation_percentile_80``` for the 80th percentile calculation, and ```node_reallocation_percentile_95``` for the 95th percentile calculation. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to region.

#### Output:
| region_name | node_reallocation_median | node_reallocation_percentile_80 | node_reallocation_percentile_95 |
|:-----------:|:------------------------:|:-------------------------------:|:-------------------------------:|
|    Africa   |            16            |                25               |                29               |
|   America   |            16            |                24               |                29               |
|     Asia    |            15            |                24               |                29               |
|  Australia  |            15            |                24               |                29               |
|    Europe   |            16            |                26               |                29               |

#### Answer: 
Based from the output of the query, the amount of time (in days) taken for customer-node reallocation for Africa is at a median of 16 days, with 25 days being the 80th percentile and 29 days being the 95th percentile. As for America, it has a median of 16 days as well, with 24 days being the 80th percentile and 29 days being the 95th percentile. For Asia, it has a median of 15 days, with 24 days being the 80th percentile and 29 days being the 95th percentile. For Australia, it has a median of 15 days, with 24 days being the 80th percentile and 29 days being the 95th percentile. Lastly, for Europe, it has a median of 16 days, with 26 days being the 80th percentile and 29 days being the 95th percentile.

- - - -

### B. Customer Transactions <a href="anchor" id="customer-transactions"></a>
1. What is the unique count and total amount for each transaction type?
#### Query:
```sql
SELECT
    txn_type,
    COUNT(customer_id) AS transaction_count,
    SUM(txn_amount) AS transaction_total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type;
```

#### Explanation:
To determine the unique count and total amount for each transaction type, first, a ```COUNT``` aggregate function was used to count the total number of customer deposits, withdrawals, and purchases made using their Data Bank debit card. An *alias* of ```transaction_count``` was given to provide a more descriptive column name for the results. Second, a ```SUM``` aggregate function was used to calculate the total amount accumulated for each transaction type. An *alias* of ```transaction_total_amount``` was given to provide a more descriptive column name for the results. Lastly, a ```GROUP BY``` statement was used to arrange the results into groups according to the transaction type.

#### Output:
|  txn_type  | transaction_count | transaction_total_amount |
|:----------:|:-----------------:|:------------------------:|
|  purchase  |        1617       |          806537          |
|   deposit  |        2671       |          1359168         |
| withdrawal |        1580       |          793003          |

#### Answer: 
Based from the output of the query, it can be observed that a total number of 1,617 customer purchases were made using their Data Bank debit card, with an accumulated total amount of $806,537 spent. On the other hand, a total number of 2,671 customer deposits were made, with an accumulated total amount of $1,359,168 deposited into their accounts. Lastly, a total number of 1,580 customer withdrawals were made, with an accumulated total amount of $793,003 withdrawn from their accounts.

- - - -

2. What is the average total historical deposit counts and amounts for all customers?
#### Query:
```sql
WITH customer_deposits AS (
  SELECT
      customer_id,
      COUNT(customer_id) AS customer_deposit_count,
      AVG(txn_amount) AS average_deposit_amount
  FROM data_bank.customer_transactions
  WHERE txn_type = 'deposit'
  GROUP BY customer_id
)

SELECT
    ROUND(AVG(customer_deposit_count)) AS average_total_deposits,
    ROUND(AVG(average_deposit_amount), 2) AS average_deposit_amount
FROM customer_deposits;
```

#### Explanation:
To determine the average total historical deposit counts and amounts for all customers, first, a CTE labeled ```customer_deposits``` was utilized to determine the amount of times each customer has made a deposit into their Data Bank accounts and the average amount they have deposited. A ```COUNT``` aggregate function was used to count the occurrences each customer has made a deposit. An *alias* of ```customer_deposit_count``` was given to provide a more descriptive column name for the results. An ```AVG``` aggregate function was used to calculate the average amount each customer has deposited into their Data Bank accounts. An *alias* of ```average_deposit_amount``` was also given to provide a more descriptive column name for the results. A ```WHERE``` clause was used to filter the records based on customers that have historically made a deposit. Lastly, a ```GROUP BY``` statement was also used to arrange the results into groups according to the Customer ID.

Followed by this, the ```AVG``` aggregate function was used twice, the first was to calculate the average total historical deposit counts based from the resulting table of the ```customer_deposits``` CTE, and the second was to calculate the average total amount for all deposits made by customers. A ```ROUND``` function was also utilized to, first, round off the average deposit count to the nearest whole number and second, to round off the average deposit amount to 2 decimal places. *Aliases* were also given, i.e. ```average_total_deposits``` and ```average_deposit_amount``` for each resulting aggregate column, respectively. 

#### Output:
| average_total_deposits | average_deposit_amount |
|:----------------------:|:----------------------:|
|            5           |         508.61         |

#### Answer: 
Based from the output of the query, it can be observed that an average count of 5 customers make a deposit into their Data Bank accounts and the average amount they typically deposit is around $508.61.

- - - -

3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
#### Query:
```sql
WITH customer_monthly_transactions AS (
  SELECT
      customer_id,
      DATE_PART('Month', txn_date) AS txn_month_id,
      TO_CHAR(txn_date, 'Month') AS txn_month_name,
      SUM(
        CASE
          WHEN txn_type = 'deposit' THEN 1
          ELSE 0
        END
      ) AS deposit_count,
      SUM(
        CASE
          WHEN txn_type = 'purchase' THEN 1
          ELSE 0
        END
      ) AS purchase_count,
      SUM(
        CASE
          WHEN txn_type = 'withdrawal' THEN 1
          ELSE 0
        END
      ) AS withdrawal_count
  FROM data_bank.customer_transactions
  GROUP BY customer_id, txn_month_id, txn_month_name
  ORDER BY customer_id, txn_month_id
)

SELECT
    txn_month_name,
    COUNT(customer_id) AS total_customers
FROM customer_monthly_transactions
WHERE deposit_count > 1 AND (purchase_count = 1 OR withdrawal_count = 1)
GROUP BY txn_month_id, txn_month_name
ORDER BY txn_month_id;
```

#### Explanation:
To determine the total number of customers that have made more than 1 deposit and either 1 purchase or 1 withdrawal within a single month, a CTE labeled ```customer_monthly_transactions``` was defined to display the amount of times each customer has made a deposit, purchase, or withdrawal for each month. First, a ```DATE_PART``` function was used to extract the month from the transaction date column and was given an *alias* of ```txn_month_id``` since the ```DATE_PART``` function returns an integer value of the month. Second, a ```TO_CHAR``` function was then used to convert the extracted month value from the transaction date column to a string and was given an *alias* of ```txn_month_name```. The ```TO_CHAR``` function was added to the query to make the results more comprehensible by displaying the month name as opposed to the results of the ```DATE_PART``` function which only displays the month ID. Third, a ```SUM``` aggregate function, in conjunction with a ```CASE``` expression, was used to perform the calculations of counting the amount of times each customer has made a deposit, purchase, or withdrawal within each month. In the first ```CASE``` expression, the initial condition returns a value of ```1``` if the customer has made a deposit. Otherwise, it would return a value of ```0```. The ```SUM``` aggregate function then adds up all of the records that return a value of ```1``` and serves as a counter for the occurrences when the customer has made a deposit. The second ```CASE``` expression performs the same evaluation except this time, it returns a value of ```1``` if the customer has made a purchase. The third ```CASE``` expression returns a value of ```1``` if the customer has made a withdrawal. The ```SUM``` aggregate function is also applied in the second and third ```CASE``` expressions to count the number of occurrences when the customer has made a purchase and a withdrawal, respectively. *Aliases* were also given, i.e. ```deposit_count``` for the first ```CASE``` expression, ```purchase_count``` for the second ```CASE``` expression, and ```withdrawal_count``` for the third ```CASE``` expression. Fourth, a ```GROUP BY``` statement is used to arrange the results into groups according to the Customer ID, the Month ID, and the Month Name. Lastly, an ```ORDER BY``` statement is used to organize the results by default in ascending order according to both the Customer ID and the Month ID to follow a chronological order.

Followed by this,

#### Output:
| txn_month_name | total_customers |
|:--------------:|:---------------:|
|     January    |       115       |
|    February    |       108       |
|      March     |       113       |
|      April     |        50       |

#### Answer: 
Based from the output of the query, it can be observed that a total number of 115 customers have made more than 1 deposit and either 1 purchase or 1 withdrawal within the month of January, 108 for the month of February, 113 for the month of March, and 50 for the month of April.

- - - -

4. What is the closing balance for each customer at the end of the month?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

5. What is the percentage of customers who increase their closing balance by more than 5%?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

### C. Data Allocation Challenge <a href="anchor" id="data-allocation-challenge"></a>
To test out a few different hypotheses - the Data Bank team wants to run an experiment where different groups of customers would be allocated data using 3 different options:
* Option 1: data is allocated based off the amount of money at the end of the previous month
* Option 2: data is allocated on the average amount of money kept in the account in the previous 30 days
* Option 3: data is updated real-time

For this multi-part challenge question - you have been requested to generate the following data elements to help the Data Bank team estimate how much data will need to be provisioned for each option:
* running customer balance column that includes the impact each transaction
* customer balance at the end of each month
* minimum, average and maximum values of the running balance for each customer

Using all of the data available - how much data would have been required for each option on a monthly basis?

- - - -

### D. Extra Challenge <a href="anchor" id="extra-challenge"></a>
Data Bank wants to try another option which is a bit more difficult to implement - they want to calculate data growth using an interest calculation, just like in a traditional savings account you might have with a bank.

If the annual interest rate is set at 6% and the Data Bank team wants to reward its customers by increasing their data allocation based off the interest calculated on a daily basis at the end of each day, how much data would be required for this option on a monthly basis?

Special notes:

* Data Bank wants an initial calculation which does not allow for compounding interest, however they may also be interested in a daily compounding interest calculation so you can try to perform this calculation if you have the stamina!

- - - -

### Extension Request <a href="anchor" id="extension-request"></a>
The Data Bank team wants you to use the outputs generated from the above sections to create a quick Powerpoint presentation which will be used as marketing materials for both external investors who might want to buy Data Bank shares and new prospective customers who might want to bank with Data Bank.

1. Using the outputs generated from the customer node questions, generate a few headline insights which Data Bank might use to market it’s world-leading security features to potential investors and customers.

2. With the transaction analysis - prepare a 1 page presentation slide which contains all the relevant information about the various options for the data provisioning so the Data Bank management team can make an informed decision.
