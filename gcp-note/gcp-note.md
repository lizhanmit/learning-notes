# GCP Note

- [GCP Note](#gcp-note)
  - [Regions and Zones](#regions-and-zones)
  - [GCP Resource Hierarchy](#gcp-resource-hierarchy)
  - [Compute Engine](#compute-engine)
  - [Cloud Shell](#cloud-shell)
    - [`gcloud`](#gcloud)
      - [Coding](#coding)
  - [GCP Storage](#gcp-storage)
    - [Cloud Storage](#cloud-storage)
      - [Four Classes](#four-classes)
    - [Cloud SQL](#cloud-sql)
    - [Cloud Spanner](#cloud-spanner)
    - [Big Table](#big-table)
    - [Cloud Data Store](#cloud-data-store)
    - [Comparison](#comparison)
  - [Kubernetes](#kubernetes)
    - [Containers](#containers)
    - [Kubernetes Pod](#kubernetes-pod)
    - [Kubernetes Cluster](#kubernetes-cluster)
    - [Architecture](#architecture)
  - [App Engine](#app-engine)
    - [Standard Environment & Flexible Environment](#standard-environment--flexible-environment)
  - [API Management](#api-management)
    - [Cloud Endpoints](#cloud-endpoints)
    - [Apigee Edge](#apigee-edge)
  - [Cloud Functions](#cloud-functions)
  - [Deployment Manager](#deployment-manager)
  - [Monitoring](#monitoring)
    - [Stackdriver](#stackdriver)
  - [Cloud Big Data Platform](#cloud-big-data-platform)
    - [Cloud Dataproc](#cloud-dataproc)
    - [Cloud Dataflow](#cloud-dataflow)
    - [BigQuery](#bigquery)
    - [Cloud Pub/Sub](#cloud-pubsub)
    - [Cloud Datalab](#cloud-datalab)
  - [Cloud Machine Learning Platform](#cloud-machine-learning-platform)
  - [Data Studio](#data-studio)
  - [BigQuery](#bigquery-1)
    - [Coding](#coding-1)
      - [In Cloud Shell](#in-cloud-shell)

---

[What is Google Cloud Platform (GCP)? – Introduction to GCP Services & GCP Account](https://www.edureka.co/blog/what-is-google-cloud-platform/)

[Apache Spark Tutorial with GCP - YouTube](https://www.youtube.com/playlist?list=PLlL9SaZVnVgi_OQf3uLjJKNVivBLLfavf)

---

## Regions and Zones

A region is a specific geographical location where you can run your resources. Each region has one or more zones. 

![regions-and-zones.png](img/regions-and-zones.png)

Resources that live in a zone are referred to as zonal resources. 

Virtual machine Instances and persistent disks live in a zone. To attach a persistent disk to a virtual machine instance, both resources must be in the same zone. 

Similarly, if you want to assign a static IP address to an instance, the instance must be in the same region as the static IP.

---

## GCP Resource Hierarchy

![gcp-resource-hierarchy.png](img/gcp-resource-hierarchy.png)

Each resource belongs to exactly one project.

A policy is set on a resource, which contains a set of roles and role members. 

Resources inherit policies from parent. 

A less restrictive parent policy overrides a more restrictive resource policy. For example, parent policy has Right A and B, whereas child policy has Right A. Then parent policy take effect. But if parent policy has Right A whereas child policy has Right A and B, child policy take effect.

Organization nodes are optional, but if you want to create folders, having one is mandatory.

---

## Compute Engine 

Google Compute Engine (GCE)

Certain Compute Engine resources live in regions or zones.

- offers managed VMs
- per second billing
- Use big VMs for memory- and compute-intensive applications.
- Use Autoscaling (a feature) for resilient, scalable applications.

Storage type:

- local SSD: Use this when your application needs high performance scratch space. But does not last past when VM terminates. 
- standard persistent disks: by default

You can create a VM instance in Compute Engine through gcloud command line tool or Cloud Console. 

---

## Cloud Shell

Cloud Shell is a Debian-based virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

### `gcloud`

`gcloud` is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

`sa_107021519685252337470@gcelab2`: 

- The reference before the `@` indicates the account being used.
- After the `@` sign indicates the host machine being accessed.


#### Coding

List the active account name: 

```
gcloud auth list
```

List the project ID:

```
gcloud config list project
```

List of configurations in your environment:

```
gcloud config list
```

See all properties and their settings:

```
gcloud config list --all
```

List your components:

```
gcloud components list
```

List the compute instance available in the project:

```
gcloud compute instances list
```

List the gcelab2 virtual machine:

```
gcloud compute instances list --filter="name=('gcelab2')"
```

List the Firewall rules in the project:

```
gcloud compute firewall-rules list
```

List the Firewall rules for the default network:

```
gcloud compute firewall-rules list --filter="network='default'"
```

List the Firewall rules for the default network where the allow rule matches an ICMP rule:

```
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"
```

To connect to your VM "gcelab2" with SSH:

```
gcloud compute ssh gcelab2 --zone $ZONE
```

View the available logs on the system:

```
gcloud logging logs list 
```

Read the logs related to the resource type of "gce_instance":

```
gcloud logging read "resource.type=gce_instance" --limit 5
```

Read the logs for a specific virtual machine:

```
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```

---

## GCP Storage

### Cloud Storage

- object storage
- different from file storage, block storage
- Each object has an unique key in form of URL.
- The objects are immutable. (versioning) (You can turn on / off versioning.)
- Cloud Storage files are organized into buckets.

#### Four Classes

![cloud-storage-classes.png](img/cloud-storage-classes.png)

### Cloud SQL

- RDBMS
- TB of capacity

### Cloud Spanner

- horizontally scalable RDBMS
- PB of capacity
- offers transactional consistency at global scale

### Big Table

- NoSQL
- billions of rows X thousands of columns, PB data
- ideal for storing large amounts of data with very low latency
- high throughput of read and write
- access using HBase API

### Cloud Data Store

- horizontally scalable NoSQL DB
- supports transactions
- can be used for online app backend

### Comparison

![comparing-storage-options-technical-details.png](img/comparing-storage-options-technical-details.png)

---

## Kubernetes

IaaS

### Containers

![containers.png](img/containers.png)

Containers start much faster than virtual machines and use fewer resources, because each container does not have its own instance of the operating system.

### Kubernetes Pod

Kubernetes pod: smallest unit, a group of containers.

Containers in a pod are deployed together. They are started, stopped, and replicated as a group.

### Kubernetes Cluster

Kubernetes cluster: a group of machines where Kubernetes can schedule containers in pods. 

Kubernetes cluster nodes are Compute Engine VMs.

The resources used to build Kubernetes Engine clusters come from Compute Engine. 

### Architecture

![kubernetes-engine.png](img/kubernetes-engine.png)

---

## App Engine

PaaS

App Engine offers NoSQL databases, in-memory caching, load balancing, health checks, logging, and user authentication to applications running in it.

### Standard Environment & Flexible Environment

Specific versions of Java, Python, PHP, and Go are supported. 

If you use other languages, choose Flexible Env and then upload your own runtime to run code.

Sandbox constraints: 

- No writing to local files.
- All requests time out at 60s.
- Cannot install arbitrary third-party software.

If you do not want these constraints, choose Flexible Env.

![example-app-engine-standard-workflow.png](img/example-app-engine-standard-workflow.png)

![app-engine-standard-env-vs-flexible-env.png](img/app-engine-standard-env-vs-flexible-env.png)

---

## API Management

### Cloud Endpoints

It is used to create and maintain APIs.

### Apigee Edge

It is used to secure and monetize APIs.

---

## Cloud Functions

Single-purpose functions that respond to events without a server or runtime.

Written in JS running in Node.js env.

Once you put event-driven components of application in Cloud Functions, it will handle scaling them seamlessly, so you do not have to provision computer resources to handle these operations.

---

## Deployment Manager

Create a .yaml template describing your env and use Deployment Manager to create resources.

infrastructure as code

--- 

## Monitoring 

### Stackdriver 

- monitoring 
- logging
- trace
- error reporting 
- debugger
- profiler

---

## Cloud Big Data Platform

### Cloud Dataproc

- provides Spark, Hive, Pig
- billed on second

### Cloud Dataflow

- offers data pipelines
- real-time data
- ETL
- batch & streaming processing 

### BigQuery 

- fully managed data warehouse
- PB-scale
- write 100,000 rows per second
- 99.9% SLA
- separates storage and computation

### Cloud Pub/Sub

- supports many-to-many asynchronous messaging
- push (subscribers get notified when new messages arrive) / pull (check for new messages at intervals) subscriptions 
- "at least once" delivery at low latency, which means small chance some messages might be delivered more than once
- one million messages per second and beyond
- foundation for Dataflow streaming

### Cloud Datalab

- notebook (Python env)
- interactive tool for data exploration
- built on Jupyter
- runs in a Compute Engine VM

---

## Cloud Machine Learning Platform

- TensorFlow, TPU
- full managed Machine Learning service
- Machine Learning APIs
  - Cloud Vision API
  - Cloud Speech API
  - Cloud Natural Language API
  - Cloud Translate API
  - Cloud Video Intelligence API# GCP Note

---

## Data Studio

Data Studio is a free, modern business intelligence product that lets you create dynamic, visually compelling reports and dashboards. 

---

## BigQuery 

In BigQuery, projects contain datasets, and datasets contain tables.

A dataset is a group of resources, such as tables and views.

A dataset name can be up to 1,024 characters long, and consist of A-Z, a-z, 0-9, and the underscore, but it cannot start with a number or underscore, or have spaces.

### Coding 

#### In Cloud Shell

Describe a table with project name: 

```
bq show <project:public dataset.table>
```

e.g. 

```
bq show bigquery-public-data:samples.shakespeare
```

Describe a table without project name: 

```
bq show <dataset_name.table_name>
```
 
e.g. 

```
bq show babynames.names2010
```

Run a query: 

```
bq query "[SQL_STATEMENT]"
```

Escape any quotation marks inside the `[SQL_STATEMENT]` with a `\` mark, or use `'[SQL_STATEMENT]'` instead of `"`. For instance, 

```sql
bq query --use_legacy_sql=false \
'SELECT
   word,
   SUM(word_count) AS count
 FROM
   `bigquery-public-data`.samples.shakespeare
 WHERE
   word LIKE "%raisin%"
 GROUP BY
   word'
```

`--use_legacy_sql=false` makes standard SQL the default query syntax.

List any existing datasets in your project: 

```
bq ls
``` 

List any existing datasets in a specific project: 

```
bq ls <project_name>:
```

e.g. 

```
bq ls bigquery-public-data:
```

Create a new dataset: 

```
bq mk <dataset_name>
```

e.g. 

```
bq mk babynames
```

Add the a zip file to your project: 

```
curl -LO <file URL>
```

e.g. 

```
curl -LO http://www.ssa.gov/OACT/babynames/names.zip
```

Unzip a file: 

```
unzip <zip_file_name>
```

e.g. 

```
unzip names.zip
```

Create or update a table and load data: 

```
bq load <dataset_name.table_name source>
<column_name:column_type>
```

For instance, 

```
bq load babynames.names2010 yob2010.txt name:string,gender:string,count:integer
```

List tables or views in your dataset: 

```
bq ls <dataset_name>
```

e.g. 

```
bq ls babynames
```

Delete all tables in a dataset: 

```
bq rm -r <dataset_name>
```

e.g.

```
bq rm -r babynames
```
