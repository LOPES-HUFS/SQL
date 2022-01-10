# 여러 개의 테이블 다루기 (multi-table database design)

다음 '나이젤에게 사랑 찾아주기'에서는 `my_contacts`이라는 테이블을 사용한다. 이 테이블은 지금까지 이 교재를 잘 따라왔다면 `gregs_list`이라는 데이터 베이스에 `my_contacts`가  앞에서 만들어져 있을 것이다. 확인해보자.

```sql
MariaDB [(none)]> USE gregs_list;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [gregs_list]> DESC my_contacts;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| contact_id | int(11)      | NO   | PRI | NULL    | auto_increment |
| phone      | varchar(10)  | YES  |     | NULL    |                |
| last_name  | varchar(30)  | YES  |     | NULL    |                |
| first_name | varchar(20)  | YES  |     | NULL    |                |
| email      | varchar(50)  | YES  |     | NULL    |                |
| gender     | char(1)      | YES  |     | NULL    |                |
| birthday   | date         | YES  |     | NULL    |                |
| profession | varchar(50)  | YES  |     | NULL    |                |
| location   | varchar(50)  | YES  |     | NULL    |                |
| status     | varchar(20)  | YES  |     | NULL    |                |
| interests  | varchar(100) | YES  |     | NULL    |                |
| seeking    | varchar(100) | YES  |     | NULL    |                |
| city       | varchar(50)  | YES  |     | NULL    |                |
| state      | varchar(2)   | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
14 rows in set (0.003 sec)
```

만약 다음과 같은 메세지가 나온다면 `gregs_list` 데이터베이스가 없는 것이다. 물론 `my_contacts` 테이블도 없을 것이다.

```sql
MariaDB [(none)]> USE gregs_list;
ERROR 1049 (42000): Unknown database 'gregs_list'
```

그러면 만들어 보자. 우선 데이터베이스를 만들고 이곳에 `my_contacts`을 만들자.

```sql
CREATE DATABASE gregs_list;
USE gregs_list;

CREATE TABLE my_contacts(
    contact_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    last_name VARCHAR(30) default NULL,
    first_name VARCHAR(20) default NULL,
    email VARCHAR(50) default NULL,
    birthday date default NULL,
    profession VARCHAR(50) default NULL,
    phone VARCHAR(10) default NULL,
    gender char(1) default NULL,
    city VARCHAR(50),
    state VARCHAR(2),
    status VARCHAR(20) default NULL,
    interests VARCHAR(100) default NULL,
    seeking VARCHAR(100) default NULL
    );

INSERT INTO my_contacts(
    contact_id, last_name, first_name, email, birthday, profession, phone, gender, city, state, status, interests, seeking
)
VALUES (
    NULL, 'Fiore', 'Carla', 'cfiore@fioreanimalclinic.com', '1974-01-07', 'Veterinarian', '5557894855', 'F', 'Round Rock', 'TX', 'single', 'horseback riding, movies, animals, mystery novels, hiking', 'single M'
);

INSERT INTO my_contacts(
    contact_id, last_name, first_name, email, birthday, profession, phone, gender, city, state, status, interests, seeking
)
VALUES (
    NULL, 'Ferguson', 'Alexis', 'alexis@yahoo.com', '1956-09-19', 'Artist', '5550983474', 'F', 'Pflugerville', 'MA', 'single', 'animals, Taxidermy', 'single M'
);
```

## 나이젤에게 사랑 찾아주기

