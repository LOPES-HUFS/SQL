# 1장 데이터와 테이블

데이터는 테이블 형태의 열과 행으로 나타낼 수 있다. 데이터 베이스는 테이블들의 저장소로 테이블과 관련된 SQL관련 구성 요소를 담고 있는 저장소이다. 쿼리(query)는 데이터 베이스에 정보를 요청하는 것이다. 열은 테이블의 구성요소 중 하나로 데이터의 카테고리나 속성 정보가 열이 될 수 있다. 예를 들면 결혼 여부가 열이되고 그 값에는 기혼이나 미혼이 들어간다. 필드라고 부르기도 한다. 행은 테이블의 구성요소 중 하나로 데이터가 나타내는 한 객체에 대한 속성을 나타내는 열 집합이다. 예를 들면 사람에 대한 테이블이라면, 그 사람의 이름, 직업, 이메일 등의 값을 알 수 있다. 레코드라고 부르기도 한다. 데이터 베이스 안의 테이블들은 서로 연결되어야 한다.

- `CREATE DATABASE`는 사용할 데이터베이스를 만드는 명령어
- `USE`는 만든 데이터베이스를 사용하는 명령어

```sql
CREATE DATABASE gregs_list;
USE gregs_list;
```

결과는 다음과 같다.

```bash
MariaDB [(none)]> CREATE DATABASE gregs_list;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> USE gregs_list;
Database changed
```

## 테이블을 만드는 방법 CREATE TABLE

`CREATE TABLE` 명령어는 데이터 베이스 안에 테이블을 만드는 명령어이다. 테이블을 만들어야 데이터 베이스 안에 데이터를 저장할 수 있다. 테이블을 구성하는 데이터 타입들에 대해 알아보자.

`VARCHAR`은 변하는 문자형으로 텍스트를 저장하는데 이용됨 () 안 숫자는 그 숫자 자리까지 저장할 수 있다는 것을 의미한다. 단 한글의 경우 2byte이기에 1byte 괄호 안 숫자의 그 절반까지만 입력가능하다. 총 255자리 까지 저장 가능하다. `CHAR` or `CHARACTER` 데이터가 정해진 길이인 문자열 저장 형식이다. `DATETIME` or `TIMESTAMP` 날짜와 시간을 저장하는 형식이다. `TIME` 시간을 저장하는 형식이다. `DATE` 날짜만 저장하는 형식이다. `BLOB` 큰 덩어리의 문자 데이터를 저장하는 형식이다. `DEC` or `DECIMAL` 십진 자리수를 저장하는 형식이다. `INT` or `INTEGER` 정수를 저장하는 형식이다.

- `CREATE TABLE`는 테이블을 만드는 명령어

```sql
CREATE TABLE doughnut_list(
    doughnut_name VARCHAR(10),
    doughnut_type VARCHAR(6)
);
```

결과는 다음과 같다.

```bash
MariaDB [gregs_list]> CREATE TABLE doughnut_list(
    ->     doughnut_name VARCHAR(10),
    ->     doughnut_type VARCHAR(6)
    -> );
Query OK, 0 rows affected (0.010 sec)
```

my_contacts 라는 이름의 테이블을 만들면 다음과 같다.

```sql
CREATE TABLE my_contacts(
    last_name VARCHAR(30),
    first_name VARCHAR(20),
    email VARCHAR(50),
    birthday Date,
    profession VARCHAR(50),
    location VARCHAR(50),
    status VARCHAR(20),
    interests VARCHAR(100),
    seeking VARCHAR(100)
);
```

```bash
MariaDB [gregs_list]> CREATE TABLE my_contacts(
    ->     last_name VARCHAR(30),
    ->     first_name VARCHAR(20),
    ->     email VARCHAR(50),
    ->     birthday Date,
    ->     profession VARCHAR(50),
    ->     location VARCHAR(50),
    ->     status VARCHAR(20),
    ->     interests VARCHAR(100),
    ->     seeking VARCHAR(100)
    -> );
Query OK, 0 rows affected (0.009 sec)
```

- `DESC`는 생성된 테이블의 정보를 보는 명령어

```sql
DESC my_contacts;
```

DESC 결과는 다음과 같이 출력된다. 각 열에 대한 정보가 출력되는데 열 이름, 열의 데이터 타입, NULL 값 여부 등이 표시된다.

```bash
MariaDB [gregs_list]> DESC my_contacts;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| last_name  | varchar(30)  | YES  |     | NULL    |       |
| first_name | varchar(20)  | YES  |     | NULL    |       |
| email      | varchar(50)  | YES  |     | NULL    |       |
| birthday   | date         | YES  |     | NULL    |       |
| profession | varchar(50)  | YES  |     | NULL    |       |
| location   | varchar(50)  | YES  |     | NULL    |       |
| status     | varchar(20)  | YES  |     | NULL    |       |
| interests  | varchar(100) | YES  |     | NULL    |       |
| seeking    | varchar(100) | YES  |     | NULL    |       |
+------------+--------------+------+-----+---------+-------+
9 rows in set (0.002 sec)
```

새로운 열을 추가하려면 기존의 테이블을 지우고 다시 만들어야 한다.

