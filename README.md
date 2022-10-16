# DannyMa_week2_challenge

### Introduction

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

###  Available Data


Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.


![Screenshot (197)](https://user-images.githubusercontent.com/109418747/194479007-fec07c95-212f-423e-84db-125319b4a1af.png)


### Analysis

### SECTION A

```
--1. How many pizzas were ordered?

SELECT COUNT([pizza_id]) AS Total_orders
FROM [pizza_runner].[customer_orders]

```
![Screenshot (198)](https://user-images.githubusercontent.com/109418747/194529713-1ac7e47f-5e69-43a6-90db-00e0591aa267.png)


```
--2. How many unique customer orders were made?

SELECT COUNT(DISTINCT [order_id]) Unique_orders
FROM[pizza_runner].[customer_orders]

```

![Screenshot (199)](https://user-images.githubusercontent.com/109418747/194527396-a7ad2799-5d93-4b57-9779-1ad9a092b8fb.png)

```
--3. How many successful orders were delivered by each runner?

SELECT COUNT([order_id]) Orders,[runner_id]
FROM [pizza_runner].[runner_orders]
WHERE  [pickup_time] != 'null'
GROUP BY [runner_id];

```

![Screenshot (200)](https://user-images.githubusercontent.com/109418747/194527606-c19a31b3-d423-4538-8d31-cd6c53346290.png)


```

--4.How many of each type of pizza was delivered?

SELECT a.[pizza_id],
      COUNT (b.order_id) count
FROM [pizza_runner].[customer_orders] a
LEFT JOIN [pizza_runner].[runner_orders] b
ON a.[order_id]= b.[order_id]
WHERE b.[duration] != 'null'
GROUP BY a.pizza_id;

```

![Screenshot (201)](https://user-images.githubusercontent.com/109418747/194527813-fc5086a8-9e57-4f1a-9d25-2eec521d16b0.png)

```

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

```

![Screenshot (211)](https://user-images.githubusercontent.com/109418747/194530334-5123cc5a-077a-471e-93f1-70864d435ea1.png)

```
    --6. What was the maximum number of pizzas delivered in a single order?
    
     SELECT b.[order_id], count(a.[pizza_id]) count
     FROM [pizza_runner].[customer_orders] a
     LEFT JOIN [pizza_runner].[runner_orders] b
     ON a.order_id = b.order_id
     WHERE [pickup_time] != 'null'
     GROUP BY b.[order_id]
     ORDER BY count(a.[pizza_id]) DESC
```

![Screenshot (202)](https://user-images.githubusercontent.com/109418747/194528092-0aaf8f42-4410-44d2-9396-1effd3f80774.png)


```
 --7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
 
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

```

![Screenshot (203)](https://user-images.githubusercontent.com/109418747/194528334-6785a9d2-718e-440a-b9c7-2de512a02105.png)

```

--8. How many pizzas were delivered that had both exclusions and extras?

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

```

![Screenshot (205)](https://user-images.githubusercontent.com/109418747/194529389-560f3d34-923f-4af8-85d5-880b49340df2.png)


```
 --9. What was the total volume of pizzas ordered for each hour of the day?
 
SELECT DATEPART(hour, CAST(order_time AS DATETIME2)),
COUNT(*) AS pizza_ordered
FROM pizza_runner.customer_orders
GROUP BY DATEPART(hour,  CAST(order_time AS DATETIME2)) 
ORDER BY pizza_ordered DESC;

```

![Screenshot (206)](https://user-images.githubusercontent.com/109418747/194529242-b23dc765-6108-457c-baa8-7f15c6fb92ed.png)



```
--10. What was the volume of orders for each day of the week?

SELECT DATEPART(day, CAST(order_time AS DATETIME2)) Day_of_week,
COUNT(*) AS pizza_ordered
FROM pizza_runner.customer_orders
GROUP BY DATEPART(day,  CAST(order_time AS DATETIME2)) 
ORDER BY pizza_ordered DESC;

```
![Screenshot (208)](https://user-images.githubusercontent.com/109418747/194529044-4bb1015c-663d-4e16-bf64-700a3216c123.png)


### SECTION B

```
--1. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

WITH time AS (SELECT  CAST(b.order_time AS datetime2) order_time,
a.runner_id,
CASE WHEN a.[pickup_time] ='null' THEN NULL 
ELSE CAST(a.[pickup_time] AS DATETIME) END pick_time
FROM [pizza_runner].[runner_orders] a
LEFT JOIN [pizza_runner].[customer_orders] b
ON a.[order_id]= b.[order_id])
SELECT runner_id,
AVG(DATEDIFF(minute,order_time, pick_time)) average_time_in_mins
FROM time
WHERE pick_time IS NOT NULL
GROUP BY runner_id

```

![Screenshot (214)](https://user-images.githubusercontent.com/109418747/194857427-e9f3f8c9-a948-4f82-bf91-aa4c0ed1f9dd.png)


```

--2. What was the average speed for each runner for each delivery and do you notice any trend for these values?

SELECT [order_id],
       [runner_id],
      ROUND(AVG(distance/hours),2) average_speed
FROM(
SELECT [order_id],
[runner_id],
CAST(SUBSTRING([distance],1,2) AS INT) distance,
CAST(SUBSTRING([duration],1,2) AS INT)/60.0 AS hours
FROM [pizza_runner].[runner_orders]
WHERE [pickup_time] != 'null') Speed
GROUP BY [order_id],
         [runner_id]
```
         
![Screenshot (215)](https://user-images.githubusercontent.com/109418747/194886962-91ac0048-10b3-41ea-8a22-fc7bea558b2a.png)
         
```
 --3. What was the difference between the longest and shortest delivery times for all orders?

SELECT  MAX(CAST(SUBSTRING([duration],1,2) AS INT))- MIN (CAST(SUBSTRING([duration],1,2) AS INT)) time_difference
FROM
[pizza_runner].[runner_orders] 
WHERE [duration] !='null'

```

![Screenshot (216)](https://user-images.githubusercontent.com/109418747/194887282-c416d8f5-e8d1-4ff9-92bd-df6f53f2d522.png)

```

--4. What was the average distance travelled for each customer?

SELECT  b.[customer_id], avg(CAST(SUBSTRING(a.[distance],1,2) AS INT)) average_distance
FROM
[pizza_runner].[runner_orders] a
LEFT JOIN [pizza_runner].[customer_orders] b
ON a.order_id= b.order_id
WHERE a.distance !='null'
GROUP BY b.[customer_id];
```

![Screenshot (217)](https://user-images.githubusercontent.com/109418747/194887586-26f11788-09bf-466e-b868-adee37098287.png)


```

--5. Is there any relationship between the number of pizzas and how long the order takes to prepare?

WITH time AS (SELECT  CAST(b.order_time AS datetime2) order_time,
a.[order_id],
count(b.[pizza_id]) count,
CASE WHEN a.[pickup_time] ='null' THEN NULL 
ELSE CAST(a.[pickup_time] AS DATETIME) END pick_time
FROM [pizza_runner].[runner_orders] a
LEFT JOIN [pizza_runner].[customer_orders] b
ON a.[order_id]= b.[order_id]
group by a.[order_id],  order_time,CASE WHEN a.[pickup_time] ='null' THEN NULL 
ELSE CAST(a.[pickup_time] AS DATETIME) END)
SELECT [order_id], count,
AVG(DATEDIFF(minute,order_time, pick_time)) average_time_in_mins
FROM time
WHERE pick_time IS NOT NULL
GROUP BY [order_id],count;

```

![Screenshot (219)](https://user-images.githubusercontent.com/109418747/194907690-1f43368f-4192-46bb-80c0-24e67d27e664.png)


```

--6. What is the successful delivery percentage for each runner?

WITH successful_count AS(
SELECT [runner_id],
       COUNT([order_id]) count
FROM [pizza_runner].[runner_orders]
WHERE [pickup_time] != 'null'
GROUP BY [runner_id])

 SELECT a.[runner_id], CONCAT( ((a.count/ b.all_count)*100),'%')
 FROM successful_count a
 LEFT JOIN
  (SELECT CAST(COUNT(*) AS FLOAT) all_count,[runner_id]
FROM [pizza_runner].[runner_orders]
GROUP BY [runner_id]) b
ON a.runner_id= b.runner_id
```

![Screenshot (218)](https://user-images.githubusercontent.com/109418747/194908063-582352c8-5e50-4cf3-a029-e067ef2f9ea1.png)

### SECTION C

```
----1. What are the standard ingredients for each pizza?
SELECT a. [pizza_id], b.topping_name
 FROM(SELECT [pizza_id] , value [toppings]  
FROM   [pizza_runner].[pizza_recipes]
    CROSS APPLY STRING_SPLIT([toppings] , ',')) a 
LEFT JOIN [pizza_runner].[pizza_toppings] b
ON a.toppings =b.topping_id

```

![Screenshot (220)](https://user-images.githubusercontent.com/109418747/195984710-6492f8f1-d22b-48e4-9d91-1b3b43995221.png)

```
--2. What was the most commonly added extra?
WITH extra AS (
SELECT a. [pizza_id], b.topping_name
 FROM(   SELECT [pizza_id] , value [toppings]  
FROM   [pizza_runner].[pizza_recipes]
    CROSS APPLY STRING_SPLIT([toppings] , ',') ) a 
LEFT JOIN [pizza_runner].[pizza_toppings] b
ON a.toppings =b.topping_id
)
SELECT a.[pizza_id], a.topping_name, 
CASE WHEN b.extras IN( ' ', 'null') THEN NULL
     ELSE b.extras END  No_of_extras
INTO #ext
FROM extra a
LEFT JOIN [pizza_runner].[customer_orders] b
ON a.pizza_id = b.pizza_id;

SELECT *
FROM #ext;

SELECT topping_name, COUNT( CAST(No_of_extras AS INT)) extra
FROM (SELECT  pizza_id,CAST(topping_name AS varchar(MAX)) AS topping_name,
value No_of_extras
FROM #ext
CROSS APPLY STRING_SPLIT(No_of_extras, ',')
where No_of_extras is not null) a
GROUP BY topping_name
ORDER BY COUNT( CAST(No_of_extras AS INT)) DESC
```

```
---3. What was the most common exclusion?
WITH exclusion AS (
SELECT a. [pizza_id], b.topping_name
 FROM(   SELECT [pizza_id] , value [toppings]  
FROM   [pizza_runner].[pizza_recipes]
    CROSS APPLY STRING_SPLIT([toppings] , ',') ) a 
LEFT JOIN [pizza_runner].[pizza_toppings] b
ON a.toppings =b.topping_id
)
SELECT a.[pizza_id], a.topping_name, 
CASE WHEN b.exclusions IN('null') THEN NULL
     ELSE b.exclusions END  No_of_exclusion
INTO #exclusions
FROM exclusion a
LEFT JOIN [pizza_runner].[customer_orders] b
ON a.pizza_id = b.pizza_id;

SELECT *
FROM #exclusions;

SELECT topping_name, COUNT( CAST( No_of_exclusion AS INT)) exclusion
FROM (SELECT  pizza_id,CAST(topping_name AS varchar(MAX)) AS topping_name,
value  No_of_exclusion
FROM #exclusions
CROSS APPLY STRING_SPLIT(No_of_exclusion, ',')
where  No_of_exclusion is not null AND No_of_exclusion != '') a
GROUP BY topping_name
ORDER BY COUNT( CAST( No_of_exclusion AS INT)) DESC
```
