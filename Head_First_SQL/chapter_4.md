
# 4장 정규화

```sql
CREATE DATABASE fish_list;
USE fish_list;
```

```sql
MariaDB [(none)]> CREATE DATABASE fish_list;
Query OK, 1 row affected (0.013 sec)

MariaDB [(none)]> USE fish_list;
Database changed
MariaDB [fish_list]>
```

```sql
CREATE TABLE fish_info(common VARCHAR(30), species VARCHAR(30), location VARCHAR(30), weight VARCHAR(30));
```

```bash
MariaDB [fish_list]> CREATE TABLE fish_info(common VARCHAR(30), species VARCHAR(30), location VARCHAR(30), weight VARCHAR(30));
Query OK, 0 rows affected (0.060 sec)
```

```sql
INSERT INTO fish_info  
VALUES
('bass, largemouth',  'M. sakmoides', 'Montgomery Lake, GA', '22 lb 4 oz'),
('walleye',  'S. vitreus', 'Old Hickory Lake, TN', '25 lb 0 oz'),
('trout, cutthroat',  'O. Clarki', 'Pyramid Lake, NV', '41 lb 0 oz'),
('perch, yellow',  'P. Flavescens', 'Boredentown, NJ', '4 lb 3 oz'),
('bluegill',  'L. Macrochirus', 'Ketana Lake, AL', '4 lb 13 oz'),
('gar, longnose',  'L. Osseus', 'Trinity River, TX', '50 lb 5 oz'),
('crappie, white',  'P. annularis', 'Enid Dam, MS', '5 lb 3 oz'),
('pickerel, grass',  'E. americanus', 'Dewart Lake, IN', '1 lb 0 oz'),
('goldfish',  'C. auratus', 'Lake Hodges, CA', '6 lb 10 oz'),
('salmon, chinook',  'O. Tshawytscha', 'Kenai River, AK', '97 lb 4 oz');

```

```bash
MariaDB [fish_list]> INSERT INTO fish_info  
    -> VALUES
    -> ('bass, largemouth',  'M. sakmoides', 'Montgomery Lake, GA', '22 lb 4 oz'),
    -> ('walleye',  'S. vitreus', 'Old Hickory Lake, TN', '25 lb 0 oz'),
    -> ('trout, cutthroat',  'O. Clarki', 'Pyramid Lake, NV', '41 lb 0 oz'),
    -> ('perch, yellow',  'P. Flavescens', 'Boredentown, NJ', '4 lb 3 oz'),
    -> ('bluegill',  'L. Macrochirus', 'Ketana Lake, AL', '4 lb 13 oz'),
    -> ('gar, longnose',  'L. Osseus', 'Trinity River, TX', '50 lb 5 oz'),
    -> ('crappie, white',  'P. annularis', 'Enid Dam, MS', '5 lb 3 oz'),
    -> ('pickerel, grass',  'E. americanus', 'Dewart Lake, IN', '1 lb 0 oz'),
    -> ('goldfish',  'C. auratus', 'Lake Hodges, CA', '6 lb 10 oz'),
    -> ('salmon, chinook',  'O. Tshawytscha', 'Kenai River, AK', '97 lb 4 oz');
Query OK, 10 rows affected (0.046 sec)
Records: 10  Duplicates: 0  Warnings: 0
```

```sql
CREATE TABLE fish_records(first_name VARCHAR(30), last_name VARCHAR(30), common VARCHAR(30), location VARCHAR(30),state VARCHAR(2), weight VARCHAR(30), date VARCHAR(30));

```

```sql
INSERT INTO fish_records  
VALUES
('George', 'Perry', 'bass, largemouth', 'Montgomery Lake', 'GA', '22 lb 4 oz', '6/2/1932'),
('Mabry', 'Harper', 'walleye', 'Old Hickory Lake', 'TN', '25 lb 0 oz', '8/2/1960'),
('John', 'Skimmerhorn', 'trout, cutthroat', 'Pyramid Lake', 'NV', '41 lb 0 oz', '12/1/1925'),
('C.C.', 'Abbot', 'perch, yellow', 'Boredentown', 'NJ', '4 lb 3 oz', '5/1/1865'),
('T.S.', 'Hudson', 'bluegill', 'Ketana Lake', 'AL', '4 lb 13 oz', '4/9/1950'),
('Townsend', 'Miller', 'gar, longnose', 'Trinity River', 'TX', '50 lb 5 oz', '7/30/1954'),
('Fred', 'Bright', 'crappie, white', 'Enid Dam', 'MS', '5 lb 3 oz', '7/31/1957'),
('Mike', 'Berg', 'pickerel, grass', 'Dewart Lake', 'IN', '1 lb 0 oz', '6/9/1990'),
('Flprentino', 'Abena', 'goldfish', 'Lake Hodges', 'CA', '6 lb 10 oz', '4/17/1996'),
('Les', 'Anderson', 'salmon, chinook', 'Kenai River', 'AK', '97 lb 4 oz', '5/17/1985');

```

