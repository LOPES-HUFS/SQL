
# JOIN과 다중 테이블 연산

저번 장에서 `clown_info`를 원자적 데이터를 가진 테이블로 만들기 위해서 스키마를 만들고 새로운 `appearance`와 `activities` 테이블을 만들었다. 아래는 해당 데이터베이스의 스키마이다.

```bash
[clown_info]      [activities]
- id(key)  ㅡㅡㅡ  - act_id(key)
- name    ㅣ      - activities
-last seenㅣ      - id (foreign key)
          ㅣ
          ㅣ      [info_apperance]
          ㄴㅡㅡ   - id (foreign key)
                 - apperance_id (foreign key) ㅡㅡㅡ [appearance]
                                                   - appearance_id (key)
                                                   - appearance
```

사실 저번 장에서는 설명을 위해 위의 스키마를 사용했지만 이 스키마는 문제가 있다. 무엇일까?

바로 `activities`의 구조이다. `activities`는 `clown_info`와 다대다 관계이다. 그렇기 때문에 위의 스키마대로 데이터베이스를 만들면 중복된 데이터가 발생하여 제1 정규형을 위반하며 쿼리의 속도가 느려진다. 따라서 다대다 관계에  맞게 스키마를 변경한다.

```bash
[clown_info]     [info_activities] ㅡㅡㅡㅡㅡ [activities]
- id(key)  ㅡㅡㅡ  - id (foreign key)         - act_id(key)
- name    ㅣ      - act_id (foreign key)     - activities
-last seenㅣ
          ㅣ
          ㅣ      [info_apperance]
          ㄴㅡㅡ   - id (foreign key)
                  - apperance_id (foreign key) ㅡㅡㅡ [appearance]
                                                    - appearance_id (key)
                                                    - appearance
```

해당 스키마로 변경을 위해 `activities`에서 참조키를 제거하자. 코드는 다음과 같다.

```sql
--activities 참조키 삭제--
ALTER TABLE activities
    DROP CONSTRAINT clown_info_id_fk;

--activities id 삭제--
ALTER TABLE activities
    DROP COLUMN id;
```

이제 이번 장을 시작할 준비가 완료되었다. 이번 장에서는 위의 스키마에서 이제 생성한 테이블에 값들을 옮기는 방법에 대해서 알아볼 것이다.

## 원자적 데이터로 변경

우선 `clown_info` 테이블을 살펴보자.

```sql
MariaDB [gregs_list]> SELECT * FROM clown_info;
+----+------------+-----------------------+---------------------------------------+-----------------------+
| id | name       | last_seen             | appearance                            | activities            |
+----+------------+-----------------------+---------------------------------------+-----------------------+
|  2 | Pickles    | Jack Green's Party    | M, orange hair, blue suit, huge feet  | mime                  |
|  3 | Sunggles   | Ball-Mart             | F, yellow shirt, baggy red pants      | horn, umbrella        |
|  4 | Mr.Hobo    | BG Circus             | M, cigar, black hair, tiny hat        | violin                |
|  5 | Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress | yelling, dancing      |
|  6 | Scooter    | Oakland Hospital      | M, blue hair, red suit, huge nose     | balloons              |
|  7 | Zippo      | Millstone Mall        | F, orange suit, baggy pants           | dancing               |
|  8 | Babe       | Earl's Autos          | F, all pink and sparkly               | balancing, little car |
|  9 | Bonzo      | NULL                  | M, in drag, polka dotted dress        | singing, dancing      |
| 10 | Sniffles   | Tracy's               | M, green and puple suit, pointy nose  | NULL                  |
+----+------------+-----------------------+---------------------------------------+-----------------------+
9 rows in set (0.017 sec)
```

위 테이블에서 알 수 있듯이  `appearance`와 `activities`는 원자적 데이터로 구성되어 있지 않다. 이 데이터들을 아래의 과정을 거쳐서 스키마에 적합한 데이터베이스를 만들 것이다.

- 기존 테이블에서 데이터 분리하기
- 분리된 데이터를 새로운 테이블에 중복 없이 추가하기 (appearance, activities)
- 각각의 기본키를 연결 테이블에 추가하기  (info_appearance, info_activities)

## 기존 테이블에서 데이터 분리하기

새로운 테이블에 데이터를 추가하기 위해서는 `clown_info`에 있는 데이터들을 분리해주어여 한다. `activities`를 먼저 살펴보자.

