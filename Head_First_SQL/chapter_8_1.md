# 7장 코드 정리

## 내부 조인에 관한 모든 것

```sql
USE gregs_list;
```

```sql
CREATE TABLE toys(
    toy_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    toy VARCHAR(30) default NULL
    );
```

```sql
INSERT INTO toys(toy_id, toy) VALUES (NULL,'hula hoop');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'balsa glider');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'toy soldiers');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'harmonica');
INSERT INTO toys(toy_id, toy) VALUES (NULL,'baseball cards');
```

```sql
CREATE TABLE boys(
    boy_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    boy VARCHAR(30) default NULL
    );
```

```sql
INSERT INTO boys(boy_id, boy) VALUES (NULL,'Davey');
INSERT INTO boys(boy_id, boy) VALUES (NULL,'Bobby');
INSERT INTO boys(boy_id, boy) VALUES (NULL,'Beaver');
INSERT INTO boys(boy_id, boy) VALUES (NULL,'Richie');
```

## 크로스 조인

어떤 테이블의 모든 행과 다른 데이블의 모든 행을 짝지워 반환한다.

```sql
SELECT t.toy, b.boy
    FROM toys AS t
    CROSS JOIN boys as b;
```

실행 결과

```sql
MariaDB [gregs_list]> SELECT t.toy, b.boy
    ->     FROM toys AS t
    ->     CROSS JOIN boys as b;
+----------------+--------+
| toy            | boy    |
+----------------+--------+
| hula hoop      | Davey  |
| hula hoop      | Bobby  |
| hula hoop      | Beaver |
| hula hoop      | Richie |
| balsa glider   | Davey  |
| balsa glider   | Bobby  |
| balsa glider   | Beaver |
| balsa glider   | Richie |
| toy soldiers   | Davey  |
| toy soldiers   | Bobby  |
| toy soldiers   | Beaver |
| toy soldiers   | Richie |
| harmonica      | Davey  |
| harmonica      | Bobby  |
| harmonica      | Beaver |
| harmonica      | Richie |
| baseball cards | Davey  |
| baseball cards | Bobby  |
| baseball cards | Beaver |
| baseball cards | Richie |
+----------------+--------+
20 rows in set (0.001 sec)
```

`boys` 테이블을 지우고 새로 만든다.

```sql
DROP table boys;
```

```sql
CREATE TABLE boys(
    boy_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    boy VARCHAR(30) default NULL,
    toy_id INT NOT NULL
    );
```

```sql
INSERT INTO boys(boy_id, boy, toy_id) VALUES (NULL,'Davey', 3);
INSERT INTO boys(boy_id, boy, toy_id) VALUES (NULL,'Bobby', 5);
INSERT INTO boys(boy_id, boy, toy_id) VALUES (NULL,'Beaver', 2);
INSERT INTO boys(boy_id, boy, toy_id) VALUES (NULL,'Richie', 1);
```

## 내부 조인

조건을 사용하여 두 테이블의 레코드를 결합한다. 결합된 행들이 조건을 만족하는 경우에만 열들을 반환한다.

### 동등 조인

같은 경우 반환

```sql
SELECT boys.boy, toys.toy
    FROM boys
        INNER JOIN
        toys
    ON boys.toy_id = toys.toy_id;
```

아이디 이름과 그 아이가 가지고 있는 장난감 이름

```sql
MariaDB [gregs_list]> SELECT boys.boy, toys.toy
    ->     FROM boys
    ->         INNER JOIN
    ->         toys
    ->     ON boys.toy_id = toys.toy_id;
+--------+----------------+
| boy    | toy            |
+--------+----------------+
| Davey  | toy soldiers   |
| Bobby  | baseball cards |
| Beaver | balsa glider   |
| Richie | hula hoop      |
+--------+----------------+
4 rows in set (0.001 sec)
```

### 비동등 조인

```sql
SELECT boys.boy, toys.toy
    FROM boys
        INNER JOIN
        toys
    ON boys.toy_id <> toys.toy_id
    ORDER BY boys.boy;
```

아래 결과를 보면 어떤 아이 이름과 아이가 갖고 있지 않은 장난감

```sql
MariaDB [gregs_list]> SELECT boys.boy, toys.toy
    ->     FROM boys
    ->         INNER JOIN
    ->         toys
    ->     ON boys.toy_id <> toys.toy_id
    ->     ORDER BY boys.boy;
+--------+----------------+
| boy    | toy            |
+--------+----------------+
| Beaver | baseball cards |
| Beaver | harmonica      |
| Beaver | toy soldiers   |
| Beaver | hula hoop      |
| Bobby  | harmonica      |
| Bobby  | toy soldiers   |
| Bobby  | balsa glider   |
| Bobby  | hula hoop      |
| Davey  | baseball cards |
| Davey  | harmonica      |
| Davey  | balsa glider   |
| Davey  | hula hoop      |
| Richie | baseball cards |
| Richie | harmonica      |
| Richie | toy soldiers   |
| Richie | balsa glider   |
+--------+----------------+
16 rows in set (0.001 sec)
```

### 자연 조인

```sql
SELECT boys.boy, toys.toy FROM boys NATURAL JOIN toys;
```

아래 결과를 보면 앞에서 했던 동등 조인과 같은 결과를 줍니다. 자연 조인은 두 테이블에 같은 이름의 열이 있을 때, 즉 일치하는 열리 있을 때 작동합니다.

```sql
MariaDB [gregs_list]> SELECT boys.boy, toys.toy FROM boys NATURAL JOIN toys;
+--------+----------------+
| boy    | toy            |
+--------+----------------+
| Davey  | toy soldiers   |
| Bobby  | baseball cards |
| Beaver | balsa glider   |
| Richie | hula hoop      |
+--------+----------------+
4 rows in set (0.001 sec)
```
