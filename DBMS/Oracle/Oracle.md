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

## 오라클 db 전체 테이블 갯수, 컬럼 갯수 추출
~~~
-- 오라클 테이블 별 컬럼 갯수
select owner, table_name, count(column_name)
from ALL_TAB_COLUMNS(관리자 계정일 경우만)
where owner = 'dbuser명'
group by owner, table_name
order by owner
;
-- 현재 사용자 테이블 갯수
SELECT count(1) FROM ALL_TABLES(관리자 계정일 경우만) 
  WHERE owner = 'dbuser명'
;
~~~

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
<strong>이미지 & 내용 출처 : 오라클 SQL과 PL/SQL 책</strong> <br>

인덱스는 WHERE절에 오는 조건 컬럼이나 조인 컬럼 등에 만들어야 함(특별한 경우에는 SELECT에 있는 컬럼에 생성하기도 함) <br>
만약 pay컬럼으로 인덱스를 생성 했는데 SQL문에서 WHERE pay + 1000 = 2000이라는 조건으로 조회를 하면 pay컬럼의 인덱스는 사용할 수 없게 됨 <br>
이런 현상을 INDEX Suppressing Error이라고 부름 <br>
인덱스는 잘 생성해 놓고 SQL을 잘못 작성해서 인덱스를 사용할 수 없는 경우를 뜻함 <br>
위 경우 말고 인덱스가 설정 되어 있는 컬럼 조회 시 Not을 사용하는 경우도 마찬가지임 <br>
<strong>※ 인덱스 사용 시 WHERE절의 조건을 절대로 다른 형태로 가공해서 사용하면 안됨</strong> <br>

그런데 업무상 반드시 WHERE pay+1000=2000이라는 형태로 써야 되고 인덱스도 반드시 써야 된다면 인덱스 생성 시 저 형태로(pay+1000) 인덱스를 생성하면 되고 이런 형태의 인덱스를 함수 기반 인덱스(FBI)라고 부름, 이는 임시방편이고 근본적인 해결책은 아니기에 아주 조심해야 함 <br>
<strong>출처 : 오라클 SQL과 PL/SQL 책</strong> <br>

B-TREE 형식의 인덱스는 주로 데이터의 값의 종류가 많고 중복 데이터가 적을 경우 사용하는 인덱스임 <br>
반대로 데이터 값의 종류가 적고 동일한 데이터가 많을 경우에는 주로 BITMAP 인덱스를 사용함 <br>
그리고 BITMAP INDEX를 생성하려면 데이터의 변경량이 적거나 없어야 함, 만약 BITMAP INDEX가 생성된 곳에 새로운 데이터가 들어오게 되거나 변경이 되면 기존에 만들어진 모든 Map을 다 고쳐야 함<br>
SELECT만 하는 테이블은 모든 컬럼에 인덱스 생성 해도 되지만 DML(INSERT, UPDATE, DELETE)이 발생하는 테이블은 인덱스를 최소한으로 작게 만들어야 함 <br>
인덱스가 많을 경우 DML에 악영향을 주기 때문에 사용하지 않는 인덱스(정말 사용하지 않는 인덱스인지 확인이 반드시 )는 삭제를 해주는 것이 좋음 <br>
오라클 11g에서는 인덱스를 실제 삭제하기 전에 "사용 안함" 상태로 만들어서 테스트 해볼 수 있는 기능을 제공하는데 이것이 Invisible Index(인비저블 인덱스)임 <br>
<strong>출처 : 오라클 SQL과 PL/SQL 책</strong> <br>

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

### view
view는 가상의 테이블을 의미, view는 데이터는 없고 원본 테이블에 가서 데이터를 불러오는 SQL query만 저장되어 있음 <br>
사용자가 해당 view를 사용하는 SQL를 실행할 때만 view에 들어 있는 쿼리가 실행됨 <br>
~~~
-- 간단한 view 생성
CREATE OR REPLACE VIEW v_emp1
AS SELECT empno, ename, hiredate
   FROM emp;   
