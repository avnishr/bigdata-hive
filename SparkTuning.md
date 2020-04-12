Driver memory - out of memory error

The default number for RPC threads that 

spark.rpc.io.serverThreads = 8  

# Scaling Spark Executor


Important Youtube Videos 

Apache Spark Core—Deep Dive—Proper Optimization Daniel Tomes Databricks https://www.youtube.com/watch?v=daXEp4HmS-E&t=4330s

!(https://github.com/avnishr/bigdata-hive/blob/master/Image.JPG)

spark.sql.files.maxPartitionBytes 

This field provides the size of each partition. This is useful if I have more cores and the data processed using lesser # of partitions. 


# Data Skew
https://www.youtube.com/watch?v=Cc2P-pPtTCw


# Tuning Spark 
https://www.youtube.com/watch?v=YgQgJceojJY

## 1 Analyzed Logical Plan 

A query which has a filter 

WHERE col1 = 0.0 shows two rows 
and 
WHERE col1 = 0  shows 1 row

The analyze logical Plan 

FILTER NOT (cast(col2#435841 as double ) = case (0.0 as double)

Where as the filter 

WHERE col1 = 0 

FILTER NOT (cast(col2#435841 as int) = 0

## 2 hive.ql.io.src.OrcSerde 

Very slow. SET spark.sql.hive.covertMetastoreOrc = true ; will turn the hive orc serde on. 

FILEScan orc default.tabdemo1[col1#454775,col2#454777, Batched:True, DataFilters: [isnotnull(col1#454775)


## 3 Vectorized Reader 

Read records in batches. Enabled when 

spark.sql.parquet.enableVectorizedReader = true
spark.sql.orc.enableVectorizedReader = true

This enables reading of records from the file in batches. 
