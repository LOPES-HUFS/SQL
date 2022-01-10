# 7장 '나이젤에게 사랑 찾아주기'에서 발견한 문제점을 해결하기

'나이젤에게 사랑 찾아주기'에서 `my_contacts` 테이블에 들어 있는 `interests` 컬럼만을 이용해서 찾아주는 것은 쉽지 않았습니다. 원자적 데이터에 대한 규칙도 위빈하고 있습니다. 그래서 이를 개선하고자 합니다. `interests` 테이블을 추가하여 문제점을 개선해 보고자 합니다. 우선 현재 만들어져 있는 테이블을 확인합니다.

```sql
MariaDB [(none)]> USE gregs_list;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [gregs_list]> DESC my_contacts;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| contact_id | int(11)      | NO   | PRI | NULL    | auto_increment |
| phone      | varchar(10)  | YES  |     | NULL    |                |
| last_name  | varchar(30)  | YES  |     | NULL    |                |
| first_name | varchar(20)  | YES  |     | NULL    |                |
| email      | varchar(50)  | YES  |     | NULL    |                |
| gender     | char(1)      | YES  |     | NULL    |                |
| birthday   | date         | YES  |     | NULL    |                |
| profession | varchar(50)  | YES  |     | NULL    |                |
| location   | varchar(50)  | YES  |     | NULL    |                |
| status     | varchar(20)  | YES  |     | NULL    |                |
| interests  | varchar(100) | YES  |     | NULL    |                |
| seeking    | varchar(100) | YES  |     | NULL    |                |
| city       | varchar(50)  | YES  |     | NULL    |                |
| state      | varchar(2)   | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
14 rows in set (0.003 sec)
```

여기서 `my_contacts`을 지우고 새로 만들겠습니다.

```sql
DROP TABLE my_contacts;

CREATE TABLE my_contacts(
    contact_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    last_name VARCHAR(30) default NULL,
    first_name VARCHAR(20) default NULL,
    phone VARCHAR(10) default NULL,
    email VARCHAR(50) default NULL,
    gender char(1) default NULL,
    birthday date default NULL,
    profession VARCHAR(50) default NULL,
    city VARCHAR(50),
    state VARCHAR(2),
    status VARCHAR(20) default NULL,
    seeking VARCHAR(100) default NULL
    );

CREATE TABLE interests(
    int_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    interest VARCHAR(50) NOT NULL,
    contact_id INT NOT NULL,
    CONSTRAINT my_contacts_contact_id_fk
    FOREIGN KEY (contact_id)
    REFERENCES my_contacts(contact_id)
    );
```

앞에서 만든 것을 확인하면 다음과 같습니다.

```sql
MariaDB [gregs_list]> DESC my_contacts;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| contact_id | int(11)      | NO   | PRI | NULL    | auto_increment |
| last_name  | varchar(30)  | YES  |     | NULL    |                |
| first_name | varchar(20)  | YES  |     | NULL    |                |
| phone      | varchar(10)  | YES  |     | NULL    |                |
| email      | varchar(50)  | YES  |     | NULL    |                |
| gender     | char(1)      | YES  |     | NULL    |                |
| birthday   | date         | YES  |     | NULL    |                |
| profession | varchar(50)  | YES  |     | NULL    |                |
| city       | varchar(50)  | YES  |     | NULL    |                |
| state      | varchar(2)   | YES  |     | NULL    |                |
| status     | varchar(20)  | YES  |     | NULL    |                |
| seeking    | varchar(100) | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
12 rows in set (0.002 sec)

MariaDB [gregs_list]> DESC interests;
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| int_id     | int(11)     | NO   | PRI | NULL    | auto_increment |
| interest   | varchar(50) | NO   |     | NULL    |                |
| contact_id | int(11)     | NO   | MUL | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
3 rows in set (0.001 sec)
```

```sql
INSERT INTO my_contacts(
    contact_id, last_name, first_name, phone, email, gender, birthday, profession, city, state, status, seeking
)
VALUES (
    NULL,'Anderson', 'Jillian', NULL, 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto', 'CA', 'Single', 'Relationship, Friends'
);

INSERT INTO my_contacts(
    contact_id,first_name, email, profession, city, state
)
VALUES(
    NULL,'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton', 'NJ'
);

INSERT INTO my_contacts
VALUES (
    873,"Sullivan","Regis",5552311122,"me@kathieleeisaflake.com","M","1955-03-20","Comedian","Cambridge","MA", "Sigle", "single F"
);

INSERT INTO my_contacts
VALUES (
    459,"Ferguson","Alexis",5550983476,"alexangel@yahoo.com","F","1956-09-19","Artist","Pflugerville","MA", "Sigle", "single M"
);

```