-- view 조회 시 아래처럼 결과가 표시됨(emp테이블에서 데이터를 조회해 )
SELECT * FROM v_emp1;
~~~
<img src="https://user-images.githubusercontent.com/44331989/113467110-d330a180-947b-11eb-833e-b8a20413edac.png"> <br>
view를 생성하면 오라클은 해당 view 정보를 딕셔너리에 저장한 다음 사용자가 view를 사용할 때만 view의 서브쿼리가 실행되어 원본 테이블에서 데이터를 가져옴 <br>
<strong>※ 이 의미는 평소에 이 view에는 데이터가 없다는 뜻이기도 함</strong> <br>
view에는 제약조건이나 인덱스 등을 생성할 수가 없음 <br>

~~~
-- emp table과 dept 테이블을 조회하여 사원이름과 부서 이름을 출력하는 view생성(부서번호로 조인) 
CREATE OR REPLACE VIEW v_emp
AS
	SELECT e.ENAME, d.DNAME 
	FROM emp e, dept d
	WHERE e.DEPTNO = d.DEPTNO
;
~~~
매번 위와 같은 복잡한 서브쿼리를 생성해서 조회하기가 귀찮고 힘들 때 view를 생성 해 놓고 간단하게 조회할 수 있다는 것이 view의 큰 편리함임 <br>
그러나 view를 잘못 사용할 경우 성능 저하의 주 원인이 되는 경우도 많음 <br>
<strong>view안에는 가급적 INTERSECT, MINUS, UNION 같은 집합 연산자는 사용하지 않는 것이 성능 향상에 도움이 된다는 점도 기억할 것</strong> <br>

#### Inline View(인라인 뷰)
view는 필요시 생성해 놓고 다른 쿼리에서 여러번 반복해서 재사용 할수 있지만 다른 쿼리에서 사용하지 않고 해당 SQL에서만 필요한 view일 경우 view를 생성하지 않고 SQL 문장의 FROM 절에 view의 서브쿼리를 바로 적어서 사용가능하고 이런 view를 Inline View라고 함<br>
<img src="https://user-images.githubusercontent.com/44331989/113468988-dc723c00-9484-11eb-9f7e-43f92f15d147.png"> <br>
위의 쿼리에서 2번 째 줄의 FROM절 뒤에 있는(부분부터 4번 줄의) 사이에 있는 쿼리를 Inline view라고 함
인라인 뷰 부분 먼저 실행하면 아래와 같은 결과가 나옴 <br>
<img src="https://user-images.githubusercontent.com/44331989/113469078-936eb780-9485-11eb-94e0-5b5391e9f06a.png"> <br>
중요한 건 FROM절에 있는 Inline View 쿼리를 수행해서 나온 테이블(e)은 원래 존재하지 않지만 우리가 원하는 결과를 만들기 위해서 메모리에 임시로 생성했다는 점임, 즉 Inline View를 이용해서 원하는 형태의 테이블을 먼저 만드는 것이 가장 중요함 <br>
<strong>출처 : 오라클 SQL과 PL/SQL 책</strong> <br>

#### view 조회
<img src="https://user-images.githubusercontent.com/44331989/113469214-e301b300-9486-11eb-9be7-b0b4f2d00db1.png"> <br>
위의 예에서는 scott 사용자가 생성한 view를 조회 했기 때문에 USER_VIEWS를 조회 했지만, 모든 view를 조회할 경우 DBA_VIEWS로 조회(DBA 권한 필요) <br>

