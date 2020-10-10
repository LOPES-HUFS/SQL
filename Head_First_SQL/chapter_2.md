# 2장 SELECT 문: 데이터 가져오기의 축복

## 테이블 생성하기
CREATE TABLE easy_drinks(drink_name VARCHAR(50), main VARCHAR(30), amount1 DEC(3,2), second VARCHAR(50), amount2 DEC(3,2), directions VARCHAR(100));

## 테이블 데이터 추가하기
INSERT INTO easy_drinks  
VALUES 
('Blackthorn',  'tonic water', 1.5, 'pineapple juice', 1 ,'stir with ice, strain into cocktail glass with lemon twist'),
('Blue moon' , 'soda', 1.5, 'blueberry juice', 0.75, 'stir with ice, strain into cocktail glass with lemon twist'),
('Oh my Gosh' , 'peach nectar', 1, 'pineapple juice', 1, 'stir with ice, strain into shot glass'),
('Lime  Fizz' , 'sprite', 1.5, 'lime juice', 1, 'stir with ice, strain into cocktail glass'),
('Kiss on the Lips' , 'cherry juice', 2, 'pineapple juice', 7, 'serve over ice with straw')

## 특정 조건 찾기
### DEC의 경우 ''사용해도 바로 찾아주지만, 글자는 '' 필수로 넣어주어야 함
SELECT * FROM easy_drinks WHERE main = 'sprite';
SELECT * FROM easy_drinks WHERE main > 'soda';
SELECT * FROM easy_drinks WHERE amount2 > 1.00;

## 작은따옴표가 포함된 정보
## 내용 안의 작은따옴표에 \ 붙여서 추가하기
INSERT INTO easy_drinks
VALUES
('Indian\'s Summer' , 'cherry juice', 2, 'pineapple juice', 7, 'serve over ice with straw')

## 특정 열의 값만 가져오기

SELECT drink_name, main, second FROM easy_drinks; # 열 전체보기
SELECT drink_name, main, second FROM easy_drinks WHERE main = 'soda'; # 해당 조건의 열 전체보기


## 쿼리들의 결합

SELECT drink_name FROM easy_drinks WHERE main = 'soda' AND amount1 > 1;
