# JOIN & UNION & MINUS 등

## JOIN

- 두 테이블을 합쳐서 하나의 결과를 만드는 데 사용
- 테이블 간 결합이 되는 기준을 설정
  - 두 테이블에서 동일한 컬럼을 찾기
  - 레코드를 식별할 수 있는 컬럼으로 결정
  - PK 혹은 FK 기준으로도 많이 사용
- 집합과 유사

```sql
SELECT A.이름, B.전화번호
FROM C_INFO (AS[생략 가능]) A JOIN PHONE (AS) B ON A.회원코드 = B.회원코드;
```

### INNER JOIN

- 교집합을 의미

```sql
SELECT A.*, B.연령
FROM GENDER A INNER JOIN AGE B	# INNER 생략 가능
ON A.회원코드 = B.회원코드
WHERE A.성별 = 'F';

# 오라클에선 아래와 같이도 가능
SELECT A.*, B.연령
FROM GENDER A , AGE B	
WHERE A.회원코드 = B.회원코드
AND A.성별 = 'F';
```

```sql
# 3개 이상 JOIN
SELECT A.*, B.연령, C.생년
FROM GENDER A JOIN B
ON A.회원코드 = B.회원코드
JOIN BIRTH C ON B.연령 = C.연령;
```



### LEFT  (OUTER) JOIN

- 왼쪽 테이블을 기준으로 전체적으로 만들어 진 후 / 오른쪽과 교집합 되는 부분이 있으면 값을 넣고, 없으면 NULL

```sql
SELECT A.*, B.연령
FROM GENDER A LEFT JOIN AGE B
ON A.회원코드 = B.회원코드;
# 오라클
SELECT A.*, B.연령
FROM GENDER A, AGE B
WHERE A.회원코드 = B.회원코드(+);	# (+)를 붙인 테이블의 속성값이 NULL을 허용
```

| 회원코드 | 성별 |
| :------: | :--: |
|   101    |  F   |
|   102    |  M   |
|   103    |  F   |
|   104    |  M   |

| 회원코드 | 연령 |
| :------: | :--: |
|   101    |  23  |
|   103    |  22  |
|   105    |  32  |

- 결과

| 회원코드 | 성별 | 연령 |
| :------: | :--: | :--: |
|   101    |  F   |  23  |
|   102    |  M   |      |
|   103    |  F   |  22  |
|   104    |  M   |      |



### RIGHT (OUTER) JOIN

- 오른쪽 테이블을 기준으로 전체적으로 만들어 진 후 / 왼쪽과 교집합 되는 부분이 있으면 값을 넣고, 없으면 NULL

### (FULL) OUTER JOIN

- 합집합과 유사한 형태

```sql
SELECT A.*, B.연령
FROM GENDER A FULL[생략가능] OUTER JOIN AGE B
ON A.회원코드 = B.회원코드;
# 오라클, 9버전 이상에선 양쪽에 (+)가능
SELECT A.*, B.연령
FROM GENDER A, AGE B
WHERE A.회원코드(+) = B.회원코드(+);
```

## UNION & UNION ALL

- 합집합과 유사한 형태
- 동일한 컬럼 개수와 데이터 타입을 가진 두 테이블을 합쳐줌

| T1.회원코드 | T1.성별 | T2.회원코드 | T2.성별 |
| :---------: | :-----: | :---------: | :-----: |
|     101     |    F    |     101     |    F    |
|     102     |    M    |     103     |    F    |
|     103     |    F    |     105     |    M    |

| 회원코드 | 성별 |
| :------: | :--: |
|   101    |  F   |
|   102    |  M   |
|   103    |  F   |
|   105    |  M   |

- UNION ALL은 중복을 제거하지 않음, 그렇기에 위 표에서 회원코드 101,103이 두번 나오게 됨

```sql
SELECT * FROM T1
UNION ALL
SELECT * FROM T2;
```

## MINUS (오라클) // EXCEPT (SQL Server)

- 차집합과 비슷
- 중복된 레코드를 제거한 후 반환

```sql
# 오라클
SELECT * FROM A
MINUS
SELECT * FROM B;
# SQL Server
SELECT * FROM T1
EXCEPT
SELECT * FROM T2;
```

## EQUI JOIN <-> non-EQUI JOIN 구분

- EQUI JOIN : 동일한 컬럼을 사용하여 두 테이블을 결합

- non-EQUI JOIN : 정확하게 일치하지 않는 컬럼들을 사용하여 두 테이블을 결합
  - `<, >, <=, >=`사용

## CROSS JOIN

- A테이블이 5개의 행, B테이블이 3개의 행이라고 했을 시 총 5*3 = 15개의 행이 조회됨

```sql
SELECT * FROM A CROSS JOIN B;
```

