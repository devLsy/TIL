# springBoot 관련
## springBoot 프로젝트 세팅(IntelliJ 사용)
https://start.spring.io 접속 후 프로젝트 세팅 후 다운로드
![image](https://user-images.githubusercontent.com/44331989/130045598-444f794c-08bb-43d8-9d03-a1c4cec35f7c.png)
1. 프로젝트 빌드도구 선택 -> 요즘 Gradle로 넘어가는 추세라고 함 <br>
2. 개발언어 선택 <br>
3. 스프링부트 버전 선택 -> SNAPSHOT은 만들고 있는거고 M1은 정식 릴리즈된 버전이 아니라서 정식 릴리즈 버전 중 선택 <br>
4. 프로젝트 메타 정보 설정 -> group은 보통 기업 도메인을 적는데 개인 공부니 아무렇게나 적어도 됨, Artfifact는 빌드되어 나온 결과물이라고 보면 됨 <br>
5. 스프링부트 프로젝트 개발시 가져올 라이브러리, 화면 렌더링 시 필요한 템플릿 엔진 선택(ex Thymeleaf, 회사는 다양함) <br>
6. GENERATE를 눌러서 프로젝트 다운로드 <br>

![image](https://user-images.githubusercontent.com/44331989/130046609-5fa130f0-dbb4-4e9d-a555-5ce26e749f43.png)
Intellij에서 프로젝트 열기에서 다운로드 받은 프로젝트 경로의 build.gradle선택(gradle 프로젝트) <br>
![image](https://user-images.githubusercontent.com/44331989/130046914-9b8caa03-edb1-49bc-a72e-61bdbca9e624.png)
최초 프로젝트 열 경우 외부에서 라이브러리를 다운받기 때문에 인터넷 연결 필수이고 시간이 꽤 걸림 <br>

![image](https://user-images.githubusercontent.com/44331989/130060960-d9660e36-70a1-417f-957a-80e07e0f9e7f.png)
gradle 설정관련 중요한 파일임, gradle 설정파일이 여기에 기입되어 있음, https://start.spring.io에서 설정한 내용과 동일함 <br>
1. 스프링부트 버전과 개발언어 등 세팅
2. 그룹, 버전, 컴파일 버전, 라이브러리 다운받는 저장소 등 세팅
3. 라이브러리, html 템플릿 엔진 그리고 테스트 라이브러리는 jUnit으로 자동 추가 됨

![image](https://user-images.githubusercontent.com/44331989/130061990-58d39d46-7780-4ebe-b5dc-7c89efaf160e.png)
.gitignore도 자동으로 추가되어 있음 <br>










