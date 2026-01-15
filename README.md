# Data Engineering Project: Coffee Shop Data Pipeline with Hadoop
This project is from [Road to Data Engineer](https://pages.datath.com/r2de3-course) course. Road to Data Engineer is a course that provides fundamental to advanced knowledge in the Data Engineer track. We can apply this knowledge to build automated data pipelines.
## Project Overview
This project aims to create a data pipeline for a coffee shop using Hadoop ecosystem to process and calculate daily member card points for running campaigns and promotions. It also intends to send real-time data when a customer places an order, allowing the back office system to quickly check the latest order.

### Hadoop System Design
<img src="image\hadoop-system-design.png" width=100% height=40%>

- Batch Layer: Handles offline data processing tasks, typically for historical customer data in this project.
- Speed Layer: Deals with real-time data processing, handling transaction data when a customer places an order.

---

## Batch Layer Workflow
### Data Collection
- Source System: Text files are generated in form of csv (customer.csv).
- CLI: The Hadoop command-line interface (CLI) is used to interact with the system.
- HDFS: Text files is stored in the Hadoop Distributed File System (HDFS).

### Data Processing
- SparkSQL: Data is loaded into SparkSQL for data cleansing.
- HIVE: Data is transformed and queried using Hive which is a data warehousing infrastructure built on top of Hadoop.

### Output
- Tables: Customer data is stored in Hive tables for further analysis or reporting.

---

## Speed Layer Workflow
### Data Collection
- Source System: Log files are generated from a source system containing transaction data when a customer places an order.
- Flume: Flume is used to collect, aggregate, and move log data into the Hadoop ecosystem.
- HDFS: Log data is stored in HDFS.
- HBASE: Data is stored in HBase, a NoSQL database optimized for fast read/write operations.

### Data Processing
- Spark Streaming: Real-time data is processed using Spark Streaming.
- HIVE: Data is transformed and queried using Hive.

### Output
- Tables: Transaction data is stored in Hive tables for further analysis or reporting.

---

## Automated workflow with Apache Oozie
- Join customer data and transaction data on the customer ID (id) to create a table suitable for calculating membership points.
- Set the scheduler to create and save a new table each day by running workflows daily.

---

## Getting Start

### 01-Hadoop Docker Installation
- Setting up Google Compute Engine for a Hadoop project
- Getting started with Cloudera Hadoop via a Docker container
- Cloning a Git repository to use the materials
### 02-Batch Layer Workflow
#### step 1: Upload the text files to HDFS using Hadoop FS commands
#### step 2: Create a Hive table to map CSV files into a structured format
#### step 3: Data cleansing using SparkSQL
#### step 4: Create a Hive table on top of cleaned data to transform it into a structured format

### 03-Speed Layer Workflow
#### step 1: Generate mock data and store it in the designated folder
#### step 2.1: Ingest log files into HDFS using Apache Flume
#### step 2.2: Ingest log files into HBase using Apache Flume
#### step 3: Create a Hive table on top of SequenceFiles to convert them into structured data
#### step 4: Data cleansing using Spark Streaming
#### step 5: Create a Hive table on top of cleaned data to transform it into a structured format

### 04-Automated workflow with Apache Oozie
#### step 1: Join customer data and transaction data on the customer ID (id) to create a table suitable for calculating membership points
#### step 2: Set the scheduler to create and save a new table each day by running workflows daily

---