#### Materialized View(Mview) 구체화된 뷰
view는 일반적으로 데이터는 없고 서브 쿼리만 가지고 있음, 그래서 사용자가 view를 조회할 때 해당 쿼리문을 실행 후 원본 테이블에서 데이터를 가져온 후 사용자에게 반환 한 다음
데이터를 삭제하는데 이 특성이 대용량 view일 경우에는 성능상 문제가 많이 됨 <br>
Mview는 사용자가 요청하는 데이터를 가지고 있다가 요청이 들어오면 사용자에게 보내 줌 <br>
사용자가 많고 데이터가 많을 수록 Mview를 사용하는 것이 일반 view를 사용하는 것보다 아주 효율적이고 성능도 좋게 됨 <br>
<strong>※ 이 방식의 문제는 원본 테이블과 MView간의 데이터 동기화임</strong> <br>
Mview를 생성하기 위해서는 Query Rewrite라는 권한과 Create Materialized view라는 권한이 있어야만 함(스펠링 특히 주의) <br>

다음과 같은 방법으로 Mview를 생성하면 됨(오라클 엔터프라이즈 버전만 지원) <br>
~~~
-- 관리자 계정으로 권한 부여
GRANT query rewrite TO scott;
GRANT CREATE MATERIALIZED VIEW TO scott;
-- Mview 생성
CREATE MATERIALIZED VIEW m_prof
build IMMEDIATE -- Mview를 생성하면서 즉시 서브 쿼리를 실행해서 데이터를 가져오라는 소리
refresh	        
ON demand       -- 사용자가 수동으로 동기화 명령을 수행
complete        -- Mview 내의 데이터 전체가 원본 테이블과 동기화되는 방법(ATOMIC_REFRESH=TRUE와 COMPLETE로 설정필요)
enable query rewrite
AS 
	SELECT profno, name, pay
	FROM professor
;
~~~
<strong>※ 오라클 엔터프라이즈 버전만 사용가능해서 테스트는 못해 봄</strong> <br>

### 서브 쿼리
서브쿼리는 하나의 메인 쿼리안에 또 하나의 쿼리가 담겨 있는 것을 의미 <br>
문법 
~~~
SELECT select_list
FROM TABLE 또는 view
WHERE 조건 연산자( SELECT select_list FROM TABLE WHERE 조건 );
~~~ 
※ 서브 쿼리 작성 시 주의사항 <br>
서브쿼리 부분은 WHERE절의 연산자 오른쪽에 위치해야 하며 반드시 괄호로 묶어야 함 <br>
특별한 경우(Top-n 분석 등)를 제외하고는 서브 쿼리절에 Order by절이 올 수 없음 <br>
단일행 서브쿼리(일반적으로 사용되는 유형이고 서브 쿼리 수행결과가 1건만 나오는 경우)와 다중행 서브쿼리에 따라 연산자를 잘 선택해야 함 <br>
~~~
-- 단일행 서브쿼리일 경우 WHERE절에서 사용되는 연산자 
= : 같다.
<> : 같지 않다.
> : 크다.
< : 작다.
<= : 작거나 같다.
~~~

<img src="https://user-images.githubusercontent.com/44331989/114257365-7fcdce80-99fa-11eb-997b-603d34e8adee.png"> <br>

<img src="https://user-images.githubusercontent.com/44331989/114385095-857a0e80-9bca-11eb-8c12-24cba42681dc.png"> <br>

### 다중행 서브 쿼리
다중행 서브쿼리란 서브 쿼리의 결과가 2건 이상 출력되는 것을 의미하고 다중행 서브 쿼리에서는 단일행 연산자를 사용할 수 없음 <br>
~~~
-- 다중행 서브 쿼리의 WHERE절에서 사용되는 연산자
IN : 서브 쿼리 결과와 같은 값을 찾음
EXISTS : 서크 쿼리의 값이 있을 경우 메인 쿼리를 수행
>ANY : 서브 쿼리 결과 중 최솟값을 반환 
<ANY : 서브 쿼리 결과 중 최댓값을 반환 
<ALL : 서브 쿼리 결과 중 최솟값을 반환 
>ALL : 서브 쿼리 결과 중 최댓값을 반환 
~~~
위의 ANY와 ALL은 연산자의 방향에 따라 최댓값, 최솟값이 달라짐 <br>
서브쿼리에서 반환되는 값은 최솟값이거나 최댓값이지만 연산자 좌측에 어떤 값이 오는가에 따라서 출력되는 결괏값은 다르게 나옴 <br>
<strong>출처 : 오라클 SQL과 PL/SQL 책</strong> <br>