```bash
MariaDB [fish_list]> INSERT INTO fish_records  
    -> VALUES
    -> ('George', 'Perry', 'bass, largemouth', 'Montgomery Lake', 'GA', '22 lb 4 oz', '6/2/1932'),
    -> ('Mabry', 'Harper', 'walleye', 'Old Hickory Lake', 'TN', '25 lb 0 oz', '8/2/1960'),
    -> ('John', 'Skimmerhorn', 'trout, cutthroat', 'Pyramid Lake', 'NV', '41 lb 0 oz', '12/1/1925'),
    -> ('C.C.', 'Abbot', 'perch, yellow', 'Boredentown', 'NJ', '4 lb 3 oz', '5/1/1865'),
    -> ('T.S.', 'Hudson', 'bluegill', 'Ketana Lake', 'AL', '4 lb 13 oz', '4/9/1950'),
    -> ('Townsend', 'Miller', 'gar, longnose', 'Trinity River', 'TX', '50 lb 5 oz', '7/30/1954'),
    -> ('Fred', 'Bright', 'crappie, white', 'Enid Dam', 'MS', '5 lb 3 oz', '7/31/1957'),
    -> ('Mike', 'Berg', 'pickerel, grass', 'Dewart Lake', 'IN', '1 lb 0 oz', '6/9/1990'),
    -> ('Flprentino', 'Abena', 'goldfish', 'Lake Hodges', 'CA', '6 lb 10 oz', '4/17/1996'),
    -> ('Les', 'Anderson', 'salmon, chinook', 'Kenai River', 'AK', '97 lb 4 oz', '5/17/1985');
Query OK, 10 rows affected (0.005 sec)
Records: 10  Duplicates: 0  Warnings: 0
```

## 연필을 깎으며

```sql
select * FROM fish_info WHERE location LIKE '%NJ';
select * FROM fish_records WHERE state='NJ';
```

```bash
MariaDB [fish_list]> select * FROM fish_info WHERE location LIKE '%NJ';
+---------------+---------------+-----------------+-----------+
| common        | species       | location        | weight    |
+---------------+---------------+-----------------+-----------+
| perch, yellow | P. Flavescens | Boredentown, NJ | 4 lb 3 oz |
+---------------+---------------+-----------------+-----------+
1 row in set (0.010 sec)
```

```bash
MariaDB [fish_list]> select * FROM fish_records WHERE state='NJ';
+------------+-----------+---------------+-------------+-------+-----------+----------+
| first_name | last_name | common        | location    | state | weight    | date     |
+------------+-----------+---------------+-------------+-------+-----------+----------+
| C.C.       | Abbot     | perch, yellow | Boredentown | NJ    | 4 lb 3 oz | 5/1/1865 |
+------------+-----------+---------------+-------------+-------+-----------+----------+
1 row in set (0.006 sec)
```

SQL은 관계형 데이터 베이스 관리를 위한 언어이다. 관계형이란 말은 테이블을 설계할 때 여러 테이블의 열들이 서로 어떠한 연관이 있는지를 고려해야한다는 말이다.

테이블의 생성과정은 3단계로 구성된다. 첫 째, 테이블로 표현하려는 것을 선택한다. 둘 째, 테이블을 사용하여 얻어야 하는 정보들의 목록을 정리한다. 셋 째, 테이블을 정리한 정보들의 조각으로 나누어 구성한다.

데이터가 충분히 열로 잘 쪼개져 있는 것을 데이터가 충분히 원자적이다라고 표현한다.

## 원자적 데이터

```sql
CREATE DATABASE pizza;
USE pizza;

CREATE TABLE pizza_deliveries(order_number INT, address VARCHAR(30));
```

```bash
MariaDB [pizza]> CREATE TABLE pizza_deliveries(order_number INT, address VARCHAR(30));
Query OK, 0 rows affected (0.016 sec)
```

