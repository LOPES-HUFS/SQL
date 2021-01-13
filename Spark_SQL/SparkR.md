# R을 이용해서 Spark SQL를 다루기

## 기초

`spark`(이하 스파크)가 설치되었다면 바로 터미널에서 `sparkR`이라고 타이핑하면 실행할 수 있습니다.

```R
(base) jovyan@efb2ccb332bf:~$ sparkR

R version 4.0.3 (2020-10-10) -- "Bunny-Wunnies Freak Out"
Copyright (C) 2020 The R Foundation for Statistical Computing
Platform: x86_64-conda-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

[Previously saved workspace restored]

Launching java with spark-submit command /usr/local/spark/bin/spark-submit   "sparkr-shell" /tmp/RtmpR6ieXA/backend_port10a8481b389a
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/usr/local/spark-3.0.1-bin-hadoop3.2/jars/spark-unsafe_2.12-3.0.1.jar) to constructor java.nio.DirectByteBuffer(long,int)
WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
21/01/02 07:23:13 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).

Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.0.1
      /_/


SparkSession available as 'spark'.
> stock_full <- read.df("stock_full.csv", "csv")
> stock_full <- read.df("stock_full.csv", "csv", header = "true", inferSchema = "true", na.strings = "NA")
> printSchema(stock_full)
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
> stock_full
SparkDataFrame[Date:string, Open:int, High:int, Low:int, Close:int, Volume:double, Adj_Close:int, code:int, id:int]
>
```