```sql
MariaDB [gregs_list]> SELECT activities FROM clown_info;
+-----------------------+
| activities            |
+-----------------------+
| mime                  |
| horn, umbrella        |
| violin                |
| yelling, dancing      |
| balloons              |
| dancing               |
| balancing, little car |
| singing, dancing      |
| NULL                  |
+-----------------------+
9 rows in set (0.002 sec)
```

이제 해당 테이블에서 쉼표(,)로 연결되어 원자적 데이터가 아닌 레코드들을 하나씩 분리할 것이다. 그렇게 하기 위해서는 한 레코드에 들어있는 데이터만큼 열을 생성해주어야 한다. 아래의 코드를 입력하여 데이터가 가장 많은 레코드를 반환해보자.

```sql
 SELECT MAX(Activities) FROM clown_info;

 +------------------+
| MAX(activities)  |
+------------------+
| yelling, dancing |
+------------------+
1 row in set (0.002 sec)
 ```

`activities`에서는 2개이다. 따라서 `clown_info`에 새로운 열 2개를 생성하여 카테고리 `activities`을 원자적 데이터로 분리할 것이다. 이때 사용할 함수는 `SUBSTRING_INDEX()`와 `SUBSTR()`이다.

먼저 새로운 열을 만들어주자.

```sql
ALTER TABLE clown_info
    ADD COLUMN activity1 VARCHAR(30),
    ADD COLUMN activity2 VARCHAR(30);
```

### SUBSTRING_INDEX()

두 번째로 함수 `SUBSTRING_INDEX()`를 사용하여 첫 번째 데이터를 `activity1`에 넣어준다.

```sql
UPDATE clown_info
    SET activity1 = SUBSTRING_INDEX(activities, ',' ,1);

+----+------------+-----------------------+---------------------------------------+-----------------------+-----------+-----------+
| id | name       | last_seen             | appearance                            | activities            | activity1 | activity2 |
+----+------------+-----------------------+---------------------------------------+-----------------------+-----------+-----------+
|  2 | Pickles    | Jack Green's Party    | M, orange hair, blue suit, huge feet  | mime                  | mime      | NULL      |
|  3 | Sunggles   | Ball-Mart             | F, yellow shirt, baggy red pants      | horn, umbrella        | horn      | NULL      |
|  4 | Mr.Hobo    | BG Circus             | M, cigar, black hair, tiny hat        | violin                | violin    | NULL      |
|  5 | Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress | yelling, dancing      | yelling   | NULL      |
|  6 | Scooter    | Oakland Hospital      | M, blue hair, red suit, huge nose     | balloons              | balloons  | NULL      |
|  7 | Zippo      | Millstone Mall        | F, orange suit, baggy pants           | dancing               | dancing   | NULL      |
|  8 | Babe       | Earl's Autos          | F, all pink and sparkly               | balancing, little car | balancing | NULL      |
|  9 | Bonzo      | NULL                  | M, in drag, polka dotted dress        | singing, dancing      | singing   | NULL      |
| 10 | Sniffles   | Tracy's               | M, green and puple suit, pointy nose  | NULL                  | NULL      | NULL      |
+----+------------+-----------------------+---------------------------------------+-----------------------+-----------+-----------+
9 rows in set (0.001 sec)
```

### `SUBSTR()`

마지막으로 함수 `SUBSTR()`을 사용하여 `activities`에서 첫 번째 값을 제거한다.

```sql
UPDATE clown_info
    SET activities = SUBSTR(activities, LENGTH(activity1)+2);

+----+------------+-----------------------+---------------------------------------+-------------+-----------+-----------+
| id | name       | last_seen             | appearance                            | activities  | activity1 | activity2 |
+----+------------+-----------------------+---------------------------------------+-------------+-----------+-----------+
|  2 | Pickles    | Jack Green's Party    | M, orange hair, blue suit, huge feet  |             | mime      | NULL      |
|  3 | Sunggles   | Ball-Mart             | F, yellow shirt, baggy red pants      |  umbrella   | horn      | NULL      |
|  4 | Mr.Hobo    | BG Circus             | M, cigar, black hair, tiny hat        |             | violin    | NULL      |
|  5 | Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress |  dancing    | yelling   | NULL      |
|  6 | Scooter    | Oakland Hospital      | M, blue hair, red suit, huge nose     |             | balloons  | NULL      |
|  7 | Zippo      | Millstone Mall        | F, orange suit, baggy pants           |             | dancing   | NULL      |
|  8 | Babe       | Earl's Autos          | F, all pink and sparkly               |  little car | balancing | NULL      |
|  9 | Bonzo      | NULL                  | M, in drag, polka dotted dress        |  dancing    | singing   | NULL      |
| 10 | Sniffles   | Tracy's               | M, green and puple suit, pointy nose  | NULL        | NULL      | NULL      |
+----+------------+-----------------------+---------------------------------------+-------------+-----------+-----------+
9 rows in set (0.001 sec)
```

