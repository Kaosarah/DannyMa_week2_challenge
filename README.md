# DannyMa_week2_challenge

### Introduction

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

###  Available Data


Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.


![Screenshot (197)](https://user-images.githubusercontent.com/109418747/194479007-fec07c95-212f-423e-84db-125319b4a1af.png)


```
--*How many pizzas were ordered?
SELECT COUNT([pizza_id]) AS Total_orders
FROM [pizza_runner].[customer_orders]

--How many unique customer orders were made?
SELECT COUNT(DISTINCT [order_id]) Unique_orders
FROM[pizza_runner].[customer_orders]

--How many successful orders were delivered by each runner?
SELECT COUNT([order_id]) Orders,[runner_id]
FROM [pizza_runner].[runner_orders]
WHERE  [pickup_time] != 'null'
GROUP BY [runner_id];


--How many of each type of pizza was delivered?

SELECT a.[pizza_id],
      COUNT (b.order_id) count
FROM [pizza_runner].[customer_orders] a
LEFT JOIN [pizza_runner].[runner_orders] b
ON a.[order_id]= b.[order_id]
WHERE b.[duration] != 'null'
GROUP BY a.pizza_id;


--5.How many Vegetarian and Meatlovers were ordered by each customer?
WITH pizza AS(SELECT [order_id],[customer_id],[pizza_id],
CASE WHEN [pizza_id]= 1 THEN 1
ELSE 0 END meatlover,
CASE WHEN [pizza_id] = 2 THEN 1
ELSE 0 END vegetarian
FROM [pizza_runner].[customer_orders])

SELECT [customer_id], SUM(meatlover) meatlover, SUM(vegetarian) vegetarian
FROM pizza
GROUP BY  [customer_id]

    --6.What was the maximum number of pizzas delivered in a single order?
     SELECT b.[order_id], count(a.[pizza_id]) count
     FROM [pizza_runner].[customer_orders] a
     LEFT JOIN [pizza_runner].[runner_orders] b
     ON a.order_id = b.order_id
     WHERE [pickup_time] != 'null'
     GROUP BY b.[order_id]
     ORDER BY count(a.[pizza_id]) DESC

 --7.For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
SELECT a.customer_id,
      count( a.[pizza_id]) delivered_pizza,
      SUM( CASE WHEN [exclusions] ='' AND [extras] = '' THEN 1
        WHEN [exclusions] ='null' AND [extras] = '' THEN 1
        WHEN [exclusions] ='' AND [extras] IS NULL THEN 1
        WHEN [exclusions] ='' AND [extras] = 'null' THEN 1
        WHEN [exclusions] ='null' AND [extras] = 'null' THEN 1
        ELSE 0 END) no_changes,
     SUM(  CASE WHEN [exclusions] ='' AND [extras] = '' THEN 0
        WHEN [exclusions] ='null' AND [extras] = '' THEN 0
        WHEN [exclusions] ='' AND [extras] IS NULL THEN 0
        WHEN [exclusions] ='' AND [extras] = 'null' THEN 0
        WHEN [exclusions] ='null' AND [extras] = 'null' THEN 0
        ELSE 1 END) changes
FROM [pizza_runner].[customer_orders] a
LEFT JOIN [pizza_runner].[runner_orders] b
ON a.order_id=b.order_id
WHERE b.pickup_time != 'null'
GROUP BY a.customer_id;


--8.How many pizzas were delivered that had both exclusions and extras?
WITH delivered_pizza AS (SELECT b.order_id,
       a.pizza_id,
       CASE WHEN [exclusions] ='' AND [extras] = '' THEN 0
        WHEN [exclusions] ='null' AND [extras] = '' THEN 0
        WHEN [exclusions] ='' AND [extras] IS NULL THEN 0
        WHEN [exclusions] = '4' AND [extras] = '' THEN 0
        WHEN [exclusions] ='null' AND [extras] = '1' THEN 0
        WHEN [exclusions] ='null' AND [extras] = 'null' THEN 0
        WHEN  [exclusions] ='null' AND [extras] = 'null' THEN 0
        ELSE 1 END delivered_with_exclusions_extras
FROM [pizza_runner].[customer_orders] a
LEFT JOIN [pizza_runner].[runner_orders] b
ON a.order_id = b.order_id
WHERE b.pickup_time != 'null')
SELECT COUNT(*) delivered_with_exclusion_extra
FROM delivered_pizza
WHERE delivered_with_exclusions_extras = 1;


 --9.What was the total volume of pizzas ordered for each hour of the day?
SELECT DATEPART(hour, CAST(order_time AS DATETIME2)),
COUNT(*) AS pizza_ordered
FROM pizza_runner.customer_orders
GROUP BY DATEPART(hour,  CAST(order_time AS DATETIME2)) 
ORDER BY pizza_ordered DESC;

--What was the volume of orders for each day of the week?
SELECT DATEPART(day, CAST(order_time AS DATETIME2)) Day_of_week,
COUNT(*) AS pizza_ordered
FROM pizza_runner.customer_orders
GROUP BY DATEPART(day,  CAST(order_time AS DATETIME2)) 
ORDER BY pizza_ordered DESC;


```
