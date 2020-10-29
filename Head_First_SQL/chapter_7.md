
# 여러 개의 테이블 다루기

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
[clown_info]     [info_activities]
- id(key) ㅡㅡㅡ  - id (foreign key)
- name   ㅣ      - activity_id (foreign key) ㅡㅡㅡ [activities]
-gender  ㅣ                                        - activity_id (key)
         ㅣ                                        - activities
         ㅣ      [info_apperance]
         ㄴㅡㅡ   - id (foreign key)
                - apperance_id (foreign key) ㅡㅡㅡ [appearance]
                                                  - appearance_id (key)
                                                  - appearance
```

위의 스키마는 `clown_info`에서 여러 값을 가진 카테고리인 appearance, activities를 다른 테이블로 생성한 뒤 연결한 모습이다. 지금부터는 어떻게 하나의 테이블을 위 스키마의 형태처럼 변경하는지 하나씩 살펴볼 것이다.

## 참조키 (foreign key)

각각의 테이블들을 살펴보기에 앞서 위 스키마에서 'foreign key'라고 쓰여있는 카테고리를 찾을 수 있다. 이는 참조키를 표시한 것인데, 참조키란 무엇이며 왜 사용하는걸까?
