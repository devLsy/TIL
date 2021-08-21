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

![image](https://user-images.githubusercontent.com/44331989/130046609-5fa130f0-dbb4-4e9d-a555-5ce26e749f43.png) <br>
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

![image](https://user-images.githubusercontent.com/44331989/130063244-83b72517-6988-43c8-be1a-0d1302154fe4.png)
@springBootApplication 에너테이션이 있는 메인 메서드 java파일이 있고 실행 시 내장 tomcat이 8080포트로 구동됨 <br>

![image](https://user-images.githubusercontent.com/44331989/130063667-f20ad8a3-8101-423b-8d58-f407cf9c1060.png) <br>
브라우저에서 페이지 호출 시 404가 표시됨(index페이지가 없으니까 당연함), 여기까지 표시되면 정상 구동된 것임 <br>
이렇게 금방 프로젝트를 구동할 수 있음 <br>
https://start.spring.io에서 프로젝트 구성후 다운로드 후 프로젝트를 IDE에서 열고 실행하면 바로 container가 구동됨 <br>

![image](https://user-images.githubusercontent.com/44331989/130064606-c5554c3e-c7d7-413c-a548-1eea0f95eb20.png) <br>
인텔리제이에서 gradle을 통해서 빌드가 되면 시간이 오래걸릴 수 있으므로 gradle 통하지 않고 인텔리제이에서 직접 빌드하도록 변경함 <br>

## thymeleaf 템플릿 엔진 이용해 view 처리
![image](https://user-images.githubusercontent.com/44331989/130309919-794bccaf-f998-408a-b9ac-d034743320d7.png) <br>
위처럼 test Controller파일 생성 후 hello라는 view파일 return <br>

![image](https://user-images.githubusercontent.com/44331989/130309958-054689ba-8d8d-4871-947a-0a4193d70528.png) <br>
상단에 네임스페이스를 thymeleaf로 설정 후 body에서 th태그를 이용해 thymeleaf 템플릿 이용, 컨트롤러에서 model에 담아 보낸 data 표시 <br>

![image](https://user-images.githubusercontent.com/44331989/130310049-a802a6b4-ad40-4418-ae74-3af08b6cfb56.png) <br>
결과물

## thymeleaf templaet engine 동작 원리
![image](https://user-images.githubusercontent.com/44331989/130310083-45000410-d185-48a6-9c53-285a872e53d6.png) <br>
image source : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/lecture/49573?tab=curriculum

## Build
* IntelliJ 2021.2 버전기준 <br>
* 스프링부트에서는 빌드하는게 정말 간단함, 빌드 실행하면 jar파일이 생기고 해당 jar파일을 실행하면 내장 tomcat을 이용해서 웹 애플리케이션이 구동됨 <br>
1. 인텔리제이에서 빌드하는 방법
    - Gradle-Tasks-build-bootjar실행
      ![image](https://user-images.githubusercontent.com/44331989/130310878-f81a05d1-440d-435d-967c-ddfee9dcf029.png) <br>

2. cmd로 빌드하는 방법

  







