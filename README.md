# Walmart Sales Analysis
In this project, I used MySQL to analyze Walmart sales data to gain insight and understand the different factors that affect sales of the different branches.
The full code is linked HERE.

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
Here are the new columns created:


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

**Best Performing Product Lines:**

Electronic accessories sold the most products with 961 units sold.

Food and beverages brought in the most revenue of $56,144.8440.

Food and beverages have the highest average rating of 7.11.

**Product Lines that Need Improvement:**
Health and beauty sold the least products with 844 units sold. This product line has relatively lower sales compared to others.

Health and beauty also had the lowest revenue of $48,854.3790. It's worth considering strategies to boost sales in this category.

Home and lifestyle and Sports and travel have slightly lower average ratings (6.84 and 6.86, respectively). There may be opportunities to improve customer satisfaction in these categories.
