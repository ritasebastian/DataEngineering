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

Here are some **realistic examples** with sample outputs to help beginners understand how **window functions** work in an e-commerce scenario.

---

### **1. ROW_NUMBER() – Assigns a unique row number per customer**
```sql
SELECT order_id, customer_id, order_date,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS row_num
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | row_num |
|----------|------------|------------|---------|
| 1        | 1          | 2024-02-01 | 1       |
| 6        | 1          | 2024-02-06 | 2       |
| 2        | 2          | 2024-02-02 | 1       |
| 7        | 2          | 2024-02-07 | 2       |

🔹 **Use Case:** Identify the first order placed by each customer.

---

### **2. RANK() – Assigns ranks with gaps**
```sql
SELECT order_id, customer_id, order_date,
       RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS rank_num
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | rank_num |
|----------|------------|------------|----------|
| 1        | 1          | 2024-02-01 | 1        |
| 6        | 1          | 2024-02-06 | 2        |
| 2        | 2          | 2024-02-02 | 1        |
| 7        | 2          | 2024-02-07 | 2        |

🔹 **Use Case:** Identify order ranking for each customer.

---

### **3. DENSE_RANK() – Assigns ranks without gaps**
```sql
SELECT order_id, customer_id, order_date,
       DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS dense_rank_num
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | dense_rank_num |
|----------|------------|------------|----------------|
| 1        | 1          | 2024-02-01 | 1              |
| 6        | 1          | 2024-02-06 | 2              |
| 2        | 2          | 2024-02-02 | 1              |
| 7        | 2          | 2024-02-07 | 2              |

🔹 **Use Case:** Useful when ranking customer orders without gaps.

---

### **4. NTILE(4) – Distributes data into 4 groups**
```sql
SELECT order_id, customer_id, order_date,
       NTILE(4) OVER (ORDER BY order_date) AS quartile
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quartile |
|----------|------------|------------|----------|
| 1        | 1          | 2024-02-01 | 1        |
| 2        | 2          | 2024-02-02 | 1        |
| 3        | 3          | 2024-02-03 | 2        |
| 4        | 4          | 2024-02-04 | 2        |
| 5        | 5          | 2024-02-05 | 3        |
| 6        | 1          | 2024-02-06 | 3        |
| 7        | 2          | 2024-02-07 | 4        |
| 8        | 3          | 2024-02-08 | 4        |

🔹 **Use Case:** Divide orders into quartiles based on order date.

---

