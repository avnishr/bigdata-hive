## Views 

    CREATE VIEW IF NOT EXISTS VIEW1 AS 
    Select col1 as id, col2 as name 
    FROM EMP_TAB


    ALTER VIEW VIEW1 AS <put a new query here> 

    ALTER VIEW VIEW1 RENAME to MY_VIEW1
    
    DROP VIEW VIEW1

## Indexes 

Refer to the index to search a record 

Indexing is done at table level.  Hive is a datawarehousing tool dealing in large datasets. 

    Create index index1 on table table1(col1) as 'COMPACT' with deferred rebuild;

WE can create two type of indexes on the table 

1. COMPACT - index column value and the block id 
2. BITMAP -  stores the combination of indexed column value and list of rows as a bitmap

Storing of mapped values of rows in different blocks. Data in hdfs is distributed across nodes. Identify which row is present in which block. 

With deferred rebuild: Newly created index is empty. Only after altering the index to rebuild the index will be generated. 

We have created an index on a table and the table on which the index is build has new data. The index has to be rebuild. 

    ALTER index index1 on table1 rebuid;


    CREATE index i2 on table table9(col9) as 'COMPACT' with deferred rebuild stored as rcfile

    CREATE index i2 on table table9(col9) as 'COMPACT' with deferred rebuild row format delimited fields terminated by '\n' stored as textfile

### Multiple indexes 

Can we have any number of indexes for a table?

    Create index i4 on table table9(col9) as 'BITMAP' with deferred rebuild

    alter index i4 on table9 rebuild

### Show indexes

    Show formatted indexes on table9


