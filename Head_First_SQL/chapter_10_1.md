# 10장 외부 조인, 셀프 조인, 유니온

우리가 사용하는 DB로 들어갑니다.

```sql
USE gregs_list;
```

예재 데이터 입력

```sql
CREATE TABLE girls(
    girl_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    girl VARCHAR(30) default NULL,
    toy_id INT NOT NULL
    );

INSERT INTO girls(girl_id, girl, toy_id) VALUES (NULL,'Jane', 3);
INSERT INTO girls(girl_id, girl, toy_id) VALUES (NULL,'Sally', 4);
INSERT INTO girls(girl_id, girl, toy_id) VALUES (NULL,'Cindy', 1);
```

입력한 결과는 다음과 같습니다.

```sql
MariaDB [gregs_list]> SELECT * FROM girls;
+---------+-------+--------+
| girl_id | girl  | toy_id |
+---------+-------+--------+
|       1 | Jane  |      3 |
|       2 | Sally |      4 |
|       3 | Cindy |      1 |
+---------+-------+--------+
3 rows in set (0.005 sec)
```

우선 앞에서 만든 테이블을 지운 다음 새로 만들겠습니다.

```sql
DROP TABLE toys;
```

윗 코드 실행결과는 다음과 같습니다.

```sql
MariaDB [gregs_list]> DROP TABLE toys;
Query OK, 0 rows affected (0.050 sec)
```

새로 만들어 봅시다.

```sql
CREATE TABLE toys(
    toy_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    toy VARCHAR(30) default NULL
    );

INSERT INTO toys(toy_id, toy) VALUES (NULL,'hula hoop');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'balsa glider');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'toy soldiers');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'harmonica');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'baseball cards');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'tinker toys');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'etch-a-skech');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'slinky');
```

만든 것을 확인하면 다음과 같습니다.

```sql
MariaDB [gregs_list]> SELECT * FROM toys;
+--------+----------------+
| toy_id | toy            |
+--------+----------------+
|      1 | hula hoop      |
|      2 | balsa glider   |
|      3 | toy soldiers   |
|      4 | harmonica      |
|      5 | baseball cards |
|      6 | tinker toys    |
|      7 | etch-a-skech   |
|      8 | slinky         |
+--------+----------------+
8 rows in set (0.001 sec)
```

앞에서 했던 내부 조인을 한 번 더 실행해보겠습니다.

```sql
SELECT g.girl, t.toy
    FROM girls g
    INNER JOIN toys t
    ON g.toy_id = t.toy_id;
```

실행결과는 다음과 같습니다.

```sql
MariaDB [gregs_list]> SELECT g.girl, t.toy
    ->     FROM girls g
    ->     INNER JOIN toys t
    ->     ON g.toy_id = t.toy_id;
+-------+--------------+
| girl  | toy          |
+-------+--------------+
| Jane  | toy soldiers |
| Sally | harmonica    |
| Cindy | hula hoop    |
+-------+--------------+
```

이제 외부 조인을 실행해 보겠습니다.

```sql
SELECT g.girl, t.toy
    FROM girls g
    LEFT OUTER JOIN toys t
    ON g.toy_id = t.toy_id;
```

결과는 다음과 같습니다.

```sql
MariaDB [gregs_list]> SELECT g.girl, t.toy
    ->     FROM girls g
    ->     LEFT OUTER JOIN toys t
    ->     ON g.toy_id = t.toy_id;
+-------+--------------+
| girl  | toy          |
+-------+--------------+
| Jane  | toy soldiers |
| Sally | harmonica    |
| Cindy | hula hoop    |
+-------+--------------+
3 rows in set (0.001 sec)
```

앞에서 실행한 내부조인과 같습니다. 다른 외부 조인을 실행해보겠습니다.

```sql
SELECT g.girl, t.toy
    FROM toys t
    LEFT OUTER JOIN girls g
    ON g.toy_id = t.toy_id;
```

결과는 다음과 같습니다.

```sql
MariaDB [gregs_list]> SELECT g.girl, t.toy
    ->     FROM toys t
    ->     LEFT OUTER JOIN girls g
    ->     ON g.toy_id = t.toy_id;
+-------+----------------+
| girl  | toy            |
+-------+----------------+
| Jane  | toy soldiers   |
| Sally | harmonica      |
| Cindy | hula hoop      |
| NULL  | balsa glider   |
| NULL  | baseball cards |
| NULL  | tinker toys    |
| NULL  | etch-a-skech   |
| NULL  | slinky         |
+-------+----------------+
8 rows in set (0.001 sec)
```
