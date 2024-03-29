# 전자정부프레임워크 
## 20181218 20:06 작성 시작

* SpringMVC 동작
1. Client 요청이 들어오면 Dispatcher Servlet이 가장 먼저 요청을 받는다. 주로 *.do
의 형태로 오는 요청을 처리한다.
2. HandlerMapping이 요청에 해당하는 Controller를 리턴한다. @RequestMapping의
형태로 요청되는 URL을 처리할 Controller 메소드가 Mapping된다.
3. Controller는 비즈니스 로직을 호출하고 전달 받은 결과를 ModelAndView에 반영
하여 리턴한다.
4. ViewResolver는 어떤 형태로 출력할 것인지 형태를 결정하고, view name을 받아 해당되는 view객체를 리턴한다. 주로 JSP가 활용되며, XML, JSON등 활용가능하다.
5. View는 Model 객체를 받아 결과를 출력한다.
<!-- springMVC 동작 -->
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50152044-e993ad00-0305-11e9-9659-34b4ed176519.JPG">
</div><br/><p>
<!-- 웹에서 스프링 활용을 위한 web.xml -->  
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50151525-7fc6d380-0304-11e9-892b-7d031ce9b28e.JPG">
</div><br/><p>
<!-- context-servlet.xml: springMVC 설정 -->  
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50151634-cae0e680-0304-11e9-991f-48c327cf34c3.JPG">	
</div><br/><p>
<!-- pom.xml간의 라이브러리 의존성 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50258883-04207000-0445-11e9-9bc4-e0578bea141c.JPG">
</div><br/><p>
<!-- pom.xml에서 라이브러리 의존성 설명 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260440-65e3d880-044b-11e9-8079-4246dba956bf.JPG">
</div>처음 Maven을 실행할 때 Local Repository에 라이브러리가 없으면 pom.xml의 repository
에 기술된 Remote Repository로부터 라이브러리를 다운로드 받게 된다.<br/><p>  
<!-- pom.xml의 Remote Repository 설정 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260492-89a71e80-044b-11e9-8f8f-ca695e74a38f.jpg">
</div><br/><p>  
<!-- maven 빌드관리 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260537-b9eebd00-044b-11e9-8582-9699b86c8f62.JPG">
</div><br/><p>  
<!-- maven 빌드 생명주기 단계 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260570-e6a2d480-044b-11e9-847a-3f8abff2503a.JPG">
</div><br/><p>  
<!-- maven 빌드 생명주기 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260597-03d7a300-044c-11e9-9359-f7af032ca24a.JPG">
</div><br/><p>  
<!-- eclipse에서 maven 빌드 설정-->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260615-1c47bd80-044c-11e9-9d54-8c9d686ae96b.JPG">
</div><br/><p>  
<!-- pom.xml의 구조-->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260667-62048600-044c-11e9-80df-dd6e2466aea9.JPG">
</div>pom.xml 은 프로젝트의 정보, 의존성, 빌드 세팅(플러그인) 정보를 포함하고 있다. Maven 프
로젝트는 빌드 되어 로컬 또는 원격 Repository 에 배포될 수 있기 때문에 자체적으로 Group
Id, Artifact Id, 버전 정보를 가진다. 앞에서 설명했던 라이브러리 관리를 위한 의존성 관리를
할 수 있다. 컴파일, 패키징 뿐만 아니라 테스트 결과 리포트, java doc 생성과 같은 작업들을
플러그인 설정을 통해서 수행 할 수 있다.<br/><p>  
<!-- pom.xml 예제 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260754-c58eb380-044c-11e9-8f66-5b6f852f3a59.JPG">
</div><br/><p>  
<!-- maven plugin -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260804-f838ac00-044c-11e9-9aca-f548f105f778.JPG">
</div>  
<!-- maven-install example -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50260941-90369580-044d-11e9-88e5-dd598144d3c8.JPG">
</div><br/><p>  

