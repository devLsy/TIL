# springFramework 사용관련 all info
## error 관련
## pom.xml에서 dependency 설정 해 줬는데 해당 라이브러리가 다운이 안되는 경우
* 보통 로컬 repository안의 내용을 지우고 다시 다운을 받거나 하는 방식으로 해결이 되는데 
* 하다하다 안되면 maven에서 참조를 못하는 .jar파일을 
* "프로젝트/src/main/webapp/WEB-INF/lib 디렉터리에 넣고 pom.xml에서 properties태그안에 * 아래와 같이 jar파일을 넣은 lib디렉터리를 설정 해줌 
<!-- -->
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
* properties에서 lib 디렉터리를 지정해준 경로의 .jar파일을 참조하도록 설정
* properties에서 lib가 있는 디렉터리를 ${project.lib.path}안에 설정 했고 그 안에 있는 .jar파일을 지정해 주면 된다.
* 그 뒤 tomcat - clean workspace & clean & project clean 이후 tomcat 재 실행

  
  
  
  
  
  
  
  
  
  
  
 
