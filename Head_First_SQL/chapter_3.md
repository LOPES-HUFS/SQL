
# 3장 Delete and Update

## 데이터베이스 접속하기

```sql
USE gregs_list;
```

## 사용할 테이블 만들기

이번 장에서는 광대들의 동선을 확인하는 `clown_info`라는 테이블을 사용할 것이다. 아래의 코드를 입력하여 테이블을 생성하자.

```sql
CREATE TABLE clown_info(name VARCHAR(20), last_seen VARCHAR(50), appearance VARCHAR(200), activities VARCHAR(50));
```

## 테이블에 데이터 입력하기

아래의 코드를 사용하여 레코드 값들을 입력한다.

```sql
INSERT INTO clown_info
VALUES
('Elsie', 'Cherry Hill Sennior Center','F, red hair, green dress, huge feet', 'balloons, little car'),
('Pickles','Jack Green\'s Party','M, orange hair, blue suit, huge feet','mime'),
('Sunggles', 'Ball-Mart','F, yellow shirt, baggy red pants','horn, umbrella'),
('Mr.Hobo','BG Circus','M, cigar, black hair, tiny hat','violin'),
('Clarabelle','Belmont Senior Certer', 'F, pink hair, huge flower, blue dress', 'yelling, dancing'),
('Scooter','Oakland Hospital','M, blue hair, red suit, huge nose', 'balloons'),
('Zippo','Millstone Mall', 'F, orange suit, baggy pants','dancing'),
('Babe','Earl\'s Autos','F, all pink and sparkly', 'balancing, little car'),
('Bonzo',NULL,'M, in drag, polka dotted dress', 'singing, dancing'),
('Sniffles','Tracy\'s','M, green and puple suit, pointy nose',NULL);
```

값들을 추가한 후 아래의 코드를 입력하면 값이 추가된 테이블을 확인할 수 있다.

```sql
 SELECT * FROM clown_info;
```

결과는 다음과 같다.

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

## 광대들이 이동한 데이터 추가하기

광대들이 계속 움직이고 있다. 추가적으로 발생하는 광대에 대한 데이터를 아래의 코드로 입력해보자.

```sql
INSERT INTO clown_info
VALUES
('Zippo','Millstone Mall', 'F, orange suit, baggy pants','dancing, singing'),
('Sunggles', 'Ball-Mart','F, yellow shirt, baggy blue pants','horn, umbrella'),
('Bonzo','Dickson Park','M, in drag, polka dotted dress', 'singing, dancing'),
('Sniffles','Tracy\'s','M, green and puple suit, pointy nose', 'little car'),
('Mr.Hobo','Eric Gray\'s Party','M, cigar, black hair, tiny hat','violin');
```

## 광대의 경로 추적하기

광대 중 `Zippo`의 경로들을 추적하고 싶다. 그렇다면 아래의 코드를 입력하며 저장된 `Zippo`의 경로들을 모두 찾아보자.

```sql
SELECT * FROM clown_info WHERE name = 'Zippo';
```

아래의 결과를 볼 수 있다.

```sql
+-------+----------------+-----------------------------+------------------+
| name  | last_seen      | appearance                  | activities       |
+-------+----------------+-----------------------------+------------------+
| Zippo | Millstone Mall | F, orange suit, baggy pants | dancing          |
| Zippo | Millstone Mall | F, orange suit, baggy pants | dancing, singing |
+-------+----------------+-----------------------------+------------------+
2 rows in set (0.005 sec)
```

하지만 위 결과들 중에서 무엇이 가장 최신 경로일까? 보통 데이터베이스는 여러 사람이 운영하며, 순서를 바꾸는 명령도 있기 때문에 테이블의 레코드 순서가 무조건적으로 시간 순이라고 볼 수 없다. 또한 중복되는 데이터들이 누적되면 데이터베이스 성능도 느려진다.

따라서 지급부터는 중복된 레코드를 피해 테이블을 설계하는 방법에 대해 알아볼 것이다.

## 명령어 DELETE

위에서 살펴봤던 광대 `zippo`는 춤만 추다가 현재는 노래까지 한다. 따라서 이전에 춤만 추고 있다는 레코드는 지워야 한다. 따라서 아래의 코드를 입력하여 이전 데이터를 삭제한다.

