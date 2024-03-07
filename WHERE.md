# WHERE

- 조건을 지정해서 조건에 맞는 레코드만 출력하기 위한 구문
- 비교연산자, 논리연산자 등 연산자를 통해 조건을 부여

## 연산자

### 비교연산자

- `=, >, >=, <, <=`
- `!=, ^=, <>` : 같지 않은 것을 조회

### 논리연산자

- AND
- OR
- NOT

### BETWEEN a AND b

```sql
WHERE col BETWEEN 10 AND 99
```

### NULL 판단

- `IS NULL`
- `IS NOT NULL`

- `a.col = NULL` **불가**

- NVL(col1,결과2), NVL2(col1,결과1,결과2) : Oracle에서 사용, NULL이면 결과2로 변경하는 함수, 
- ISNULL(col1,대체값) : SQLserver에서 사용, 
- IFNULL(col1,대체값) : MySQL에서 사용,
- COALESCE(v1,v2,v3... vn) : NULL이 아닌 최초의 값을 반환, v1이 NOT NULL이면 v1 ....
- NULLIF(v1,v2) : v1과 v2가 같으면 NULL, 다르면 v1

### IN

- `IN(x,y,z ...)` : x,y,z 등 목록에 값이 있는지 확인

```sql
SELECT * FROM C_INFO WHERE AGE IN (10,20,30);
```

- 이때 IN 으로 NULL값과 비교할 경우 실제 NULL값이여도 FALSE



### LIKE

- `A LIKE B` : A에 대하여 B와 유사한 문자열을 찾아줌
- `%` : 문자 0개 이상 존재한다는 의미
- `_`: 문자 한개 존재한다는 의미

```sql
WHERE name LIKE '%K%';
```



## Subquery

```sql
SELECT COUNT(*) FROM 결제 WHERE 회원번호 NOT IN (SELECT 회원번호 FROM 회원) AS 회원목록;
```

- 하나의 SQL문에 포함되어 있는 또 다른 SQL문
- 서브쿼리 내에서는 ORDER BY 불가

### 다중행 연산자

- IN : 메인쿼리의 비교대상이 서브쿼리 결과 중에서 하나라도 일치하면 참

- EXISTS : 서브쿼리의 결과가 존재하는지를 확인 하는 연산

  ```sql
  SELECT column1, column2
  FROM table_name
  WHERE EXISTS (SELECT 1 FROM another_table WHERE another_table.column = table_name.column);
  ```

- ALL : 메인쿼리의 비교대상이 서브쿼리의 결과와 모든 값이 일치하면 참

- ANY, SOME : 메인쿼리의 비교대상이 서브쿼리의 결과 중 하나 이상 일치하면 참

  ```sql
  SELECT * FROM 결제
  WHERE 나이 < ANY (SELECT 나이 FROM 시니어);
  ```

### WITH

```sql
SELECT * FROM (SELECT * FROM C_INFO WHERE name LIKE '%a%') AS T;

WITH T AS (SELECT * FROM C_INFO WHERE name LIKE '%a%')
, T2 AS (SELECT ... FROM T)
SELECT * FROM T;
```

- WITH 테이블명 AS (쿼리문) SELECT ... FROM 테이블명
- 임시테이블이나 뷰처럼 사용 가능
- 쿼리내에서 반복해서 동일한 테이블에 접근하는 경우 WITH절로 반복을 줄여 테이블 엑세스를 줄여 쿼리 성능을 높임

#### VIEW 테이블?

- 일종의 가상 테이블로서 실제 데이터가 하드웨어에 저장되는 것은 아님
- 실제 데이터를 가지고 있지 않고, SQL만 저장한다
- 테이블 구조가 변경되더라도 독립적으로 존재



## GROUP BY

- 특정 열을 기준으로 행들을 그룹화하는 데 사용

```sql
SELECT 고객이름, SUM(주문금액) AS 총주문금액
FROM 주문
GROUP BY 고객이름;
```



## HAVING

```sql
# C_INFO에서 평균 연령이 30대인 성별,평균 연령을 출력
SELECT 성별, AVG(연령)
FROM C_INFO
GROUP BY 성별
HAVING AVG(연령) >= 30 AND AVG(연령) < 40;
```

