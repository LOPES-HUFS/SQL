# R으로 Docker로 구성한 MariaDB 사용하기

이 글은 R로 MariaDB(이하 마리아디비)에 접근하기 위한 글입니다. 컴퓨터에 직접 마리아디비를 설치하고 이용하는 것이 아니라, 도커에 설치한 마리아디비를 이용하고자 합니다. 직접 설치하신 마리아디비를 사용하고자 하실 분들이나 외부 다른 컴퓨터에 설치된 것을 사용하고자 하실 분들도 아래 연결 설정값을 적절하게 입력하신다면, 그 글을 따라가시는데 문제가 없을 것 같습니다.

## 본격적으로 시작하기에 앞서

참고로 여기에서는 이미 Docker로 마리아디비를 설치했다고 가정할 것입니다. 설치하는 방법은 다음 링크를 참고하세요. [Docker를 사용해서 MariaDB을 설치하기](http://www.epistemology.pe.kr/2020/09/26/1293) 그리고 이미 다음과 같은 `sql`명령을 사용하여 `gregs_list`이라는 DATABASE를 만들고, 그 안에 `my_contacts`라는 테이블을 만들고 두 사람의 자료를 입력했습니다.

```sql
CREATE DATABASE gregs_list;

USE gregs_list;

CREATE TABLE my_contacts(
    last_name VARCHAR(30),
    first_name VARCHAR(20),
    email VARCHAR(50),
    birthday Date,
    profession VARCHAR(50),
    location VARCHAR(50),
    status VARCHAR(20),
    interests VARCHAR(100),
    seeking VARCHAR(100)
);

INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);

INSERT INTO my_contacts(
    first_name, email,  profession, location
)
VALUES (
    'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton, NJ'
);
```

위에서 작업한 결과로 gregs_list 데이터베이스의 my_contacts라는 테이블에는 다음과 같이 두 사람의 자료가 들어있습니다.

```sql
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| last_name | first_name | email                            | gender | birthday   | profession       | location      | status | interests          | seeking               |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
| NULL      | Pat        | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton, NJ | NULL   | NULL               | NULL                  |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
2 rows in set (0.004 sec)
```

## `RMariaDB` 설치하기

R 에서 MariaDB에 접근하기 위해서는 `odbc` 패키지나 `RMariaDB`를 사용해야 합니다. 이에 관련된 내용은 [R Statistical Programming Using MariaDB as the Background Database - MariaDB Knowledge Base](https://mariadb.com/kb/en/r-statistical-programming-using-mariadb-as-the-background-database/#data-transfer-between-r-and-mariadb)을 참고하시면 됩니다. 여기서는 `RMariaDB`를 사용하도록 하겠습니다. 앞 링크에서 RMariaDB을 다음과 같이 소개하고 있습니다.
> “RMariaDB” R library, is a modern 'MariaDB' client based on 'Rcpp'.

사용하기 위에서는 당연히 패키지를 설치해야 합니다.

```R
install.packages("RMariaDB")
```

설치를 했으면 사용하시는 방법은 다음과 같습니다. 앞에서 말한 도커에 설치된 마리아디비의 정보를 그대로 입력했습니다. 여기서는 직접 입력하고 있지만, 실전에서 사용하시려면 [Database Interface and MariaDB Driver • RMariaDB](https://rmariadb.r-dbi.org/#mariadb-configuration-file)에서처럼 별도 파일에 입력하여 사용하시는 것이 좋을 것 습니다. 마지막 줄에서 `dbListTables(con)`을 통해서 `gregs_list`이라는 데이터베이스가 가지고 있는 테이블을 보여주게 됩니다.

```R
library(RMariaDB)

con <- dbConnect(
  drv = RMariaDB::MariaDB(),
  username = "root",
  password = "RT27hDosK",
  host = "0.0.0.0",
  port = 3306,
  dbname = "gregs_list"
)

dbListTables(con)
```

앞의 코드가 실행된 결과는 다음과 같습니다. 현재 앞에서 입력한 `my_contacts` 테이블도 볼 수 있습니다.

```R
> library(RMariaDB)
> con <- dbConnect(
+   drv = RMariaDB::MariaDB(),
+   username = "root",
+   password = "RT27hDosK",
+   host = "0.0.0.0",
+   port = 3306,
+   dbname = "gregs_list"
+ )
>
> con
<MariaDBConnection>
  Host:    0.0.0.0
  Server:  
  Client:  
> dbListTables(con)
[1] "movie_table"   "easy_drinks"   "my_contacts"   "doughnut_list" "users"         "clown_info"
```

## R로 데이터베이스에 입력한 테이블을 살펴보기

이제 본격적으로 앞에서 데이터베이스에 저장한 테이블을 살펴보겠습니다. `dbReadTable()`을 사용하면 쉽게 테이블 내용을 확인하실 수 있습니다.

```R
dbReadTable(con, "my_contacts")
```

윗 코드를 실행하면 다음과 같습니다.

```R
> dbReadTable(con, "my_contacts")
  contact_id phone last_name first_name                            email gender   birthday       profession      location status          interests               seeking
1          1  <NA>  Anderson    Jillian jill_anderson@breakneckpizza.com      F 1980-09-05 Technical Writer Palo Alto, CA Single Kayaking, Reptiles Relationship, Friends
2          2  <NA>      <NA>        Pat       patpost@breakneckpizza.com   <NA>       <NA>    Postal Worker Princeton, NJ   <NA>               <NA>                  <NA>
```

`SQL`을 사용하려면 다음과 같이 하시면 됩니다.

```R
res <- dbSendQuery(con, "SELECT * FROM my_contacts")
```

위의 코드를 실행하면 다음과 같습니다. `dbClearResult()`을 이용해서 사용하신 다음에 정리하고 마무리하지 않으시면 에러가 날 수 있습니다. 주의하세요!

```R
> res <- dbSendQuery(con, "SELECT * FROM my_contacts")
> res
<MariaDBResult>
  SQL  SELECT * FROM my_contacts
  ROWS Fetched: 0 [incomplete]
       Changed: 0
> dbFetch(res)
  contact_id phone last_name first_name                            email gender   birthday       profession      location status          interests               seeking
1          1  <NA>  Anderson    Jillian jill_anderson@breakneckpizza.com      F 1980-09-05 Technical Writer Palo Alto, CA Single Kayaking, Reptiles Relationship, Friends
2          2  <NA>      <NA>        Pat       patpost@breakneckpizza.com   <NA>       <NA>    Postal Worker Princeton, NJ   <NA>               <NA>                  <NA>
> dbClearResult(res)
```

조금 더 세련되게 할 수도 있습니다.

```R
res <- dbSendQuery(con, "SELECT * FROM my_contacts")
while(!dbHasCompleted(res)){
  chunk <- dbFetch(res, n = 5)
  print(chunk)
  print(nrow(chunk))
}
```

윗 코드를 실행하면 다음과 같습니다.

```R
> dbClearResult(res)
> res <- dbSendQuery(con, "SELECT * FROM my_contacts")
> while(!dbHasCompleted(res)){
+   chunk <- dbFetch(res, n = 5)
+   print(chunk)
+   print(nrow(chunk))
+ }
  contact_id phone last_name first_name                            email gender   birthday       profession      location status          interests               seeking
1          1  <NA>  Anderson    Jillian jill_anderson@breakneckpizza.com      F 1980-09-05 Technical Writer Palo Alto, CA Single Kayaking, Reptiles Relationship, Friends
2          2  <NA>      <NA>        Pat       patpost@breakneckpizza.com   <NA>       <NA>    Postal Worker Princeton, NJ   <NA>               <NA>                  <NA>
[1] 2
```

`tidyverse`를 추가해서 사용하면 좀 더 쉽고 멋있게 처리할 수 있습니다. 다음 코드에서 볼 수 있는 것처럼 SQL를 사용하지 않고 `filter()`와 같은 것을 이용하여 쉽게 테이블에 있는 자료를 처리할 수 있습니다. 마지막 줄에서 `filter()`를 사용하여 first_name 열에 있는 "Pat"이라는 단어가 있는 줄을 뽑아서 보여줍니다.

```R
library(tidyverse)
library(RMariaDB)
library(dplyr, warn.conflicts = FALSE)
con <- dbConnect(
  drv = RMariaDB::MariaDB(),
  username = "root",
  password = "RT27hDosK",
  host = "0.0.0.0",
  port = 3306,
  dbname = "gregs_list"
)
my_contacts <- tbl(con, "my_contacts")
my_contacts
my_contacts %>% filter(first_name == "Pat")
```

윗 코드를 실행하면 다음과 같습니다.

```R
> library(tidyverse)
─ Attaching packages ─────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ─
✔ ggplot2 3.3.0     ✔ purrr   0.3.4
✔ tibble  3.0.1     ✔ dplyr   1.0.0
✔ tidyr   1.1.0     ✔ stringr 1.4.0
✔ readr   1.3.1     ✔ forcats 0.5.0
─ Conflicts ──────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ─
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
> library(RMariaDB)
> library(dplyr, warn.conflicts = FALSE)
> con <- dbConnect(
+   drv = RMariaDB::MariaDB(),
+   username = "root",
+   password = "RT27hDosK",
+   host = "0.0.0.0",
+   port = 3306,
+   dbname = "gregs_list"
+ )
> my_contacts <- tbl(con, "my_contacts")
> my_contacts
# Source:   table<my_contacts> [?? x 12]
# Database: mysql [root@0.0.0.0:NA/gregs_list]
  contact_id phone last_name first_name email                            gender birthday   profession       location      status interests          seeking
       <int> <chr> <chr>     <chr>      <chr>                            <chr>  <date>     <chr>            <chr>         <chr>  <chr>              <chr>
1          1 <NA>  Anderson  Jillian    jill_anderson@breakneckpizza.com F      1980-09-05 Technical Writer Palo Alto, CA Single Kayaking, Reptiles Relationship, Friends
2          2 <NA>  <NA>      Pat        patpost@breakneckpizza.com       <NA>   NA         Postal Worker    Princeton, NJ <NA>   <NA>               <NA>
> my_contacts %>% filter(first_name == "Pat")
# Source:   lazy query [?? x 12]
# Database: mysql [root@0.0.0.0:NA/gregs_list]
  contact_id phone last_name first_name email                      gender birthday   profession    location      status interests seeking
       <int> <chr> <chr>     <chr>      <chr>                      <chr>  <date>     <chr>         <chr>         <chr>  <chr>     <chr>  
1          2 <NA>  <NA>      Pat        patpost@breakneckpizza.com <NA>   NA         Postal Worker Princeton, NJ <NA>   <NA>      <NA>
```

## 참고 자료

- [Database Interface and MariaDB Driver • RMariaDB](https://rmariadb.r-dbi.org/index.html)
- [R Statistical Programming Using MariaDB as the Background Database - MariaDB Knowledge Base](https://mariadb.com/kb/en/r-statistical-programming-using-mariadb-as-the-background-database/#data-transfer-between-r-and-mariadb)
- [A Grammar of Data Manipulation • dplyr](https://dplyr.tidyverse.org/)
- [A dplyr Back End for Databases • dbplyr](https://dbplyr.tidyverse.org/)
