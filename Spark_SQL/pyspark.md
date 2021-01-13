# pyspark을 이용해서 Spark SQL를 다루기

기본적으로 hadoop(이하 하둡)의 `yarn`(이하 얀)위에서 Spark(이하 스파크)가 작동한다고 가정하고 이 글을 작성하겠다. 연습을 하기 위해서 우리 팀원이 모은 주식 자료를 사용하고자 한다. 하둡과 얀을 아래와 같이 시작하고, 스파크에서 자료를 쉽게 입력하기 위하여 `dfs`에 `stock_full.csv`을 넣는다.

```bash
pi@master:~ $ start-dfs.sh && start-yarn.sh
hadoop dfs -put stock_full.csv
```

## 기초

우선 pyspark(이하 파이스파크)를 실행하고 `SparkSession`을 이용해 자료를 저장할 곳을 하나 만든다. 진행 과정은 다음과 같다.

```pytho
spark = SparkSession \
     .builder \
     .appName("Python Spark SQL basic example") \
     .config("spark.some.config.option", "some-value") \
     .getOrCreate()
```

```bash
pi@master:~ $ pyspark
Python 3.7.3 (default, Jul 25 2020, 13:03:44) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
2021-01-13 07:01:32,574 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 3.0.1
      /_/

Using Python version 3.7.3 (default, Jul 25 2020 13:03:44)
SparkSession available as 'spark'.
>>> from pyspark.sql import SparkSession
>>> 
>>> spark = SparkSession \
...     .builder \
...     .appName("Python Spark SQL basic example") \
...     .config("spark.some.config.option", "some-value") \
...     .getOrCreate()
2021-01-13 07:02:03,486 WARN util.SizeEstimator: Failed to check whether UseCompressedOops is set; assuming yes
```

앞에서 만든 `spark`라는 `SparkSession`에 주식자료를 입력해보자. `df.show()`으로 입력한 자료를 확인할 수 있고, `df.printSchema()`을 이용해서는 입력한 자료 형식을 확인할 수 있다.

```python
>>> df = spark.read.csv("stock_full.csv")
2021-01-13 07:02:03,486 WARN util.SizeEstimator: Failed to check whether UseCompressedOops is set; assuming yes
>>> df.show()                                                                   
+----------+----+----+----+-----+--------+---------+------+---+
|       _c0| _c1| _c2| _c3|  _c4|     _c5|      _c6|   _c7|_c8|
+----------+----+----+----+-----+--------+---------+------+---+
|      Date|Open|High| Low|Close|  Volume|Adj_Close|  code| id|
|2000-01-04|2658|3110|2658|15552|  898582|     2752|000020|  1|
|2000-01-05|3167|3572|2940|16495| 3865389|     2918|000020|  2|
|2000-01-06|3299|3468|2808|14327| 1382353|     2535|000020|  3|
|2000-01-07|2978|3289|2827|16448| 2402938|     2910|000020|  4|
|2000-01-10|3638|3694|3157|16024| 3993227|     2835|000020|  5|
|2000-01-11|3204|3487|3110|16354| 1596654|     2893|000020|  6|
|2000-01-12|3289|3760|3204|18804| 3941243|     3327|000020|  7|
|2000-01-13|3770|4203|3468|17532|11397248|     3102|000020|  8|
|2000-01-14|3506|3506|2988|14940| 4794737|     2643|000020|  9|
|2000-01-17|2950|3204|2903|14798| 3258024|     2618|000020| 10|
|2000-01-18|3016|3110|2827|14233| 2067694|     2518|000020| 11|
|2000-01-19|2856|2978|2676|13479| 2006692|     2385|000020| 12|
|2000-01-20|2639|2714|2545|13007| 1070448|     2301|000020| 13|
|2000-01-21|2639|2865|2554|13290| 2250169|     2351|000020| 14|
|2000-01-24|2714|2865|2667|13479| 1558462|     2385|000020| 15|
|2000-01-25|2667|2771|2582|12913|  939957|     2285|000020| 16|
|2000-01-26|2658|2676|2545|13054|  772335|     2310|000020| 17|
|2000-01-27|2639|2969|2610|13667| 3375253|     2418|000020| 18|
|2000-01-28|2827|2827|2667|13761| 1084770|     2435|000020| 19|
+----------+----+----+----+-----+--------+---------+------+---+
only showing top 20 rows

>>> df.printSchema()
root
 |-- _c0: string (nullable = true)
 |-- _c1: string (nullable = true)
 |-- _c2: string (nullable = true)
 |-- _c3: string (nullable = true)
 |-- _c4: string (nullable = true)
 |-- _c5: string (nullable = true)
 |-- _c6: string (nullable = true)
 |-- _c7: string (nullable = true)
 |-- _c8: string (nullable = true)
```

아쉽게도 옵션을 제대로 주지 않아서 `header`를 제대로 처리하지 못 했다. `header` 옵션을 추가해서 다시 입력하자.

