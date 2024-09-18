# Introduction
## What is spark
Apache spark is an open-source, **distributed computing system** designed for big data processing. It is a framwork to process big data on a cluster.
Spark doesn't have its own file system. Spark can read/write from HDFS, GCS, S3 or Azure blob storage.

## MapReduce Vs Spark
Distributed processing started with Hadoop Map-Reduce. Limitation of Map-Reduce led us to apache spark. 

**Map-Reduce Limitations:**
1. Only Batch Processing. Map-Reduce doesn't support streaming data, real-time or near real-time processing.
2. Complexities in Map-Reduce jobs and very steep learning curve. Managing complex workflows with multiple stages and dependencies can be challenging in MapReduce.
3. There is inefficient application level fault tolerance compared to spark.
4. There is no support for interactive proccessing.
5. MapReduce primarily operates on disk, which can be inefficient for certain types of data processing tasks.

**Spark Advantages:**
1. In-Memory Processing: Spark can process data in memory, significantly improving performance for many types of workloads. It is 100 times faster than MapReduce. Spark can process data stored in disk as well, that would be slower compared to in-memory processing.
2. DAG Execution: Spark's Directed Acyclic Graph (DAG) execution model allows for more flexible and efficient workflow management.
3. Fault Tolerance: Spark provides robust fault tolerance mechanisms to ensure data integrity and minimize downtime.
4. Simplified APIs: Spark offers a unified API for various data processing tasks, including SQL, RDDs, and DataFrames.
5. Spark can handle real time data as well as batch data processing.
6. Spark provide high level APIs in Scala, Java, Python and R.
7. Lazy Evaluation: Evaluation on RDD only occur when some action (write/print/count/collect) occurs.

## Spark Ecosystem
1. **Storage:** Spark doesn't have storage system of its own. Spark can use HDFS, GCS, S3, Azure Blob Storage.
2. **Cluster Resource Management:** Yarn, Mesos, Spark
3. **Engine:** Spark Core is the central engine to manage spark application/jobs such as task scheduling, monitoring.
4. **Libraries:** Spark provide several libraries as per use case such as: Spark SQL, Spark MLlib, Spark Streaming, Spark Graphx.
5. **Programming Languages:** Scala, Python, Java and R.

![image](https://github.com/user-attachments/assets/e87558a0-9ae6-480a-8568-392ec202000f)

## Spark Concepts

### RDD
It is a group of data that can be stored in-memory on worker nodes. This is the building block and core abstraction in spark.
- **Resilient:** This is fault tolerant. It can rebuild the data on application failure from previous step using lineage.
- **Distributed:** Data is distributed among multiple nodes in cluster.
- **Dataset:** Collection of records.

**Features:**
- Immutable: RDD is immutable. Any transformation creates a new RDD.
- Persistence: RDDs can be persist whic allows to reuse it multiple times.
- Operations: 2 kinds of operations are possible on RDD. Transformation (It creates a new RDD), Action (It return value to driver program or external system).
- Partitions: Data is distributed into partitions across cluster nodes. Number of partitions is same as number of blocks in file system when spark read the data. After that user can manage data distribution and partitions.

### Operations:

#### Transformations
It is an operation on a RDD to create a new RDD. 
2 categories:
1. **Narrow Transformation:**
  - One partition is processed and it creates a new partition.
  - No data shuffle is required.
  - Like: Map(), Filter().
2. **Wide Transformation:**
  - Multiple partitions are required to process the data.
  - Data shuffle across partitions occurs.
  - Like Join(), groupby() etc.

#### Actions
It is an operation which provide a non-RDD output. Output is sent to driver program or write data to external system.
Action trigger the execution flow (DAG).
- Collect()
- Count()
-Take()
- Write()

Note: Reading the data is an initial step in the DAG. It is neither action nor transformation.

### DAG
Directed Acyclic Graph also called lineage graph. We cannot have cycle in the graph.
- Each node in the graph is an RDD. Arrow is a transformations in the graph.
- DAG is optimized by spark to create stages.

**Job:** Job represent each action in spark application. Each job has a DAG which reads data from source, perform certain actions and generate output. 
**Stage:** Stage is a sequence of transformations that can be performed without data shuffling. Each job is split into multiple stages. 
**Task:** Task is executed per partition per stage. 


### Architecture

1. **Driver Program**  
  It runs the main() function of spark application.

3. **Spark Context**  
   This is the entry point of the spark for user. 
  
4. **Cluster Manager**  
  Manages the cluster resources.

5. **Executer**  
   Executers are present in worker nodes. Executer provide the space in Memory to store the partitinos and run the task on those partitions.

6. **DAG Scheduler**
  Responsible to optimize the DAG and define stages. It then submit the stages to task scheduler. 

7. **Task Scheduler**
  Responsible to launch tasks on executer. 

8. **Master**
  It is the base of standlone mode spark. This is not applicable when spark is running on cluster.

10. **Worker**
  This is also specific to standalone mode spark. Each worker has multiple executers.


### Depolyment Mode

1. **Client Mode**
  - Spark session is running on users local machine. Jobs are still running on cluster.
  - This makes interactive development possible.
  
2. **Cluster Mode**
  - User just submit the job and spark session is running inside cluster.

3. **Standalone Mode**
  - Spark is running in one single machine where all master and workers and running in single machine.




