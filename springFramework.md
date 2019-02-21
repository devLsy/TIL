# [springFramework 사용관련 all info]
## error 관련
### pom.xml에서 dependency 설정 해 줬는데 해당 라이브러리가 다운이 안되는 경우
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

## 20190219 TIL: 
### 스프링MVC 구성 관련<br>
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

### 스프링 MVC의 기본 사상<br>
Java를 이용하는 웹 애플리케이션을 제작해 본적이 있다면 보통 Servlet/JSP 기술을 활용해서 제작하는 방식을 먼저 배우고 그 이후 
모델2라는 방식에 대해서 학습하는데, 스프링 MVC의 경우 이러한 부분은 개발자들에게 보여주지 않고, 개발자들은 자신이 필요한 부분만을 집중해서
개발할 수 있는 구조로 만들어져 있음
웹 프로그래밍에서 가장 익숙한 단어들 중 하나인 Request/Response..
Servlet/JSP에서는 HttpServletRequest/HttpServletResponse라는 타입의 객체를 이용해 브라우저에게 전송한 정보를 처리하는 방식임
스프링 MVC의 경우 이 위에 하나의 계층을 더한 형태가 되는데
스프링 MVC는 개발자들이 직접 HttpServletRequest/HttpServletResponse 등과 같이 Servlet/JSP의 API를 사용할 필요성이 현저하게 줄어듬
스프링은 중간에 연결 역할을 하기 때문에 이러한 코드를 작성하지 않고도 원하는 기능을 구현할 수 있게 됨

개발자의 코드는 스프링 MVC에서 동작하기 때문에 과거에는 스프링 MVC의 특정한 클래스를 상속하거나 인터페이스를 구현하는 형태로 개발할 수 있었지만,
스프링 2.5버전부터 등장한 어노테이션 방식으로 인해 최근 개발에는 어노테이션이나 XML 등의 설정만으로 개발이 가능하게 되었음<br>
(스프링 MVC 역시 내부적으로는 Servlet API를 활용함)<br><p>
<img src="https://user-images.githubusercontent.com/44331989/52994208-2db44280-345a-11e9-9b3e-dc7c65209668.jpg"><br><p>
	
<img src="https://user-images.githubusercontent.com/44331989/52994777-fd6da380-345b-11e9-95ae-8325c64e01cf.jpg"><br><p>

<img src="https://user-images.githubusercontent.com/44331989/52995137-132f9880-345d-11e9-85f3-312b7dd4f7b3.jpg"><br><p>	

### 스프링 MVC를 이용하는 경우 작성되는 Controller는 다음과 같은 특징이 있음
* HttpServletRequest, HttpServletResponse를 거의 사용할 필요 없이 필요한 기능 구현 <br>
* 다양한 타입의 parameter 처리, 다양한 타입의 return 타입 사용 가능 <br>
* GET, POST방식 등 전송방식에 대한 처리를 어노테이션으로 처리 가능 <br>
* 상속/인터페이스 방식 대신에 어노테이션만으로도 필요한 설정 가능 <br>
<strong>다른 프레임워크들과 달리 스프링 MVC는 어노테이션을 중심으로 구성되기 때문에 각 어노테이션의 의미에 대해서 주의해 가며 학습해야 함</strong><br>
샘플 프로젝트 작성 후 com.ex.controller이라는 패키지 작성 후 거기에 sampleController 클래스 작성 후

@Controller라는 어노테이션을 적용 시 자동으로 스프링의 객체(Bean)으로 등록되는데 servlet-context.xml에 그 이유가 있음
servlet-context.xml 하단부분에 context:component-scan base-package="com.ex.controller"라고 되어 있고 이는
context:component-scan이라는 태그를 이용해서 지정된 패키지를 scan하라는 의미, 해당 패키지에 선언된 클래스들을 scan하면서 스프링에서 객체(Bean)설정에 사용되는 어노테이션들을 가진 클래스들을 파악하고 필요하면 이를 객체로 생성해서 관리하게 됨
SampleController 클래스가 스프링에서 관리되면 아래 이미지와 같이 클래스 옆에 작게 's'모양의 아이콘이 추가됨
<img src="https://user-images.githubusercontent.com/44331989/52996242-54757780-3460-11e9-896c-e2e4754002bf.JPG"><br><p>
	
