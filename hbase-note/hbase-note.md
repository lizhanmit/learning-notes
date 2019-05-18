# HBase Note

HBase is a NoSQL database, a distributed, scalable, **column-oriented** database running on top of HDFS, which is modeled after Google's BigTable.

Use HBase when you need random, real-time read/write access to your Big Data. 

HBase is used to host very large tables - billions of rows X millions of columns.

HBase is not relational. Does not support SQL.

## Basics

### Data Model

![hbase-data-model.png](img/hbase-data-model.png)

#### Cells

The timestamp is auto-assigned by HBase at the time of cell insertion.

A cell’s content is an uninterpreted array of bytes.

#### Rows

Rows are sorted by row key. The sort is byte-ordered.

Row keys are byte arrays, so anything can serve as a row key.

Row updates are atomic.

#### Columns

Format: `<column_family>:<qualifier>`

Column families must be specified up front as part of the table schema definition, but new column family members (qualifiers) can be added on demand.

All column family members are stored together on the filesystem.

Tuning and storage specifications are done at the column family level.

#### HBase VS RDBMS

For HBase,

- Cells are versioned. 
- Rows are sorted. 
- Columns can be added on the fly by the client as long as the column family
they belong to preexists.

#### Regions

Tables are automatically partitioned horizontally by HBase into regions.

Initially, a table comprises a single region, but as the region grows it eventually crosses a configurable size threshold, at which point it splits at a row boundary into two new regions of approximately equal size. 

Regions are the units that get distributed over an HBase cluster. Each node hosts a subset of the table’s total regions.

### Column- VS Row-Oriented

Column-oriented: analytical application, high data compression ratio

Row-oriented: more transactional operations, low data compression ratio 

---

## Architecture

![hbase-architecture.png](img/hbase-architecture.png)

![hbase-architecture-2.png](img/hbase-architecture-2.png)


- Compression
- In-memory operations:
  - MemStore
  - BlockCache
- Pursues efficiency of analysis. A great amount of data is stored redundantly.

![hbase-access-interface.png](img/hbase-access-interface.png)


### Namespace

- Namespace命名空间指对一组表的逻辑分组，类似RDBMS中的database，方便对表在业务上划分。
- Namespace特性是对表资源进行隔离的一种技术，隔离技术决定了HBase能否实现资源统一化管理的关键，提高了整体的安全性。

---

## Coding

- `list`: List all created tables in the current DB.
- `create '<table_name>', '<column_family_name>'`
- `describe '<table_name>'`: Check basic info of the table.
- `scan '<table_name>'`: Check all data of a table.
- `put '<table_name>', '<row_key_value>', '<column_family_name>:<column_name>', '<cell_value>'`: Insert data.
- `get '<table_name>', '<row_key_value>'`: Check data of a row.
- `get '<table_name>', '<row_key_value>', '<column_family_name>:<column_name>`: Check data of a cell.
- `truncate '<table_name>'`: Delete data in the table.
- Delete a table: `disable '<table_name>'` and then `drop '<table_name>'`

Take "student" table as an example:

| id | name     | gender | age |
| :--| :--------| :------| :---|
| 1  | Zhangsan | F      | 23  |
| 2  | Lisi     | M      | 24  |

1. `create 'student', 'info'`
2. `put 'student', '1', 'info:name', 'Zhangsan'`
3. `put 'student', '1', 'info:gender', 'F'`
4. `put 'student', '1', 'info:age', '23'`
5. `put 'student', '2', 'info:name', 'Lisi'`
6. `put 'student', '2', 'info:gender', 'M'`
7. `put 'student', '2', 'info:age', '24'`
