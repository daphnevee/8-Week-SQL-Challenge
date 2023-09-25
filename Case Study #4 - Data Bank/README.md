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

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

### B. Customer Transactions <a href="anchor" id="customer-transactions"></a>
1. What is the unique count and total amount for each transaction type?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

2. What is the average total historical deposit counts and amounts for all customers?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

- - - -

3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer: 

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
