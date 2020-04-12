Driver memory - out of memory error

The default number for RPC threads that 

spark.rpc.io.serverThreads = 8  

# Scaling Spark Executor


Important Youtube Videos 

Apache Spark Core—Deep Dive—Proper Optimization Daniel Tomes Databricks https://www.youtube.com/watch?v=daXEp4HmS-E&t=4330s

!(Image.jpg)

spark.sql.files.maxPartitionBytes 

This field provides the size of each partition. This is useful if I have more cores and the data processed using lesser # of partitions. 


# Data Skew
https://www.youtube.com/watch?v=Cc2P-pPtTCw


# Tuning Spark 
https://www.youtube.com/watch?v=YgQgJceojJY

# Analyzed Logical Plan 

A query which has a filter 

WHERE col1 = 0.0 shows two rows 
and 
WHERE col1 = 0  shows 1 row

The analyze logical Plan 

FILTER NOT (cast(col2#435841 as double ) = case (0.0 as double)

Where as the filter 

WHERE col1 = 0 

FILTER NOT (cast(col2#435841 as int) = 0




Spark may treat 

column1 = 0

and column1 = 0.0  differently. 
