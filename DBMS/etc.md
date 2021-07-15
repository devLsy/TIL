# fk없이 개발
대형 DB에서 마이그레이션 혹은 DB 복구 시 문제가 될 수 있어 fk를 지양하기도 한다고 함 <br>
논리적 fk만 있고 프로그램적으로 처리하도록 유도하기도 한다고 함 <br>
개발기간이 짧은 경우라도 생각해보면 테스트 시 참조관계가 있으면 귀찮기는 함<br>
부득이하게 자식테이블에 먼저 데이터를 넣거나 그 밖의 경우는 Foreign Key를 잠시 Disable 시켜 놓고 나중에 다시 enable 하는 방법이 있음 <br>

출처 : https://engineering-skcc.github.io/oracle%20tuning/foreign_key_%EC%97%86%EC%9D%B4_%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94_DB/ <br>
