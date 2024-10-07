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
- Take()
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
   This is the entry point of the spark for user in spark 1.0. 
  
4. **Spark Session**  
    This is the entry point of the spark for user in spark 2.0. It also provide access to spark context.

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


### Cache Data
Some data can be used multiple times in our process. We can cache it so spark doesn't create it again from source.

1. **Cache()**  
  - Stores the data in memory. Eg: ds.cache()
  - If memory is filled then spill the data on disks.

2. **Persits(storageLevel)**
  - Provide storageLevel, where we want to store the data.

3. **Storage Levels**


| Storage Level | In-Memory | On Disk | Serialization | Replication |
| -------- | ------- | ------- | ------- | ------- |
| DISK_ONLY | No | Yes | No | No |
| DISK_ONLY_2| No | Yes | No | Yes |
| MEMORY_AND_DISK| Yes | Yes | No | No |
| MEMORY_AND_DISK_2| Yes | Yes | No | Yes |
| MEMORY_AND_DISK_SER| Yes | Yes | Yes | No |
| MEMORY_AND_DISK_SER_2| Yes | Yes | Yes | Yes |
| MEMORY_ONLY| Yes | No | No | No |
| MEMORY_ONLY_2| Yes | No | No | Yes |
| MEMORY_ONLY_SER| Yes | No | Yes | No |
| MEMORY_ONLY_SER_2| Yes | No | Yes | Yes |


- Serialization reduces the required storage space.
- Replication stores the data in 2 nodes.

 
### Data Skewness
When data is not evenly distributed across partitions. Maybe one key contains most of the records which becomes slowest step in the execution and it increases overall job time. 

Methods to work with skewed data:
- Increase of number of partitions:

  It can help to distribute data more evenly, However it can increase the overhead of managing more partitions.
  
- Use reduceByKey instead of groupByKey:
  reduceByKey performs local aggregation before shuffling. 

- Separate processing of skewed data:
  Separate the keys which are causing skewness and process them separately.   
  
- Salting:
  In this method, we add a random component to a skewed key to create additional unique key. After performing the operation, this can be dropped. 

### Manage partitions in spark

1.Configuration
  - `spark.default.parallelism`: This define the default number of partitions for the newly created dataframe. If data is being read from a distributed file system, then initial number of partitions would be same as number of blocks in source.

2. Repartition()
  - This function can be helpful to increase or decrease the number of partitions. Data is shuffled and new partitions are balanced.
  - It can also be helpful to distribute data based on few columns so records with certain value would end up in one partition.

3. Coalesce()
  - This is used to reduce the partition count.
  - It avoid the full shuffle and new partitions are not necessarily balanced. It can cause skewness in partitions.


### RDD, Dataframes and Datasets

| Criteria | RDD | Dataframe | Dataset |
| -------- | -------- | ------- | ------- |
| Abstraction Level | Lowest level access to data  | On top of RDD | On top of dataframe |
| Schema | No | Yes | Yes |
| Type Safety | No | No | Yes | 
| Optimization | No | Catalyst Optimizer | Catalyst Optimizer |


> RDD is lowest level of data access in spark. It doesn;t have schema on data and without any in-built optimization.
> Dataframe is on top of RDD with defined schema. It is optimized using catalyst optimizer.
> Dataset is similar to dataframe except it provides compile-time type safety.


## Memory Management 

Spark has 2 kinds of processes, which both has memory configuration:

### **1. Driver Process**
  - This is a single process running in master node.
  - "--driver-memory" option provide the driver memory configuration.
  - "spark.driver.maxResultSize" define the mas result size which will be provided to driver program from executors.


### **2. Executor Process**
  - Each node has "yarn.nodemanager.resource.memory-mb" amount of memory available for spark to distribute among exectors.
  - Each executer can have maximum memory limit till "yarn.scheduler.maximum-allocation-mb".
  - Executer memory is devided into:

  **1. Heap Memory:**
    - This is where spark run its operations and store data. 
    - Each executor memory is configured by "spark.executor.memory".
    - It is further devided into:

      - **Reserved Memory:**
          - This ensures basic JVM lelvel proceses doesn't get stuck. It is not configurable. Generally, it is fixed around 300MB (reserved_system_memory_bytes). 

      - **Usable Memory**  
          - This is (executor memory - reserved memory). It is further sub-devided into:

          - **User Memory**
             - (1-spark.memory.fraction) of usable memory.
             - It is used by custom user code (e.g., large arrays, use user-defined functions, or work with external libraries that load objects into memory).

          - **Execution Memory:**    
              - Used for execution, shuffle, joins, sorts, aggregations etc.
    
          - **Storage Memory:**
              - Used for caching, broadcasting etc. It is configured using "spark.memory.storageFraction".

          > Execution memory and storage memory combined is configured using spark.memory.fraction. Generally, this is 0.6 
          > Dynamic memory occupancy is where storage and execution memory is assigned as per needs. Execution memory is the priority. If execution needs more memory, it can allocate even from storage memory and when execution doesn't needs even assigned memory then executor memory can be assigned for storage as well. 


  **2. Off-Heap Memory:**
    - This memory is outside of JVM garbage collection control. This can be useful for large datasets, reducing the frequency of GC operations
    - By default it is disabled. It can be enabled using "spark.executor.memoryOffHeap.enabled" and can be configured using "spark.executor.memoryOffHeap.size".

  **3. Overhead Memory:**
    - This is outside of JVM Heap memory. It include Off-Heap Memory, memory used for native processes like Hadoop or python processes. 
    - It is by default 10% of executor memory with a minimum of 384MB.
    - This can be configured using "spark.executor.memoryOverhead" (absolute size) or "spark.executor.memoryOverheadFactor" (fraction of executor memory).
    - 





