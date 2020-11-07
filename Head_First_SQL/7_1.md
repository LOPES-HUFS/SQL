# 7장 코드 만들기

현재 만들어져 있는 테이블을 확인합니다.

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
