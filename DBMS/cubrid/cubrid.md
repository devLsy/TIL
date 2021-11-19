# cubrid 관련 TIL
## 큐브리드 테이블에 대한 리스트 조회는 db_class로 조회 -> SELECT * FROM db_Class
## 테이블에 대한 Column 리스트는 db_attribute SELECT * FROM db_attribute
참조 url : https://www.cubrid.com/qna/3804836 <br>

## 큐브리드 환경변수
~~~
source /opt/cubrid/.cubrid.sh cubrid.sh이 있는 경로를 source 명령어로 설정 <br>
~~~
cubrid service status <br>
cubrid service start <br>

## INSTR(문자열 위치 반환)
![image](https://user-images.githubusercontent.com/44331989/138824498-fbeb4e3d-dd12-4f81-8209-e08f1ed11e4e.png) <br>
image source : https://www.cubrid.com/faq/3794774 <br>




### cubrid\_broker.conf 세션 타임아웃 해제
dbeaver같은 툴로 큐브리드 접속해서 쿼리 에디터에서 SQL을 작성 시 사용안하는 경우 연결이 끊긴다고 나오는 경우가 있음
이 경우 cubrid의 세션 타임아웃 설정을 변경해주면 된다는 답변을 보고 수정 했음

```
# 예시(경로는 다를 수 있음)
vi /opt/cubrid/conf/cubrid_broker.conf
```

[##_Image|kage@K7wHZ/btrloeyhu57/BP4dYRDwIrXVZN9tW9VTI1/img.png|CDM|1.3|{"originWidth":865,"originHeight":652,"style":"alignCenter","caption":"cubrid_broker.conf파일"}_##]
이렇게 하고 다시 테스트 해 볼 것
[##_Image|kage@bgzIxW/btrlpb9iRup/XkP4BvqsbYR9ImtvWu2E3K/img.png|CDM|1.3|{"originWidth":1002,"originHeight":308,"style":"alignCenter","caption":"Cannot communicate with the broker or received invalid packet"}_##]

여전히 dbeaver에서는 에러가 발생한다, 다시 확인 해 봐야 겠다.
아직 큐브리드를 내렸다 올리지 않았는데 한번 내렸다 올린 후 테스트 해볼 예정

**21.11.19 큐브리드를 내렸다 올려봤는데도 여전히 같은 에러 발생**

**큐브리드 매니저에서는 잘되네, dbeaver에서만 저러네..**

출처 : [https://www.cubrid.com/qna/3801470](https://www.cubrid.com/qna/3801470) <br>