```sql
INSERT INTO interests
VALUES (NULL, "Kayaking", 1),
(NULL, "Reptiles", 1),
(NULL, "Surfing",2),
(NULL, "Animals",873),
(NULL, "geocaching",873),
(NULL, "Trading Card",873),
(NULL, "Animals",459);
```

```sql

MariaDB [gregs_list]> SELECT * FROM my_contacts;
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
| contact_id | last_name | first_name | phone      | email                            | gender | birthday   | profession       | city         | state | status | seeking               |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
|          1 | Anderson  | Jillian    | NULL       | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto    | CA    | Single | Relationship, Friends |
|          2 | NULL      | Pat        | NULL       | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton    | NJ    | NULL   | NULL                  |
|        459 | Ferguson  | Alexis     | 5550983476 | alexangel@yahoo.com              | F      | 1956-09-19 | Artist           | Pflugerville | MA    | Sigle  | single M              |
|        873 | Sullivan  | Regis      | 5552311122 | me@kathieleeisaflake.com         | M      | 1955-03-20 | Comedian         | Cambridge    | MA    | Sigle  | single F              |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
4 rows in set (0.001 sec)


MariaDB [gregs_list]> SELECT * FROM interests;
+--------+--------------+------------+
| int_id | interest     | contact_id |
+--------+--------------+------------+
|      1 | Kayaking     |          1 |
|      2 | Reptiles     |          1 |
|      3 | Surfing      |          2 |
|      4 | Animals      |        873 |
|      5 | geocaching   |        873 |
|      6 | Trading Card |        873 |
|      7 | Animals      |        459 |
+--------+--------------+------------+
7 rows in set (0.003 sec)

```

이렇게 본래 테이블의 기본키를 흥미 테이블에 참조키로 사용하고 제약조건을 걸어 둘 경우 본래 테이블에 데이터를 지우려고 하면 아래와 같은 에러가 발생합니다.

```sql
MariaDB [gregs_list]> DELETE FROM my_contacts WHERE contact_id = 1;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`gregs_list`.`interests`, CONSTRAINT `my_contacts_contact_id_fk` FOREIGN KEY (`contact_id`) REFERENCES `my_contacts` (`contact_id`))
```

## 제 1 정규형이 아닌데

```sql
CREATE DATABASE toy_db;
USE toy_db;
```

```sql
CREATE TABLE is_not_1nf_toy(
    toy_id INT NOT NULL,
    toy VARCHAR(30) default NULL,
    color VARCHAR(30) default NULL
    );

INSERT INTO is_not_1nf_toy(toy_id, toy, color) VALUES (5, 'whiffleball', 'white, yellow, blue');
INSERT INTO is_not_1nf_toy(toy_id, toy, color) VALUES (6, 'frisbee', 'green, yellow');
INSERT INTO is_not_1nf_toy(toy_id, toy, color) VALUES (9, 'kite', 'red, blue, green');
INSERT INTO is_not_1nf_toy(toy_id, toy, color) VALUES (12, 'yoyo', 'white, yellow');
```

지금까지 만든 것을 확인하며 다음과 같습니다.

```sql
MariaDB [toy_db]> DESC is_not_1nf_toy;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| toy_id | int(11)     | NO   |     | NULL    |       |
| toy    | varchar(30) | YES  |     | NULL    |       |
| color  | varchar(30) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.003 sec)

MariaDB [toy_db]> SELECT * FROM is_not_1nf_toy;
+--------+-------------+---------------------+
| toy_id | toy         | color               |
+--------+-------------+---------------------+
|      5 | whiffleball | white, yellow, blue |
|      6 | frisbee     | green, yellow       |
|      9 | kite        | red, blue, green    |
|     12 | yoyo        | white, yellow       |
+--------+-------------+---------------------+
4 rows in set (0.001 sec)
```

그러나 이 테이블은 **제 1 정규형(1NF)**이 아닙니다.

`1NF(제 1 정규형)`은 규칙 1: 열은 원자적 값만을 포함한다, 규칙 2: 같은 데이터가 여러 열에 반복되지 말아야 합니다.

위의 **is_not_1nf_toy** 테이블은 `color`열에 색이 2, 3가지 색이 들어 있기 때문에 **규칙 1**을 위배하고 있습니다. 이를 보완하기 위해서 과감히 지우고 새 테이블을 만들어 봅시다.

```sql
MariaDB [toy_db]> DROP TABLE is_not_1nf_1_toy;
Query OK, 0 rows affected (0.007 sec)
```

