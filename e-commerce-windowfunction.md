**e-commerce example tables** with sample data to help beginners understand different **Window Functions** in SQL. The tables include `orders`, `customers`, and `products`, which will be useful for applying **ROW_NUMBER(), RANK(), DENSE_RANK(), NTILE(), LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE(), and SUM() OVER()** functions.

---
https://www.tutorialspoint.com/execute_sql_online.php

### **Step 1: Create Tables**
```sql
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100),
    country VARCHAR(50)
);

CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id),
    product_id INT REFERENCES products(product_id),
    order_date DATE,
    quantity INT
);
```

---

### **Step 2: Insert Sample Data**
```sql
INSERT INTO customers (customer_name, country) VALUES
('Alice', 'USA'),
('Bob', 'Canada'),
('Charlie', 'UK'),
('David', 'India'),
('Eve', 'USA');

INSERT INTO products (product_name, category, price) VALUES
('Laptop', 'Electronics', 1200.00),
('Smartphone', 'Electronics', 800.00),
('Tablet', 'Electronics', 500.00),
('Headphones', 'Accessories', 150.00),
('Smartwatch', 'Accessories', 200.00);

INSERT INTO orders (customer_id, product_id, order_date, quantity) VALUES
(1, 1, '2024-02-01', 1),
(2, 2, '2024-02-02', 2),
(3, 3, '2024-02-03', 1),
(4, 4, '2024-02-04', 3),
(5, 5, '2024-02-05', 1),
(1, 2, '2024-02-06', 2),
(2, 3, '2024-02-07', 1),
(3, 4, '2024-02-08', 1),
(4, 5, '2024-02-09', 2),
(5, 1, '2024-02-10', 1);
```

---

### **Step 3: Apply Window Functions**

#### **1. ROW_NUMBER() – Assigns a unique row number per partition**
```sql
SELECT order_id, customer_id, order_date,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS row_num
FROM orders;
```

#### **2. RANK() – Assigns ranks with gaps**
```sql
SELECT order_id, customer_id, order_date,
       RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS rank_num
FROM orders;
```

#### **3. DENSE_RANK() – Assigns ranks without gaps**
```sql
SELECT order_id, customer_id, order_date,
       DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS dense_rank_num
FROM orders;
```

#### **4. NTILE(4) – Divides data into 4 equal groups**
```sql
SELECT order_id, customer_id, order_date,
       NTILE(4) OVER (ORDER BY order_date) AS quartile
FROM orders;
```

#### **5. LAG() – Get previous order quantity**
```sql
SELECT order_id, customer_id, order_date, quantity,
       LAG(quantity, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS prev_quantity
FROM orders;
```

#### **6. LEAD() – Get next order quantity**
```sql
SELECT order_id, customer_id, order_date, quantity,
       LEAD(quantity, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS next_quantity
FROM orders;
```

#### **7. FIRST_VALUE() – Get the first product ordered by each customer**
```sql
SELECT order_id, customer_id, order_date, product_id,
       FIRST_VALUE(product_id) OVER (PARTITION BY customer_id ORDER BY order_date) AS first_product
FROM orders;
```

#### **8. LAST_VALUE() – Get the last product ordered by each customer**
```sql
SELECT order_id, customer_id, order_date, product_id,
       LAST_VALUE(product_id) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_product
FROM orders;
```

#### **9. SUM() OVER() – Running total of quantity ordered**
```sql
SELECT order_id, customer_id, order_date, quantity,
       SUM(quantity) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM orders;
```

---

These scripts provide a structured way for beginners to understand how **Window Functions** work in an **e-commerce** dataset. Let me know if you need modifications or explanations! 🚀
