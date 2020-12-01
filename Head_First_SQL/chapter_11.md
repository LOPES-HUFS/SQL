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

뷰는 쿼리 상에서만 존재하는 테이블이다. 테이블을 다루는 것과 같은 방식으로 다룰 수 있기 때문에 가상테이블이라 부르기도 한다. 그러나 가상테이블은 데이터 베이스 내에 존재하는 테이블이 아니다. 이름을 지정한 가상테이블은 지속되는 가상테이블이다. 만약 이름이 없는 뷰를 사용하고 나면 이후 지워진다. 이름이 지정된 가상테이블은 새 열이 데이터베이스에 추가될 때 새 정보가 뷰에 반영되어 있기에 편하다.

```sql
CREATE VIEW web_designers AS
SELECT mc.first_name, mc.last_name, mc.phone, mc.email FROM my_contacts mc
NATURAL JOIN job_desired jd
WHERE jd.title = 'Web Designer';
```

뷰 문은 쿼리의 결과가 나오지 않는다. 쿼리의 결과를 web_designers로 저장한다. 쿼리의 결과를 알기위해서 새로운 쿼리문이 필요하다.

```bash
MariaDB [gregs_list]> CREATE VIEW web_designers AS
    -> SELECT mc.first_name, mc.last_name, mc.phone, mc.email FROM my_contacts mc
    -> NATURAL JOIN job_desired jd
    -> WHERE jd.title = 'Web Designer';
Query OK, 0 rows affected (0.035 sec)
```

`SELECT * FROM 뷰의 이름` 같은 구조로 뷰로 저장한 쿼리의 결과를 확인할 수 있다.

```sql
SELECT * FROM web_designers;
```

```bash
MariaDB [gregs_list]> SELECT * FROM web_designers;
+------------+-----------+----------+-------+
| first_name | last_name | phone    | email |
+------------+-----------+----------+-------+
| jared      | Callaway  | 555-5674 | NULL  |
+------------+-----------+----------+-------+
1 row in set (0.007 sec)
```

뷰의 실제적 역할은 서브 쿼리와 같다. 예를 들어 위의 쿼리문 `SELECT * FROM web_designers;`은 아래의 쿼리문과 같다.

```sql
SELECT * FROM
(SELECT mc.first_name, mc.last_name, mc.phone, mc.email FROM my_contacts mc
NATURAL JOIN job_desired jd
WHERE jd.title = 'Web Designer') AS web_designers;
```

서브 쿼리 안 SELECT 문이 가상의 테이블을 반환하지만 이것을 sql에서 접근하려면 AS web_designers 와 같이 테이블의 이름을 지정해주어야 한다.

## 뷰의 장점

뷰를 만들어 이용하면, 데이터베이스 내 테이블 구조를 변경하더라도 테이블 구조가 바탕이 되는 어플리케이션의 변경을 할 필요가 없다. 뷰를 이용하면 복잡한 쿼리문을 이름을 지정하여 간단하게 만들어 확인할 수 있다. sql 쿼리문에 조인과 서브쿼리 등이 포함되면 그 내용이 길어질 경우가 많은데 뷰를 이용하면 간단한 코드가 된다. 사용자에게 필요없는 정보를 숨길 수 있다. 뷰를 통해 가상테이블을 만들면 데이터베이스 내에는 존재하는 것이 아니기에 사용자에게 필요없는 정보를 숨기고 그 정보를 따로 이용할 수 있다.

```sql
CREATE VIEW job_raise AS
SELECT mc.first_name, mc.last_name, mc.email, mc.phone, jc.contact_id, jc.salary, jd.salary_low,
jd.salary_low - jc.salary AS raise
FROM job_current jc
INNER JOIN job_desired jd
INNER JOIN my_contacts mc
WHERE jc.contact_id = jd.contact_id
AND jc.contact_id = mc.contact_id;
```

