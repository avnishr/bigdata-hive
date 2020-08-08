# Joins

## Inner Joins

Only matching rows are selected. 

    create table if not exists emp_tab (
    col1 int,
    col2 string,
    col3 string,
    col4 int,
    col5 int,
    col6 int,
    col7 string) 
    row format delimited 
    fields terminated by',' 
    lines terminated by'\n'
    stored as textfile;

    create table if not exists dept_tab (
    col1 int,
    col2 string,
    col3 string,
    col4 string) 
    row format delimited 
    fields terminated by',' 
    lines terminated by'\n'
    stored as textfile;


    JOIN query 
    select emp_tab.col1, emp_tab.col2,
    emp_tab.col3,
    dept_tab.col1,
    dept_tab.col2,
    dept_tab.col3 
    From emp_tab 
    JOIN dept_tab on (emp_tab.col6 = dept_tab.col1);

*HIVE will not allow any < or > sign in join operation*

*How is a joined performed*
If a join is performed between a table stocks and a table dividends, the stocks table is read by individual mappers and then each mapper will *emit a key value pair*.  The key will be the join column and the value the selected columns. Keys will be divided amongst the reducer. 

The tables in the join are loaded in memory except the last table. Records for the last table are streamed to the reducer. In final step the the reducer does the final joining of the records. 

The records of the last table is streamed and the other tables are loaded in memory. So we can optimize this join by moving the larger table to the last table. 

** JOIN Optimizations **

[Join Optimizations ](https://www.youtube.com/watch?v=dwd9m1Zl04Q)

In Hive in the join, the last table of the join is streamed and the rest of the tables are buffered. 

the main table is buffered and the joining table is streamed. 

If the joining columns are same, i.e, in the following query the join is happening on col1 of dept_tab, so the first two tables are buffered and the last table is streamed. 

    select emp_tab.col1,emp_tab.col2,dept_tab.col2,dept_tab.col3,third_tab.col2 from emp_tab join dept_tab on ( emp_tab.col7 = dept_tab.col1 ) join third_tab on (dept_tab.col1 = third_tab.col1);

If the joining columns on different columns, i.e, as follows 

    select emp_tab.col1,emp_tab.col2,dept_tab.col2,dept_tab.col3,third_tab.col2 from emp_tab join dept_tab on ( emp_tab.col7 = dept_tab.col4 ) join third_tab on (dept_tab.col1 = third_tab.col1);

The first join between *emp_tab* and *dept_tab* will have emp_tab buffered and dept_tab streamed and then the result of the join of these two tables buffered and the *third_tab* streamed. 

This leads us to ensure that the smaller tables are mentioned in the first in a join clause and the larger tables at the end. Larger tables will be streamed and the smaller tables will be buffered. 

Stream table hint 

    Select /*+  STREAMTABLE(s) */ s.col1, d.dividend 
    FROM stocks s INNER JOIN dividend d ON s.symbol = d.symbol

We can optimize this query even further by reducing the shuffle and sort phase. By using the map joins 

#### Map Joins 

SELECT /*+ MAPJOIN(d) */ s.symbol, d.dividend FROM stocks s INNER JOIN dividend d  ON s.symbol = d.symbol

Dividend is the small dataset and we have specified the dividend dataset in the Map Join hint.  The table is loaded in memory by a map reduce task. This task reads and serializes the data into a hash table and is then uploaded to distributed cache. Hadoop copies the files associated with the distributed cache to all the machines where the map jobs are going to be executed. This means that the files will be available locally to all these map tasks. 

This will optimize the inner joins. Left outer join is also possible as dividend table is available at all the mappers in total. 
Right outer join is not possible as we need the complete data for the right table. 

**Auto Map Join**

To enable auto map join we need to set 

    set hive.auto.convert.join=true

To do a map only join, we need to know the smaller tables. The smaller tables are loaded into the cache and made available to all the mappers. 

    SELECT d.col1, d.col2 FROM dividends d 
    INNER JOIN (SELECT symbol, ymd FROM stocks WHERE volume > 100000) s ON s.symbol = d.symbol and s.ymd = d.ymd

In the above table, hive can't determine which table is big and which table is small. Hive can't decide which dataset can be put in memory. 

Option 1 - If the subquery is larger dataset, the dividends table is sent to the mappers. 

Option 2 - If the subquery is the smaller dataset, the results of the subquery are sent to all the mappers. 

Option 3 - If the dividends and the results of the subquery are both large, the regular join is performed 

small table is decided by 
**hive.auto.convert.join=true**
**hive.mapjoin.smalltable.filesize property = 30MB**

### SMB Map Join

Sort Merge Bucketed Join 

1. All join tables must be bucketed
2. Number of buckets of big table should be divisible by the number of buckets of the small tables. 
3. Bucket columns from the table and the join columns should be same. 

CREATE TABLE IF NOT EXISTS stocks (

) 
CLUSTERED BY (symbol) SORTED BY (symbol) INTO 10 BUCKETS 
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY ','
SAVE AS TEXTFILE;

CREATE TABLE IF NOT EXISTS dividend (

) 
CLUSTERED BY (symbol) SORTED BY (symbol) INTO 5 BUCKETS 
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY ','
SAVE AS TEXTFILE;

1. There will be 10 Mappers created 
2. Matching partitions of the smaller table are replicated to the mappers
3. Join takes place at the map side. 

properties to set

    set hive.optimize.bucketmapjoin = true
    set hive.auto.convert.sortmerge.join=true
    set hive.optimize.bucketmapjoin.sortedmerge=true












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
4. SET hive.enforce.bucketing = true  </br>
   SET hive.auto.convert.sortmerge.join = true  </br>
   SET hive.optimize.bucketmapjoin = true </br>
   SET hive.optimize.bucketmapjoin.sortedmerge  = true </br>
   SET hive.auto.convert.sortmerge.join.noconditionaltask = true Set to true so that map join hint is not needed  </br>
   SET hive.input.format=org.apache.hadoop.hive.ql.io.BucketizedHiveInputFormat  </br>

hive.auto.convert.join.noconditionaltask.size

Default Value: 10000000

If hive.auto.convert.join.noconditionaltask is off, this parameter does not take effect. However, if it is on, and the sum of size for n-1 of the tables/partitions for an n-way join is smaller than this size, the join is directly converted to a mapjoin (there is no conditional task). The default is 10MB.

hive.auto.convert.join.use.nonstaged

Default Value: false

For conditional joins, if input stream from a small alias can be directly applied to the join operator without filtering or projection, the alias need not be pre-staged in the distributed cache via a mapred local task.



CREATE TABLE IF NOT EXISTS table_name (
 column1 STRING, 
 column2 INT,
 column3 FLOAT
 ) CLUSTERED BY (column1, column2) SORTED BY (column1, column2) INTO 10 BUCKETS
 ROW FORMAT DELIMITED
 FIELDS TERMINATED BY ',';


INSERT OVERWRITE TABLE abc
SELECT * FROM STOCKS;


### SORT BY, ORDER BY, DISTRIBUTE BY, CLUSTER BY

DISTRIBUTE BY :- Divide the rows into N groups/reducers. However the rows are not sorted

CLUSTER BY :-  means DISTRIBUTE BY and SORT BY


