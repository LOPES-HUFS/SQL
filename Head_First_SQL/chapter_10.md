# 외부조인, 셀프조인, 유니온

내부 조인에서는 두 테이블의 행을 비교하지만 두 테이블 사이의 순서는 중요하지 않다. 외부 조인은 조인되는 테이블 중 하나의 모든 행을 다른 테이블에서 일치하는 정보의 테이블을 반환해준다. 아래의 코드는 내부 조인의 경우의 예이다.

```sql
CREATE TABLE girls (
    girl_id INT NOT NULL AUTO_INCREMENT,
    girl VARCHAR(20),
    toy_id INT,
    PRIMARY KEY(girl_id)
);

CREATE TABLE toys(
    toy_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    toy VARCHAR(30) default NULL
    );

INSERT INTO toys(toy_id, toy)
VALUES (NULL,'hula hoop'),
(NULL,'balsa glider'),
(NULL,'toy soldiers'),
(NULL,'harmonica'),
(NULL,'baseball cards'),
(NULL, 'tinker toys'),
(NULL,'eych-a-sketch'),
(NULL,'slinky');

INSERT INTO girls
VALUES (1,'Jane', 3),
(2,'Sally', 4),
(3, 'Cindy', 1);

```

```bash
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
|      7 | eych-a-sketch  |
|      8 | slinky         |
+--------+----------------+
8 rows in set (0.001 sec)
```

```sql
SELECT g.girl, t.toy FROM girls g
INNER JOIN toys t
ON g.toy_id = t.toy_id;
```

```bash
MariaDB [gregs_list]> SELECT g.girl, t.toy FROM girls g
    -> INNER JOIN toys t
    -> ON g.toy_id = t.toy_id;
+-------+--------------+
| girl  | toy          |
+-------+--------------+
| Jane  | toy soldiers |
| Sally | harmonica    |
| Cindy | hula hoop    |
+-------+--------------+
3 rows in set (0.007 sec)

```

## 왼쪽 외부 조인

외부 조인은 두 테이블 사이의 관계에 좀 더 집중한다. 왼쪽 외부조인은 왼쪽 테이블의 모든 행을 오른쪽 테이블의 행과 비교한다. 왼쪽 테이블과 오른쪽 테이블이 일대다 관계일때 유용하게 사용된다. 왼쪽 테이블이란 코드에서 FROM 뒤 조인 전에 나오는 테이블을 의미하며, 오른쪽 테이블은 조인 뒤에 나오는 테이블이다. 아래의 코드에서 girls가 왼쪽 테이블이고 toys가 오른쪽 테이블이 된다. 아래의 코드는 내부 조인을 사용한 것과 동일한 결과를 나타낸다.

```sql
SELECT g.girl, t.toy FROM girls g
LEFT OUTER JOIN toys t
ON g.toy_id = t.toy_id;

```

```bash
MariaDB [gregs_list]> SELECT g.girl, t.toy FROM girls g
    -> LEFT OUTER JOIN toys t
    -> ON g.toy_id = t.toy_id;
+-------+--------------+
| girl  | toy          |
+-------+--------------+
| Jane  | toy soldiers |
| Sally | harmonica    |
| Cindy | hula hoop    |
+-------+--------------+
3 rows in set (0.001 sec)
```

외부 조인의 특징은 다른 테이블과 일치하는 것이 있는지 여부에 상관없이 행을 반환한다. 만약 일치하는것이 없을 경우 NULL값을 반환한다. 즉 결과가 NULL 값이라면 왼쪽 테이블의 값이 오른쪽 테이블에는 없다는 것이다. 예를 들면 아래와 같은 경우이다. 장난감 8개 중 5개는 해당하는 소녀가 없기 때문에 소녀의 이름인 girl 열에 값이 NULL이 반환된다.

```sql
SELECT g.girl, t.toy FROM toys t
LEFT OUTER JOIN girls g
ON g.toy_id = t.toy_id;
```

```bash
MariaDB [gregs_list]> SELECT g.girl, t.toy FROM toys t
    -> LEFT OUTER JOIN girls g
    -> ON g.toy_id = t.toy_id;
+-------+----------------+
| girl  | toy            |
+-------+----------------+
| Jane  | toy soldiers   |
| Sally | harmonica      |
| Cindy | hula hoop      |
| NULL  | balsa glider   |
| NULL  | baseball cards |
| NULL  | tinker toys    |
| NULL  | eych-a-sketch  |
| NULL  | slinky         |
+-------+----------------+
8 rows in set (0.005 sec)

```

