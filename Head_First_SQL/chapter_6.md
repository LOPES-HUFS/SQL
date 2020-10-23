
# 6장 고급 SELECT문

이번 장에서는 순서를 정하고 데이터를 '그룹핑'하고 결과에 '수학 연산'을 수행하는 방법을 살펴볼 것이다.

## 사용할 테이블 만들기

먼저 아래의 코드를 입력하여 사용할 테이블을 생성하자.

```sql
--사용할 테이블 입력--
USE gregs_list;

--테이블 생성--
CREATE TABLE movie_table(title VARCHAR(100), rating VARCHAR(3),
     drama CHAR(1), comedy CHAR(1), action CHAR(1), sf CHAR(1),
     cartoon CHAR(1), category VARCHAR(100));

--데이터 입력--
INSERT INTO movie_table
    VALUE
        ('Big Adventure', 'G', 'F','F','F','F','T', NULL),
        ('Mad Clowns', 'R', 'F','T','F','F','F', NULL),
        ('Paraskavedekatriaphobia', 'R', 'F','F','T','T','F', NULL),
        ('End of the Line', 'PG','T','F','F','T','T', NULL),
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

`category`는 각 영화의 장르를 나타낸다. 하지만 위에 보면 열의 `category`가 전부 `NULL`값으로 되어 있다. 해당 열에 값들을 채워보자. 각 영화의 장르는 앞 열의 값들을 통해 알 수 있다. 예를 들어 `Big Adventure`의 경우 열 `cartoon`의 값만 `T` 즉, True 이므로 cartoon 인 것을 알 수 있다. 이를 쿼리로 나타내면 다음과 같다.

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

열 `drama`의 값이 'T'인 레코드 `Angry Pirate`는 `category` 값으로 "drama"로 변경되었으며, 레코드 `End of the Line`는 `category` 값으로 "cartoon"로 변경되었다.

하지만 `End of the Line`를 주목해보자. 해당 레코드는 열 `drama`에만 True의 값을 가지는 것은 아니라 열 `sf`, `cartoon`에서도 True 값을 가진다.

**이런 경우, 마지막으로 실행된 UPDATE문 결과가 적용된다.**

즉, `End of the Line`의 `category`는 drama -> cartoon 순으로 변경되어 결과적으로는 `cartoon`값을 가지는 것이다.

하지만 `End of the Line`의 `category`를 "drama"로 유지하고 싶다면 이런 경우 어떻게 해야 할까? 바로 조건문을 사용하면 된다.

## 조건문 명령어 CASE

명령어 `CASE`는 조건을 여러 개 설정하여 그에 따라 값을 변경할 수 있도록 해준다. 아래의 코드를 통해 사용법을 알아보자.

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

위 코드는 WHEN 조건에 해당되는 경우에 값을 변경하고 바로 종료한다. 위 코드의 결과는 다음과 같다.

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

만약 위 테이블을 열 `title`의 값 알파벳 순서를 기준으로 정렬하고 싶다고 생각해보자. 실제로 해당 테이블의 순서는 엉망이다. 이럴 때 명령어 `ORDER BY`를 사용한다. 아래의 코드를 입력해보고 이전과 비교해보자.

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

그렇다면 명령어 `ORDER BY`는 어떤 순서로 정렬할까? 이를 확인해보기 위해서 아래의 테이블을 하나 생성한 후 정렬해보자.

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

