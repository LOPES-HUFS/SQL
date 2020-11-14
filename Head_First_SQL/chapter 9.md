#chapter 9

```sql

CREATE TABLE job_current (
    contact_id INT NOT NULL AUTO_INCREMENT,
    title VARCHAR(20),
    salary VARCHAR(20),
    start_date DATE,
    PRIMARY KEY(contact_id)
);

CREATE TABLE job_desired (
    contact_id INT NOT NULL AUTO_INCREMENT,
    title VARCHAR(20),
    salary_low VARCHAR(20),
    salary_high VARCHAR(20),
    available VARCHAR(20),
    years_exp VARCHAR(20),
    PRIMARY KEY(contact_id)
);

CREATE TABLE job_listing (
    contact_id INT NOT NULL AUTO_INCREMENT,
    title VARCHAR(20),
    salary VARCHAR(20),
    zip VARCHAR(20),
    description VARCHAR(20),
    PRIMARY KEY(contact_id)
);

INSERT INTO job_listing
VALUES (NULL,"Cook","$250",NULL,NULL),
(NULL,"Hairdresser","$180",NULL,NULL),
(NULL,"Waiter","$170",NULL,NULL),
(NULL,"Web Designer","$200",NULL,NULL),
(NULL,"Web Developer","$220",NULL,NULL),
(459,"Artist","$150",NULL,NULL),
(873, "Comedian","$210",NULL,NULL);

INSERT INTO job_current
VALUES (NULL,"Cook","$250","2018-05-28"),
(NULL,"Hairdresser","$180","2018-07-13"),
(NULL,"Waiter","$170","2019-12-19"),
(NULL,"Web Designer","$200","2020-02-16"),
(NULL,"Web Developer","$220","2020-08-23"),
(459,"Artist","$150","2015-03-18"),
(873, "Comedian","$210","2017-04-21");

INSERT INTO job_desired
VALUES (NULL,"Cook","$250","$350",NULL,"4 years"),
(NULL,"Hairdresser","$180","$250",NULL,"4 years"),
(NULL,"Waiter","$170","$230",NULL,"3 years"),
(NULL,"Web Designer","$200","$280",NULL,"2 years"),
(NULL,"Web Developer","$220","$300",NULL,"2 years"),
(459,"Artist","$150","$200",NULL,"5 years"),
(873, "Comedian","$210","$270",NULL,"3 years");


INSERT INTO my_contacts(
    contact_id, first_name, last_name, phone
)
VALUES(3,'Sean', 'Miller', "555-4443"),
(4,'jared', 'Callaway', "555-5674"),
(5,"juan", "Garza", "555-0098");
```

```bash
MariaDB [gregs_list]> SELECT title FROM job_listing GROUP BY title ORDER BY title;
+---------------+
| title         |
+---------------+
| Cook          |
| Hairdresser   |
| Waiter        |
| Web Designer  |
| Web Developer |
+---------------+
5 rows in set (0.003 sec)
```

```sql
SELECT mc.first_name, mc.last_name, mc.phone
FROM my_contacts AS mc NATURAL JOIN job_desired AS jd
WHERE jd.title = "Web Developer"
AND jd.salary_low < "$250";
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name, mc.last_name, mc.phone FROM my_contacts AS mc NATURAL JOIN job_desired AS jd WHERE jd.title = "Web Developer" AND jd.salary_low < "$250";
+------------+-----------+----------+
| first_name | last_name | phone    |
+------------+-----------+----------+
| juan       | Garza     | 555-0098 |
+------------+-----------+----------+
1 row in set (0.001 sec)
```

## 서브쿼리

두 개의 쿼리가 하는 일을 하나의 쿼리로 하려면 두개 중 한 개의 쿼리를 서브쿼리로 넣어야 한다. 서브쿼리는 다른 쿼리에 쌓여진 쿼리를 의미하며 내부 쿼리라고도 한다. 예를 들면 아래의 쿼리 문에서 ()부분을 제외한 부분이 외부 쿼리가 되며, () 부분은 내부 쿼리가 된다. 이 내부 쿼리는 `SELECT title FROM job_listing GROUP BY title ORDER BY title;` 쿼리의 결과와 같다. 이에 따라서 외부 쿼리와 내부 쿼리를 합친 하나의 쿼리로 만들면 아래와 같이 바꿀 수 있다. 즉 서브 쿼리는 쿼리문 안에 있는 또 다른 쿼리문을 의미한다. 이렇게 서브 쿼리를 사용하는 방식은 조인을 사용하는 것 보다 코드가 줄고 간편해진다.

