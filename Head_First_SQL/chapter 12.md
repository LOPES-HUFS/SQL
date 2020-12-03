
# 12장. 보안

지금까지 데이터베이스를 생성하고 수정하는 과정들은 모두 'root' 계정을 사용한 것이다. 만약 혼자가 아니라 여러 명이 데이터베이스 관리를 해야하는 상황에서 다같이 root 계정을 사용한다면 어떻게 될까? 한명의 실수로 데이터가 전부 날라가거나 아니면 다른 테이블에 이상한 데이터를 넣을 수도 있다.

따라서 DB 관리자는 데이터베이스 접근이 필요한 다른 사람들에게 새로운 계정을 만드록 필요한 접근 권한만을 제공해야 한다.

이번 장에서는 우리가 DB 관리자이며, 다른 팀이 `clown` 데이터를 바탕으로 앱개발을 한다고 가정해보자. 우리는 해당 팀원들에게 필요한 데이터를 가져가고 테이븗 변경 및 수정할 수 있는 권한만을 줄 것이다.

## root 계정 비밀번호 변경

먼저 현재 사용하고 있던 root 계정의 비밀번호는 모두 알고 있다. 따라서 비밀번호를 변경하자.

```sql
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('DBadmin');

Query OK, 0 rows affected (0.003 sec)
```

이제 비밀번호가 변경되었는지 재접속을 통해 확인해보자.

```bash
> mysql -u root -p
Enter password:
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
```

기존의 비밀번호로 접속하려고 하면 위와 같은 에러가 발생한다. 따라서 새로 만든 'DBadmin'으로 접속해보자.

```bash
mysql -u root -p
Enter password:

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 218
Server version: 10.5.6-MariaDB-1:10.5.6+maria~focal mariadb.org binary distribution
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

## 새 사용자 축가

이제 앱 개발 팀원인 'elise'의 계정을 새로 만들자. 데이터베이스에서 이런 사용자의 정보 또한 DB내의 테이블에 저장된다.

```sql
CREATE USER elise
IDENTIFIED BY 'password';

Query OK, 0 rows affected (0.035 sec)
```

해당 코드를 입력하면 쿼리가 정상적으로 수행된 것을 확인할 수 있다.
생성한 사용자 계정으로 다시 로그인해보자.

```bash
mysql -u elise -p
Enter password:

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 223
Server version: 10.5.6-MariaDB-1:10.5.6+maria~focal mariadb.org binary distribution
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

정상적으로 작동하는 것을 확인할 수 있다. 하지만 해당 계정은 지금 아무것도 할 수 없다.

```sql
MariaDB [(none)]> USE gregs_list;
ERROR 1044 (42000): Access denied for user 'elise'@'%' to database 'gregs_list'
```

만약 데이터베이스에 접근하려고 하면 위와 같은 에러가 발생한다. 이는 우리가 아직 어떤 권한도 주지 않았기 때문이다.
권한을 주기 위해 다시 root 계정으로 로그인하자.

## GRANT 권한 수여

해당 사용자에게 `clown` 데이터를 사용할 수 있도록 권한을 제공하려고 한다. 그럴 때는 명령어 `GRANT`를 사용하면 된다. 먼저 elise는 `clown` 데이터를 필요한 데이터를 각져와서 앱에 전달하고 또 앱에서 생성을 요청한 데이터를 DB에 만들어야 한다. 따라서 우리는 elise에게 SELECT, INSERT, DELETE의 권한을 수여할 것이다.

해당 테이블이 존재하는 DB로 접속한 후 아래의 코드를 입력하자. 우리의 경우 해당 테이블들을 `gregs_list`라는 데이터베이스에 있다.

```sql
GRANT SELECT, INSERT, DELETE ON clown_info TO elise;
GRANT SELECT, INSERT, DELETE ON activities TO elise;
GRANT SELECT, INSERT, DELETE ON info_apperance TO elise;
GRANT SELECT, INSERT, DELETE ON appearance TO elise;
```

## REVOKE 권한 취소

앱 개발팀의 업무가 변경되어 elise는 더이상 SELECT 할 필요가 없다. 이런 경우는 명령어 `REVOKE`를 사용하여 해당 권한을 취소할 수 있다.

```sql
REVOKE SELECT ON clown_info FROM elise;
REVOKE SELECT ON activities FROM elise;
REVOKE SELECT ON info_apperance FROM elise;
REVOKE SELECT ON appearance FROM elise;
```

## GRANT OPTION 권한 나눔

이번에 elise가 앱개발 DB 관리자로 승진했다. 그렇기 떄문에 elise에게 해당 `clown` 테이블 접근 권한을 나눠줄 수 있는 옵션을 제공하려고 한다. 그럴 때는 명령어 `GRANT OPTION`을 추가 붙여서 권한을 수여하면 된다.

```sql
GRANT ALL ON clown_info TO elise WITH GRANT OPTION;
GRANT ALL ON activities TO elise WITH GRANT OPTION;
GRANT ALL ON info_apperance TO elise WITH GRANT OPTION;
GRANT ALL ON appearance TO elise WITH GRANT OPTION;
```

위 코드에서 `ALL`은 SELECT, INSERT, DELETE, UPDATE 를 포함한다. 이제 elise는 본인의 다른 팀원들에게 권한을 수여할 수 있다.

elise는 광대 위치를 실시간으로 보여주는 역할인 `sleepy`에게 `clown_info` 테이블의 `SELECT` 할 수 있는 권한을 수여하려고 한다.
일단 root 계정에서 아래의 코드를 입력하여 `sleepy` 사용자를 생성하자.

```sql
CREATE USER sleepy
IDENTIFIED BY 'sleep';
```

그 다음 elise 게정으로 접속하여 sleepy에게 위의 권한을 수여하자.

```sql
mysql -u elise -p --계정 접속--

USE gregs_list; --해당 DB 접속--

GRANT SELECT ON clown_info TO sleepy; --sleepy에게 권한 수여--
```

권한이 수여되었다. 이제 sleepy 계정으로 접속해도 `clown_info`를 SELECT 할 수 있다.

## GRANT OPTION 권한 취소

현재 앱 개발팀과의 DB 권한 관계는 다음과 같다.

```bash
나(DB 총괄 관리자) -- elise(앱 DB 관리자) -- sleepy(직원)
```

이 상황에서 elise에게 더이상 `UPDATE`에 대한 `GRANT OPTION`을 취소하려고 한다. 그렇다면 아래와 같은 코드를 사용하면 된다.

### 해당 부분의 코드가 작동하지 않습니다. 나머지는 식당 갔다와서 끝내겠습니당

```sql
REVOKE GRANT OPTION ON UPDATE ON clown_info FROM elise;
REVOKE GRANT OPTION ON UPDATE ON activities FROM elise;
REVOKE GRANT OPTION ON UPDATE ON info_apperance FROM elise;
REVOKE GRANT OPTION ON SELECT ON appearance FROM elise;
```
