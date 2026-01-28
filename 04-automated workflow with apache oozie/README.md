# Automated Workflow with Apache Oozie
## Step 1: Join customer data and transaction data on the customer ID (id) to create a table suitable for calculating membership points
### Create_hive_loyalty.sql
```sh
CREATE EXTERNAL TABLE loyalty(
cust_id int
,cust_name string
,cust_member_card_no string
,spnd_amt int
) 
PARTITIONED BY (data_dt string)
STORED AS ORC
LOCATION '/tmp/default/loyalty/'
```

### Create a table named loyalty using Hive commands
```sh
$ hive -f /data/sql/create_hive_loyalty.sql
```
<img src="image\create table Hive command.png" width=100% height=40%>

#### result
<img src="image\result create table loyalty .png" width=100% height=40%>

### Insert_hive_loyalty.sql
```sh
INSERT OVERWRITE TABLE loyalty
PARTITION(data_dt='${data_dt}')
select a.cust_id, a.cust_nm, a.cust_member_card_no, sum(b.odr_prc) as spnd_amt 
from customers_cln as a
join transactions_cln as b
on a.cust_id = b.cust_id
group by a.cust_id, a.cust_nm, a.cust_member_card_no
```

### Insert data into the loyalty table
This error occurs because the cloudera user does not have permission to write to the /tmp/default/loyalty path

<img src="image\not permission to write.png" width=100% height=40%>
<img src="image\permission.png" width=100% height=40%>

#### Changes permissions of an HDFS directory
```sh
$ hadoop fs -chmod 777 /tmp/defult/loyalty 
```
<img src="image\chmod 777.png" width=100% height=40%>
<img src="image\permission rwx.png" width=100% height=40%>

#### result
<img src="image\result insert.png" width=100% height=40%>
<img src="image\result insert in HDFS.png" width=100% height=40%>

---

## Step 2: Set the scheduler to create and save a new table each day by running workflows daily
<img src="image\go to oozie.png" width=100% height=40%>
<img src="image\oozie editor.png" width=100% height=40%>

### Upload the insert_hive_loyalty.sql file to Hadoop HDFS at `/tmp/file`

```sh
$ hadoop fs -put /data/sql/insert_hive_loyalty.sql /tmp/file
```
<img src="image\upload sql to HDFS.png" width=100% height=40%>

### Create workflow
<img src="image\create workflow.png" width=100% height=40%>
<img src="image\pull Hive2.png" width=100% height=40%>
<img src="image\add sql file in workflow.png" width=100% height=40%>
<img src="image\add parameters and save.png" width=100% height=40%>
<img src="image\My workflow.png" width=100% height=40%>

### Set the scheduler
<img src="image\coordinator create.png" width=100% height=40%>
<img src="image\Set the scheduler.png" width=100% height=40%>
<img src="image\submit.png" width=100% height=40%>
<img src="image\submit my coordinator.png" width=100% height=40%>
<img src="image\Coordinator My_Coordinator.png" width=100% height=40%>
<img src="image\Coordinator running.png" width=100% height=40%>

### Trigger when the time is due
<img src="image\trigger to run.png" width=100% height=40%>
<img src="image\run succeeded.png" width=100% height=40%>

### Result
<img src="image\result in file path.png" width=100% height=40%>
<img src="image\result select distinct date.png" width=100% height=40%>
<img src="image\result select all.png" width=100% height=40%>
<img src="image\result.png" width=100% height=40%>

---
