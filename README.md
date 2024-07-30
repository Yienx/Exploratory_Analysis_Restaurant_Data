## Exploratory_Analysis_Restaurant_Data

**$\color{blue}{\text{TABLE OF CONTENTS}}$**

- [Introduction](#INTRODUCTION)
- [Exploring Menu Items Data](#Exploring the Menu Items Data)
- [Exploring Order Details Data](#Exploring the Order Details Data)
- [Insights from Analysis](#Insights From Analysis)
- [Recommendations](#Recommendations)

## INTRODUCTION
This is an exploratory Data Analysis in MySQL on Restaurant Order Data. This dataset comprise of two data;
1. Menu Items Data
2. Order Details Data
#### Data Source: 
This dataset was got from [Maven Analytics Data Playground](https://mavenanalytics.io/data-playground?order=date_added%2Cdesc&search=restaurant)
#### Purpose: 
The purpose of this analysis is to explore the restaurant data in `MYSQL` and to discover consumer behaviour towards ordering in a restaurant. 

#### Exploring the Menu Items Data

```
USE restaurant_db;

SELECT * FROM menu_items;
```
![menu_1](https://github.com/user-attachments/assets/08373a5b-ff43-4fc3-a238-fdc06281c270)


The `menu_items data` has four fields consisting of: 
* menu_item_id - Primary Key
* item_name: containing the name of each items on the menu
* Category: category of the item (American dish, Italian dish, etc)
* Price: price of the item. 

**1. Checking the number of menu in the menu_items data.**

```
SELECT Count(*) from menu_items;  
```
![count_menu](https://github.com/user-attachments/assets/2f692da0-8562-4877-b50c-5799508042fc)

- There are 32 different menu in this table

**2. What is the least expensive menu on the list?**
```
SELECT * FROM MENU_ITEMS
ORDER BY PRICE;  -- Edamame is the least expensive menu of $5
```
![least_menu](https://github.com/user-attachments/assets/a33a1517-9751-4a61-9ee0-51f51a84f85d)

- The Asian food, `Edamame` is the least expensive food on the menu. 

**3. What is the most expensive menu on the list?**

```
SELECT * FROM MENU_ITEMS
ORDER BY PRICE DESC;  -- Shrimp Scampi is the most expensive menu of $19.95
```
![most_menu](https://github.com/user-attachments/assets/2b55d3d0-742a-45eb-8d93-20ee9ac61d1e)

- The Italian food, `Shrimp Scampi is the most expensive food on the menu, which costs &19.95 per plate. 

**4.  How many dishes are in each category and what is their average price?**

```
SELECT category, COUNT(*) AS Count, AVG(price)
from menu_items
group by category;
```
![category_count](https://github.com/user-attachments/assets/7342ddac-4ebc-46a2-98d0-6b40c2189379)

- Looking at the above display, it is observed that the  `Italian Food Category` is the most expensive with an Average price of $16.75, while American food menu is the least expensive with an Average price of $10.06.

#### Exploring the Order Details Data

-- View the order_details table

SELECT * FROM order_details;

![order_1](https://github.com/user-attachments/assets/58531feb-a862-4d0f-a62d-4477617c61d4)

The order details data shows the details of ordering and what customers are ordering in the restaurant. It consists of:
1. order_details_id: primary key
2. order_id: Order ID of each menu
3. order_date: Date a particular menu item was ordered
4. order_time: The particular time of order
5. item_id: Foreign key, menu item ID from MENU ITEMS DATA.

**1. What is the order dateperiod / range**
```
SELECT MIN(order_date), MAX(order_date)
FROM order_details;
```

![min_max_date](https://github.com/user-attachments/assets/d47546dc-41f3-4a9f-8138-0f3e4b7accdf)

- This data dates from `2023-01-01` to `2023-03-31` (1st Quarter of 2023). 
- All orders during this period is what is used for this analysis.

**2. How many unique items were ordered within this period?**
```
SELECT	COUNT(distinct order_id)  AS unique_orders
FROM order_details;
```
![unique_orders](https://github.com/user-attachments/assets/527fb0f5-bc18-4ce8-825f-683a25bae1cd)

- 5,370 unique items were ordered during the 1st Quarter of 2023.

**3. What is the total item made within the 1st Quarter?**
```
SELECT	COUNT(item_id) FROM order_details;
```
![total_orders](https://github.com/user-attachments/assets/e279d2fc-6a57-4fd8-8da1-9fec52559e79)

- 12,097 total items were ordered.

**4. Which order has the most number of items?**
```
SELECT order_id, count(item_id) AS num_items
FROM order_details 
GROUP BY order_id
ORDER BY num_items DESC;
```
![most_items](https://github.com/user-attachments/assets/cd74d67b-796e-4497-9b96-2fb862176b76)

* It can be observed that order_ids `4305`, `3473`, `1957`, `330`, `440`, `443`, and `2675`  ordered the most number of items (14) within this given period. We can explore what was ordered a little further below:

##### COMBINNING BOTH TABLES (MENU_ITEMS & ORDER_DETAILS)
SELECT * FROM menu_items;
SELECT * FROM order_details;
-- THEIR UNIQUE ID IS THE ITEM_ID.

```
SELECT * FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id;
```
![joined_table](https://github.com/user-attachments/assets/15966eff-a638-49a0-aef6-19a4a517e088)

###### Now, lets look at the orders from only client (4305)
```
SELECT * FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id 
WHERE od.order_id = '4305'
```
![4305_orders](https://github.com/user-attachments/assets/6826d1ef-4a3e-4d2c-b833-50640c204806)

* Client 4305 ordered a variety of dishes, American, Itanlian, Asian and Mexican.

**5. How many clients have ordered more than 12 items?**
* This will require using a `sub-query` as seen below.
``` 
SELECT COUNT(*) FROM
(SELECT order_id, count(item_id) as num_items
FROM order_details
GROUP BY order_id
HAVING num_items > 12) AS num_orders;
```
![more_12](https://github.com/user-attachments/assets/0a3435a0-2ed6-41aa-8d58-a372317d4672)

- 20 different clients have ordered more than 12 times in this restaurant.

**6. What is the Least Ordered Item?**
```
SELECT item_name, count(order_details_id) AS least_ordered FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id
GROUP BY item_name
ORDER BY least_ordered;
```
![least_ordered](https://github.com/user-attachments/assets/400e8988-0c52-4291-b5dc-21149fc0cbab)

- Chicken Tacos is the least ordered item which has been ordered 123 times.

**7. What is the Most Ordered Item?**
```
SELECT item_name, count(order_details_id) AS most_ordered
FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id
GROUP BY item_name
ORDER BY most_ordered DESC;
```
![most_ordered](https://github.com/user-attachments/assets/9f257400-d260-42b0-94ed-a293cfb36e74)

- Hamburger is the most ordered item which has been ordered 622 times in 3 months.

**8. What are the Top 5 Orders that spent the most money?**
```
SELECT order_id, sum(price) as total_spent
FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id
Group by order_id
Order by total_spent desc
LIMIT 5;
```
![top_5_client](https://github.com/user-attachments/assets/77a424d1-5db7-4122-bc0e-5efdbf97e1ac)

- Client 440 spent the most amount of money with a total of $192.15
- This clients loves to eat the following food items:

**9. View the details of the highest spend order.**
```
SELECT order_id, item_name, category
FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id  -- Italian dishes are the most prefered for this client. 
Where order_id = 440
ORDER BY category;

```
![440_order](https://github.com/user-attachments/assets/8a3b93d8-d542-448e-8d98-cb2f7f5396c2)

- Though this clients orders from different food category, the Italian dishes are still the most prefered.

**10. View the details of the Top 5 highest spend orders. What insights can you gather?**
```
SELECT category, Count(item_id) 
FROM order_details od
LEFT JOIN menu_items mi
ON od.item_id = mi.menu_item_id  
Where order_id IN ('440', '2075', '1957', '330', '2675') -- Italian dishes are still the most prefered for the top 5 clients. 
GROUP BY category;
```
![top_5_category](https://github.com/user-attachments/assets/4125d391-d4f4-44ae-bc99-b05752c505f8)

### Insights From Analysis
1. Italian dishes are the most expensive dishes on the menu with an average price of $16.75. It is also the most ordered by the Top paying client.
2. Italian dishes were also ordered 26 times by the Top 5 high paying clients.
3. American Dish (Hamburgers) and Asian dish (Edamame) are the most ordered dishes for this period.
4. American dishes are the least expensive with an average price of $10.06

### Recommendations
1. Shrimp Scampi, which is the most expensive dish is not well ordered by client. Stakeholders can look into this menu to revise it. They can further do a customer response analyses on this menu to know this Italian Dish is not moving like the others.
2. The least ordered item is Chicken Tacos. Stakeholders can consider pairing this item with other items like fries, burgers, etc as a combo menu to attract more customers to purchase. 
3. The total number of orders in the first quarter was 12,097 which is a good number. Stakeholders can use this opportunity to drive more traffic into their restaurant by introducing special orders once a week. For instance, buy 1 get one free, discounted prices, etc. 
