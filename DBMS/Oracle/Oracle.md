# Oracle 관련 TIL
## 재귀 복사
개발시 테스트를 위해 더미 데이터를 많이 집어넣을 때 사용하면 유용함<br>
<strong>(ex)INSERT INTO tbl_board (bno, title, content, writer)<br>
    (SELECT SEQ_BOARD.nextval, title, content, writer FROM tbl_board);</strong><br>
위의 경우는 tbl_board 테이블의 데이터 수만큼 insert를 진행하는 예제 쿼리문임<br>
반복할 경우 2배씩 증가함<br>

## Hint
오라클은 Slect문을 전달할 때 "힌트(hint)"라는 것을 사용할 수 있는데 힌트는 말 그대로 데이터베이스에 "지금 내가 전달한 Select문을 이렇게 실행해 주면 고맙겠습니다."라는 힌트이며 특이하게도 힌트구문에서 에러가 나도 SQL 실행에는 전혀 지장을 주지 않음<br>
따라서 Hint를 이용한 Select문을 작성한 후에는 실행 계획을 통해서 개발자가 원하는 대로 SQL이 실행되는지를 확인하는 습관을 가져야 함<br>
힌트 사용 문법은 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/53297899-ab1bf080-3868-11e9-9a89-617b6471d0c8.JPG"><br>
이 힌트는 pk_board라는 인덱스를 이용해서 뒤에서부터 정렬하라는 의미임<br>
웹에서 게시글 목록 페이지에서 가장 많이 사용하는 힌트는 인덱스와 관련된 'INDEX_ASC(오름차순), INDEX_DESC(내림차순)' 힌트이고 주로 'ORDER BY'를 위해서 사용한다고 생각하면 됨(데이터베이스에서 ORDER BY로 정렬해서 데이터를 가져오는 것 자체가 부하를 가져옴), 인덱스 자체가 정렬을 해둔 상태이므로 이를 통해서 SORT과정을 생략하기 위한 용도임<br>
INDEX_ASC/DESC 힌트는 테이블 이름과 인덱스 이름을 같이 parameter로 사용함<br>
INDEX_ASC/DESC를 이용하는 경우에는 동일한 조건의 ORDER BY 구문을 작성하지 않아도 됨<br>

## 사용자 테이블 중 빈 테이블 조회(ALL_TABLES, USER_TABLES, TAB 이용)
~~~
SELECT * FROM ALL_TABLES; (관리자일 경우)
SELECT * FROM USER_TABLES; (관리자 아닐 경우)
SELECT * FROM TAB; (관리자 아닐 경우)
~~~
참조 블로그 : https://javaoop.tistory.com/65 

## GROUP BY + 그룹함수(SUM, COUNT 등)
쉽게 생각하면 된다. GROUP BY 다음에 오는 컬럼으로 먼저 그룹핑을 한 다음 그룹함수를 실행<br>
(ex)
~~~
SELECT deptno, 
	   job,
	   AVG(NVL(sal, 0)) "AVG_SAL"
FROM emp
GROUP BY deptno, job
ORDER BY deptno,job;
~~~
위의 쿼리는 emp테이블에서 학급(deptno)으로 먼저 그룹핑을 한 다음 직업(job)으로 그룹핑을 한 후 SELECT절에 있는 그룹함수인 AVG() 함수를 실행한 결과를 나타낸다.<br>
주의사항이 있는데 GROUP BY사용 시 SELECT절의 그룹함수를 제외한 컬럼은 반드시 GROUP BY 절에 나온 컬럼을 명시해야 한다. 안할 경우 에러 발생<br>
~~~
SELECT deptno, 
	   job,
	   AVG(NVL(sal, 0)) "AVG_SAL"
FROM emp
GROUP BY deptno
ORDER BY deptno;
~~~
위와 같은 쿼리는 "ORA-00979 : GROUP BY 표현식이 아닙니다." 에러 발생 -> GROUP BY절에 없는 job이라는 컬럼을 SELECT절에 사용 했기 때문임<br>