```sql
SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc NATURAL JOIN my_contacts AS mc
WHERE jc.title IN ("Cook","Hairdresser","Waiter","Web Designer","Web Developer");

SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc NATURAL JOIN my_contacts AS mc
WHERE jc.title IN (SELECT title FROM job_listing);

```

```bash
MariaDB [gregs_list]> SELECT mc.first_name, mc.last_name, mc.phone, jc.title
    -> FROM job_current AS jc NATURAL JOIN my_contacts AS mc
    -> WHERE jc.title IN ("Cook","Hairdresser","Waiter","Web Designer","Web Developer");
+------------+-----------+----------+---------------+
| first_name | last_name | phone    | title         |
+------------+-----------+----------+---------------+
| Jillian    | Anderson  | NULL     | Cook          |
| Pat        | NULL      | NULL     | Hairdresser   |
| Sean       | Miller    | 555-4443 | Waiter        |
| jared      | Callaway  | 555-5674 | Web Designer  |
| juan       | Garza     | 555-0098 | Web Developer |
+------------+-----------+----------+---------------+

MariaDB [gregs_list]> SELECT mc.first_name, mc.last_name, mc.phone, jc.title
    -> FROM job_current AS jc NATURAL JOIN my_contacts AS mc
    -> WHERE jc.title IN (SELECT title FROM job_listing);
+------------+-----------+------------+---------------+
| first_name | last_name | phone      | title         |
+------------+-----------+------------+---------------+
| Jillian    | Anderson  | NULL       | Cook          |
| Pat        | NULL      | NULL       | Hairdresser   |
| Sean       | Miller    | 555-4443   | Waiter        |
| jared      | Callaway  | 555-5674   | Web Designer  |
| juan       | Garza     | 555-0098   | Web Developer |
| Alexis     | Ferguson  | 5550983476 | Artist        |
| Regis      | Sullivan  | 5552311122 | Comedian      |
+------------+-----------+------------+---------------+
7 rows in set (0.004 sec)
```

서브 쿼리 규칙은 3가지: 서브 쿼리는 하나의 SELECT 문 안에 있다. 서브 쿼리는 항상 괄호() 안에 있다. 서브 쿼리는 세미 콜론이 없다. 세미 콜론은 항상 전체 쿼리의 마지막 부분에 위치한다. 서브 쿼리는 SELECT 문 외에도 INSERT, DELETE, UPDATE와도 함께 사용될 수 있다.

서브 쿼리 문이 올 수 있는 위치 4 가지: SELECT 절, 열들의 하나로 열 리스트를 선택하는 경우, FROM 절, HAVING 절
열 리스트를 선택하는 경우는 서브쿼리가 `WHERE column = (SELECT column FROM table)` 과 같이 쓰이는 경우이다. HAVING 절은 GROUP_BY의 WHERE 절과 같은 역할을 한다.

쿼리 결과에 나오는 열 이름을 지정해 줄 수도 있다. 아래와 같이 SELECT 문에 AS 새로운 열 이름을 추가해주면 된다.

```sql
SELECT mc.first_name AS firstname, mc.last_name AS lastname, mc.phone , jc.title
FROM job_current AS jc NATURAL JOIN my_contacts AS mc
WHERE jc.title IN (SELECT title FROM job_listing);
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name AS firstname, mc.last_name AS lastname, mc.phone , jc.title
    -> FROM job_current AS jc NATURAL JOIN my_contacts AS mc
    -> WHERE jc.title IN (SELECT title FROM job_listing);
+-----------+----------+------------+---------------+
| firstname | lastname | phone      | title         |
+-----------+----------+------------+---------------+
| Jillian   | Anderson | NULL       | Cook          |
| Pat       | NULL     | NULL       | Hairdresser   |
| Sean      | Miller   | 555-4443   | Waiter        |
| jared     | Callaway | 555-5674   | Web Designer  |
| juan      | Garza    | 555-0098   | Web Developer |
| Alexis    | Ferguson | 5550983476 | Artist        |
| Regis     | Sullivan | 5552311122 | Comedian      |
+-----------+----------+------------+---------------+
7 rows in set (0.002 sec)
```