```sql
INSERT INTO pizza_deliveries  
VALUES
('246',  '59 N. Ajax Rapids'),
('247',  '849 SQL Street'),
('248',  '2348 E. PMP Plaza'),
('249',  '1978 HTML Heights'),
('250',  '24 S. Sevlets Springs'),
('251',  '807 Infinite Circle'),
('252',  '32 Disign Patterns Plaza'),
('253',  '9208 S. Java Ranch'),
('254',  '4653 W. EJB Estate'),
('255',  '8678 OOA&D Orchard');

```

```bash
MariaDB [pizza]> INSERT INTO pizza_deliveries  
    -> VALUES
    -> ('246',  '59 N. Ajax Rapids'),
    -> ('247',  '849 SQL Street'),
    -> ('248',  '2348 E. PMP Plaza'),
    -> ('249',  '1978 HTML Heights'),
    -> ('250',  '24 S. Sevlets Springs'),
    -> ('251',  '807 Infinite Circle'),
    -> ('252',  '32 Disign Patterns Plaza'),
    -> ('253',  '9208 S. Java Ranch'),
    -> ('254',  '4653 W. EJB Estate'),
    -> ('255',  '8678 OOA&D Orchard');
Query OK, 10 rows affected (0.009 sec)
Records: 10  Duplicates: 0  Warnings: 0
```

```sql
SELECT address FROM pizza_deliveries WHERE order_number = 252;
```

```bash
MariaDB [pizza]> SELECT address FROM pizza_deliveries WHERE order_number = 252;
+--------------------------+
| address                  |
+--------------------------+
| 32 Disign Patterns Plaza |
+--------------------------+
1 row in set (0.005 sec)
```

```sql
CREATE DATABASE real_estates;
USE real_estates;

CREATE TABLE real_estate(street_number INT, street_name VARCHAR(30), property_type VARCHAR(30), price INT);

```

```bash
MariaDB [real_estates]> CREATE TABLE real_estate(street_number INT, street_name VARCHAR(30), property_type VARCHAR(30), price INT);
Query OK, 0 rows affected (0.015 sec)
```

```sql
INSERT INTO real_estate  
VALUES
('59', 'N. Ajax Rapids', 'condo',189000),
('849', 'SQL Street', 'apartment', 109000),
('2348', 'E. PMP Plaza', 'house', 355000),
('1978', 'HTML Heights', 'apartment', 134000),
('24', 'S. Sevlets Springs', 'house', 355000),
('807', 'Infinite Circle', 'condo', 143900),
('32', 'Disign Patterns Plaza', 'house', 465000),
('9208', 'S. Java Ranch', 'house', 699000),
('4653', 'SQL Street', 'apartment', 115000),
('8678', 'OOA&D Orchard', 'house', 355000) ;

```

```bash
MariaDB [real_estates]> INSERT INTO real_estate  
    -> VALUES
    -> ('59', 'N. Ajax Rapids', 'condo',189000),
    -> ('849', 'SQL Street', 'apartment', 109000),
    -> ('2348', 'E. PMP Plaza', 'house', 355000),
    -> ('1978', 'HTML Heights', 'apartment', 134000),
    -> ('24', 'S. Sevlets Springs', 'house', 355000),
    -> ('807', 'Infinite Circle', 'condo', 143900),
    -> ('32', 'Disign Patterns Plaza', 'house', 465000),
    -> ('9208', 'S. Java Ranch', 'house', 699000),
    -> ('4653', 'SQL Street', 'apartment', 115000),
    -> ('8678', 'OOA&D Orchard', 'house', 355000) ;
Query OK, 10 rows affected (0.007 sec)
Records: 10  Duplicates: 0  Warnings: 0
```

```sql
SELECT price, property_type FROM real_estate WHERE street_name = 'SQL Street';
```

```bash
MariaDB [real_estates]> SELECT price, property_type FROM real_estate WHERE street_name = 'SQL Street';
+--------+---------------+
| price  | property_type |
+--------+---------------+
| 109000 | apartment     |
| 115000 | apartment     |
+--------+---------------+
2 rows in set (0.001 sec)
```

원자적 데이터의 규칙: 원자적 데이터로 구성된 열은 그 열에 같은 타입의 데이터 여러개를 가질 수 없다. 원자적 데이터로 구성된 테이블은 같은 타입의 데이터를 여러 열에 가질 수 없다.

## 정규화

