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
