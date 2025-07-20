## Datacamp Data Associate Exam (PRACTICE AND EXAM SOLUTIONS)

### VIDEO TUTORIAL ON DETAILS OF PRACTICE EXAM
[![](https://markdown-videos-api.jorgenkh.no/youtube/lqT-mPDlTZk)](https://youtu.be/lqT-mPDlTZk)

https://www.youtube.com/watch?v=lqT-mPDlTZk

### Practice Practical Exercise 1.

``` sql
-- Starts Below
WITH median_sales AS (
    SELECT 
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY sales::NUMERIC) AS median
    FROM 
        public.pet_supplies
    WHERE 
        sales IS NOT NULL
)
SELECT 
    product_id, 
    CASE 
        WHEN category NOT IN ('Housing', 'Food', 'Toys', 'Equipment', 'Medicine', 'Accessory') OR category IS NULL 
        THEN 'Unknown'
        ELSE category
    END AS category, 
    CASE 
        WHEN animal NOT IN ('Dog', 'Cat', 'Fish', 'Bird') OR animal IS NULL 
        THEN 'Unknown'
        ELSE animal
    END AS animal, 
    CASE 
        WHEN LOWER(size) = 'small' THEN 'Small'
        WHEN LOWER(size) = 'medium' THEN 'Medium'
        WHEN LOWER(size) = 'large' THEN 'Large'
        ELSE 'Unknown'
    END AS size, 
    CASE 
        WHEN price::TEXT ~ '^[0-9]+(\.[0-9]+)?$' THEN ROUND(CAST(price AS NUMERIC), 2)
        ELSE 0
    END AS price, 
    CASE 
        WHEN sales IS NOT NULL THEN ROUND(CAST(sales AS NUMERIC), 2)
        ELSE (SELECT median FROM median_sales)
    END AS sales, 
    COALESCE(rating, 0) AS rating, 
    repeat_purchase 
FROM 
    public.pet_supplies
WHERE 
    repeat_purchase IS NOT NULL;
```

### Practice Practical Exercise 2.

``` sql

-- Starts Below
SELECT 
    animal, 
    repeat_purchase, 
    ROUND(AVG(sales)) AS avg_sales, 
    ROUND(MIN(sales)) AS min_sales, 
    ROUND(MAX(sales)) AS max_sales
FROM 
    public.pet_supplies
WHERE 
    sales IS NOT NULL
GROUP BY 
    animal, 
    repeat_purchase
ORDER BY 
    animal, 
    repeat_purchase;
``` 

### Practice Practical Exercise 3.

``` sql

-- Starts Below
SELECT 
    product_id, 
    ROUND(sales) AS sales, 
    COALESCE(rating, 0) AS rating
FROM 
    public.pet_supplies
WHERE 
    animal IN ('Cat', 'Dog') -- Focus on cats and dogs
    AND repeat_purchase = 1 -- Products bought repeatedly
    AND sales IS NOT NULL -- Ensure sales contain valid numeric values
ORDER BY 
    sales DESC; -- Order by sales in descending order
```

## EXAM EXERCISES
### Exercise 1.

Practical Exam: Grocery Store Sales
FoodYum is a grocery store chain that is based in the United States.

Food Yum sells items such as produce, meat, dairy, baked goods, snacks, and other household food staples.

As food costs rise, FoodYum wants to make sure it keeps stocking products in all categories that cover a range of prices to ensure they have stock for a broad range of customers.

Data
The data is available in the table products.

The dataset contains records of customers for their last full year of the loyalty program.

Column Name	Criteria
product_id	Nominal. The unique identifier of the product. Missing values are not possible due to the database structure.
product_type	Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). Missing values should be replaced with “Unknown”.
brand	Nominal. The brand of the product. One of 7 possible values. Missing values should be replaced with “Unknown”.
weight	Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. Missing values should be replaced with the overall median weight.
price	Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. Missing values should be replaced with the overall median price.
average_units_sold	Discrete. The average number of units sold each month. This can be any positive integer value. Missing values should be replaced with 0.
year_added	Nominal. The year the product was first added to FoodYum stock.Missing values should be replaced with 2022.
stock_location	Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D Missing values should be replaced with “Unknown”.
Task 1
Last year (2022) there was a bug in the product system. For some products that were added in that year, the year_added value was not set in the data. As the year the product was added may have an impact on the price of the product, this is important information to have.

Write a query to determine how many products have the year_added value missing. Your output should be a single column, missing_year, with a single row giving the number of missing values.


``` sql
SELECT 
    COUNT(*) AS missing_year
FROM 
    public.products
WHERE 
    year_added IS NULL;
``` 

### Exercise 2.
Task 2
Given what you know about the year added data, you need to make sure all of the data is clean before you start your analysis. The table below shows what the data should look like.

Write a query to ensure the product data matches the description provided. Do not update the original table.

Column Name	Criteria
product_id	Nominal. The unique identifier of the product. Missing values are not possible due to the database structure.
product_type	Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). Missing values should be replaced with “Unknown”.
brand	Nominal. The brand of the product. One of 7 possible values. Missing values should be replaced with “Unknown”.
weight	Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. Missing values should be replaced with the overall median weight.
price	Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. Missing values should be replaced with the overall median price.
average_units_sold	Discrete. The average number of units sold each month. This can be any positive integer value. Missing values should be replaced with 0.
year_added	Nominal. The year the product was first added to FoodYum stock.Missing values should be replaced with last year (2022).
stock_location	Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D Missing values should be replaced with “Unknown”.

