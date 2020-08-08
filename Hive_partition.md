# Partitioning 

    CREATE TABLE IF NOT EXISTS PART_DEPT (
    deptno INT, 
    empname STRING, 
    sal INT)
    PARTITIONED BY (deptname string) row format delimited
    FIELDS TERMINATED BY ',' 
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE

The partitioning column should not be included in the create table columns. 

INSERT data 

    INSERT INTO PART_DEPT partition (deptname='HR') SELECT col1, col3, col4 FROM Department where col2 = 'HR'

A partition HR is created in the HDFS. The folder name is like 

deptname=HR

Since it is just a directory, we can create the partition name different from data. For example

    INSERT INTO PART_DEPT partition (deptname='XZ') SELECT col1, col3, col4 FROM Department where col2 = 'HR'

A folder name deptname=XZ will be created. 

But then we need to know/remember that the folder XZ is storing the HR data. 

You can also load the data using the load file but in that case, we have to seperate files for each department 

    LOAD data local inpath '/home/avrastogi/' into table part_dept partition (deptname = 'XZ')

# Dynamic partitioning

set hive.exec.dynamic.partition=true
set hive.exec.dynamic.partition.mode = nonstrict 

in strict mode, we need to specify one static partition along with other dynamic partitions if you are doing partitions on multiple columns like deptname and salary. 
So hive wants one column to be static

create table if not exists part_dept1 (
deptno int, 
empname string, 
sal int) 
partitioned by (deptname string) row format delimited 
fields terminated by ','
lines terminated by '\n'
stored as textfile

Now let us insert data into the dynamic partitions 

    insert into table part_dept1 partition(deptname) select col1, col3, col4, col2 from dept;

The column on which the partitioning is to be done should be present in the last (col2 in the source table is kept at the last)

Hive will create a seperate partition for each of the unique values in the col2. 
