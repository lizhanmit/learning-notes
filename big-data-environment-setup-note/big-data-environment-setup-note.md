# Big Data Environment Setup Note

## SSH & FTP

A SSH client (e.g. PuTTY) is necessary to connect to the cloud environment.

- PuTTY command line is cloud environment command line.   

A FTP tool (e.g. WinSCP, FileZilla) is necessary to transfer files.

[PuTTY and WinSCP setup tutorial](https://courses.cognitiveclass.ai/asset-v1:BigDataUniversity+BD0111EN+2016+type@asset+block/01_Lab_Setup_4.0.0_Cloud_.pdf)

---

## Web UI Address

- Hadoop: `localhost:50070` 
- Yarn: `8088` 
- HBase: `16010` 

---

## CLI Commands

### Linux

#### Check Version

- Flume: `flume-ng version` 
- Python: `python3 --version` 
- Hadoop: `hadoop version`  
- Spark: `spark-shell –version` 
- Kafka: go to kafka/libs installation directory to check. 
- Maven: `mvn –v` 
- Hbase: go into hbase shell, `version` 

#### Start & Stop

- HBase: (start Zookeeper first)
  - `start-hbase.sh`
  - `stop-hbase.sh`
  - login HBase shell: `hbase shell`
- Hive: (start MySQL first, then Hadoop)
  - login Hive shell: `hive`
- HDFS: 
  - `start-dfs.sh`
  - `stop-dfs.sh`
- Kafka: 
  - start ZooKeeper first (Kafka built-in ZooKeeper): `zookeeper-server-start.sh config/zookeeper.properties`
  - start Kafka: `bin/kafka-server-start.sh config/server.properties`
  - start Kafka in background: `kafka-server-start.sh -daemon config/server.properties`
- MySQL service: 
  - `service mysql start`
  - login: `mysql -u <username> -p <password>` (username is root by default)
- Spark: 
  - start Spark shell: `spark-shell`
  - quit Spark shell: `:quit`
  - If Spark needs connect MySQL, you need to specify `--jars` and `--driver-class-path`: `spark-shell --jars /usr/local/spark/jars/mysql-connector-java-5.1.40/mysql-connector-java-5.1.40-bin.jar --driver-class-path /usr/local/spark/jars/mysql-connector-java-5.1.40/mysql-connector-java-5.1.40-bin.jar`
- Storm: (start ZooKeeper first)
  - start Storm nimbus: `storm nimbus`
  - start Storm supervisor: `storm supervisor`
- Yarn:
  - `start-yarn.sh`
  - `stop-yarn.sh`
- ZooKeeper: 
  - `zkServer.sh start`
  - `zkServer.sh stop`

---

### Windows

Display processes using port 8080: `netstat -ano | findstr 8080`

Display detailed tasks using port 8080: `tasklist | findstr 8080`

Kill task with pid 8080: `taskkill /F /PID 8080`

#### Start & Stop

- Scala: 
  - `sbt console`

---

### Mac OS

Set global environment variables under Mac OS: 

1. In terminal, `open ~/.bash_profile`.  
2. In TextEdit, add e.g. `export PATH="/Users/zhanli/spark/bin:$PATH"`. Save.  
3. In terminal, `source ~/.bash_profile`. 