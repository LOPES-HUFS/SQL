# 2장 SELECT 문: 데이터 가져오기의 축복

## 데이터베이스 접속하기

```sql
USE gregs_list;
```

## 테이블 생성하기

```sql
CREATE TABLE easy_drinks(drink_name VARCHAR(50), main VARCHAR(30), amount1 DEC(3,2), second VARCHAR(50), amount2 DEC(3,2), directions VARCHAR(100));
```

## 테이블 데이터 추가하기

이번에는 한 줄씩 입력하지 말고 한꺼번에 입력하겠다.

```sql
INSERT INTO easy_drinks  
VALUES
('Blackthorn',  'tonic water', 1.5, 'pineapple juice', 1 ,'stir with ice, strain into cocktail glass with lemon twist'),
('Blue moon' , 'soda', 1.5, 'blueberry juice', 0.75, 'stir with ice, strain into cocktail glass with lemon twist'),
('Oh my Gosh' , 'peach nectar', 1, 'pineapple juice', 1, 'stir with ice, strain into shot glass'),
('Lime  Fizz' , 'sprite', 1.5, 'lime juice', 1, 'stir with ice, strain into cocktail glass'),
('Kiss on the Lips' , 'cherry juice', 2, 'pineapple juice', 7, 'serve over ice with straw');
```

입력한 자료는 다음과 같다. 참고로 `SELECT`를 사용하면 입력한 자료를 볼 수 있다.  `*`를 사용하는 이유는 `easy_drinks` 데이블의 모든 열을 보여달라는 것이다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks;
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| drink_name       | main         | amount1 | second          | amount2 | directions                                                 |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| Blackthorn       | tonic water  |    1.50 | pineapple juice |    1.00 | stir with ice, strain into cocktail glass with lemon twist |
| Blue moon        | soda         |    1.50 | blueberry juice |    0.75 | stir with ice, strain into cocktail glass with lemon twist |
| Oh my Gosh       | peach nectar |    1.00 | pineapple juice |    1.00 | stir with ice, strain into shot glass                      |
| Lime  Fizz       | sprite       |    1.50 | lime juice      |    1.00 | stir with ice, strain into cocktail glass                  |
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw                                  |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
5 rows in set (0.001 sec)
```

## 특정 조건 찾기

우선 `main`열에 `sprite`가 들어가는 자료를 찾아보자.

```sql
SELECT * FROM easy_drinks WHERE main = 'sprite';
```

결과는 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks WHERE main = 'sprite';
+------------+--------+---------+------------+---------+-------------------------------------------+
| drink_name | main   | amount1 | second     | amount2 | directions                                |
+------------+--------+---------+------------+---------+-------------------------------------------+
| Lime  Fizz | sprite |    1.50 | lime juice |    1.00 | stir with ice, strain into cocktail glass |
+------------+--------+---------+------------+---------+-------------------------------------------+
1 row in set (0.001 sec)
```

```sql
SELECT * FROM easy_drinks WHERE main > 'soda';
```

알파벳 순서가 soda 보다 큰 경우만 쿼리하는 것이다. 결과는 다음과 같다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks WHERE main > 'soda';
+------------+-------------+---------+-----------------+---------+------------------------------------------------------------+
| drink_name | main        | amount1 | second          | amount2 | directions                                                 |
+------------+-------------+---------+-----------------+---------+------------------------------------------------------------+
| Blackthorn | tonic water |    1.50 | pineapple juice |    1.00 | stir with ice, strain into cocktail glass with lemon twist |
| Lime  Fizz | sprite      |    1.50 | lime juice      |    1.00 | stir with ice, strain into cocktail glass                  |
+------------+-------------+---------+-----------------+---------+------------------------------------------------------------+
2 rows in set (0.001 sec)
```

그러면 데이터 타입이 `DEC`인 `amount2`열에서는 `>`이 어떻게 작동할까? 한 번 해보자.

```sql
SELECT * FROM easy_drinks WHERE amount2 > '1.00';
```

결과는 다음과 같다. `amount2` 열에서 1.00보다 높은 값이 들어 있는 자료를 반환한다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks WHERE amount2 > '1.00';
+------------------+--------------+---------+-----------------+---------+---------------------------+
| drink_name       | main         | amount1 | second          | amount2 | directions                |
+------------------+--------------+---------+-----------------+---------+---------------------------+
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw |
+------------------+--------------+---------+-----------------+---------+---------------------------+
1 row in set (0.001 sec)
```

그러면 작은 따옴표('')를 빼고 하면 어떻게 될까?

```sql
SELECT * FROM easy_drinks WHERE amount2 > 1.00;
```