#### Exists, IN 연산자
EXISTS 연산자와 IN 연산자는 비슷한 듯 하지만 아주 다름 <br>
EXISTS 연산자는 서브 쿼리의 결과가 있으면 그 결과와 관계없이 메인쿼리를 수행하지만 IN 연산자는 서브 쿼리의 결과가 있으면 그 결과에 해당되는 메인 쿼리를 수행 <br>
이 두가지를 잘 구분해서 적절하게 사용해야 하며 특히 EXISTS는 실무에서 어떤 데이터가 있는지 없는지 체크해서 쿼리를 실행할 때 자주 사용되는 함수이므로 꼭 이해하고 있어야 함 <br>

<img src="https://user-images.githubusercontent.com/44331989/114650572-722b8800-9d1d-11eb-9d95-f3368befc29c.png"> <br>
<img src="https://user-images.githubusercontent.com/44331989/114650667-9ab38200-9d1d-11eb-9641-abdd933b0cba.png"> <br>

#### 다중 컬럼 서브쿼리
다중 컬럼 서브 쿼리는 서브 쿼리의 결과가 여러 컬럼인 경우를 말함 <br>
주로 pk를 여러 컬럼으로 합쳐서 만들었을 경우() 한꺼번에 비교하기 위해서 자주 사용<br>
<img src="https://user-images.githubusercontent.com/44331989/114652682-588c3f80-9d21-11eb-9beb-fe639e351b22.png"> <br>
위를 보면 서브 쿼리 부분에서 학년별로 최대 몸무게를 구한 다음 한행 씩 메인 쿼리로 넘겨줘서 메인쿼리를 수행한 후 그 조건에 맞는 행을 출력한 것임<br>
<strong>여기서 중요한 점은 서브 쿼리에서 두 개의 컬럼을 동시에 메인 쿼리로 넘겨서 비교한 다는 것</strong> <br>

### SELECT 결과값 별칭을 사용해서 테이블처럼 질의
현업에서 아래처럼 사용하는 경우가 많음 <br>
학생 테이블에서 학생번호, 이름, 아이디, 주민번호, 생년월일을 조회하는 쿼리 결과값을 s라는 별칭을 줘서 하나의 테이블이 되는것임 <br>
바깥쪽 메인쿼리에서는 안쪽 결과를 테이블처럼 사용 <br>

<img src="https://user-images.githubusercontent.com/44331989/114260315-f83e8a80-9a0e-11eb-8604-63deee19d320.png"> <br>

