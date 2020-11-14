# MariaDB 관련 TIL
## 빈 테이블 조회
~~~
SELECT
            table_name, table_rows
FROM
            information_schema. tables
WHERE table_schema =  'knpapo' and table_rows = 0
;
~~~


