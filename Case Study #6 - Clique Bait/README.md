# Case Study #6: Clique Bait

## Introduction
Clique Bait is not like your regular online seafood store - the founder and CEO Danny, was also a part of a digital data analytics team and wanted to expand his knowledge into the seafood industry!

In this case study - you are required to support Danny's vision and analyse his dataset and come up with creative solutions to calculate funnel fallout rates for the Clique Bait online store.

## Problem Statement
Danny wants to analyze the sales data for his online seafood store, Clique Bait, to calculate funnel fallout rates.

## Table of Contents
1. [Enterprise Relationship Diagram](#erd)
2. [Digital Analysis](#digital-analysis)
3. [Product Funnel Analysis)(#product-funnel-analysis)
4. [Campaigns Analysis](#campaigns-analysis)

## Enterprise Relationship Diagram <a href="anchor" id="erd"></a>

- - - -

## Case Study Questions and Answers
### A. Digital Analysis <a href="anchor" id="digital-analysis"></a>
1. How many users are there?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

2. How many cookies does each user have on average?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

3. What is the unique number of visits by all users per month?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

4. What is the number of events for each event type?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

5. What is the percentage of visits which have a purchase event?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

6. What is the percenatage of visits which view the checkout page but do not have a purchase event?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

7. What are the top 3 pages by number of views?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

8. What is the number of views and cart adds for each product category?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

9. What are the top 3 products by purchases?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

### B. Product Funnel Analysis <a href="anchor" id="product-funnel-analysis"></a>
Using a single SQL query - create a new output table which has the following details:
* How many times was each product viewed?
* How many times was each product added to cart?
* How many times was each product added to a cart but not purchased (abandoned)?
* How many times was each product purchased/

Additionally, create another table which further aggregates the data for the above points but this time for each product category instead of individual products.

Use your 2 new output tables - answer the ff. questions:
1. Which product had the most views, cart adds and purchases?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

2. Which product was most likely to be abandoned?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

3. Which product had the highest view to purchase percentage?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

4. What is the average conversion rate from view to cart add?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

5. What is the average conversion rate from cart add to purchase?
#### Query:
```sql

```

#### Explanation:

#### Output:

#### Answer:

- - - -

### C. Campaigns Analysis <a href="anchor" id="campaigns-analysis"></a>
Generate a table that has 1 single row for every unique ```visit_id``` record and has the ff. columns:
* ```user_id```
* ```visit_id```
* ```visit_start_time```: the earliest ```event_time``` for each visit
* ```page_views```: count of page views for each visit
* ```cart_adds```: count of product cart add events for each visit
* ```purchase```: 1/0 flag if a purchase event exists for each visit
* ```campaign_name```: map the visit to a campaign if the ```visit_start_time``` falls between the ```start_date``` and ```end_date```
* ```impression```: count of ad impressions for each visit
* ```click```: count of ad clicks for each visit
* <b>(Optional column)</b> ```cart_products```: a comma separated text value with products added to the cart sorted by the order they were added to the cart (hint: use the ```sequence_number```)

