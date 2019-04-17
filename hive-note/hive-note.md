# Hive Note

## Basics

- Hive is a **data warehousing** component which performs reading, writing and managing large data sets in a distributed environment using SQL-like interface: HiveQL.
- Hive is mostly used for data warehousing where you can perform analytics and data mining that does not require real time processing.
- Hive internally gets converted into **MapReduce** programs.
- You can couple Hive with other tools to use it in many other domains. For example,
  - Tableau along with Apache Hive can be used for Data Visualization.
  - Apache Tez integration with Hive will provide you real time processing capabilities.
- Stores data in HDFS by default. Also supports Amazon S3.

---

### Partitions

- Each Table can have one or more partition Keys which determines how the data is stored.
- Each unique value of the partition keys defines a partition of the Table.
- Partitions allow the user to efficiently identify the rows that satisfy a specified criteria. You can run the query only on the relevant partition of the table, thereby speeding up the analysis.

**NOTE** that the value of the partition key does not mean it contains all or only relevant data. It is the user's job to guarantee the relationship between partition name and data content.

Example: 

```sql
CREATE TABLE logs (timestamp BIGINT, line STRING)
PARTITIONED BY (date STRING, country STRING);


SHOW PARTITIONS logs;
```

Directory structure of "logs" table:

![logs-table-directory-structure.png](img/logs-table-directory-structure.png)

The partition values are specified explicitly when loading data into a partitioned table. Datafiles do not
contain values for partition columns.

---

### Buckets 

Data in each partition may be subdivided further into Buckets based on the value of a hash function of some column of the Table.

For example the page_views table may be bucketed by userid, which is one of the columns, other than the partitions columns, of the page_view table. 

Advantages: 

- enable more efficient queries.
    - a join of two tables that are bucketed on the same columns, map-side join
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

**It is advisable to get Hive to perform the bucketing**, because Hive does not check that the buckets in the datafiles on disk are consistent with the buckets in the table definition. 

Populate the bucketed table:

1. Set `hive.enforce.bucketing` property to `true`.
2. `INSERT OVERWRITE TABLE bucketed_users SELECT * FROM users;`

Example of sampling:

```sql
-- sample 1/4
SELECT * FROM bucketed_users TABLESAMPLE(BUCKET 1 OUT OF 4 ON id);
```

---

### Managed Tables & External Tables

[Differences between managed tables & external tables:](http://www.aboutyun.com/thread-7458-1-1.html)

- When importing data to an external table, data is not moved under its data warehouse directory, which means data in the external table is not managed by Hive. This is different with managed table.
- Deleting:
  - When deleting managed tables, Hive will delete both metadata and data.
  - When deleting external tables, Hive only deletes metadata. Data is retained.

Which one to use?

- Not many differences in general. So, it depends on personal preference.
- Practical experience: If all processes involve Hive, create managed tables. Otherwise, use external tables.

---

### Traditional DB VS Hive

Traditional database: 

- Schema on write: data is checked against the schema when it is written into the database.
- Query time performance faster because the database can index columns and perform compression on the data.

Hive: 

- Schema on read: does not verify the data when it is loaded.
- Very fast initial load, since the data does not have to be read, parsed, and serialized to disk in the database’s internal format. 

---

### Locking

Hive supports for table- and partition-level locking using ZooKeeper.

For instance, it prevents one process from dropping a table while another is reading from it.  

By default, locks are not enabled.

---

### Indexes

Two types: 

#### Compact Indexes

- Store the HDFS block numbers of each value, rather than each file offset.
- Do not take up much disk space.

#### Bitmap Indexes

Appropriate for low-cardinality columns (such as gender or country).

---

## Architecture

![hive-architecture.png](img/hive-architecture.png)

![data-processing-model.png](img/data-processing-model.png)

---

### Metastore

- Stores metadata for each of the tables such as their schema and location.
- The metadata helps the driver to keep track of the data. 
- A backup server regularly replicates the data which can be retrieved in case of data loss.

![metastore-configurations.png](img/metastore-configurations.png)

#### Embedded Metastore Configuration

By default, the metastore service runs in the same JVM as the Hive service and contains an embedded Derby database instance backed by the local disk.

However, only one embedded Derby database can access the database files on disk at any one time,
which means you can have only one Hive session open at a time that accesses the same metastore.

#### Local Metastore Configuration

Use a standalone database to support multiple sessions and multiple users.

Metastore service still runs in the same process as the Hive service but connects to a database running in a separate process.

MySQL is a popular choice for the standalone metastore.

#### Remote Metastore Configuration

One or more metastore servers run in separate processes to the Hive service.

Better manageability and security because the database tier can be completely firewalled off, and the clients no longer need the database credentials.

---

## Limitations

- As Hadoop is intended for long sequential scans and Hive is based on Hadoop, you would expect queries to have a very high latency. (Response time: several minutes)
- Hive is **read-based** and therefore **not appropriate for transaction processing** that typically involves a high percentage of write operations.
- Not designed for OLTP. Only used for OLAP.
- Supports overwriting or apprehending data, but not updates and deletes.
- Sub queries are not supported in Hive.
- Index is less used in Hive, which is different with traditional DB.

---

## Coding

- `show databases;`
- `show tables;`
- `show create table <table_name>;`: Show statement that creates the table.
- `desc <table_name>;`: Show simple structure of the table.
- `desc formatted <table_name>;`: Show formatted detailed info about the table.
- `! <command>`: In Hive shell, execute Linux commands. For instance, `! ls`.
- Need alias when order by count. Otherwise, error "Not yet supported place for UDAF 'count'". For instance, `select count(*) as cnt, brand_id from user_log where action='2' group by brand_id order by cnt desc;`.

