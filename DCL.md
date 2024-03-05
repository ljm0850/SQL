# DCL

`GRANT SELECT, UPDATE ON 테이블_명 TO 유저`

- GRANT : 권한 부여
  - `GRANT 권한 ON 테이블 TO 유저`
    - `WITH GRANT OPTION`: A가 B에게 권한을 주고, B가 C에게 준 후, A가 B의 권한을 회수하면 C도 회수됨 (부모가 회수 당하면 같이 회수)
    - `WITH ADMIN OPTION` : 권한을 독립적으로 부여
- REVOKE : 권한 회수
  - `REVOKE 권한 ON 테이블 FROM 유저`



## 권한의 종류

- SELECT
- INSERT
- UPDATE
- DELETE

- REFERENCES
- ALTER
- INDEX

- ALL



## ROLE

- 데이터베이스상에서 많은 사용자들에게 개별적으로 권한을 부여하고 관리하기는 어렵기에 만들어짐
- 다양한 권한을 하나의 그룹으로 묶어서 관리할 수 있도록 하는 논리적인 권한의 그룹

```sql
CREATE ROLE role_name;
GRANT SELECT ON table_name TO role_name;	# role_name이라는 ROLE에 SELECT 권한 부여
GRANT role_name TO user_name;	# 역할 부여
```