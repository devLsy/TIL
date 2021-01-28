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