# 서버 개발환경
* 표준프레임워크 형상관리 도구인 Subversion을 활용하여 팀프로젝트를 통합하고, 배포도구인 Jenkins를 활용하여 계속적인 테스트와 통합을 수행 한다.
<!-- SVN 개요 및 설치 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50263885-53719b00-045b-11e9-8057-e48f9e5c4e00.JPG">
</div>설치가 되었으면 VisualSVN Server Manager 를 실행하고, Repositories 를 선택하고 마우스 오른
쪽 버튼을 누르고 Create New Repository 를 선택한다. Regular FSFS repository 를 선택하고,
Repository 이름을 입력한다. 아무 이름이나 입력 가능하며, firstbook 으로 입력 하였다. empty
Repository 를 선택하고 Access 권한 설정을 위해서 Customize permissions 를 선택한다. Add 를
선택, Create User 를 선택하고 User name 과 password 를 입력한다. 편의상 User name 을 dev 로
password 도 동일하게 dev 로 입력하였다. Users 에서 dev 를 선택하고 OK 를 누르고, 확인을
누른다. 권한이 Read/Write 로 되어 있는지 다시 한번 확인한다. 설정된 Repository 정보를 확
30
인하면 설정이 끝난다. 설정이 완료된 후 상단에 표시되는 URL 정보는 나중에 Eclipse 에 연
동할 때 필요한 정보이므로 잘 기억해야 한다.
<!-- VisualSVN Repository 설정 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50263972-c7ac3e80-045b-11e9-918b-6919c7daab4f.JPG">
</div>
<!-- VisualSVN Repository 설정완료 -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50263998-f75b4680-045b-11e9-94aa-e4ccfb41bb62.JPG">
</div><br/><p>  
<!-- Revisions -->  
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264025-2a053f00-045c-11e9-9c83-676ece5264e9.JPG">
</div><br/><p>  
<!-- eclipse와 SVN 연동하기 -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264062-57ea8380-045c-11e9-83a0-109d49216b4a.JPG">
</div><br/><p>  
<!-- eclipse에서 SVN에 프로젝트 반영 -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264161-bdd70b00-045c-11e9-97a4-4f0301674a7b.JPG">
</div><br/><p>  
<!-- SVN Server에서 프로젝트 CheckOut -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264263-2d4cfa80-045d-11e9-8339-d4cb076083fa.JPG">
</div>Subversion 에 저장되어 있는 프로젝트를 활용하기 위해서 개발자가 처음으로 해야 되는 일
은 Subversion 으로 연결하여 해당 소스코드를 자신의 Workspace 로 내려 받는 것(Check out)
이다. Check out 후에는 프로젝트가 Subversion 에 연결을 유지하여 자신이 수정한 내용을 반
영(commit)하거나 다른 개발자가 수정한 내용을 내려 받기(update) 할 수 있다. Check out 을
받기 위해 먼저 SVN Repository Exploring 으로 퍼스펙티브를 변경한다. New>Repository Location을 선택하면, First commit 에서와 동일하게 Subversion 위치 및 계정 정보를 입력하는
화면이 나오게 된다. 정보를 입력하면 Subversion Repository 가 등록되게 되면 프로젝트를 선
택한 후에 마우스 오른쪽 버튼을 눌러서 Check Out 을 선택하게 되면 현재의 workspace 로
해당 프로젝트를 가져온다.<br/><p>
<!-- SVN Commit or Update -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264344-7c932b00-045d-11e9-9da6-0719452dbb6e.JPG">
</div>처음 프로젝트를 share 하거나 Check out 을 한 후에 개발자는 자신이 수정하거나 추가한 소
스코드를 Repository 에 commit 하여 변경을 반영할 수 있다. 다른 개발자가 변경한 소스코드
의 내용은 update 를 통해서 자신의 소스코드에 변경을 반영하게 된다.<br/><p>  
<!-- SVN History -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264416-fc20fa00-045d-11e9-9aeb-101cf733587e.JPG">
</div>Subversion 을 활용하다 보면 종종 충돌이 발생하게 되는 경우가 있다. 하나의 소스코드를 내
려 받은 이후에 수정을 한 후 commit 을 하려고 할 때, 다른 개발자가 동일한 소스코드를
변경하여 Repository 에 이미 반영한 경우에 충돌이 발생된다. 양쪽 소스코드 모두 변경이 된
상태이기 때문에 Subversion 은 소스코드 충돌 표시를 한 후 개발자에게 개발자 소스코드를
강제로 commit 할 것인지 변경된 내역을 다시 update 받을 것인지를 선택하도록 한다. 주로 공동작업이 이루어지는 공통모듈의 경우 충돌이 발생될 여지가 많다. 다른 개발자가 변경한
내역을 무시하고 반영할 경우 문제 발생여지가 많기 때문에 충돌의 해결은 개발자간 커뮤니
케이션을 통해 변경 내역을 확인 한 후에 Override and Commit 이나 Override and update 로 해
결 한다.<br/><p>  
<!-- SVN에서 충돌해결 -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264463-2ffc1f80-045e-11e9-989b-437fb0e80218.JPG">
</div><br/><p><p>