``` sql
-- Select query for task 2
WITH median_values AS (
    SELECT 
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY CAST(REGEXP_REPLACE(weight, '[^0-9.]', '', 'g') AS NUMERIC)) AS median_weight,
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY price) AS median_price
    FROM 
        public.products
    WHERE 
        CAST(REGEXP_REPLACE(weight, '[^0-9.]', '', 'g') AS NUMERIC) > 0 AND price > 0 -- Ensure valid positive values
)
SELECT 
    product_id, 
    COALESCE(product_type, 'Unknown') AS product_type, 
    COALESCE(
        CASE 
            WHEN brand = '-' THEN 'Unknown' 
		    WHEN brand = '_' THEN 'Unknown' 
            ELSE brand 
        END, 
        'Unknown'
    ) AS brand, 
    ROUND(COALESCE(CAST(REGEXP_REPLACE(weight, '[^0-9.]', '', 'g') AS NUMERIC), (SELECT median_weight FROM median_values))::NUMERIC, 2) AS weight, 
    ROUND(COALESCE(price, (SELECT median_price FROM median_values))::NUMERIC, 2) AS price, 
    COALESCE(average_units_sold, 0) AS average_units_sold, 
    COALESCE(year_added, 2022) AS year_added, 
    COALESCE(UPPER(stock_location), 'UNKNOWN') AS stock_location
FROM 
    public.products 

```


### Exercise 3.

To find out how the range varies for each product type, your manager has asked you to determine the minimum and maximum values for each product type.

Write a query to return the product_type, min_price and max_price columns.

``` sql
-- Write your query for task 3 in this cell
SELECT 
    product_type, 
    MIN(price) AS min_price, 
    MAX(price) AS max_price
FROM 
    public.products
WHERE 
    price IS NOT NULL -- Ensure valid price values
GROUP BY 
    product_type

```

### Exercise 4.
The team want to look in more detail at meat and dairy products where the average units sold was greater than ten.

Write a query to return the product_id, price and average_units_sold of the rows of interest to the team.

``` sql
-- Write your query for task 4 in this cell
SELECT 
    product_id, 
    price, 
    average_units_sold
FROM 
    public.products
WHERE 
    product_type IN ('Meat', 'Dairy') -- Focus on meat and dairy products
    AND average_units_sold > 10 -- Filter rows where average units sold is greater than 10
```