서브 쿼리의 어려운 점은 서브 쿼리를 언제 사용할지와 어느 부분이 서브 쿼리로 사용할 수 있을지를 파악하는 일이다. 쿼리를 분석하는 것은 문제를 나누어서 보는 것과 같다. 예를 들면 현재 직업 리스트 중에서 가장 높은 월급을 받는 사람을 찾고자 한다면 이 질문을 2가지로 나누어야 한다. 먼저 누가의 정보를 담은 my_contacts의 first_name, last_name이 필요하다. 연봉이 가장 많은 이라는 정보는 job_current 테이블의 salary 값의 최대가 필요하다.

각 정보를 얻기 위해 쿼리 문을 만들어 보면 다음과 같다. 먼저 가장 높은 월급이라는 정보는 MAX(salary)를 사용하면 된다. 또한 사람에 대한 정보를 찾는 것 또한 간단하다.

```sql
SELECT MAX(salary) FROM job_current;

SELECT mc.first_name AS firstname, mc.last_name AS lastname
FROM my_contacts AS mc;
```

```bash
MariaDB [gregs_list]> SELECT MAX(salary) FROM job_current;
+-------------+
| MAX(salary) |
+-------------+
| $250        |
+-------------+
1 row in set (0.001 sec)

MariaDB [gregs_list]> SELECT mc.first_name AS firstname, mc.last_name AS lastname
    -> FROM my_contacts AS mc;
+-----------+----------+
| firstname | lastname |
+-----------+----------+
| Jillian   | Anderson |
| Pat       | NULL     |
| Sean      | Miller   |
| jared     | Callaway |
| juan      | Garza    |
| Alexis    | Ferguson |
| Regis     | Sullivan |
+-----------+----------+
7 rows in set (0.002 sec)
```

두 쿼리를 연결하는 방법은 natural join을 사용하면 된다.

```sql
SELECT mc.first_name AS firstname, mc.last_name AS lastname, jc.salary
FROM my_contacts AS mc NATURAL JOIN job_current AS jc;
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name AS firstname, mc.last_name AS lastname, jc.salary
    -> FROM my_contacts AS mc NATURAL JOIN job_current AS jc;
+-----------+----------+--------+
| firstname | lastname | salary |
+-----------+----------+--------+
| Jillian   | Anderson | $250   |
| Pat       | NULL     | $180   |
| Sean      | Miller   | $170   |
| jared     | Callaway | $200   |
| juan      | Garza    | $220   |
| Alexis    | Ferguson | $150   |
| Regis     | Sullivan | $210   |
+-----------+----------+--------+
7 rows in set (0.001 sec)
```

이제 WHERE 절로 가장 높은 이라는 조건을 만족시키는 서브 쿼리 문을 작성한다.

```sql
SELECT mc.first_name AS firstname, mc.last_name AS lastname, jc.salary
FROM my_contacts AS mc NATURAL JOIN job_current AS jc
WHERE jc.salary = (SELECT MAX(jc.salary) FROM job_current jc);
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name AS firstname, mc.last_name AS lastname, jc.salary
    -> FROM my_contacts AS mc NATURAL JOIN job_current AS jc
    -> WHERE jc.salary = (SELECT MAX(jc.salary) FROM job_current jc);
+-----------+----------+--------+
| firstname | lastname | salary |
+-----------+----------+--------+
| Jillian   | Anderson | $250   |
+-----------+----------+--------+
1 row in set (0.001 sec)
```

물론 서브 쿼리말고도 다른 방법을 사용해서 원하는 결과를 얻을 수도 있다.

