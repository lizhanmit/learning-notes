# ZooKeeper Note

ZooKeeper is a distributed, open-source coordination service for distributed applications. It exposes a simple set of primitives that distributed applications can build upon to implement higher level services for synchronization, configuration maintenance, and groups and naming.

ZooKeeper allows distributed processes to coordinate with each other through a shared hierarchical namespace which is organized similarly to a standard file system.

ZooKeeper data is kept in-memory.

- high throughput 
- low latency numbers
- high performance
- highly available
- strictly ordered access

---

### Replication

ZooKeeper itself is intended to be replicated over a sets of hosts called an **ensemble**.

In production, you should run ZooKeeper in replicated mode.

A replicated group of servers in the same application is called a **quorum**, and in replicated mode, all servers in the quorum have copies of the same configuration file.

For replicated mode, a **minimum of three servers** are required, and it is **strongly recommended** that you have an odd number of servers. 

---

### ZooKeeper Service

![zooKeeper-service.jpg](img/zooKeeper-service.jpg)

The servers that make up the ZooKeeper service must all know about each other.

They maintain an in-memory image of state, along with transaction logs and snapshots in a persistent store.

As long as a majority of the servers are available, the ZooKeeper service will be available.

---

### Clients

The client maintains a TCP connection through which it sends requests, gets responses, gets watch events, and sends heart beats. If the TCP connection to the server breaks, the client will connect to a different server.

---

ZooKeeper performs best (faster) where reads are more common than writes, at ratios of around 10:1, since writes involve synchronizing the state of all servers.

---

### ZooKeeper's Hierarchical Namespace

![zooKeeper's-hierarchical-namespace.jpg](img/zooKeeper's-hierarchical-namespace.jpg)

---

### Znode

ZooKeeper was designed to store coordination data: status information, configuration, location information, etc., so the data stored at each node (**znode**) is usually small, in the byte to kilobyte range.

Each time a znode's data changes, the version number increases.

The data stored at each znode in a namespace is read and written atomically. Reads get all the data bytes associated with a znode and a write replaces all the data. 

---

### Watch

Clients can set a **watch** on a znodes. A watch will be triggered and removed when the znode changes. When a watch is triggered the client receives a packet saying that the znode has changed. And if the connection between the client and one of the ZooKeeper servers is broken, the client will receive a local notification.

---

### ZooKeeper Components

![zooKeeper-components.jpg](img/zooKeeper-components.jpg)

The replicated database is an in-memory database containing the entire data tree. Updates are logged to disk for recoverability, and writes are serialized to disk before they are applied to the in-memory database.

All write requests from clients are forwarded to a single server - **leader**. The rest of the ZooKeeper servers - **followers**, receive message proposals from the leader and agree upon message delivery. The **messaging layer** takes care of replacing leaders on failures and syncing followers with leaders.

---

### Reliability

If followers fail and recover quickly, then ZooKeeper is able to sustain a high throughput despite the failure. 

ZooKeeper takes less than 200ms to elect a new leader. 

---

### Optimizations

To get low latencies on updates it is important to have a dedicated transaction log directory. By default transaction logs are put in the same directory as the data snapshots and myid file. The `dataLogDir` parameters indicates a different directory to use for the transaction logs.