클래스 선언부에는 @Controller와 함께 @RequestMapping을 많이 사용함
@RequestMapping은 현재 클래스의 모든 메서드들의 기본적인 URL 경로가 됨<br>
예를 들어 SampleController 클래스를 아래와 같이 "/sample/*" 이라는 경로로 지정했다면 /sample/aaa, /sample/bbb과 같은 URL은 모두 SampleController에서 처리됨

<img src="https://user-images.githubusercontent.com/44331989/52997564-3ad62f00-3464-11e9-984d-d620944a922d.JPG"><br><p>

### RequestMapping의 변화
@Controller 어노테이션은 추가적인 속성을 지정할 수 없지만 @RequestMapping은 몇 가지의 속성을 추가할 수 있는데 그중 많이 사용하는 속성이
method 속성이고 흔히 GET방식, POST 방식을 구분해서 사용할 때 이용되며 스프링 4.3버전에서는 @RequestMapping을 줄여서 사용할 수 있는 @GetMapping, @PostMapping이 등장하는데 축약형의 표현이므로 기존의 @RequestMapping과 비교해서 학습하는 걸 권장<br>
@RequestMapping은 POST, GET 방식 둘다 지원해야 하는 경우네느 배열로 처리해서 지정할 수 있고 일반적으로는 GET, POST방식만을 사용하지만 최근에는 PUT, DELETE 방식 등도 점점 많이 사용하고 있음, @GetMapping의 경우 오직 GET방식에만 사용할 수 있으므로 간편하지만 기능에 대한 제한은 많은 편임<br>

### Controller의 parameter 수집
Controller를 작성할 때 가장 편리한 기능은 parameter가 자동으로 수집되는 기능임, 이 기능을 이용하면 매번 request.getParameter()를 이용하는 불편함을 없앨 수 있음<br>

<img src="https://user-images.githubusercontent.com/44331989/53060353-8f79b880-34fd-11e9-85e9-87b2966d56a6.JPG"><br>
위와 같이 SampleDTO class가 있고(Lombok 사용으로 @Data 어노테이션으로 자동으로 setter, getter, toString method 생성)<br>

<img src="https://user-images.githubusercontent.com/44331989/53060878-43c80e80-34ff-11e9-987f-85f2f09b7c76.JPG"><br>
위와 같이 SampleController의 method가 SampleDTO를 parameter로 사용하게 되면 자동으로 setter 메서드가 동작하면서 parameter를 수집하게 됨<br>
method에는 @GetMapping이 사용되었으므로, 아래와 같이 브라우저에서 필요한 parameter를 추가후 호출하게 되면

<img src="https://user-images.githubusercontent.com/44331989/53060596-69a0e380-34fe-11e9-980f-3a86ce31418b.JPG"><br>

아래와 같이 Tomcat의 log에 호출한 parameter값이 찍히는걸 확인할 수 있음<strong>(주목할 점은 자동으로 타입을 변환해서 처리한다는 점)</strong><br>
<img src="https://user-images.githubusercontent.com/44331989/53060661-9fde6300-34fe-11e9-9249-21ac057cf689.JPG"><br><p>

### parameter의 수집과 변환
Controller가 parameter를 수집하는 방식은 parameter type에 따라 자동으로 변환하는 방식을 이용함<br>
예를 들어 위읫 SampleDTO에는 int type으로 선언된 age가 자동으로 숫자로 변환되는 것을 볼수 있음<br>

만일 기본 자료형이나 문자열 등을 이용한다면 아래 이미지와 같이 parameter의 type만을 맞게 선언해주는 방식을 사용할 수 있음<br>
<img src="https://user-images.githubusercontent.com/44331989/53092821-16a84a00-3559-11e9-8a64-388c52f211bd.JPG"><br>