테이블을 정규화 한다는 것은 테이블들이 표준 규칙을 따른다는 의미이며, 원자적 데이터로 테이블을 구성하는 것이 정규화의 첫 걸음이다. 테이블 정규화를 하면 중복 데이터가 없어서 데이터베이스의 크기를 줄여준다. 또한, 찾아야할 데이터가 적어 쿼리가 더 빨라진다. 테이블이 작은 경우에도 커질 경우를 고려하여 정규 테이블을 사용하는 것이 좋다.

정규 테이블이 되려면, 데이터가 제 1 정규형(1NF)의 형태를 가져야 한다. 제 1 정규형은 각 행의 데이터들은 원자적 값을 가져야한다. 각 행은 유일무이한 식별자인 기본키를 가져야 한다.

## 기본키 규칙

기본키는 각 행을 다른 행과 구분하는 열이다. 기본키를 구성하는데 필요한 4가지 규칙이 있다. 첫 째 기본키는 NULL 값이 될 수 없다. NULL 값은 유일무이하지 않기 때문이다. 둘 째 기본키는 행이 삽입될 때 값이 있어야 한다. 셋 째 기본키는 간결해야한다. 유일무이한 정보를 가지고 있어야한다. 넷 째 기본키의 값은 변경 불가능한 값이여야 한다. 유일무이 하기에 변경 가능하면 중복이 생길 수도 있기에 변경 불가능 해야 한다.
기본키를 만들기 위한 가장 좋은 방법은 기본키만을 위한 열을 새로 만드는 방법이다.

SHOW CREATE TABLE 명령어를 사용하면 이전에 만들었던 테이블을 만든 코드를 반환해주기에 테이블을 다시 만들 수 있다.

```sql
USE gregs_list;

SHOW CREATE TABLE my_contacts;
```

```bash

MariaDB [gregs_list]> SHOW CREATE TABLE my_contacts;

+-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table       | Create Table                                                                                                                                                                                                                                                                                                                                                                                               |
+-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| my_contacts | CREATE TABLE `my_contacts` (
  `last_name` varchar(30) default NULL,
  `first_name` varchar(20) default NULL,
  `email` varchar(50) default NULL,
  `gender` char(1) default NULL,
  `birthday` date default NULL,
  `profession` varchar(50) default NULL,
  `location` varchar(50) default NULL,
  `status` varchar(20) default NULL,
  `interests` varchar(100) default NULL,
  `seeking` varchar(100) default NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 |
+-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.008 sec)
```

이제 기본키가 있는 테이블을 생성해 보자. 아래와 같이 contact_id 열을 추가한 후 PRIMARY KEY 명령어로 기본키를 만들 수 있다.

```sql
DROP TABLE my_contacts;

CREATE TABLE my_contacts(
    contact_id INT NOT NULL,
    last_name varchar(30) default NULL,
    first_name varchar(20) default NULL,
    email varchar(50) default NULL,
    gender char(1) default NULL,
    birthday date default NULL,
    profession varchar(50) default NULL,
    location varchar(50) default NULL,
    status varchar(20) default NULL,
    interests varchar(100) default NULL,
    seeking varchar(100) default NULL,
    PRIMARY KEY (contact_id)
    );
```

```bash
MariaDB [gregs_list]> CREATE TABLE my_contacts(
    ->     contact_id INT NOT NULL,
    ->     last_name varchar(30) default NULL,
    ->     first_name varchar(20) default NULL,
    ->     email varchar(50) default NULL,
    ->     gender char(1) default NULL,
    ->     birthday date default NULL,
    ->     profession varchar(50) default NULL,
    ->     location varchar(50) default NULL,
    ->     status varchar(20) default NULL,
    ->     interests varchar(100) default NULL,
    ->     seeking varchar(100) default NULL,
    ->     PRIMARY KEY (contact_id)
    ->     );
Query OK, 0 rows affected (0.013 sec)
```

id 1부터 n까지 숫자를 자동증가하게 만들려면 명령어 AUTO_INCREMENT를 뒤에 추가하면 된다.

```sql
DROP TABLE my_contacts;

CREATE TABLE my_contacts(
    contact_id INT NOT NULL AUTO_INCREMENT,
    last_name varchar(30) default NULL,
    first_name varchar(20) default NULL,
    email varchar(50) default NULL,
    gender char(1) default NULL,
    birthday date default NULL,
    profession varchar(50) default NULL,
    location varchar(50) default NULL,
    status varchar(20) default NULL,
    interests varchar(100) default NULL,
    seeking varchar(100) default NULL,
    PRIMARY KEY (contact_id)
    );
```

