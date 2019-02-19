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

스프링 구동 시 읽는 XML은 web.xml, root-context.xml, servlet-context.xml파일임
이 파일 중 web.xml은 Tomcat 구동과 관련된 설정, 나머지 두 파일은 스프링과 관련된 설정임


  
  
  
  
  
  
  
  
  
  
  
 
