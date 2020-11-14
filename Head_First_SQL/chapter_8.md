
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

해당 스키마로 변경을 위해 `info_activities` 테이블을 생성하고 `activities`에서 참조키를 제거하자. 코드는 다음과 같다.

```sql
--activities 참조키 삭제--
ALTER TABLE activities
    DROP CONSTRAINT clown_info_id_fk;

--activities id 삭제--
ALTER TABLE activities
    DROP COLUMN id;

--info_activities 생성--
CREATE TABLE info_activities(
    id INT NOT NULL,
    act_id INT NOT NULL,
    FOREIGN KEY (id) REFERENCES clown_info(id),
    FOREIGN KEY (act_id) REFERENCES activities(act_id));
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
- 분리된 데이터를 새로운 열에 중복 없이 추가하기 (appearance, activities)
- 각각의 기본키를 연결 테이블에 추가하기  (info_appearance, info_activities)

## 기존 테이블에서 데이터 분리하기

