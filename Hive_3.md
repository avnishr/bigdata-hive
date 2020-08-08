##  Hive Functions

### RLIKE

Select 'Hadoop' rlike 'ha';
 
RLIKE trim spaces as well. 

    Select 'Hadoop    ' rlike 'ha';
    
    SELECT null RLIKE 'ha' ; 
    >>> NULL 

### RANK, DENSE_RANK and ROW_NUMBER 

RANK gives the ranking within ordered partition. Ties are assigned the same rank with the next ranking skipped. 

|col|rank|
|---|---|
|a|1|
|a|1|
|a|1|
|b|4|
|c|5|

DENSE_RANK gives the ranking within ordered partition but the ranks are consecutive. No ranks are skipped if these ranks are multiple items 

|col|dense_rank|
|---|---|
|a|1|
|a|1|
|a|1|
|b|2|
|c|3|

ROW_NUMBER assigns unique number to each row within the PARTITION given by the ORDER BY clause
|col|row_number|
|---|---|
|a|1|
|a|2|
|a|3|
|b|4|
|c|5|


    SELECT col1, col2, rank() over(order by col2 desc) from table1

Use only 1 reducer as it is ORDER BY 

So if we want to take the first two rows, rank wont serve the purpose. We would have to use DENSE_RANK

    SELECT col1, col2, dense_rank() over(order by col2 desc) from table1

Will use only 1 reducer as we are using ORDER BY
Still does not give the top 2 transactions by using DENSE_RANK. 

 SELECT col1, col2, row_number over(order by col2 desc) from table1

We can also use PARTITION BY col1 ORDER BY col2

    SELECT col1, col2, dense_rank() over(partition by col1 order by col2 desc) from table1

The output will be partitioned by col1 and then ranked based on the order in the partition. 


