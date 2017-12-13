# Tools 

## sqoop
 
```
> hadoop fs -ls /folder

> hadoop fs -rm -r -f /folder

> hadoop fs -cat /folder

> sqoop import --connect "jdbc:mysql://localhost:3306/retail_db" \
 --username retail_dba --password cloudera \
 --table orders \
 --compress --compression-codec org.apache.hadoop.io.compress.SnappyCodec \
 --bindir ./ \
 --target-dir /user/cloudera/problem1/orders --as-avrodatafile

> sqoop import \
--connect "jdbc:mysql://localhost:3306/retail_db" \
--username retail_dba \
--password cloudera \
--table order_items \
--compress \
--compression-codec org.apache.hadoop.io.compress.SnappyCodec  --bindir ./ \
--target-dir /user/cloudera/problem1/order-items \
--as-avrodatafile

```

## Spark

### Load into DataFrame
```
import com.databricks.spark.avro._
var oDF = spark.sqlContext.read.avro("hdfs://localhost:9000/user/cloudera/problem1/orders")
var oIDF = spark.sqlContext.read.avro("hdfs://localhost:9000/user/cloudera/problem1/order-items")
```

### Join DataFrames
```
var oJoinDF = oDF.join(oIDF, oDF("order_id") === oIDF("order_item_order_id"))
```

### Group and Aggregate
```
oJoinDF.groupBy("order_date", "order_status").agg(sum(col("order_item_subtotal")),countDistinct(col("order_id"))).show()

oJoinDF.groupBy("order_date", "order_status").agg(round(sum(col("order_item_subtotal")),2),countDistinct(col("order_id"))).show()

oJoinDF.groupBy(from_unixtime(col("order_date")/1000),col("order_status")).agg(round(sum(col("order_item_subtotal")),2),countDistinct(col("order_id"))).show()

oJoinDF.groupBy(from_unixtime(col("order_date")/1000).alias("order_date_formatted"),col("order_status")).agg(round(sum(col("order_item_subtotal")),2).alias("total_amount"),countDistinct(col("order_id")).alias("total_orders")).show()

 oJoinDF.groupBy(to_date(from_unixtime(col("order_date")/1000)).alias("order_date_formatted"),col("order_status")).agg(round(sum(col("order_item_subtotal")),2).alias("total_amount"),countDistinct(col("order_id")).alias("total_orders")).show()

 oJoinDF.groupBy(to_date(from_unixtime(col("order_date")/1000)).alias("order_date_formatted"),col("order_status")).agg(round(sum(col("order_item_subtotal")),2).alias("total_amount"),countDistinct(col("order_id")).alias("total_orders")).orderBy(col("order_date_formatted").desc,col("order_status"),col("total_amount").desc,col("total_orders")).show()

 var dataframeResult = oJoinDF.groupBy(to_date(from_unixtime(col("order_date")/1000)).alias("order_date_formatted"),col("order_status")).agg(round(sum(col("order_item_subtotal")),2).alias("total_amount"),countDistinct(col("order_id")).alias("total_orders")).orderBy(col("order_date_formatted").desc,col("order_status"),col("total_amount").desc,col("total_orders"))
```

### Spark SQL
```
oJoinDF.registerTempTable("orderanditems")  -- deprecated
oJoinDF.createOrReplaceTempView("orderanditems")

var sqlResult = spark.sqlContext.sql("select * from orderanditems")
sqlResult.show()    -- Show the records

var sqlResult = spark.sqlContext.sql("select order_date, order_status, sum(order_item_subtotal), count(distinct(order_id)) from orderanditems group by order_date, order_status")
sqlResult.show()

var sqlResult = spark.sqlContext.sql("select to_date(from_unixtime(order_date/1000)) as order_date_formatted, order_status, cast(sum(order_item_subtotal) as decimal(10,2)) as total_amount, count(distinct(order_id)) as total_orders from orderanditems group by order_date, order_status")
sqlResult.show()

var sqlResult = spark.sqlContext.sql("select to_date(from_unixtime(order_date/1000)) as order_date_formatted, order_status, cast(sum(order_item_subtotal) as decimal(10,2)) as total_amount, count(distinct(order_id)) as total_orders from orderanditems group by order_date, order_status order by order_date_formatted desc, order_status, total_amount desc, total_orders")
sqlResult.show()

dataframeResult.show() -- compare with date frame result

```