결과는 다음과 같다. 위와 같은 결과가 나온다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks WHERE amount2 > 1.00;
+------------------+--------------+---------+-----------------+---------+---------------------------+
| drink_name       | main         | amount1 | second          | amount2 | directions                |
+------------------+--------------+---------+-----------------+---------+---------------------------+
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw |
+------------------+--------------+---------+-----------------+---------+---------------------------+
1 row in set (0.001 sec)
```

참고로 데이터 타입이 `DEC`인 경우에는 작은 따옴표('')를 사용해도 바로 찾아주지만, 글자는 작은 따옴표('')를 필수로 넣어주어야 함

`amount1`에서 `SELECT * FROM easy_drinks WHERE amount1 > 1.00`을 실행하면, 이 명령어를 더 잘 이해할 수 있다.

```sql
MariaDB [gregs_list]> SELECT * FROM easy_drinks WHERE amount1 > 1.00;
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| drink_name       | main         | amount1 | second          | amount2 | directions                                                 |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
| Blackthorn       | tonic water  |    1.50 | pineapple juice |    1.00 | stir with ice, strain into cocktail glass with lemon twist |
| Blue moon        | soda         |    1.50 | blueberry juice |    0.75 | stir with ice, strain into cocktail glass with lemon twist |
| Lime  Fizz       | sprite       |    1.50 | lime juice      |    1.00 | stir with ice, strain into cocktail glass                  |
| Kiss on the Lips | cherry juice |    2.00 | pineapple juice |    7.00 | serve over ice with straw                                  |
+------------------+--------------+---------+-----------------+---------+------------------------------------------------------------+
4 rows in set (0.000 sec)
```

## 작은따옴표가 포함된 정보

'은 SQL의 문법으로 사용되기 때문에 텍스트로 사용되는 경우 내용 안의 작은따옴표에 \ 붙여서 추가하기.

```sql
INSERT INTO easy_drinks
VALUES
('Indian\'s Summer' , 'cherry juice', 2, 'pineapple juice', 7, 'serve over ice with straw');
```

## 특정 열의 값만 가져오기

```sql
SELECT drink_name, main, second FROM easy_drinks; # 특정 열 전체보기
SELECT drink_name, main, second FROM easy_drinks WHERE main = 'soda'; # 해당 조건의 열 전체보기
```

## 쿼리들의 결합 (AND, OR)

### AND

```sql
SELECT drink_name FROM easy_drinks WHERE main = 'soda' AND amount1 > 1;
SELECT drink_name, second FROM easy_drinks WHERE amount1 > 1 AND amount2 > 1;
```

### OR

```sql
SELECT drink_name FROM easy_drinks WHERE main = 'soda' OR amount1 > 1;
SELECT drink_name, second FROM easy_drinks WHERE amount1 < 2 OR amount2 > 5;
```

## 비교 연산자

- '=' : 같다  
- '<>' : 같지 않다  
- '<' : 조건보다 작다 (미만)  
- '>' : 조건보다 크다 (초과)  
- '<=' : 조건과 같거나 작다 (이하)  
- '>=' : 조건과 같거나 크다 (이상)  

## 비교연산자로 문자열 처리

```sql
SELECT drink_name, second FROM easy_drinks WHERE drink_name < 'C';
```

 음료 이름이 A, B로 시작하는 음료만 보여준다.

## NULL 찾기

### NULL 포함된 데이터 추가하기

```sql
INSERT INTO easy_drinks
VALUES
('Blue sun' , NULL, 1.5, 'blueberry juice', 0.75, NULL);
```

### NULL 포함된 데이터 찾기

```sql
 SELECT drink_name FROM easy_drinks WHERE main IS NULL;
```

## 문자 일부만으로 데이터 찾기 (LIKE)

```sql
SELECT drink_name FROM easy_drinks WHERE main LIKE '%juice';
```

 main 카테고리에 juice로 끝나는 데이터 찾기 (%: 다수의 불특정 문자, _ : 하나의 불특정 문자)

## 사이 값 찾기

```sql
 SELECT drink_name, second FROM easy_drinks WHERE amount1 BETWEEN 1 AND 1.5;
```

## OR 여러 개 == IN

```sql
SELECT drink_name FROM easy_drinks WHERE main = 'soda' OR main = 'sprite' OR main = 'tonic water';
SELECT drink_name FROM easy_drinks WHERE main IN ('soda', 'sprite','tonic water');
```

## NOT 사용법

```sql
SELECT drink_name FROM easy_drinks WHERE main NOT IN ('soda', 'sprite','tonic water');
SELECT drink_name, second FROM easy_drinks WHERE NOT amount1 BETWEEN 1 AND 1.5;
SELECT drink_name FROM easy_drinks WHERE NOT main LIKE '%juice';
```