## 연습 문제

toy 열에 NULL 값이 나올려면 왼쪽 테이블은 girls가 되고 해당 테이블의 toy_id가 오른쪽 테이블인 toys의 toy_id 전부와 일치하지 않아야한다. 반면 girl 열에 NULL 값이 나올려면 왼쪽 테이블은 toys가 되고, 오른쪽 테이블은 girls가 되며 toys 테이블의 toy_id가 girls 테이블의 toy_id와 일치하지 않는다.  

```sql
INSERT INTO girls
VALUES (4,'Jen', 2),
(5,'Cleo', 5),
(6, 'Mandy', 9);

INSERT INTO girls
VALUES (7,'Martha', 3);

SELECT g.girl, t.toy FROM girls g
LEFT OUTER JOIN toys t
ON g.toy_id = t.toy_id;

SELECT g.girl, t.toy FROM toys t
LEFT OUTER JOIN girls g
ON g.toy_id = t.toy_id;
```

```bash
MariaDB [gregs_list]> SELECT g.girl, t.toy FROM girls g
    -> LEFT OUTER JOIN toys t
    -> ON g.toy_id = t.toy_id;
+--------+----------------+
| girl   | toy            |
+--------+----------------+
| Jane   | toy soldiers   |
| Sally  | harmonica      |
| Cindy  | hula hoop      |
| Jen    | balsa glider   |
| Cleo   | baseball cards |
| Mandy  | NULL           |
| Martha | toy soldiers   |
+--------+----------------+
7 rows in set (0.001 sec)

MariaDB [gregs_list]> SELECT g.girl, t.toy FROM toys t
    -> LEFT OUTER JOIN girls g
    -> ON g.toy_id = t.toy_id;
+--------+----------------+
| girl   | toy            |
+--------+----------------+
| Jane   | toy soldiers   |
| Sally  | harmonica      |
| Cindy  | hula hoop      |
| Jen    | balsa glider   |
| Cleo   | baseball cards |
| Martha | toy soldiers   |
| NULL   | tinker toys    |
| NULL   | eych-a-sketch  |
| NULL   | slinky         |
+--------+----------------+
9 rows in set (0.001 sec)
```

toys테이블에서 toy_id가 6,7,8 인 장난감은 girls 테이블의 모든 행의 toy_id와 일치하지 않기 때문에 NULL 값이 반환된다.

## 오른쪽 외부 조인

왼쪽이 있으면 오른쪽도 있다. 오른쪽 외부 조인은 오른쪽 테이블을 왼쪽 테이블과 비교한다. 오른쪽 외부 조인에서 오른쪽 테이블의 위치는 FROM 뒤 조인 앞이고, 왼쪽 테이블의 위치는 조인 뒤의 테이블이다.

```sql
SELECT g.girl, t.toy FROM girls g
RIGHT OUTER JOIN toys t
ON g.toy_id = t.toy_id;
```

물론 테이블의 위치를 바꾸는 것이 명령어를 바꾸는 것보다 쉬우며 덜 혼동되기 때문에 하나의 외부 조인 명령어를 사용하는 경우가 더 많다. 외부 조인은 같은 테이블에서도 사용할 수 있다.

## 셀프 조인 (자신을 참조하는 참조키)

자신을 참조하는 참조키의 경우 같은 테이블에서 다른 목적으로 사용하는 기본키이다.

```sql
CREATE TABLE clown_info(
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(20),
    boss_id INT
    );

CREATE TABLE clown_boss(
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    boss_id INT
    );

INSERT INTO clown_info
VALUES (NULL,'Elsie',3),
(NULL,'Pickles',5),
(NULL,'Sunggles',10),
(NULL,'Mr.Hobo',3),
(NULL,'Clarabelle',10),
(NULL,'Scooter',3),
(NULL,'Zippo',3),
(NULL,'Babe',5),
(NULL,'Bonzo',5),
(NULL,'Sniffles',10);

INSERT INTO clown_boss
VALUES (NULL,3),
(NULL,5),
(NULL,10),
(NULL,3),
(NULL,10),
(NULL,3),
(NULL,3),
(NULL,5),
(NULL,5),
(NULL,10);
```
