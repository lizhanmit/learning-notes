# Hive Note

- [Hive Note](#hive-note)
  - [Concepts](#concepts)
  - [Best Practices](#best-practices)
    - [Execution Engine](#execution-engine)
    - [Avoid Managed Table](#avoid-managed-table)
    - [Choosing File Format](#choosing-file-format)
    - [Partitioning](#partitioning)
    - [Normalization](#normalization)
  - [Data Units](#data-units)
    - [Databases](#databases)
    - [Partitions](#partitions)
    - [Buckets / Clusters](#buckets--clusters)
  - [Data Types](#data-types)
    - [String Types](#string-types)
  - [Storage Formats](#storage-formats)
    - [Row Format](#row-format)
    - [File Format](#file-format)
      - [Query performance](#query-performance)
      - [Disk Usage and Compression](#disk-usage-and-compression)
      - [Schema Change / Evolution](#schema-change--evolution)
  - [Managed Tables VS External Tables](#managed-tables-vs-external-tables)
  - [Traditional DB VS Hive](#traditional-db-vs-hive)
  - [Locking](#locking)
  - [ACID in Hive](#acid-in-hive)
  - [Indexes](#indexes)
    - [Compact Indexes](#compact-indexes)
    - [Bitmap Indexes](#bitmap-indexes)
  - [Hive Web UI](#hive-web-ui)
  - [Architecture](#architecture)
    - [Metastore](#metastore)
      - [Embedded Metastore Configuration](#embedded-metastore-configuration)
      - [Local Metastore Configuration](#local-metastore-configuration)
      - [Remote Metastore Configuration](#remote-metastore-configuration)
    - [HCatalog](#hcatalog)
    - [Job Execution Flow](#job-execution-flow)
    - [Mode of Hive](#mode-of-hive)
      - [Local Mode](#local-mode)
      - [Map Reduce Mode](#map-reduce-mode)
  - [Hive 2](#hive-2)
  - [Limitations](#limitations)
  - [Spark x Hive](#spark-x-hive)
    - [Spark on Hive](#spark-on-hive)
    - [Hive over Spark](#hive-over-spark)
  - [Coding](#coding)
    - [Create Tables](#create-tables)
    - [Load Data](#load-data)
    - [beeline](#beeline)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Interview](#interview)

---

## Concepts

- Hive is a **data warehousing** component based on Hadoop, which performs reading, writing and managing large data sets in a distributed environment using SQL-like interface: HiveQL.
- Enables users to do ad-hoc querying, summarization and data analysis and data mining easily that does not require real time processing.
- Hive's query response time is typically much faster than others on the same volume of big datasets.
- Hive internally gets converted into **MapReduce** programs.
- You can couple Hive with other tools to use it in many other domains. For example,
  - Tableau along with Apache Hive can be used for Data Visualization.
  - Apache Tez integration with Hive will provide you real time processing capabilities.
- Stores data in HDFS by default. Also supports Amazon S3.
- Hive can be used to process terabytes to petabytes of data.
- **Hive is not a storage database. It is just an abstraction layer over a file stored on HDFS/S3.**

---

## Best Practices

### Execution Engine

Hive uses MapReduce as a **default** processing engine that runs MapReduce job for executing a query in the background. **Recommend** setting the default engine to Tez, which optimizes Hive query response time, not uses a MapReduce engine, prepares and optimizes a DAG plan and executes queries. 

`set hive.execution.enginer=tez`

### Avoid Managed Table

If a user drops the table, the data will be deleted from the target location on which the Hive table was created. **Recommend** using external tables in production. 

### Choosing File Format

The file format plays an important role in performance and managing schema evolution. 

If the Hive tables are used for reports, choose columnar file format such as ORC.

Choose Avro to deal with schema evolution.

### Partitioning

In most cases we should partition it.

### Normalization

Joining is a costly operation in Hive because it requires data to be shuffled across a network.

The tables in Hive should not be in normalization form.

---

## Data Units

### Databases

Used to enforce security for a user or group of users.

**Namespaces** are used to avoid naming conflicts for tables, views, partitions, columns, and so on.

### Partitions

Partitioning divides large amount of data into multiple slices based on the value of a table column.

Each Table can have one or more partition keys which determines how the data is stored.

Each unique value of the partition keys defines a partition of the table.

Partitions allow the user to efficiently identify the rows that satisfy a specified criteria. You can run the query only on the relevant partition of the table, thereby speeding up the analysis.

Partition does not solve the responsiveness problem in the case of data skewing towards a particular partition value. 

Example: 

```sql
CREATE TABLE logs (time_stamp BIGINT, line STRING)
PARTITIONED BY (dt STRING, country STRING);


SHOW PARTITIONS logs;
```

At the filesystem level, partitions are simply nested subdirectories of the table directory. Directory structure of "logs" table:

![logs-table-directory-structure.png](img/logs-table-directory-structure.png)

The partition values are specified explicitly when loading data into a partitioned table. Partition columns are virtual columns, they are not part of the data itself but are derived on load.

Example: 

```sql
LOAD DATA LOCAL INPATH 'input/hive/partitions/file1'
INTO TABLE logs
PARTITION (dt='2001-01-01', country='GB');
```

Partitions are not divided by the value of the data, but specified by the user. In the above example, there could be no value of "dt" and "country" in the data.

Hive can only add partitions every fifteen minutes to an hour. 

Drawback to having too many partitions: the large number of Hadoop files and directories that are created unnecessarily and the overhead to NameNode, since it must keep all metadata for the filesystem in memory.

### Buckets / Clusters

Bucketing decomposes data into more manageable or equal parts.

Data in each partition may be subdivided further into buckets based on the value of a hash function of some column of the table.

For example the "page_views" table may be bucketed by "userid", which is one of the columns, other than the partitions columns, of the "page_view" table. 

Advantages: 

- enable more efficient queries.
    - A join of two tables that are bucketed on the same columns, efficient map-side join. Because of equal volumes of data in each partition, joins at map side will be quicker.
- make sampling more efficient.

Bucketing is used to avoid data skew.

Example:

```sql
CREATE TABLE bucketed_users (id INT, name STRING)
CLUSTERED BY (id) INTO 4 BUCKETS;
```

If the bucket is sorted by one or more columns, more efficient map-side joins. 

```sql
CREATE TABLE bucketed_users (id INT, name STRING)
CLUSTERED BY (id) SORTED BY (id ASC) INTO 4 BUCKETS;
```

It is **advisable to get Hive to perform the bucketing**, because Hive does not check that the buckets in the datafiles on disk are consistent with the buckets in the table definition. 

Populate the bucketed table:

1. Set `hive.enforce.bucketing` property to `true`. Then Hive knows to create the number of buckets declared in the table definition. 
2. `INSERT OVERWRITE TABLE bucketed_users SELECT * FROM users;`

Each bucket is just a file in the table (or partition) directory.

The number of buckets in MapReduce output files is the same as reduce tasks.

Example of sampling:

```sql
-- sample 1/4
SELECT * FROM bucketed_users TABLESAMPLE(BUCKET 1 OUT OF 4 ON id);
```

Bucket numbering is 1-based.

For example, suppose a table using "date" as the top-level partition and "employee_id" as the second-level partition leads to too many small partitions. Instead, if we bucket the "employee" table and use "employee_id" as the bucketing column, the value of this column will be hashed by a user-defined number into buckets. Records with the same "employee_id" will always be stored in the same bucket. Assuming the number of "employee_id" is much greater than the number of buckets, each bucket will have many "employee_id". 

The number of buckets is fixed so it does not fluctuate with data. 

---

## Data Types 

### String Types

- STRING: sequence of characters in a specified character set.
- VARCHAR: sequence of characters in a specified character set with a maximum length.
- CHAR: sequence of characters in a specified character set with a defined length.

Implicit conversion of STRING to DOUBLE is allowed.

---

## Storage Formats

### Row Format

Row format: how rows, and the fields in a particular row, are stored. Defined by a SerDe.

- deserializer: querying a table
- serializer: `INSERT` or `CREATE TABLE ... AS SELECT ...`

### File Format

File format: the container format for fields in a row.

Hive supports: 

- TEXTFILE (plain-text file, simplest one)
- SEQUENCEFILE
- ORC 
- RCFILE 
- Avro
- Parquet (from Hive 0.13)

Important factors to consider when choosing file formats for tables:

- Query performance
- Disk usage and compression
- Schema change / evolution

#### Query performance 

The write performance can be increased by choosing a file in a non-compressed format as compression takes more time.

If data is compressed, more data will be processed at a single mapper when being read.

Hadoop is designed for write once read many. Therefore, **always go for compression**.

For partial data read or only reading a few columns, columnar file format such as ORC is a better option.

#### Disk Usage and Compression

Compression help reduce space when storing data.

AVRO without compression can reduce disk usage by up to 10%, while AVRO with compression can reduce it by up to 40-50%.

Parquet can reduce it to 80% by using specific compression techniques. 

Compression also helps in reducing network I/O.

#### Schema Change / Evolution

Data with a flexible structure can have fields added, updated, or deleted over time and even varies amongst concurrently ingested records.

Almost all of the file format choices focus on managing flexibly structured data.  

---

## Managed Tables VS External Tables

[Differences between managed tables & external tables:](http://www.aboutyun.com/thread-7458-1-1.html)

- When importing data to an external table, data is not moved under its data warehouse directory, which means data in the external table is not managed by Hive. This is different with managed table.
- Deleting:
  - When deleting managed tables, Hive will delete both metadata and data.
  - When deleting external tables, Hive only deletes metadata. Data is retained.

Which one to use?

- Not many differences in general. So, it depends on personal preference.
- Practical experience: If all processes involve Hive, create managed tables. Otherwise, use external tables.

How to check a table is managed or external: `desc formatted <table_name>`. See "table type" in output.

---

## Traditional DB VS Hive

Traditional database: 

- Schema on write: data is checked against the schema when it is written into the database.
- Query time performance faster because the database can index columns and perform compression on the data.

Hive: 

- **Schema on read only**: does not verify the data when it is loaded.
- Very fast initial load, since the data does not have to be read, parsed, and serialized to disk in the database’s internal format. 

---

## Locking

Hive supports for table- and partition-level locking using ZooKeeper.

For instance, it prevents one process from dropping a table while another is reading from it.  

By default, locks are not enabled.

---

## ACID in Hive

By default, transactions are configured to be off. Tables must be bucketed to make use of these features.

External tables cannot be made ACID tables 

Hive transaction manager must be set to `org.apache.hadoop.hive.ql.lockmgr.DbTxnManager` in order to work with ACID tables.

Transactions are provided at the row level in Hive 0.14. 

Make sure you have set all the following properties to enable transactional capability on Hive:

```sql
set hive.support.concurrency = true;
set hive.enforce.bucketing = true;
set hive.exec.dynamic.partition.mode = nonstrict;
set hive.txn.manager = org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
set hive.compactor.initiator.on = true;
set hive.compactor.worker.threads = 1;
```

---

## Indexes

Two types: 

### Compact Indexes

- Store the HDFS block numbers of each value, rather than each file offset.
- Do not take up much disk space.

### Bitmap Indexes

Appropriate for low-cardinality columns (such as gender or country).

---

## Hive Web UI

Hive has its own built-in web IDE,
Hive Web Interface. However, it is not powerful and seldom used. 

Instead, both Ambari Hive View and Hue (http:/​/​gethue.​com/​) are popular, user-friendly, and powerful web IDEs for the Hadoop and Hive ecosystem. 

---

## Architecture

![hive-architecture.png](img/hive-architecture.png)

![data-processing-model.png](img/data-processing-model.png)

### Metastore

- Stores metadata for each of the tables such as their schema and location.
- The metadata helps the driver to keep track of the data. 
- A backup server regularly replicates the data which can be retrieved in case of data loss.

![metastore-configurations.png](img/metastore-configurations.png)

#### Embedded Metastore Configuration

**By default**, the metastore service runs in the same JVM as the Hive service and contains an embedded **Derby** database instance backed by the local disk.

However, only one embedded Derby database can access the database files on disk at any one time,
which means you can have only one Hive session open at a time that accesses the same metastore. By contrast, configured relational database offers a shared service so that all hive users can see the same metadata set.

#### Local Metastore Configuration

Use a standalone database to support multiple sessions and multiple users.

Metastore service still runs in the same process as the Hive service but connects to a database running in a separate process.

MySQL is a popular choice for the standalone metastore.

#### Remote Metastore Configuration

One or more metastore servers run in separate processes to the Hive service.

Better manageability and security because the database tier can be completely firewalled off, and the clients no longer need the database credentials.

**In the real production environment, it always configures an external relational database as the Hive metastore.**

### HCatalog

HCatalog is a table and storage management service.

HCatalog is built on top of the Hive Metastore service. It enables users to view data like relational tables without worrying about where the data is stored and what the format of a file is.  

![hcatalog-architecture.png](img/hcatalog-architecture.png)

The tables in HCatalog are immutable, which means data in the table and the partition are not appendable in nature. 

- In the case of the partition table, data can be appended to a new partition without affecting the old partition.
- In the case of the non-partitioned table, table must be deleted before executing the Pig script.

### Job Execution Flow

![job-execution-flow.png](img/job-execution-flow.png)

1. Executing Query from the UI (User Interface).
2. The driver is interacting with Compiler for getting the plan. (Here plan refers to query execution, process and its related metadata information gathering.)
3. The compiler creates the plan for a job to be executed. Compiler communicating with Meta store for getting metadata request.
4. Meta store sends metadata information back to compiler.
5. Compiler communicating with Driver with the proposed plan to execute the query.
6. Driver Sending execution plans to Execution engine.
7. Execution Engine (EE) acts as a bridge between Hive and Hadoop to process the query. For DFS operations.
    - EE should first contacts Name Node and then to Data nodes to get the values stored in tables.
    - EE is going to fetch desired records from Data Nodes. The actual data of tables resides in data node only. While from Name Node it only fetches the metadata information for the query.
    - It collects actual data from data nodes related to mentioned query.
    - Execution Engine (EE) communicates bi-directionally with Meta store present in Hive to perform DDL (Data Definition Language) operations. Here DDL operations like CREATE, DROP and ALTERING tables and databases are done. Meta store will store information about database name, table names and column names only. It will fetch data related to query mentioned.
    - Execution Engine (EE) in turn communicates with Hadoop daemons such as Name node, Data nodes, and job tracker to execute the query on top of Hadoop file system.
8. Fetching results from driver.
9. Sending results to Execution engine. Once the results fetched from data nodes to the EE, it will send results back to driver and to UI (front end).

### Mode of Hive

Hive can operate in two modes depending on the size of data nodes in Hadoop.

By default, Map Reduce mode.

Set to local mode: `SET mapred.job.tracker=local;`

#### Local Mode

When to use:

- when the Hadoop installed under pseudo mode with having one data node.
- when the data size is smaller in term of limited to single local machine.

Processing will be very fast on smaller data sets present in the local machine.

#### Map Reduce Mode

When to use:

- when Hadoop is having multiple data nodes and data is distributed across different node.
- It will perform on large amount of data sets and query is going to execute in parallel way.

Processing of large data sets with better performance can be achieved through this mode.

---

## Hive 2

Hive2 architecture:

![hive2-architecture.png](img/hive2-architecture.png)

Hiveserver2 has an enhanced server designed for multiple client concurrency and improved authentication.

It provides better support for open API clients such as JDBC and ODBC.

**Recommend** using `beeline` as the major Hive CLI instead of the `hive` command.

The primary difference between two version: 

- `hive` is an Apache-Thrift-based
client. The `hive` command directly connects to the Hive
drivers, so we need to install the Hive library on the client. 
- `beeline` is a JDBC client, which connects to hiveserver2 through JDBC connections without installing Hive libraries on the client. That means we can run `beeline` remotely from outside the cluster.

HiveServer2 allocates one worker thread per TCP connection. Disadvantage: a thread will be allocated to a connection even if a connection is idle, which leads to a decrease in performance.

---

## Limitations

- As Hadoop is intended for long sequential scans and Hive is based on Hadoop, you would expect queries to have a very high latency. (Response time: several minutes)
- Hive is **read-based** and therefore **not appropriate for transaction processing** that typically involves a high percentage of write operations.
- Not designed for OLTP. Only used for OLAP.
- Supports overwriting or apprehending data, but not updates and deletes (below version 0.13). Supports update and delete since version 0.14.
- Index is less used in Hive, which is different with traditional DB.

---

## Spark x Hive

### Spark on Hive

Spark can leverage the Hive metastore to write or query data in Hive.

1. Copy the `hive-site.xml` to
the `${SPARK_HOME}/conf` directory.
2. Run `spark-sql` command. Then you can write SQL to query Hive tables.

### Hive over Spark

Hive can use Spark as an alternative engine. 

Requires the Yarn `FairScheduler` and set `hive.execution.engine=spark`. 

---

## Coding

All Hive keywords, table names and column names are **case-insensitive**.

- `show databases;`
- `show tables;`
- `show tables 'prefix.*';`: List tables with prefix 'prefix'. (Java regular expression syntax)
- `show create table <table_name>;`: Show statement that creates the table.
- `desc <table_name>;`: Show simple structure of the table.
- `desc extended <table_name>;`: Show columns and all other properties of table.
- `desc formatted <table_name>;`: Show formatted detailed info about the table.
- `alter table <old_table_name> rename to <new_table_name>;`
- `alter table <old_table_name> replace columns (<column_name> <column_type>, ...);`: Replace all the existing columns.
- `alter table <old_table_name> add columns (<new_col_name> <new_col_type>);`
- `drop table <table_name>;`
- `alter table <table_name> drop partition (ds='2019-05-10');`: Drop a partition.
- `! <command>`: In Hive shell, execute Linux commands. For instance, `! ls`.
- Need alias when order by count. Otherwise, error "Not yet supported place for UDAF 'count'". For instance, `select count(*) as cnt, brand_id from user_log where action='2' group by brand_id order by cnt desc;`.
- Save the result of Hive query to text file: `hive -e 'desc dbName.tableName;' > ~/descriptionOfTable.txt`.
- `explain <query_statement>`: gives the detail plans of execution in stages, which is important when you are debugging queries for optimization.

Hive queries can be written in a file with the `.hql` extension and can be run using the `hive -f <filename>` command.

### Create Tables

```sql
CREATE TABLE IF NOT EXISTS product(
product_id int,
product_name String,
product_catagory
price String,
manufacturer String
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE
location '/user/packt/products;

--external table
--partitioning
CREATE EXTERNAL TABLE IF NOT EXISTS product(
product_id int,
product_name String,
product_catagory
price String,
manufacturer String
)
PARTITIONED BY (manufacturer_country STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE
location '/user/packt/products;
```

The location is optional. If not specified, it will search for data in the Hive warehouse directory and if the directory is not present, then it will create one directory with the table name. The default warehouse directory is `/user/hive/warehouse`. In the above example, the data should be in `/user/hive/warehouse/product`.

### Load Data

There are multiple ways to load data into Hive tables. 

If there is already legacy data in HDFS, steps: 

1. Copy a data file into the specified location using HDFS "put" or "copy" commands.

```
hadoop dfs -put /tmp/pv_2008-06-08.txt /user/data/staging/page_view
```

2. Create an external table pointing to this location with all relevant row format info.

```sql
CREATE EXTERNAL TABLE page_view_stg(viewTime INT, userid BIGINT,
                page_url STRING, referrer_url STRING,
                ip STRING COMMENT 'IP Address of the User',
                country STRING COMMENT 'country of origination')
COMMENT 'This is the staging page view table'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '44' LINES TERMINATED BY '12'
STORED AS TEXTFILE
LOCATION '/user/data/staging/page_view';
```

3. Transform or load data and insert into Hive tables.

```sql
FROM page_view_stg pvs
INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='US')
SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip
WHERE pvs.country = 'US';
```

If loading data from a file in the local files system directly into a Hive table where the input data format is the same as the table format, you can do it like the following: 

`LOAD DATA LOCAL INPATH '/tmp/pv_2008-06-08_us.txt' INTO TABLE page_view PARTITION(date='2008-06-08', country='US')`

If data is copied from one location to the target tables partition location using "copy" command, and data in partitions are not loaded via the Hive interface, then you must run `msck repair table <table_name>;`  to update partition information in the metastore DB. 

### beeline

![beeline-command.png](img/beeline-command.png)

`dfs` command may be disabled in beeline for permissions control in some Hadoop distributions.

`!history`: show the command's history.

---

## Installation 

Hive requires Hadoop and Java 1.7 or later already installed on the machine.

Check Hive version: `hive --version`

---

## Configuration 

Enable Snappy compression for Hive output:

```
SET hive.exec.compress.output=true;
SET mapred.output.compression.codec=org.apache.hadoop.io.compress.SnappyCodec;
SET mapred.output.compression.type=BLOCK; 
```

https://www.cloudera.com/documentation/enterprise/5-9-x/topics/introduction_compression_snappy.html

---

## Interview

[Top Hadoop Interview Questions To Prepare In 2022 – Apache Hive](https://www.edureka.co/blog/interview-questions/hive-interview-questions/)