위 과정을 열 갯수만큼 반복하면 된다. `activity2`를 변경하는 것은 다음과 같다.

```sql
UPDATE clown_info
    SET activity2 = SUBSTRING_INDEX(activities, ',' ,1)
    SET activities = SUBSTR(activities, LENGTH(activity2)+2);

+----+------------+-----------------------+---------------------------------------+------------+-----------+-------------+
| id | name       | last_seen             | appearance                            | activities | activity1 | activity2   |
+----+------------+-----------------------+---------------------------------------+------------+-----------+-------------+
|  2 | Pickles    | Jack Green's Party    | M, orange hair, blue suit, huge feet  |            | mime      |             |
|  3 | Sunggles   | Ball-Mart             | F, yellow shirt, baggy red pants      |            | horn      |  umbrella   |
|  4 | Mr.Hobo    | BG Circus             | M, cigar, black hair, tiny hat        |            | violin    |             |
|  5 | Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress |            | yelling   |  dancing    |
|  6 | Scooter    | Oakland Hospital      | M, blue hair, red suit, huge nose     |            | balloons  |             |
|  7 | Zippo      | Millstone Mall        | F, orange suit, baggy pants           |            | dancing   |             |
|  8 | Babe       | Earl's Autos          | F, all pink and sparkly               |            | balancing |  little car |
|  9 | Bonzo      | NULL                  | M, in drag, polka dotted dress        |            | singing   |  dancing    |
| 10 | Sniffles   | Tracy's               | M, green and puple suit, pointy nose  | NULL       | NULL      | NULL        |
+----+------------+-----------------------+---------------------------------------+------------+-----------+-------------+
```

`clown_info` 테이블을 확인해보면 잘 분리된 것을 확인할 수 있다.

## 분리된 데이터를 새로운 테이블에 중복 없이 추가하기

다음으로는 분리된 열들을 새로운 테이블에 중복없이 추가해야 한다. 위에서 `clown_info`의 카테고리 'activities'를 분리한 'activity1', 'activity2'를 새로 생성한 테이블 `activities`에 넣어보자.

'activity1', 'activity2'에는 중복되는 값들이 있다. 하지만 테이블 `activities`에는 중복된 값을 넣으면 안된다. 그 이유는 제1 정규형을 지키기 위함도 있으며, 이후 `info_activities`에서 중복된 값들을 참조키로 정의하기 때문이다. 즉, 테이블 `activities`에는 고유값만 있으면 된다.

이 파트를 해결하는 방법은 크게 3가지가 있다.

### 방법 1: SELECT, INSERT 사용하기

첫 번째 방법은 데이터를 직접 선택해서 고유값만 넣는 것이다. 아래의 `GROUP BY`가 중복된 값들을 제거해준다.

```sql
INSERT INTO activities (activities)
    SELECT activity1 FROM clown_info
    GROUP BY activity1
    ORDER BY activity1;

+--------+------------+
| act_id | activities |
+--------+------------+
|     28 | NULL       |
|     29 | balancing  |
|     30 | balloons   |
|     31 | dancing    |
|     32 | horn       |
|     33 | mime       |
|     34 | singing    |
|     35 | violin     |
|     36 | yelling    |
+--------+------------+
9 rows in set (0.001 sec)
```

다음 방법도 테스트해야 하니 해당 테이블을 삭제해주자.

```sql
 DROP TABLE activities;
```

### 방법 2: AS, ALTER 사용하기

다음으로는 새로운 명령어 `AS`를 사용하여 테이블을 생성과 동시에 데이터를 삽입한다.

