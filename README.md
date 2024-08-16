# SQL Analysis of Pizza Orders
Under the guidance of [Mentorness](https://www.linkedin.com/company/mentorness/)

# Overview
The main aim of this project is to analyze pizza sales to gain insights into customer behaviour, popular pizza types, sales trends and overall performance.

The analysis was performed on the these table:

 - [**Order details**](/Dataset/order_details.csv)
    - **order_details_id**: Unique identifier for the order detail. 
    - **order_id**: Identifier linking to the orders table. 
    - **pizza_id**: Identifier linking to the pizza table. 
    - **quantity**: Number of pizzas ordered. 
 
 - [**Orders**](/Dataset/orders.csv)
    - **order_id**: Unique identifier for the order.
    - **date**: Date the order was placed.
    - **time**: Time the order was placed.

 - [**Pizza Type**](/Dataset/pizza_types.csv)
    - **pizza_type_id**: Unique identifier for the pizza type. 
    - **name**: Name of the pizza. 
    - **category**: Category of the pizza(vegetarian, meat, etc.) 
    - **ingredients**: List of ingredients used in the pizza. 

 - [**Pizzas**](/Dataset/pizzas.csv)
    - **pizza_id**: Unique identifier for the pizza.
    - **pizza_type_id**: Identifier linking to the pizza_type table. 
    - **size**: Size of the pizza (e.g., small, medium, large).  price: Price of the pizza.
# SQL Queries
 
 ### Q1: The total number of order place
    SELECT COUNT(*) AS Total_orders FROM ORDERS;
 
 ### Q2 Find the total revenue generated from pizza sales
    SELECT ROUND(SUM(O.QUANTITY*P.PRICE),1) AS Total_revenue FROM 
    ORDER_DETAILS O JOIN PIZZAS P ON O.PIZZA_ID=P.PIZZA_ID;
 
 ### Q3 Identify the highest priced pizza.
    SELECT PT.NAME, P.PRICE FROM 
    PIZZA_TYPES PT JOIN PIZZAS P ON 
    PT.PIZZA_TYPE_ID=P.PIZZA_TYPE_ID
    WHERE P.PRICE=(SELECT MAX(PRICE) FROM PIZZAS);
 
 ### Q4 Find the most common pizza size ordered.
    SELECT P.SIZE, COUNT(O.QUANTITY) AS 'NO. OF ORDERS' FROM
    PIZZAS P JOIN ORDER_DETAILS O ON
    P.PIZZA_ID = O.PIZZA_ID
    GROUP BY P.SIZE ORDER BY MAX(O.QUANTITY) DESC;

 ### Q5 Identify the top 5 most ordered pizza types along their quantities.
    SELECT PT.NAME,SUM(O.QUANTITY) AS 'TOTAL QUANTITY'
    FROM ORDER_DETAILS O JOIN PIZZAS P ON O.PIZZA_ID=P.PIZZA_ID 
    JOIN PIZZA_TYPES PT ON P.PIZZA_TYPE_ID=PT.PIZZA_TYPE_ID
    GROUP BY PT.NAME ORDER BY 'TOTAL QUANTITY' LIMIT 5;

 ### 6 What is the quantity of each pizza categories ordered?
    SELECT PT.CATEGORY,SUM(O.QUANTITY) AS 'TOTAL QUANTITY'
    FROM ORDER_DETAILS O JOIN PIZZAS P ON O.PIZZA_ID=P.PIZZA_ID 
    JOIN PIZZA_TYPES PT ON P.PIZZA_TYPE_ID=PT.PIZZA_TYPE_ID
    GROUP BY PT.CATEGORY ORDER BY 'TOTAL QUANTITY';

 ### 7 Identify the distribution of orders by hours of the day.
    SELECT HOUR(TIME) AS 'HOUR OF THE DAY',
    COUNT(ORDER_ID) AS 'NO. OF ORDERS' FROM ORDERS 
    GROUP BY HOUR(TIME);

 ### 8 Find the category-wise distribution of pizzas.
    SELECT CATEGORY,COUNT(CATEGORY) AS 'NO. OF PIZZA' FROM PIZZA_TYPES 
    GROUP BY CATEGORY;

 ### Q9 What is the average number of pizzas ordered per day?
    SELECT ROUND(AVG(PERDAY_TOTAL.TOTAL_QUANTITY),0) AS 'AVG ORDERS PER DAY' 
    FROM (SELECT O.DATE, SUM(OD.QUANTITY) AS TOTAL_QUANTITY FROM 
    ORDER_DETAILS OD JOIN ORDERS O ON O.ORDER_ID = OD.ORDER_ID
    GROUP BY O.DATE) AS PERDAY_TOTAL;

 ### Q10 Identify the top 3 most ordered pizza type base on revenue.
    SELECT PT.NAME, SUM(O.QUANTITY*P.PRICE) AS TOTAL_REVENUE FROM ORDER_DETAILS O JOIN 
    PIZZAS P ON O.PIZZA_ID=P.PIZZA_ID JOIN PIZZA_TYPES PT 
    ON P.PIZZA_TYPE_ID = PT.PIZZA_TYPE_ID GROUP BY PT.NAME 
    ORDER BY TOTAL_REVENUE DESC LIMIT 3;

 ### Q11 What is the percentage contribution of each pizza type to revenue?
    SELECT PT.CATEGORY,ROUND((SUM(O.QUANTITY*P.PRICE)/(SELECT ROUND
    (SUM(O.QUANTITY*P.PRICE),2) AS TOTAL_REV FROM ORDER_DETAILS O 
    JOIN PIZZAS P ON P.PIZZA_ID=O.PIZZA_ID))*100,2) AS REV FROM 
    PIZZA_TYPES PT JOIN PIZZAS P ON PT.PIZZA_TYPE_ID = P.PIZZA_TYPE_ID 
    JOIN ORDER_DETAILS O ON O.PIZZA_ID=P.PIZZA_ID
    GROUP BY PT.CATEGORY ORDER BY REV DESC;
    
 ### Q12 Identify the cumulative revenue generated over time.
    SELECT O.DATE,ROUND(SUM(OD.QUANTITY*P.PRICE),2) 
    AS DAILY_REVENUE,ROUND(SUM(SUM(OD.QUANTITY*P.PRICE)) 
    OVER (ORDER BY O.DATE),2) AS CUMULATIVE_REVENUE
    FROM ORDERS O JOIN ORDER_DETAILS OD ON 
    O.ORDER_ID=OD.ORDER_ID JOIN PIZZAS P ON 
    P.PIZZA_ID = OD.PIZZA_ID 
    GROUP BY O.DATE ORDER BY O.DATE;

 ### Q13 Find the top 3 most ordered pizza type based on revenue for each pizza category.
    SELECT CATEGORY,NAME,REVENUE FROM(
    SELECT PT.CATEGORY,PT.NAME,ROUND(SUM(OD.QUANTITY*P.PRICE),2) AS REVENUE, 
    RANK() OVER (PARTITION BY PT.CATEGORY ORDER BY SUM(OD.QUANTITY*P.PRICE)DESC)
    AS ranks FROM ORDER_DETAILS OD JOIN PIZZAS P ON OD.PIZZA_ID=
    P.PIZZA_ID JOIN PIZZA_TYPES PT ON P.PIZZA_TYPE_ID=PT.PIZZA_TYPE_ID 
    GROUP BY PT.CATEGORY,PT.NAME) RANKED_PIZZAS WHERE ranks<=3 
    ORDER BY CATEGORY,ranks;
# Conclusion

Through the sales analysis we explored various aspects of pizza orders, including customer preferences, overall sales trend, product and cumulative renvenue insights.


Based on these insights the pizza store can make informed decisions to enhance customer experience, drive sales and hence achieve sustained growth.
