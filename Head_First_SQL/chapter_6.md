
# 6장 고급 SELECT문

이번 장에서는 순서를 정하고 데이터를 '그룹핑'하고 결과에 '수학 연산'을 수행하는 방법을 살펴볼 것이다.

## 사용할 테이블 만들기

먼저 아래의 코드를 입력하여 사용할 테이블을 생성하자.

```sql
--사용할 테이블 입력--
USE gregs_list;

--테이블 생성--
CREATE TABLE movie_table(titile VARCHAR(100), rating VARCHAR(3), drama CHAR(1), comedy CHAR(1), action CHAR(1), sf CHAR(1), cartoon CHAR(1), category VARCHAR(100));

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
| titile                  | rating | drama | comedy | action | sf   | cartoon | category |
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