- `DROP TABLE`은 생성된 테이블을 지우는 명령어

```sql
DROP TABLE my_contacts;
```

테이블을 삭제할 때는 안에 데이터가 있든 없든 테이블 전체가 삭제되므로 주의해야 한다.

```bash
MariaDB [gregs_list]> DROP TABLE my_contacts;
Query OK, 0 rows affected (0.007 sec)
```

기존의 테이블을 지웠으면, 이제 gender 열을 추가한 새로운 테이블을 만들어 준다.

```sql
CREATE TABLE my_contacts(
    last_name VARCHAR(30),
    first_name VARCHAR(20),
    email VARCHAR(50),
    gender CHAR(1),
    birthday Date,
    profession VARCHAR(50),
    location VARCHAR(50),
    status VARCHAR(20),
    interests VARCHAR(100),
    seeking VARCHAR(100)
);
```

코드의 출력 결과는 다음과 같다.

```bash
MariaDB [gregs_list]> CREATE TABLE my_contacts(
    ->     last_name VARCHAR(30),
    ->     first_name VARCHAR(20),
    ->     email VARCHAR(50),
    ->     gender CHAR(1),
    ->     birthday Date,
    ->     profession VARCHAR(50),
    ->     location VARCHAR(50),
    ->     status VARCHAR(20),
    ->     interests VARCHAR(100),
    ->     seeking VARCHAR(100)
    -> );

Query OK, 0 rows affected (0.011 sec)
```

## INSERT 문 만들기

`INSERT INTO`문은 데이터를 테이블에 넣는 명령어이다. 이때 입력되는 값들은 열이름과 같은 순서로 입력되어야 한다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
    -> )
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
    -> );
Query OK, 1 row affected (0.005 sec)
```

참고로 내가 넣은 자료를 확인하려면 `SELECT`을 사용하면 된다. 자세한 내용을 2장에서 나온다.

```sql
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| last_name | first_name | email                            | gender | birthday   | profession       | location      | status | interests          | seeking               |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
1 row in set (0.000 sec)
```

## 연필을 깎으며

문제가 있는 INSERT 문을 쳐보고 에러를 확인해보자.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer',  'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

위의 명령어로 자료를 추가하고자 할때, 에러가 나는 이유는 값을 입력할 때 location 열에 해당하는 값을 같이 입력하지 않았기 때문이다. 따라서 열과 값이 맞지 않는다는 다음과 같은 에러 메세지를 볼 수 있다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
    -> )
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer',  'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
    -> );
ERROR 1136 (21S01): Column count doesn't match value count at row 1
```

에러를 고칠려면 Technical Writer', 와 'Single', 사이에 location 열에 해당하는 값, 'Palo Alto, CA'를 다음과 같이 추가해 주면 된다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

이번에는 email 열이 없지만 email 열에 해당하는 값이 있는 경우이다. 마찬가지로 열과 값이 서로 맞지 않는다는 에러 메세지가 출력된다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     last_name, first_name, gender, birthday, profession, location, status, interests, seeking
    -> )
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
    -> );
ERROR 1136 (21S01): Column count doesn't match value count at row 1
```

에러를 고칠려면 first_name, 과 gender 사이에 email, 을 추가해주면 된다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer' 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

'Technical Writer' 'Palo Alto, CA' 사이에 , 빠졌다. 즉 값이 열 개수에 비해 값의 개수가 하나 부족하다. 따라서 열과 값의 개수가 맞지 않는다는 에러 메세지가 출력된다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
    -> )
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer' 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
    -> );
ERROR 1136 (21S01): Column count doesn't match value count at row 1
```

에러를 고치려면 'Technical Writer' 'Palo Alto, CA' 사이에 ,를 추가해주면 된다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends
);

```

'Relationship, Friends 따옴표가 빠졌다. 에러 메세지는 안나오지만 코드가 ;가 있어도 끝나지 않는다. 이 경우 `';`을 입력하면 문법에 맞지 않는다는 에러 메세지와 함께 빠져 나올 수 있다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
    -> )
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends
    '> );
    '> ';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '' at line 5
```

에러를 고치려면 'Relationship, Friends 다음에 ;을 쓰기 전에 '를 붙여준다.

## INSERT 문 변형

INSERT INTO 문에서 열 입력 순서를 바꾸거나, 열이름은 생략할 수 있지만 단 값 입력 순서는 열과 맞아야한다. 몇개의 열만 생략하고 입력하는 것도 가능하다.

열 순서 바꿔 입력하기

```sql
INSERT INTO my_contacts(
    interests, first_name, last_name, gender, email, birthday, profession, location, status, seeking
)
VALUES (
    'Kayaking, Reptiles', 'Jillian', 'Anderson', 'F', 'jill_anderson@breakneckpizza.com', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Relationship, Friends'
);
```

열 순서가 interests 부터 시작하고, first_name이 다음 순서로 온다. 값도 마찬가지로 interests에 해당하는 'Kayaking, Reptiles' 가 먼저 입력이 된다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     interests, first_name, last_name, gender, email, birthday, profession, location, status, seeking
    -> )
    -> VALUES (
    ->     'Kayaking, Reptiles', 'Jillian', 'Anderson', 'F', 'jill_anderson@breakneckpizza.com', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Relationship, Friends'
    -> );
Query OK, 1 row affected (0.002 sec)
```

