# 5장 ALTER

기존에 그렉의 테이블에 전화번호 열을 추가해보자.

```sql
USE gregs_list;
```

그렉 리스트 라는 데이터 베이스를 불러오면 4장에서 만들었던 my_contacts 테이블이 남아있습니다. 이 테이블에 전화번호 열을 추가하면 된다.

```sql
ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10);
```

열 순서를 first_name 뒤로 배치하려면 위의 코드 대신에 아래 코드를 치면 된다.

```sql
ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10)
AFTER first_name;
```

열 순서를 지정하는 방법들에 대해 알아보자.

첫 번째로 지정하려면 FIRST를 추가하면 된다.

```sql
ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10) FIRST;
```

전화번호 열을 마지막 열에 추가하려면, 아무것도 추가하지 않으면 자동으로 마지막 열로 추가된다. 물론 마지막 열 전의 열인 seeking 다음 열로 순서를 지정해도 마지막 열로 만들어진다.

```sql
ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10);

ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10)
AFTER seeking;
```

전화번호 열을 두 번째 열로 만들고 싶으면 AFTER contact_id 같이 1번째 열 이름 후라는 명령어를 이용하면 된다. 3번째로 만들고 싶으면 마찬가지로 AFTER last_name을 해주면 된다.

```sql
ALTER TABLE my_contacts
ADD COLUMN phone VARCHAR(10)
AFTER contact_id;
```

## ALTER 테이블의 명령어

CHANGE: 테이블 내에 열 이름과 데이터 타입을 변경할 수 있다.
MODIFY: 테이블 내에 열의 데이터 타입이나 위치를 변경할 수 있다.
ADD: 테이블 내에 새로운 열을 추가할 수 있다.
DROP: 테이블 내에 열을 제거한다.

CHANGE, MODIFY, DROP 명령어의 경우 데이터가 변경되거나 삭제되기 때문에 데이터 손실이 발생할 수 있다.

```sql
CREATE TABLE projekts(
    number INT(11),
    desciptionofproj VARCHAR(50),
    contractoronjob VARCHAR(10);
)

INSERT INTO projekts(
    VALUES
        (1, 'outside house painting', 'Murphy'),
        (2, 'kitchen remodel', 'Valdez'),
        (3, 'wood floor installation', 'Keller'),
        (4, 'roofing', 'Jackson')
);
```

```bash
MariaDB [gregs_list]> CREATE TABLE projekts(
    ->     number INT(11),
    ->     desciptionofproj VARCHAR(50),
    ->     contractoronjob VARCHAR(10)
    -> )
    -> ;
Query OK, 0 rows affected (0.016 sec)

MariaDB [gregs_list]> INSERT INTO projekts(
    ->     VALUES
    ->         (1, 'outside house painting', 'Murphy'),
    ->         (2, 'kitchen remodel', 'Valdez'),
    ->         (3, 'wood floor installation', 'Keller'),
    ->         (4, 'roofing', 'Jackson')
    -> );
Query OK, 4 rows affected (0.004 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

만들어진 테이블을 확인한다.

```sql
MariaDB [gregs_list]> DESC projekts;
+------------------+-------------+------+-----+---------+-------+
| Field            | Type        | Null | Key | Default | Extra |
+------------------+-------------+------+-----+---------+-------+
| number           | int(11)     | YES  |     | NULL    |       |
| desciptionofproj | varchar(50) | YES  |     | NULL    |       |
| contractoronjob  | varchar(10) | YES  |     | NULL    |       |
+------------------+-------------+------+-----+---------+-------+
3 rows in set (0.004 sec)
```

RENAME TO: 테이블의 이름을 바꾸는 방법이다.

```sql
ALTER TABLE projekts
RENAME TO project_list;
```

RENAME TO 명령어를 사용해 테이블 이름을 프로젝트 리스트로 변경해준다.

```bash
MariaDB [gregs_list]> ALTER TABLE projekts
    -> RENAME TO project_list;
Query OK, 0 rows affected (0.008 sec)
```

이제 이 테이블에 고유 아이디, 시작일, 계약회사의 이름과 전화번호, 예상 금액, 프로젝트 내용 설명 등의 열들을 추가하거나 기존의 열을 변경해주어야 한다. 기존에 있는 열에 프로젝트 내용 설명, 계약 회사의 이름, 고유 아이디로 사용할 숫자는 존재하고 있다. 따라서 이 3가지 열을 알맞은 이름과 역할로 변경해주어야 한다.

```sql
ALTER TABLE project_list
CHANGE COLUMN number proj_id INT NOT NULL AUTO_INCREMENT,
ADD PRIMARY KEY (proj_id);
```

```bash
MariaDB [gregs_list]> ALTER TABLE project_list
    -> CHANGE COLUMN number proj_id INT NOT NULL AUTO_INCREMENT,
    -> ADD PRIMARY KEY (proj_id);