위에서는 @RequestParam 어노테이션을 사용했는데 @RequestParam은 parameter로 하나 이상의 parameter를 받을 때(사용된 변수의 이름과 전달되는 parameter의 이름이 다른 경우에 유용하게 사용됨)사용됨, 단점이 있는데 넘어오는 requestParam중 @RequestParam에서 지정한 키값이 존재하지 않다면 BabRequest http 400 에러가 발생함 이를 방지하기 위한 방법 url: https://heavenly-appear.tistory.com/302<br><p>
브라우저에서 http://localhost:9393/sample/ex02?name=dev&age=98 이런식으로 parameter를 추가해서 호출 시 아래 이미지처럼 이전과 동일하게 데이터가 들어온 것을 확인할 수 있음
<img src="https://user-images.githubusercontent.com/44331989/53093336-7a7f4280-355a-11e9-9901-f978303b2ea2.JPG"><br><p>
	
### 리스트, 배열 처리
동일한 이름의 parameter가 여러 개 전달되는 경우에는 아래 이미지와 같이 ArrayList<> 등을 이용해서 처리가 가능함<br>
스프링은 parameter의 type을 보고 객체를 생성하므로 parameter의 type은 List<>와 같이 interface type이 아닌 실제적인 class type으로 지정해야 함
아래 코드의 경우 'ids'라는 이름의 parameter가 여러개 전달되더라도 ArrayList<String>이 생성되어 자동으로 수집됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53093534-05f8d380-355b-11e9-8b50-fe3e592a2754.JPG"><br>

브라우저에서 http://localhost:9393/sample/ex02List?ids=dev&ids=111&ids=222 이런식으로 호출 시 아래 이미지 처럼 데이터가 들어옴<br>
<img src="https://user-images.githubusercontent.com/44331989/53093908-0a71bc00-355c-11e9-8452-4b3b07f68835.JPG"><br>

아래 이미지처럼 배열의 경우도 동일하게 처리할 수 있음
<img src="https://user-images.githubusercontent.com/44331989/53094296-1742df80-355d-11e9-877a-e986630b8e9d.JPG"><br>
브라우저에서 http://localhost:9393/sample/ex02Array?ids=dev&ids=good&ids=tel 이렇게 호출 시 아래 이미지 처럼 데이터가 들어옴<br>

<img src="https://user-images.githubusercontent.com/44331989/53094456-84ef0b80-355d-11e9-9c3c-79520bd5c28a.JPG"><br>

### 객체 리스트
만일 전달하는 데이터가 SampleDTO와 같이 객체 타입이고 여러 개를 처리해야 한다면 약간의 작업으로 한번에 처리 가능<br>
예를 들어 SampleDTO를 여러 개 전달 받아서 처리하고 싶다면 아래 이미지처럼 SampleDTO의 리스트를 포함하는 SampleDTOList class를 설계함<br>
<img src="https://user-images.githubusercontent.com/44331989/53094986-f2e80280-355e-11e9-9c09-5ce2177795d0.JPG"><br>

SampleController에서는 아래 이미지처럼 SampleDTOList tyle을 parameter로 사용하는 method를 작성함<br>
<img src="https://user-images.githubusercontent.com/44331989/53095227-9c2ef880-355f-11e9-94b7-9efb3ca2aed5.JPG"><br>

parameter는 "[인덱스]"와 같은 형식으로 전달해서 처리할 수 있음<br>
전송하려고 하는 URL: http://localhost:9393/sample/ex02Bean?list[0].name=aaa&list[2].name=bbb <br>
Tomcat은 version에 따라서 위와 같은 문자열에서 "[]" 문자를 특수문자로 허용하지 않을 수 있는데 이 경우에는 아래와 같은 에러를 발생시킴<br>
<img src="https://user-images.githubusercontent.com/44331989/53142162-421d4a00-35d6-11e9-8b09-ad0eb5b3f35a.JPG"><br>

