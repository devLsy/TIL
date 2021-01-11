# MariaDB 관련 TIL
## information_schema DB를 활용(절대 information_schema의 정보를 수정하면 안됨)
### Mysql 혹은 MariaDB의 메타데이터 정보를 담고 있는 information_schema를 활용해서 다양하게 사용 가능(빈 테이블 조회 등)
#### information_schema를 이용한 빈테이블 조회
~~~
SELECT 
	TABLE_NAME AS 테이블명, 
	table_comment AS 코멘트, 
	table_rows AS 행
FROM information_schema.tables
	WHERE
    table_schema = '스키마명'AND table_rows <= 0;
~~~
~~~
컬럼으로 테이블 조회
SELECT 
	TABLE_SCHEMA
       ,TABLE_NAME
       ,COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS
WHERE COLUMN_NAME='컬럼명';
~~~
### 다양한 메타정보를 가지고 있는 information_schema
<img src="https://user-images.githubusercontent.com/44331989/99140701-7b89c900-2687-11eb-82ee-f043198c88cf.png" alt="MariaDB information_schema" /> <br>
화면에는 나오지 않았지만 TABLE_COMMENT도 조회할 수 있음 <br>


