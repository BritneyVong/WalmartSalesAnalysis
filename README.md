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
