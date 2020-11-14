# 챕터7 357 이후

## gregs_list 정규화하기

정규화의 조건에는 제 1 정규형, 제 2 정규형, 제 3 정규형이 있다. 제 1 정규형은 1NF라 부른다. 제 1 정규형을 만족하는 조건은 2가지이다. 첫 째 테이블을 구성하는 열은 원자적 값만 포함해야한다. 같은 데이터가 여러 열에 반복되면 안된다는 규칙을 가진다. 예를 들면 하나의 열에 여러 정보를 담고 있거나, 여러 열에 같은 정보에 대한 값을 가지는 경우 제 1 정규형이 아니다.

```sql
USE gregs_list

CREATE TABLE toy(
    toy_id INT NOT NULL,
    toy VARCHAR(20),
    colors VARCHAR(50),
    PRIMARY KEY(toy_id)
);

INSERT INTO toy
VALUES (5,"whiffleball","white,yellow,blue"),
(6,"frisbee","green,yellow"),
(9,"Kite","red,blue,green"),
(12,"yoyo","white,yellow");

SELECT * FROM toy;

```

```bash
MariaDB [gregs_list]> CREATE TABLE toy(
    ->     toy_id INT NOT NULL,
    ->     toy VARCHAR(20),
    ->     colors VARCHAR(50),
    ->     PRIMARY KEY(toy_id)
    -> );
Query OK, 0 rows affected (0.051 sec)


MariaDB [gregs_list]> INSERT INTO toy
    -> VALUES (5,"whiffleball","white,yellow,blue"),
    -> (6,"frisbee","green,yellow"),
    -> (9,"Kite","red,green,yellow"),
    -> (12,"yoyo","white,yellow");
Query OK, 4 rows affected (0.006 sec)
Records: 4  Duplicates: 0  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM toy;
+--------+-------------+-------------------+
| toy_id | toy         | colors            |
+--------+-------------+-------------------+
|      5 | whiffleball | white,yellow,blue |
|      6 | frisbee     | green,yellow      |
|      9 | Kite        | red,green,yellow  |
|     12 | yoyo        | white,yellow      |
+--------+-------------+-------------------+
4 rows in set (0.002 sec)
```

기존의 테이블은 하나의 열에 하나의 값 만 포함해야하는데 그렇지 못하여 제 1 정규형을 만족하지 못한다.

```sql
ALTER TABLE toy
ADD COLUMN color1 VARCHAR(10),
ADD COLUMN color2 VARCHAR(10),
ADD COLUMN color3 VARCHAR(10),
DROP COLUMN colors;

SELECT * FROM toy;

update toy SET color1 = "white", color2 = "yellow", color3 = "blue" WHERE toy_id = 5;
update toy SET color1 = "green", color2 = "yellow" WHERE toy_id = 6;
update toy SET color1 = "red", color2 = "blue", color3 = "green" WHERE toy_id = 9;
update toy SET color1 = "white", color2 = "yellow" WHERE toy_id = 12;

SELECT * FROM toy;
```

```bash
MariaDB [gregs_list]> ALTER TABLE toy
    -> ADD COLUMN color1 VARCHAR(10),
    -> ADD COLUMN color2 VARCHAR(10),
    -> ADD COLUMN color3 VARCHAR(10),
    -> DROP COLUMN colors;
Query OK, 0 rows affected (0.011 sec)
Records: 0  Duplicates: 0  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM toy;
+--------+-------------+--------+--------+--------+
| toy_id | toy         | color1 | color2 | color3 |
+--------+-------------+--------+--------+--------+
|      5 | whiffleball | NULL   | NULL   | NULL   |
|      6 | frisbee     | NULL   | NULL   | NULL   |
|      9 | Kite        | NULL   | NULL   | NULL   |
|     12 | yoyo        | NULL   | NULL   | NULL   |
+--------+-------------+--------+--------+--------+
4 rows in set (0.001 sec)

MariaDB [gregs_list]> update toy SET color1 = "white", color2 = "yellow", color3 = "blue" WHERE toy_id = 5;
Query OK, 0 rows affected (0.001 sec)
Rows matched: 1  Changed: 0  Warnings: 0

MariaDB [gregs_list]> update toy SET color1 = "green", color2 = "yellow" WHERE toy_id = 6;
Query OK, 0 rows affected (0.001 sec)
Rows matched: 1  Changed: 0  Warnings: 0

MariaDB [gregs_list]> update toy SET color1 = "red", color2 = "blue", color3 = "green" WHERE toy_id = 9;
Query OK, 0 rows affected (0.000 sec)
Rows matched: 1  Changed: 0  Warnings: 0

MariaDB [gregs_list]> update toy SET color1 = "white", color2 = "yellow" WHERE toy_id = 12;
Query OK, 0 rows affected (0.001 sec)
Rows matched: 1  Changed: 0  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM toy;
+--------+-------------+--------+--------+--------+
| toy_id | toy         | color1 | color2 | color3 |
+--------+-------------+--------+--------+--------+
|      5 | whiffleball | white  | yellow | blue   |
|      6 | frisbee     | green  | yellow | NULL   |
|      9 | Kite        | red    | blue   | green  |
|     12 | yoyo        | white  | yellow | NULL   |
+--------+-------------+--------+--------+--------+
4 rows in set (0.001 sec)
```

