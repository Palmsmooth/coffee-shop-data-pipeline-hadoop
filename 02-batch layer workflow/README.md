# Batch Layer Workflow
## Step 1: Upload the text files to HDFS using Hadoop FS commands
### Find where `customer.csv` is located
It is used to change the current working directory in docker container
```sh
$ cd [path]
```
It is used to lists files and directories
```sh
$ ls -lrt
```
It displays the full path of the current directory
```sh
$ pwd
```
<img src="image\find customer.csv.png" width=100% height=40%>

### Create a directory in Hadoop HDFS to store the customer.csv file
View all HDFS paths
```sh
$ hadoop fs -ls /
```
<img src="image\hadoop ls.png" width=100% height=40%>

View in Hue

<img src="image\all path HDFS in Hue.png" width=100% height=40%>

Create a folder in Hadoop HDFS to store the `customer.csv` file,which will be placed in `/tmp/file/sink`
```sh
$ hadoop fs -mkdir /tmp/file/sink
```

### Upload the customer.csv file to Hadoop HDFS at `/tmp/file/sink`

```sh
$ hadoop fs -put /data/file/source/customer.csv /tmp/file/sink
```

<img src="image\csv in HDFS.png" width=100% height=40%>

---

## Step 2: Create a Hive table to map CSV files into a structured format
<img src="image\go to Hive.png" width=100% height=40%>
<img src="image\Hive UI.png" width=100% height=40%>

### Create a Hive table named `customers`
```sh
CREATE TABLE customers(
	  customer_id int, 
	  home_store int, 
	  customer_firstname string, 
	  customer_email string, 
	  customer_since string, 
	  loyalty_card_number string, 
	  birthdate string, 
	  gender string, 
	  birth_year int)
	ROW FORMAT DELIMITED 
	  FIELDS TERMINATED BY ',' 
	  LINES TERMINATED BY '\n' 
	STORED AS INPUTFORMAT 
	  'org.apache.hadoop.mapred.TextInputFormat' 
	OUTPUTFORMAT 
	  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
	LOCATION
	  '/tmp/file/sink/'
```

<img src="image\create hive customers.png" width=100% height=40%>

---

## Step 3: Data cleansing using SparkSQL
### spark.py
```sh
import sys
from pyspark.sql import SparkSession
from pyspark.sql import Row
from pyspark.sql.functions import *
from pyspark.sql.types import *

spark = SparkSession.builder.appName("customers_spark_job").enableHiveSupport().getOrCreate()

cust_df = spark.sql("select * from default.customers")

clean_cust_df = cust_df.withColumn("customer_name", trim(col("customer_firstname"))) \
                    .withColumn("customer_since",to_date(col("customer_since"),"yyyy-MM-dd").cast("string")) \
                    .withColumn("loyalty_card_number",regexp_replace("loyalty_card_number",'\"', '')) \
                    .withColumn("birthdate",to_date(col("birthdate"),"yyyy-MM-dd").cast("string")) \
                    .withColumn("gender",expr("case when gender = 'M' then 'MALE' when gender = 'F' then 'FEMALE' else 'NA' end"))

final_cust_df = clean_cust_df.selectExpr("customer_id as cust_id", \
                                        "customer_name as cust_nm", \
                                        "customer_email as cust_email", \
                                        "customer_since as cust_strt_dt", \
                                        "loyalty_card_number as cust_member_card_no", \
                                        "birthdate as cust_birth_dt", \
                                        "birth_year as cust_birth_yr",\
                                        "gender as cust_gender")

final_cust_df.write.mode("overwrite").save("/tmp/default/customers_cln/")

spark.stop()
```

### Run Spark Submit
```sh
$ spark-submit /data/spark/spark.py
```
<img src="image\Run Spark Submit.png" width=100% height=40%>

### Result
<img src="image\result run spark.py.png" width=100% height=40%>

---

## Step 4: Create a Hive table on top of cleaned data to transform it into a structured format
<img src="image\go to Hive.png" width=100% height=40%>
<img src="image\Hive UI.png" width=100% height=40%>

### Create a Hive table named `customers_cln`
```sh
CREATE EXTERNAL TABLE customers_cln(
cust_id int,
cust_nm string
,cust_email string
,cust_strt_dt string
,cust_member_card_no string
,cust_birth_dt string
,cust_birth_yr int
,cust_gender string
) 
STORED AS PARQUET
LOCATION '/tmp/default/customers_cln/'
```

<img src="image\create hive customers_cln.png" width=100% height=40%>

---