```bash
MariaDB [gregs_list]> SELECT * FROM job_raise;
+------------+-----------+----------------------------------+------------+------------+--------+------------+-------+
| first_name | last_name | email                            | phone      | contact_id | salary | salary_low | raise |
+------------+-----------+----------------------------------+------------+------------+--------+------------+-------+
| Jillian    | Anderson  | jill_anderson@breakneckpizza.com | NULL       |          1 |    250 | $250       |  -250 |
| Pat        | NULL      | patpost@breakneckpizza.com       | NULL       |          2 |    180 | $180       |  -180 |
| Sean       | Miller    | NULL                             | 555-4443   |          3 |    170 | $170       |  -170 |
| jared      | Callaway  | NULL                             | 555-5674   |          4 |    200 | $200       |  -200 |
| juan       | Garza     | NULL                             | 555-0098   |          5 |    220 | $220       |  -220 |
| Alexis     | Ferguson  | alexangel@yahoo.com              | 5550983476 |        459 |    150 | $150       |  -150 |
| Regis      | Sullivan  | me@kathieleeisaflake.com         | 5552311122 |        873 |    210 | $210       |  -210 |
+------------+-----------+----------------------------------+------------+------------+--------+------------+-------+
7 rows in set, 7 warnings (0.009 sec)

```

뷰를 가지고 SELECT 문만 사용할 수 있는 것은 아니다. 뷰문을 UPDATE, INSERT, DELETE와 함께 사용할 수도 있다. 단 데이터를 변경할 때 집계값(SUM,COUNT,AVG)을 사용하면, 데이터 변경에 뷰를 사용할 수 없다. 또한, GROUP BY DISTINCT, HAVING이 포함되어 있는 경우에도 데이터를 변경할 수 없다.

```sql
DROP TABLE piggy_bank;

CREATE TABLE piggy_bank
(
id INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
coin VARCHAR(1) NOT NULL,
coin_year INT NOT NULL
);

INSERT INTO piggy_bank
VALUES (NULL,'Q',1950),(NULL,'P',1972),(NULL,'N',2005),(NULL,'Q',1999),(NULL,'Q',1981),
(NULL,'D',1940),(NULL,'Q',1980),(NULL,'P',2001),(NULL,'D',1926),(NULL,'P',1999);
```

```bash
MariaDB [gregs_list]> SELECT * FROM piggy_bank;
+----+------+-----------+
| id | coin | coin_year |
+----+------+-----------+
|  1 | Q    |      1950 |
|  2 | P    |      1972 |
|  3 | N    |      2005 |
|  4 | Q    |      1999 |
|  5 | Q    |      1981 |
|  6 | D    |      1940 |
|  7 | Q    |      1980 |
|  8 | P    |      2001 |
|  9 | D    |      1926 |
| 10 | P    |      1999 |
+----+------+-----------+
10 rows in set (0.001 sec)
```

## 연습문제

```sql
CREATE VIEW pb_quarters AS
SELECT * FROM piggy_bank WHERE coin = 'Q';

CREATE VIEW pb_dimes AS
SELECT * FROM piggy_bank WHERE coin = 'D' WITH CHECK OPTION;
```

```sql
INSERT INTO pb_quarters VALUES (NULL,'Q',1993);

INSERT INTO pb_quarters VALUES (NULL,'D',1942);
```

위의 INSERT 문 두개는 모두 제대로 작동한다. 뷰로 만든 테이블에 데이터를 추가하는 INSERT를 적용시킨 예이다.
반면 아래의 INSERT 문은 작동되지 않고 에러를 반환한다.

```sql
INSERT INTO pb_dimes VALUES (NULL,'Q',2005);
```

위의 INSERT 문이 에러를 반환하는 이유는 뷰를 만들때 적용한 WITH CHECK OPTION 때문이다. 이 체크 옵션을 사용할 경우 뷰의 WHERE절 조건에 맞는지 검사하게 되어 만족하지 않으면, 에러를 반환한다. 즉 체크 옵션을 통해 뷰에서도 제약 조건을 걸수 있다.
이제 마찬가지로 뷰로 만든 테이블에 DELETE문과 UPDATE문을 적용해보자.

```sql
DELETE FROM pb_quarters WHERE coin = 'N' OR coin = 'P' OR coin = 'D';

UPDATE pb_quarters SET coin = 'Q' WHERE coin = 'P';
```

위의 딜리트 문과 업데이트 문은 조건이 만족하지 않아 적용되지만 해당되는 행이 없기에 변화가 없다.

```bash
SELECT * FROM piggy_bank;
```

참고로 UPDATE, INSERT, DELETE는 NOT NULL인 열만 반환하기에 만약 NULL이 허용된 열이라면 뷰에서는 SELECT 문만 사용가능합니다.

