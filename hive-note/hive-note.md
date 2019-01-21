# Hive Note

## Basics

- Hive is a **data warehousing** component which performs reading, writing and managing large data sets in a distributed environment using SQL-like interface.
- Hive is mostly used for data warehousing where you can perform analytics and data mining that does not require real time processing.
- Hive internally gets converted into **MapReduce** programs.
- You can couple Hive with other tools to use it in many other domains. For example,
  - Tableau along with Apache Hive can be used for Data Visualization.
  - Apache Tez integration with Hive will provide you real time processing capabilities.

### Limitations

- As Hadoop is intended for long sequential scans and Hive is based on Hadoop, you would expect queries to have a very high latency. (Response time: several minutes)
- Hive is **read-based** and therefore **not appropriate for transaction processing** that typically involves a high percentage of write operations.
- Not designed for OLTP. Only used for OLAP.
- Supports overwriting or apprehending data, but not updates and deletes.
- Sub queries are not supported in Hive.
- Index is less used in Hive, which is different with traditional DB.

### Tables & External Tables

[Differences between tables & external tables:](http://www.aboutyun.com/thread-7458-1-1.html)

- When importing data to an external table, data is not moved under its data warehouse directory, which means data in the external table is not managed by the external table itself. This is different with table.
- Deleting:
  - When deleting tables, Hive will delete both metadata and data.
  - when deleting external tables, Hive only delete metadata. Data is kept.

Which one to use?

- Not many differences in general. So, it is personal preference.
- Practical experience: If all processes involve Hive, create tables. Otherwise, use external tables.

---

## Architecture

![hive-architecture.png](img/hive-architecture.png)

![data-processing-model.png](img/data-processing-model.png)
