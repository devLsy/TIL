# FK없이 DB 설계
대형 DB에서 마이그레이션 혹은 DB 복구 시 문제가 될 수 있어 fk를 지양하기도 한다고 함 <br>
논리적 fk만 있고 프로그램적으로 처리하도록 유도하기도 한다고 함 <br>
개발기간이 짧은 경우라도 생각해보면 테스트 시 참조관계가 있으면 귀찮기는 함<br>
* 자식 테이블 INSERT 시에 부모 테이블이 존재하는지 확인해야 함
* 테이블 구조 변경시 고려사항이 많이 생김
* 테스트 데이터 만드는 것이 불편함

DB에서 FK 없이 설계하는 경우 프로그램에서 체크해야 함 <br>
DB에서 FK 없이 설계하는 경우에 대한 의견은 다 다름 <br>

이 경우 Foreign Key를 잠시 Disable 시켜 놓고 나중에 다시 enable 하는 방법도 있음 <br>
![image](https://user-images.githubusercontent.com/44331989/125753576-e41f1596-0989-469d-8940-14bf9af183b4.png) <br>

출처 : https://engineering-skcc.github.io/oracle%20tuning/foreign_key_%EC%97%86%EC%9D%B4_%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94_DB/ <br>
출처 : https://velog.io/@subutai/%EB%A7%A4%EC%9D%BC-2day <br>
