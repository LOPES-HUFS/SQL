# 파이썬으로 Docker로 구성한 MariaDB 사용하기

도커를 이용하여 MariaDB(이하 마리아디비)를 설치해봤습니다. 이렇게 설치한 마리아디비에 파이썬으로 이용하여 자료를 저장하고 저장한 값을 확인하고 지워보겠습니다. 우선 파이썬으로 마리아디비와 같은 실제 DB(이하 데이터베이스)에 접근하기 위해서는 해당 데이터베이스에 맞는 드라이버(또는 클라이언트 라이브러리(client library)라고 하기도 합니다)를 설치해줘야 합니다. 이 글에서는 [PyMySQL](https://pymysql.readthedocs.io/en/latest/)을 사용하겠습니다. 설치 방법은 [Installation](https://pymysql.readthedocs.io/en/latest/user/installation.html)을 참고하세요. 현재 사용하고 있는 PyMySQL의 버전은 0.10.1입니다.

## 본격적으로 시작하기에 앞서

참고로 여기에서는 이미 Docker로 MariaDB를 설치했다고 가정할 것입니다. 설치하는 방법은 다음 링크를 참고하세요. [Docker를 사용해서 MariaDB을 설치하기](http://www.epistemology.pe.kr/2020/09/26/1293) 그리고 이미 다음과 같은 `sql`명령을 사용하여 `gregs_list`이라는 DATABASE를 만들고, 그 안에 `my_contacts`라는 테이블을 만들고 두 사람의 자료를 입력했습니다.그리고 사용하기 쉽게 하기 위해서 `sqlalchemy`을 사용합니다. 이것의 설치 방법은 [How to Install SQLAlchemy on Windows, Mac and Linux | Python Central](https://www.pythoncentral.io/how-to-install-sqlalchemy/)을 참고하세요!

```sql
CREATE DATABASE gregs_list;

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

INSERT INTO my_contacts(
    last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking
)
VALUES (
    'Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'
);

INSERT INTO my_contacts(
    first_name, email,  profession, location
)
VALUES (
    'Pat', 'patpost@breakneckpizza.com', 'Postal Worker', 'Princeton, NJ'
);
```

위에서 작업한 결과로 gregs_list 데이터베이스의 my_contacts라는 테이블에는 다음과 같이 두 사람의 자료가 들어있습니다.

```sql
MariaDB [gregs_list]> SELECT * FROM my_contacts;
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| last_name | first_name | email                            | gender | birthday   | profession       | location      | status | interests          | seeking               |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
| Anderson  | Jillian    | jill_anderson@breakneckpizza.com | F      | 1980-09-05 | Technical Writer | Palo Alto, CA | Single | Kayaking, Reptiles | Relationship, Friends |
| NULL      | Pat        | patpost@breakneckpizza.com       | NULL   | NULL       | Postal Worker    | Princeton, NJ | NULL   | NULL               | NULL                  |
+-----------+------------+----------------------------------+--------+------------+------------------+---------------+--------+--------------------+-----------------------+
2 rows in set (0.004 sec)
```

## 데이터베이스에 입력된 것을 가져오기

그러면 파이썬으로 이것에 연결해보겠습니다. 도커에 설치된 마리아디비의 정보를 파이썬에 입력하기 위해서 정보가 들어 있는 `dict`를 하나 만들었습니다.

```python
db = {
    'user'     : 'root',
    'password' : 'RT27hDosK',
    'host'     : '0.0.0.0',
    'port'     : '3306',
    'database' : 'gregs_list'
}
```

위에 입력한 자료를 가지고 `DB_URL`이라는 문자열을 만들어서 이를 가지고 데이터 베이스에 접근하기 위해 `sqlalchemy`으로 `create_engine`을 만들고, my_contacts 테이블에 접근하기 위해 `execute`을 가지고 sql을 직접 실행합니다. 그렇게 받아온 자료를 `print`를 이용해 확인합니다.

```python
import sqlalchemy as sa
DB_URL = f"mysql+pymysql://{db['user']}:{db['password']}@{db['host']}:{db['port']}/{db['database']}?charset=utf8"
conn = sa.create_engine(DB_URL, encoding = 'utf-8')

rows = conn.execute('SELECT * FROM my_contacts')

for row in rows:
    print(row)
```

지금까지 한 것을 실행하면 다음과 같습니다. 맥에서 실행한 것이니 조금씩 다를 수는 있겠지만, 거의 다 같게 작동할 것입니다. 만약 자료를 보기 위해서 `print(rows)`라고 하신다면 `<sqlalchemy.engine.result.ResultProxy object at 0x102e33940>`이라고 나올 것입니다. 왜냐하면 sqlalchemy의 ResultProxy 객체는 출력해서 볼 수 없기 때문입니다.

```python
❯ python3
Python 3.8.6 (default, Oct 16 2020, 14:50:59)
[Clang 11.0.3 (clang-1103.0.32.62)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import sqlalchemy as sa
>>> db = {
...     'user'     : 'root',
...     'password' : 'RT27hDosK',
...     'host'     : '0.0.0.0',
...     'port'     : '3306',
...     'database' : 'gregs_list'
... }
>>> DB_URL = f"mysql+pymysql://{db['user']}:{db['password']}@{db['host']}:{db['port']}/{db['database']}?charset=utf8"
>>> conn = sa.create_engine(DB_URL, encoding = 'utf-8')
>>> rows = conn.execute('SELECT * FROM my_contacts')
>>> for row in rows:
...     print(row)
...
('Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', datetime.date(1980, 9, 5), 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends')
(None, 'Pat', 'patpost@breakneckpizza.com', None, None, 'Postal Worker', 'Princeton, NJ', None, None, None)
```

위의 방법을 보시면 아시겠지만, 기본적으로 `SQL`을 이용하는 것과 별 차이가 없습니다. 또한 PostgreSQL와 같은 다른 종류의 데이터베이스를 사용하면, 사용할 수 있는 `SQL`가 다르기 때문에 각각 DB에 맞춰 코드를 짜야 합니다. 이처럼 같은 기능을 하지만, 각각의 데이터베이스에서는 상이한 표현으로 되어 있는 것들을 같은 함수로 처리할 수 있는 기능이 SQLAlchemy에 들어 있습니다. 이 기능이 바로 'SQL Expression Language'입니다. 이것을 이용하여 위의 것과 동일한 일을 하는 코드를 작성하면 아래와 같습니다.

```python
db = {
    'user'     : 'root',
    'password' : 'RT27hDosK',
    'host'     : '0.0.0.0',
    'port'     : '3306',
    'database' : 'gregs_list'
}
import sqlalchemy as sa

DB_URL = f"mysql+pymysql://{db['user']}:{db['password']}@{db['host']}:{db['port']}/{db['database']}?charset=utf8"

conn = sa.create_engine(DB_URL, encoding = 'utf-8')

meta = sa.MetaData()

my_contacts = sa.Table('my_contacts', meta,
    sa.Column('last_name', sa.String),
    sa.Column('first_name', sa.String),
    sa.Column('email', sa.String),
    sa.Column('birthday', sa.Date),
    sa.Column('profession', sa.String),
    sa.Column('location', sa.String),
    sa.Column('status', sa.String),
    sa.Column('interests', sa.String),
    sa.Column('seeking', sa.String))

meta.create_all(conn)

result = conn.execute(my_contacts.select())
rows = result.fetchall()
print(rows)
```

이 코드를 보시면 아시겠지만, `SQL`에서 벗어나, 순수 파이썬 코드처럼 보입니다. 윗 코드를 실행시키면 앞에 한 것과 동일한 결과가 나옵니다. 앞부분은 윗 코드와 동일하지만 데이블에 접근하는 방법은 많이 다릅니다.

```python
Python 3.8.6 (default, Oct 16 2020, 14:50:59)
[Clang 11.0.3 (clang-1103.0.32.62)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import sqlalchemy as sa
>>> db = {
...     'user'     : 'root',
...     'password' : 'RT27hDosK',
...     'host'     : '0.0.0.0',
...     'port'     : '3306',
...     'database' : 'gregs_list'
... }
>>> DB_URL = f"mysql+pymysql://{db['user']}:{db['password']}@{db['host']}:{db['port']}/{db['database']}?charset=utf8"
>>> conn = sa.create_engine(DB_URL, encoding = 'utf-8')
>>> meta = sa.MetaData()
>>> my_contacts = sa.Table('my_contacts', meta,
...     sa.Column('last_name', sa.String),
...     sa.Column('first_name', sa.String),
...     sa.Column('email', sa.String),
...     sa.Column('birthday', sa.Date),
...     sa.Column('profession', sa.String),
...     sa.Column('location', sa.String),
...     sa.Column('status', sa.String),
...     sa.Column('interests', sa.String),
...     sa.Column('seeking', sa.String))
>>> meta.create_all(conn)
>>> result = conn.execute(my_contacts.select())
>>> rows = result.fetchall()
>>> print(rows)
[('Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', datetime.date(1980, 9, 5), 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'), (None, 'Pat', 'patpost@breakneckpizza.com', None, 'Postal Worker', 'Princeton, NJ', None, None, None)]
```

이것을 이용해서 자료를 입력해보겠습니다. 참고로 생년월일을 입력하기 위해서 `datetime`가 필요합니다.

```python
import datetime

conn.execute(my_contacts.insert(('Soukup', 'Alan', 'soupup@breakneckbizza.com', datetime.date(1975, 12, 2), 'Aeronautical Engineer', 'San Antonio, TX', 'Married', 'RPG, Programming', 'Nothing')))
```

윗 코드를 작동시켜보면 자료가 입력된 것을 확인할 수 있습니다.

```python
>>> import datetime
>>> conn.execute(my_contacts.insert(('Soukup', 'Alan', 'soupup@breakneckbizza.com', datetime.date(1975, 12, 2), 'Aeronautical Engineer', 'San Antonio, TX', 'Married', 'RPG, Programming', 'Nothing')))
<sqlalchemy.engine.result.ResultProxy object at 0x10ad859d0>
>>> result = conn.execute(my_contacts.select())
>>> rows = result.fetchall()
>>> print(rows)
[('Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', datetime.date(1980, 9, 5), 'Technical Writer', 'Palo Alto, CA', 'Single', 'Kayaking, Reptiles', 'Relationship, Friends'), (None, 'Pat', 'patpost@breakneckpizza.com', None, 'Postal Worker', 'Princeton, NJ', None, None, None), ('Soukup', 'Alan', 'soupup@breakneckbizza.com', datetime.date(1975, 12, 2), 'Aeronautical Engineer', 'San Antonio, TX', 'Married', 'RPG, Programming', 'Nothing')]
```

## SQLAlchemy, the Object-Relational Mapper (ORM) 작성 중

참고: 현재 만든 데이플에 primary_key가 없어 다시 테이블을 만들어야 합니다.

```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

class my_contacts(Base):
    __tablename__ = 'my_contacts'
    last_name = sa.Column('last_name', sa.String)
    first_name = sa.Column('first_name', sa.String)
    email = sa.Column('email', sa.String)
    birthday = sa.Column('birthday', sa.Date)
    profession = sa.Column('profession', sa.String)
    location = sa.Column('location', sa.String)
    status = sa.Column('status', sa.String)
    interests = sa.Column('interests', sa.String)
    seeking = sa.Column('seeking', sa.String)
    def __init__(self, last_name, first_name, email, birthday, profession, location, status, interests, seeking):
        self.last_name = last_name
        self.first_name = first_name
        self.email = email
        self.birthday = birthday
        self.profession = profession
        self.location = location
        self.status = status
        self.interests = interests
        self.seeking = seeking
    def __repr__(self):
        return "<contacts({}, {}, {}, {}, {}, {}, {}, {},{})>".format(self.last_name, self.first_name, self.email, self.birthday, self.profession, self.location, self.status, self.interests, self.seeking)
```
