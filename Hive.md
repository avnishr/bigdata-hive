## Hive 3 What is new


# MAP JOIN 
In hive, the table that is being broadcasted should be mentioned in the hint. 

SELECT /* MAPJOIN(d) */ s.symbol, d.dividend
FROM stocks s INNER JOIN dividend d 
ON s.symbol = d.symbol

The left hand side table can now map/join with the right side table in memory. However, we can't perform a RIGHT OUTER JOIN or FULL OUTER JOIN. The reason being that for a record in right hand table, it may be possible that there is no record in one mapper, but the same may exist in another mapper. 

AUTO Map Join 

In step 1, Hive 

Set hive.mapjoin.smalltable.filesize = 30000000 bytes
SET hive.auto.convert.join = true

The above setting will enable auto map join in hive. 

# SMB Join 

Sort Merge Bucketed Join 
1. ALL join tables must be bucketized
2. Number of buckets in big table must be divisible by number of buckets of other tables
3. Bucket columns and Join Columns in the query must be same 
4. hive.enforce.bucketing = true 

CREATE TABLE IF NOT EXISTS table_name (
 column1 STRING, 
 column2 INT,
 column3 FLOAT
 ) CLUSTERED BY (column1, column2) SORTED BY (column1, column2) INTO 10 BUCKETS
 ROW FORMAT DELIMITED
 FIELDS TERMINATED BY ',';
