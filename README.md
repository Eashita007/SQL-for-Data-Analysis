# SQL-for-Data-Analysis (Task 4)

This repository contains your Task 4 deliverables for practicing SQL data-analysis on the Chinook sample database.

## Project Structure

SQL-for-Data-Analysis/
├── Chinook_MySql.sql          # MySQL dump of the Chinook schema & data
├── README.md                  # This file
└── Screenshots of results/    # PNGs of each query’s output

## Prerequisites

- **MySQL 8.0+** installed on your machine  
- A MySQL user with privileges to create databases and run `SOURCE`

## 1. Import the Chinook Database

Open a terminal in this folder and run:

```bash
# Drop any old copy, create the DB, and import the schema + data
mysql -u root -p <<EOF
DROP DATABASE IF EXISTS Chinook;
CREATE DATABASE Chinook;
USE Chinook;
SOURCE $(pwd)/Chinook_MySql.sql;
EOF

eashita@ZUNI:~/SQL-for-Data-Analysis$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 24
Server version: 8.0.42-0ubuntu0.24.04.1 (Ubuntu)

Copyright (c) 2000, 2025, Oracle and/or its affiliates.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> DROP DATABASE IF EXISTS Chinook;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE DATABASE Chinook;
Query OK, 1 row affected (0.01 sec)

mysql> USE Chinook;
Database changed

mysql> SOURCE /home/eashita/SQL-for-Data-Analysis/Chinook_MySql.sql;
Query OK, 11 rows affected (0.19 sec)
…

mysql> SHOW TABLES;
+-------------------+
| Tables_in_Chinook |
+-------------------+
| Album             |
| Artist            |
| Customer          |
| Employee          |
| Genre             |
| Invoice           |
| InvoiceLine       |
| MediaType         |
| Playlist          |
| PlaylistTrack     |
| Track             |
+-------------------+
11 rows in set (0.01 sec)

mysql> SELECT DISTINCT YEAR(InvoiceDate) AS Year
    ->   FROM Invoice
    ->  ORDER BY Year DESC;
+------+
| Year |
+------+
| 2025 |
| 2024 |
| 2023 |
| 2022 |
| 2021 |
+------+
5 rows in set (0.01 sec)

mysql> SELECT InvoiceId, CustomerId, InvoiceDate, Total
    ->   FROM Invoice
    ->  WHERE YEAR(InvoiceDate)=2025
    ->  ORDER BY InvoiceDate DESC;
+-----------+------------+---------------------+-------+
| InvoiceId | CustomerId | InvoiceDate         | Total |
+-----------+------------+---------------------+-------+
|       412 |         58 | 2025-12-22 00:00:00 |  1.99 |
|       411 |         44 | 2025-12-14 00:00:00 | 13.86 |
… (80 rows total)
+-----------+------------+---------------------+-------+
80 rows in set (0.01 sec)

mysql> SELECT i.InvoiceId,
    ->        CONCAT(c.FirstName,' ',c.LastName) AS Customer,
    ->        i.Total
    ->   FROM Invoice i
    ->   JOIN Customer c ON i.CustomerId=c.CustomerId
    ->  ORDER BY i.InvoiceDate DESC;
+-----------+----------------------+-------+
| InvoiceId | Customer             | Total |
+-----------+----------------------+-------+
|       412 | Eashita Suvarna      |  1.99 |
… 
+-----------+----------------------+-------+
80 rows in set (0.01 sec)

mysql> SELECT g.Name AS Genre,
    ->        SUM(il.Quantity*il.UnitPrice) AS Sales
    ->   FROM InvoiceLine il
    ->   JOIN Track t ON il.TrackId=t.TrackId
    ->   JOIN Genre g ON t.GenreId=g.GenreId
    ->  GROUP BY g.Name
    ->  ORDER BY Sales DESC;
+--------------------+--------+
| Genre              | Sales  |
+--------------------+--------+
| Rock               | 826.65 |
… 
+--------------------+--------+
24 rows in set (0.03 sec)

mysql> DROP VIEW IF EXISTS customer_spend;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE VIEW customer_spend AS
    ->   SELECT CustomerId, SUM(Total) AS TotalSpent
    ->     FROM Invoice
    ->    GROUP BY CustomerId;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT CustomerId, TotalSpent
    ->   FROM customer_spend
    ->  ORDER BY TotalSpent DESC
    ->  LIMIT 10;
+------------+------------+
| CustomerId | TotalSpent |
+------------+------------+
|          6 |      49.62 |
… 
+------------+------------+
10 rows in set (0.01 sec)

mysql> EXIT;
Bye
