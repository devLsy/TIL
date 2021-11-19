# Oracle 관련 TIL
## 재귀 복사
개발시 테스트를 위해 더미 데이터를 많이 집어넣을 때 사용하면 유용함<br>
<strong>(ex)INSERT INTO tbl_board (bno, title, content, writer)<br>
    (SELECT SEQ_BOARD.nextval, title, content, writer FROM tbl_board);</strong><br>
위의 경우는 tbl_board 테이블의 데이터 수만큼 insert를 진행하는 예제 쿼리문임<br>
반복할 경우 2배씩 증가함<br>

## Hint![image](https://user-images.githubusercontent.com/44331989/138861539-681f6a84-5eb9-4b12-b542-175e60aa115f.png)

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

### 오라클 컬럼 코멘트 조회
![image](https://user-images.githubusercontent.com/44331989/126446814-afec158a-2e2d-4bfa-a217-bf9e5bc08544.png)
image source : https://gent.tistory.com/206 <br>

### 오라클 컬럼으로 테이블 조회
![image](https://user-images.githubusercontent.com/44331989/132607146-99a8fdba-d9a1-47de-9acb-a38e4f8c1e82.png)

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

## 오라클 테이블 명세서 추출 쿼리(PK포함)
~~~
WITH LIST AS
(
    SELECT A.TABLE_NAME,
           A.COLUMN_NAME,
           A.DATA_TYPE,
           A.DATA_LENGTH,
           A.NULLABLE,
           B.COMMENTS
    FROM   dba_tab_columns A,
           all_col_comments B
    WHERE  A.OWNER = B.OWNER
    AND    A.TABLE_NAME = B.TABLE_NAME
    AND    A.COLUMN_NAME = B.COLUMN_NAME
    AND    A.OWNER = ''   -- DB명
),
PKLIST AS
(
    SELECT C.TABLE_NAME,
           C.COLUMN_NAME,
           C.POSITION
    FROM USER_CONS_COLUMNS C,
         USER_CONSTRAINTS S
    WHERE C.CONSTRAINT_NAME = S.CONSTRAINT_NAME
    AND S.CONSTRAINT_TYPE = 'P'
)
SELECT L.TABLE_NAME AS "테이블명",
       L.COLUMN_NAME AS "컬럼명",
       L.DATA_TYPE AS "데이터타입",
       L.DATA_LENGTH AS "길이",
       CASE WHEN P.POSITION < 99 THEN 'Y'
            ELSE ' '
       END AS "PK",
       L.NULLABLE AS "Null 여부",
       L.COMMENTS AS "Comments"
FROM LIST L,
     PKLIST P
WHERE L.TABLE_NAME = P.TABLE_NAME(+)
  AND L.COLUMN_NAME = P.COLUMN_NAME(+)
 ORDER BY L.TABLE_NAME,
          NVL(P.POSITION, 99)
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

### having
![image](https://user-images.githubusercontent.com/44331989/138797041-229151cb-c055-46bb-91b9-2b542c73ecf6.png) <br>
image source : http://www.gurubee.net/lecture/1032 <br>

![image](https://user-images.githubusercontent.com/44331989/139014099-f59f2356-8d44-4d17-95e7-a22dd06e6e21.png) <br>
일단 부서별 평균급여를 구한 다음 이 구한 평균값을 다시 조건을 걸 때 having을 사용 <br>

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
-- 아래는 professor테이블에서 교수번호가 3000번에서 3999인 교수들의 교수번호와 이름을 prof_3, prof_4테이블에 동시에 입력하는 쿼리
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
~~~
SELECT col1, (SELECT ...)     -- 스칼라 서브쿼리(Scalar Sub Query): 하나의 컬럼처럼 사용 (표현 용도)
FROM (SELECT ...)             -- 인라인 뷰(Inline View): 하나의 테이블처럼 사용 (테이블 대체 용도)
WHERE col = (SELECT ...)    -- 일반 서브쿼리: 하나의 변수(상수)처럼 사용 (서브쿼리의 결과에 따라 달라지는 조건절)
출처: https://data-make.tistory.com/25 [Data Makes Our Future]
~~~

![image](https://user-images.githubusercontent.com/44331989/138833629-12dce6c6-d307-4935-8d26-97a90bcbc283.png) <br>

스칼라 서브쿼리 <br>
![image](https://user-images.githubusercontent.com/44331989/138833653-bd1491e6-dc5b-46dc-8159-d8a2e44e1830.png) <br>
스칼라 서브쿼리 예시 <br>
![image](https://user-images.githubusercontent.com/44331989/138982600-96a70b69-9fbe-45e3-8dd9-22ac21308093.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138981120-117f0d95-764f-4e06-ab9b-a00c8d1dd358.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138975159-233d63f4-68a0-4082-946f-07187f2ac64d.png) <br>

인라인뷰 서브쿼리 <br>
![image](https://user-images.githubusercontent.com/44331989/138833687-45c29fd4-5b38-4aae-b93f-8662c0a28576.png) <br>

인라인뷰 서브쿼리 예시
![image](https://user-images.githubusercontent.com/44331989/138861855-752f571b-b04f-4d57-9c76-a7efcece8979.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138861882-3c65ae7d-a654-447c-a4b1-528650f99404.png) <br>

일반 서브쿼리
![image](https://user-images.githubusercontent.com/44331989/138833720-36db0915-436f-4de7-93a2-1f8061670d5f.png) <br>
일반 서브쿼리 예시 -> 'ALLEN'의 급여를 emp테이블에서 조회한 다음 급여가 'ALLEN'보다 높은 사람정보 출력 <br>
![image](https://user-images.githubusercontent.com/44331989/138982349-d0b6f9fe-24c3-4553-90f6-46d18320587b.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138982302-e9ebca70-79e0-4566-b771-30c085e5fc2d.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138975195-f2dcb2dc-0c5c-40cf-94b1-7b336ba023a2.png) <br>

단일행 서브쿼리
![image](https://user-images.githubusercontent.com/44331989/138833747-faa11784-0eb2-4ea4-8d78-2b015d3e8bb8.png) <br>

다중행 서브쿼리
![image](https://user-images.githubusercontent.com/44331989/138833780-801a1012-2a03-4010-a950-263d6c80e663.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138833801-887afe43-7765-44d1-8430-3e022b4f8d11.png) <br>

image source : https://mjn5027.tistory.com/51 <br>

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

![image](https://user-images.githubusercontent.com/44331989/139253911-ef04322c-ca6e-4bd1-adbc-5db7760a7a7a.png) <br>

![image](https://user-images.githubusercontent.com/44331989/139254326-be00614e-1e59-4155-98ef-6dbf49832eee.png) <br>
![image](https://user-images.githubusercontent.com/44331989/139254654-7818d22c-b10f-4d94-9bc8-33c0f8878bea.png) <br>


image source : https://gent.tistory.com/278

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
테이블을 임시로 만드는 점은 VIEW와 쓰임이 비슷하지만 VIEW는 DROP하기 전까지는 없어지지 않지만 WITH절은 한번 실행할 쿼리문안에서만 실행됨 <br>
오라클 공유 메모리에 임시 테이블을 생성하여 반복 재사용이 가능하도록 해줌 <br>
WITH절 사용 시 동일 테이블 접근을 최소화 하여 메모리에 생성된 임시 테이블에서 필요한 데이터를 메모리로 접근하기에 디스크 IO보다 성능 개선의 이점이 있음 <br>
그리고 SQL도 가독성 있게 바꿀 수 있음 <br>
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
![image](https://user-images.githubusercontent.com/44331989/125763151-74ddc675-9cf2-411c-9fbf-b80d9e39ac43.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125757918-83f12f72-949f-458d-a3af-4aa0c192a67e.png)
image source : https://coding-factory.tistory.com/445 <br>

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
블록 내의 각 부분에 포함되는 명령들 중 DECLARE, BEGIN, EXCEPTION과 같은 예약어들은 ;(세미콜론)으로 끝나지 않지만 <br>
나머지 명령어들은 SQL 문장처럼 ;으로 끝이 남 <br>
PL/SQL 블록 내에서 DML(CRUD)도 처리 할 수 있음 <br>
※ PL/SQL은 기본적으로 처리된 PL/SQL의 문장의 결과를 화면에 출력하지 않음, 아래처럼 활성화 필요<br>
~~~
SET SERVEROUTPUT ON;
~~~

### PL/SQL 블록 작성 시 기본규칙과 권장사항
![image](https://user-images.githubusercontent.com/44331989/121443539-a4c5ca00-c9c8-11eb-9844-ef377dba2c0a.png) <br>

### PL/SQL 문 내에서의 SQL 문장 사용 하기
![image](https://user-images.githubusercontent.com/44331989/121443617-c7f07980-c9c8-11eb-8a6e-fff43072d505.png) <br>
![image](https://user-images.githubusercontent.com/44331989/121444054-b065c080-c9c9-11eb-9a98-5f9e15f574a2.png) <br>
![image](https://user-images.githubusercontent.com/44331989/121444476-911b6300-c9ca-11eb-8743-133d7d4c585b.png) <br>

## Oracle Package(패키지)
서브 프로그램 단위의 하나인 패키지는 특정 처리를 위해 관련된 PL/SQL 블록들이 논리적으로 하나의 그룹을 이루는 특수한 형태 <br>
이 때 패키지를 구성하는 PL/SQL 블록들은 프로시저 또는 함수가 될 수 있으며, 그 외에도 오라클에서 제공하는 PL/SQL 데이터 유형, 복합 유형 등이 포함됨 <br>
<strong>※ 패키지는 연관성이 높은 함수나 프로시저를 하나의 그룹으로 묶어두는 개념</strong>

패키지는 선언부(spec)와 몸체부(body)로 구성됨 <br>
선언부는 해당 패키지에 사용될 함수나 프로시저, 변수 등에 대한 정의를 선언하는 부분이고 몸체부는 선언부에서 선언된 함수나 프로시저 등이 실제로 구현되는 부분임 <br>
패키지 선언부에서 선언되지 않더라도 패키지 몸체에서 사용될 수는 있지만 권장사항은 아니니 선언부에서 선언 후 몸체에서 사용하길 권장함 <br>
<strong>※ 패키지 개발 시 선언부에 사용될 함수나 프로시저등을 먼저 생성한 뒤 패키지 몸체를 생성하는 것을 권장함 <br></strong>
만일 생성된 선언부가 변경되었다면 무조건 패키지 몸체는 다시 재생성해야 하고 참조하는 서브 프로그램들도 재번역(recompile)해야 함 <br>
반대로 몸체부만 변경되는 경우는 선언부와 다른 관련 서브 프로그램에 영향을 주지 않고 몸체부만 재 생성하면 됨 <br>

패키지 선언부 문법
~~~
CREATE [OR REPLACE] PACKAGE package_name
IS | AS
 public type and item declarations
 Subprogram specifications
END package_name;
~~~
OR REPLACE : 생성하려는 패키지가 존재할 경우 기존의 내용을 현재의 내용으로 수정하는 옵션, 이 옵션은 해당 패키지 body를 삭제한 후 다시 생성함 <br>
package_name : 생성하고자 하는 패키지명으로 스키마내에는 유일한 이름이어야 함, 패키지 선언부와 패키지 몸체부의 패키지명은 동일해야 함 <br>
public type and item declarations : 변수, 상수, 명시적 커서, 사용자 정의 예외, PRAGMA 등을 선언, 이들은 모두 public 임 <br>
Subprogram specifications : PL/SQL 서브 프로그램을 선언하는 부분, 선언할 때에는 형식 매개 변수를 포함한 헤더만을 기술 함 <br>
Subprogram bodies : 실제 작동할 서브 프로그램(프로시저, 함수 등)을 기록하는 부분임 <br>
주의해야 할 사항은 서브 프로그램의 순서인데 기본적으로 참조되는 변수든 서브 프로그램이든 참조하는 서브 프로그램보다는 먼저 정의 되어야 함 <br>
일반적으로 PUBLIC의 서브 프로그램은 마지막 부분에 정의함 <br>

패키지 몸통부 문법
~~~
CREATE [OR REPLACE] PACKAGE BODY package_name
IS | AS
 public type and item declarations
 Subprogram specifications
END package_name;
~~~

### 패키지 삭제
~~~
-- 패키지 전체 삭제
DROP PACKAGE package_name ;
-- 패키지 몸통 삭제
DROP PACKAGE BODY package_name ;
~~~

### 패키지 생성 예제
![image](https://user-images.githubusercontent.com/44331989/121463508-27f91700-c9ed-11eb-983c-da1f84b90f61.png)
![image](https://user-images.githubusercontent.com/44331989/121463517-2cbdcb00-c9ed-11eb-87ad-e81905fbebc8.png)

### as sysdba 방지
![image](https://user-images.githubusercontent.com/44331989/125397032-57ce7c80-e3e8-11eb-8606-612746e22942.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125397060-5f8e2100-e3e8-11eb-9e1b-7078ce14e929.png) <br>
image source : https://wookoa.tistory.com/235 <br>	
	
### Table Space
![image](https://user-images.githubusercontent.com/44331989/126739462-513af7c7-a892-48f9-a670-83cc15537f7d.png) <br>
![image](https://user-images.githubusercontent.com/44331989/126739485-cea23916-19b2-47b5-add1-f9c69515841a.png) <br>
![image](https://user-images.githubusercontent.com/44331989/126739526-ca51cb11-5402-4e21-9ca9-728a2c58a8fd.png) <br>
![image](https://user-images.githubusercontent.com/44331989/126739573-0d87e326-3d72-46b1-a6a2-311eb2efedda.png) <br>
	
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=ohmydata00&logNo=221262610912 <br>

### Oracle의 Schema 생성 절차
![image](https://user-images.githubusercontent.com/44331989/126739632-5733c392-c993-4b52-b05d-22a8220c6865.png) <br>

image source : https://yjh5369.tistory.com/entry/Oracle-Create-a-Schema-%EC%98%A4%EB%9D%BC%ED%81%B4-%EC%8A%A4%ED%82%A4%EB%A7%88-%EC%83%9D%EC%84%B1 <br>
	
### Oracle 내(현재) 계정 권한 조회
![image](https://user-images.githubusercontent.com/44331989/126740698-a06136be-ad77-4ccc-88b8-7d8cd6fc44b2.png) <br>
![image](https://user-images.githubusercontent.com/44331989/126741028-ad326247-8e21-4c87-bcee-cc838a75e7bb.png) <br>
image source : https://keichee.tistory.com/68 	
	
### Oracle DBF : data file 즉, 물리적인 저장 구조
![image](https://user-images.githubusercontent.com/44331989/126745847-fab6a461-ef59-41c2-a3ae-e583b0c1bde3.png) <br>
image source : https://blog.daum.net/pilgrimfortruth/2877	

### Oracle DB Architecture
![image](https://user-images.githubusercontent.com/44331989/126746018-3c6dcb31-258b-4609-b047-e0e31125beb9.png) <br>
image source : https://jeong-pro.tistory.com/147

### 구버전 오라클 설치 방법
https://yeonyeon.tistory.com/11	 <br>

### CentOS Oracle 설치
출처 : https://xxsiyoung.tistory.com/3 <br>
	
### CentOS 오라클 설치 후 charSet 변경
![image](https://user-images.githubusercontent.com/44331989/137291934-ed636786-3ae9-4da1-a1e8-dc1d4f172c14.png) <br>
image source : https://couplewith.tistory.com/entry/%EC%98%A4%EB%9D%BC%ED%81%B4-Charset-%EB%B3%80%EA%B2%BD-%ED%95%B4%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EC%99%80-%EB%B0%A9%EB%B2%95 <br>

### 오라클 db dump	
![image](https://user-images.githubusercontent.com/44331989/137300739-50196aac-c5e9-481a-993a-ca9c9fa2acc0.png) <br>
image source : https://tenlie10.tistory.com/35 <br>	
![image](https://user-images.githubusercontent.com/44331989/137446646-f7ef90b2-4cd0-4b0f-940c-9fc8862ef944.png) <br>
image source : https://fruitdev.tistory.com/40 <br>	

![image](https://user-images.githubusercontent.com/44331989/137501076-5d660500-4111-40d4-8d14-e10261b5511d.png) <br>
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sin160cm&logNo=220803132213 <br>

![image](https://user-images.githubusercontent.com/44331989/137504096-95c4b6a9-77b2-40ea-95ae-d1220381ab59.png) <br>	
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hi_mylover&logNo=50085628355 <br>	
토드같은 툴을 이용하는 방법도 있지만 난 아직까지 이 cli 방식이 더 잘되는 듯 싶다. <br>
핵심은 userid는 system으로 하고 parameter값으로 owner을 dump 뜨고 싶은 스키마명(test)으로 할 경우
system 계정으로 test 데이터베이스를 dump 뜨겠다는 소리, 
imp시에는 fromuser에 test를 넣고 touser에는 스키마명이 그대로 될 경우 똑같이 test를 넣으면 됨
그냥 cmd로 dump 뜨는게 테이블 스페이스, 인덱스, 시퀀스, 프로시저, 데이터 전부 dump가 되서 이게 더 편하다 나는 <br>
	
#### 테이블 스페이스 export & import
![image](https://user-images.githubusercontent.com/44331989/137460575-2a5b037f-5823-4bcc-8faa-9ae9b6d561a1.png) <br>
오라클은 테이블을 생성하기전에 테이블 스페이스를 먼저 생성해서 테이블이 들어갈 공간을 확보하는데  <br>
import시에도 테이블 스페이스를 먼저 만들어야 함, export한 db가 테이블스페이스를 가지고 있는 경우 <br>
동일한 테이블 스페이스가 존재해야 import가 제대로 됨	 <br>
image source : https://supercoding.tistory.com/6 <br>	

![image](https://user-images.githubusercontent.com/44331989/137465530-825d2b88-0e43-47b0-a5f7-f95588acee08.png) <br>
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=steady1547&logNo=220777076107 <br>	

![image](https://user-images.githubusercontent.com/44331989/137500074-b1c43aff-d83e-4f09-a27f-5c63d56e6b7f.png) <br>
image source : https://godlvkhj.tistory.com/215 <br>
	
### 사용자가 생성한 테이블 전부 삭제
![image](https://user-images.githubusercontent.com/44331989/137472927-443a6c46-5e6a-4be8-b0fa-a98d8cd02498.png) <br>
중요한 점은 반드시 해당 테이블을 가진 사용자로 로그인 후  <br>
select 해 봐야 된다는 것임	<br>
image source : https://zzznara2.tistory.com/228 <br>	
~~~
SELECT  'DROP TABLE ' || object_name || ' CASCADE CONSTRAINTS;'
  FROM    user_objects
WHERE   object_type = 'TABLE';
~~~

### 리눅스 오라클 삭제 
https://euless.tistory.com/75 <br>	

### 사용자에게 부여된 권한 조회
~~~
SELECT GRANTEE, PRIVILEGE, ADMIN_OPTION
 FROM DBA_SYS_PRIVS
WHERE grantee= '사용자명'
;	
~~~
~~~
권한부여	
# 아래는 예시
GRANT SELECT ANY SEQUENCE, CREATE ANY VIEW, CREATE VIEW, SELECT ANY TABLE, INSERT ANY TABLE, SELECT ANY TRANSACTION, CREATE ANY SEQUENCE, UNLIMITED TABLESPACE
TO "사용자명";	
~~~

### 오라클 삭제
https://corock.tistory.com/135 <br>	
	
### TRUNC(시간이나 숫자 절사) <br>
![image](https://user-images.githubusercontent.com/44331989/138977371-bda96c84-d8f8-4ff3-afc4-8338b0283175.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138977389-1c0922fa-32f4-4fa3-b384-633ab3748552.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138977410-f92e80cb-63f2-48cc-a6be-f73ad79c2c58.png) <br>
image source : https://kkkapuq.tistory.com/106 <br>	

## 조인
### 조인 시 On절, WHERE절 차이
![image](https://user-images.githubusercontent.com/44331989/139012802-3405c4a9-8414-4d56-aec2-5fc8fe3b081f.png) <br>
![image](https://user-images.githubusercontent.com/44331989/139012870-29b35ad5-e1b2-48bb-baae-a33f94feae14.png) <br>
![image](https://user-images.githubusercontent.com/44331989/139012908-88f077ec-e1f2-4b99-bb2c-babb9f414609.png) <br>
![image](https://user-images.githubusercontent.com/44331989/139013151-c730b374-2abe-46c5-b630-dcef2a1be8f0.png) <br>
![image](https://user-images.githubusercontent.com/44331989/139013185-ee91c3ad-de8f-40f9-9b12-5948cf10520f.png) <br>
image source : https://myjamong.tistory.com/229 <br>	

### OUTER JOIN
![image](https://user-images.githubusercontent.com/44331989/139248344-e600ee73-8a55-41d0-a1f0-1d6a65c3c52b.png) <br>
image source : https://goddaehee.tistory.com/62 <br>	
	
### 계층형 쿼리
테이블에 계층형 데이터가 존재할 경우 사용 <br>
조직, 사원, 메뉴등의 순환관계 등에 사용됨 <br>	
~~~
SELECT ... 
       [ CONNECT_BY_ISLEAF ISLEAF 
  FROM 테이블명 
 START WITH condition
CONNECT BY PRIOR condition AND condition;
-- START WITH절  |  계층구조 전개의 시작 위치를 지정하는 구문
-- CONNECT BY 절  |  다음에 전개될 자식 데이터를 지정하는 구문	
~~~	

~~~
-----------------------------------------------------------------
-- 계층형쿼리
------------------------------------------------------------------
--PRIOR 자식 = 부모 _ 부모 -> 자식 방향으로 전개
--PRIOR 부모 = 자식 _ 자식 -> 부모 방향으로 전개

--1. 순방향 전개
SELECT LEVEL, ENAME,
       LPAD(' ', 4 * (LEVEL-1)) || EMPNO 사원, 
       MGR, 
       CONNECT_BY_ISLEAF ISLEAF 
  FROM EMP 
  START WITH MGR IS NULL 
  CONNECT BY PRIOR EMPNO = MGR;	
~~~	
![image](https://user-images.githubusercontent.com/44331989/140033921-fb043efa-a204-4c70-87f1-7d23ad0f2546.png) <br>
~~~
--2. 역방향 전개
SELECT LEVEL, ENAME,
       LPAD(' ', 4 * (LEVEL-1)) || EMPNO 사원, 
       MGR, 
       CONNECT_BY_ISLEAF ISLEAF 
  FROM EMP 
 START WITH EMPNO = 7876 
 CONNECT BY PRIOR MGR = EMPNO;	
~~~	
![image](https://user-images.githubusercontent.com/44331989/140034011-edb68fb6-d69a-4fec-ad84-bd2e2441bb21.png) <br>

~~~
--3. 루트와 순차적 계층경로
SELECT CONNECT_BY_ROOT EMPNO 루트사원, 
       SYS_CONNECT_BY_PATH(EMPNO, '/') 경로, 
       EMPNO, 
       MGR 
  FROM EMP 
 START WITH MGR IS NULL 
 CONNECT BY PRIOR EMPNO = MGR; 	
~~~	
![image](https://user-images.githubusercontent.com/44331989/140034097-0050c058-7d0a-47e8-be50-25b713c8f254.png) <br>

![image](https://user-images.githubusercontent.com/44331989/140034128-89c267fe-4c7a-4d69-b321-257f1af9ee22.png) <br>
![image](https://user-images.githubusercontent.com/44331989/140034166-43736b20-5325-452b-b443-e4048b339c9b.png) <br>
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=dlscjf1505&logNo=220868957220 <br>	
	
### oracle expdp 이용한 백업 정책
#### 셸 스크립트 생성	
~~~
# (ex)expd_script.sh
# oracle home direcoty 설정
ORACLE_BASE=/oracle/app; export ORACLE_BASE
ORACLE_HOME=/oracle/app/product/12.2.0.1; export ORACLE_HOME
# oracle SID 설정
ORACLE_SID=TEST; export ORACLE_SID
PATH=/usr/sbin:$PATH; export PATH
PATH=$ORACLE_HOME/bin:$PATH; export PATH
# oracle 소유자 설정
export ORACLE_OWNER=oracle
# 백업파일뒤에 붙일 날짜 세팅
export DATE=`date +%Y%m%d`
export DATE1=`date -d "-2 day" +%Y%m%d`
# expdp 이용한 백업 실행
expdp TEST/TEST1234 schemas=TEST directory=DATA_PUMP_DIR dumpfile=test_backup_$DATE.dmp logfile=test.log
exit;	
~~~	

#### 크론탭으로 스케줄 등록
~~~
# (ex)매일 오후 23시59분에 아래 스크립트 실행
59 23 * * * /home/test/backup/expd_script.sh	
~~~	

![image](https://user-images.githubusercontent.com/44331989/142134326-52bf9579-92f0-4946-b78f-614c86b8713b.png) <br>
결과물 <br>
	
#### oracle expdp
![image](https://user-images.githubusercontent.com/44331989/142134374-f3fd08b3-f822-4b67-843c-214c3058dcaf.png) <br>
image source: https://deftkang.tistory.com/144 <br>	
	
	
	

	
	
	
	
	

	
	
## ORA-12547 TNS lost contact
상황 : oracle 계정이 아닌 다른 계정으로 oracle에 접속해서 expdp를 이용해서 dump를 실행 중임
어쩌다가 실수로 아래처럼 오라클 하위 폴더의 계정을 other의 권한을 rwx 가능하게 변경 했더니
```
sudo chmod -R 757 /oracle
```

![image](https://user-images.githubusercontent.com/44331989/142616589-64056f84-d114-4ffe-8e78-0c0bf4070cd3.png) <br>

위처럼 표시가 되었었다.
실수로 잘 돌아가는 덤프를 잘못 건드리는 바람에..(**다시는 이런 실수 하지 않으리**)

당연히 오라클 폴더의 권한 문제라는 강한 확신을 가지고 구글링을 했음
아래 블로그에서 도움을 얻었다.
$ORACLE\_HOME(/oracle/app/product/12.2.0.1)/bin 아래에 oracle 폴더의 권한을 6751로 변경 했음

```
sudo chmod 6751 oracle
```
![image](https://user-images.githubusercontent.com/44331989/142616650-74cfbdab-a402-4c11-a33a-be3c4d22e191.png) <br>
	
그 후 dump 스크립트 실행 결과 아래처럼 잘 되는 걸 확인 할 수 있었음
![image](https://user-images.githubusercontent.com/44331989/142616782-408e3a46-7a5e-46a7-8b3f-db1d288e3016.png) <br>

**잘 돌아가는 걸 굳이 건드리면 이렇게 고생한다는 걸 또한 번 깨달았음**
블로그에 아래와 같은 글을 적으셨던데.. 누군가가 소유권을 바꿨을 수 있다.(내가 바꿨다)
다행히 저걸로 해결이 되었다.
![image](https://user-images.githubusercontent.com/44331989/142616836-7de1096e-8fc2-48f9-a69a-d27c8ba3d86b.png) <br>
	
#### **벤더사 제품의 파일 소유권은 절대로 함부로 건드리지 말자**

![](https://t1.daumcdn.net/keditor/emoticon/friends1/large/021.gif)
image source : [http://www.dadbm.com/how-to-fix-ora-12547-tns-lost-contact-when-try-to-connect-to-oracle/](http://www.dadbm.com/how-to-fix-ora-12547-tns-lost-contact-when-try-to-connect-to-oracle/) <br>

 