외로운 친구 나이젤에게 관심사((interests)가 같은 여자를 소개시켜주자!

나이젤의 데이터

- 일련변호(contact_id): 341
- 성(last_name): 무어(Moore)
- 이름(first_name): 나이젤(Nigel)
- 전화번호(phone): 5552311111
- 이메일(email): nigelmoore@ranchersrule.com
- 성별(gender): M
- 생일(birthday): 1975-08-28
- 직업(profession): 목장주(Rancher)
- 도시(city): 오스틴(Austin)
- 주(state): 텍사스(TX)
- 결혼여부(status): 미혼(single)
- 관심사(interests): 동물(animals), 말타기(horsebackriding), 영화(movies)
- 원하는 것(seeking): 미혼 여자(single F)

**주의할 점**: 관심사(interests) 열이 원자적이지 않다.

**To do**: 나이젤을 위한 쿼리 작성하기

```sql
/* my_contact 테이블에서 아래에 조건에 맞는 모든 것을 찾는다. */
SELECT * FROM my_contact
/* 우선 여성 */
WHERE gender = 'F'
/* 미혼 */
AND status = 'single'
/* 같은 주 */
AND state = 'TX'
/* 찾는 사람도 미혼 남자를 찾아야 한다. */
AND seeking LIKE '%single M%'
/* 나이 차이가 아래 위로 5살 차이!*/
AND birthday > '1970-08-28'
AND birthday < '1980-08-28'
/* 관심사 3개가 모두 같으면 좋겠다! */
AND interests LIKE '%animals%'
AND interests LIKE '%horseback riding%'
AND interests LIKE '%movies%';
```

주석이 포함되어 있으면 에러가 발생할 수 있으니 아래 코드를 사용하자.

```sql
SELECT * FROM my_contacts
WHERE gender = 'F'
AND status = 'single'
AND state = 'TX'
AND seeking LIKE '%single M%'
AND birthday > '1970-08-28'
AND birthday < '1980-08-28'
AND interests LIKE '%animals%'
AND interests LIKE '%horseback riding%'
AND interests LIKE '%movies%';
```

직접 실행하면 다음과 같다. 참고로 결과는 생략했다.

```sql
MariaDB [gregs_list]> SELECT * FROM my_contacts
    -> WHERE gender = 'F'
    -> AND status = 'single'
    -> AND state = 'TX'
    -> AND seeking LIKE '%single M%'
    -> AND birthday > '1970-08-28'
    -> AND birthday < '1980-08-28'
    -> AND interests LIKE '%animals%'
    -> AND interests LIKE '%horseback riding%'
    -> AND interests LIKE '%movies%';
```

다른 친구 레기스도 여자 친구를 소개해달라고 합니다. 나이차가 다섯 살 아래 여자를 찾고 있습니다.

레기스의 데이터

- 일련변호(contact_id): 873
- 성(last_name): 설리반(Sullivan)
- 이름(first_name): 나이젤(Regis)
- 전화번호(phone): 5552311122
- 이메일(email): me@kathieleeisaflake.com
- 성별(gender): M
- 생일(birthday): 1955-03-20
- 직업(profession): 코메디언(Comedian)
- 도시(city): 오스틴(Cambridge)
- 주(state): 텍사스(MA)
- 결혼여부(status): 미혼(single)
- 관심사(interests): 동물(animals), 카드 교환(trading cards), 보물찾기(geocashing)
- 원하는 것(seeking): 미혼 여자(single F)

`my_contacts`에 자료가 많다고 가정하면 위와 같이 `AND interests LIKE '%animals%'`과 같은 형식을 많이 사용한다면 결과가 느리게 나올 수 있다. 빠르게 하기 위해 `interests`에서 `animals` 하나만 사용해서 찾는 쿼리를 작성하면 다음과 같다.

(요기에 내용을 추가한다.)

문제가 발생했다. 레기스를 위해 찾아준 사람은 동물도 좋아하지만, 아주 심각한 다른 취미도 있었다. 이런 문제를 해결하기 위하여 만약 `interests`을 분리하여 `interests1`, `interests2`과 같이 여러 개의 컬럼을 만든다고 해도 쿼리가 간단해지지 않는다. 원자적 대이터에 대한 규칙도 위반하게 된다.

초기에 테이블을 잘 설계하면 쿼리의 복잡도가 낮아지며, 원하는 데이터를 더 쉽게 찾을 수 있다. 그렇다면 테이블을 잘 설계하는 방법은 무엇인가? 그 방법 중 하나가 여러 개의 테이블을 사용하는 것이다. 여러 개의 테이블을 사용하면 각 카테고리에 대한 세부 정보들을 체계적으로 정리할 수 있다.

## 광대 추적 데이터베이스

이번 장에서는 이전에 사용했었던 테이블 `clown_info`를 사용할 것이다. 아래의 코드를 입력하여 불러오자.

```sql
USE gregs_list
SELECT * FROM clown_info;
```

그러면 아래와 같은 테이블을 볼 수 있다.

```sql
+------------+----------------------------+---------------------------------------+-----------------------+
| name       | last_seen                  | appearance                            | activities            |
+------------+----------------------------+---------------------------------------+-----------------------+
| Elsie      | Cherry Hill Sennior Center | F, red hair, green dress, huge feet   | balloons, little car  |
| Pickles    | Jack Green's Party         | M, orange hair, blue suit, huge feet  | mime                  |
| Sunggles   | Ball-Mart                  | F, yellow shirt, baggy red pants      | horn, umbrella        |
| Mr.Hobo    | BG Circus                  | M, cigar, black hair, tiny hat        | violin                |
| Clarabelle | Belmont Senior Certer      | F, pink hair, huge flower, blue dress | yelling, dancing      |
| Scooter    | Oakland Hospital           | M, blue hair, red suit, huge nose     | balloons              |
| Zippo      | Millstone Mall             | F, orange suit, baggy pants           | dancing               |
| Babe       | Earl's Autos               | F, all pink and sparkly               | balancing, little car |
| Bonzo      | NULL                       | M, in drag, polka dotted dress        | singing, dancing      |
| Sniffles   | Tracy's                    | M, green and puple suit, pointy nose  | NULL                  |
+------------+----------------------------+---------------------------------------+-----------------------+
10 rows in set (0.002 sec)
```

위 테이블에는 기본키 설정이 안 되어 있다. 따라서 아래의 코드를 입력하여 기본키를 생성해주자.

```sql
ALTER TABLE clown_info
    ADD COLUMN id INT NOT NULL AUTO_INCREMENT FIRST,
    ADD PRIMARY KEY(id);
```

위 테이블에서 카테고리 appearance, activities는 여러개의 데이터가 포함되어 있다. 만약 해당 테이블에서 여성(F)이고, 큰 발을 가진(huge feet) 사람을 찾는다면 어떻게 쿼리를 줘야 할까?

```sql
SELECT * FROM clown_info
WHERE SUBSTRING_INDEX(appearance,',',1) = 'F'
AND appearance LIKE '%huge feet%';
```

쿼리가 훨씬 복잡해진 것을 느낄 수 있다. 그 이유로는 카테고리 appearance가 원자적 데이터를 가지고 있지 않기 때문이다. 이와 같이 한 카테고리에 많은 정보가 들어가야 하는 경우 해당 카테고리를 분리하고 새로운 테이블을 만들어 사용하면 훨씬 체계적으로 데이터를 관리할 수 있다.

**NOTE**
위 코드에서 사용된 명령어 `SUBSTRING_INDEX(카테고리 이름,'분리단위',위치)`는 한 카테고리에서 지정된 위치까지의 정보를 가져온다. 즉, 위에서는 카테고리 appearance에서 콤마(,)로 구분된 데이터들 중에 1번째 데이터까지 가져오라는 의미이다.

## 테이블 간의 관계도, 스키마

스키마란 데이터베이스 내의 데이터(열들과 테이블들), 그리고 데이터들 사이의 연결 방식에 대한 표현을 뜻한다. 이 말이 어렵다면, 데이터들의 위치를 알려주는 지도와 같다고 생각해도 좋다.

우리는 `clown_info`을 아래와 같은 단위로 살펴볼 것이다.

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

위의 스키마는 `clown_info`에서 여러 값을 가진 카테고리인 appearance, activities를 다른 테이블로 생성한 뒤 연결한 모습이다. 지금부터는 어떻게 하나의 테이블을 위 스키마의 형태처럼 변경하는지 하나씩 살펴볼 것이다.

## 참조키 (foreign key)

각각의 테이블들을 살펴보기에 앞서 위 스키마에서 'foreign key'라고 쓰여있는 카테고리를 찾을 수 있다. 이는 참조키를 표시한 것인데, 참조키란 무엇이며 왜 사용하는걸까?

쉽게 설명한다면 참조키는 기본키와 세트로 사용되며 각각의 테이블의 레코드 번호를 맞추어 테이블 간의 관계를 정립해준다. 즉, 참조키는 연결할 다른 테이블의 기본키라고도 볼 수 있다. 참조키의 특징은 다음과 같다.

- 참조키는 연결되는 기본키와 다른 이름일 수 있다.
- 참조키에서 참조하는 기본키는 부모키라고도 한다. 부모키가 있는 테이블을 부모 테이블이라고 한다.
- 참조키는 한 테이블의 열들이 다른 테이블의 열과 연결되도록 하는데 사용할 수 있다.
- 기본키는 NULL일 수 없지만 참조키는 NULL일 수 있다.
- 참조키는 유일할 필요가 없다. (실제 유일하지 않은 경우도 많다)

참조키에 NULL이 쓰일 필요가 있을까? 고민해보면 생각보다 많은 경우가 있다. 위에서 사용했던 스키마를 바탕으로 예를 들어보자. 해당 상황의 조건은 새로 테이블을 만들지 않고 50가지의 activity 항목이 있는 기존 테이블을 사용해야 한다는 전제를 가진다.

Situation: 50가지의 activity 항목이 있는 테이블이 있으며, 우리는 `clown_info` 테이블의 키본키를 사용하여 각 광대들의 activity 들을 연결하려고 한다.

위 상황에서 50가지 항목 중에는 광대들의 activity가 속하는 항목도 있고 아닌 항목도 있다. 이런 경우 해당하지 않는 항목의 참조키에는 NULL이 포함된다.

## 참조키의 제약조건

하지만 먄악 위의 경우가 없이 부모 테이블의 기본키를 참조키로 연결하려면 어떻게 해야할까? 즉, 해당 자식 테이블은 NULL값 없이 참조키는 무조건 부모 테이블의 기본키 값 중 하나를 가져야 하는 것이다. 이런 제약조건을 '참조 무결성(referential integrity)'라고 한다. 제약조건을 포함하여 참조키를 설정하는 방법은 아래와 같다. 아래의 코드는 위 스키마에서 `activities` 테이블을 만드는 코드이다. 해당 참조키는 부모 테이블인 `clown_info`의 기본키를 제약조건으로 설정한다.

```sql
CREATE TABLE activities(
    act_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    activities VARCHAR(30),
    id INT NOT NULL,
    CONSTRAINT clown_info_id_fk FOREIGN KEY (id) REFERENCES clown_info (id));
```

위 코드는 id 카테고리를 생성한 후 참조키로 지정한다. 명령어 `CONSTRAINT`는 해당 참조키가 어떤 테이블의 기본키와 제약조건으로 묶이는지를 정의한 것이며, 명령어 `FOREIGN KEY`는 해당 카테고리가 참조키임을 나타낸 것이다. 마지막으로 `REFERENCES`는 부모 테이블과 기본키 정보를 저장한다.
위 코드를 입력하고 테이블 정보를 확인하면 아래와 같다.

```sql
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| act_id     | int(11)     | NO   | PRI | NULL    | auto_increment |
| activities | varchar(30) | NO   |     | NULL    |                |
| id         | int(11)     | NO   | MUL | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
3 rows in set (0.005 sec)
```

해당 테이블에서 참조키는 `MUL`이라고 표시되었다. 이는 기본키와는 다르게 값은 값도 여러 번 입력될 수 있다는 것을 보여주는 것이다.

## 참조키가 필요한 이유

그렇다면 왜 굳이 참조키를 설정해야 하는가? 굳이 참조키를 설정하지 않고 테이블을 따로 관리해도 괜찮지 않을까? 이에 대한 답변으로는 고아(orphan)를 만들지 않기 위해서이다.
만약에 수많은 자식 테이블에 참조키로 들어가 있는 부모 테이블의 레코드가 삭제된다면 어떻게 될까? 부모를 잃은 자식 테이블들의 레코드는 쓸모없는 존재가 된다. 즉, 불러올 일이 없는 것이다. 이 부분을 막기 위해서 참조키의 제약조건을 설정한다. 이렇게 제약조건을 설정하면 부모 테이블의 기본키가 참조된 자삭 테이블을을 삭제해야 부모 테이블의 정보를 삭제할 수 있다. 이해가 어렵다면 아래의 예시를 살펴보자.

먼저 부모 테이블 `clown_info`와 자식 테이블인 `activities`를 이용해보자. 아래의 코드를 입력하여 `activities`에 데이터를 입력해보자.

```sql
INSERT INTO activities
VALUES
(NULL, 'dance', 2),
(NULL, 'singing', 1),
(NULL, 'reading', 1),
(NULL, 'walking', 2);
```

위의 데이터들을 추가하면 `activities`가 아래와 같이 변경된다.

```sql
+--------+------------+----+
| act_id | activities | id |
+--------+------------+----+
|      1 | dance      |  2 |
|      2 | singing    |  1 |
|      3 | reading    |  1 |
|      4 | walking    |  2 |
+--------+------------+----+
4 rows in set (0.001 sec)
```

이 상황에서 부모 테이블인 `clown_info`에서 `id`가 1인 레코드를 삭제해보자. 만약 삭제가 된다면 `activities`의 2,3번은 고아가 된다.

```sql
DELETE FROM clown_info WHERE id = 1;
```

위의 코드를 입력하여 부모 테이블의 `id`가 1번인 레코드를 삭제해보자.

```sql
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`gregs_list`.`activities`, CONSTRAINT `clown_info_id_fk` FOREIGN KEY (`id`) REFERENCES `clown_info` (`id`))
```

자식이 있는 부모 레코드를 지우려고 하면 위와 같은 에러가 발생한다. 고아가 발생하는 것을 방지하기 위해서이다. 자식이 있는데 부모가 사라진다면 자식은 그대로 고아가 되기 때문이다. 만약 부모 테이블의 레코드를 삭제하려면 자식 테이블의 레코드를 먼저 삭제해야 한다.

```sql
DELETE FROM activities WHERE id = 1;
+--------+------------+----+
| act_id | activities | id |
+--------+------------+----+
|      1 | dance      |  2 |
|      4 | walking    |  2 |
+--------+------------+----+
2 rows in set (0.001 sec)
```

자식 테이블에서 참조키가 1인 데이터가 모두 삭제되었다. 이제 다시 부모 테이블에서 기본키가 1인 레코드를 삭제해보자.

```sql
 DELETE FROM clown_info WHERE id = 1;
```

그후 다시 부모 테이블을 불러오면 삭제가 정상적으로 이루어진 것을 확인할 수 있다.

```sql
SELECT * FROM clown_info;
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
9 rows in set (0.001 sec)
```

그렇다면 왜 굳이 자식 테이블의 참조키 레코드들을 다 지워야 할까? 바로 데이터 용량 때문이다. 만약 부모 테이블에서 삭제되었는데 자식 테이블에 남아있다면 사실상 해당 자료는 쓸모가 없다. 하지만 DB의 용량은 차지하고 있다. 이런 테이블 구조에서는 자식 테이블의 용량은 계속 증가할 수밖에 없으며, 결국 쿼리 속도가 느려진다. 또한 나중에 이를 해결하려고 하면 어떤 데이터가 고아인지 찾아야 하기에 매우 복잡해진다.

## 테이블간의 관계

지금까지는 테이블들을 연결하는 방법에 대해서 알아보았다. 그렇다면 어떤 테이블을 어떻게 연결하는 것이 좋을까? 이 질문이 생길 것이다. 따라서 이제부터는 데이블간의 관계 종류를 살펴보자.

테이블간의 관계는 크게 3가지로 분류된다.

- 일대일 관계 : 부모 테이블 레코드 한 개와 자식 테이블의 레코드 한 개가 연결되어 있다.
- 일대다 관계 : 부모 테이블 레코드 한 개와 자식 테이블의 레코드 여러 개가 연결되어 있다.
- 다대다 관계 : 부모 테이블 레코드 여러 개와 자식 테이블 레코드 여러 개가 연결되어 있다.

글로만 이해하기는 어려울 수 있다. 따라서 예시로 우리가 만들 테이터베이스의 스키마를 다시 한번 상키시켜보자.

```bash
[clown_info]      [activities]
- id(key)  ㅡㅡㅡ  - act_id(key)
- name    ㅣ      - activities
-last seenㅣ      - id (foreign key)
          ㅣ
          ㅣ      [info_appearance]
          ㄴㅡㅡ   - id (foreign key)
                 - apperance_id (foreign key) ㅡㅡㅡ [appearance]
                                                   - appearance_id (key)
                                                   - appearance
```

위의 스키마를 예시로 설명하면서 테이블간의 관계를 하나씩 살펴보자.

1.일대일 관계

일대일 관계는 이해하기 가장 쉬운 간단한 형태이다. 즉, **부모 테이블 기본키 = 자식 테이블 참조키**라고 정의할 수 있다. 부모 테이블의 하나의 레코드와 자식의 참조키가 하나인 경우이다. 물론 부모 테이블의 기본키 순서와 자식 테이블의 참조키 순서가 다를 순 있지만, 하나의 부모 테이블의 기본키가 자식 테이블의 참조키 여러 개로 들어가지는 않는다.

2.일대다 관계

일대다 관계는 부모 테이블 기본키 하나가 자식 테이블의 참조키로 여러번 들어가 있는 경우이다. 예를 들면 위에서 살펴봤던 부모 테이블인 `clown_info`와 자식 테이블인 `activities`가 여기에 해당한다. `clown_info`에서 기본키인 `2`가 자식 테이블인 `activities`에 여러 번 들어가 있는 것을 확인할 수 있다.

```sql
+--------+------------+----+
| act_id | activities | id |
+--------+------------+----+
|      1 | dance      |  2 |
|      4 | walking    |  2 |
+--------+------------+----+
2 rows in set (0.001 sec)
```

3.다대다 관계

마지막으로 다대다 관계이다. 이 관게는 두 개의 테이블이 서로 참조키를 가지고 있는 경우이다. `clown_info`와 `activities`를 통해서 해당 설정이 가능한지 살펴보자.

먼저 `activities` 데이터를 추가로 입력한다.

```sql
INSERT INTO activities
VALUES
(NULL, 'dance', 4),
(NULL, 'singing', 5),
(NULL, 'reading', 5),
(NULL, 'walking', 3);
```

그러면 `activities`는 아래와 같다.

```sql
+--------+------------+----+
| act_id | activities | id |
+--------+------------+----+
|      1 | dance      |  2 |
|      4 | walking    |  2 |
|      9 | dance      |  4 |
|     10 | singing    |  5 |
|     11 | reading    |  5 |
|     12 | walking    |  3 |
+--------+------------+----+
6 rows in set (0.001 sec)
```

위 `activities`를 보면 dance에는 2,4번이 속해 있으며, walking에는 2,3번이 속해 있다. 해당 데이터는 여러 개의 부모 테이블 레코드와 여러 개의 자식 테이블 레코드가 연결되어 있으므로 다대다 관계이다. 이를 부모 테이블에도 직접 참조키를 추가하여 정의해주자.

```sql
ALTER TABLE clown_info
    ADD COLUMN act_id INT NOT NULL,
    ADD CONSTRAINT activities_act_id_fk FOREIGN KEY (act_id) REFERENCES  activities(act_id);
```

그러면 아래와 같은 결과가 나온다.

```sql
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`gregs_list`.`#sql-alter-1-5a`, CONSTRAINT `activities_act_id_fk` FOREIGN KEY (`act_id`) REFERENCES `activities` (`act_id`))
```

이미 부모-자식 관게가 되었기에 자식의 기본키를 부모 테이블의 참조키로 넣을 수 없다는 것이다. 그럼 다대다 관계를 어떻게 나타낼까? 우리는 중간에 두 테이블의 관계만을 나타내는 테이블이 하나 더 추가함으로써 이를 구현할 수 있다.

이 예시로는 우리가 예시로 사용하는 스키마에서 `clown_info`와 `info_appearance`, `appearance`의 관계이다. 이 예시를 통해서 개념을 살펴보자.

먼저 `appearance`부터 만들어주자.

```sql
-- 테이블 생성 --
CREATE TABLE appearance(
    appearance_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    appearance VARCHAR(30) NOT NULL);

-- 데이터 입력 --
INSERT INTO appearance
VALUES
(NULL, 'huge feet'),
(NULL, 'yellow shirt'),
(NULL, 'red suit'),
(NULL, 'pink hair');
```

그럼 결과는 아래와 같다.

```sql
+---------------+--------------+
| appearance_id | appearance   |
+---------------+--------------+
|             1 | huge feet    |
|             2 | yellow shirt |
|             3 | red suit     |
|             4 | pink hair    |
+---------------+--------------+
4 rows in set (0.001 sec)
```

이제 `clown_info`의 2,5번 모두가 `huge feet`에 해당되고, 4,7번은 `yellow shirt`에 해당하며, 3,9번은 `red suit`에 포함된다고 해보자.

그러면 연결 테이블은 이를 모두 표현해야 한다.

```sql
id - appearance_id
2  -  1
5  -  1
4  -  2
7  -  2
3  -  3
9  -  3
```

이를 나타내는 연결 테이블 `info_appearance`을 생성해보자.

```sql
CREATE TABLE info_appearance(
    id INT NOT NULL,
    appearance_id INT NOT NULL,
    FOREIGN KEY (id) REFERENCES clown_info(id),
    FOREIGN KEY (appearance_id) REFERENCES appearance(appearance_id));
```

그 다음 위의 정보들을 입력해주자.

```sql
INSERT INTO info_appearance
VALUES
(2, 1),
(5, 1),
(4, 2),
(7, 2),
(3, 3),
(9, 3);
```

그후 연결 테이블을 살펴보면 다음과 같다.

```sql
SELECT * FROM info_appearance;

+----+---------------+
| id | appearance_id |
+----+---------------+
|  2 |             1 |
|  5 |             1 |
|  4 |             2 |
|  7 |             2 |
|  3 |             3 |
|  9 |             3 |
+----+---------------+
6 rows in set (0.001 sec)

DESC info_appearance;
+---------------+---------+------+-----+---------+-------+
| Field         | Type    | Null | Key | Default | Extra |
+---------------+---------+------+-----+---------+-------+
| id            | int(11) | NO   | MUL | NULL    |       |
| appearance_id | int(11) | NO   | MUL | NULL    |       |
+---------------+---------+------+-----+---------+-------+
2 rows in set (0.003 sec)
```

모두 참조키인 `MUL`의 형태인 것을 알 수 있다. 이렇게 연결하여 관리하면 `clown_info`와  `appearance`의 데이터를 중복하지 않고 다대다 관계를 구현할 수 있다.
