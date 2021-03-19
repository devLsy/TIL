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

### 계정 생성 등
<img src="https://user-images.githubusercontent.com/44331989/105168972-350c8b00-5b5e-11eb-925f-0b0c36434232.PNG" /> <br>
### 권한 부여 등
<img src="https://user-images.githubusercontent.com/44331989/105169090-5b322b00-5b5e-11eb-8de9-dd5360e0be4f.PNG" /> <br>
출처 : https://ora-sysdba.tistory.com/entry/MariaDB-Maria-DB-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%83%9D%EC%84%B1-%EA%B6%8C%ED%95%9C-%EB%B6%80%EC%97%AC-%EC%A0%91%EC%86%8D <br>

### Operation CREATE USER failed for
<img src="https://user-images.githubusercontent.com/44331989/105273386-88beb900-5bde-11eb-8b93-820eb7a4dab1.PNG" /> <br>
출처 : https://hsunnystory.tistory.com/75 <br>

### Table '테이블명' doesn't exist
테이블이 있음에도 테이블이 없다고 나오는 경우 <br>
윈도우는 기본적으로 대소문자 구분이 없이 mariadb가 설치 되지만 리눅스는 대소문자 구분해서 설치하는 경우가 있음 <br>
mysql 계정으로 접속 후 아래의 쿼리를 보낸 후 값을 확인해야 함 <br>
0인 경우 대소문자 구분함, 1인 경우 대소문자 구분안함, 값이 2인 경우도 있다는데 아직 본적은 없음<br>
~~~
show variables like 'lower_case_table_names';
~~~
쿼리 날렸을 때 값이 0이면 값을 1로 바꿔서 대소문자 구분을 없애줘야 함 <br>
vi로 /etc/my.cnf을 열어서 [mysqld]안에 lower_case_table_names = 1를 넣어줌 <br>
<img src="https://user-images.githubusercontent.com/44331989/105277086-118d2300-5be6-11eb-9f4c-a994142a3acc.PNG" /> <br>
그 후 mariadb 재시작 후 테이블 다시 조회 해서 테스트 <br>

### linux 터미널에서 sql파일 실행
~~~
mysql -uroot -p "db명" < "sql파일명"
~~~

### 테이블 필드의 공객, 개행문자, 캐리지리턴 치환
~~~
-- 공백제거
update table set field = replace(field, ' ', '');
-- 개행문자 제거
update table set field = replace(field, '\r\n', '');
-- 탭 제거
update table set field = replace(field, 'char(9)', '');
-- 라인피드 제거
update table set field = replace(field, 'char(10)', '');
-- 캐리지리턴 제거
update table set field = replace(field, 'char(13)', '');
~~~
출처 : https://curryyou.tistory.com/68
