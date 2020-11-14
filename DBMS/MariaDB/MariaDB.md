# MariaDB 관련 TIL
## 빈 테이블 조회
~~~
SELECT * FROM information_schema.tables
	WHERE
    table_schema = '스키마명'
    	AND table_rows = 0
;
~~~