열 이름 생략하고 입력하기

```sql
INSERT INTO my_contacts

VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
   );
```

입력 시 열 이름을 생략하고 입력할 수 있다. 이때 값 입력 순서와 열 이름 순서는 같아야 한다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts
    -> VALUES (
    ->     'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
    ->    );
Query OK, 1 row affected (0.003 sec)
```

몇 개의 열 생략하고 입력하기

```sql
INSERT INTO my_contacts(
    first_name, email,  profession, location
)
VALUES (
    'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton, NJ'
);
```

first_name, email,  profession, location 이 4개의 열만 입력하고 값을 입력할 수 있다. 마찬가지로 입력한 열 이름과 맞는 순서로 값을 입력해야한다.

```bash
MariaDB [gregs_list]> INSERT INTO my_contacts(
    ->     first_name, email,  profession, location
    -> )
    -> VALUES (
    ->     'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton, NJ'
    -> );
Query OK, 1 row affected (0.003 sec)
```

참고로 내가 넣은 자료를 확인하려면 `SELECT`을 사용하면 된다. 자세한 내용을 2장에서 나온다.

```sql
SELECT * FROM my_contacts;
```

테이블의 입력된 내용을 확인할 수 있다.

```bash
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| last_name | first_name | email                            | gender | birthday   | profession       | location      | status | interests          | seeking               |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
| NULL      | Pat        | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton, NJ | NULL   | NULL               | NULL                  |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
4 rows in set (0.001 sec)
```

## NULL 값 다루기

앞서 일부 열에만 값을 입력했는데 이럴 경우 값이 입력이 되지 않은 열의 값은 NULL 값이 된다. 따라서 이 값들을 제어 해야한다. 먼저 기존의 테이블을 삭제해주고 새로 테이블을 만들어보자.

```sql
DROP TABLE my_contacts;
```

```bash
MariaDB [gregs_list]> DROP TABLE my_contacts;
Query OK, 0 rows affected (0.006 sec)
```

테이블을 만들 때 데이터 타입 설정 다음에 NOT NULL을 추가해주면 NULL 값을 제어할 수 있다. 단 이럴 경우 이 열에는 반드시 값을 넣어주어야 한다. 만약 NOT NULL을 추가하지 않는다면, 필수 입력 값이 아니라는 의미로 값에 NULL이 들어갈 수 있다. NOT NULL을 적어주지 않을 경우 값에 NULL 값이 추가되어도 되며, default 값이 설정되어 있다면, 그 값을 비워두어도 NULL 값이 되지 않고 defualt 값으로 설정된다.

```sql
CREATE TABLE my_contacts(
    last_name VARCHAR(30) NOT NULL,
    first_name VARCHAR(20) NOT NULL
);
```

이제 다시 임시 테이블을 제거하고 NULL 값을 제어하는 my_contacts 테이블 만들어보면 다음과 같다.

```sql
DROP TABLE my_contacts;
```

- `DROP TABLE`는 만든 테이블을 지우는 명령어, 명령어 뒤에 지울 테이블 이름을 입력하면 된다.

```sql
CREATE TABLE my_contacts(
    last_name VARCHAR(30) NOT NULL,
    first_name VARCHAR(20) NOT NULL,
    email VARCHAR(50) NOT NULL,
    gender CHAR(1) NOT NULL,
    birthday Date NOT NULL,
    profession VARCHAR(50) NOT NULL,
    location VARCHAR(50) NOT NULL,
    status VARCHAR(20) NOT NULL,
    interests VARCHAR(100) NOT NULL,
    seeking VARCHAR(100) NOT NULL
);
```

실행하면 다음과 같다.

```bash
MariaDB [gregs_list]> CREATE TABLE my_contacts(
    ->     last_name VARCHAR(30) NOT NULL,
    ->     first_name VARCHAR(20) NOT NULL,
    ->     email VARCHAR(50) NOT NULL,
    ->     gender CHAR(1) NOT NULL,
    ->     birthday Date NOT NULL,
    ->     profession VARCHAR(50) NOT NULL,
    ->     location VARCHAR(50) NOT NULL,
    ->     status VARCHAR(20) NOT NULL,
    ->     interests VARCHAR(100) NOT NULL,
    ->     seeking VARCHAR(100) NOT NULL
    -> );
Query OK, 0 rows affected (0.010 sec)
```

이제 NULL 값을 제어한 도넛 리스트도 다시 만들어 보자. doughnut_cost 열에 디폴트 값으로 1.00이 추가되어 있다. 이것은 값이 입력되지 않는다면 1.00을 사용한다는 의미이다.

```sql
DROP TABLE doughnut_list;
```

```sql
CREATE TABLE doughnut_list(
    doughnut_name VARCHAR(10) NOT NULL,
    doughnut_type VARCHAR(6) NOT NULL,
    doughnut_cost DEC(3,2) NOT NULL DEFAULT 1.00
);
```