## ROLL UP을 이용한 소계 구하기
~~~
SELECT 
	  DECODE(POSITION,NULL,'합계',POSITION) postion
	, NVL(sum(BONUS),0) sum
FROM PROFESSOR
GROUP BY ROLLUP(position) 
;
~~~
위의 쿼리의 결과는 아래와 같고 직급별 보너스의 합계를 구한 뒤 그 합계의 소계를 구함 (position으로 group by한 다음 sum한 값의 합을 구함) <br>
<img src="https://user-images.githubusercontent.com/44331989/108153081-c64f2d00-711d-11eb-942c-2ee90c3029e5.PNG" alt="오라클rollup"> <br>

### 테이블 복사(CTAS라고도 함)
#### 테이블 전체 복사
~~~
CREATE TABLE emp2
AS SELECT * FROM emp;
~~~
테이블을 만들 시 기존에 만들어진 테이블을 참조하여 생성하는 방법인데 주로 테이블 복사(백업 테이블)에 쓰임, 위의 쿼리는 emp테이블의 모든 걸 복사하는 것임 <br>
#### 테이블 구조만 복사
~~~
CREATE TABLE emp2
AS SELECT * FROM emp
   WHERE 1 = 2;
~~~
위 방법은 데이터를 제외한 테이블 구조만 복사할 때 많이 사용하는 방법이며 WHERE절에 1 = 2라는 틀린 조건을 적어줘서 그 조건에 해당되는 데이터가 없게 만드는게 핵심임 <br>
### DELETE, TRUNCATE, DROP 차이
<img src="https://user-images.githubusercontent.com/44331989/106087790-10b93b80-6168-11eb-95a3-a444f2e27626.jpg"> <br>
delete는 데이터만 지워지고 사용중인 디스크상의 공간은 유지, TRUNCATE는 CREATE TABLE로 테이블을 만들었던 상태(데이터가 1도 없음)로 모든 데이터를 삭제하고 컬럼만 남겨놓음, 사용중인 디스크 공간도 사라짐, DROP은 제일 위험한 명령어이고 데이터와 테이블 전체를 삭제함(사용중인 공간, 인덱스, 제약조건등 전부 삭제됨) <br>


이미지 출처 : 오라클 SQL과 PL/SQL 책 <br>

### oracle INSERT ALL로 다른 테이블에 동시에 같은 데이터 입력하기
INSERT ALL로 다른 테이블에 동시에 같은 데이터를 넣을 수 있음 <br>
~~~
-- 아래는 professor테이블에서 교수번호가 3000번에서 3999인 교수들의 교수번호와 이름을 prof_3, prof_3테이블에 동시에 입력하는 쿼리
INSERT ALL 
	INTO prof_3 values(profno, name)
	INTO prof_4 values(profno, name)
SELECT profno, name	
FROM PROFESSOR
WHERE profno BETWEEN 3000 AND 3999
~~~

### DML 중 DELETE문법
~~~
DELETE FROM table WHERE 조건;
~~~
DELETE 문법은 쉽지만 DELETE문은 데이터를 삭제한다고 알고 있지만 사실 데이터는 삭제되지 않고 해당 블록에 그대로 남아 있고 BBED같은 툴을 이용하면 DELETE된 데이터도 전부 복구 가능하다고 함 <br>
그래서 DELETE를 한 후에 테이블의 크기를 확인해 보면 크기가 줄어들지 않고 그대로임 <br>
예를 들어, 100만건 데이터가 있는 테이블의 용량이 100MB였는데, 1만건만 남기고 DELETE한다고 해서 용량이 1MB가 되지 않는다<br>
중요하니까 꼭 숙지할 것<br>

