# retail-sales-sql-project

--customers Table--
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100),
    city VARCHAR(50),
    email VARCHAR(50),
    join_date DATE 
);
INSERT INTO customers (customer_id, customer_name, city, email, join_date) VALUES
(1,'Rahul','Delhi','rahul@gmail.com','2023-01-10'),
(2,'Priya','Mumbai','priya@gmail.com','2023-01-12'),
(3,'Amit','Bangalore','amit@gmail.com','2023-01-15'),
(4,'Sneha','Chennai','sneha@gmail.com','2023-01-18'),
(5,'Rohit','Kolkata','rohit@gmail.com','2023-01-20'),
(6,'Neha','Delhi','neha@gmail.com','2023-02-01'),
(7,'Karan','Mumbai','karan@gmail.com','2023-02-03'),
(8,'Pooja','Bangalore','pooja@gmail.com','2023-02-05'),
(9,'Arjun','Hyderabad','arjun@gmail.com','2023-02-07'),
(10,'Meera','Pune','meera@gmail.com','2023-02-10');



--products Table--
CREATE TABLE products (
product_id INT PRIMARY KEY,
product_name VARCHAR(50),
category VARCHAR(30),
price INT,
cost INT
);
INSERT INTO products (product_id, product_name, category, price, cost) VALUES
(101,'Mobile','Electronics',15000,12000),
(102,'Laptop','Electronics',55000,45000),
(103,'Tablet','Electronics',25000,20000),
(104,'Headphones','Accessories',3000,2000),
(105,'Smart Watch','Accessories',8000,6000),
(106,'Keyboard','Accessories',1200,800),
(107,'Mouse','Accessories',700,400),
(108,'TV','Electronics',40000,35000),
(109,'Printer','Electronics',9000,7000),
(110,'Camera','Electronics',45000,38000);



--orders Table--
CREATE TABLE orders (
order_id INT PRIMARY KEY,
customer_id INT,
order_date DATE,
FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
INSERT INTO orders (order_id, customer_id, order_date) VALUES
(1001,1,'2023-03-01'),
(1002,2,'2023-03-02'),
(1003,3,'2023-03-03'),
(1004,4,'2023-03-04'),
(1005,5,'2023-03-05'),
(1006,6,'2023-03-06'),
(1007,7,'2023-03-07'),
(1008,8,'2023-03-08'),
(1009,9,'2023-03-09'),
(1010,10,'2023-03-10');



--order_details Table--
CREATE TABLE order_details (
order_detail_id INT PRIMARY KEY,
order_id INT,
product_id INT,
quantity INT,
FOREIGN KEY (order_id) REFERENCES orders(order_id),
FOREIGN KEY (product_id) REFERENCES products(product_id)
);
INSERT INTO order_details (order_detail_id, order_id, product_id, quantity) VALUES
(1,1001,101,2),
(2,1001,104,1),
(3,1002,102,1),
(4,1002,107,2),
(5,1003,103,1),
(6,1003,106,1),
(7,1004,108,1),
(8,1005,109,2),
(9,1006,105,1),
(10,1007,110,1);

-- 1. Display all customers from Delhi--
SELECT * FROM customers WHERE city = 'Delhi';

-- 2. List all electronic products--
SELECT product_name, price FROM products WHERE category = 'Electronics';

-- 3. Customer name with their order dates--
SELECT c.customer_name,o.order_date 
FROM customers c
INNER JOIN orders o
ON c.customer_id = o.customer_id
ORDER BY c.customer_name, o.order_date;

-- 4. Order‑wise product details--
SELECT o.order_id,p.product_name,od.quantity
FROM orders o
INNER JOIN order_details od
ON o.order_id = od.order_id
INNER JOIN products p
ON od.product_id = p.product_id;

-- 5. Total sales per product--
SELECT p.product_name,SUM(od.quantity * p.price) AS total_sales
FROM products p
JOIN order_details od ON p.product_id = od.product_id
GROUP BY p.product_name;

-- 6. Total revenue per customer--
SELECT c.customer_name,SUM(od.quantity * p.price) AS revenue
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_details od ON o.order_id = od.order_id
JOIN products p ON od.product_id = p.product_id
GROUP BY c.customer_name;

-- 7. Customers with revenue greater than 30,000--
SELECT c.customer_name,
SUM(od.quantity * p.price) AS revenue
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_details od ON o.order_id = od.order_id
JOIN products p ON od.product_id = p.product_id
GROUP BY c.customer_name
HAVING SUM(od.quantity * p.price) > 30000;

-- 8. Customers who placed at least one order--
SELECT customer_name
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);

-- 9. Product with highest price--
SELECT * FROM products
WHERE price = (SELECT MAX(price) FROM products);

-- 10. Monthly sales report--
SELECT TO_CHAR(o.order_date, 'YYYY-MM') AS month,
SUM(od.quantity * p.price) AS monthly_sales
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN products p ON od.product_id = p.product_id
GROUP BY TO_CHAR(o.order_date,'YYYY-MM');

-- 11. Profit per product--
SELECT product_name,
SUM(quantity * (price - cost)) AS profit
FROM products p
JOIN order_details od ON p.product_id = od.product_id
GROUP BY product_name;


