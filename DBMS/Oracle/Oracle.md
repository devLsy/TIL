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

## 사용자 테이블 중 빈 테이블 조회(user_tables 이용)
~~~
select * FROM user_tables
    WHERE NUM_ROWS = 0
;
~~~


