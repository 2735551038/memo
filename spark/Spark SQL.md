# 语法

# 1. to_timestamp 毫秒丢失, spark3.0之后解决

```scala
val dfMilli = Seq(
("2019-02-19 03:14:12.254", "20190219T031412.254"),
("9999-12-31 23:23:41.182", "99991231T232341.182")
).toDF("Standard", "Formatted")

val dfMicro = Seq(
("2019-02-19 03:14:12.254183", "20190219T031412.254183"),
("9999-12-31 23:23:41.182109", "99991231T232341.182109")
).toDF("Standard", "Formatted")

val dfNano = Seq(
("2019-02-19 03:14:12.254183893", "20190219T031412.254183893"),
("9999-12-31 23:23:41.182109821", "99991231T232341.182109821")
).toDF("Standard", "Formatted")

val dfMilliTs = dfMilli
  .withColumn("StandardTs", to_timestamp('Standard))
  .withColumn("FormattedTs", to_timestamp('Formatted, "yyyyMMdd'T'HHmmss.SSS"))

val dfMicroTs = dfMicro
  .withColumn("StandardTs", to_timestamp('Standard))
  .withColumn("FormattedTs", to_timestamp('Formatted, "yyyyMMdd'T'HHmmss.SSSSSS"))

val dfNanoTs = dfNano
  .withColumn("StandardTs", to_timestamp('Standard))
  .withColumn("FormattedTs", to_timestamp('Formatted, "yyyyMMdd'T'HHmmss.SSSSSSSSS"))

scala> dfMilliTs.show(false)
+-----------------------+-------------------+-----------------------+-------------------+
|Standard               |Formatted          |StandardTs             |FormattedTs        |
+-----------------------+-------------------+-----------------------+-------------------+
|2019-02-19 03:14:12.254|20190219T031412.254|2019-02-19 03:14:12.254|2019-02-19 03:14:12|
|9999-12-31 23:23:41.182|99991231T232341.182|9999-12-31 23:23:41.182|9999-12-31 23:23:41|
+-----------------------+-------------------+-----------------------+-------------------+


scala> dfMicroTs.show(false)
+--------------------------+----------------------+--------------------------+-----------+
|Standard                  |Formatted             |StandardTs                |FormattedTs|
+--------------------------+----------------------+--------------------------+-----------+
|2019-02-19 03:14:12.254183|20190219T031412.254183|2019-02-19 03:14:12.254183|null       |
|9999-12-31 23:23:41.182109|99991231T232341.182109|9999-12-31 23:23:41.182109|null       |
+--------------------------+----------------------+--------------------------+-----------+


scala> dfNanoTs.show(false)
+-----------------------------+-------------------------+--------------------------+-----------+
|Standard                     |Formatted                |StandardTs                |FormattedTs|
+-----------------------------+-------------------------+--------------------------+-----------+
|2019-02-19 03:14:12.254183893|20190219T031412.254183893|2019-02-19 03:14:12.254183|null       |
|9999-12-31 23:23:41.182109821|99991231T232341.182109821|9999-12-31 23:23:41.182109|null       |
+-----------------------------+-------------------------+--------------------------+-----------+
```



## 2. Dataframe save to csv or json

```scala
df.write.option("header","true").csv("hdfs://hadoop1:9000/input/xx.csv")

# 合并成一个文件

df.coalesce(1).write.option("header","true").csv("hdfs://hadoop1:9000/input/xx.csv")

df.write.json("hdfs://hadoop1:9000/input/xx.json")
```

