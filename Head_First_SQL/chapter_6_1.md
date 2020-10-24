# 6장 고급 SELECT문

이번 장에서는 순서를 정하고 데이터를 '그룹핑'하고 결과에 '수학 연산'을 수행하는 방법을 살펴보겠습니다.

우리가 비디오 가게가 가지고 가지고 있는 비디오 자료를 정리하려고 한다고 가정해 봅시다. 현재 우리가 가지고 있는 자료를 다음과 같다고 해봅시다.

```sql
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

위에 테이블을 만든 방법은 다음과 같습니다.

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

이 테이블을 토대로 장르별로 되어 있는 진열대에 비디오를 놓으려고 합니다. 그러나 자료를 잘 살펴보면, 비디오가 여러 장르에 걸쳐 있는 것을 확인할 수 있습니다.
