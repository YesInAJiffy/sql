## Datacamp Data Associate Exam (PRACTICE AND EXAM SOLUTIONS)

# VIDEO TUTORIAL ON HOW TO APPLY
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
Exercise 1.

``` sql
SELECT 
    COUNT(*) AS missing_year
FROM 
    public.products
WHERE 
    year_added IS NULL;
``` 

Exercise 2.

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


Exercise 3.

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

Exercise 4.

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


