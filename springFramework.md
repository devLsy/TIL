# [springFramework 사용관련 all info]
## error 관련
## pom.xml에서 dependency 설정 해 줬는데 해당 라이브러리가 다운이 안되는 경우
* 보통 로컬 repository안의 내용을 지우고 다시 다운을 받거나 하는 방식으로 해결이 되는데 하다하다 안되면 maven에서 참조를 못하는 .jar파일을 
"프로젝트/src/main/webapp/WEB-INF/lib 디렉터리에 넣고 pom.xml에서 properties태그안에 * 아래와 같이 jar파일을 넣은 lib디렉터리를 설정 해줌 
<!-- properties 예제 -->
    <properties>
        <spring.maven.artifact.version>4.2.4.RELEASE</spring.maven.artifact.version>
        <egovframework.rte.version>3.7.0</egovframework.rte.version>
        <project.lib.path>${project.basedir}/src/main/webapp/WEB-INF/lib</project.lib.path>
    </properties>
* 그 다음 dependency를 해당 lib디렉터리로 설정
<!-- 20190110 이상엽 추가(spring-webmvc-4.2.4.RELEASE.jar 인식을 못해서 수동으로 설정) -->
		<dependency>
			<groupId>org.springframework.webmvc</groupId>
			<artifactId>org.springframework.webmvc-4.2.4</artifactId>
			<version>4.2.4</version>
			<scope>system</scope>
			<systemPath>${project.lib.path}/spring-webmvc-4.2.4.RELEASE.jar</systemPath>
		</dependency>

* 위의 설정에서 groupId, artifactId, version은 형식적으로 작성하되 중요한 건 scope, systemPath가 중요함
properties에서 lib 디렉터리를 지정해준 경로의 .jar파일을 참조하도록 설정
properties에서 lib가 있는 디렉터리를 ${project.lib.path}안에 설정 했고 그 안에 있는 .jar파일을 지정해 주면 된다.
그 뒤 tomcat - clean workspace & clean & project clean 이후 tomcat 재 실행 <br>
<hr>
* 이클립스에서 프로젝트명을 변경 시 web.xml의 태그명이 변경되어서 에러 나는 경우
로그는 아래와 비슷함..로그를 보니..이미 정의되어 있다고 한다.. <br>
<img src="https://user-images.githubusercontent.com/44331989/51740761-49852c80-20d8-11e9-8b3c-069129c81a74.JPG">
원인은 아래와 같이 web.xml에 xmlns가 중복되어 있었음
<img src="https://user-images.githubusercontent.com/44331989/51740881-a41e8880-20d8-11e9-85c5-bcf2c24eb365.JPG">
그리고 web.xml상단의 web-app에서 이미 id값도 설정 했기에 제일 마지막 줄의 id도 삭제를 해야 정상적으로 tomcat을 구동할 수 있음 <br>
<hr>

## 20190219 TIL: 스프링MVC 구성 관련<br>
스프링 구동 시 읽는 XML은 web.xml, root-context.xml, servlet-context.xml파일임
이 파일 중 web.xml은 Tomcat 구동과 관련된 설정, 나머지 두 파일은 스프링과 관련된 설정임
web.xml상단에 root-context.xml의 경로가 설정되어 있고 <listener>에는 스프링 MVC의 ContextLoaderListener가 등록되어 있음
ContextLoaderListener는 해당 웹 애플리케이션 구동 시 같이 동작하므로 프로젝트 실행 시 가장 먼저 로그를 출력하면서 기록함

root-context.xml이 처리되면 그안에 있는 빈(Bean)설정들이 동작하게 됨
root-context.xml에 정의된 객체(Bean)들은 스프링의 영역(context)안에 생성되고 객체들 간의 의존성이 처리됨
root-context.xml이 처리된 후에는 스프링 MVC에서 사용하는 DispatcherServlet이라는 서블릿과 관련된 설정이 동작함

org.springframework.web.servlet.DispatcherServlet 클래스는 스프링 MVC의 구조에서 가장 핵심적인 역할을 하는 클래스임
내부적으로 웹 관련 처리의 준비작업을 진행하는데 이 때 사용하는 파일이 servlet-context.xml임
프로젝트가 실행될 때 로그를 보면 DispatcherServlet에서 XmlWebApplicationContext를 이용해서 servlet-context.xml을 로딩하고 해석하기 시작함
이 과정에서 등록된 객체(Bean)들은 기존에 만들어진 객체(Bean)들과 같이 연동되게 됨




  
<h1>20190219 TIL 출처: 코드로 배우는 스프링 웹 프로젝트(개정판)예제</h1>
  
  
  
  
  
  
  
  
  
 