```sql
DELETE FROM clown_info WHERE activities = 'dancing';
```

그후 다시 아래의 코드를 입력하여 `zippo`의 레코드를 확인해보자.

```sql
SELECT * FROM clown_info WHERE name = 'Zippo';
```

`zippo`의 데이터가 하나만 남은 것을 확인할 수 있다.

```sql
+-------+----------------+-----------------------------+------------------+
| name  | last_seen      | appearance                  | activities       |
+-------+----------------+-----------------------------+------------------+
| Zippo | Millstone Mall | F, orange suit, baggy pants | dancing, singing |
+-------+----------------+-----------------------------+------------------+
```

참고로 `WHERE`없이 명령어 `DELETE`만 입력하면 모든 행이 지워지니 주의하기 바란다.

## INSERT & DELETE

어떤 레코드를 변경하기 위해서는 새로운 레코드를 추가하고 이전의 레코드를 삭제하면 된다. 광대 `Clarabelle`의 정보를 변경해보자.

- INSERT: 새로운 레코드 추가 (행동의 차이가 있음)

```sql
INSERT INTO clown_info
VALUES ('Clarabelle','Belmont Senior Certer', 'F, pink hair, huge flower, blue dress', 'dancing');
```

- DELETE: 이전의 레코드 삭제

```sql
DELETE FROM clown_info WHERE name = 'Clarabelle' AND activities = 'yelling, dancing';
```

변화를 확인해보면 아래와 같다.

```sql
# 변경 전 데이터
MariaDB [gregs_list]> SELECT * FROM clown_info WHERE name = 'Clarabelle';
+------------+-----------------------+---------------------------------------+------------------+
| name       | last_seen             | appearance                            | activities       |
+------------+-----------------------+---------------------------------------+------------------+
| Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress | yelling, dancing |
+------------+-----------------------+---------------------------------------+------------------+
1 row in set (0.001 sec)

# 변경 후 데이터
MariaDB [gregs_list]> SELECT * FROM clown_info WHERE name = 'Clarabelle';
+------------+-----------------------+---------------------------------------+------------+
| name       | last_seen             | appearance                            | activities |
+------------+-----------------------+---------------------------------------+------------+
| Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress | dancing    |
+------------+-----------------------+---------------------------------------+------------+
1 row in set (0.001 sec)
```

**NOTE**
안전하게 삭제하는 방법  
만약 WHERE 명령어를 잘못 준 상태로 DELETE를 사용한다면 의도치 않게 다른 데이터들이 삭제될 수 있다. 따라서 SELECT을 사용하여 사용하려는 WHERE 명령어가 원하는 데이터만 가져오는지 확인한 후에 DELETE을 사용하는 것이 안전하다. 따라서 순서는 다음과 같다.

 1. SELECT을 사용하여 변경하려는 데이터 확인
 2. INSERT를 사용하여 변경된 데이터 추가
 3. DELETE을 사용하여 이전 데이터 삭제

## UPDATE

레코드를 삭제하지 않고 기존 레코드를 변경할 수는 없을까? UPDATE 명령어는 기존 레코드의 데이터 일부를 변경해준다.

```sql
UPDATE clown_info
SET activities = 'yelling, dancing'
WHERE name = 'Clarabelle';
```

위 코드를 입력하면 광대 'Clarabelle'의 활동을 'yelling, dancing'로 수정한다. 아래의 코드를 입력하여 확입해보자.

```sql
MariaDB [gregs_list]> SELECT * FROM clown_info WHERE name = 'Clarabelle';
```

그러면  `Clarabelle`에 대한 정보가 변경된 것을 확인할 수 있다.

```sql
+------------+-----------------------+---------------------------------------+------------------+
| name       | last_seen             | appearance                            | activities       |
+------------+-----------------------+---------------------------------------+------------------+
| Clarabelle | Belmont Senior Certer | F, pink hair, huge flower, blue dress | yelling, dancing |
+------------+-----------------------+---------------------------------------+------------------+
1 row in set (0.001 sec)
```

**WARNING**
주의해야 할 점은 WHERE로 특정 레코드를 지정해주지 않으면 모든 레코드의 데이터가 변경된다는 것이다.
