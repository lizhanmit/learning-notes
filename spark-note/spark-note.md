# Spark Note

Apache Spark is a fast and general-purpose distributed / cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including Spark SQL for SQL and structured data processing, MLlib for machine learning, GraphX for graph processing, and Spark Streaming.

## Spark Core

### Architecture  

A Job includes multiple RDDs.

A RDD can be broken into multiple Stages.

A Stage gets broken into multiple Tasks that are distributed to nodes on the cluster and then executed.

![spark-workflow.png](img/spark-workflow.png)

![spark-architecture.png](img/spark-architecture.png)

![img/spark-hdfs-architecture.png](img/spark-hdfs-architecture.png)

![spark-architecture-2.png](img/spark-architecture-2.png)

There is a **BlockManager** storage module in **Executor**, which uses both RAM and disk as storage devices in order to reduce IO overhead effectively.

When executing an application, Driver will apply resources from Cluster Manager, start up Executors, send program code and files to Executors, and then execute Tasks on Executors.

![spark-architecture-3.png](img/spark-architecture-3.png)

---

### Cluster Resource Manager

- Standalone
- Yarn
- Mesos
- Kubernetes

Spark built-in cluster resource manager (standalone) is not easy to use. It is more limited than other cluster managers. The cluster can only run Spark. **DO NOT** use it generally.

Mesos integrates with Spark better than Yarn.

You can use Mesos and Yarn at the same time. Mesos is for **coarse-grained** management allocating resource to Docker, and then Yarn is for **fine-grained** management.

---

### Deployment of Spark Applications

