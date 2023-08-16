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

## Case Study Questions and Answers
### A. Pizza Metrics
1. How many pizzas were ordered?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

2. How many unique customer orders were made?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

- - - -

3. How many successful orders were delivered by each runner?
#### Query:
```sql
```
#### Explanation:

#### Output:

#### Answer:

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