### **5. LAG() – Get previous order quantity**
```sql
SELECT order_id, customer_id, order_date, quantity,
       LAG(quantity, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS prev_quantity
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | prev_quantity |
|----------|------------|------------|----------|---------------|
| 1        | 1          | 2024-02-01 | 1        | NULL          |
| 6        | 1          | 2024-02-06 | 2        | 1             |
| 2        | 2          | 2024-02-02 | 2        | NULL          |
| 7        | 2          | 2024-02-07 | 1        | 2             |

🔹 **Use Case:** Compare a customer's current and previous order quantities.

---

### **6. LEAD() – Get next order quantity**
```sql
SELECT order_id, customer_id, order_date, quantity,
       LEAD(quantity, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS next_quantity
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | next_quantity |
|----------|------------|------------|----------|---------------|
| 1        | 1          | 2024-02-01 | 1        | 2             |
| 6        | 1          | 2024-02-06 | 2        | NULL          |
| 2        | 2          | 2024-02-02 | 2        | 1             |
| 7        | 2          | 2024-02-07 | 1        | NULL          |

🔹 **Use Case:** Identify the next order placed by each customer.

---

### **7. FIRST_VALUE() – Get the first product ordered by each customer**
```sql
SELECT order_id, customer_id, order_date, product_id,
       FIRST_VALUE(product_id) OVER (PARTITION BY customer_id ORDER BY order_date) AS first_product
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | product_id | first_product |
|----------|------------|------------|------------|--------------|
| 1        | 1          | 2024-02-01 | 1          | 1            |
| 6        | 1          | 2024-02-06 | 2          | 1            |
| 2        | 2          | 2024-02-02 | 2          | 2            |
| 7        | 2          | 2024-02-07 | 3          | 2            |

🔹 **Use Case:** Track the first product purchased by each customer.

---

### **8. LAST_VALUE() – Get the last product ordered by each customer**
```sql
SELECT order_id, customer_id, order_date, product_id,
       LAST_VALUE(product_id) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_product
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | product_id | last_product |
|----------|------------|------------|------------|-------------|
| 1        | 1          | 2024-02-01 | 1          | 2           |
| 6        | 1          | 2024-02-06 | 2          | 2           |
| 2        | 2          | 2024-02-02 | 2          | 3           |
| 7        | 2          | 2024-02-07 | 3          | 3           |

🔹 **Use Case:** Track the last product purchased by each customer.

---

### **9. SUM() OVER() – Running total of quantity ordered**
```sql
SELECT order_id, customer_id, order_date, quantity,
       SUM(quantity) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | running_total |
|----------|------------|------------|----------|---------------|
| 1        | 1          | 2024-02-01 | 1        | 1             |
| 6        | 1          | 2024-02-06 | 2        | 3             |
| 2        | 2          | 2024-02-02 | 2        | 2             |
| 7        | 2          | 2024-02-07 | 1        | 3             |

🔹 **Use Case:** Track cumulative purchases per customer.

---
Here are **more SQL window function examples** with realistic e-commerce use cases and sample outputs.

---

### **10. COUNT() OVER() – Count orders per customer**
```sql
SELECT order_id, customer_id, order_date, quantity,
       COUNT(order_id) OVER (PARTITION BY customer_id) AS total_orders
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | total_orders |
|----------|------------|------------|----------|-------------|
| 1        | 1          | 2024-02-01 | 1        | 2           |
| 6        | 1          | 2024-02-06 | 2        | 2           |
| 2        | 2          | 2024-02-02 | 2        | 2           |
| 7        | 2          | 2024-02-07 | 1        | 2           |

🔹 **Use Case:** Find the number of orders each customer placed.

---

### **11. AVG() OVER() – Running average of order quantity**
```sql
SELECT order_id, customer_id, order_date, quantity,
       AVG(quantity) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS avg_order_quantity
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | avg_order_quantity |
|----------|------------|------------|----------|--------------------|
| 1        | 1          | 2024-02-01 | 1        | 1.0                |
| 6        | 1          | 2024-02-06 | 2        | 1.5                |
| 2        | 2          | 2024-02-02 | 2        | 2.0                |
| 7        | 2          | 2024-02-07 | 1        | 1.5                |

🔹 **Use Case:** Track the **average** order quantity for each customer.

---

### **12. MAX() OVER() – Find the max quantity ordered per customer**
```sql
SELECT order_id, customer_id, order_date, quantity,
       MAX(quantity) OVER (PARTITION BY customer_id) AS max_order_quantity
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | max_order_quantity |
|----------|------------|------------|----------|--------------------|
| 1        | 1          | 2024-02-01 | 1        | 2                  |
| 6        | 1          | 2024-02-06 | 2        | 2                  |
| 2        | 2          | 2024-02-02 | 2        | 2                  |
| 7        | 2          | 2024-02-07 | 1        | 2                  |

🔹 **Use Case:** Identify the **largest quantity** a customer has ordered.

---

### **13. MIN() OVER() – Find the first order quantity per customer**
```sql
SELECT order_id, customer_id, order_date, quantity,
       MIN(quantity) OVER (PARTITION BY customer_id) AS min_order_quantity
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | min_order_quantity |
|----------|------------|------------|----------|--------------------|
| 1        | 1          | 2024-02-01 | 1        | 1                  |
| 6        | 1          | 2024-02-06 | 2        | 1                  |
| 2        | 2          | 2024-02-02 | 2        | 1                  |
| 7        | 2          | 2024-02-07 | 1        | 1                  |

🔹 **Use Case:** Find the **smallest** order quantity per customer.

---

### **14. PERCENT_RANK() – Determine order position within customer history**
```sql
SELECT order_id, customer_id, order_date, quantity,
       PERCENT_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS order_percentile
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | order_percentile |
|----------|------------|------------|----------|------------------|
| 1        | 1          | 2024-02-01 | 1        | 0.00             |
| 6        | 1          | 2024-02-06 | 2        | 1.00             |
| 2        | 2          | 2024-02-02 | 2        | 0.00             |
| 7        | 2          | 2024-02-07 | 1        | 1.00             |

🔹 **Use Case:** Determine the **position of an order** relative to others.

---

### **15. CUME_DIST() – Find cumulative distribution of orders**
```sql
SELECT order_id, customer_id, order_date, quantity,
       CUME_DIST() OVER (PARTITION BY customer_id ORDER BY order_date) AS cume_distribution
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | cume_distribution |
|----------|------------|------------|----------|-------------------|
| 1        | 1          | 2024-02-01 | 1        | 0.5               |
| 6        | 1          | 2024-02-06 | 2        | 1.0               |
| 2        | 2          | 2024-02-02 | 2        | 0.5               |
| 7        | 2          | 2024-02-07 | 1        | 1.0               |

🔹 **Use Case:** Calculate the **cumulative percentage** of orders per customer.

---

### **16. DIFFERENCE BETWEEN CURRENT AND PREVIOUS ORDER DATE**
```sql
SELECT order_id, customer_id, order_date,
       order_date - LAG(order_date, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS days_since_last_order
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | days_since_last_order |
|----------|------------|------------|----------------------|
| 1        | 1          | 2024-02-01 | NULL                 |
| 6        | 1          | 2024-02-06 | 5                    |
| 2        | 2          | 2024-02-02 | NULL                 |
| 7        | 2          | 2024-02-07 | 5                    |

🔹 **Use Case:** Find **gaps between customer orders**.

---

### **17. FIND CUSTOMERS WHO ORDERED FOR CONSECUTIVE DAYS**
```sql
SELECT order_id, customer_id, order_date,
       order_date - LAG(order_date, 1) OVER (PARTITION BY customer_id ORDER BY order_date) AS days_diff
FROM orders
WHERE order_date - LAG(order_date, 1) OVER (PARTITION BY customer_id ORDER BY order_date) = 1;
```
**Example Output:**
| order_id | customer_id | order_date  | days_diff |
|----------|------------|------------|----------|
| 6        | 1          | 2024-02-06 | 1        |

🔹 **Use Case:** Find customers who made **back-to-back purchases**.

---

### **18. ROLLING SALES SUM OVER LAST 3 ORDERS**
```sql
SELECT order_id, customer_id, order_date, quantity,
       SUM(quantity) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS rolling_3_orders
FROM orders;
```
**Example Output:**
| order_id | customer_id | order_date  | quantity | rolling_3_orders |
|----------|------------|------------|----------|------------------|
| 1        | 1          | 2024-02-01 | 1        | 1                |
| 6        | 1          | 2024-02-06 | 2        | 3                |

🔹 **Use Case:** Calculate **rolling sales trends**.

---

These **advanced window function examples** are **crucial** for e-commerce analytics, tracking customer behavior, and analyzing sales trends. Let me know if you need more! 🚀