이제 하나의 열에 하나의 값만 포함하지만 여전히 제 1 정규형을 만족하지 못한다. 왜냐하면, 여러 열들이 같은 종류의 정보 값을 가지고 있어 같은 데이터가 반복되기 때문이다. 그렇다면 제 1정규형을 만족시키려면 어떻게 해야할까 ?

```sql
CREATE TABLE toy_color(
    toy_id INT NOT NULL,
    color VARCHAR(10));

INSERT INTO toy_color
VALUES (5,"white"),
(5,"yellow"),
(5,"blue"),
(6,"green"),
(6,"yellow"),
(9,"red"),
(9,"blue"),
(9,"green"),
(12,"white"),
(12,"yellow");
```

```bash
MariaDB [gregs_list]> CREATE TABLE toy_color(
    ->     toy_id INT NOT NULL,
    -> color VARCHAR(10));
Query OK, 0 rows affected (0.013 sec)

MariaDB [gregs_list]> INSERT INTO toy_color
    -> VALUES (5,"white"),
    -> (5,"yellow"),
    -> (5,"blue"),
    -> (6,"green"),
    -> (6,"yellow"),
    -> (9,"red"),
    -> (9,"blue"),
    -> (9,"green"),
    -> (12,"white"),
    -> (12,"yellow");
Query OK, 10 rows affected (0.004 sec)
Records: 10  Duplicates: 0  Warnings: 0

MariaDB [gregs_list]> SELECT * FROM toy_color;
+--------+--------+
| toy_id | color  |
+--------+--------+
|      5 | white  |
|      5 | yellow |
|      5 | blue   |
|      6 | green  |
|      6 | yellow |
|      9 | red    |
|      9 | blue   |
|      9 | green  |
|     12 | white  |
|     12 | yellow |
+--------+--------+
10 rows in set (0.001 sec)
```

새로 만든 toy_color 테이블은 기존 테이블의 기본키를 참조키로 만들어져 있다. 게다가 각 행이 유일 무이하며, 각 열은 제 1 정규형의 조건을 모두 만족한다. 이 테이블의 모든 행이 유일무이한 값이기에 이 두 열을 조합하여 기본키로 사용할 수 있는데 이를 합성키라고 부른다. 합성키는 여러개의 열들로 구성되며, 유일무이한 키를 만드는 기본키이다.

한 테이블 내의 열들이 서로 어떻게 연결될 수 있는지에 대해 고려하는 것이 제 2 정규형과 제 3 정규형을 아는데 중요하다. 테이블 내 열과 열 간의 관계에는 종속관계가 있다. 종속관계란 하나의 열이 변경되면 그에 따라 같이 변경되야하는 열을 의미한다. 예를 들면 아래 테이블의 이름과 이니셜 열의 관계가 종속 관계이다.

```sql
CREATE TABLE super_heroes(
    name VARCHAR(20),
    power VARCHAR(50),
    weakness VARCHAR(20),
    city VARCHAR(20),
    country VARCHAR(10),
    arch_enemy VARCHAR(50),
    initials VARCHAR(10)
);

INSERT INTO super_heroes
VALUES ("Super Trashman","Cleans quickly","bleach","Gotham","US","Verminator","ST"),
("The Broker","Makes money from nothing", NULL, "New York","US","Mister Taxman","TB"),
("Super Guy", "Flies","birds","Metropolis","US","Super Fella","SG"),
("Wonder Waiter", "Never forgets an order", "insects","Paris","France","All You Can Eat Girl","WW"),
("Dirtman", "Creates dust storms", "bleach","Tulsa","US","Hoover","D"),
("Super Guy", "Super strength", "aluminum", "Metropolis","US","Badman","SG"),
("Furious Woman", "Get really, really angry", NULL, "Rome", "italy","The Therapist", "FW"),
("The Toad", "Tongue of justice","insects","London","England","Heron","T"),
("Librarian", "Can find anything", "children","Springfield","US","Chaos Creep","L"),
("Goose Girl", "Flies", "NULL","Minneapolis","US","THE Quilter","GG"),
("Stick Man", "Stands in for humans", "hang man", "London", "England", "Eraserman","SM");

```

