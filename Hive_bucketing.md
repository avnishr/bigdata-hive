All the same column values of a bucketed columns will go into the same bucket

Let us say that we have a table of employee salary, employee_id, employee_dept and salary. 

Let us say that we can partition the data into departments. What if the data still has lot of divisions. We can bucket them 

Enable bucketing by 

set hive.enforce.bucketing = true
set hive.exec.dynamic.partition.mode = nonstrict

    CREATE TABLE IF NOT EXISTS DEPT_BUCK (
    deptno int, 
    empname string, 
    sal int, 
    location string) 
    Partitioned by (deptname string) clustered by (location) into 4 bucket
    row format delimited
    fields terminated by ','
    lines terminated by '\n'
    stored as textfile. 


    insert into table dept_buck partition(deptname) select col1, col3, col4, col5, col2 from dept_with_loc;

Last column in the select clause is the partition column.

The number of reduce tasks for the above query will be 4 as there are going to be 4 buckets 

If there are lot of distinct values in a column like 100 depts, then we should use buckets rather than partitions

Bucketing enables faster map joins

**Bucketed Map Join** The buckets on both the tables should be equal or in multiples of another 

**Table Sampling**
Sample is a small portion to judge the object 
Table sampling can be defined as taking a few records from a table. LIMIT operator can also be used to extract a set of records from the table. However, the LIMIT operator can just extract data from a partition or a bucket. 

In table sampling it will collect the data in a distributed manner 

select * from dept_buck LIMIT 15;

    select deptno, empname, sal, location  from dept_buck tablesample (bucket 1 out of 2 on location);

Table sampling can be done using some other parameters like percentage etc. That is called block sampling 

**at least 2 % of the data**

    select deptno, empname, sal, location  from dept_buck tablesample (2 percent)
    
**on basis of memory**

    select deptno, empname, sal, location  from dept_buck tablesample (1M)

**by number of rows**

    select deptno, empname, sal, location  from dept_buck tablesample (20rows)


