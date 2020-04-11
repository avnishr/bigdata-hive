## Hive 3 What is new


# MAP JOIN 
In hive, the table that is being broadcasted should be mentioned in the hint. 

SELECT /* MAPJOIN(d) */ s.symbol, d.dividend
FROM stocks s INNER JOIN dividend d 
ON s.symbol = d.symbol

The left hand side table can now map/join with the right side table in memory. However, we can't perform a RIGHT OUTER JOIN or FULL OUTER JOIN. The reason being that for a record in right hand table, it may be possible that there is no record in one mapper, but the same may exist in another mapper. 

AUTO Map Join 

In step 1, Hive 

# Set hive.mapjoin.smalltable.filesize = 30000000 bytes


# SET hive.auto.convert.join = true

The above setting will enable auto map join in hive. 