### MERGE
<img src="https://user-images.githubusercontent.com/44331989/106987202-47b6cf00-67b0-11eb-9450-cb9cc028458a.jpg"> <br>
mege를 할 때 중복값이 있으면 에러가 발생하기에 일반적으로 집계가 되는(중심이 되는) 테이블의 조건 컬럼에는 PK나 UNIQUE INDEX를 많이 설정하고 merge기능은 쿼리의 특징상 부하가 아주 많이 걸리는 경우가 많음, 많이 사용하지만 사용시에 주의 해야 함 <br>
이미지 출처 : 오라클 SQL과 PL/SQL 책 <br>

### package 
오라클의 패키지를 생성 후 이를 사용하기 위해서는 컴파일을 통해서 사용할 수 있도록 해야 함 <br>
이 때 이 패키지관련 권한 체크도 필요할 수 있음 <br>

### SELECT 1 from 테이블명
<img src="https://user-images.githubusercontent.com/44331989/107314145-64684500-6ad7-11eb-9b74-e06a9071b010.png"> <br>
이미지 출처 : https://codedragon.tistory.com/5885 <br>

### index
어떤 데이터가 어디에 있다는 주소록과 같은 개념이고 잘 사용하면 정말 좋지만 잘못 쓸 경우 오히려 성능 저하의 주범이 되므로 정말 잘 알고 공부를 많이 해서 사용해야 함 <br>
주로 많이 사용되는 인덱스는 크게 B-TREE 인덱스와 BITMAP 인덱스가 있음 <br>
사용자가 A라는 값을 조회하기 위해 SELECT 구문 수행 시 쿼리를 받은 오라클 서버 프로세스가 해당 쿼리를 수행해서 사용자가 원하는 데이터를 출력하는데 
오라클 서버 프로세스는 제일 먼저 데이터베이스 버퍼 캐시(하드 디스크에서 데이터를 가져올 경우 시간이 많이 걸리므로 많이 사용되는 데이터들을 임시로 보관하고 변경작업을 하는 메모리 공간)에 해당 데이터가 있는지 확인, 버퍼 캐시에 찾는 데이터가 있을 경우 하드 디스크까지 안가기에 빨리 결과를 볼 수 있음 <br>
하지만 버퍼 캐시에 데이터가 없을 경우 서버 프로세스는 하드 디스크에 있는 데이터 파일에서 A의 정보가 들어 있는 블록을 찾아서 버퍼 캐시로 복사 한 뒤 사용자에게 값을 돌려줌 <br>
여기서 쿼리 수행 속도에 아주 중요한 부분이 나오는데 이 때 인덱스를 사용하면 하드디스크의 모든 블록을 다 읽는 table full scan을 하지 않고 원하는 데이터가 있는 블록 주소를 찾아서 그 블록만 메모리로 복사 해 오면 빨리 작업을 끝낼 수 있음 <br>
인덱스 생성 명령을 실행하면 아래그림의 1번 과정에서 해당 데이터의 내용들을 전부 다 읽어서 메모리로 가져옴<br>
그리고 인덱스 생성하는 동안 데이터가 변경되지 못하도록 조치를 한 뒤 메모리에서 정렬을 함<br>
만약 PGA 메모리가 부족할 경우 임시 테이블 스페이스(Temporary Tablespace)를 사용해서 정렬을 함(이 정렬과정이 시간이 정말 많이 걸리는 과정임) <br>
※ 오라클에서 쿼리를 빨리 수행하게 하려면 가능한 한 정렬을 줄여야 함<br>
메모리에서 정렬이 끝난 데이터들은 인덱스 파일의 블록에 순서대로 기록됨<br>
인덱스 생성에서 기억해야 될 점은 인덱스를 생성하라고 하면 전체 테이블 스캔 후 정렬한 뒤 인덱스 파일의 블록에 순서대로 기록을 하게 됨 <br>
※ 이 과정에서 인덱스는 데이터가 정렬이 된 상태로 들어간다는 걸 알 수 있음 <br>
<img src="https://user-images.githubusercontent.com/44331989/111773481-939d7d80-88f1-11eb-9dc4-86eeabe35baa.png"> <br>
이미지 & 내용 출처 : 오라클 SQL과 PL/SQL 책 <br>