### Spark RDD
```
oJoinDF
oJoinDF.show()

var rddResult = oJoinDF.map(x=> (x(1),x(3), x(0), x(9)))

var rddResult = oJoinDF.map(x=> (x(1).toString,x(3).toString, x(0).toString.toInt, x(9).toString.toDouble)).rdd
rddResult.take(5).foreach(println)

var rddResult = oJoinDF.map(x=> ( (x(1).toString,x(3).toString), (x(9).toString.toDouble, x(0).toString.toInt ))).rdd -- key value
rddResult.take(5).foreach(println)


import org.apache.spark.SparkContext._

var rddResult = oJoinDF.map(x=> ( (x(1).toString,x(3).toString), (x(9).toString.toDouble, x(0).toString.toInt ))).rdd.combineByKey((x:(Double,Int))=>(x._1,Set(x._2)),(x:(Double,Set[Int]),y:(Double,Int))=>(x._1+y._1,x._2+y._2),(x:(Double,Set[Int]),y:(Double,Set[Int]))=>(x._1+y._1, x._2++y._2))


var rddResult = oJoinDF.map(x=> ( (x(1).toString,x(3).toString), (x(9).toString.toDouble, x(0).toString.toInt ))).rdd.combineByKey((x:(Double,Int))=>(x._1,Set(x._2)),(x:(Double,Set[Int]),y:(Double,Int))=>(x._1+y._1,x._2+y._2),(x:(Double,Set[Int]),y:(Double,Set[Int]))=>(x._1+y._1, x._2++y._2)).map(x=>(x._1._1,x._1._2,x._2._1,x._2._2.size))


var rddResult = oJoinDF.map(x=> ( (x(1).toString,x(3).toString), (x(9).toString.toDouble, x(0).toString.toInt ))).rdd.combineByKey((x:(Double,Int))=>(x._1,Set(x._2)),(x:(Double,Set[Int]),y:(Double,Int))=>(x._1+y._1,x._2+y._2),(x:(Double,Set[Int]),y:(Double,Set[Int]))=>(x._1+y._1, x._2++y._2)).map(x=>(x._1._1,x._1._2,x._2._1,x._2._2.size)).toDF().orderBy(col("_1").desc,col("_2"),col("_3").desc,col("_4"))

rddResult.show()

```

## Saving the Results
```
dataframeResult.show()

spark.sqlContext.setConf("spark.sql.parquet.compression.codec","snappy")
dataframeResult.write.parquet("hdfs://localhost:9000/user/cloudera/problem1/result4a-snappy")
hadoop fs -ls /user/cloudera/problem1/result4a-snappy


spark.sqlContext.setConf("spark.sql.parquet.compression.codec","gzip")
dataframeResult.write.parquet("hdfs://localhost:9000/user/cloudera/problem1/result4a-gzip")
hadoop fs -ls /user/cloudera/problem1/result4a-gzip

dataframeResult.map(x=>x(0)+","+x(1)+","+x(2)+","+x(3)).rdd.saveAsTextFile("hdfs://localhost:9000/user/cloudera/problem1/result4a-csv")hadoop fs -ls /user/cloudera/problem1/result4a-csv
hadoop fs -cat /user/cloudera/problem1/result4a-csv/part-00198

dataframeResult.count()

```

## Load CSV Results into MySQL Table
```
drop table result;

create table retail_db.result(order_date varchar(255) not null,order_status varchar(255) not null, total_orders int, total_amount numeric, constraint pk_order_result primary key (order_date,order_status)); 

desc result;

sqoop export \
--table result \
--connect "jdbc:mysql://localhost:3306/retail_db" \
--username retail_dba \
--password cloudera \
--export-dir "/user/cloudera/problem1/result4a-csv" \
--columns "order_date,order_status,total_amount,total_orders" \
--bindir ./

```