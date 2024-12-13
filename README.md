# Learn SQL Queries in 10 commands

## Setup
**We are going to use sqlite3 for the ease and convenience**
### Installation of sqlite3
Follow the instructions
[SQLITE INSTALLATION PAGES](https://www.sqlite.org/download.html)
### Base Commands
Go to command prompt
1. sqlite3 "DB Name"
2. .headers on
3. .mode box
4. .databases
5. .tables

### Data Preparation
#### Customers
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    country TEXT,
    age INTEGER
);

INSERT INTO customers (customer_id, first_name, last_name, country, age)
VALUES
    (1, 'John', 'Doe', 'USA', 25),
    (2, 'Jane', 'Smith', 'Canada', 30),
    (3, 'Bob', 'Johnson', 'USA', 35),
    (4, 'Alice', 'Williams', 'Canada', 20),
    (5, 'Mike', 'Davis', 'USA', 40);
#### Orders
CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

INSERT INTO orders (order_id, customer_id, order_date)
VALUES
    (1, 1, '2020-01-01'),
    (2, 1, '2020-01-15'),
    (3, 2, '2020-02-01'),
    (4, 3, '2020-03-01'),
    (5, 4, '2020-04-01'),
    (6, 5, '2020-05-01');
#### Products
CREATE TABLE products (
    product_id INTEGER PRIMARY KEY,
    product_name TEXT,
    price REAL
);

INSERT INTO products (product_id, product_name, price)
VALUES
    (1, 'Product A', 10.99),
    (2, 'Product B', 9.99),
    (3, 'Product C', 12.99),
    (4, 'Product D', 11.99),
    (5, 'Product E', 8.99);
#### Order Items
CREATE TABLE order_items (
    order_item_id INTEGER PRIMARY KEY,
    order_id INTEGER,
    product_id INTEGER,
    quantity INTEGER,
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

INSERT INTO order_items (order_item_id, order_id, product_id, quantity)
VALUES
    (1, 1, 1, 2),
    (2, 1, 2, 1),
    (3, 2, 3, 3),
    (4, 3, 1, 1),
    (5, 4, 2, 2),
    (6, 5, 4, 1);
#### Table Description
PRAGMA table_info(customers);

####
          +---------------+
          |  Customers    |
          +---------------+
                  |
                  |  Customer ID
                  v
          +---------------+
          |  Orders       |
          +---------------+
                  |
                  |  Order ID
                  v
          +---------------+
          |  Order Items  |
          +---------------+
                  |
                  |  Order Item ID
                  v
          +---------------+
          |  Products     |
          +---------------+


          
### SQL Queries
1. SELECT: Retrieves data from a database table. Example: SELECT * FROM customers;
2. WHERE: Filters data based on conditions. Example: SELECT * FROM customers WHERE country='USA';
3. AND: Combines multiple conditions in a WHERE clause. Example: SELECT * FROM customers WHERE country='USA' AND age>25;
OR: Combines multiple conditions in a WHERE clause. Example: SELECT * FROM customers WHERE country='USA' OR country='Canada';
4. ORDER BY: Sorts data in ascending or descending order. Example: SELECT * FROM customers ORDER BY last_name ASC;
5. LIMIT: Limits the number of rows returned. Example: SELECT * FROM customers LIMIT 2;
OFFSET: Skips a specified number of rows and returns the next rows. Example: SELECT * FROM customers OFFSET 2 LIMIT 2;
6. GROUP BY: Groups data by one or more columns. Example: SELECT country, AVG(age) FROM customers GROUP BY country;
7. HAVING: Filters grouped data based on conditions. Example: SELECT country, AVG(age) FROM customers GROUP BY country HAVING AVG(age)>30;
8. JOIN: Combines data from two or more tables. Example: SELECT * FROM customers JOIN orders ON customers.customer_id=orders.customer_id;
List all products from John
SELECT p.product_name
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
WHERE c.first_name = 'John';
10. UNION: Combines the result sets of two or more SELECT queries, removing duplicates. Example: select * from customers where first_name ='John' union select * from customers where last_name like 'D%';
11. SUBQUERY: A query nested inside another query, allowing for more complex filtering and data manipulation.
SELECT *
FROM customers
WHERE customer_id IN (
    SELECT order_id
    FROM orders
    WHERE order_date > '2020-01-01'
);

### CleanUp
DROP TABLE customers;
DROP TABLE orders;
DROP TABLE products;
DROP TABLE order_items;


![Screenshot of a comment on a GitHub issue showing an image, added in the Markdown, of an Octocat smiling and raising a tentacle.](https://myoctocat.com/assets/images/base-octocat.svg)
