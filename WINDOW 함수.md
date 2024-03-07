# WINDOW 함수

## WINDOW 함수

- 레코드(행) 사이의 관계를 쉽게 정의하기 위한 함수

| 매장코드 | 지역명 | 메뉴명 | 판매량 | 판매순위 |
| -------- | ------ | ------ | ------ | -------- |
| 101      | 서울   | 연어   | 500    | 1        |
| 101      | 서울   | 참치   | 400    | 2        |
| 101      | 서울   | 소고기 | 300    | 3        |
| 102      | 부산   | 연어   | 600    | 1        |
| 102      | 부산   | 튀김   | 300    | 2        |

```sql
SELECT WINDOW_FUNCTION (ARGUMENTS)
OVER ([PARTITION BY 컬럼명][ORDER BY 컬럼명][WINDOWING])
FROM 테이블명;

SELECT 매장코드, 지역명, 메뉴명, 판매량
			, DENSE_RANK () OVER (PARTITION BY 지역명 ORDER BY 판매량 DESC) AS 판매순위
FROM 판매;
```

- WINDOW_FUNCTION : 윈도우 함수

- ARGUMENTS : 인수(컬럼명 등 함수의 작업이 이뤄지는 대상) => SUM(판매량) 에서 판매량

- PARTITION BY : 테이블의 레코드들을 쪼개는 기준

- ORDER BY : 쪼개진 레코드들 내에서 혹은 전체 테이블에서 레코드를 어떤 기준으로 정렬할지

- WINDOWING : 함수의 연산 대상이 되는 레코드의 범위를 정함

  - `RANGE BETWEEN a AND b`

  - UNBOUNDED PRECEDING : 윈도우의 시작 위치 (첫 번째 행)

  - UNBOUNDED FOLLOWING : 윈도우의 마지막 위치(마지막 행)

  - CURRENT ROW : 윈도우 시작 위치가 현재 행

  - ```sql
    # 매출액 컬럼의 첫 행에서부터 현재 행 까지 누적합을 위해 sum을 사용
    SELECT 지역명, 매장코드, 매출일자, sum(매출액) OVER (PARTITION BY 매장코드 ORDER BY 매출일자
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) sum_sales
    FROM 매출
    WHERE EXTRACT(YEAR FROM 매출일자) = 2021
    ORDER BY 매출일자;
    ```

  - 

### WINDOW_FUNCTION 종류

- 그룹 내 집계함수: COUNT, SUM, MIN, MAX, AVG 등

- 그룹 내 순위 함수 : RANK, DENSE_RANK, ROW_NUMBER

  - RANK : 동일한 순위에 대해 동일한 순위를 부여, 동일한 순위를 하나의 건수로 계산하지 않음(1등이 2명일 경우 바로 3등)
  - DENSE_RANK: 동일한 순위에 대해 동일한 순위를 부여, 돌일한 순위를 하나의 건수로 계산(1등이 2명일 경우에도 2등이 존재)
  - ROW_NUMBER : 동일한 순위에 대해 고유한 순위를 부여(같은 점수여도 2,3등으로 나눔)

- 그룹 내 비율 관련 함수

  - PERCENT_RANK : 값이 아닌, 순서를 대상으로, 파티션 내에서 순서별 백분율을 조회함
  - NTILE(n) : 파티션별로 전체 건수를 n등분 한 값을 반환. n=4 경우 4등분한 것 내에서 몇인지 조회
  - CUME_DIST : 파티션 내 전체에서 현재 행의 값 이하인 레코드 건수에 대한 누적 백분율 조회 (0~1 사이의 값)

- 그룹 내 행 순서 함수

  - FIRST_VALUE : 가장 처음 나오는 값
  - LAST_VALUE : 가장 마지막에 나오는 값
  - LAG(컬럼명, 레코드위치차이값,null일 경우 대체값) : 이전 행을 가져온다
    - 레코드위치차이값의 기본값은 1
  - LEAD(컬럼명, 레코드위치차이값, null일 경우 대체값) : 다음 행을 가져온다

  ```sql
  CREATE TABLE orders (
    order_id INT,
    order_date DATE
  );
  
  INSERT INTO orders VALUES (1, '2022-01-10');
  INSERT INTO orders VALUES (2, '2022-01-15');
  INSERT INTO orders VALUES (3, '2022-01-20');
  INSERT INTO orders VALUES (4, '2022-02-05');
  INSERT INTO orders VALUES (5, '2022-02-10');
  
  SELECT
    order_id,
    order_date,
    LAG(order_date) OVER (ORDER BY order_date) AS previous_order_date
  FROM
    orders;
  ```

```yaml
| order_id | order_date | previous_order_date |
|----------|------------|----------------------|
| 1        | 2022-01-10 | null                 |
| 2        | 2022-01-15 | 2022-01-10           |
| 3        | 2022-01-20 | 2022-01-15           |
| 4        | 2022-02-05 | 2022-01-20           |
| 5        | 2022-02-10 | 2022-02-05           |

```