<!-- CI 개요 및 설치 -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264529-7e112300-045e-11e9-9644-3883f350b41d.JPG">
</div>개발자들이 개발한 소스코드를 commit 하면 형상관리 서버에서 통합이 된다. CI 서버는 통합
된 소스코드를 자체 디렉터리로 check out 받아 Maven 등의 도구를 활용하여 테스트 및 빌
드를 수행한다. 주로 Maven package 를 수행하여 테스트 및 배포파일 생성을 수행하고, 개발
서버로 배포하여 개발된 내역을 개발서버에서 확인할 수 있도록 지원한다. 라이브러리의 경
우 Maven deploy 를 실행하여 Nexus 등의 원격 Repository 로 배포 할 수 있다. CI 서버로는
Hudson 이 많이 활용되었으나 Sun 이 Oracle 로 흡수 합병에 따라 기존 Hudson 오픈소스 프
로젝트에 변경이 생기면서 Jenkins 프로젝트(http://jenkins-ci.org)가 시작 되었다. Hudson 도
Eclipse 에서 오픈소스 프로젝트(http://hudson-ci.org)로 개발이 진행 중에 있으나 Jenkins 보다
업데이트 등이 활발하지 못한 상태이다. 두 개의 CI 서버는 거의 동일한 기능을 가지고 있기
때문에 굳이 구별을 할 필요는 없지만, 사용성 및 향후 발전 측면에서는 Jenkins 가 더 유리
한 상태이므로 Jenkins 를 기준으로 설명을 하도록 하겠다. 설치를 하기 위해서 먼저 WAR 형
태(또는 zip)의 배포파일을 다운 받는다. 기존에 설치된 Tomcat 등의 WAS 의 webapp 밑에
압축을 풀어 주는 것으로 설치가 완료 된다. Hudson 의 경우도 마찬가지로 설치 할 수 있다.
Jenkins 의 경우 WAS 까지 패키징하여 윈도우, 맥 등의 실행파일 형태로 설치할 수 있도록
지원하고 있다. 이 경우 별도 WAS 의 설치가 필요하지 않아 편리한 점도 있으나 윈도우 등
OS 에서 자동 실행되는 서비스로 등록이 되기 때문에 개발 및 테스트 용도로는 관리가 어
려울 수 있다.(하지만 인스톨 버전으로 설치해도 세팅하는데는 문제가 없음을 확인)Jenkins 사이트 (http://jenkins-ci.org)에서 다운로드 받은 배포파일을 Tomcat>
webapp 에 압축을 풀고 커맨드창에서 Tomcat>bin 디렉터리에서 startup 으로 Tomcat 을 시작
한다.<br/><p>  
<!-- jenkins 설치 및 시작 -->
<div>  
<img src = "https://user-images.githubusercontent.com/44331989/50264755-5d959880-045f-11e9-98bc-c337419b6834.JPG">
</div><br/><p>  
jenkins 접속 후 시작 및 배포 등등은 20181219_전자정부프레임워크_설치 및 설정 가이드.zip파일을 참조

출처: 전자정부프레임워크 퍼스트북
  
## egovframework 경량화
![image](https://user-images.githubusercontent.com/44331989/129294376-b413e59e-8f8c-4946-bb97-2c24801476d9.png)
image source : https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:dev3.8:install_guide  

## GPKI 의존성 에러
![image](https://user-images.githubusercontent.com/44331989/129294742-0e00a546-3d09-4e0d-bfb8-cb6f99dcecca.png)
image source : https://roadrunner.tistory.com/423
해당 라이브러리 사용하는 java파일 모두 삭제 해야 함  

## maven https 이슈
maven 저장소가 https로 바뀌면서 jdk가 1.7인 개발환경에서 빌드가 실패하는 경우가 있음 <br>
여러가지 방법이 있지만 로컬에서 할 수 있는 가장 쉬운 방법은 jdk 버전을 1.8로 올리는 방법인데 IDE에서 jdk가 여러개 설치된 경우 아래처럼 1.8로 설정 후 빌드 할 것 <br>
![image](https://user-images.githubusercontent.com/44331989/131087167-4a7dbb1c-b521-4750-8f56-9ad72126bb62.png) <br>
참조 url : https://egovframe.go.kr/home/faqinfo/faqinfoRead.do?menuNo=68&faqId=FAQ_0000000000000761 <br>
  