```bash
MariaDB [gregs_list]> SELECT * FROM super_heroes;
+----------------+--------------------------+----------+-------------+---------+----------------------+----------+
| name           | power                    | weakness | city        | country | arch_enemy           | initials |
+----------------+--------------------------+----------+-------------+---------+----------------------+----------+
| Super Trashman | Cleans quickly           | bleach   | Gotham      | US      | Verminator           | ST       |
| The Broker     | Makes money from nothing | NULL     | New York    | US      | Mister Taxman        | TB       |
| Super Guy      | Flies                    | birds    | Metropolis  | US      | Super Fella          | SG       |
| Wonder Waiter  | Never forgets an order   | insects  | Paris       | France  | All You Can Eat Girl | WW       |
| Dirtman        | Creates dust storms      | bleach   | Tulsa       | US      | Hoover               | D        |
| Super Guy      | Super strength           | aluminum | Metropolis  | US      | Badman               | SG       |
| Furious Woman  | Get really, really angry | NULL     | Rome        | italy   | The Therapist        | FW       |
| The Toad       | Tongue of justice        | insects  | London      | England | Heron                | T        |
| Librarian      | Can find anything        | children | Springfield | US      | Chaos Creep          | L        |
| Goose Girl     | Flies                    | NULL     | Minneapolis | US      | THE Quilter          | GG       |
| Stick Man      | Stands in for humans     | hang man | London      | England | Eraserman            | SM       |
+----------------+--------------------------+----------+-------------+---------+----------------------+----------+
11 rows in set (0.001 sec)

```

열 간의 종속 관계를 표시할 때는 T.x ->; T.y 와 같이 표시할 수 있다. 이 표시는 특정 테이블(T)의 y열은 열 x에 종속되어 있다는 의미이다. 위의 테이블을 토대로 표시해보자면, super_heroes.name ->; super_heroes.initials 과 같이 표시한다. 즉 슈퍼 히어로 테이블의 이니셜 열은 슈퍼 히어로 테이블의 이름 열에 종속된다.

종속 관계에는 부분적 함수 종속과 이행적 함수 종속이 있다. 부분 함수 종속은 키가 아닌 열이 합성키의 전체가 아닌 일부에 종속하는 경우를 의미한다. 예를 들면 이름과 파워 열을 합성키로 사용할 때 이니셜열이 이름 열에 종속되는 것이 부분적 함수 종속이다. 이행적 함수 종속은 키가 아닌 열이 다른 열을 변경시키게 되는 경우를 의미한다. 예를 들면, 위의 테이블에서 도시(city) 열은 나라(country)열에 종속되어 있는데 이 때 나라가 변경되면 도시도 변경되며 나라 열은 합성 키와 관련되어 있지 않기 때문에 이행적 종속 관계이다. 부분적 종속 함수는 키와 관련되기에 부분적 종속 문제를 피할려면, 새로운 키 열을 만들면 피할 수 있다.

이 종속관계가 바로 제 2 정규형과 연관된다.

```sql
CREATE TABLE toy_table(
    toy_id INT NOT NULL,
    toy VARCHAR(20),
    PRIMARY KEY(toy_id)

)

INSERT INTO toy_table
VAlUES (5, "whiffleball"),
(6, "frisbee"),
(9, "Kite"),
(12, "yoyo");

CREATE TABLE toy_store(
    toy_id INT NOT NULL,
    store_id INT NOT NULL,
    color VARCHAR(10),
    inventory INT,
    store_address VARCHAR(20),
    PRIMARY KEY(toy_id,store_id)
);

INSERT INTO toy_store
VALUES (5,1,"white",34, "23 Maple"),
(5,3,"yellow",12, "100 E. North St."),
(5,2,"blue",5, "23 Maple"),
(6,2,"green",10, "1902 Amber Ln."),
(6,4,"yellow",24, "17 Engleside") ,
(9,1,"red",50, "23 Maple"),
(9,2,"blue",2, "1902 Amber Ln."),
(9,4,"green",18, "1902 Amber Ln."),
(12,1,"white",28,"17 Engleside"),
(12,4,"yellow",11,"17 Engleside");


```

