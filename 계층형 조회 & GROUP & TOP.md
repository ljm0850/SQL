# 계층형 조회 & GROUP & TOP(N)

## 계층형 조회

- 트리형태의 데이터에 대해 조회를 수행하는 것
- ROOT 노드(부모 데이터) : 계층 구조 시작점, START WITH로 계층형 조회의 시작점을 설정

| col1 | col2 | col3 |
| ---- | ---- | ---- |
| 11   |      | 10   |
| 12   | 11   | 12   |
| 13   | 11   | 13   |
| 14   | 13   | 15   |



```sql
SELECT col3
FROM 조직구조
# START WITH로 ROOT NODE를 알려줌, 이 경우 col2가 NULL값인 곳(1행)에서 시작을 함
START WITH col2 IS NULL
# CONNECY BY, PRIOR 다음 레코드를 알려줌,이 경우 앞선 레코드(root)의 col1인 11과 같은 값을 가진 col2를 갖는 2,3행
CONNECT BY PRIOR col1 = col2
# ORDER SIBLINGS BY col3 : 같은 경우 순서를 결정, col3값을 기준이므로 col3가 작은 2행이 먼저(왼쪽노드에 위치한다고 생각하자)
ORDER SIBLINGS BY col3;
```

- 출력시 전위순회 방식 출력(부모-왼쪽-오른쪽)



## GROUP

- `GROUP BY 성별,연령` 
  - 성별*연령 별 소계
- `GROUP BY GROUPING SETS(성별, 연령)`
  - 성별,  연령 별 소계, => 괄호 묶은 집합별 집계 가능
  - GROUP BY 성별  UNION ALL  GROUP BY 연령 과 같음
- `GROUP BY ROLLUP(성별,연령)`
  - 성별,  성별*연령 별 소계,  전체(total) 집계 => GROUP BY에 앞에 오는 성별, 그리고 그룹되지 않았을때 전체 집계 표현
- `GROUP BY CUBE(성별,연령)` 
  - 성별, 연령, 성별*연령 전체 집계 => 모든 경우

```sql
SELECT 성별, 연령, SUM(결제급액)
FROM 결제
GROUP BY GROUPING SETS(성별, 연령대)
```

### GROUPING ?

- 집계함수를 지원하는 함수
- 집계된 컬럼이면 1을 반환, 집계 대상 컬럼이면 0을 반환 = 집계함수 적용 후 null이 되는 컬럼을 1로 출력

```sql
SELECT GROUPING(성별) 성별집계대상여부
, CASE WHEN GROUPING(성별)=1 THEN '모든성별'
ELSE 성별 END AS 성별
, GROUPING (연령대) 연령대집계대상여부
, CASE WHEN GROUPING(연령대)=1 THEN '전연령'
ELSE 연령대 END 연령대
,SUM(결제금액) 결제총액

FROM 결제
GROUP BY ROLLUP(성별,연령대)
ORDER BY 성별,연령;
```

| 성별집계대상여부 | 성별     | 연령대집계대상여부 | 연령대   | 결제총액 |
| ---------------- | -------- | ------------------ | -------- | -------- |
| 0                | F        | 1                  | 모든연령 | 4000     |
| 0                | M        | 1                  | 모든연령 | 7000     |
| 0                | F        | 0                  | 10대     | 1000     |
| 0                | F        | 0                  | 20대     | 3000     |
| 0                | M        | 0                  | 10대     | 1500     |
| 0                | M        | 0                  | 20대     | 5500     |
| 1                | 모든성별 | 1                  | 전연령   | 11000    |



## TOP(N)

- 테이블에서 N개의 행만 가져오고 싶을 경우 사용

### Oracle

- ROWNUM 슈도 칼럼, SQL 처리결과 집합의 각 행에 대해 임시적으로 부여되는 일련번호

```sql
SELECT *
FROM (SELECT * 
      FROM C_INFO
     	ORDER BY AGE)
WHERE ROWNUM <=100;
```

### SQL Server

- TOP절

```sql
SELECT TOP(100) NAME,AGE
FROM C_INFO
ORDER BY AGE;

SELECT TOP(100) WITH TIES # 마지막 행과 동일한 값이 있는 경우 추가 행이 출력됨, 이 경우 100번째와 나이가 같으면 추가 출력됨
				NAME,AGE
FROM C_INFO
ORDER BY AGE;
```

### 표준

```sql
SELECT NAME, AGE
FROM C_INFO
ORDER BY AGE FETCH FIRST 100 ROWS ONLY;
```

- FETCH : 반환할 것을 지정해줌
- ONLY : WITH TIES와 동일

```sql
SELECT NAME, AGE
FROM C_IFNO
ORDER BY AGE OFFSET 100 ROWS;
```

- OFFSET : 상위 N개를 건너 뛴 후 반환