Javascript를 이용하는 경우에는 encodeURLComponent()와 같은 방법으로 해결할 수 있으나 URL 인코딩 방식으로 처리하도록 함<br>
참조 url: https://stackoverflow.com/questions/9966053/what-does-5b-and-5d-in-post-requests-stand-for <br>
위의 url을 http://localhost:9393/sample/ex02Bean?list%5B0%5D.name=aaa&list%5B2%5D.name=bbb 변경후 다시 요청하면 아래와 같이 3개의 SampleDTO객체가 생성된 것을 볼 수 있고 '[]' 안에 인덱스 번호에 맞게 객체의 속성값이 들어간 게 확인 됨
<img src="https://user-images.githubusercontent.com/44331989/53142435-2fefdb80-35d7-11e9-9f43-418fb10303e9.JPG"><br>

<strong>parameter의 수집을 다른 용어로는 바인딩이라고 하는데 변환이 가능한 데이터는 자동으로 변환되지만 경우에 따라서 parameter를 변환해서 처리해야 하는 경우도 존재하는데 예를 들면 화면에서 '2018-02-21'과 같이 문자열로 전달 된 데이터를 java.util.Date타입으로 변환하는 과정이 그렇다<br>
스프링 Controller에서는 @InitBinder를 이용해서 이러한 변환처리를 할 수 있음</strong><br>
아래와 같이 TodoDTO 클래스를 하나 만들고 dueDate의 변수타입을 java.util.Date로 설정했음

<img src="https://user-images.githubusercontent.com/44331989/53143371-08027700-35db-11e9-9830-7fcd73ba665d.JPG"><br>


아래와 같이 SampleController에서 /ex03으로 요청받게 매핑 한 뒤<br>
<img src="https://user-images.githubusercontent.com/44331989/53143420-3d0ec980-35db-11e9-972f-6f111c1a4b7e.JPG">

브라우저에서 http://localhost:9393/sample/ex03?title=test&dueDate=2018-02-21 이런식으로 호출을 하면 아래처럼 400 에러가 발생한다.<br>
<img src="https://user-images.githubusercontent.com/44331989/53143479-75160c80-35db-11e9-90ec-444f2c1a952a.JPG"><br>

이를 해결하기 위해 SampleController에서 아래처럼 @InitBinder를 이용해서 작업을 해준다.<br>
<img src="https://user-images.githubusercontent.com/44331989/53143515-a5f64180-35db-11e9-8fb0-42985e0aa532.JPG"><br>
@InitBinder 설명 참조 url: https://nkcnow.tistory.com/174 <br>

그 후 다시 브라우저에서 http://localhost:9393/sample/ex03?title=test&dueDate=2018-02-21로 호출 하면 아래와 같이 타입이 변환되어 값이 들어옴<br>
<img src="https://user-images.githubusercontent.com/44331989/53143762-c70b6200-35dc-11e9-8243-d68b4792c27c.JPG"><br>

