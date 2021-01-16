# beeline

## 사용법

일반적인 `sql`과 거의 동일하게 작동하는 것 같습니다.

```sql
beeline> !connect jdbc:hive2://localhost:10000
Connecting to jdbc:hive2://localhost:10000
Enter username for jdbc:hive2://localhost:10000: 
Enter password for jdbc:hive2://localhost:10000: 
log4j:WARN No appenders could be found for logger (org.apache.hive.jdbc.Utils).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
Connected to: Spark SQL (version 3.0.1)
Driver: Hive JDBC (version 2.3.7)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://localhost:10000> 
0: jdbc:hive2://localhost:10000> !list
1 active connection:
 #0  open     jdbc:hive2://localhost:10000
0: jdbc:hive2://localhost:10000> CREATE DATABASE gregs_list;
+---------+
| Result  |
+---------+
+---------+
No rows selected (2.015 seconds)
0: jdbc:hive2://localhost:10000> CREATE TABLE doughnut_list(
. . . . . . . . . . . . . . . .>     doughnut_name VARCHAR(10),
. . . . . . . . . . . . . . . .>     doughnut_type VARCHAR(6)
. . . . . . . . . . . . . . . .> );
+---------+
| Result  |
+---------+
+---------+
No rows selected (3.311 seconds)
0: jdbc:hive2://localhost:10000> CREATE TABLE easy_drinks(drink_name VARCHAR(50), main VARCHAR(30), amount1 DEC(3,2), second VARCHAR(50), amount2 DEC(3,2), directions VARCHAR(100));
+---------+
| Result  |
+---------+
+---------+
No rows selected (1.172 seconds)
0: jdbc:hive2://localhost:10000> INSERT INTO easy_drinks  
. . . . . . . . . . . . . . . .> VALUES
. . . . . . . . . . . . . . . .> ('Blackthorn',  'tonic water', 1.5, 'pineapple juice', 1 ,'stir with ice, strain into cocktail glass with lemon twist'),
. . . . . . . . . . . . . . . .> ('Blue moon' , 'soda', 1.5, 'blueberry juice', 0.75, 'stir with ice, strain into cocktail glass with lemon twist'),
. . . . . . . . . . . . . . . .> ('Oh my Gosh' , 'peach nectar', 1, 'pineapple juice', 1, 'stir with ice, strain into shot glass'),
. . . . . . . . . . . . . . . .> ('Lime  Fizz' , 'sprite', 1.5, 'lime juice', 1, 'stir with ice, strain into cocktail glass'),
. . . . . . . . . . . . . . . .> ('Kiss on the Lips' , 'cherry juice', 2, 'pineapple juice', 7, 'serve over ice with straw');
+---------+
| Result  |
+---------+
+---------+
No rows selected (15.335 seconds)
0: jdbc:hive2://localhost:10000> SELECT * FROM easy_drinks;
+-------------------+---------------+----------+------------------+----------+----------------------------------------------------+
|    drink_name     |     main      | amount1  |      second      | amount2  |                     directions                     |
+-------------------+---------------+----------+------------------+----------+----------------------------------------------------+
| Oh my Gosh        | peach nectar  | 1.00     | pineapple juice  | 1.00     | stir with ice, strain into shot glass              |
| Blue moon         | soda          | 1.50     | blueberry juice  | 0.75     | stir with ice, strain into cocktail glass with lemon twist |
| Blackthorn        | tonic water   | 1.50     | pineapple juice  | 1.00     | stir with ice, strain into cocktail glass with lemon twist |
| Lime  Fizz        | sprite        | 1.50     | lime juice       | 1.00     | stir with ice, strain into cocktail glass          |
| Kiss on the Lips  | cherry juice  | 2.00     | pineapple juice  | 7.00     | serve over ice with straw                          |
+-------------------+---------------+----------+------------------+----------+----------------------------------------------------+
5 rows selected (4.653 seconds)
```