인덱스는 WHERE절에 오는 조건 컬럼이나 조인 컬럼 등에 만들어야 함(특별한 경우에는 SELECT에 있는 컬럼에 생성하기도 함) <br>
만약 pay컬럼으로 인덱스를 생성 했는데 SQL문에서 WHERE pay + 1000 = 2000이라는 조건으로 조회를 하면 pay컬럼의 인덱스는 사용할 수 없게 됨 <br>
이런 현상을 INDEX Suppressing Error이라고 부름 <br>
인덱스는 잘 생성해 놓고 SQL을 잘못 작성해서 인덱스를 사용할 수 없는 경우를 뜻함 <br>
위 경우 말고 인덱스가 설정 되어 있는 컬럼 조회 시 Not을 사용하는 경우도 마찬가지임 <br>
<strong>※ 인덱스 사용 시 WHERE절의 조건을 절대로 다른 형태로 가공해서 사용하면 안됨</strong> <br>

그런데 업무상 반드시 WHERE pay+1000=2000이라는 형태로 써야 되고 인덱스도 반드시 써야 된다면 인덱스 생성 시 저 형태로(pay+1000) 인덱스를 생성하면 되고 이런 형태의 인덱스를 함수 기반 인덱스(FBI)라고 부름, 이는 임시방편이고 근본적인 해결책은 아니기에 아주 조심해야 함 <br>
출처 : 오라클 SQL과 PL/SQL 책 <br>

B-TREE 형식의 인덱스는 주로 데이터의 값의 종류가 많고 중복 데이터가 적을 경우 사용하는 인덱스임 <br>
반대로 데이터 값의 종류가 적고 동일한 데이터가 많을 경우에는 주로 BITMAP 인덱스를 사용함 <br>
그리고 BITMAP INDEX를 생성하려면 데이터의 변경량이 적거나 없어야 함, 만약 BITMAP INDEX가 생성된 곳에 새로운 데이터가 들어오게 되거나 변경이 되면 기존에 만들어진 모든 Map을 다 고쳐야 함<br>
SELECT만 하는 테이블은 모든 컬럼에 인덱스 생성 해도 되지만 DML(INSERT, UPDATE, DELETE)이 발생하는 테이블은 인덱스를 최소한으로 작게 만들어야 함 <br>
인덱스가 많을 경우 DML에 악영향을 주기 때문에 사용하지 않는 인덱스(정말 사용하지 않는 인덱스인지 확인이 반드시 )는 삭제를 해주는 것이 좋음 <br>
오라클 11g에서는 인덱스를 실제 삭제하기 전에 "사용 안함" 상태로 만들어서 테스트 해볼 수 있는 기능을 제공하는데 이것이 Invisible Index(인비저블 인덱스)임 <br>
출처 : 오라클 SQL과 PL/SQL 책 <br>

### 인덱스 조회
~~~
-- 특정 사용자가 생성한 인덱스 조회
SELECT table_name, COLUMN_NAME, index_name
		FROM USER_IND_COLUMNS;
SELECT table_name, INDEX_NAME 
		FROM user_indexes
;
~~~
~~~
-- 데이터베이스 전체에 생성된 인덱스 내역 조회
SELECT table_name, index_name 
	FROM DBA_IND_COLUMNS 
;
SELECT table_name, index_name 
	FROM DBA_INDEXES 
;
~~~
인덱스는 한번 만들어 놓으면 영구적으로 잘 작동하는것이 아니라 생성 후에도 꾸준히 관리를 해 줘야 좋은 성능을 기대할 수 있음 <br>

### 프로시저
1부터 10000까지 숫자를 입력하는 간단한 프로시저
~~~
BEGIN
	FOR i IN 1..10000 LOOP
		INSERT INTO test_table values(i);
	END LOOP;
	COMMIT;
END;
~~~
