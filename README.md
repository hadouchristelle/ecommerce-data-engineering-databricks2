# E-Commerce Data Engineering Project — Batch and Real Time
## Project Overview
This project implements a modern data engineering architecture for processing and analyzing data from an e-commerce platform. It combines batch processing for historical data with real-time processing for newly generated events.

The data is organized according to the Medallion architecture: Bronze, Silver, and Gold. The Gold tables are then used in Power BI to create a business dashboard covering sales, customers, products, sessions, and reviews.
## General Objective

The main objective is to build an end-to-end data solution capable of:

-centralizing five e-commerce data sources;

-preserving raw data for traceability;

-cleaning, validating, and transforming data with Apache Spark;

-processing historical data in batch mode;

-processing new transactions incrementally in near real time;

-ombining batch and real-time results in the Gold layer;

-producing business KPIs in Power BI;

-managing table quality, history, and performance with Delta Lake;

-version-controlling the code with Git and GitHub.
## Business Objectives

The solution is designed to answer the following business questions.

1)Monitor Sales Performance

-What is the total revenue?

-How many transactions have been completed?

-How do sales change from month to month?

-Which products and categories generate the most revenue?

-Which countries generate the highest sales?

2)Understand Customers

-How many customers are registered?

-How are customers distributed by segment and country?

-How are new customer registrations changing over time?

-Which customers have the highest lifetime value?

3)Analyze Digital Activity

-How many sessions have been recorded?

-Which devices are used most often?

-Which marketing channels generate the most traffic?

-What is the session conversion rate?

4)Measure Customer Satisfaction

-What is the average customer rating?

-Which products receive the highest ratings?

-Which products may require improvement?
## Global Architecture
<img width="981" height="652" alt="image" src="https://github.com/user-attachments/assets/8b6ca4f0-935d-4695-896e-29afd061c874" />
## Implementation Steps
### 1. Store Source Files in ADLS Gen2
   The five historical files are stored in Azure Data Lake Storage Gen2. ADLS Gen2 provides a centralized storage location before the data is processed in Databricks.
### 2. Ingest Data into the Batch Bronze Layer
Databricks reads the files from ADLS Gen2 and stores them in Bronze tables. This layer preserves the data as closely as possible to its original format.
### 3. Clean Data in the Batch Silver Layer
The Bronze data is cleaned and standardized with PySpark. The main operations include data-type conversion, null handling, duplicate removal, identifier validation, and business-rule validation.
### 4. Build the Batch Gold Layer
The Silver data is transformed into business-ready tables:
dim_customers
dim_products
fact_sales
fact_sessions
fact_reviews
The dimension tables describe customers and products. The fact tables contain sales, sessions, and reviews.

### 5. Analyze Data with Databricks SQL
SQL queries calculate the main indicators, including total revenue, transaction count, customer count, session count, average rating, sales by category, product performance, and conversion rate.

### 6. Generate Real-Time Events
A Python generator simulates new e-commerce transactions. Each event includes a transaction identifier, customer, product, quantity, unit price, total amount, and timestamp
### 7. Ingest Events with Auto Loader
The generator progressively creates new JSON files. Auto Loader monitors the landing directory, detects only new files, and writes them to a real-time Bronze Delta table.
Auto Loader uses a schema location and a checkpoint to track processed files and resume correctly after an interruption.
### 8. Clean Events with Structured Streaming
Structured Streaming reads new Bronze records as micro-batches. It converts data types, validates identifiers, rejects invalid quantities and prices, recalculates the total amount, and removes duplicate transactions.
### 9. Combine Batch and Real-Time Data
The MERGE INTO operation compares records by transaction_id:
a missing transaction is inserted;
an existing transaction is updated;
rerunning the process does not create duplicates.
### 10. Manage Delta Tables
The project demonstrates several Delta Lake capabilities:
-INSERT, UPDATE, and DELETE;
-MERGE INTO for idempotent processing;
-DESCRIBE HISTORY for table history;
-Time Travel for querying previous versions;
-OPTIMIZE for compacting small files;
-Z-ORDER for improving queries filtered by customer or product.
### 11. Build the Power BI Dashboard
Power BI uses the Gold tables to create an interactive dashboard. It presents the main KPIs, monthly trends, customer segments, product categories, marketing channels, devices, and countries.
<img width="1145" height="710" alt="image" src="https://github.com/user-attachments/assets/6abd5a91-fc65-445c-9754-2b43d740d36c" />
### Technologies Used
-Azure Data Lake Storage Gen2
-Azure Databricks
-Databricks Free Edition
-Apache Spark and PySpark
-Spark Structured Streaming
-Databricks Auto Loader
-Delta Lake
-Databricks SQL
-Python
-Power BI
-Git and GitHub