```python
>>> stock = spark.read.option("header",True).csv("stock_full.csv")
>>> stock.printSchema()
root
 |-- Date: string (nullable = true)
 |-- Open: string (nullable = true)
 |-- High: string (nullable = true)
 |-- Low: string (nullable = true)
 |-- Close: string (nullable = true)
 |-- Volume: string (nullable = true)
 |-- Adj_Close: string (nullable = true)
 |-- code: string (nullable = true)
 |-- id: string (nullable = true)
>>> stock.show()
+----------+----+----+----+-----+--------+---------+------+---+
|      Date|Open|High| Low|Close|  Volume|Adj_Close|  code| id|
+----------+----+----+----+-----+--------+---------+------+---+
|2000-01-04|2658|3110|2658|15552|  898582|     2752|000020|  1|
|2000-01-05|3167|3572|2940|16495| 3865389|     2918|000020|  2|
|2000-01-06|3299|3468|2808|14327| 1382353|     2535|000020|  3|
|2000-01-07|2978|3289|2827|16448| 2402938|     2910|000020|  4|
|2000-01-10|3638|3694|3157|16024| 3993227|     2835|000020|  5|
|2000-01-11|3204|3487|3110|16354| 1596654|     2893|000020|  6|
|2000-01-12|3289|3760|3204|18804| 3941243|     3327|000020|  7|
|2000-01-13|3770|4203|3468|17532|11397248|     3102|000020|  8|
|2000-01-14|3506|3506|2988|14940| 4794737|     2643|000020|  9|
|2000-01-17|2950|3204|2903|14798| 3258024|     2618|000020| 10|
|2000-01-18|3016|3110|2827|14233| 2067694|     2518|000020| 11|
|2000-01-19|2856|2978|2676|13479| 2006692|     2385|000020| 12|
|2000-01-20|2639|2714|2545|13007| 1070448|     2301|000020| 13|
|2000-01-21|2639|2865|2554|13290| 2250169|     2351|000020| 14|
|2000-01-24|2714|2865|2667|13479| 1558462|     2385|000020| 15|
|2000-01-25|2667|2771|2582|12913|  939957|     2285|000020| 16|
|2000-01-26|2658|2676|2545|13054|  772335|     2310|000020| 17|
|2000-01-27|2639|2969|2610|13667| 3375253|     2418|000020| 18|
|2000-01-28|2827|2827|2667|13761| 1084770|     2435|000020| 19|
|2000-01-31|2714|2865|2714|13573| 1197226|     2401|000020| 20|
+----------+----+----+----+-----+--------+---------+------+---+
only showing top 20 rows
```

`header`는 적절하게 잘 들어갔지만, 이번에는 `stock.printSchema()`로 입력된 자료를 스키마를 살펴봤더니 모두 문자열(string)로 들어갔다. 이것을 해결할 옵션 `inferSchema`을 사용해보자.

```python
>>> stock = spark.read.option("header",True).options(inferSchema='True',delimiter=',').csv("stock_full.csv")
>>> stock.show()                                                                
+----------+----+----+----+-----+-----------+---------+----+---+
|      Date|Open|High| Low|Close|     Volume|Adj_Close|code| id|
+----------+----+----+----+-----+-----------+---------+----+---+
|2000-01-04|2658|3110|2658|15552|   898582.0|     2752|  20|  1|
|2000-01-05|3167|3572|2940|16495|  3865389.0|     2918|  20|  2|
|2000-01-06|3299|3468|2808|14327|  1382353.0|     2535|  20|  3|
|2000-01-07|2978|3289|2827|16448|  2402938.0|     2910|  20|  4|
|2000-01-10|3638|3694|3157|16024|  3993227.0|     2835|  20|  5|
|2000-01-11|3204|3487|3110|16354|  1596654.0|     2893|  20|  6|
|2000-01-12|3289|3760|3204|18804|  3941243.0|     3327|  20|  7|
|2000-01-13|3770|4203|3468|17532|1.1397248E7|     3102|  20|  8|
|2000-01-14|3506|3506|2988|14940|  4794737.0|     2643|  20|  9|
|2000-01-17|2950|3204|2903|14798|  3258024.0|     2618|  20| 10|
|2000-01-18|3016|3110|2827|14233|  2067694.0|     2518|  20| 11|
|2000-01-19|2856|2978|2676|13479|  2006692.0|     2385|  20| 12|
|2000-01-20|2639|2714|2545|13007|  1070448.0|     2301|  20| 13|
|2000-01-21|2639|2865|2554|13290|  2250169.0|     2351|  20| 14|
|2000-01-24|2714|2865|2667|13479|  1558462.0|     2385|  20| 15|
|2000-01-25|2667|2771|2582|12913|   939957.0|     2285|  20| 16|
|2000-01-26|2658|2676|2545|13054|   772335.0|     2310|  20| 17|
|2000-01-27|2639|2969|2610|13667|  3375253.0|     2418|  20| 18|
|2000-01-28|2827|2827|2667|13761|  1084770.0|     2435|  20| 19|
|2000-01-31|2714|2865|2714|13573|  1197226.0|     2401|  20| 20|
+----------+----+----+----+-----+-----------+---------+----+---+
only showing top 20 rows

>>> stock.printSchema()
root
 |-- Date: string (nullable = true)
 |-- Open: integer (nullable = true)
 |-- High: integer (nullable = true)
 |-- Low: integer (nullable = true)
 |-- Close: integer (nullable = true)
 |-- Volume: double (nullable = true)
 |-- Adj_Close: integer (nullable = true)
 |-- code: integer (nullable = true)
 |-- id: integer (nullable = true)

>>> 
```

