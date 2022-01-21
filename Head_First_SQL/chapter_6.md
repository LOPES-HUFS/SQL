
# 6장 고급 SELECT문

데이터를 다루다보면 원하는 값들만 뽑아보고 싶을 때가 있습니다.
이번 장에서는 순서를 정해야 하거나, 데이터를 특정 조건에 맞는 값끼리 '그룹핑'하거나 혹은 
주어진 값들을 토대로 '수학 연산'을 수행하는 방법을 살펴볼 것입니다.


## 사용할 테이블 만들기

먼저 아래의 코드를 입력하여 사용할 테이블을 생성하고 데이터를 입력해서 살펴봅시다

```sql
--사용할 테이블 입력--

USE gregs_list;

우리가 비디오 가게가 가지고 가지고 있는 비디오 자료를 정리하려고 한다고 가정해 봅시다. 현재 우리가 가지고 있는 자료를 다음과 같다고 해봅시다.

MariaDB [gregs_list]> SELECT * FROM movie_table;
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+
| movice_id | title                   | rating | drama | comedy | action | gore | sdifi | for_kids | cartoon | purchased  |
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+
|         1 | Monsters, Inc.          | G      | F     | T      | F      | F    | F     | T        | T       | 2002-03-06 |
|         2 | The Godfather           | R      | F     | F      | T      | T    | F     | F        | F       | 2001-02-05 |
|         3 | Gone with the Wind      | G      | T     | F      | F      | F    | F     | F        | F       | 1999-11-20 |
|         4 | American Pie            | R      | F     | T      | F      | F    | F     | F        | F       | 2003-04-19 |
|         5 | Nightmare on Elm Street | R      | F     | F      | T      | T    | F     | F        | F       | 2003-04-19 |
|         6 | Casablanca              | PG     | T     | F      | F      | F    | F     | F        | F       | 2001-02-05 |
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+
6 rows in set (0.002 sec)
```

위의 테이블을 만든 방법은 다음과 같습니다.

```sql
--사용할 테이블 입력--
USE gregs_list;

--테이블 생성--
CREATE TABLE movie_table(
    movice_id INT NOT NULL AUTO_INCREMENT,
    title VARCHAR(100),
    rating VARCHAR(3),
    drama CHAR(1),
    comedy CHAR(1),
    action CHAR(1),
    gore CHAR(1),
    sdifi CHAR(1),
    for_kids CHAR(1),
    cartoon CHAR(1),
    purchased Date,
    PRIMARY KEY (movice_id));

--데이터 입력--
INSERT INTO movie_table
    VALUE
        (NULL, 'Monsters, Inc.', 'G', 'F', 'T', 'F', 'F', 'F','T', 'T', '2002-03-06'),
        (NULL, 'The Godfather', 'R', 'F', 'F', 'T', 'T', 'F','F', 'F', '2001-02-05'),
        (NULL, 'Gone with the Wind', 'G', 'T', 'F', 'F', 'F', 'F','F', 'F', '1999-11-20'),
        (NULL, 'American Pie', 'R', 'F', 'T', 'F', 'F', 'F','F', 'F', '2003-04-19'),
        (NULL, 'Nightmare on Elm Street', 'R', 'F', 'F', 'T', 'T', 'F','F', 'F', '2003-04-19'),
        (NULL, 'Casablanca', 'PG', 'T', 'F', 'F', 'F', 'F','F', 'F', '2001-02-05');
```

이 테이블을 토대로 7개의 장르별로 되어 있는 새 진열대에 비디오를 놓으려고 합니다. 그러나 테이블 자료를 잘 살펴보면, 여러가지 문제가 있습니다. 먼저 비디오가 여러 장르에 걸쳐 있는 것을 확인할 수 있습니다. 여러 장르에 걸쳐 비디오 영화가 어느 장르에 속하는지 정확히 알기 어렵습니다. 
참, 거짓 값에 우선 순위가 없기 때문에, 코미디 섹션에 공포 영화 비디오가 들어갈 수도 있습니다. 마지막으로 참, 거짓으로 데이터를 채우는 것에는 시간이 오래걸리며, 에러가 발생할 가능성도 커집니다. 따라서 새롭게 진열을 하기 위해서는 장르 카테고리가 필요합니다.