Query OK, 4 rows affected (0.034 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

나머지 2개의 열의 이름도 변경해보자.

```sql
ALTER TABLE project_list
CHANGE COLUMN desciptionofproj proj_desc VARCHAR(100),
CHANGE COLUMN contractoronjob con_name VARCHAR(30);
```

하나의 ALTER 문에서 여러 개의 열 이름을 변경할 수 있다.

```bash
MariaDB [gregs_list]> ALTER TABLE project_list
    -> CHANGE COLUMN desciptionofproj proj_desc VARCHAR(100),
    -> CHANGE COLUMN contractoronjob con_name VARCHAR(30);
Query OK, 4 rows affected (0.022 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

만약 열에 100자리 보다 더 많은 문자열을 저장하게 하고 싶다면 MODIFY를 사용하면 된다.

```sql
ALTER TABLE project_list
MODIFY COLUMN proj_desc VARCHAR(120);
```

```bash
MariaDB [gregs_list]> ALTER TABLE project_list
    -> MODIFY COLUMN proj_desc VARCHAR(120);
Query OK, 0 rows affected (0.007 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

시작일, 계약회사의 전화번호, 예상금액 열을 추가해보자.

```sql
ALTER TABLE project_list
ADD COLUMN con_phone VARCHAR(10),
ADD COLUMN start_date DATE,
ADD COLUMN est_cost DECIMAL(7,2);
```

```bash
MariaDB [gregs_list]> ALTER TABLE project_list
    -> ADD COLUMN con_phone VARCHAR(10),
    -> ADD COLUMN start_date DATE,
    -> ADD COLUMN est_cost DECIMAL(7,2);
Query OK, 0 rows affected (0.006 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

프로젝트가 중단되어 프로젝트 시작일 열이 필요가 없어졌다. 이제 필요 없는 열을 지워야한다. 열을 삭제하는 것은 DROP COLUMN 명령어로 할 수 있다. 참고로 지금은 데이터를 입력하지 않아 사라지지 않았지만 열을 삭제하면 해당 열 안에 있는 데이터도 모두 같이 사라진다.

```sql
ALTER TABLE project_list
DROP COLUMN start_date;
```

```bash
MariaDB [gregs_list]> ALTER TABLE project_list
    -> DROP COLUMN start_date;
Query OK, 0 rows affected (0.006 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

## 연습문제 (1)

```sql
CREATE TABLE hooptie(
    color VARCHAR(10),
    year INT(4),
    make VARCHAR(10),
    mo VARCHAR(10),
    howmuch VARCHAR(10)
);

INSERT INTO hooptie(
    VALUES
        ('silver', 1998, 'Porsche', 'Boxter', '17992.540'),
        (NULL, 2000, 'Jaguar', 'XJ', '15995'),
        ('red', 2002, 'Cadilac', 'Escalade', '40215.9')
);
```

```bash
MariaDB [gregs_list]> CREATE TABLE hooptie(
    ->     color VARCHAR(10),
    ->     year INT(4),
    ->     make VARCHAR(10),
    ->     mo VARCHAR(10),
    ->     howmuch VARCHAR(10)
    -> );
Query OK, 0 rows affected (0.011 sec)

MariaDB [gregs_list]> INSERT INTO hooptie(
    ->     VALUES
    ->         ('silver', 1998, 'Porsche', 'Boxter', '17992.540'),
    ->         (NULL, 2000, 'Jaguar', 'XJ', '15995'),
    ->         ('red', 2002, 'Cadilac', 'Escalade', '40215.9')
    -> );
Query OK, 3 rows affected (0.005 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

```sql
ALTER TABLE hooptie
RENAME TO car_table,
ADD COLUMN car_id INT NOT NULL AUTO_INCREMENT FIRST,
ADD PRIMARY KEY (car_id),
CHANGE COLUMN mo model VARCHAR(20),
CHANGE COLUMN howmuch price DEC(7,2),
ADD COLUMN VIN VARCHAR(17)
AFTER car_id,
MODIFY color varchar(10) AFTER model,
MODIFY year int(4) AFTER color;

```

```bash
MariaDB [gregs_list]> ALTER TABLE hooptie
    -> RENAME TO car_table,
    -> ADD COLUMN car_id INT NOT NULL AUTO_INCREMENT FIRST,
    -> ADD PRIMARY KEY (car_id),
    -> CHANGE COLUMN mo model VARCHAR(20),
    -> CHANGE COLUMN howmuch price DEC(7,2),
    -> ADD COLUMN VIN VARCHAR(17)
    -> AFTER car_id,
    -> MODIFY color varchar(10) AFTER model,
    -> MODIFY year int(4) AFTER color;
Query OK, 3 rows affected (0.021 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

```sql
UPDATE car_table
SET VIN = 'RNKLK66N33G213481'
WHERE car_id = 1;

UPDATE car_table
SET VIN = 'SAEDA44B175BO4113'
WHERE car_id = 2;

UPDATE car_table
SET VIN = '3GYEK63NT2G28O668'
WHERE car_id = 3;
```

```bash
MariaDB [gregs_list]> UPDATE car_table
    -> SET VIN = 'RNKLK66N33G213481'
    -> WHERE car_id = 1;
Query OK, 1 row affected (0.012 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [gregs_list]> UPDATE car_table
    -> SET VIN = 'SAEDA44B175BO4113'
    -> WHERE car_id = 2;
Query OK, 1 row affected (0.003 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [gregs_list]>
MariaDB [gregs_list]> UPDATE car_table
    -> SET VIN = '3GYEK63NT2G28O668'
    -> WHERE car_id = 3;
Query OK, 1 row affected (0.004 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

완성된 테이블을 확인해보자.

```sql
SELECT * FROM car_table;
```

```bash
MariaDB [gregs_list]> SELECT * FROM car_table;
+--------+-------------------+---------+----------+--------+------+----------+
| car_id | VIN               | make    | model    | color  | year | price    |
+--------+-------------------+---------+----------+--------+------+----------+
|      1 | RNKLK66N33G213481 | Porsche | Boxter   | silver | 1998 | 17992.54 |
|      2 | SAEDA44B175BO4113 | Jaguar  | XJ       | NULL   | 2000 | 15995.00 |
|      3 | 3GYEK63NT2G28O668 | Cadilac | Escalade | red    | 2002 | 40215.90 |
+--------+-------------------+---------+----------+--------+------+----------+
3 rows in set (0.001 sec)

```

## 연필을 깎으며

기존 그렉의 리스트 중 my_contacts 테이블에 도시와 주 열을 추가해보자. 이 두 열은 테이블 내에 로케이션(location) 열을 원자적 데이터로 만들기 위해서 두 개의 열로 분리하는 것이다.

```sql
ALTER TABLE my_contacts
ADD COLUMN city VARCHAR(50),
ADD COLUMN state VARCHAR(2);
```

```bash
MariaDB [gregs_list]> ALTER TABLE my_contacts
    -> ADD COLUMN city VARCHAR(50),
    -> ADD COLUMN state VARCHAR(2);
Query OK, 0 rows affected (0.006 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

이제 만들어진 열에 내용을 집어 넣을 차례이다. 먼저 주의 내용부터 찾아보자. SELECT RIGHT를 사용하면 특정 열의 오른쪽에서 n 번째 내용을 찾을 수 있다. 즉 아래와 같이 로케이션 열에서 오른쪽부터 2글자를 선택하는 SELECT 문을 구성할 수 있다.

```sql
SELECT RIGHT(location, 2) FROM my_contacts;
```

```bash
MariaDB [gregs_list]> SELECT RIGHT(location, 2) FROM my_contacts;
+--------------------+
| RIGHT(location, 2) |
+--------------------+
| CA                 |
+--------------------+
1 row in set (0.004 sec)
```

다음으로 도시의 내용을 찾아보자. 이번에도 SELECT RIGHT를 쓸 수 있지만, 콤마를 기준으로 앞에 문자열 부분을 추출하는 방법을 이용한다. 그 방법은 SUBSTRING_INDEX()를 이용하는 것이다. 문자열을 찾을 열 이름과 찾는 문자, 해당 문자가 여러개일 경우 몇번째 인지 등의 정보를 필요로 한다.

```sql
SELECT SUBSTRING_INDEX(location, ',', 1) FROM my_contacts;
```

```bash
MariaDB [gregs_list]> SELECT SUBSTRING_INDEX(location, ',', 1) FROM my_contacts;
+-----------------------------------+
| SUBSTRING_INDEX(location, ',', 1) |
+-----------------------------------+
| Palo Alto                         |
+-----------------------------------+
1 row in set (0.006 sec)
```

## 연습문제 (2)

5번째 문자열 부터 3개까지 뽑는 방법

```sql
SELECT SUBSTRING('San Antonio, TX', 5,3);
```

```bash
MariaDB [gregs_list]> SELECT SUBSTRING('San Antonio, TX', 5,3);
+-----------------------------------+
| SUBSTRING('San Antonio, TX', 5,3) |
+-----------------------------------+
| Ant                               |
+-----------------------------------+
1 row in set (0.000 sec)
```

```sql
SELECT UPPER('uSa');

SELECT LOWER('spaGHEtti');
```

대문자로 만들거나 소문자로 만들거나도 할 수 있다.

```bash
MariaDB [gregs_list]> SELECT UPPER('uSa');
+--------------+
| UPPER('uSa') |
+--------------+
| USA          |
+--------------+
1 row in set (0.000 sec)

MariaDB [gregs_list]> SELECT LOWER('spaGHEtti');
+--------------------+
| LOWER('spaGHEtti') |
+--------------------+
| spaghetti          |
+--------------------+
1 row in set (0.000 sec)
```

문자열을 거꾸로 하는 명령어도 있다.

```sql
SELECT REVERSE('spaGHEtti');
```

```bash
MariaDB [gregs_list]> SELECT REVERSE('spaGHEtti');
+----------------------+
| REVERSE('spaGHEtti') |
+----------------------+
| ittEHGaps            |
+----------------------+
1 row in set (0.003 sec)
```

```sql
SELECT LTRIM(' dogfood ');

SELECT RTRIM(' catfood ');
```

```bash
MariaDB [gregs_list]> SELECT LTRIM(' dogfood ');
+--------------------+
| LTRIM(' dogfood ') |
+--------------------+
| dogfood            |
+--------------------+
1 row in set (0.004 sec)

MariaDB [gregs_list]> SELECT RTRIM(' catfood ');
+--------------------+
| RTRIM(' catfood ') |
+--------------------+
|  catfood           |
+--------------------+
1 row in set (0.000 sec)
```

```sql
SELECT LENGTH('San Antonio, TX ');
```

```bash
MariaDB [gregs_list]> SELECT LENGTH('San Antonio, TX ');
+----------------------------+
| LENGTH('San Antonio, TX ') |
+----------------------------+
|                         16 |
+----------------------------+
1 row in set (0.000 sec)
```

업데이트 문과 함께 사용하여 내용을 추가해보자.

```sql
UPDATE my_contacts
SET state = RIGHT(location, 2);
```

```bash
MariaDB [gregs_list]> UPDATE my_contacts
    -> SET state = RIGHT(location, 2);
Query OK, 1 row affected (0.005 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

```sql
DROP TABLE my_contacts;

CREATE TABLE my_contacts(
    contact_id INT NOT NULL AUTO_INCREMENT,
    location VARCHAR(50),
    city VARCHAR(50),
    state VARCHAR(2),
    PRIMARY KEY (contact_id)
);

INSERT INTO my_contacts(location)
VALUES('Chester, NJ'),
('Katy, TX'),
('San Mateo, CA');


```

전체를 한번에 입력하려면 다음과 같이 하면 된다.

```sql
UPDATE my_contacts
SET state = RIGHT(location,2);
```

```bash
MariaDB [gregs_list]> UPDATE my_contacts
    -> SET state = RIGHT(location,2);
Query OK, 2 rows affected (0.003 sec)
Rows matched: 3  Changed: 2  Warnings: 0
```

아래와 같이 한 줄씩 입력할 수도 있다. 단 WHERE 문을 조심히 입력하지 않으면 잘못 입력될 수도 있으니 주의해야한다.

```sql
UPDATE my_contacts
SET state = RIGHT('Chester,NJ',2)
WHERE contact_id = 1;

UPDATE my_contacts
SET state = RIGHT('Katy,TX',2)
WHERE contact_id = 2;

UPDATE my_contacts
SET state = RIGHT('San Mateo,CA',2)
WHERE contact_id = 3;
```