```sql
CREATE TABLE is_not_1nf_toy(
    toy_id INT NOT NULL,
    toy VARCHAR(30) default NULL,
    color1 VARCHAR(30) default NULL,
    color2 VARCHAR(30) default NULL,
    color3 VARCHAR(30) default NULL
    );

INSERT INTO is_not_1nf_toy(toy_id, toy) VALUES (5, 'whiffleball', 'white', 'yellow', 'blue');
INSERT INTO is_not_1nf_toy(toy_id, toy) VALUES (6, 'frisbee', 'green', 'yellow','');
INSERT INTO is_not_1nf_toy(toy_id, toy) VALUES (9, 'kite', 'red', 'blue', 'green');
INSERT INTO is_not_1nf_toy(toy_id, toy) VALUES (12, 'yoyo', 'white', 'yellow', '');
```

새로 만든 것을 확인하며 다음과 같습니다.

```sql
MariaDB [toy_db]> DESC is_not_1nf_toy;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| toy_id | int(11)     | NO   |     | NULL    |       |
| toy    | varchar(30) | YES  |     | NULL    |       |
| color1 | varchar(30) | YES  |     | NULL    |       |
| color2 | varchar(30) | YES  |     | NULL    |       |
| color3 | varchar(30) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
5 rows in set (0.001 sec)

MariaDB [toy_db]> SELECT * FROM is_not_1nf_toy;
+--------+-------------+--------+--------+--------+
| toy_id | toy         | color1 | color2 | color3 |
+--------+-------------+--------+--------+--------+
|      5 | whiffleball | white  | yellow | blue   |
|      6 | frisbee     | green  | yellow |        |
|      9 | kite        | red    | blue   | green  |
|     12 | yoyo        | white  | yellow |        |
+--------+-------------+--------+--------+--------+
4 rows in set (0.001 sec)
```

그러나 아쉽게도 여전히 `is_not_1nf_toy` 테이블은 **제 1 정규형**이 아닙니다. 왜냐하면 열들에 같은 종류의 값을 가지고 있어, **규칙 2**을 위배하고 있기 때문입니다.

## 마침내 1NF

`1NF`을 맟추기 위해서는 테이블을 2개로 나는 것이 좋습니다.

```sql
CREATE TABLE toys(
    toy_id INT NOT NULL,
    toy VARCHAR(30) default NULL
    );

INSERT INTO toys(toy_id, toy) VALUES (5, 'whiffleball');
INSERT INTO toys(toy_id, toy) VALUES (6, 'frisbee');
INSERT INTO toys(toy_id, toy) VALUES (9, 'kite');
INSERT INTO toys(toy_id, toy) VALUES (12, 'yoyo');

CREATE TABLE toy_colors(
    toy_id INT NOT NULL,
    color VARCHAR(20) default NULL
    );

INSERT INTO toy_colors(toy_id, color) VALUES (5, 'white');
INSERT INTO toy_colors(toy_id, color) VALUES (5, 'yellow');
INSERT INTO toy_colors(toy_id, color) VALUES (5, 'blue');
INSERT INTO toy_colors(toy_id, color) VALUES (6, 'green');
INSERT INTO toy_colors(toy_id, color) VALUES (6, 'yellow');
INSERT INTO toy_colors(toy_id, color) VALUES (9, 'red');
INSERT INTO toy_colors(toy_id, color) VALUES (9, 'blue');
INSERT INTO toy_colors(toy_id, color) VALUES (9, 'green');
INSERT INTO toy_colors(toy_id, color) VALUES (12, 'white');
INSERT INTO toy_colors(toy_id, color) VALUES (12, 'yellow');
```

```sql
MariaDB [toy_db]> SELECT * FROM toys;
+--------+-------------+
| toy_id | toy         |
+--------+-------------+
|      5 | whiffleball |
|      6 | frisbee     |
|      9 | kite        |
|     12 | yoyo        |
+--------+-------------+
4 rows in set (0.001 sec)

MariaDB [toy_db]> SELECT * FROM toy_colors;
+--------+--------+
| toy_id | color  |
+--------+--------+
|      5 | white  |
|      5 | yellow |
|      5 | blue   |
|      6 | green  |
|      6 | yellow |
|      9 | red    |
|      9 | blue   |
|      9 | green  |
|     12 | white  |
|     12 | yellow |
+--------+--------+
10 rows in set (0.001 sec)
```

뒤에서 배우겠지만 이렇게 2개의 테이블을 작성하면 다음과 같은 명령어로 어떤 장난감이 어떤 색을 가지고 있는지 파악할 수 있다.

```sql
SELECT t.toy, c.color
    FROM toys t
    INNER JOIN toy_colors c
    ON t.toy_id = c.toy_id;
```
