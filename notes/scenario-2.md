```
sqoop import \
--connect "jdbc:mysql://localhost:3306/retail_db" \
--username retail_dba \
--password cloudera \
--table products \
--as-textfile \
--target-dir /user/cloudera/products \
--fields-terminated-by '|'
```

```
hadoop fs -mkdir /user/cloudera/problem2/
hadoop fs -mkdir /user/cloudera/problem2/products
hadoop fs -mv /user/cloudera/products/* /user/cloudera/problem2/products/

hadoop fs -chmod 765 /user/cloudera/problem2/products/*
```

```
val products = sc.textFile("/user/cloudera/problem2/products/").map(
    rec => {
        var d = rec.split('|'); 
        (d(0).toInt,d(1).toInt,d(2).toString,d(3).toString,d(4).toFloat,d(5).toString)
    }
)
```

```
case class Product(productID:Integer, productCategory: Integer, productName: String, productDesc:String, productPrice:Float, productImage:String)

```

```
var productsDF = products.map(x=> Product(x._1,x._2,x._3,x._4,x._5,x._6)).toDF()
productsDF.show()
productsDF.printSchema()
productsDF.createOrReplaceTempView("products")
```

```
var dataFrameResult = productsDF.filter("productPrice < 100").groupBy(col("productCategory")).agg(max(col("productPrice")).alias("max_price"),countDistinct(col("productID")).alias("tot_products"),round(avg(col("productPrice")),2).alias("avg_price"),min(col("productPrice")).alias("min_price")).orderBy(col("productCategory"))

dataFrameResult.show()

```

```
productsDF.createOrReplaceTempView("products")
var sqlResult = spark.sqlContext.sql("select productCategory, max(productPrice) as maximum_price, count(distinct(productID)) as total_products, cast(avg(productPrice) as decimal(10,2)) as average_price, min(productPrice) as minimum_price from products where productPrice <100 group by productCategory order by productCategory desc")

sqlResult.show();

```

```


```


```

```


```

```