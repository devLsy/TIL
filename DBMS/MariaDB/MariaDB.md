# MariaDB 관련 TIL
## 빈 테이블 조회 -> information_schema DB를 활용(절대 information_schema의 정보를 수정하면 안됨)
### Mysql 혹은 MariaDB의 메타데이터 정보를 담고 있는 information_schema
~~~
SELECT
            table_name, table_rows
FROM
            information_schema. tables
WHERE table_schema =  '스키마명' and table_rows = 0
;
~~~