ALTER를 사용하여 CATEGORY COLUMN을 추가해줘봅시다.


```sql
ALTER TABLE movie_table ADD COLUMN category VARCHAR(100);
```

```bash
MariaDB [gregs_list]> ALTER TABLE movie_table ADD COLUMN category VARCHAR(100);
Query OK, 0 rows affected (0.007 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```sql
UPDATE movie_table SET category = 'drama' WHERE drama = 'T';
UPDATE movie_table SET category = 'comedy' WHERE comedy = 'T';
UPDATE movie_table SET category = 'action' WHERE action = 'T';
UPDATE movie_table SET category = 'horror' WHERE gore = 'T';
UPDATE movie_table SET category = 'scifi' WHERE scifi = 'T';
UPDATE movie_table SET category = 'family' WHERE for_kids = 'T';
UPDATE movie_table SET category = 'family' WHERE cartoon = 'T' AND rating = 'G';
UPDATE movie_table SET category = 'misc' WHERE cartoon = 'T' AND rating <> 'G';

SELECT * FROM movie_table;
```

```bash
MariaDB [gregs_list]> UPDATE movie_table SET category = 'drama' WHERE drama = 'T';
Query OK, 2 rows affected (0.003 sec)
Rows matched: 2  Changed: 2  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'comedy' WHERE comedy = 'T';
Query OK, 2 rows affected (0.002 sec)
Rows matched: 2  Changed: 2  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'action' WHERE action = 'T';
Query OK, 2 rows affected (0.001 sec)
Rows matched: 2  Changed: 2  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'horror' WHERE gore = 'T';
Query OK, 2 rows affected (0.001 sec)
Rows matched: 2  Changed: 2  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'scifi' WHERE scifi = 'T';
ERROR 1054 (42S22): Unknown column 'scifi' in 'where clause'
MariaDB [gregs_list]> UPDATE movie_table SET category = 'family' WHERE for_kids = 'T';
Query OK, 1 row affected (0.001 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'family' WHERE cartoon = 'T' AND rating = 'G';
Query OK, 0 rows affected (0.002 sec)
Rows matched: 1  Changed: 0  Warnings: 0

MariaDB [gregs_list]> UPDATE movie_table SET category = 'misc' WHERE cartoon = 'T' AND rating <> 'G';
Query OK, 0 rows affected (0.003 sec)
Rows matched: 0  Changed: 0  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM movie_table;
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+----------+
| movice_id | title                   | rating | drama | comedy | action | gore | sdifi | for_kids | cartoon | purchased  | category |
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+----------+
|         1 | Monsters, Inc.          | G      | F     | T      | F      | F    | F     | T        | T       | 2002-03-06 | family   |
|         2 | The Godfather           | R      | F     | F      | T      | T    | F     | F        | F       | 2001-02-05 | horror   |
|         3 | Gone with the Wind      | G      | T     | F      | F      | F    | F     | F        | F       | 1999-11-20 | drama    |
|         4 | American Pie            | R      | F     | T      | F      | F    | F     | F        | F       | 2003-04-19 | comedy   |
|         5 | Nightmare on Elm Street | R      | F     | F      | T      | T    | F     | F        | F       | 2003-04-19 | horror   |
|         6 | Casablanca              | PG     | T     | F      | F      | F    | F     | F        | F       | 2001-02-05 | drama    |
+-----------+-------------------------+--------+-------+--------+--------+------+-------+----------+---------+------------+----------+
6 rows in set (0.001 sec)
```

다른 테이블 구성을 만들어서 다시 해봅시다.

```SQL
DROP TABLE movie_table;
```

--테이블 생성--

```sql
CREATE TABLE movie_table(title VARCHAR(100), rating VARCHAR(3),
     drama CHAR(1), comedy CHAR(1), action CHAR(1), sf CHAR(1),
     cartoon CHAR(1), category VARCHAR(100));
```

--데이터 입력--

```sql
INSERT INTO movie_table
    VALUE
        ('Big Adventure', 'G', 'F','F','F','F','T', NULL),
        ('Mad Clowns', 'R', 'F','T','F','F','F', NULL),
        ('Paraskavedekatriaphobia', 'R', 'F','F','T','T','F', NULL),
        ('End of the Line', 'PG','T','F','F','T','T  ', NULL),
        ('Take it back', 'G', 'F','T','F','F','F', NULL),
        ('Angry Pirate', 'PG', 'T','F','F','F','F', NULL);
```



`movie_table`을 열어보면 아래와 같이 테이블이 셋팅된 것을 확인할 수 있다.

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                  | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Big Adventure           | G      | F     | F      | F      | F    | T       | NULL     |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | NULL     |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | NULL     |
| End of the Line         | PG     | T     | F      | F      | T    | T       | NULL     |
| Take it back            | G      | F     | T      | F      | F    | F       | NULL     |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | NULL     |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

`category`는 각 영화의 장르를 나타낸다. 하지만 위에 보면 열의 `category`가 전부 `NULL`값으로 되어 있다. 해당 열에 값들을 채워보자. 
우리에겐 이미 각 장르에 대한 정보가 T와 F로 나눠져있는 각 열들이 있다. 각 영화의 장르는 앞 열의 값들을 통해 알 수 있다. 
예를 들어 `Big Adventure`의 경우 열 `cartoon`의 값만 `T` 즉, True 이므로 장르가 cartoon 인 것을 알 수 있다. 
이를 쿼리로 나타내면 다음과 같다.

```sql
UPDATE movie_table SET category = 'drama' WHERE drama = 'T';
UPDATE movie_table SET category = 'cartoon' WHERE cartoon = 'T';
```

위 코드를 입력한 결과는 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Big Adventure           | G      | F     | F      | F      | F    | T       | cartoon  |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | NULL     |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | NULL     |
| End of the Line         | PG     | T     | F      | F      | T    | T       | cartoon  |
| Take it back            | G      | F     | T      | F      | F    | F       | NULL     |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

열 `drama`의 값이 'T'인 레코드 `Angry Pirate`는 `category` 값이 "drama"로 변경되었으며, 레코드 `End of the Line`는 `category` 값이 "cartoon"로 변경되었다.

고민되는 부분이 있다.
그렇다면 장르가 두 가지 이상으로 겹치는 경우는 어떻게 해야할까?

사례를 통해 보자. `End of the Line`를 주목해보자.
해당 레코드는 열 `drama`에만 True의 값을 가지는 것은 아니라 열 `sf`, `cartoon`에서도 True 값을 가진다.
이럴 때 자료는 update문을 반영해서 어떤 식으로 산출 될까?


**이런 경우, 마지막으로 실행된 UPDATE문 결과가 적용된다.**

즉, `End of the Line`의 `category`는 drama -> cartoon 순으로 변경되어 결과적으로는 `cartoon`의 값을 가지는 것이다.

그렇다면 `End of the Line`의 `category`를 "drama"로 유지하고 싶다면 이런 경우 어떻게 해야 할까? 바로 조건문을 사용하면 된다.


## 조건문 명령어 CASE

명령어 `CASE`는 조건을 여러 개 설정하여 그에 따라 값을 변경할 수 있도록 해준다. 아래의 코드를 통해 사용법을 알아보자.

- `CASE`: 여러 개를 한꺼번에 바꾼다.

```sql
UPDATE movie_table
    SET category =
        CASE
            WHEN drama = 'T' THEN 'drama'
            WHEN comedy = 'T' THEN 'comedy'
            WHEN action = 'T' THEN 'action'
            WHEN sf = 'T' THEN 'scifi'
            WHEN cartoon = 'T' THEN 'family'
            ELSE 'misc'
        END;
```

<<<<<<< HEAD
위 코드는 WHEN 조건에 해당되는 경우에 값을 변경하고 바로 종료한다. 
이 부분이 핵심이다. 단순 UPDATE문은 결과가 덮였지만, CASE 조건문의 경우
해당되는 값을 반환하고 나면 그 다음 단계로 넘어가기에 한번 산출된 값은 덮여지지 않는다.

위 코드의 결과는 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

중복된 장르를 가진 `End of the Line`가 원하던 대로 `category`값 "drama"로 변경된 것을 확인할 수 있다. 참고로 조건문 명령어인 `CASE`는 `INSERT`, `DELETE`에서도 사용할 수 있다.

## 데이터 정렬하기

데이터를 다루다보면 문자, 숫자형 자료를 내림차순 혹은 오름차순 등의 순서로 정렬하고 싶을 때가 있다.
아래부터는 데이터를 정렬하는 법을 살펴본다.

만약 위 테이블을 열 `title`의 값 알파벳 순서를 기준으로 정렬하고 싶다고 생각해보자. 실제로 해당 테이블의 순서는 엉망이다. 이럴 때 명령어 `ORDER BY`를 사용한다. 다시 한번 기억하자. 정렬은 'ORDER BY'다 !

그럼,
아래의 코드를 입력해보고 이전과 비교해보자.


- `ORDER BY`: 결과를 언급한 컬럼을 토대로 알바벳으로 정렬한다.

```sql
SELECT * FROM movie_table
    ORDER BY title;
```

아래의 표는 이전의 결과와 이후의 결과를 보여준다.

```sql
--변경 이전 테이블--
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
+-------------------------+--------+-------+--------+--------+------+---------+----------+

--변경 이후 테이블--
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
```

`title`이 알파벳 순서로 작동하는 것을 확인할 수 있다.

그렇다면 명령어 `ORDER BY`는 어떤 기준으로 순서를 정렬할까? 
이를 확인해보기 위해서 아래의 테이블을 하나 생성한 후 정렬해보자.

```sql
--테스트 테이블 생성--
CREATE TABLE test(test char(1));

--데이터 입력하기--
INSERT INTO test
    VALUE
        (0),(1),(2),(3),(4),(5),(6),(7),(8),(9),
        ('A'),('B'),('C'),('D'),('a'),('b'),('c'),('d'),
        ('!'),('@'),('*'),('$'),('?');
```

위의 코드로 생성된 테이블은 아래와 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM test;
+------+
| test |
+------+
| 0    |
| 1    |
| 2    |
| 3    |
| 4    |
| 5    |
| 6    |
| 7    |
| 8    |
| 9    |
| A    |
| B    |
| C    |
| D    |
| a    |
| b    |
| c    |
| d    |
| !    |
| @    |
| *    |
| $    |
| ?    |
+------+
23 rows in set (0.001 sec)
```

해당 테이블을 정렬하면 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM test ORDER BY test;
+------+
| test |
+------+
| !    |
| $    |
| *    |
| 0    |
| 1    |
| 2    |
| 3    |
| 4    |
| 5    |
| 6    |
| 7    |
| 8    |
| 9    |
| ?    |
| @    |
| a    |
| A    |
| B    |
| b    |
| C    |
| c    |
| d    |
| D    |
+------+
```

실험 결과 명령어 `ORDER BY`의 순서는 기호 - 숫자 - 알파벳이고, 알파벳의 경우 대소문자 구분을 하지 않는다.

다시 한번 기억하자. 
기호 / 숫자/ 알파벳 순이다.


## 여러 열로 정렬하기

다음은 정렬할 것이 한 개의 열이 아닌 경우의 케이스다. 
즉, 하나의 열이 아니라 여러 개의 열을 기준으로 각각 정리하고 싶다면 어떻게 쿼리를 보내야할까? 

예를 들어서 `movie_table`을 `category`를 가장 큰 기준으로 정렬하고 이후 세부적으로는 `title`의 알파벳 순으로 정렬하고 싶다고 가정해보자.

그러면 아래와 같이 쿼리를 보내면 된다.

```sql
SELECT * FROM movie_table
    ORDER BY category, title;
```

위 코드는 첫 번째로 오는 열인 `category`를 기준으로 레코드 순서를 정렬한다. 그 다음 카테고리가 겹치는 데이터들을 두 번째로 오는 `title`을 기준으로 정렬한다.

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table
    ->     ORDER BY category, title;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

위 테이블을 보면 `category`는 알파벳 순서대로 정렬되었으며, 같은 `category`값을 가진 데이터들은 `title`기준으로 정렬된 것을 확인할 수 있다.

## 역순으로 정렬하기

오름차순이 아니라 내림차순으로 정렬하고 싶을 때는 명령어 `DESC`를 `ORDER BY`에 오든 열 이름 뒤에 붙여주면 된다. 쿼리를 확인해보자.

- `DESC`: 역순으로

```sql
SELECT * FROM movie_table
    ORDER BY category, title DESC;
```

위의 쿼리는 `category`는 알파벳 순서대로 정렬하고 같은 `category`안의 데이터들은 `title` 역순으로 정렬하라고 명령한다. 그 결과는 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table
    ->     ORDER BY category, title DESC;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

만약 정렬하는 모든 열들을 내림차순으로 하고싶다면 어떻게 해야할까? 아래의 쿼리를 참고하자.

```sql
SELECT * FROM movie_table
    ORDER BY category DESC, title DESC;
```

```sql
MariaDB [gregs_list]> SELECT * FROM movie_table
    ->     ORDER BY category DESC, title DESC;
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| title                   | rating | drama | comedy | action | sf   | cartoon | category |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
| Big Adventure           | G      | F     | F      | F      | F    | T       | family   |
| End of the Line         | PG     | T     | F      | F      | T    | T       | drama    |
| Angry Pirate            | PG     | T     | F      | F      | F    | F       | drama    |
| Take it back            | G      | F     | T      | F      | F    | F       | comedy   |
| Mad Clowns              | R      | F     | T      | F      | F    | F       | comedy   |
| Paraskavedekatriaphobia | R      | F     | F      | T      | T    | F       | action   |
+-------------------------+--------+-------+--------+--------+------+---------+----------+
6 rows in set (0.001 sec)
```

하나만 내림차순으로 변경했던 테이블과 비교해보면 `category`도 역순으로 정렬된 것을 확인할 수 있다.

## 추가적인 질문!! 업무를 하다보면 단순히 정렬만 하진 않는다. 직원들의 매출액 합산액도 보고 싶고,
## 계산도 해서 깔끔하게 이름순으로 내림차순으로 자료를 뽑아보고 싶을 때가 있을 것이다. 이럴 땐 어떻게 해야할까?

위의 질문을 해결하기 아래의 설명을 따라하자.

## → GROUP BY를 이용해 SUM(), AVG()와 정렬을 함께 해보자 !



## 쿼리로 계산해서 정렬해보기

지금부터는 숫자가 포함된 데이터를 다룰 예정이다. 기존에 생성했던 `easy_drinks` 테이블에 데이터 몇개만 추가하여 사용할 것이다. 아래의 코드를 통해 데이터를 생성하자.

```sql
INSERT INTO easy_drinks  
VALUES
('Blackthorn',  'tonic water', 3, 'pineapple juice', 9 ,'stir with ice, strain into cocktail glass with lemon twist'),
('Blue moon' , 'soda', 4, 'blueberry juice', 0, 'stir with ice, strain into cocktail glass with lemon twist'),
('Oh my Gosh' , 'peach nectar', 5, 'pineapple juice', 7, 'stir with ice, strain into shot glass'),
('Lime  Fizz' , 'sprite', 6, 'lime juice', 1, 'stir with ice, strain into cocktail glass'),
('Kiss on the Lips' , 'cherry juice', 2, 'pineapple juice', 0, 'serve over ice with straw');
```

그러면 `easy_drinks` 테이블은 아래와 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks;
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| drink_name       | main         | amount1 | second          | amount2 | directions                                                 |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| Blackthorn       | tonic water  |    1.50 | pineapple juice |    1.00 | stir with ice, strain into cocktail glass with lemon twist |
| Blue moon        | soda         |    1.50 | blueberry juice |    0.75 | stir with ice, strain into cocktail glass with lemon twist |
| Oh my Gosh       | peach nectar |    1.00 | pineapple juice |    1.00 | stir with ice, strain into shot glass                      |
| Lime  Fizz       | sprite       |    1.50 | lime juice      |    1.00 | stir with ice, strain into cocktail glass                  |
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw                                  |
| Indian's Summer  | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw                                  |
| Blue sun         | NULL         |    1.50 | blueberry juice |    0.75 | NULL                                                       |
| Blackthorn       | tonic water  |    3.00 | pineapple juice |    9.00 | stir with ice, strain into cocktail glass with lemon twist |
| Blue moon        | soda         |    4.00 | blueberry juice |    0.00 | stir with ice, strain into cocktail glass with lemon twist |
| Oh my Gosh       | peach nectar |    5.00 | pineapple juice |    7.00 | stir with ice, strain into shot glass                      |
| Lime  Fizz       | sprite       |    6.00 | lime juice      |    1.00 | stir with ice, strain into cocktail glass                  |
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    0.00 | serve over ice with straw                                  |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
12 rows in set (0.001 sec)
```

## 덧셈 명령어 SUM

먼저 더하는 방법이다. 아래의 코드를 입력해보자.

```sql
SELECT SUM(amount1) FROM easy_drinks
    WHERE drink_name = 'Oh my Gosh';
```

위 코드는 음료 이름이 'Oh my Gosh'인 레코드의 `amount1`을 합한 값을 보여준다. 결과는 아래와 같다.
'oh my gosh' 행의 amount1 값이 두개인데 각각 1과 5다. 두 개를 합한 값이 6이 나올 것이다. 아래르 보자.

```sql
+--------------+
| SUM(amount1) |
+--------------+
|         6.00 |
+--------------+
1 row in set (0.010 sec)
```

만약에 각각의 음료들의 `amount1`을 합한 값을 알고 싶다면 어떻게 해야할까? 
그럴 때는 명령어 `GROUP BY`를 사용하면 된다. 명령어 `GROUP BY`는 지정되는 카테고리의 결과들을 총체적으로 보여준다.
말 그대로 그룹을 기준으로 결과를 만들어주는 것이다.


- `GROUP BY`: 공통 컬럼을 가지고 열들을 통합 정리한다(Consolidate rows based on a common column).

```sql
SELECT drink_name,SUM(amount1) FROM easy_drinks
    GROUP BY drink_name;
```

위 코드는 각 `drink_name`의 `amount1`합을 보여달라는 요청이다. 그 결과는 아래와 같다.

```sql
MariaDB [gregs_list]> SELECT drink_name,SUM(amount1) FROM easy_drinks GROUP BY drink_name;
+------------------+--------------+
| drink_name       | SUM(amount1) |
+------------------+--------------+
| Blackthorn       |         4.50 |
| Blue moon        |         5.50 |
| Blue sun         |         1.50 |
| Indian's Summer  |         2.00 |
| Kiss on the Lips |         4.00 |
| Lime  Fizz       |         7.50 |
| Oh my Gosh       |         6.00 |
+------------------+--------------+
7 rows in set (0.004 sec)
```

## 평균 명렁어 AVG

명령어 `AVG`는 평균을 구해준다. 즉, `SUM`과 사용방법은 동일하지만 더하는 것이 아니라 평균을 구해주는 것이다. 
아래의 코드를 입력하고 결과를 확인해보자.

```sql
SELECT drink_name,AVG(amount1) FROM easy_drinks
    GROUP BY drink_name;

+------------------+--------------+
| drink_name       | AVG(amount1) |
+------------------+--------------+
| Blackthorn       |     2.250000 |
| Blue moon        |     2.750000 |
| Blue sun         |     1.500000 |
| Indian's Summer  |     2.000000 |
| Kiss on the Lips |     2.000000 |
| Lime  Fizz       |     3.750000 |
| Oh my Gosh       |     3.000000 |
+------------------+--------------+
7 rows in set (0.003 sec)
```

## MIN과 MAX

<<<<<<< HEAD
최솟값을 구해주는 명령어 `MIN`과 최댓값을 구해주는 명령어인 `MAX`도 위의 연산 명령어들과 동일하다. 
아래의 코드를 입력하고 결과를 확인해보자.
=======
최솟값을 구해주는 명령어 `MIN`과 최댓값을 구해주는 명령어인 `MAX`도 위의 연산 명령어들과 동일하다. 아래의 코드를 입력하고 결과를 확인해보자.
>>>>>>> afa749b7384685187cb9b66225823ee25aa6114b

### MIN test

```sql
SELECT drink_name,MIN(amount1) FROM easy_drinks
    GROUP BY drink_name;

+------------------+--------------+
| drink_name       | MIN(amount1) |
+------------------+--------------+
| Blackthorn       |         1.50 |
| Blue moon        |         1.50 |
| Blue sun         |         1.50 |
| Indian's Summer  |         2.00 |
| Kiss on the Lips |         2.00 |
| Lime  Fizz       |         1.50 |
| Oh my Gosh       |         1.00 |
+------------------+--------------+
7 rows in set (0.003 sec)
```

### MAX test

```sql
SELECT drink_name,MAX(amount1) FROM easy_drinks
    GROUP BY drink_name;

+------------------+--------------+
| drink_name       | MAX(amount1) |
+------------------+--------------+
| Blackthorn       |         3.00 |
| Blue moon        |         4.00 |
| Blue sun         |         1.50 |
| Indian's Summer  |         2.00 |
| Kiss on the Lips |         2.00 |
| Lime  Fizz       |         6.00 |
| Oh my Gosh       |         5.00 |
+------------------+--------------+
7 rows in set (0.001 sec)
```

## 개수를 세어주는 명령어 COUNT

명령어 `COUNT`는 해당 테이블의 레코드 개수를 확인할 수 있다. 아래의 코드를 입력해보자.

```sql
SELECT COUNT(drink_name) FROM easy_drinks;

+-------------------+
| COUNT(drink_name) |
+-------------------+
|                12 |
+-------------------+
1 row in set (0.001 sec)
```

총 12개의 레코드가 있다고 반환한다. 그렇다면 만약에 NULL값이 있어도 개수로 인식할까? NULL이 포함된 데이터를 추가하여 다시 개수를 세어보자.

```sql
--NULL 추가--
INSERT INTO easy_drinks  
VALUES
(NULL,  'tonic water', 3, 'pineapple juice', 9 ,'stir with ice, strain into cocktail glass with lemon twist');

--테이블 확인--
MariaDB [gregs_list]> SELECT drink_name FROM easy_drinks;
+------------------+
| drink_name       |
+------------------+
| Blackthorn       |
| Blue moon        |
| Oh my Gosh       |
| Lime  Fizz       |
| Kiss on the Lips |
| Indian's Summer  |
| Blue sun         |
| Blackthorn       |
| Blue moon        |
| Oh my Gosh       |
| Lime  Fizz       |
| Kiss on the Lips |
| NULL             |
+------------------+
13 rows in set (0.001 sec)

--다시 COUNT--
MariaDB [gregs_list]> SELECT COUNT(drink_name) FROM easy_drinks;
+-------------------+
| COUNT(drink_name) |
+-------------------+
|                12 |
+-------------------+
1 row in set (0.001 sec)
```

보다시피 NULL값은 카운트되지 않는다.

## 중복 제거하는 명령어 DISTINCT

명령어 `DISTINCT`는 해당 데이터의 고유값들만 보여준다.
비유하자면 엑셀의 중복값 제거 기능과 비슷하다. 
이 easy_drink 테이블의 경우 drink_name이 중복되어 있다. 중복된 값을 날리고
drink_name을 보고 싶을 땐 어떻게 할까?
그럴 때 쓰는게 DISTINCT다.


```sql
SELECT DISTINCT drink_name FROM easy_drinks;

| drink_name       |
+------------------+
| Blackthorn       |
| Blue moon        |
| Oh my Gosh       |
| Lime  Fizz       |
| Kiss on the Lips |
| Indian's Summer  |
| Blue sun         |
| NULL             |
+------------------+
8 rows in set (0.001 sec)
```

여기서는 NULL값도 포함되는 것을 확인할 수 있다.


갯수도 알 수 있다.
만약 중복 제거된 고유값이 몇 개인지 알고 싶다면 아래처럼 쿼리를 주자.


```sql
SELECT COUNT(DISTINCT drink_name) FROM easy_drinks;

+----------------------------+
| COUNT(DISTINCT drink_name) |
+----------------------------+
|                          7 |
+----------------------------+
1 row in set (0.003 sec)
```

역시나 명령어 `COUNT`는 NULL값을 세지 않는다.

## 결과 개수를 제한하는 명령어 LIMIT

만약 내가 `amount1`의 합이 가장 큰 음료 2개만 알고 싶다고 하자. 그러면 아래와 같이 코드를 입력하면 된다.

```sql
SELECT drink_name,SUM(amount1) FROM easy_drinks
    GROUP BY drink_name
    ORDER BY SUM(amount1) DESC;
```

위 코드의 결과는 다음과 같다.

```sql
+------------------+--------------+
| drink_name       | SUM(amount1) |
+------------------+--------------+
| Lime  Fizz       |         7.50 |
| Oh my Gosh       |         6.00 |
| Blue moon        |         5.50 |
| Blackthorn       |         4.50 |
| Kiss on the Lips |         4.00 |
| NULL             |         3.00 |
| Indian's Summer  |         2.00 |
| Blue sun         |         1.50 |
+------------------+--------------+
```

위 결과는 순서를 잘 보여주지만 2개 이상을 보여준다. 만약 정확히 2개만 보고 싶을 때는 명령어 `LIMIT`을 사용하여 개수를 제한하자.

```sql
SELECT drink_name,SUM(amount1) FROM easy_drinks
    GROUP BY drink_name
    ORDER BY SUM(amount1) DESC
    LIMIT 2;

+------------+--------------+
| drink_name | SUM(amount1) |
+------------+--------------+
| Lime  Fizz |         7.50 |
| Oh my Gosh |         6.00 |
+------------+--------------+
2 rows in set (0.002 sec)
```

원하는 정보만 바로 받아볼 수도 있다.
만약 특정 구간만을 제한해서 결과를 보고 싶다면 어떻게 해야 할까? 

아래의 코드는 `amount1`의 합이 큰 음료 중에서 3등부터 5등까지만 보여준다.

```sql
SELECT drink_name,SUM(amount1) FROM easy_drinks
    GROUP BY drink_name
    ORDER BY SUM(amount1) DESC
    LIMIT 2,3;
```

위 코드는 3번째부터 아래로 3개의 데이터를 보여달라는 의미이다. 
SQL에서는 0부터 순서로 시작하기 때문에 2가 세 번째 레코드를 의미한다. 결과는 다음과 같다.

```sql
+------------------+--------------+
| drink_name       | SUM(amount1) |
+------------------+--------------+
| Blue moon        |         5.50 |
| Blackthorn       |         4.50 |
| Kiss on the Lips |         4.00 |
+------------------+--------------+
3 rows in set (0.001 sec)
```

