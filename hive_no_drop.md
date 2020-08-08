# Gaurd Features 

## no drop and offline 

No Drop - prevent the partition or table to be dropped 

We can enable this to a table. 

    Alter table <Table Name>  enable no_drop 

In case we now want to drop this table 

    ALter table <Table Name> disable no_drop;

We can even enable no drop for a partition

Alter table part_dept partition(deptname='HR') enable no_drop;

alter table part_dept drop partition (deptname='HR') 
will give an error 

Now if we want to drop it , we can disable it 

alter table part_dept partition(deptname='HR') disable no_drop 

## offline 

This will offline the table 

    Alter table part_dept enable offline ;

If we select against this table, it will give an error. 

We can disable it by 

    Alter table part_dept disable offline. 

We can do it against a partition as well 

    Alter table part_dept partition(deptname='HR') enable offline; 

We can't query on the HR partition now. 

select * from part_dept where deptname = 'HR' 

will give error
