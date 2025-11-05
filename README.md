# Create_database_using_sql
CREATE DATABASE funskool_toys;
USE funskool_toys;

#Creating Tables
CREATE TABLE customers (
  CustomerID   VARCHAR(64),
  City         VARCHAR(128)
);

CREATE TABLE quantity (
  InvoiceNo    VARCHAR(64),
  Product      VARCHAR(256),
  Quantity     VARCHAR(64)
);

CREATE TABLE orders (
  InvoiceNo    VARCHAR(64),
  Product      VARCHAR(256),
  InvoiceDate  VARCHAR(256),
  CustomerID   VARCHAR(64)
);


CREATE TABLE price (
  InvoiceNo    VARCHAR(64),
  StockCode    VARCHAR(64),
  Product      VARCHAR(256),
  UnitPrice    VARCHAR(64)
);

CREATE TABLE products (
  SN           VARCHAR(64),
  Product      VARCHAR(256)
);


#sanity check
-- Row counts
SELECT 'customers' datasets, COUNT(*) cnt FROM customers
UNION ALL SELECT 'products', COUNT(*) FROM products
UNION ALL SELECT 'orders', COUNT(*) FROM orders
UNION ALL SELECT 'price', COUNT(*) FROM price
UNION ALL SELECT 'quantity', COUNT(*) FROM quantity;

SELECT 
  SUM(InvoiceNo IS NULL) AS null_invoiceno,
  SUM(Product IS NULL)   AS null_product,
  SUM(InvoiceDate IS NULL) AS null_date,
  SUM(CustomerID IS NULL) AS null_customer
FROM orders;

SELECT MIN(InvoiceDate) AS first_date, 
       MAX(InvoiceDate) AS last_date
FROM orders;

-- Join coverage
SELECT 
  (SELECT COUNT(*) FROM orders) AS order_rows,
  (SELECT COUNT(*) FROM orders o 
     JOIN price p ON p.InvoiceNo=o.InvoiceNo AND p.Product=o.Product) AS matched_price,
  (SELECT COUNT(*) FROM orders o 
     JOIN quantity q ON q.InvoiceNo=o.InvoiceNo AND q.Product=o.Product) AS matched_qty;
     
     
CREATE TABLE ML_dataset AS
SELECT
    o.InvoiceNo,
    p.StockCode,
    o.Product,
    q.Quantity,
    o.InvoiceDate,
    p.UnitPrice    AS Price,
    o.CustomerID,
    c.City
FROM orders o
LEFT JOIN price p
  ON o.InvoiceNo = p.InvoiceNo
 AND o.Product   = p.Product
LEFT JOIN quantity q
  ON o.InvoiceNo = q.InvoiceNo
 AND o.Product   = q.Product
LEFT JOIN customers c
  ON o.CustomerID = c.CustomerID;
  
  
  -- How many rows?
SELECT COUNT(*) FROM ML_dataset;

-- Check if any data is missing
SELECT 
  SUM(InvoiceNo IS NULL) AS null_Inv,
  SUM(StockCode IS NULL) AS null_Stk,
  SUM(Product IS NULL)    AS null_prod,
  SUM(Quantity IS NULL)    AS null_qty,
  SUM(InvoiceDate IS NULL)    AS null_Invd,
  SUM(Price IS NULL)    AS null_Prc,
  SUM(CustomerID IS NULL)    AS null_Cid,
  SUM(City IS NULL)     AS null_city
FROM ML_dataset;

-- Preview
SELECT * FROM ML_dataset;