다음과 같이 SELECT 열에 서브 쿼리를 사용할 수도 있다. 단 이 경우에는 서브 쿼리는 언제나 하나의 값만 반환하기에 원하는 결과가 사실과 달라질 수 있다는 점을 주의해야한다.  

```sql
SELECT mc.first_name, mc.last_name, (SElECT MAX(salary) FROM job_current) AS salary FROM my_contacts mc;
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name, mc.last_name, (SElECT MAX(salary) FROM job_current) AS salary FROM my_contacts mc;
+------------+-----------+--------+
| first_name | last_name | salary |
+------------+-----------+--------+
| Jillian    | Anderson  | $250   |
| Pat        | NULL      | $250   |
| Sean       | Miller    | $250   |
| jared      | Callaway  | $250   |
| juan       | Garza     | $250   |
| Alexis     | Ferguson  | $250   |
| Regis      | Sullivan  | $250   |
+------------+-----------+--------+
7 rows in set (0.000 sec)
```

위와 같이 월급이 앤더슨 외에도 모든 사람이 250 달러가 되는 모습을 볼 수 있다. 즉 열에 서브쿼리를 사용할 때는 항상 서브 쿼리가 반환되는 값에 주의해서 사용해야 한다.

서브 쿼리 안에 NATURAL JOIN 이 들어가도 사용할 수 있다. 다음과 같이 앤더슨의 월급 보다 낮은 월급을 지닌 사람들을 보여주라는 쿼리가 그 예이다.

```sql
SELECT mc.first_name, mc.last_name, jc.salary FROM my_contacts AS mc NATURAL JOIN job_current AS jc
WHERE jc.salary < (SELECT jc.salary FROM my_contacts mc NATURAL JOIN job_current jc WHERE email = 'jill_anderson@breakneckpizza.com');
```

```bash
MariaDB [gregs_list]> SELECT mc.first_name, mc.last_name, jc.salary FROM my_contacts AS mc NATURAL JOIN job_current AS jc
    -> WHERE jc.salary < (SELECT jc.salary FROM my_contacts mc NATURAL JOIN job_current jc WHERE email = 'jill_anderson@breakneckpizza.com');
+------------+-----------+--------+
| first_name | last_name | salary |
+------------+-----------+--------+
| Pat        | NULL      | $180   |
| Sean       | Miller    | $170   |
| jared      | Callaway  | $200   |
| juan       | Garza     | $220   |
| Alexis     | Ferguson  | $150   |
| Regis      | Sullivan  | $210   |
+------------+-----------+--------+
6 rows in set (0.001 sec)
```

## 비상관 쿼리

지금까지 사용한 서브 쿼리는 모두 비상관 쿼리이다. 비상관 쿼리란 외부 쿼리의 어떤 것도 참조하지 않고, 단독으로 사용되는 경우이다. 서브 쿼리는 단독으로도 외부 쿼리 값과 무관하게 사용될 수도 있다.

```sql
UPDATE job_current
SET salary = SUBSTR(salary, 2)

ALTER TABLE job_current
MODIFY COLUMN salary INT;
```

```sql
SELECT mc.first_name, mc.last_name, jc.salary FROM my_contacts AS mc NATURAL JOIN job_current AS jc
WHERE title LIKE 'Web%';

SELECT AVG(salary) FROM job_current WHERE title LIKE 'Web%';

SELECT mc.first_name, mc.last_name, jc.salary,
jc.salary - (SELECT AVG(salary) FROM job_current WHERE title LIKE 'Web%')
FROM my_contacts mc NATURAL JOIN job_current jc
WHERE title LIKE 'Web%';

```

웹 디자이너와 웹 개발자의 평균 월급을 구하기 위해 월급 열을 INT 형으로 변경해준다. 그리고 웹 디자이너와 웹 개발자의 정보와 평균 월급을 구해본다. 이제 구해진 두 정보를 하나의 쿼리를 묶어본다. 웹 디자이너와 웹 개발자의 월급에서 웹 직군의 평균 월급을 빼는 결과를 하나의 쿼리로 묶었다.

## 여러 값을 사용하는 비상관 쿼리 IN, NOT IN
