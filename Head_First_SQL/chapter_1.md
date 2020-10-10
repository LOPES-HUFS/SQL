# 1장 데이터와 테이블

데이터를 테이블 형태의 열과 행으로 나타낼 수 있다.

데이터 베이스는 테이블들의 저장소로 테이블과 관련된 SQL관련 구성 요소를 담고 있는 저장소이다.

쿼리: 데이터 베이스에 정보를 요청하는 것

열: 테이블의 구성요소 중 하나로 데이터의 카테고리나 속성 정보가 열이 될 수 있다. 예를 들면 결혼 여부가 열이되고 그 값에는 기혼이나 미혼이 들어간다. 필드라고 부르기도 함

행: 테이블의 구성요소 중 하나로 데이터가 나타내는 한 객체에 대한 속성을 나타내는 열 집합이다. 예를 들면 사람에 대한 테이블이라면, 그 사람의 이름, 직업, 이메일 등의 값을 알 수 있다. 레코드라고 부르기도 함

데이터 베이스 안의 테이블들은 서로 연결되어야 한다.

```sql
CREATE DATABASE gregs_list;
USE gregs_list;
```

## 테이블을 만드는 방법 CREATE TABLE

VARCHAR 변하는 문자형으로 텍스트를 저장하는데 이용됨 () 안 숫자는 그 숫자 자리까지 저장할 수 있다는 것을 의미한다. 단 한글의 경우 2byte이기에 1byte 괄호 안 숫자의 그 절반까지만 입력가능하다. 총 255자리 까지 저장가능
CHAR or CHARACTER 데이터가 정해진 길이인 문자열 저장 형식
DATETIME or TIMESTAMP 날짜와 시간을 저장하는 형식
TIME 시간을 저장하는 형식
DATE 날짜만 저장하는 형식
BLOB 큰 덩어리의 문자 데이터
DEC or DECIMAL 십진 자리수를 저장하는 형식
INT or INTEGER 정수를 저장하는 형식

```sql
CREATE TABLE doughnut_list(
    doughnut_name VARCHAR(10),
    doughnut_type VARCHAR(6)
);
```

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

DESC는 생성된 테이블의 정보를 보는 명령어이다.

```sql
DESC my_contacts;
```

DROP TABLE은 생성된 테이블을 지우는 명령어이다.

```sql
DROP TABLE my_contacts;
```

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

## INSERT 문 만들기

INSERT INTO문은 데이터를 테이블에 넣는 명령어로 값들은 열이름과 같은 순서로 입력되어야 한다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

## p.77 연필을 깎으며,

로케이션 열은 있지만 값이 없다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer',  'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

열 리스트에 email이 없지만 값은 존재한다.

```sql
INSERT INTO my_contacts(
    last_name, first_name, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

'Technical Writer' 'Palo Alto, CA' 사이에 , 빠짐

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer' 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);
```

'Relationship, Friends 따옴표가 빠짐

```sql
INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends
);

```

## INSERT 문 변형

INSERT INTO 문에서 열이름은 생략할 수 있다. 단 값 순서는 열과 맞아야함, 몇개의 열만 생략하는 것도 가능하다.

```sql
INSERT INTO my_contacts(
    first_name, email,  profession, location
)
VALUES (
    'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton, NJ'
);
```

SELECT문으로 테이블 안 데이터를 불러올 수 있다.

```sql
SELECT * FROM my_contacts;
```

## NULL 값 제어하기

```sql
DROP TABLE my_contacts;
```

```sql
CREATE TABLE my_contacts(
    last_name VARCHAR(30) NOT NULL,
    first_name VARCHAR(20) NOT NULL
);
```

```sql
DROP TABLE my_contacts;
```

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

```sql
CREATE TABLE doughnut_list(
    doughnut_name VARCHAR(10) NOT NULL,
    doughnut_type VARCHAR(6) NOT NULL,
    doughnut_cost DEC(3,2) NOT NULL DEFAULT 1.00
);
```