```bash
MariaDB [gregs_list]> SELECT * FROM toy_store;
+--------+----------+--------+-----------+------------------+
| toy_id | store_id | color  | inventory | store_address    |
+--------+----------+--------+-----------+------------------+
|      5 |        1 | white  |        34 | 23 Maple         |
|      5 |        2 | blue   |         5 | 23 Maple         |
|      5 |        3 | yellow |        12 | 100 E. North St. |
|      6 |        2 | green  |        10 | 1902 Amber Ln.   |
|      6 |        4 | yellow |        24 | 17 Engleside     |
|      9 |        1 | red    |        50 | 23 Maple         |
|      9 |        2 | blue   |         2 | 1902 Amber Ln.   |
|      9 |        4 | green  |        18 | 1902 Amber Ln.   |
|     12 |        1 | white  |        28 | 17 Engleside     |
|     12 |        4 | yellow |        11 | 17 Engleside     |
+--------+----------+--------+-----------+------------------+
10 rows in set (0.001 sec)

```

합성키 역시 기본키이기에 중복된 경우가 발생하면 안된다. 따라서 두 가지 열을 합친 것이 모두 고유해야한다. 합성키를 기본키로 가진 테이블에서 키가 아닌 다른 모든 열들이 기본키의 일부이면 1NF 테이블은 2NF 테이블이 될 수 있다. 또한, 1NF 테이블이 하나의 열로 된 기본키를 가지면 2NF 제 2 정규형을 만족한다. AUTO INCREMENT를 ID 열에 할당하는 이유도 제 2 정규형을 만족시키기 위함이다.

이에 따라 제 2 정규형의 조건은 제 1 정규형이여야 한다. 그리고, 열들 간에 부분적 함수 의존이 없어야 한다. 위의 토이 스토어 테이블은 제 2 정규형 테이블 3개로 변경해보자.

```sql
CREATE TABLE toy_info(
    toy_id INT NOT NULL AUTO_INCREMENT,
    toy VARCHAR(20),
    color VARCHAR(10),
    cost DEC(3,2),
    weight DEC(2,1),
    PRIMARY KEY(toy_id)
);

CREATE TABLE store_info(
    store_id INT NOT NULL AUTO_INCREMENT,
    address VARCHAR(20),
    phone VARCHAR(10),
    manager VARCHAR(10),
    PRIMARY KEY(store_id)
);

CREATE TABLE store_inventory(
    toy_id INT NOT NULL AUTO_INCREMENT,
    store_id INT NOT NULL,
    inventory INT,
    PRIMARY KEY(toy_id,store_id)
);

INSERT INTO toy_info
VALUE (NULL, "whiffleball", "white", 1.95, 0.3),
(NULL, "whiffleball", "yellow", 2.20, 0.4),
(NULL, "whiffleball", "blue", 1.95, 0.3),
(NULL, "frisbee", "green", 3.50, 0.5),
(NULL, "frisbee", "yellow", 1.50, 0.2),
(NULL, "kite", "red", 5.75, 1.2),
(NULL, "kite", "blue", 5.75, 1.2),
(NULL, "kite", "green", 3.15, 0.8),
(NULL, "yoyo", "white", 4.25, 0.4),
(NULL, "yoyo", "yellow", 1.50, 0.2);

INSERT INTO store_info
VALUE (NULL, "23 Maple", "555-6712","Joe"),
(NULL, "1902 Amber Ln.", "555-3478","Susan"),
(NULL, "100 E. North St.", "555-0987","Tara"),
(NULL, "17 Engleside", "555-6554","Gordon");

INSERT INTO store_inventory
VALUE (NULL,1,34),
(NULL,3,12),
(NULL,1,5),
(NULL,2,10),
(NULL,4,24),
(NULL,1,50),
(NULL,2,2),
(NULL,2,18),
(NULL,4,28),
(NULL,4,11);
```

그렇다면 제 3 정규형의 조건은 무엇일까? 제 3정규형의 조건은 제 2 정규형을 만족해야한다. 그리고, 열들 간에 이행적 종속 관계가 없어야 한다. 인위적으로 만든 기본키가 있고 합성키가 없는 테이블은 항상 제 2 정규형을 만족한다. 따라서 제 3 정규형의 조건 중 하나는 만족시키기 쉽다.