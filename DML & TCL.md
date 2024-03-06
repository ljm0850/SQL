# DML & TCL

- 정의된 데이터베이스에 레코드를 입력하거나, 수정, 삭제 및 조회하기 위한 명령어

## DML

### INSERT

```sql
-- employees 테이블에 새로운 직원 레코드 추가
INSERT INTO employees (employee_id, first_name, last_name, hire_date)
VALUES (1, 'John', 'Doe', '2022-03-05');
```

### UPDATE

```sql
-- customers 테이블에서 customer_id가 101인 레코드의 first_name과 last_name을 수정
UPDATE customers
SET first_name = 'Alicia', last_name = 'Johnson'
WHERE customer_id = 101;
```

### DELETE

```sql
-- employees 테이블에서 employee_id가 1인 레코드 삭제
DELETE FROM employees
WHERE employee_id = 1;

-- 아래 같이 전체를 삭제시 FROM 생략 가능
DELETE MENU;
```



### SELECT

```sql
SELECT name FROM employees where id = 1;
SELECT DISTINCT 성별 FROM employees;
```

- DISTINCT : 중복 제거, NULL값도 단일 행으로 봄

#### 문자형 함수

- LOWER(문자열) : 영어 문자열 소문자로 변환
- UPPER(문자열) : 영어 문자열 대문자로 변환
- CONCAT(문자열1,문자열2) : 문자열1과 문자열2를 결합
- SUBSTR(문자열,s,cnt) : 문자열[s:s+cnt], s번째 자리값부터 cnt개를 자른다
- LENGTH(문자열) = LEN(문자열) : 공백을 포함한 문자열의 길이 값

- TRIM(a FROM bString)
  - bString에서 a라는 문자를 삭제 (aabbccdd=>bbccdd)
  - 지정된 문자가 없으면 공백 삭제
- LTRIM(문자열,제거대상)
  - 왼쪽에서 지정된 문자를 삭제
  - LTRIM('aabbccaa','a) aabbccaa => bbccaa
  - 지정된 문자가 없으면 좌측의 공백 제거
- REPLACE(문자열,대상,대체문자)
  - REPLACE('aabbccaa','a','t') => 'ttbbcctt'
  - 대체 문자가 없을 경우 대상이 삭제가 됨

#### 숫자형 함수

- ROUND(숫자, [자릿수]) : 반올림, 자리수가 없으면 0이 default
- TRUNC(숫자,[자릿수]) : 버림
- CEIL(숫자) : 크거나 같은 최소 정수 33.3 => 34
- FLOOR(숫자) : 작거나 같은 최대 정수 33.3 => 33
- MOD(분자,분모) : 분자를 분모로 나눈 **나머지** 반환
- SIGN(숫자) : 숫자가 양수면 1, 0이면 0, 음수면 -1
- ABS(숫자) : 절대값

#### 날자형 함수

- SYSDATE : 쿼리를 돌리는 현재 날자&시간 출력
  - 2024/03/06 23:16 형태
- EXTRACT(정보 FROM 날짜) : 날짜형 데이터에서 원하는 값을 추출함
  - `EXTRACT (YEAR FROM date '2024-03-06') => 2024`

#### 명시적 형변환 함수 (like 타입 변환)

- TO_NUMBER(문자열) : 문자열을 숫자로 변환
- TO_CHAR(숫자 or 날짜, [포맷])
  - `TO_CHAR(date '2024-03-06','day) => 수요일`
  - `TO_CHAR(200) => '200'`
- TO_DATE(문자열,포맷)
  - `TO_DATE('2024030620','YYYYMMDDHH24') => 2024/03/06 20:00:00`
- 인덱스 사용시 형변환을 수행하면 인덱스가 사용 불가

#### 암시적 형변환

- `SELECT * FROM 고객 WHERE 고객번호 ='12345'`
  - 고객번호가 number형 타입일 경우 타입이 자동으로 변형되어 탐색
- 인덱스에 대해 형변환 수행시

#### DECODE (ORACLE DB) & CASE WHEN

- IF 문

```sql
SELECT DECODE(grade, 'A', 'Excellent', 'B', 'Good', 'C', 'Average', 'Fail') AS result
FROM student_grades;

# 다른 DB
SELECT
  CASE grade
    WHEN 'A' THEN 'Excellent'
    WHEN 'B' THEN 'Good'
    WHEN 'C' THEN 'Average'
    ELSE 'Fail'
  END result	# 결과 열의 이름을 result로 지정
FROM student_grades;
```



#### ORDER BY

```sql
SELECT 회원코드 AS C_ID , 연령 AS AGE, 이름 AS NAME FROM C_INFO ORDER BY (CASE WHEN 회원코드 = 101 OR 회원코드 = 104 THEN 1 ELSE 2 END), 연령 DESC;
# 회원코드가 101,104를 우선순위로 정렬 한 후, 연령순서로 내림차순 정렬
```





## TCL ?

- Transaction Control Language
- 트랜잭션 : 데이터베이스의 상태를 변화시키기 위해서 수행하는 작업의 단위

### 특징

- 고립성 : 트랜잭션이 실행되는 동안 다른 트랜잭션에 영향을 받아 잘못된 결과를 만들어선 안된다.
- 원자성 : 트랜잭션에서 정의된 연산은 모두 성공적으로 실행되던지, 전혀 실행되지 않은 상태로 있어야 한다.
- 지속성 : 트랜잭션이 성공적으로 완료되면 해당 트랜잭션이 갱신한 DB 내용은 영구적으로 저장된다.
- 일관성 : 트랜잭션 발생 전 DB에 잘못된 점이 없다면 트랜잭션 수행 후에도 잘못된 것이 있으면 안된다.

### COMMIT

- 데이터에 대한 변화를 DB에 반영하기 위한 명령어
- 커밋 이후 이전 데이터는 사라지고, 변경된 데이터는 DB에 반영됨

### SAVEPOINT

- 코드를 분할하기 위한 저장 포인트 지정

### ROLLBACK

- 트랜잭션이 시작되기 이전의 상태로 되돌리기 위한 언어
- 최신 COMMIT 이나 특수한 SAVEPOINT로 되돌릴 수 있음