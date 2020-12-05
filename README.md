# Study Notes: Learning Hadoop and Spark using Docker-compose environment
(Hadoop, MapReduce, YARN, Hive, Parquet, Pig, HBase, Phenix, Spark)

# Part 0: Introduction and System Setup

When it comes to learning distributed file systems such as Hadoop and Spark, the biggest challenge for beginner is to find a proper production environment with cluster settings. Nowadays, though cloud computing become more and more popular, it is hard to imagine the structure of storing, managing, and computing data over the cluster of servers as a whole picture in the virtual settings. Besides, learners need to wisely select enormous information on the web and put together piece by piece due to the nature of open-source projects and communities.

Thus, the aim of this article is to use a docker-compose environment to simulate the production environment on a local machine and go through fundamental concepts of Hadoop ecosystem and Spark with graphs using consistent naming system and basic coding examples in the lab environment. However, the details about setting up the Docker containers infrastructure are out of the scope of this article. All the references will be listed in the end of each section. 


# Part 1: Hadoop, HDFS
## Hadoop Architecture

Hadoop is a distributed computing framework developed and maintained by the Apache Software Foundation, which means that Hadoop is a free open source.
Hadoop contains three main parts: on the bottom is a file system to manage the storage of data – HDFS (Hadoop Distributed File System; on top of HDFS, there’s a framework to run data processing tasks across multiple servers –YARN (Yet! Another Resource Negotiator), also for cluster resource management; and a framework to define data processing tasks across multiple servers – MapReduce, and. HDFS and YARN also support other data processing engines, such as Spark.

Hadoop is normally deployed on a group of machines, which is called cluster. A cluster is made up of nodes. One of the node acts as master node is Name Node (e.g. **[nna]**), which manage the overall file system and stores both the directory structure and the metadata for all the files. Other nodes are called Data Nodes (e.g. **[wka01],[wka02],[wka03]**,etc.), which physically store the data.

## HDFS structure

#### How a file is stored in HDFS?
A file is divided into several equal-sized units (blocks): 128MB per block, each of the blocks are stored across the Data Nodes (e.g. **[wka01],[wka02],[wk03]**,etc.). Block locations for each file are stored in the Name Node (e.g. **[nna]**).

#### What if one of the blocks gets corrupted or one of the data nodes crashes?
By defining replication factor, each block is replicated, and replicas are stored in different data nodes. The default is 3 copies per block: 2 copies in one rack, another copies in a different rack, which is known as rack awareness.

#### Pros:
- Write once, read many
- Load balancing
- Fault tolerance

# Part 2: MapReduce, YARN

## MapReduce

#### MapReduce operates in two phases: 
1. Each data node (e.g. **[wka01],[wka02],[wka03]**) independently runs the process on the blocks which live within it and produces a result for each block. This is called Map phase. In other words, **[wka01], [wka02], [wka03]** simultaneously execute ‘map’ program via parallel processing with intermediate results. By default, the number of maps is equal to the number of blocks. 
2. Then take all the results to one data node (e.g. **[wka01]**) and combine them to get the final result. This is called Reduce phase.

#### Comparison between MapReduce and Spark
1. MapReduce is ‘disk killer’, while Spark is ‘memory killer’
2. Complex SQL commands (join, groupBy, sortBy…) require multiple runs of MapReduce. If something wrong happens, it will restart from the beginning. Spark, on the other hand, will restart from where it gets lost.

## YARN

YARN is for the management of resources on the Hadoop cluster. In other words, YARN coordinates all the different MapReduce tasks running on the cluster; and monitors for failures and reassign new nodes when others fail.

YARN runs programs for data processing engines, such as MapReduce, Spark. There will be Resource Manager (RM, e.g. **[rma]**), and Node Managers (NM, e.g. **[wka01],[wka02],[wka03]**). NM send heartbeat to RM.

When a job is submitted, client (e.g. **[ds101]**) talks to RM (e.g. **[rma]**) and RM creates Application Master in one of the Data Node (e.g. **[wka01]**). Once AM is created, it will talk to Name Node (e.g. **[nna]**) for block locations and to NM for usage of the cluster.

#### How YARN works
1. [ds101] submits an application
2. [rma] allocates a container to start Application Manager in [wka01]
3. Application Manager gets block locations from [nna]
4. Application Manager registers with [rma] and asks containers from [rma
5. [rma] allocates containers in [wka02],[wka03]
6. Application Manager in [wka01] notifies Node Manager in [wka02], [wka03] to launch containers and communicates with Node Managers while running the tasks
7. Application code is executed in the container (i.e. YARNChild in [wka02], [wka03])

# Part 3: Hive, Parquet, Compression
# Part 4: Pig
# Part 5: Spark
# Part 6: HBase, Phenix 