```sql
CREATE TABLE activities AS
    SELECT activity1 FROM clown_info
    GROUP BY activity1
    ORDER BY activity1;

ALTER TABLE activities
    ADD COLUMN act_id INT NOT NULL AUTO_INCREMENT FIRST,
    ADD PRIMARY KEY(act_id);

+--------+-----------+
| act_id | activity1 |
+--------+-----------+
|      1 | NULL      |
|      2 | balancing |
|      3 | balloons  |
|      4 | dancing   |
|      5 | horn      |
|      6 | mime      |
|      7 | singing   |
|      8 | violin    |
|      9 | yelling   |
+--------+-----------+
9 rows in set (0.001 sec)
```

첫 번째 쿼리는 테이블을 생성할 때 `SELECT`으로 가져오는 데이터를 카테고리로 동시에 만든다. 그 다음 `ALTER`을 사용하여 기본키를 추가해준다.

다음 쿼리도 테스트해봐야 하니 다시 `activities`테이블을 삭제하자.

```sql
 DROP TABLE activities;
```

### 방법 3: CREATE와 INSERT 동시에 하기

마지막으로 테이블 생성을 하는 동시에 데이터를 삽입하는 방법이다. 코드 먼저 확인해보자.

```sql
CREATE TABLE activities(
    act_id INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    activity1 VARCHAR(30)
)   AS
    SELECT activity1 FROM clown_info
    GROUP BY activity1
    ORDER BY activity1;

+--------+-----------+
| act_id | activity1 |
+--------+-----------+
|      1 | NULL      |
|      2 | balancing |
|      3 | balloons  |
|      4 | dancing   |
|      5 | horn      |
|      6 | mime      |
|      7 | singing   |
|      8 | violin    |
|      9 | yelling   |
+--------+-----------+
9 rows in set (0.001 sec)
```

위 코드는 `CREATE`로 먼저 테이블을 생성한 후 `AS`를 사용하여 이름이 같은 열에 데이터를 삽입한다.

## 별명 사용하기

위 코드를 보면 테이블 이름과 열 이름들로 혼동스러울 수 있다. 또한 위의 3가지 방법의 결과에서 두 번째 카테고리의 이름이 `activities`일 때도 있고, `activity1`일 때도 있다. 혼동스럽다. 이럴 땐 특정 열에 별명을 붙여서 혼란을 방지할 수 있다. 별명을 지정하는 방식은 다음과 같다.

```sql
--기존 테이블 삭제--
 DROP TABLE activities;

--새로 테이블 생성--
CREATE TABLE activities(
    act_id INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    cw_acts VARCHAR(30)
)   AS
    SELECT activity1 AS cw_acts FROM clown_info
    GROUP BY cw_acts
    ORDER BY cw_acts;

+--------+-----------+
| act_id | cw_acts   |
+--------+-----------+
|      1 | NULL      |
|      2 | balancing |
|      3 | balloons  |
|      4 | dancing   |
|      5 | horn      |
|      6 | mime      |
|      7 | singing   |
|      8 | violin    |
|      9 | yelling   |
+--------+-----------+
9 rows in set (0.001 sec)
```

열 이름 뿐만 아니라 테이블 이름에서도 별명을 사용할 수 있으며, 이를 활용하면 쿼리를 사용할 때 훨씬 간편하다.

## 카디젼 조인 (크로스 조인)

카디젼 조인은 조건 없이 가능한 모든 행들을 반환하는 쿼리를 말하며, 크로스 조인이라고도 한다. 즉, 두 테이블의 경우의 수를 모두 반환해주는 것이다. 아래의 예시를 확인해보자.

```sql
SELECT cw.name, act.cw_acts
    FROM clown_info AS cw
    CROSS JOIN
    activities AS act;
```

위 코드의 결과는 아래와 같다.

```sql
+------------+-----------+
| name       | cw_acts   |
+------------+-----------+
| Pickles    | NULL      |
| Pickles    | balancing |
| Pickles    | balloons  |
| Pickles    | dancing   |
| Pickles    | horn      |
| Pickles    | mime      |
| Pickles    | singing   |
             .
             .
             .
81 rows in set (0.003 sec)
```

9개 이름과 9개 activity의 경우의 수와 같은 것을 확인할 수 있다.

카디젼 조인을 사용하는 쿼리는 다음과 같이 간단한 방식도 있다.

```sql
SELECT clown_info.name, activities.cw_acts
FROM clown_info,activities;
```

## 내부 조인

