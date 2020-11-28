# 외부조인, 셀프조인, 유니온

내부 조인에서는 두 테이블의 행을 비교하지만 두 테이블 사이의 순서가 중요하지 않다. 외부 조인은 조인되는 테이블 중 하나의 모든 행을 다른 테이블에서 일치하는 정보의 테이블을 반환해준다. 아래의 코드는 내부 조인의 경우의 예이다.

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

## 내부 조인과 차이점

외부 조인은 내부 조인과 달리 조건과 일치하지 않으면 NULL값이 나올 수 있다. 또한 외부 조인의 경우 조인되는 테이블의 순서에 따라 결과가 달라 질 수 있기 때문에 내부 조인과 달리 조인할 때 순서를 맞춰주어야한다.

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

이 두 테이블을 이용해 각 광대의 이름과 보스의 이름을 같이 확인하고 싶은 테이블을 만들려면 어떻게 해야할까 ?
같은 테이블을 만들어 쿼리에서 참조하는 경우와 셀프조인을 통해 하나의 테이블을 두번 사용하는 경우가 있다. 같은 테이블을 만드는 작업은 복잡하고 공간 낭비도 심하다. 따라서 셀프조인을 사용한다.

```sql
SELECT c1.name, c2.name AS boss FROM clown_info c1
INNER JOIN clown_info c2
ON c1.boss_id = c2.id;
```

```bash
MariaDB [gregs_list]> SELECT c1.name, c2.name AS boss FROM clown_info c1
    -> INNER JOIN clown_info c2
    -> ON c1.boss_id = c2.id;
+------------+------------+
| name       | boss       |
+------------+------------+
| Elsie      | Sunggles   |
| Pickles    | Clarabelle |
| Sunggles   | Sniffles   |
| Mr.Hobo    | Sunggles   |
| Clarabelle | Sniffles   |
| Scooter    | Sunggles   |
| Zippo      | Sunggles   |
| Babe       | Clarabelle |
| Bonzo      | Clarabelle |
| Sniffles   | Sniffles   |
+------------+------------+
10 rows in set (0.020 sec)
```

다음과 같이 사용할 수 있다. 셀프조인에서는 내부조인을 통해 같은 테이블끼리 결합하고 ON 구문으로 조건을 주어 해당 조건에 맞는 정보만 결합시킬 수 있다.

셀프 조인을 통해 만든 이 결과를 서브 쿼리를 사용하여 만들어보자.

```sql
SELECT name, (SELECT name FROM clown_info WHERE c1.boss_id = id) AS boss FROM clown_info AS c1;
```

```bash
MariaDB [gregs_list]> SELECT name, (SELECT name FROM clown_info WHERE c1.boss_id = id) AS boss FROM clown_info AS c1;
+------------+------------+
| name       | boss       |
+------------+------------+
| Elsie      | Sunggles   |
| Pickles    | Clarabelle |
| Sunggles   | Sniffles   |
| Mr.Hobo    | Sunggles   |
| Clarabelle | Sniffles   |
| Scooter    | Sunggles   |
| Zippo      | Sunggles   |
| Babe       | Clarabelle |
| Bonzo      | Clarabelle |
| Sniffles   | Sniffles   |
+------------+------------+
10 rows in set (0.001 sec)

```

## 유니온

여러 테이블에서 정보를 결합하는 경우가 있을 수 있다. 이 경우 유니온을 사용하면 된다.
다시 girls 테이블로 돌아와서 보면 각 아이들이 원하는 장난감 번호와 그렇지 않은 번호를 모두 합친 전체 장난감 번호를 볼 수 있다.

```sql
SELECT toy_id FROM girls UNION SELECT toy_id FROM toys;
```

```bash
MariaDB [gregs_list]> SELECT toy_id FROM girls UNION SELECT toy_id FROM toys;
+--------+
| toy_id |
+--------+
|      3 |
|      4 |
|      1 |
|      2 |
|      5 |
|      9 |
|      6 |
|      7 |
|      8 |
+--------+

```

단 이럴 경우 순서가 정해져 있지 않다. 따라서 ORDER BY를 이용하면 순서를 정리할 수 있다. UNION으로 결합할 때 가장 마지막 쿼리 문 다음에 ORDER BY를 사용하면 된다.

```sql
SELECT toy_id FROM girls UNION SELECT toy_id FROM toys ORDER BY toy_id;
```

```bash
MariaDB [gregs_list]> SELECT toy_id FROM girls UNION SELECT toy_id FROM toys ORDER BY toy_id;
+--------+
| toy_id |
+--------+
|      1 |
|      2 |
|      3 |
|      4 |
|      5 |
|      6 |
|      7 |
|      8 |
|      9 |
+--------+

```

유니온을 사용할 때 주의할 점은 각 쿼리문에서 뽑아오는 열의 개수가 다르면 안된다는 것이다. 유니온 올(UNION ALL)을 사용하면 중복된 것도 다 확인할 수 있다.

```sql
SELECT toy_id FROM girls UNION ALL SELECT toy_id FROM toys;
```

```bash
MariaDB [gregs_list]> SELECT toy_id FROM girls UNION ALL SELECT toy_id FROM toys;
+--------+
| toy_id |
+--------+
|      3 |
|      4 |
|      1 |
|      2 |
|      5 |
|      9 |
|      3 |
|      1 |
|      2 |
|      3 |
|      4 |
|      5 |
|      6 |
|      7 |
|      8 |
+--------+
```

유니온은 `CREATE TABLE newtablename AS` 와 결합되어 새로운 테이블을 만드는 데에도 사용할 수 있다.

## 조인과 서브 쿼리 간 공통점 및 차이점

조인과 서브 쿼리는 모두 같은 결과를 만드는데 사용할 수 있다. 또한 쿼리 구문에서 열 이름에 대한 AS(별명)를 사용할 수 있다. 반면 차이점의 경우 약 3가지가 있다.

1. 조인의 경우가 서브 쿼리보다 빠르게 결과를 도출한다.

2. 조인의 경우 여러 테이블에서 필요한 열을 뽑는 유일한 방법이다. 반면 서브 쿼리의 경우 집계 값을 다룰 때 사용하는 유일한 방법이다. 즉 조인의 경우 결과로 여러 열을 반환하기에 집계 값을 다루는 데 어려움이 있다.

3. 서브 쿼리의 경우 UPDATE, DELETE, INSERT 구문과 함께 사용할 수 있는 반면 조인은 그렇지 못하다.