뷰를 제거하는 방법은 `DROP VIEW` 를 이용하면 된다. 뷰를 제거하기 전에 본래의 테이블을 드랍할 경우 뷰를 제거할 수 없게 되기 때문에 뷰를 먼저 제거하는 것이 좋다.

## 트랜잭션

트랜잭션은 sql이 한 단위 일을 수행하는 일련의 sql 문이다. 예를 들면 잔고가 350달러인 은행계좌에서 300달러를 인출하려는 과정을 2사람이 한다고 할 때 트랜잭션은 한사람이 300달러를 인출하는 과정이 끝날때까지 다른사람이 계좌에 접근하지 못하게 막는 것이다. SQL 문이 4가지 조건을 만족 시킬때 트랜잭션이라 부를 수 있다.

1. 원자성(ATOMICITY)
트랜잭션은 모든 명령이 완료되거나 만약 모든 명령이 완료될 수 없다면, 어떤 명령도 실행되지 않아야 한다. 즉 트랜잭션은 한 단위의 일이 일부만 수행될 수 없다.

2. 일관성(CONSISTENCY)
트랜잭션이 완료된 후 데이터베이스는 일관성을 유지해야한다.

3. 독립성(ISOLATION)
모든 트랜잭션은 동시에 일어나는 다른 트랜잭션과 상관없이 데이터베이스에 대한 일관된 뷰를 가지고 있어야 한다.

4. 지속성(DURABILITY)
트랜잭션이 끝난 후 데이터베이스는 데이터를 정확히 저장하고 보호해야한다.

트랜잭션은 START TRANSACTION, COMMIT, ROLLBACK으로 이루어진다.

```sql
START TRANSACTION;
SELECT * FROM piggy_bank;
UPDATE piggy_bank set coin = 'Q' where coin = 'P';
SELECT * FROM piggy_bank;
ROLLBACK;
SELECT * FROM piggy_bank;
```

```bash
MariaDB [gregs_list]> START TRANSACTION;
Query OK, 0 rows affected (0.000 sec)

MariaDB [gregs_list]> SELECT * FROM piggy_bank;
+----+------+-----------+
| id | coin | coin_year |
+----+------+-----------+
|  1 | Q    |      1950 |
|  2 | P    |      1972 |
|  3 | N    |      2005 |
|  4 | Q    |      1999 |
|  5 | Q    |      1981 |
|  6 | D    |      1940 |
|  7 | Q    |      1980 |
|  8 | P    |      2001 |
|  9 | D    |      1926 |
| 10 | P    |      1999 |
| 11 | Q    |      1993 |
| 12 | D    |      1942 |
+----+------+-----------+
12 rows in set (0.000 sec)

MariaDB [gregs_list]> UPDATE piggy_bank set coin = 'Q' where coin = 'P';
Query OK, 3 rows affected (0.003 sec)
Rows matched: 3  Changed: 3  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM piggy_bank;
+----+------+-----------+
| id | coin | coin_year |
+----+------+-----------+
|  1 | Q    |      1950 |
|  2 | Q    |      1972 |
|  3 | N    |      2005 |
|  4 | Q    |      1999 |
|  5 | Q    |      1981 |
|  6 | D    |      1940 |
|  7 | Q    |      1980 |
|  8 | Q    |      2001 |
|  9 | D    |      1926 |
| 10 | Q    |      1999 |
| 11 | Q    |      1993 |
| 12 | D    |      1942 |
+----+------+-----------+
12 rows in set (0.001 sec)

MariaDB [gregs_list]> ROLLBACK;
Query OK, 0 rows affected (0.006 sec)

MariaDB [gregs_list]> SELECT * FROM piggy_bank;
+----+------+-----------+
| id | coin | coin_year |
+----+------+-----------+
|  1 | Q    |      1950 |
|  2 | P    |      1972 |
|  3 | N    |      2005 |
|  4 | Q    |      1999 |
|  5 | Q    |      1981 |
|  6 | D    |      1940 |
|  7 | Q    |      1980 |
|  8 | P    |      2001 |
|  9 | D    |      1926 |
| 10 | P    |      1999 |
| 11 | Q    |      1993 |
| 12 | D    |      1942 |
+----+------+-----------+
12 rows in set (0.001 sec)
```

롤백을 시켜 다시 테이블을 원상태로 만든 것을 확인할 수 있다. 반면 롤백 대신 COMMIT 을 사용한다면 본래의 테이블의 내용이 변경되고 되돌아 갈 수 없게 된다.