내부 조인은 조건을 사용하여 두 레코드를 결합한다. 즉, 제한조건이 있는 크로스 조인이라고 볼 수 있다. 그렇다면 제한조건의 종류는 무엇이 있으며, 그에 따라 결과는 어떻게 달라질까?

### 배우기 위한 준비

이 과정을 배우기 위해서는 `activities`를 살짝 변경해야 한다. 아래의 코드를 입력하여 `clown_info` 기본키를  `activities`에 참조키로 추가하자.

```sql
--테이블 activities 삭제--
 DROP TABLE activities;

--테이블 새로 생성 및 참조키 설정--
CREATE TABLE activities(
    act_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    cw_acts VARCHAR(30),
    id INT NOT NULL,
    CONSTRAINT clown_info_id_fk FOREIGN KEY(id) REFERENCES clown_info(id));

--값 추가--
INSERT INTO activities (cw_acts, id)
    SELECT activity1, id FROM clown_info
    GROUP BY activity1
    ORDER BY activity1;

--결과--
+--------+-----------+----+
| act_id | cw_acts   | id |
+--------+-----------+----+
|      1 | NULL      | 10 |
|      2 | balancing |  8 |
|      3 | balloons  |  6 |
|      4 | dancing   |  7 |
|      5 | horn      |  3 |
|      6 | mime      |  2 |
|      7 | singing   |  9 |
|      8 | violin    |  4 |
|      9 | yelling   |  5 |
+--------+-----------+----+
9 rows in set (0.002 sec)
```

준비가 모두 끝났다. 이제 제한 조건에 따른 내부 조인의 여러 종류를 배워보자.

### 동등 조인

동등 조인은 두 테이블에 같은 값이 존재할 때 어떤 값들이 매핑되는지를 보여준다. 아래의 예시를 보면 기본키와 참조키를 사용하여 각 광대들의 활동이 무엇인지를 한번에 볼 수 있도록 반환한다. 참고로 두 개의 열은 각각 다른 테이블에서 도출된 것이다.

```sql
SELECT cw.name, act.cw_acts
FROM clown_info AS cw
    INNER JOIN activities AS act
ON cw.id = act.id;

+------------+-----------+
| name       | cw_acts   |
+------------+-----------+
| Sniffles   | NULL      |
| Babe       | balancing |
| Scooter    | balloons  |
| Zippo      | dancing   |
| Sunggles   | horn      |
| Pickles    | mime      |
| Bonzo      | singing   |
| Mr.Hobo    | violin    |
| Clarabelle | yelling   |
+------------+-----------+
9 rows in set (0.003 sec)
```

### 비동등 조인

비동등 조인은 동등 조인과 반대로 값지 않은 값들만 도출한다.

```sql
SELECT cw.name, act.cw_acts
FROM clown_info AS cw
    INNER JOIN activities AS act
ON cw.id <> act.id;

+------------+-----------+
| name       | cw_acts   |
+------------+-----------+
| Pickles    | NULL      |
| Sunggles   | NULL      |
| Mr.Hobo    | NULL      |
| Clarabelle | NULL      |
| Scooter    | NULL      |
| Zippo      | NULL      |
| Babe       | NULL      |
| Bonzo      | NULL      |
| Pickles    | balancing |
| Sunggles   | balancing |
| Mr.Hobo    | balancing |
             .
             .
             .
72 rows in set (0.002 sec)
```

위 결과를 보면 81개의 경우의 수 중에 같은 값에 해당하는 9개를 제외한 나머지 72개를 도출하는 것을 볼 수 있다.

### 자연 조인

자연 조인은 지정되는 두 테이블의 같은 열 이름을 찾아서 비교한 후 일치하는 행을 반환한다.

```sql
SELECT cw.name, act.cw_acts
FROM clown_info AS cw
    NATURAL JOIN activities AS act;

+------------+-----------+
| name       | cw_acts   |
+------------+-----------+
| Sniffles   | NULL      |
| Babe       | balancing |
| Scooter    | balloons  |
| Zippo      | dancing   |
| Sunggles   | horn      |
| Pickles    | mime      |
| Bonzo      | singing   |
| Mr.Hobo    | violin    |
| Clarabelle | yelling   |
+------------+-----------+
9 rows in set (0.002 sec)
```

`clown_info`와 `activities`은 'id'라는 공통된 열을 가지고 있기 때문에 해당 열의 값이 일치하는 레코드들을 나열한 것이다.
