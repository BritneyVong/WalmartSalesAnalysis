# Walmart Sales Analysis
In this project, I used MySQL to analyze Walmart sales data to gain insight and understand the different factors that affect sales of the different branches.
The full code to this project is linked [HERE](https://github.com/BritneyVong/WalmartSalesAnalysis/blob/main/Project%20Full%20Code).

## About The Data
I found this dataset from Kaggle. It contains sales transactions from three different branches of Walmart, respectively located in Mandalay, Yangon and Naypyitaw.

## Notes
For more detailed information about the data, I modified the table to add some new columns:

> time_of_day will give insight into sales in the Morning, Afternoon, and Evening. This will help answer the question of which part of the day most sales are made. I also ran into some issues creating this column but I was able to fix it using delimiters.

```
/* MySQL appears to be running the alter table query at the same time as the udpate query so I keep getting an error loading in the data
into the time_of_day column because the alter table query runs again and there is a dupliacte column. To fix this, I added a DELIMITER
statement to run both queries at the same time. First I set the DELIMETER to '//' so that '//' will end the statement
*/

DELIMITER //
-- Add the time_of_day column
ALTER TABLE sales ADD COLUMN time_of_day VARCHAR(20);

-- Inserting data into table
UPDATE sales SET time_of_day = (
	CASE
		WHEN time BETWEEN "00:00:00" AND "12:00:00" THEN "Morning"
        WHEN time BETWEEN "12:01:00" AND "16:00:00" THEN "Afternoon"
        ELSE "Evening"
    END
);

-- Next, I reset the DELIMITER back to ;
DELIMITER ;
```

> day_name contains the extracted days of the week on which the given transaction took place (Mon, Tue, Wed, Thur, Fri). This will help answer the question of which day of the week each branch is busiest.

```
-- day_name column data
SELECT
	date,
    DAYNAME(date) AS day_name
FROM sales;

-- creating day_name column
ALTER TABLE sales ADD COLUMN day_name VARCHAR(10);

-- inserting the data into the day_name column
UPDATE sales SET day_name = DAYNAME(date);
```

> month_name that contains the extracted months of the year on which the given transaction took place (Jan, Feb, Mar). Help determine which month of the year has the most profit.

```
-- month_name column data
SELECT 
	date,
    MONTHNAME(date) AS month_name
FROM sales;

-- creating month_name column
ALTER TABLE sales ADD COLUMN month_name VARCHAR(10);

-- inserting data into month_name column
UPDATE sales SET month_name = MONTHNAME(date);
```
Here are the new columns created with the data inserted:


<img width="220" alt="Screenshot 2023-12-07 at 5 10 08 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/670bea9f-6a64-490e-9937-e7d8eca12290">

## General Questions
First, I wanted to gain some basic information about the data to know what I was working with. I used the SQL statement below to answer the following questions:

1. How many different cities does the data have?
```
-- How many unique cities does the data have?
SELECT
	DISTINCT city
FROM sales;
```
<img width="110" alt="Screenshot 2023-12-07 at 5 42 34 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/1104210a-8998-40f1-8965-b7401a48b134">


2. In what city is each branch?
```
-- In what city is each branch?
SELECT
	DISTINCT branch,
    city
FROM sales;
```
<img width="147" alt="Screenshot 2023-12-07 at 5 44 06 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/1b6de2d8-44ae-4bd5-bde8-41af6edb6975">
   

3. How many different customer types does the data have?
```
-- Customer types
SELECT
	DISTINCT customer_type
FROM sales;
```
<img width="147" alt="Screenshot 2023-12-07 at 5 46 16 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/7737cd12-4c17-47ae-be36-11a6b7e1d853">

The sales data containes three cities—Mandalay (Branch B), Yangon (Branch A), and Naypyitaw (Branch C). Additionally, two distinct customer types, Normal and Member.
   

## Product Analysis
Conduct analysis of the data to understand the different product lines, the product lines performing best, and the product lines that need to be improved.

1. How many unique product lines does the data have?
```
-- How many unique product lines does the data have?
SELECT
	COUNT(DISTINCT product_line) AS num_products
FROM sales;
```
<img width="147" alt="Screenshot 2023-12-07 at 5 48 27 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/0f646f1e-4d57-42b1-8e6e-4f611c4b396c">
   

2. What is the most selling product line?
```
-- What is the most selling product line?
SELECT
	product_line,
    SUM(quantity) AS qty
FROM sales
GROUP BY product_line
ORDER BY qty DESC;
```
<img width="198" alt="Screenshot 2023-12-07 at 5 49 28 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/922203b4-b4a0-4263-beea-925c2d3588fd">

The results display the six product lines as well as the product line with the most sales on top. 
   

3. What product line had the largest revenue?
```
SELECT
	product_line,
    SUM(total) AS total_revenue
FROM sales
GROUP BY product_line
ORDER BY total_revenue DESC;
```
<img width="225" alt="Screenshot 2023-12-07 at 6 09 19 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/c783ccbb-5c5d-40a2-8d64-482d102a1529">

This query displays the product lines with the largest revenue at the top. I was surprised to find that electronic accessories was one of the least contributors to revenue as it was the product line that sold the most.
   

4. What is the average rating of each product line?
```
SELECT
	product_line,
    ROUND(AVG(rating), 2) AS avg_rating
FROM sales
GROUP BY product_line
ORDER BY avg_rating DESC;
```
<img width="225" alt="Screenshot 2023-12-07 at 6 27 49 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/e8b879f5-49c1-4420-8988-fe0ce57c4ed0">

### **Best Performing Product Lines:**

Electronic accessories sold the most products with 961 units sold.

Food and beverages brought in the most revenue of $56,144.8440.

Food and beverages have the highest average rating of 7.11.

### **Product Lines that Need Improvement:**   
Health and beauty sold the least products with 844 units sold. This product line has relatively lower sales compared to others.

Health and beauty also had the lowest revenue of $48,854.3790. It's worth considering strategies to boost sales in this category.

Home and lifestyle and Sports and travel have slightly lower average ratings (6.84 and 6.86, respectively). There may be opportunities to improve customer satisfaction in these categories.

## Sales Analysis
This analysis aims to answer the question of the sales trends among the branches. The result of this can help us determine what modifications should be considered to gain more sales.

1. Number of sales made at each time of the day per weekday
```
-- Number of sales made at each time of the day on a specific day
-- First, I made a temporary table RankedSales that displayed the total sales for each combination of day_name and time_of_day
WITH RankedSales AS (
  SELECT
    day_name,
    time_of_day,
    COUNT(*) AS total_sales,
    ROW_NUMBER() OVER (PARTITION BY day_name ORDER BY COUNT(*) DESC) AS RowRank
  FROM
    sales
  GROUP BY
    day_name, time_of_day
)
/* Then I selected the columns from the temporary table RankedSales ordered first by day_name and then
	by total_sales in descending order. This arrangement shows the time of day with the highest sales
    at the top for each day */
SELECT
  day_name,
  time_of_day,
  total_sales
FROM
  RankedSales
ORDER BY
  day_name, total_sales DESC;
```
<img width="225" alt="Screenshot 2023-12-07 at 6 50 00 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/a971118a-9bfb-4993-93ee-5c8a6ae95744">

The data shows that evenings experience most sales, and the stores are filled during the evening hours. Mornings are the least busy.

2. Which branch has the most sales
```
-- Which branch has the most sales
SELECT
  branch,
  COUNT(*) AS total_sales
FROM sales
GROUP BY branch
ORDER BY total_sales DESC;
```
<img width="136" alt="Screenshot 2023-12-07 at 7 38 17 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/92590a14-5007-4dc7-be0e-7971a0933b6d">

Branch A is leading in total sales with 339, followed closely by B with 329 and C with 327. We can look deeper into this by finding the busiest day for each branch.

3. Which day of the week is each branch the busiest?
```
-- Which day of the week is each branch the busiest?
/*
	I used a temporary table and ROW_NUMBER() window function to assign ranks to each day based on total sales within each branch.
	The final SELECT statement filters the results to only include the days with rank 1, the busiest day for each branch.
*/
WITH RankedDays AS (
  SELECT
    branch,
    day_name,
    COUNT(*) AS total_sales,
    ROW_NUMBER() OVER (PARTITION BY branch ORDER BY COUNT(*) DESC) AS DayRank
  FROM
    sales
  GROUP BY
    branch, day_name
)
SELECT
  branch,
  day_name,
  total_sales
FROM
  RankedDays
WHERE
  DayRank = 1;
```
<img width="207" alt="Screenshot 2023-12-07 at 7 40 38 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/ac23924a-e127-4906-97c8-efac380e4fe8">

The analysis reveals the busiest days for each branch:
Branch A: Sunday with 52 sales
Branch B: Saturday with 60 sales
Branch C: Tuesday with 54 sales
Understanding the branch-specific busiest days helps in tailoring strategies for promotions, staffing, and events to maximize sales on those days.


4. Which month of the year has the most profit?
```
-- Which month of the year has the most profit
SELECT
	month_name,
    SUM(total) AS total_profit
FROM sales
GROUP BY month_name
ORDER BY total_profit DESC;
```
<img width="207" alt="Screenshot 2023-12-07 at 7 44 13 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/df2b3222-55f2-4ccc-928d-33c84455a3ce">

This query shows the results of each month's profit with the one with the most profitable month at the top. January had the most profit out of the two months, followed by March and February.

## Customer Analysis
This analysis aims to uncover the different customer segments and purchase trends.

1. What is the most common customer type?
```
-- What is the most common customer type?
SELECT
	customer_type,
    COUNT(*) AS amt
FROM sales
GROUP BY customer_type
ORDER BY amt DESC;
```
<img width="207" alt="Screenshot 2023-12-07 at 7 46 56 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/69dbabb9-9c19-4bab-8750-3efe356a4158">

2. Which of the customer types brings the most revenue?
```
-- Which of the customer types brings the most revenue?
SELECT
	customer_type,
    SUM(total) AS total_revenue
FROM sales
GROUP BY customer_type
order by total_revenue DESC;
```
<img width="207" alt="Screenshot 2023-12-07 at 7 48 12 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/f3b0d9e8-12fc-4fea-be65-09a368c6089e">

The revenue breakdown by customer type indicates that Member customers are a significant contributor to overall revenue. We could consider analyzing the behavior and preferences of Member customers to enhance loyalty programs and potentially attract more members. 

3. What is the most common payment method?
```
SELECT
	payment,
    COUNT(payment) AS amount
FROM sales
GROUP BY payment
ORDER BY amount DESC;
```
<img width="207" alt="Screenshot 2023-12-07 at 7 49 40 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/d241e502-e9fa-42e5-88d0-a8f4700b45c4">

4. Most common product line by gender
```
-- What is the gender of most of the customers?
SELECT
	gender,
    COUNT(*) amt
FROM sales
GROUP BY gender
ORDER BY amt DESC;
```
<img width="116" alt="Screenshot 2023-12-07 at 7 50 49 PM" src="https://github.com/BritneyVong/WalmartSalesAnalysis/assets/130412196/4a6b1e98-2064-4ab2-acd9-1d4857683a23">

## Results and Final Notes

Using SQL, I was able to find interesting insights into Walmart sales. Branch A has the most sales and Branch B has the busiest day of the week. Top performing product lines were electronic accessories and food and beverages. Although electronic accessories are sold the most, it’s interesting to see that they as not performing as well in terms of rating and revenue when compared to other product lines. However, food and beverages is consistent in performing well with ratings, sales, and revenue. Members are the most common type of customers and bring in the most revenue. While Normal customers contribute substantial revenue, there may be opportunities to increase their engagement or incentivize higher spending.
