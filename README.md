# Cloudera CCA175 - Spark and Hadoop Developer

The [Cloudera VMs](https://www.cloudera.com/downloads/quickstart_vms/5-12.html "Cloudera VMs") require lot of machine resources and this could be a challenge for those who want to obtain CCA175 certification, but do not have a powerful machine. 

Also, for learning purpose it is better to be more hands on and actually configure the tools and utilities by yourself. Here I collected the resources as part of my learning for this certification to install them in my MacBook.

## Setting up Hadoop

Follow the [instructions here](https://www.slideshare.net/SunilkumarMohanty3/install-apache-hadoop-on-mac-os-sierra-76275019) to set up Hadoop in your MacBook

The PDF is also available [here](./setting_up_on_mac.pdf)

## Setting up Cloudera retail_db

Script to set up the [retail_db](./cloudera_retail_db.sql)


## Setting up sqoop

```
brew install sqoop
```

## Setting up Flume

```
brew install flume
```

## Setting up Spark

```
brew install scala
brew install apache-spark
```

* Copy spark-avro to $SPARK_HOME/libexec/jars


## Tools and Commands
* [Import/export using sqoop, and how to use Spark DataFrame, SQL and RDD](HOWTO_orders_table.md)

## Learning Resources
* http://arun-teaches-u-tech.blogspot.my/p/certification-preparation-plan.html
* https://www.coursera.org/learn/machine-learning
* https://www.kaggle.com
* https://www.youtube.com/playlist?list=PLf0swTFhTI8q0x0V1E6We5zBQ9UazHFY0


## Fun to know
* https://www.youtube.com/watch?v=rIofV14c0tc
* https://www.youtube.com/watch?v=ccCblUZFM0w&t=4116s 
* https://github.com/BenjiKCF/Neural-Network-with-Financial-Time-Series-Data
* https://deeplearning4j.org/
* https://keras.io/
* http://colah.github.io/posts/2015-08-Understanding-LSTMs/
* https://skillsmatter.com/legacy_profile/tetiana-ivanova#skillscasts
* https://www.youtube.com/watch?v=ICKBWIkfeJ8&list=PLAwxTw4SYaPkQXg8TkVdIvYv4HfLG7SiH
* https://github.com/CamDavidsonPilon/Probabilistic-Programming-and-Bayesian-Methods-for-Hackers
* https://speakerdeck.com/jakevdp/statistics-for-hackers
* https://www.youtube.com/watch?v=Iq9DzN6mvYA