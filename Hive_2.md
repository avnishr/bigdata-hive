# Basic Queries

#### Create Internal Tables
CREATE Table IF NOT EXISTS TABLE1 (col1 string, col2 array<string> , col3 string, col4 int)
ROW FORMAT DELIMITED BY   ','
COLLECTION ITEMS TERMINATED BY ':' 
LINES TERMINATED BY '\n'
STORED as TEXTFILE


CREATE TABLE IF NOT EXISTS TABLE7 (col1 string, col2 array<string>, col3 string, col4 int) 
row format DELIMITED FIELDS TERMINATED BY ','
collection items terminated by ':'
lines terminated by '\n'
STORED as TEXTFILE

/usr/hive/warehouse (internal tables)

LOAD DATA LOCAL INPATH <data path> INTO TABLE TABLE1;

#### External Tables
CREATE EXTERNAL TABLE IF NOT EXISTS TABLE7 (col1 string, col2 array<string>, col3 string, col4 int) 
row format DELIMITED FIELDS TERMINATED BY ',' collection items terminated by ':' lines terminated by '\n'
STORED as TEXTFILE

LOAD DATA LOCAL INPATH <PATH> INTO TABLE <EXTERNAL_TABLE>

We can display column names on the table
set hive.cli.print.header = true


INSERT INTO TABLE tab select col1, col2, col3 from emp_tab;

Note:- If we run this command two times, the number of records will be added twice

INSERT OVERWRITE TABLE TAB SELECT COL1, COL2, COL3 FROM EMP_TAB;

# WRITE source data into two tables 

 1. Create two target tables
 2. Create table DEV (id int, name string, desg string) stored as textfile;
	Create table PROD (id int, name string, desg string) stored as textfile;
	
3. FROM emp_tab INSERT INTO DEV select col1, col2, col3 where col3 = 'Developer' INSERT INTO PROD SELECT COL1,COL2, COL3 WHERE COL3 = 'Prod'

# ALTER TABLE
1. ALTER TABLE <Table Name> change col1 col1New after col3; 

Desc < table name>

2. Change the name of the column - ALTER TABLE CHANGE COLUMN COL2 NEW_COL2 string; 

3. Change the name of the table -  ALTER TABLE TAB RENAME TO TAB1;

4. ALTER TABLE <TABLE NAME> REPLACE COLUMNS (id int, name string); This will remove all the column names and if the table contained 
5. columns, only the first two will be retained

6. ALTER TABLE <table name> set TBLPROPERTIES('auto.purge' = true);
7. ALTER TABLE SET fileformat avro;


# SORT BY DISTRIBUTE BY and CLUSTER BY 

ORDER BY  - SELECT COL2 from TABLE5 ORDER BY COL2

The ordering is done by the reducer. ORDER BY clause should be followed by LIMIT clause. LIMIT is mandatory because we are passing data to one reducer 
Single Reducer can take a lot of time and hence LIMIT clause is used. 

SORT BY - Does sorting. More than one reducer will be used by the job. Does not guarantee full sorting of data. So if sorting is done by col2 and if two reducers are used, the final output comprises of two outputs from two reducers

DISTRIBUTE BY - Distribute the key value pairs amongst the reducers. It ensures that all the rows with the same DISTRIBUTE BY values will go to the same reducer. 

CLUSTER BY - short form of (DISTRIBUTE BY and SORT BY)

select col2 from table5 cluster by col2; 

# Conditions in HIVE
Conditional Statements: IF 

1. SELECT IF(col3= 'England', col1, col2) FROM TABLE1; 

	So if the condition is met, select col1 and if the condition is NOT met select col2.

2. CASE statement 

	SELECT CASE col2 
		WHEN 'Apple' THEN 'The fruit is Apple'
		WHEN 'Orange' THEN 'The fruit is Orange'
		ELSE 'Not recognized fruit'
		END 
	FROM table1 

3. ISNULL 
	SELECT ISNULL(col1) FROM table1

4. COALESCE 
	SELECT COALESCE(col1, col2, col3) FROM table1 	Select first non null values from table1

5. NVL 
	SELECT NVL(col1, col2) FROM table1. Select col2 if col1 is NULL


# EXPLODE/LATERAL VIEW

EXPLODE function - takes an array as input and returns the elements of the array

select col1 from table1
|col1|  
|--|
|[1, 2, 6]|
|[9, 6]|
|4|
|5|


select EXPLODE(col1) FROM table1
|col1|  
|--|
|[1, 2, 6]|
|1|
|2|
|6|
|9|
|6|
|4|
|5|

USE CASE 8  - Lateral view function

We can't select other columns in the select statements 

Author		Books 

A 		[x, y, z]
B		[l, m]
C		[o, p, q, r]

A virtual table is created using the explode function and then joined. 

SELECT AUTHOR, DUMMY_BOOKS FROM TABLE2 LATERAL VIEW EXPLODE(BOOKS) DUMMY AS DUMMY_BOOKS

A x
A y 

Here the virtual table is DUMMY having a column DUMMY_BOOKS. THis is then joined with the TABLE2. 

