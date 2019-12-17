# Various File Formats 

#### References : 
* [ORC File & Vectorization - Improving Hive Data Storage and Query Performance](https://www.youtube.com/watch?v=GV7vpR7vpjM)
* [Apache ORC Format](https://orc.apache.org/)

The big data world started off with 

* Text File
* Sequence File 

# Record Columnar (RC)
 
Later Record Columnar file format arrived. RC Format was developed to improve the read performance. Compresses the columns seperately and hence only columns that were needed were deserialized

### Disadvantages of RC formats 
* Did not distinguish between data of different types - cant distinguish between integer, string, binary etc
* Can't interpret the data or build index over the data 
* Hive has complex types like map etc but RC format stores it as a binary blob 
* Seeking to the start of the InputSplit, we have to do byte by bytes 
* Meta data was added at the front - that means that the data had to be accumulated and then written as the metadata was at the start of the file. 

## Needs 

* Ability to write into the file in chunks 
* Break the file into set of rows called stripes
* Default stripe size 256 MB
* Footer contains stripe locations - you can jump to the stripe rather than seeking byte by byte
* Footer contains min, max, sum etc for each columns
* So for queries like count(*), hive just needs to read the last few kbs to read the data 
* The entire file including the footer is compressed. The information is stored in something called postscript. 
* 


