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