### @InitBinder 외에 또 다른 방법
아래와 같이 parameter로 사용되는 인스턴스 변수에 @DateTimeFormat을 적용해도 변환이 가능함(이 경우에는 @InitBinder가 필요 없음)<br>
<img src="https://user-images.githubusercontent.com/44331989/53143942-8bbd6300-35dd-11e9-855f-43e2ba7a0473.JPG"><br>
브라우저에서 dueDate의 형식이 2018/02/21으로 호출(http://localhost:9393/sample/ex03?title=test&dueDate=2018/02/21)할 경우 @InitBinder를 사용했을 때와 동일하게 타입이 변환되어 값이 들어옴<br><p>
	
### @ModelAttribute 어노테이션
스프링MVC의 Controller는 기본적으로 Java Beans 규칙에 맞는 객체는 다시 화면으로 객체를 전달함<br>
좁은 의미에서 Java Beans 규칙은 단순히 생성자가 없거나 빈 생성자를 가져야 하며, getter/setter를 가진 클래스의 객체들을 의미함<br>
앞의 예제에서 parameter로 사용되었던 SampleDTO의 경우는 Java Beans 규칙에 맞기 때문에 자동으로 다시 화면까지 객체가 전달되었음(전달 될 때는 클래스명의 앞글자는 소문자로 처리됨)<br>
반면 기본 자료형의 경우는 parameter로 선언하더라도 기본적으로 화면까지 전달되지는 않음<br>
아래 이미지처럼 SampleController에서 method를 작성한 뒤 브라우저에서 parameter의 값을 추가해서 호출해 보면 dto의 값은 화면에 전달되지만, page의 값은 화면에 전달되지 않음<br>
<img src="https://user-images.githubusercontent.com/44331989/53144706-b4932780-35e0-11e9-929f-251a4b34badb.JPG"><br>

화면에서의 결과는 아래와 같이 page에는 추가한 값이 안 들어왔음<br>
<img src="https://user-images.githubusercontent.com/44331989/53144799-12277400-35e1-11e9-8aa5-daecde532ea7.JPG"><br>

<strong>@ModelAttribute는 강제로 전달받은 parameter를 Model에 담아서 전달하도록 할 때 필요한 어노테이션임, @ModelAttribute가 걸린 Parameter는 타입에 관계없이 무조건 Model에 담아서 전달되므로, parameter로 전달된 데이터를 다시 화면에서 사용해야 할 경우에 유용하게 사용됨</strong><br>

SampleController에 추가한 메서드의 parameter에 @ModelAttribute를 추가하면 아래와 같이 됨, 기본자료형에 @ModelAttribute를 적용할 경우에는 반드시 @ModelAttribute("변수명") value(int page) 이런식으로 지정해야 함 <br>
<img src="https://user-images.githubusercontent.com/44331989/53144954-b14c6b80-35e1-11e9-847d-170864bc764b.JPG"><br>

브라우저에서 호출 시 화면에 page의 값이 잘 전달된 것이 확인됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53145025-fbcde800-35e1-11e9-8539-5fa192b929db.JPG"><br>
jsp에서 Model에서 받아온 값을 출력하는 건 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/53145247-ce356e80-35e2-11e9-9e2a-aea7663c98d4.JPG"><br>
${"Controller의 Model객체에서 설정한 변수명을 입력"}<br>

### RedirectAttribute
Model 타입과 더불어서 스프링 MVC가 자동으로 전달해 주는 타입 중에는 RedirectAttribute 타입이 존재함<br>
RedirectAttribute는 조금 특별하게도 일회성으로 데이터를 전달하는 용도로 사용됨<br>
RedirectAttribute는 기존에 Servlet에서 response.sendRedirect()를 사용할 때와 동일한 용도로 사용됨<br>
아래 이미지처럼 RedirectAttribute는 Model과 같이 parameter로 선언해서 사용하고 addFlashAttribute(이름, 값) 메서드를 이용해서 화면에 한번만 사용하고 다음에는 사용하지 않는 데이터를 전달하기 위해서 사용됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53148602-946a6500-35ee-11e9-8d84-708e17c159c0.JPG"><br>

### Controller의 return type
스프링 MVC의 구조가 기존의 상속과 인터페이스에서 어노테이션을 사용하는 방식으로 변한 이후 가장 큰 변화 중 하나는 return type이 자유로워 졌다는 점임<br>
Controller의 method가 사용할 수 있는 return type은 주로 다음과 같음<br>
* String: jsp를 이용하는 경우에는 jsp파일의 경로와 파일이름을 나타내기 위해서 사용함<br>
* void: 호출하는 URL과 동일한 이름의 jsp를 의미함<br>
* VO, DTO 타입: 주로 JSON type의 데이터를 만들어서 반환하는 용도로 사용됨<br>
* ResponseEntity 타입: response 할 때 Http 헤더 정보와 내용을 가공하는 용도로 사용함<br>
* Model, ModelAndView: Model로 데이터를 반환하거나 화면까지 같이 지정하는 경우에 사용함(최근에는 많이 사용하지 않음)<br>
* HttpHeaders: 응답에 내용 없이 Http 헤더 메시지만 전달하는 용도로 사용함<br>

### @ResponseBody
return 되는 데이터가 view를 통해서 출력되지 않고 HTTP Response Body에 직접 쓰여지게 됨<br>
참조 url: https://ismydream.tistory.com/140 <br>

아래와 같은 메서드의 경우는 JSON type으로 객체를 변환해서 브라우저에 전달됨<br>
선행작업이 필요함(pom.xml에 Jackson Databind 라이브러리를 추가해야 함, 안 할 경우 에러 발생)
<img src="https://user-images.githubusercontent.com/44331989/53153073-a3571480-35fa-11e9-8a6b-c3b0198aa169.JPG"><br>

아래와 같이 JSON type으로 변환되어 브라우저에 전달됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53153121-d00b2c00-35fa-11e9-9913-0cb18a21e07b.JPG"><br>

### ResponseEntity
HTTP protocol의 header를 다룰 경우에 스프링 MVC에서는 HttpServletRequest나 HttpServletResponse를 직접 핸들링하지 않아도 이런 작업이 가능하게 되어 있음<br>
이러한 처리를 위해 ResponseEntity를 통해서 원하는 헤더 정보나 데이터를 전달할 수 있음<br>
ResponseEntity는 HttpHeaders 객체를 같이 전달할 수 있고, 이를 통해서 원하는 HTTP 헤더 메시지를 가공하는 것이 가능함<br>
아래 이미지는 Controller에서 JSON type의 Header Message와 200 OK라는 상태 코드를 브라우저로 전송하는 method임<br>
<img src="https://user-images.githubusercontent.com/44331989/53153678-39d80580-35fc-11e9-85bc-9318d1592d7e.JPG"><br>

Header Message가 잘 들어왔고 개발자도구에서 보면 200 OK라는 상태코드가 전달됨이 확인 됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53154089-4741bf80-35fd-11e9-9da4-55025caa9ab5.JPG"><br>

### Controller의 Exception 처리
Controller를 작성할 때 예외 상황을 고려하면 처리해야 하는 작업이 엄청나게 늘어날 수 밖에 없는데 스프링 MVC에서는 이러한 작업을 다음과 같은 방식으로 처리할 수 있음<br>

* @ExceptionHandler와 @ControllerAdvice를 이용한 처리
* @ResponseEntity를 이용하는 예외 메시지 구성

#### ControllerAdvice
@ControllerAdvice는 스프링의 AOP(Aspect-Oriented-Programming)을 이용하는 방식(간단히 언급하면 핵심적인 로직은 아니지만 프로그램에서 필요한 '공통적인 관심사(cross-concern)는 분리'하자는 개념임)<br>
이를 활용해서 공통적인 예외사항에 대해서는 별도로 @ControllerAdvice를 이용해서 분리하는 방식임<br>

아래 이미지는 공통적인 예외처리를 담당할 CommonExceptionAdvice 클래스를 생성 후 Exception Message를 콘솔에 출력<br>
model에 Exception을 담아서 예외 발생 시 에러 페이지로 넘겨서 error_page에서 에러 메시지를 출력하는 예제임<br>
@ControllerAdvice 어노테이션은 해당 객체가 스프링의 컨트롤러에서 발생하는 예외를 처리하는 존재임을 명시하는 용도임<br>
@ExceptionHandler는 해당 method가 () 들어가는  예외타입을 처리한다는 것을 의미하며 @ExceptionHandler 어노테이션의 속성으로는 Exception 클래스 타입을 지정할 수 있음(아래의 경우는 Exception.class를 지정하였으므로 모든 예외에 대한 처리가 except()만을 이용해서 처리할 수 있음<br>
만일 특정한 타입의 예외를 다루고 싶다면 Exception.class 대신에 구체적인 예외의 클래스를 지정해야 함<br>
<img src="https://user-images.githubusercontent.com/44331989/53157556-16fe1f00-3605-11e9-8a3f-eea6c49829dd.JPG"><br>
예외처리가 있는 클래스는 servlet-context.xml에 component-scan에 해당 클래스가 있는 패키지를 설정해줘야 함<br><p>

에러 페이지가 정상적으로 출력되는 지 확인하기 위해서 브라우저에서 고의로 parameter를 넣지 않고 호출한 결과는 아래 이미지와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/53158180-5f6a0c80-3606-11e9-85e1-30bd99cc9ffb.JPG"><br>




















	






















































  
<h2>20190219 TIL 출처: 코드로 배우는 스프링 웹 프로젝트(개정판)예제</h2>
  
  
  
  
  
  
  
  
  
 