스키마가 적절하게 잘 들어갔는지 `stock.printSchema()`으로 확인해보니 적절하게 잘 들어갔다. 앞에

```python

from pyspark.sql.types import StructType, StructField
from pyspark.sql.types import StringType, IntegerType, DateType

schema = StructType()  \
     .add("Date", DateType(),True) \
     .add("Open",IntegerType(),True) \
     .add("High",IntegerType(),True) \
     .add("Low",IntegerType(),True) \
     .add("Close",IntegerType(),True) \
     .add("Volume",IntegerType(),True) \
     .add("Adj_Close",IntegerType(),True) \
     .add("code",StringType(),True) \
     .add("id",IntegerType(),True)

stock = spark.read.format("csv").option("header", True).schema(schema).load("stock_full.csv")
```

지금까지 한 것을 정리해서 차례로 처리하면 다음과 같은 결과가 나올 것이다.

```python
>>> park = SparkSession \
...      .builder \
...      .appName("Python Spark SQL basic example") \
...      .config("spark.some.config.option", "some-value") \
...      .getOrCreate()
>>> from pyspark.sql.types import StructType, StructField
>>> from pyspark.sql.types import StringType, IntegerType, DateType
>>> 
>>> schema = StructType()  \
...      .add("Date", DateType(),True) \
...      .add("Open",IntegerType(),True) \
...      .add("High",IntegerType(),True) \
...      .add("Low",IntegerType(),True) \
...      .add("Close",IntegerType(),True) \
...      .add("Volume",IntegerType(),True) \
...      .add("Adj_Close",IntegerType(),True) \
...      .add("code",StringType(),True) \
...      .add("id",IntegerType(),True)
>>> stock = spark.read.format("csv").option("header", True).schema(schema).load("stock_full.csv")
2021-01-13 08:31:05,361 WARN util.SizeEstimator: Failed to check whether UseCompressedOops is set; assuming yes
>>> stock.printSchema()
root
 |-- Date: date (nullable = true)
 |-- Open: integer (nullable = true)
 |-- High: integer (nullable = true)
 |-- Low: integer (nullable = true)
 |-- Close: integer (nullable = true)
 |-- Volume: integer (nullable = true)
 |-- Adj_Close: integer (nullable = true)
 |-- code: string (nullable = true)
 |-- id: integer (nullable = true)

>>> stock.show() 
+----------+----+----+----+-----+--------+---------+------+---+                 
|      Date|Open|High| Low|Close|  Volume|Adj_Close|  code| id|
+----------+----+----+----+-----+--------+---------+------+---+
|2000-01-04|2658|3110|2658|15552|  898582|     2752|000020|  1|
|2000-01-05|3167|3572|2940|16495| 3865389|     2918|000020|  2|
|2000-01-06|3299|3468|2808|14327| 1382353|     2535|000020|  3|
|2000-01-07|2978|3289|2827|16448| 2402938|     2910|000020|  4|
|2000-01-10|3638|3694|3157|16024| 3993227|     2835|000020|  5|
|2000-01-11|3204|3487|3110|16354| 1596654|     2893|000020|  6|
|2000-01-12|3289|3760|3204|18804| 3941243|     3327|000020|  7|
|2000-01-13|3770|4203|3468|17532|11397248|     3102|000020|  8|
|2000-01-14|3506|3506|2988|14940| 4794737|     2643|000020|  9|
|2000-01-17|2950|3204|2903|14798| 3258024|     2618|000020| 10|
|2000-01-18|3016|3110|2827|14233| 2067694|     2518|000020| 11|
|2000-01-19|2856|2978|2676|13479| 2006692|     2385|000020| 12|
|2000-01-20|2639|2714|2545|13007| 1070448|     2301|000020| 13|
|2000-01-21|2639|2865|2554|13290| 2250169|     2351|000020| 14|
|2000-01-24|2714|2865|2667|13479| 1558462|     2385|000020| 15|
|2000-01-25|2667|2771|2582|12913|  939957|     2285|000020| 16|
|2000-01-26|2658|2676|2545|13054|  772335|     2310|000020| 17|
|2000-01-27|2639|2969|2610|13667| 3375253|     2418|000020| 18|
|2000-01-28|2827|2827|2667|13761| 1084770|     2435|000020| 19|
|2000-01-31|2714|2865|2714|13573| 1197226|     2401|000020| 20|
+----------+----+----+----+-----+--------+---------+------+---+
only showing top 20 rows

>>> 
```
