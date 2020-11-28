# 제약조건, 뷰, 트랜젝션

원래 그렉의 테이블인 my_contacts로 돌아와 보자. 이 때 그렉이 만든 이 테이블을 다른 사람이 수정해야할 일이 생겼다.

```sql
SELECT * FROM my_contacts;
```

```bash
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
| contact_id | last_name | first_name | phone      | email                            | gender | birthday   | profession       | city         | state | status | seeking               |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
|          1 | Anderson  | Jillian    | NULL       | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto    | CA    | Single | Relationship, Friends |
|          2 | NULL      | Pat        | NULL       | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton    | NJ    | NULL   | NULL                  |
|          3 | Miller    | Sean       | 555-4443   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|          4 | Callaway  | jared      | 555-5674   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|          5 | Garza     | juan       | 555-0098   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|        459 | Ferguson  | Alexis     | 5550983476 | alexangel@yahoo.com              | F      | 1956-09-19 | Artist           | Pflugerville | MA    | Sigle  | single M              |
|        873 | Sullivan  | Regis      | 5552311122 | me@kathieleeisaflake.com         | M      | 1955-03-20 | Comedian         | Cambridge    | MA    | Sigle  | single F              |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
7 rows in set (0.006 sec)

```

남이 만든 테이블을 조작한다고 할 때 서로 다른 스타일로 테이블을 작성하는 경우가 있습니다 예를 들면 NULL 값 대신 'X'로 표기하는 사람이 있다고 할 경우 다음과 같이 새로운 사람을 추가할 수 있다.

```sql
INSERT INTO my_contacts
VALUES (6,'Murphy','pat', '5551293', 'patmurphy@somemail.com', 'X', '1978-04-15',X,X,X,X,X);
```

```bash
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
| contact_id | last_name | first_name | phone      | email                            | gender | birthday   | profession       | city         | state | status | seeking               |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
|          1 | Anderson  | Jillian    | NULL       | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto    | CA    | Single | Relationship, Friends |
|          2 | NULL      | Pat        | NULL       | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton    | NJ    | NULL   | NULL                  |
|          3 | Miller    | Sean       | 555-4443   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|          4 | Callaway  | jared      | 555-5674   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|          5 | Garza     | juan       | 555-0098   | NULL                             | NULL   | NULL       | NULL             | NULL         | NULL  | NULL   | NULL                  |
|          6 | Murphy    | pat        | 5551293    | patmurphy@somemail.com           | X      | 1978-04-15 | X                | X            | X     | X      | X                     |
|        459 | Ferguson  | Alexis     | 5550983476 | alexangel@yahoo.com              | F      | 1956-09-19 | Artist           | Pflugerville | MA    | Sigle  | single M              |
|        873 | Sullivan  | Regis      | 5552311122 | me@kathieleeisaflake.com         | M      | 1955-03-20 | Comedian         | Cambridge    | MA    | Sigle  | single F              |
+------------+-----------+------------+------------+----------------------------------+--------+------------+------------------+--------------+-------+--------+-----------------------+
8 rows in set (0.001 sec)

```

이렇게 새 데이터가 들어갔을 때 그렉이 남자가 몇명인지 여자가 몇명인지 알고 싶을 때 다음과 같은 쿼리문을 사용할 수 있다.

```sql
SELECT COUNT(*) AS gender_F FROM my_contacts WHERE gender = "F";

SELECT COUNT(*) AS gender_M FROM my_contacts WHERE gender = "M";

SELECT COUNT(*) AS total FROM my_contacts;
```

```bash
MariaDB [gregs_list]> SELECT COUNT(*) AS gender_F FROM my_contacts WHERE gender = "F";
+----------+
| gender_F |
+----------+
|        2 |
+----------+
1 row in set (0.004 sec)

MariaDB [gregs_list]> SELECT COUNT(*) AS gender_M FROM my_contacts WHERE gender = "M";
+----------+
| gender_M |
+----------+
|        1 |
+----------+
1 row in set (0.001 sec)


MariaDB [gregs_list]> SELECT COUNT(*) AS total FROM my_contacts;
+-------+
| total |
+-------+
|     8 |
+-------+
1 row in set (0.000 sec)
```

이 경우 5개 값이 NULL 값이 생각할 수 있지만 실제로는 그렇지 않다. 다음 쿼리를 통해 확인할 수 있다.

```sql
SELECT gender AS gender FROM my_contacts WHERE gender <> "M" AND gender <> 'F';
```

```bash
MariaDB [gregs_list]> SELECT gender AS gender FROM my_contacts WHERE gender <> "M" AND gender <> 'F';
+--------+
| gender |
+--------+
| X      |
+--------+
1 row in set (0.001 sec)
```

이렇게 NULL 값을 의미하지만 실제로는 값 X가 들어간 경우가 발생하게 된다. 이런 경우 총계가 불분명해지고 데이터 베이스의 테이블이 혼란스러워진다. 따라서 제약조건이 필요하다.

## 체크 제약조건

위와 같은 문제를 막고자 테이블을 만들 때 특정 열에 체크 제약조건을 걸 수 있다. 예를 들면 다음과 같다.

```sql
CREATE TABLE piggy_bank (
    id INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    coin VARCHAR(1) CHECK (coin IN ('P','N','D','Q'))
);
```

```bash
MariaDB [gregs_list]> DESC piggy_bank;
+-------+------------+------+-----+---------+----------------+
| Field | Type       | Null | Key | Default | Extra          |
+-------+------------+------+-----+---------+----------------+
| id    | int(11)    | NO   | PRI | NULL    | auto_increment |
| coin  | varchar(1) | YES  |     | NULL    |                |
+-------+------------+------+-----+---------+----------------+
2 rows in set (0.006 sec)
```

제약 조건은 테이블 설명서에서는 확인할 수 없다.

기존에 테이블에 제약 조건을 추가할 수도 있다. 예를 들면 그렉의 테이블에 성별 칸에 오직 'M','F' 값만 들어가도록 설정해보자.

NULL 값이 있는 경우 기존의 NULL 값을 다 UPDATE 문으로 변경해주지 않는 한 "M","F" 오직 두 값만 들어가게 끔 설정하는 것은 불가능하다. 따라서 NULL값도 가능하다는 것을 추가해주면 제한 조건을 걸 수 있다. 

```sql
ALTER TABLE my_contacts ADD CONSTRAINT CHECK (gender IN (NULL,'M','F'));
```

제약 조건이 잘 걸렸는지 확인은 `SELECT * from INFORMATION_SCHEMA.CHECK_CONSTRAINTS;` 구문으로 가능하다. 이 명령어를 통해 데이터 베이스 내 모든 테이블에 걸린 제약 조건을 확인할 수 있다.

```bash
MariaDB [gregs_list]>  SELECT * from INFORMATION_SCHEMA.CHECK_CONSTRAINTS;
+--------------------+-------------------+-------------+-----------------+-----------------------------+
| CONSTRAINT_CATALOG | CONSTRAINT_SCHEMA | TABLE_NAME  | CONSTRAINT_NAME | CHECK_CLAUSE                |
+--------------------+-------------------+-------------+-----------------+-----------------------------+
| def                | mysql             | global_priv | Priv            | json_valid(`Priv`)          |
| def                | gregs_list        | my_contacts | CONSTRAINT_1    | `gender` in (NULL,'M','F')  |
| def                | gregs_list        | piggy_bank  | coin            | `coin` in ('P','N','D','Q') |
+--------------------+-------------------+-------------+-----------------+-----------------------------+
3 rows in set (0.011 sec)

```

## 뷰

