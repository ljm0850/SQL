# DDL

- 데이터를 보관하고 관리하기 위한 객체의 구조를 정의하기 위한 언어
- CREATE
- ALTER
- DROP
- RENAME
- TRUNCATE

## CREATE

- 데이터베이스 상 테이블 구조 생성
- `CREATE TABLE C_INFO()`
  - `CREATE TABLE example_table (column_name VARCHAR2(255) NOT NULL, -- 다른 열 정의 가능);`

### 데이터 유형

- **char** : 고정길이 문자열(할당된 길이만큼 메모리를 사용)
- **varchar**: 가변길이 문자열(문자 길이만큼 메모리를 사용, OracleDB에서는 varchar2 사용 권장)
- **number**: 숫자형
- **date**: 날자형

### 제약조건

- **defalut** : 기본값 지정
- **not null**: null 입력 불가
- **primary key** : 기본키 지정(unique한 값)
- **foreign key** : 외래키 지정

## ALTER

- 테이블과 컬럼에 대해 이름 및 속성 변경, 추가/삭제 등 구조 수정을 위해 사용

### RENAME

- `ALTER TABLE [테이블 이름] RENAME (TO) [변경할 이름]`
  - 이 경우 oracle 에서는 (TO)를 쓰지만, mySQL에서는 안씀 
  - `ALTER TABLE MENU RENAME TO LJM_MENU;` => oracle
  - `ALTER TABLE MENU RENAME LJM_MENU` => mySQL
  - `RENAME TABLE MENU TO LJM_MENU;` (ALTER 생략도 가능)
- `ALTER TABLE [테이블 이름] RENAME COLUMN [컬럼 이름] TO [변경할 이름]`
  - `RENAME TABLE TABLE_A TO TABLE_1, TABLE_B TO TABLE2;` (mySQL에서는 ,를 이용하여 여러개 변경 가능)

### MODIFY

- 컬럼 속성 변경

- ```sql
  ALTER TABLE example_table MODIFY COLUMN existing_column VARCHAR(50);
  ```

### ADD

- 컬럼 추가

- ```sql
  ALTER TABLE example_table ADD COLUMN new_column INT;
  ```

### DROP

- 컬럼 삭제

- ```sql
  ALTER TABLE example_table DROP column_name
  ```

### CONSTRAINT

```sql
ALTER TABLE table_name
ADD CONSTRAINT pk_constraint_name PRIMARY KEY (column1, column2);

ALTER TABLE table_name
ADD CONSTRAINT fk_constraint_name
FOREIGN KEY (column1) REFERENCES another_table(another_column);
```

- 제약조건을 변경할 떄 사용

#### MODIFY와 ADD CONSTRAINT

- NOT NULL을 제외한 제약조건을 추가시 ADD CONSTRAINT 사용

```sql
ALTER TABLE MENU MODIFY (이름 varchar(20) NOT NULL);
ALTER TABLE MENU ADD CONSTRAINT 제약조건명 제약조건 (대상);
```

## DROP

- 컬럼 및 테이블 삭제에 사용

```sql
ALTER TABLE MENU DROP COLUMN 이름; # 컬럼 삭제
DROP TABLE MENU; # 테이블 삭제
DROP TABLE MENU CASCADE CONSTRAINT; # 해당 테이블의 데이터를 외래키로 참조한 경우도 모두 삭제, 오라클에서 주로 사용 mySQL은 기본 옵션
```

##  TRUNCATE

- 테이블 초기화

```sql
TRUNCATE TABLE MENU;
```

- 테이블 속 데이터만 삭제되고 구조는 남아있음