## 스칼라 서브 쿼리
스칼라 서브 쿼리는 SELECT절에 오는 서브 쿼리로 한번에 결과를 1행씩 반환함 <br>
emp2 테이블과 dept2 테이블을 조회하여 사원들의 이름과 부서 이름을 출력하는 예제 <br>
![image](https://user-images.githubusercontent.com/44331989/116868507-d91bcd00-ac49-11eb-8a88-3a82005ea959.png) <br>
원래 위와 같은 결과를 출력하려면 join 등의 방법을 이용해야 하지만 스칼라 서브쿼리를 이용해서 조회 할 수 있음 <br>
스칼라 서브 쿼리는 서브 쿼리의 결과가 없을 경우 Null 값을 return 함 <br>
그래서 스칼라 서브 쿼리는 Outer join과 동일함 <br>
조회하려는 데이터의 양이 적을 경우 스칼라 서브쿼리를 사용하고, 데이터의 양이 많을 경우는 Join을 사용하는 걸 권장 <br>

### 스칼라 서브쿼리의 작동 원리
1. 메인 쿼리를 수행한 후 스칼라 서브쿼리에 필요한 값을 제공 <br>
2. 스칼라 서브쿼리를 수행하기 위해 필요한 데이터가 들어 있는 블록을 메모리로 로딩 <br>
3. 메인 쿼리에서 주어진 주건을 가지고 필요한 값을 찾고 이 결과를 입력값과 출력값으로 메모리 내의 query execution cache라는 곳에 저장 해 둠(여기서 입력값은 메인쿼리에서 주어진 값이고 출력값은 스칼라 서브쿼리를 수행 후 나온 결과값임) <br>
4. 다음 조건이 메인 쿼리에서 스칼라 서브 쿼리로 들어오면 해시 함수를 이용해서 해당 값이 캐시에 존재하는지 찾고 있으면 즉시 결괏값을 출력, 없으면 다시 블록을 엑세스 해서 해당 값을 찾은 후 다시 메모리에 캐시해 둠, 이 작업을 메인 쿼리가 끝날 때까지 반복 <br>
※ 위의 순서에서 알 수 있듯이 스칼라 서브 쿼리가 빠른 이유는 찾는 데이터가 메모리에 있는 값을 참조하기 때문임 <br>
만약 모든 데이터가 메모리에 없거나 또는 데이터양이 많을 경우에는 Join이 더 빠름 <br>
스칼라 서브 쿼리의 결과가 1개가 아닐 경우 "단일행 하위 질의에 2개 이상의 행이 리턴되었습니다." 에러 발생 함 <br>
스칼라 서브 쿼리에서 2개 이상의 컬럼을 조회할 경우에도 "ORA-00913 : 값의 수가 너무 많습니다." 에러 발생 함 <br>

### WITH절을 활용한 서브 쿼리
오라클 9i 버전부터 지원됨, WITH절을 사용하여 원하는 테이블을 메모리에 미리 뷰처럼 가상의 테이블로 생성 후 데이터를 가져오는 기법 <br>
성능이 좋아서 현업에서 아주 많이 사용되지만 사용법이 어렵다는 단점이 있음 <br>
※ with 절 안에는 SELECT 문장만 쓸 수 있음, WITH 절 안에 또 다른 WITH 절을 쓸 수 없음 <br>
~~~
기본 문법
- 단일 가상 테이블 생성
WITH a AS
(
  SELECT query..
)
SELECT * FROM a
;

- 다중 가상 테이블 생성
WITH a AS
(
  SELECT query..
),
b AS
(
  SELECT query..
) 
SELECT * FROM a
UNION ALL
SELECT * FROM b
;
~~~

### 스칼라 서브쿼리의 NULL 처리
스칼라 서브 쿼리에서 조인 후 해당 값이 없을 때, 실제로 NULL이 발생하는 곳은 스칼라 서브 쿼리를 호출한 메인 쿼리의 컬럼부분임 <br>
그 부분에 NULL 처리를 해야 함 <br>

### 스칼라 서브 쿼리 성능을 빠르게 하는 방법
스칼라 서브쿼리는 메인 쿼리가 먼저 수행된 후 나온 결과 집합 수 만큼 건건이 스칼라 서브 쿼리를 호출함 <br>
만약 메인 쿼리에서 나온 결과 집합이 100건이라면 100번 스칼라 서브쿼리를 호출함 <br>
만일 스칼라 서브 쿼리에 조인되는 컬럼에 인덱스가 존재하지 않으면 굉장히 비효율적, 100번 호출 할 때 마다 해당 테이블을 처음부터 끝까지 다 검색 하게 됨<FTS> <br>
그래서 조인 컬럼에 반드시 인덱스가 존재해야 함 <br>

### 시퀀스
시퀀스 개념은 생략 <br>
아래의 조건으로 제품 주문번호를 생성하기 위해 사용할 시퀀스 예제
~~~
-- 시퀀스 명 : dev_seq
-- 시작 번호 : 100
-- 끝 번호 : 110
- 증가값 : 1
-- 반복되고 캐싱은 2개씩 되도록 할 것(반복될 때 다시 시작되는 값은 90으로 할 것)
CREATE SEQUENCE dev_seq
INCREMENT BY 1
START WITH 100
MAXVALUE 110
MINVALUE 90
CYCLE : CYCLE 옵션 넣을 경우 MAXVALUE 값을 다 사용하게 되면 MINVALUE로 설정된 값으로 다시 돌아가서 시작됨,
        만일 CYCLE 옵션 지정하지 않을 경우 기본값은 NOCYCLE인데 이 경우 값을 초과한 요청이 올 경우 ORA-08004 : sequence SEQ_NAME.NEXTVAL exceeds MAXVALUE and cannot be instantiated와 같은 에러 발생
CACHE 2
;
~~~
위 시퀀스 생성 후 아래처럼 NEXTVAL 함수로 데이터를 넣으면 아래처럼 결과가 표시됨 <br>
~~~
INSERT INTO s_order
VALUES(dev_seq.NEXTVAL, 'James', 'apple', 5);
INSERT INTO s_order
VALUES(dev_seq.NEXTVAL, '홍길동', '포도', 5000);
~~~
![image](https://user-images.githubusercontent.com/44331989/121270236-47fcdd80-c8fc-11eb-8c92-031eb959c5d1.png)

### 시퀀스 초기화하기
일반적으로 시퀀스를 초기화하는 경우도 드물지만 정말 업무상 반드시 초기화를 해야 할 경우도 생김 <br>
이 경우에는 아래와 같이 프로시저를 생성해서 수행하면 됨 <br>
~~~
CREATE OR REPLACE PROCEDURE re_seq
(
  SNAME IN VACHAR2
)
IS
  VAL NUMBER
BEGIN
  EXCUTE IMMEDIATE `SELECT ` || SNAME || `.NEXTVAL FROM DUAL `INTO VAL;
  EXCUTE IMMEDIATE `ALTER SEQUENCE ` || SNAME || ` INCREMENT BY -` || VAL || ` MINVALUE 0`;
  EXCUTE IMMEDIATE `SELECT ` || SNAME || `.NEXTVAL FROM DUAL ` INTO VAL;
  EXCUTE IMMEDIATE `ALTER SEQUENCE ` || SNAME || `INCREMENT BY 1 MINVALUE 0`; 
END  
~~~

### 시퀀스 조회 및 수정
~~~
-- 조회
SELECT SEQUENCE_NAME, MIN_VALUE, MAX_VALUE, INCREMENT_BY, CYCLE_FLAG, ORDER_FLAG, CACHE_SIZE, LAST_NUMBER
FROM USER_SEQUENCES 
WHERE SEQUENCE_name='SEQ_TEST'

-- 수정
ALTER SQUENCE seq_test
MAXVALUE 120
CACHE 10;
-- ※ START WITH 값은 변경 불가
~~~

### 시퀀스 삭제하기
~~~
DROP SEQUENCE '시퀀스명';
~~~

### SYNONYM(시노님-동의어)
사람도 친구의 별명을 부르듯 오라클에서도 테이블에 별명을 붙일 수 가 있는데 이 별명을 붙이는 기능을 시노님이라고 함 <br>
시노님을 사용하는 목적은 보안이나 사용자의 편리성 때문임 <br>
~~~
-- 생성 문법
CREATE [PUBLIC] SYNONYM synonym_name
FOR [schema.] 대상객체;
~~~
종류는 Private Synonym과 public Synonym이 있고 private synonym은 만든 사용자만 사용할 수 있는 synonym임 <br>
대부분은 synonym은 프로젝트에 참여한 많은 사람들이 다 볼 수 있도록 만드는 것이 좋은데 이 때 사용하는게 public synonym임 <br>
synonym을 생성하려면 CREATE [PUBLIC] SYNONYM이란 권한이 필요하므로 아래와 같이 권한을 먼저 할당 해야 함 <br>
~~~
conn / as sysdba로 sysdba 권한으로 접속(터미널로 안하고 db툴에서 system계정으로 부여해도 될 듯)
create synonym TO scott;
create public synonym TO scott;
~~~

#### 시노님 조회
user_synonyms에서 조회하면 됨 <br>

#### 시노님 삭제
~~~
drop synonym "synonym명";
~~~

## 12c SQL에 추가된 새로운 기능
### DEFALUT VALUE로 sequence의 next value 지정 가능
12c 이전의 오라클에서는 자동 증가값을 설정하기 위해 시퀀스를 생성 한 후 테이블을 만들 때부터 생성한 시퀀스의 값을 설정할 수 있음 <br>
~~~
-- ex) 12c에서는 아래처럼 시퀀스를 생성 후 
CREATE SEQUENCE t_seq
start with 1
increment by 1
maxvalue 10
nocycle
;

-- 테이블을 만들 때 시퀀스의 next value 지정이 가능함
CREATE TALBE test
( no NUMBER DEFAULT t_seq.nextval PRIMARY KEY,
  name varchar2(10)
);
~~~

### invisible columne 사용 가능
11g까지는 테이블에 invisible columne이 생성되지 않았지만 12c부터는 생성 가능함 <br>
invisible된 컬럼은 안보이지만 제약조건도 적용됨<br>

### 순위 뽑을 때 Top-N 기능 사용 가능
11g까지는 인라인뷰를 사용하거나 rownum등을 사용해서 비교적 복잡한(?) 방법을 사용했었지만 12c부터는 편하게 Top-N으로 가능해 졌음 <br>
(11g 사용중이라 테스트는 못 해 봤음) <br>

### IDENTITY Column 지원
기존의 ANSI SQL에서는 테이블에서 primary key와 같은 기능을 사용하기 위해 자동증가되는 identity columne을 지원했으나 오라클은 없었음 <br>
그래서 시퀀스 등을 이용해서 수동으로 설정 했었는데 12c부터 오라클에서도 이 기능을 지원하게 되었음 <br>
mysql의 auto-increment와 비슷한 기능으로 이해 됨 <br>

~~~
-- 문법은 아래와 같음
CREATE TABLE t_iden
( no NUMBER GENERATED AS IDENTITY, -- 이 부분이 자동증가
  name varchar2(10)
);
~~~

### Null 값 위한 DEFAULT 값 지정 가능
12c에서는 null이 입력될 경우 null 대신 입력될 값을 default로 지정하는 기능이 추가 됨 <br>
~~~
-- 문법은 아래와 같음
CREATE TABLE d_test100
( no NUMBER,
  name varchar2(10),
  sal NUMBER DEFAULT on null 100 -- null일 경우 100을 입력
);
~~~

## Oracle PL/SQL
PL/SQL은 오라클에서 제공하는 프로그래밍 언어임, 기존 SQL은 DB의 데이터를 조회하거나 조작할 때 여러 불편함이 많지만,
SQL과 PL/SQL을 함께 활용하면 효과적으로 DB에 접근할 수 있음 <br>
PL/SQL이란 Procedural Language/SQL의 약자로 절차적인 기능을 기본적으로 가지는 프로그래밍 언어임 <br>
PL/SQL은 일반 프로그래밍 언어적인 요소를 거의 다 가지고 있어서 실무에서 요구되는 절차적인 데이터 처리가 모두 가능하고 <br>
SQL과 결합해 기존 언어보다 더 강력하게 DB 관련 작업들을 할 수 있음 <br>

### PL/SQL 기본구조
PL/SQL은 기본적으로 블록(BLOCK) 구조이고
선언부(DECLARE), 실행부(BEGIN), 예외 처리부(EXCEPTION)로 구성되어 있음 <br>
PL/SQL 블록은 블록 안에 블록을 포함할 수 있는데, 포함된 블록을 Nested Block(중첩 블록)이라고 부르기도 함 <br>
블록의 유형에는 Anonymous PL/SQL Block(익명 블록)과 Stored PL/SQL Block(저장된 블록)이 있음 <br>
익명 블록은 주로 일회성에 많이 사용되고, 저장된 블록은 서버에 파싱해서 저장 한 뒤 주기적으로 반복해서 사용할 때 많이 사용 <br>
때에 따라 선언부와 예외처리부는 생략도 가능함 <br>











