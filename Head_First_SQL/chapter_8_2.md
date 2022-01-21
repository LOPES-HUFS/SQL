# JOIN과 다중 테이블 연산

(382쪽 자료인데 잘못 만들어져 있는데 완벽하게 만들어서 저장합니다.)

```sql
CREATE DATABASE gregs_list;
USE gregs_list;

CREATE TABLE my_contacts_8(
    contact_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    last_name VARCHAR(30) default NULL,
    first_name VARCHAR(20) default NULL,
    profession VARCHAR(50) default NULL,
    seeking VARCHAR(100) default NULL
    );

INSERT INTO my_contacts_8(
    contact_id, last_name, first_name, profession, seeking
)
VALUES
    (NULL, 'Sobel', 'Paul', 'teacher', 'books, music'),
    (NULL, 'Marry', 'Harrison', 'lawyer', 'sports, music'),
    (NULL, 'John', 'Jacobs', 'lawyer', 'books'),
    (NULL, 'Andrew', 'Warner', 'programmer', 'sports'),
    (NULL, 'Ruby', 'Gordon', 'lawyer', 'books'),
    (NULL, 'Deanne', 'Reilly', 'lawyer', 'pets, books'),
    (NULL, 'Geraldine', 'Taylor', 'lawyer', 'music'),
    (NULL, 'Harry', 'Maxwell', 'lawyer', 'pets'),
    (NULL, 'Jaques', 'Pennington', 'teacher', 'books'),
    (NULL, 'Wendy', 'Hilsop', 'programmer', 'sports');
```

새로운 컬럼 추가 및 저장
(현재는 1열만 추가 했는데 한꺼번에 2열을 추가하는 코드를 작성!)

```sql
ALTER TABLE my_contacts_8
    ADD COLUMN interest1 VARCHAR(30);

UPDATE my_contacts_8
    SET interest1 = SUBSTRING_INDEX(seeking, ',' ,1);
```
