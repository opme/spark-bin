A spark binary built on 64 bit ubuntu that fixes the 32 bit limitations in spark.

https://issues.apache.org/jira/browse/SPARK-6235

Spark has some 2G limits in it's 2.x codebase.  These are due to 32 bit types used in various places in the code.  The first of these happens when reading a data block that has been stored to disk.  The code uses an instance of MappedByteBuffer which cannot exceed a size of 2G.  The second is when using kryo serialized data.  The serialized data is stored in a byte[] the size of which cannot exceed 2G.  The third situation occurs when RPC writes data to be sent to a channel.  The code uses a ByteBuf which means that it cannot transfer data over 2G in memory.   The fourth and final issue occurs when an RPC message is received where again a ByteBuf is used which cannot exceed 2G.   The result to the end user is the same for all of the above issues.  Their big data application crashes with a ``Size exceeds Integer.MAX_VALUE at sun.nio.ch.FileChannelImpl.map''.

The recommended workaround for this limitation is to set additional partitions beyond the default 200 on the dataframes.  The properties file for the application allows the partition size to be set for all dataframes created.   Tests were done with up to 4000 partitions but instability was still seen as the study parameters were modified.

There is currently a https://github.com/apache/spark/pull/14995 pull request pending to fix this issue but it has not been incorporated to a released version of spark.  The Spark foundation team has so far refused to incorporate the patch as it contains 143 files and 4400 lines of code.  


Source code for this binary came from https://github.com/opme/spark
