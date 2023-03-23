# Spark notes

## Concepts

### OOMING

When multiple tasks are running on the same executor(becasue the executor has multiple cores). You can get OOM because
even though the tasks are running on different cores they are still using the same executor memory. So if your executor
has 3 cores, running 3 tasks, on 3 different partitions you can get OOM error if the sum of the memory consumed by the 3
partitions is greater than the total available memory in the executor.

### Small file problem

In case of a spark code that reads data from a dfs, performs a transformation, and writes the data back to dfs

If each task writes its output data to the file system separately, it may lead to a large number of small files being
written to the file system, which can degrade the write performance and increase the overhead of the file system. To
avoid this, it's better to batch the output data of multiple tasks and write them to the file system in a single
operation, reducing the number of small files and improving the write performance.

In Spark, you can control the batch size by adjusting the spark.sql.shuffle.partitions configuration parameter, which
determines the number of partitions used in shuffling the data between stages. By setting this parameter to an
appropriate value, you can balance the memory usage and the write performance, and optimize the output write operations
for your specific use case.

### Does a task operate on a partiiton as a whole or an element of a partition?

In Spark, a task operates on a partition as a whole, rather than on an element of a partition.

A partition is a logical division of the input data, which is processed independently by a single task. The data in a
partition is typically stored as an RDD (Resilient Distributed Dataset), which is a fault-tolerant collection of
elements that can be processed in parallel across multiple nodes in a cluster.

When a task is assigned to a partition, it operates on the entire partition as a whole, rather than on individual
elements of the partition. The task processes the data in the partition and produces an output, which is typically
another RDD or a result that is written to a file system.

The partitioning of the data and the assignment of tasks to partitions is done automatically by Spark, based on the
partitioning strategy used for the RDDs.

### spark.sql.shuffle.partitions

### What is AQE

https://www.intel.com/content/www/us/en/developer/articles/technical/spark-sql-adaptive-execution-at-100-tb.html

### Distributed shared memory vs RDDs

**fine grained updates to mutable states vs coarse grained transformations on immutable objects**

**logging the data vs logging transformations**

In-memory storage across clusters: You have a huge file and you want to process it in memory since operating on objects
that are in memory is way faster than first loading them into disk and then operating on them. But the problem is that
the file will not fit in memory of a single machine. One way to do this would be load chunks of the file into memory
and process the whole file chunk by chunk in a single machine. But this will be slow, another alternative would be to
have a cluster of machines and laod a partiion of the large file into each of the machines. Now each machine can
process their own partition parallely. Thus we can say that we **put the data where the compute is**.

Existing solutions for In-memory storage across clusters: Distributed shared memory

DSM systems allow fine grained updates i.e. you can update each cell of each row of your file in memory. That sounds
great
but the problem occurs when you have to implement **fault tolerance** i.e when a machine in your cluster containing a
partition of
your large file breaks down. Now to make that available you have to log each update and keep replicating your data to
another machine continuously
so that if a machine is lost you can have another machine which contains the lost data using the logs (Replication log).
But there are problems. When you use replication it means you have to copy a large amount of data across the network
to another machine.
Networks bandwidths between machines in a cluster are not that great. Also storing logs of every update can increase the
storage costs.

This is where coarse grained transforamtion comes in. RDDs don't allow you to make fine grained updates to your partiion
of data,
instead can only perform transformations that apply to the entire data and thus all elements of a partition. So you
cannot
update individual rows/cells of your data, you have to transform your entire data(and create new data). So when a
machine breaks down and
a partition is lost, the only thing you need to recalculate the partiion is the log of transofrmations(which will be
tiny). You can use this
log to recalculate your lost partition right from source. So as you can see we have eleminated both the **network
bandwidth issue** and the **log storage costs** by using
RDDs.

But that's not the whole picture, RDDs are only capable of solving usecases where you don't need fine grained updates to
shared state. SO it is also said
that **"RDDs are a restricted form of shared memory"**. Nevertheless, there are a lot of usecases, especially in
analytics where we need
coarse grained transformations instead of fine grained updates.

![img.png](img.png)

### Representing an RDD

![img_1.png](img_1.png)

### Spark internals

In a single task one partiion will be processed. Is the partition processed element by element? Is the error count
updated as a running count as each element of the partiion is processed? Or is the task performed on a partition as a
whole and error count is updated in bulk?

In Spark, a task corresponds to a single partition of an RDD, which is processed independently by a single executor.
Within a task, the data is processed element by element by the executor, following the transformations specified in the
DAG.

In your case, once the task is sent to an executor, it will read and filter the log lines in the partition element by
element. As each element is processed, the error count will be updated accordingly. Specifically, Spark will maintain a
running count of errors for each partition, and update it as each element is processed.

The updates to the error count will happen in-memory on the executor, and will be aggregated across all elements in the
partition. Once the partition has been fully processed, the final error count for that partition will be returned to the
driver as a result of the task. This process is repeated for each partition of the RDD, and the final error count is
computed by aggregating the results from all the partitions.