[Spark Modes of Deployment – Cluster Mode and Client Mode](https://techvidvan.com/tutorials/spark-modes-of-deployment/)

---

### Life Cycle of a Spark Application

#### Outside Spark (cluster mode)

1. Client Request
    1. You submit an application executing code on your local machine through `spark-submit`.
    2. Make a request to the cluster manager driver node explicitly asking for resources for the Spark driver process only.
    3. Cluster manager places the driver process onto a node in the cluster.
    4. The client process exits and application is off and running on the cluster.
2. Launch
    1. The driver process runs user code on the cluster including a SparkSession that initializes a Spark cluster.
    2. The SparkSession communicates with the cluster manager asking it to
launch Spark executor processes across the cluster.
    3. The cluster manager responds by launching the executor processes and sends the relevant information about their locations to the driver process.
3. Execution
    1. The driver and the workers communicate among themselves, executing code and moving data around.
4. Completion
    1. The driver process exits with either success or failure.
    2. The cluster manager then shuts down the executors.
    3. You can see the success or failure of the Spark Application by asking the cluster manager.
 
#### Inside Spark 

1. Create a SparkSession (the first step of any Spark Application).
2. Execute code.

1 data block - 1 partition - 1 Spark task - runs on 1 executor

---

### Optimization

#### Spark SQL Optimization

![spark-sql-optimization.jpg](img/spark-sql-optimization.jpg)

#### Pipelining

- Spark performs as many steps as it can at one point in time before writing data to memory or disk.
- It occurs at and below the RDD level.
- With pipelining, any sequence of operations that feed data directly into each other, without needing to move it across nodes, is collapsed into a single stage of tasks that do all the operations together.

---

### RDDs

- A RDD (Resilient Distributed Dataset) is essentially a **read-only** immutable, partitioned collection of records.
- Each record is just a Scala, Java or Python object.
- Each RDD can be divided into multiple partitions. Each partition is a dataset fragment. Each partition can be stored on different nodes in the cluster. (parallel computing)
- Virtually everything in Spark is built on top of RDDS.
- RDD API is similar to the Dataset, except that RDDs are not stored in, or manipulated with, the structured data engine.
- One thing that you might use RDDs for is to parallelize raw data that you have stored in memory on the driver machine. For instance, `spark.sparkContext.parallelize(Seq(1,2,3)).toDF()`.
- Read a text file line by line: `spark.sparkContext.textFile("<directory>")`. Each line corresponds to each record in a RDD. 
- Read text files, each text file becomes a record: `spark.sparkContext.wholeTextFiles("<directory>")`.
    - The 1st object is the name of the file.
    - The 2nd string object is the value of the text file.

There are lots of subclasses of RDD. You will likely only create two types: 

- generic RDD type
- key-value RDD 

When to use RDDs: when you need fine-grained control over the physical distribution of data (custom partitioning of data).

#### Printing Elements of an RDD

It is common to print out the elements of an RDD using `<rdd_var>.foreach(println)` or `<rdd_var>.map(println)`.

- On a single machine, OK.
- In cluster mode, the methods called by executors will print output on executors rather than the driver.

To print all elements on the driver, you may use collect all RDDs to the driver firstly and then print: `<rdd_var>.collect().foreach(println)`.

- This will cause the driver to run out of memory.
- Thus, only print a few elements for testing: `<rdd_var>.take(<number_of_elements>).foreach(println)`.

#### RDD Running Process

![rdd-running-process.png](img/rdd-running-process.png)

![rdd-running-process-2.png](img/rdd-running-process-2.png)

![rdd-running-process-3.png](img/rdd-running-process-3.png)

![spark-transformations-dag.png](img/spark-transformations-dag.png)


#### Stage Division & Dependencies

- Narrow dependencies: The relationship between RDDs is 1 : 1 or many : 1.
- Wide dependencies: The relationship between RDDs is 1 : many or many : many.

Create a new stage when it comes across wide dependencies.

![stage-division-according-to-dependencies.png](img/stage-division-according-to-dependencies.png)

Optimize for dependencies:  

- Do as many narrow dependencies together before hitting a wide dependency.  
- Try to group wide dependency transformations together, possibly inside a single function and do it once. 

---

### Partitioning

If you often access a column when reading data, you can partition by it when writing the data for optimization. 

As you do this, you encode this column as a folder. When you read data later, it allows you to skip lots of data and only read in relevant data instead of having to scan the complete dataset. E.g. `<dataFrameName>.write.format("parquet").partitionBy("<columnName>").save("<filePath>")`.

It is supported for all file-based data sources.

If there is no partitioner, the partitioning is not based upon characteristic of data but distribution is random and uniformed across nodes.

```scala
// default partitioner
rdd.partitionBy(<numberOfPartitions>)
// or HashPartitioner
rdd.partitionBy(new HashPartitioner(<numberOfPartitions>))
// RangePartitioner
rdd.partitionBy(new RangePartitioner(<numberOfPartitions>, rdd))
```

Get partition number of a Dataset or DataFrame: `<dataset_or_dataFrame>.rdd.partitions.size`.

Get partition number of a RDD: 

- `<rdd>.partitions.size`
- `<rdd>.getNumPartitions`

#### Downsides

If you partition at too fine a granularity, it can result in many small files, and a great deal of overhead trying to list all the files in the storage system.

#### Partitioning Rule

- The number of partitions should be equal to the number or its integer multiple (2~3) of CPU cores in the cluster as possible.
- Each partition is generally between 100 - 200 MB.

#### Default Partition Number

Configure default partition number through set the parameter `spark.default.parallelism`.

- Local model: Number of CPU of local machine. If `local[N]` has been set, default partition number is N.
- Mesos: 8.
- Standalone or Yarn: Max value between number of CPU cores in the cluster and 2.

#### Manually Configure Partition

RDD partitioning is automatic but can be done manually through programming.

- When creating a RDD using `textFile()` or `parallelize()` methods, specify it. For example, `sc.textFile(<path>, <partitionNum>)`.
  - By default, Spark creates one partition for each block of the file (blocks being 128MB by default in HDFS).
  - You cannot have fewer partitions than blocks.
- When getting a new RDD by transformation, invoke `repartition(<partitionNum>)` method. For example, `rdd2 = rdd1.repartition(4)`. 4 partitions.

---

### Partitioner

#### HashPartitioner

Default partitioner of Spark.

HashPartitioner works on Java’s `Object.hashcode()`. Objects which are equal should have the same hashcode. So, HashPartitioner will divide the keys that have the same hashcode(). 

`partitionNo = hashCode % numberOfPartitions`

Work for discrete values.

#### RangePartitioner 

RangePartitioner will sort the records in almost equal ranges based on the key and then it will divide the records into a number of partitions based on the given value. The ranges are determined by sampling the content of the RDD passed in.

Work for continuous values.

#### CustomPartitioner

You can also customize the number of partitions you need and what should be stored in those partitions by extending the default Partitioner class in Spark.

Specifying your own custom Partitioner can give you significant performance and stability improvements if you use it correctly. You control exactly how data is laid out on the cluster and manipulate that individual partition accordingly.

Do this **only** when you have lots of domain knowledge about your problem space. 

Steps:

1. Drop down to RDDs from the Structured APIs.
2. Apply your custom partitioner.
3. Convert it back to a DataFrame or Dataset.

Used to deal with data skew (some keys have many, many more values than other keys):

- break these keys
- improve parallelism
- prevent OutOfMemoryErrors

---

### Bucketing

- Bucketing create a certain number of files and organize your data into those "buckets".
- Data with the same bucket ID will all be grouped together into one physical partition.
- The data is pre-partitioned according to how you expect to use that data later on, which avoids shuffles later when you go to read the data.
- E.g. `<dataFrameName>.write.format("parquet")
.bucketBy(<numberBuckets>, "<columnNameToBucketBy>").saveAsTable("<bucketedFilesName>")`.
- It is supported only for Spark-managed tables.

---

### Coalesce VS Repartition

#### Coalesce

- Coalesce is fast in certain situations because it minimizes data movement.
- No shuffle. Merge partitions on the same node into one partition.
- Coalesce changes the number of nodes by moving data from some partitions to existing partitions. This algorithm obviously **cannot increase** the number of partitions.
  - For example, `numbersDf.rdd.partitions.size` is 4. Even if you use `numbersDf.coalesce(6)`, it is still 4 rather than 6.
- Avoids a full data shuffle.

#### Repartition

- Repartition can be used to either increase or decrease the number of partitions in a DataFrame.
- Does a full data shuffle.
- If there is only a little amount of data but the number of partitions is big, there will be many empty partitions.
- You should only repartition when the future number of partitions is greater than the current number of partitions or when you are looking to partition by a set of columns.
- If you are going to be filtering by a certain column often, it can be worth repartitioning based on that column. E.g. `df.repartition(col("<columnName>"))`, or `df.repartition(<numberOfPartitions>, col("<columnName>"))`.

#### Real World Example

1. Suppose you have a data lake that contains 2 billion rows of data (1TB) split in 13,000 partitions.
2. You want to create a data puddle containing 2,000 rows of data for the purpose of development by random sampling of one millionth of the data lake.
3. Write the data puddle out to S3 for easy access.

```scala
val dataPuddle = dataLake.sample(true, 0.000001)
dataPuddle.write.parquet("s3a://my_bucket/puddle/")
```

4. Spark does not adjust the number of partitions, so the dataPuddle will also have 13,000 partitions, which means a lot of the partitions will be empty.
5. Not efficient to read or write thousands of empty text files to S3. Improve by repartitioning.

```scala
val dataPuddle = dataLake.sample(true, 0.000001)
val goodPuddle = dataPuddle.repartition(4)
goodPuddle.write.parquet("s3a://my_bucket/puddle/")
```

6. **Why choosing 4 partitions here?** 13,000 partitions / 1,000,000 = 1 partition. Then `number_of_partitions = number_of_cpus_in_cluster * 4` (2, 3 or 4).
7. **Why using repartition instead of coalesce here?** The data puddle is small. The repartition method returns equal sized text files, which are more efficient for downstream consumers.

- **When filtering large DataFrames into smaller ones, you should almost always repartition the data.**
- **If you are reducing the number of overall partitions, first try `coalesce`.**

---

### Shared Variables

#### Broadcast Variables

When you use a variable in a closure, it must be deserialized on the worker nodes many times (one per task).

Broadcast variables let you save a large **immutable** value (such as a lookup table or a machine learning model) cached on all the worker nodes instead of serialized with every single task and reuse it across many Spark actions without re-sending it to the cluster. 

- Use `SparkContext.broadcast(<var>)` to create a broadcast variable from a normal variable, which is cached on each machine.
- The broadcast variable is like a wrapper of its corresponding normal variable.
- The broadcasted data is cached in serialized form and deserialized before running each task.
- All functions in the cluster can access the broadcast variable, thereby you do not need to repeatedly send the original normal variable to all nodes.
- After the broadcast variable is created, it should be used instead of the original normal variable in any functions run on the cluster.
And the original normal variable cannot be modified (is **read-only**). Consequently, the broadcast variable on all nodes are the same.
- **When to use**: when a very large variable need to be used repeatedly.
- Use case: pass around a large lookup table that fits in memory on the executors and use that in a
function.

For instance,

1. Create: `val broadcastVar = spark.sparkContext.broadcast(Array(1,2,3))`
2. Get value: `broadcastVar.value`

#### Accumulators

Accumulators are a way of updating a value inside of a variety of transformations and propagating that value to the driver node in an efficient and fault-tolerant way.

Accumulators let you add together data from all the tasks into a shared result. 

- Use case: counters or sum functions.
- Accumulators are available on worker nodes, but **worker nodes cannot read them**.
- **Driver node is the only one that can read and compute the aggregate of all updates.** 
- Spark natively supports accumulators of numeric types, and programmers can add support for new types through extending class `AccumulatorV2`.
- Named accumulators will display their running
results in the Spark UI, whereas unnamed ones will not.
- Create: 

```scala
/*
 * unnamed accumulators
 */
val <accumulatorVar> = new LongAccumulator spark.sparkContext.register(<accumulatorVar>)

/*
 * named accumulators
 */
val <accumulatorVar> = new LongAccumulator
spark.sparkContext.register(<accumulatorVar>, "<accumulatorName>")
// or
val <accumulatorVar> = spark.SparkContext.longAccumulator("<accumulatorName>")
```

- Use: `<accum_var>.add(<number>)`
- Get value:`<accum_var>.value`.

For instance,

1. Create: `val nErrors = sc.accumulator(0.0)`
2. Load file: `val logs = sc.textFile("/Users/akuntamukkala/temp/output.log")`
3. Count number of "error": `logs.filter(_.contains(“error”)).foreach(x => nErrors += 1)`
4. Get value: `nErrors.value`

##### Accumulator Update

- For action operations, accumulator updates in each task will only be applied once even if the task restarts.
- For transformation operation, each task’s update may be applied more than once if tasks or job stages are re-executed.
- Accumulator updates are not guaranteed to be executed when made within a lazy transformation because of lazy evaluation.

---

### Caching / Persisting

- By default, each transformed RDD may be recomputed each time you run an action on it.
- Cache or persist datasets in memory on nodes.
- Future actions are faster (often by more than 10x).
- `<rdd_var>.cache()` for default storage level - `MEMORY_ONLY`.
- Or `<rdd_var>.persist()` with a specified StorageLevel parameter.
- Spark removes cached data automatically in a least-recently-used (LRU) fashion.
- Or use `<rdd_var>.unpersist()` if you want manually.

When to cache data:

- When doing data validation and cleansing.
- When querying a small “hot” dataset.
- Cache for iterative algorithm like PageRank.
- Generally, **DO NOT** use for input data as input data is too large.

#### Storage Level

- `MEMORY_ONLY` is the 1st choice.
- `MEMORY_ONLY_SER` is the 2nd choice. (Java and Scala)
- **DO NOT** use disk unless computing datasets are expensive, or amount of the data is large. Recomputing a partition may be as fast as reading it from disk.

---

### Checkpointing 

Saving an RDD to disk. 

Future references to this RDD point to those intermediate partitions on disk rather than recomputing the RDD from its original source.

Not available in DataFrame API. 

```scala
spark.sparkContext.setCheckpointDir("/some/path/for/checkpointing")
<rdd>.checkpoint()
```

---

### Shuffle

A shuffle represents a physical repartitioning of the data.

- Shuffle moves data across worker nodes, which is costly.
- It involves disk I/O, data serialization, and network I/O.
- It generates a large number of intermediate files on disk. (shuffle persistence) 
    - Spark has the "source" tasks (those sending data) write shuffle files to their local disks. 
    - Running a new job over data that has already
been shuffled does not rerun the "source" side of the shuffle.
- By default, when we perform a shuffle, Spark outputs 200 shuffle partitions. You can specify it through `spark.conf.set("spark.sql.shuffle.partitions", "<number_of_shuffle_you_want>")`.
- Use minimal shuffle as possible and do them in late stages for better performance.  

No shuffle transformations:  

- map
- filter  
- flatMap
- mapPartitions  
- ...

Shuffle transformations:  

- distinct  
- groupByKey  
- reduceByKey
- join  
- countByValue
- ...

---

### UDFs

Spark will serialize the UDF on the driver and transfer it over the network to all executor processes.

If the function is written in Scala or Java,

- little performance penalty. 
- performance issues if you create or use a lot of objects.

If the function is written in Python,

1. Spark starts a Python process on the worker, serializes all of the data to a format that Python can understand.
2. Executes the function row by row on that data in the Python process.
3. Returns the results of the row operations to the JVM and Spark.

Starting this Python process and serializing the data to Python are expensive. **Recommend writing UDFs in Scala or Java.**

**Best practice**: define the return type for the UDF when you define it.

Steps:

1. Define a function.
2. Convert the function into a UDF.
3. Apply the UDF to DataFrame or Dataset.

---

### Lazy Evaluation

Lazy evaulation means that Spark will wait until the very last moment to execute the graph of
computation instructions.

Taking advantage of lazy evaluation:  

- Do as many transformations as possible before hitting an action.  
- Avoid debugging statements with shuffle, e.g. printing counts.  

---

### API

![transformation-api.png](img/transformation-api.png)

![action-api.png](img/action-api.png)

The difference between `foreach()` and `map()`:

- `foreach()`: return void or no return value.
- `map()`: return dataset object.

`map()`: row based

`mapPartitions()`: partition based

`mapPartitionsWithIndex()`: The partition index is the partition number in the RDD.

`glom()`: Take every partition in the dataset and convert each partition to an array. Collect the data to the driver.

**DO NOT** use `groupByKey() + reduce()` if you can use `reduceByKey()`.

- `groupByKey()` does not receive functions as parameter. When invoking it, Spark will move all key-value pairs, which result in big overhead and transmission delay. And each executor must hold all values for a given key in memory before applying the function to them. If you have massive key skew, you will get OutOfMemoryErrors.
- But if you have consistent value sizes for each key and know that they will fit in the memory of a given executor, you are going to be just fine.

![groupByKey.png](img/groupByKey.png)

![reduceByKey.png](img/reduceByKey.png)

Very rare, very low-level aggregation methods: 

- aggregate
- treeAggregate
- aggregateByKey
- combineByKey
- foldByKey

Used to combine RDDs:

- cogroup
- join
- cartesian (**very dangerous**)
- zip

---

### Serialization

Any object that you hope to parallelize (or function) must be serializable.

The default serialization is slow. 

#### Kryo

Spark can use the Kryo library (version 2) to serialize objects more quickly.

Kryo is significantly faster and more compact than Java serialization (often as much as 10x).

Does not support all serializable types.

Need to register the classes you will use in the program in advance for best performance.

**Recommend** trying it in any network-intensive application.

Since Spark 2.0.0, we internally use Kryo serializer when shuffling RDDs with simple types, arrays of simple types, or string type.

---

### Datasets

Datasets and DataFrames are distributed table-like collections with well-defined rows and columns.

After Spark 2.0, RDDs are replaced by Datasets. The RDD interface is still supported. **The trend in Spark is to use RDDs less and Datasets more.**

Datasets are similar to RDDs but are **strongly typed** that mapped to relational schema.

Datasets can explicitly wrap a given struct or type. (Dataset[Person], Dataset[(String, Double)])

The Dataset API gives users the ability to assign a Java/Scala class to the records within a DataFrame and manipulate it as a collection of typed objects.

**Recommend** using Datasets only with user-defined encoding surgically and only where it makes sense. This might be at the beginning of a big data pipeline or at the end of one.

Think of Datasets as a blend between the higher-level Structured APIs and the low-level RDD APIs.

#### Advantages

- Datasets are more efficient.
  - They can be serialized very efficiently, even better than Kryo.
  - Optimal execution plans can be determined at compile time.
- Datasets allow for better interoperability.
  - MLLib and Spark Streaming are moving toward using Datasets instead of RDDs for their primary API.  
- Datasets simplify development.
  - You can perform most SQL operations on a dataset with one line.

Processing or transmitting over the network:

- RDD: using Java serialization or Kryo.
- Dataset: using a specialized Encoder to serialize the objects.
  - This Encoder is highly optimized and generates bytecode at run time for serialization and deserialization.

Two ways to create a Dataset:

- from Hadoop InputFormats (such as HDFS files).
- by transforming other Datasets.

RDDs can be converted to Datasets with `.toDF()`.

![when-to-use-datasets.png](img/when-to-use-datasets.png)

#### Disadvantages

- Cost of performance: Spark converts the Row format (domain specifies type) to the object you specified (a case class or Java class).
- By specifying a function, you are forcing Spark to evaluate this function on every row in your Dataset. This can be very resource intensive. For simple filters it is always **preferred** to write
SQL expressions.

---

### DataFrames 

- A DataFrame simply represents a table of data with rows and columns. 
- A DataFrame is a kind of distributed dataset on basis of RDD.
- A DataFrame is a distributed set of `Row` objects or Dataset of type Row (`Dataset[Row]`).
- Each `Row` object represents a row of record, which provides detailed schema info.
- `Row` is an untyped JVM object.
- Through DataFrame, Spark SQL is able to know column name and type of the dataset.
- When you are using DataFrames, you are taking advantage of Spark's optimized internal Catalyst format. 
- Can be created from:
  - structured data files
  - Hive tables
  - external databases
  - existing RDDs: `val myDF = spark.createDataFrame(myRDD, mySchema)`

#### RDD VS DataFrame VS Dataset

![rdd-dataframe-dataset.png](img/rdd-dataframe-dataset.png)

- DataFrame maps to a collection of Row-type objects. 
- Dataset maps to a collection of objects. 
- DataFrames schema is inferred at **runtime**; but a Dataset can be inferred at **compile time**.
  - faster detection of errors and better optimization

[A Tale of Three Apache Spark APIs: RDDs vs DataFrames and Datasets](https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)

---

### RDD -> DataFrame

![difference-between-rdd-and-dataframe.png](img/difference-between-rdd-and-dataframe.png)

Two ways to convert RDD to DataFrame:

- Use Reflection to infer the schema of the RDD that contains specific type data.
  - Firstly define a case class. Then Spark will convert it to DataFrame implicitly.
  - This way is suitable for the RDD whose data type is known.
  - More concise code.
  - Currently (Spark 2.4), Spark SQL does not support converting JavaBeans to DataFrames that contain Map field(s).
- Use programming interface to construct a schema and apply it to an existing RDD.
  - Construct Datasets when the columns and their types are not known until runtime.
  - More verbose.
  - Steps:
    1. Create an RDD of `Rows` from the original RDD.
    2. Create the schema represented by a `StructType` matching the structure of Rows in the RDD created in Step 1.
    3. Apply the schema to the RDD of `Rows` via `createDataFrame()` method provided by `SparkSession`.

---

### Structured API Execution

Steps: 

1. Write DataFrame / Dataset / SQL Code.
2. If valid code, Spark converts this to a Logical Plan.
3. Spark transforms this Logical Plan to a Physical Plan, checking for optimizations along the way through Catalyst Optimizer.
4. Spark then executes this Physical Plan (RDD manipulations) on the cluster.

![catalyst-optimizer.png](img/catalyst-optimizer.png)

#### Logical Plan

![logical-planning-process.png](img/logical-planning-process.png)

#### Physical Plan 

![physical-planning-process.png](img/physical-planning-process.png)

#### Execution

Spark performs further optimizations at runtime, generating native Java bytecode that can remove entire tasks or stages during execution.

---

### Schema

- Schema-on-read
  - Infer schema automatically.
  - Inaccurate, precision issues.
- Define schema manually through StructType and StructField
  - Use this in production especially for untyped sources like CSV and JSON files.

#### Column

Different ways to construct or refer a column: 

- `col("columnName")`
- `column("columnName")`
- `$"columnName"` (in Scala)
- `'columnName` (in Scala)
- `expr("columnName")` (the most flexible)

- Columns are just expressions.
- Columns and transformations of those columns compile to the same logical plan as parsed expressions. For example, `expr("columnName - 5")` = `expr("columnName") - 5`, because Spark compiles these to a logical tree specifying the order of operations.

#### Row

- Each row in a DataFrame is a single record.
- A record is an object of type Row.
- Row objects internally represent arrays of bytes. 

- Create a Row: `val myRow = Row("hello", null, 1, false)`
- Access a Row: 
  - `myRow(0)`: type Any
  - `myRow(0).asInstanceOf[String]`: type String
  - `myRow.getString(0)`: type String
  - `myRow.getInt(2)`: type Int

---

### Joins

Communication Strategies

- Shuffle join
  - All-to-all communication.
  - Every node talks to every other node. 
  - Expensive, especially if your data is not partitioned well.
- Broadcast join
  - Replicate the small DataFrame onto every worker node.
  - More efficient.
  - Explicitly use a broadcast join: `<df1>.join(broadcast(<df2>), <joinExpr>)`.

#### Big table–to–big table

Use shuffle join.

![join-two-big-tables.png](img/join-two-big-tables.png)

#### Big table–to–small table

Small: small enough to fit into the memory of a single worker node. 

Use broadcast join.

Steps:

1. Use broadcast variable to broadcast small table onto every node.
2. Joins will be performed on every single node individually. No further communication between nodes.

![join-big-table-and-small-table.png](img/join-big-table-and-small-table.png)

#### Small table–to–small table

It is usually best to let Spark decide how to join them.

### `joinWith()`

DatasetA `joinWith` DatasetB end up with two nested Datasets. Each column represents one Dataset. 

#### `join()` VS `joinWith()`

![join-vs-joinWith.png](img/join-vs-joinWith.png)

DataFrames can also join Datasets.

---

### Data Sources

Six core data sources:

- csv
- json
- parquet
- orc
- jdbc/odbc connections
- plain-text files

When reading and writing, Spark will use the Parquet format **by default**.

When writing, the destination directory is actually a folder with numerous files within it, which reflects the number of partitions in our DataFrame at the time we write it out.

#### JSON

**Recommend** using line-delimited JSON format, which is much more stable than the multiline.

#### Parquet

**Recommend** writing data out to Parquet for long-term storage because reading from a Parquet file will always be **more efficient than JSON or CSV**.

Advantages: 

- Storage optimizations, columnar compression.
- When reading, it allows for reading individual columns instead of entire files.
- Supports complex types (array, map, struct), which would fail with a CSV file.

**Be careful** when you write out Parquet files with different versions of Spark (especially older ones) because this can cause significant headache.

#### ORC

A self-describing, type-aware columnar file format designed for Hadoop workloads.

- optimized for large streaming reads
- finding required rows quickly

Difference between ORC and Parquet:

- Parquet is further optimized for Spark.
- ORC is further optimized for Hive.

#### Text Files

- When reading, each line in the file becomes a record in the DataFrame.
- When writing, be sure to have only one string column; otherwise, the write will fail.

#### Advanced I/O Concepts

- **Recommend** Parquet with gzip compression.
- Multiple executors cannot read from the same file at the same time.
- By default, one file is written per partition of the data.

#### Small File Problem

When you are writing lots of small files, there is a significant metadata overhead that you incur managing all of those files.

Spark and HDFS do not work well with small files.

When writing files, you can control file sizes by controlling the number of records that are written to each file: `<columnName>.write.option("maxRecordsPerFile", <numberOfRecords>)`. 

---

## Spark SQL

- Use RDD to process text file.
- Use Spark SQL to process structured data.
- Spark SQL is intended to operate as an OLAP not OLTP.

### Spark SQL CLI

- Cannot communicate with the Thrift JDBC server.
- To start Spark SQL CLI, in terminal under the Spark directory, `./bin/spark-sql`.

---

### Spark SQL Thrift JDBC/ODBC Server

![sparkSQL-shell-access.png](img/sparkSQL-shell-access.png)

---

### SparkSession

From Spark 2.0, `SparkSession` interface was introduced to realize all functions of `SQLContext` and `HiveContext`.

Using `SparkSession`, you can

- load data from different data source and transfer to DataFrame.
- transfer DataFrame to table in `SQLContext`.
- use SQL statements to operate data.

#### SparkContext

SparkContext focuses on more fine-grained control of Spark’s central abstractions.

You should not need to explicitly initialize a SparkContext through `new SparkContext`, but use the following way:

```scala
import org.apache.spark.SparkContext
val sc = SparkContext.getOrCreate()
```

**You should never need to use the SQLContext.**

#### SQLContext

SQLContext focuses on the higher-level tools like Spark SQL.

**You should rarely need to use the SparkContext.**

---

### Spark SQL & Hive

It is compulsory to add Hive support for Spark in order to access Hive using Spark.

Pre-compile version Spark from official site generally does not contain Hive support. You need to compile the source code.

Spark SQL can connect to Hive metastores. To connect to the Hive metastore, there are several properties that you will need to specify.

---

### Catalog

The highest level abstraction in Spark SQL.

It is an abstraction for the storage of metadata about the data stored in your tables, databases, tables, functions, and views.

It is a repository of all table and DataFrame information.

---

### Tables

Core difference between tables and DataFrames:

- You define DataFrames in the scope of a programming language.
- You define tables within a database.

#### Spark-Managed Tables

- When you define a table from files on disk, you are defining an **unmanaged table**.
- When you use `saveAsTable` on a DataFrame, you are creating a **Spark-managed table**.

---

### Views

- A view specifies a set of transformations on top of an existing table. 
- It basically just saves query plans, which can be convenient for organizing or reusing your query logic.
- Spark will perform it only at query time.

---

### Aggregations

[SparkSQL 中group by、grouping sets、rollup和cube方法详解](https://www.jianshu.com/p/45cf609f5a61)

---

## Spark ML

- Machine learning algorithms in MLlib require that data is represented as numerical values. 
- All machine learning algorithms in Spark take as input a Vector type, which must be a set of numerical values. 

### ML Workflow

Steps: 

1. Data preprocessing: prepare **trainDataFrame** and **testDataFrame** based on raw data without using any ML concepts.
2. Data processing: feature engineering and get **transformation pipeline**. 
3. Prepare for training.
    1. Transformation pipeline fits trainDataFrame to get **fitted pipeline**.
    2. Fitted pipeline transforms trainDataFrame to get **transformedTraining**.
4. Cache transformedTraining.
5. Prepare for ML model.
    1. Create **untrained model**.
    2. Untrained model fits transformedTraining to get **trained model**.
    3. Trained model computes cost of transformedTraining.
6. Test.
    1. Fitted pipeline transforms testDataFrame to get **transformedTest**.
    2. Trained model computes cost of transformedTest.

Estimator receives an input DataFrame via `.fit()` method, and then generate a Transformer.

- Estimator: algorithm
- Transformer: model

![ml-workflow.png](img/ml-workflow.png)

![ml-workflow-2.png](img/ml-workflow-2.png)

- Red frame: estimator
- Blue frame: transformer

1. Create a new pipeline to fit trainingData, and then generate a pipelineModel.
2. Use this model to transform testData, and then get DataFrame with prediction.

![ml-pipeline.png](img/ml-pipeline.png)

---

### Feature Transformation

Feature transformation: transformation of label and index.

`StringIndexer`

- Construction order of the index is the frequency of label.
- When encoding labels, give priority to those with higher frequency.
- The index of the label with highest frequency is 0.

---

## Metrics & Monitoring

### Monitoring API

#### Query Status

Most basic monitoring API

Answer question: What processing is my stream performing right now?

Get the status of a given query: `query.status`.

#### Recent Progress

Answer question: How fast are tuples arriving from the source?

Get access to more time-based information like the processing rate and batch durations, and input sources and output sinks: `query.recentProgress`.


When the input rate is much greater than the processing rate, it means the stream is falling behind. You need to scale the cluster up to handle the larger load.

**Best practice**: visualize the changes in batch duration, and input and processing rates instead of simply reporting changes over time.

---

### Alerting

Automatic alerting 

Building on the recent progress API to integrate existing alerting tools with Spark.

Monitoring system:

- Coda Hale Metrics library 
- Prometheus
- Splunk

---

### Advanced Monitoring

- lower-level
- more powerful 

`StreamingQueryListener` class is used to receive asynchronous updates from the streaming query and automatically output this information to other systems.

1. Develop your own object to extend
`StreamingQueryListener`.
2. Attach your custom listener with `sparkSession.streams.addListener()`.

---

## System Scaling

### Scale Kafka Connect  

- Configure `tasks.max` to number of tables/files.  
- Create different instances for different tables/files.  
- Increase frequency of polling.  
- Use Kafka Connect in cluster mode.  

### Scale Kafka Broker  

- Create multiple partitions for each topic.  
- Increase nodes in the cluster.  

### Scale Spark Streaming  

- Have a single instance of the drive program with separate threads for each stream in the driver.  
- Create separate driver instances for each stream.
- Spark cluster – add nodes.  

### Scale MySQL

- Keep table size small, thereby keeping the entire table in memory, which makes updates and querying very quick.
- Use distributed databases with a lot of nodes that are good for frequent updates – e.g. Cassandra. 

---

## Spark Ecosystem

![spark-ecosystem.png](img/spark-ecosystem.png)

![bdas-architecture.png](img/bdas-architecture.png)

![spark-ecosystem-2.png](img/spark-ecosystem-2.png)

---

## Features & Versions

- Write Ahead Logs (WAL): introduced in Spark Streaming 1.2.
- Direct approach of Spark Streaming and Kafka integration: introduced in Spark 1.3.
- `SparkSession` interface: introduced in Spark 2.0.
- Dataset: introduced in Spark 1.6.
  - After Spark 2.0, RDDs are replaced by Dataset, which is strongly-typed like an RDD. The RDD interface is still supported.
- Since Spark 2.0, Structured Streaming has supported joins (inner join and some type of outer joins) between a streaming and a static DataFrame/Dataset. 
- Watermarking: introduced in Spark 2.1. Allows the user to specify the threshold of late data, and allows the engine to accordingly clean up old state.
- Continuous Processing: introduced in Spark 2.3.
- Stream-stream joins: introduced in Spark 2.3.

---

## Glossary

- **Avro**: Avro format is great for **row oriented data**. The schema is stored in another file.
- **Parquet**: It is a very efficient format. Generally, you take .csv or .json files. Then do ETL. Then write down to parquet files for future analysis. Parquet is great for **column oriented data**. The schema is in the file.  

---

## Reasons for Using Spark

- Spark's simple, robust programming model makes it easy to apply to a large number of problems. 
- There are also a lot of packages created by Spark community. 
