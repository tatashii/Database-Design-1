<p align="center">
  <img src="https://cdn-icons-png.flaticon.com/512/6295/6295417.png" width="100" />
</p>
<p align="center">
    <h1 align="center">DATABASE-DESIGN-1</h1>
</p>
<p align="center">
    <em>This repo is for assignment 1: [Reading Chs [2, 3, 4, 5] from Practical Web Database Design Book 📖]</em>
</p>
<hr>

## 🔗 Quick Links

> - [📍 Overview](#-overview)
> - [📄 Create the DB Schema Script](#-create-the-db-schema-script)
> - [🔗 Identify Relationships](#-identify-the-relationships-between-entities)
> - [📊 Draw the ERD Diagram](#-draw-the-erd-diagram-of-This-Sample-Schema)
> - [📅 Daily Revenue Report](#-Write-an-SQL-Query-to-Generate-a-Daily-Revenue-Report)
> - [📈 Monthly Top-Selling Products Report](#-write-an-sql-query-to-generate-a-monthly-top-selling-products-report)
> - [💵 High-Value Customers Report](#-write-an-sql-query-to-retrieve-high-value-customers)
> - [🛠️ Denormalization Mechanism](#-how-to-apply-denormalization-on-customer-and-order-entities)

---

## 📍 Overview

For this assignment, you will be working on a sample database design. The tasks include:

- Creating a DB schema script with the following entities.
- Identifying the relationships between entities.
- Drawing the ERD diagram of the sample schema.
- Writing SQL queries for various reports.
- Applying denormalization mechanisms.

---

## 📄 Create the DB Schema Script

Provide the SQL script to create the database schema with the given entities.


![image](https://github.com/user-attachments/assets/b6c33a5e-343b-4c83-bb8a-2023241cdf47)

``` mysql
create schema if not exists Ecommerce;

use ecommerce;

drop schema Ecommerce;

DROP TABLE IF EXISTS products;
DROP TABLE IF EXISTS categories;
DROP TABLE IF EXISTS product_category;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS product_order; -- order_details 
DROP TABLE IF EXISTS customers;

create table products (

	product_id int primary key,
    name varchar(50) not null,
    description varchar(250) not null ,
    price decimal (10,2) not null check(price between   0 and  99999999.99),
    stock_quantity int

);



create table categories(

	category_id int primary key,
	category_name varchar(50) not null
);

create table product_category(
	
    product_id int ,
    category_id int ,
    isDefault boolean default true,
    
    foreign key (product_id) references products (product_id),
    foreign key (category_id) references categories (category_id)
    
);

create table customers(

	customer_id int primary key,
    first_name VARCHAR(50) NOT NULL CHECK (
        first_name REGEXP '^[A-Za-z].*'
    ),
    
    last_name VARCHAR(50) NOT NULL CHECK (
        last_name REGEXP '^[A-Za-z].*'
    ),
    
    email varchar(50) not null,
    
    password varchar(50) not null
);

create table orders(

	order_id int primary key,
    customer_id int,
    order_date datetime,
    total_amount decimal(10,2) not null check( total_amount > 0 ),
    
    foreign key (customer_id) references customers (customer_id)
);

create table product_order(
	
    product_id int ,
    order_id int ,
    unit_price decimal (10,2) not null check(unit_price between   0 and  99999999.99),
    unit_quantity int not null check(unit_quantity > 0),
    
    foreign key (product_id) references products (product_id),
    foreign key (order_id) references orders (order_id)
    
);

```


---

## 🔗 Identify the Relationships Between Entities

Describe the relationships between the entities, specifying primary and foreign keys.

You can see relationships among entites in the system in Draw the ERD Diagram section.

---

## 📊 Draw the ERD Diagram of This Sample Schema

Include an ERD diagram that visually represents the entities and their relationships.
![image](https://github.com/user-attachments/assets/51144bf2-cc18-4a87-ac8a-6aef374a7df7)




---

## 📅 Write an SQL Query to Generate a Daily Revenue Report

Write an SQL query to generate a daily report of the total revenue for a specific date.


![image](https://github.com/user-attachments/assets/c9a4359c-ab29-49f9-87f6-71cd3ad45d38)

``` mysql
select date(order_date) , sum(total_amount) as total_revenue
from orders
where DATE(order_date) = '2024-08-21'
GROUP BY date(order_date)  
ORDER BY date(order_date) DESC;

```

![image](https://github.com/user-attachments/assets/4dafe4f9-593b-4cac-acfb-46bc3dc21d94)

---

## 📈 Write an SQL Query to Generate a Monthly Top-Selling Products Report

Write an SQL query to generate a monthly report of the top-selling products in a given month.

### Orders Table 

![image](https://github.com/user-attachments/assets/f45d34d4-2ce4-4321-b524-a53d1788ebbe)

---
### Products Table

![image](https://github.com/user-attachments/assets/b127154f-60d8-4b8f-acbb-3b0da64262d1)

---

### Orders_Product (Joining table)

![image](https://github.com/user-attachments/assets/af40fe9d-f2bc-4ebf-b102-c3b6f4b7ab3c)


``` mysql
select month(order_date) as month , count(products.product_id) as product_count , name as product_name
from orders 
join product_order on orders.order_id = product_order.order_id
join products on products.product_id = product_order.product_id
GROUP BY  month ,  name 
ORDER BY product_count DESC;

```
![image](https://github.com/user-attachments/assets/851e99e3-26e1-4c27-a6d0-c2518a0d2078)


---

## 💵 Write an SQL Query to Retrieve High-Value Customers

Write a SQL query to retrieve a list of customers who have placed orders totaling more than $500 in the past month. Include customer names and their total order amounts.

```
select c.first_name , c.last_name , sum(o.total_amount) as total_order_amount , month(o.order_date) as month
from customers c
join orders o on c.customer_id = o.customer_id
where month(o.order_date) = 8
GROUP BY c.first_name , c.last_name , month(o.order_date)
Having total_order_amount > 500 
ORDER BY total_order_amount DESC;
```
![image](https://github.com/user-attachments/assets/69f3f11f-a4c6-4d20-9b19-ac4f677fe95f)


---

## 🛠️ How to Apply Denormalization on Customer and Order Entities

Discuss the denormalization mechanism and how it can be applied to the customer and order entities.

---

[**Return**](#-quick-links)
