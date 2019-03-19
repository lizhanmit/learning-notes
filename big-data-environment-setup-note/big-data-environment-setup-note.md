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

### Under Linux

Check version:  

- Flume: `flume-ng version` 
- Python: `python3 --version` 
- Hadoop: `hadoop version`  
- Spark: `spark-shell –version` 
- Kafka: go to kafka/libs installation directory to check. 
- Maven: `mvn –v` 
- Hbase: go into hbase shell, `version` 

Start & Stop:

- HDFS: 
  - `start-dfs.sh`
  - `stop-dfs.sh`
- Yarn:
  - `start-yarn.sh`
  - `stop-yarn.sh`
- HBase: (start Zookeeper first)
  - `start-hbase.sh`
  - `stop-hbase.sh`
  - login HBase shell: `hbase shell`
- Hive: (start MySQL first, then Hadoop)
  - login Hive shell: `hive`
- MySQL service: 
  - `service mysql start`
  - login: `mysql -u <username> -p <password>` (username is root by default)

---

## Under Windows