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

```

```