## 연습문제

```sql
CREATE TABLE name_lists(
    id INT NOT NULL AUTO_INCREMENT,
    first_name varchar(20) default NULL,
    last_name varchar(30) default NULL,
    PRIMARY KEY (id)
);
```

```bash

MariaDB [gregs_list]> CREATE TABLE name_lists(
    ->     id INT NOT NULL AUTO_INCREMENT,
    ->     first_name varchar(20) default NULL,
    ->     last_name varchar(30) default NULL,
    ->     PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.015 sec)
```

```sql
INSERT INTO name_lists (id, first_name, last_name)
VALUES (NULL, 'Marcia', 'Bredy');

INSERT INTO name_lists (id, first_name, last_name)
VALUES (1, 'Jan', 'Bredy');

INSERT INTO name_lists
VALUES (2, 'Bobby', 'Bredy');

INSERT INTO name_lists (first_name, last_name)
VALUES ('Cindy', 'Bredy');

INSERT INTO name_lists (id, first_name, last_name)
VALUES (99, 'Peter', 'Bredy');
```

위에서 1번 코드는 작동되며, 기본키가 NULL로 들어갈 수 있는 이유는 AUTO_INCREMENT 때문에 자동으로 기본키가 1로 지정되기 때문이다. 2번 코드의 경우 에러가 발생한다. 왜냐하면, 기본키로 이미 1이 있는 상태에서 기본키가 1인 데이터를 추가할 수 없기 때문이다. 3번 코드의 경우 작동이 잘 된다. 기본키가 2인 데이터는 기존 테이블에 없기 때문이다.

```bash
MariaDB [gregs_list]> INSERT INTO name_lists (id, first_name, last_name)
    -> VALUES (NULL, 'Marcia', 'Bredy');
Query OK, 1 row affected (0.006 sec)

MariaDB [gregs_list]> INSERT INTO name_lists (id, first_name, last_name)
    -> VALUES (1, 'Jan', 'Bredy');
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'

MariaDB [gregs_list]> INSERT INTO name_lists
    -> VALUES (2, 'Bobby', 'Bredy');
Query OK, 1 row affected (0.005 sec)

MariaDB [gregs_list]> INSERT INTO name_lists (first_name, last_name)
    -> VALUES ('Cindy', 'Bredy');
Query OK, 1 row affected (0.004 sec)

MariaDB [gregs_list]> INSERT INTO name_lists (id, first_name, last_name)
    -> VALUES (99, 'Peter', 'Bredy');
Query OK, 1 row affected (0.004 sec)
```

```sql
SELECT *  FROM name_lists;
```

```bash
MariaDB [gregs_list]> SELECT *  FROM name_lists;
+----+------------+-----------+
| id | first_name | last_name |
+----+------------+-----------+
|  1 | Marcia     | Bredy     |
|  2 | Bobby      | Bredy     |
|  3 | Cindy      | Bredy     |
| 99 | Peter      | Bredy     |
+----+------------+-----------+
4 rows in set (0.001 sec)
```

## 기존 테이블에 기본키 추가하기 ALTER

아래 코드는 1장에서 만든 DROP TABLE 명령어를 사용하지 않고 기존 테이블에 새로운 열만 추가하는 방법이다. FIRST는 첫번째 열로 추가해준다는 명령어이며, 따로 열 순서를 지정하지 않을 경우 마지막 줄로 들어간다.

```sql
ALTER TABLE my_contacts
ADD COLUMN contact_id INT NOT NULL AUTO_INCREMENT FIRST,
ADD PRIMARY KEY (contact_id);
```

```bash
MariaDB [gregs_list]> ALTER TABLE my_contacts
    -> ADD COLUMN contact_id INT NOT NULL AUTO_INCREMENT FIRST,
    -> ADD PRIMARY KEY (contact_id);
Query OK, 0 rows affected (0.043 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

기존 테이블에 들어 있던 데이터에 id 열이 생기고 id가 1이 부여된 것을 확인할 수 있습니다.

```sql
SELECT * FROM my_contacts;
```

```bash
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+------------+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| contact_id | last_name | first_name | email                            | gender | birthday   | profession       | location      | status | interests          | seeking               |
+------------+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
|          1 | Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
+------------+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
1 row in set (0.001 sec)
```
