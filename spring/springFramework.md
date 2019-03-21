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

<hr>

### RequestMapping의 변화
@Controller 어노테이션은 추가적인 속성을 지정할 수 없지만 @RequestMapping은 몇 가지의 속성을 추가할 수 있는데 그중 많이 사용하는 속성이
method 속성이고 흔히 GET방식, POST 방식을 구분해서 사용할 때 이용되며 스프링 4.3버전에서는 @RequestMapping을 줄여서 사용할 수 있는 @GetMapping, @PostMapping이 등장하는데 축약형의 표현이므로 기존의 @RequestMapping과 비교해서 학습하는 걸 권장<br>
@RequestMapping은 POST, GET 방식 둘다 지원해야 하는 경우네느 배열로 처리해서 지정할 수 있고 일반적으로는 GET, POST방식만을 사용하지만 최근에는 PUT, DELETE 방식 등도 점점 많이 사용하고 있음, @GetMapping의 경우 오직 GET방식에만 사용할 수 있으므로 간편하지만 기능에 대한 제한은 많은 편임<br>

<hr>

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

<hr>

### parameter의 수집과 변환
Controller가 parameter를 수집하는 방식은 parameter type에 따라 자동으로 변환하는 방식을 이용함<br>
예를 들어 위읫 SampleDTO에는 int type으로 선언된 age가 자동으로 숫자로 변환되는 것을 볼수 있음<br>

만일 기본 자료형이나 문자열 등을 이용한다면 아래 이미지와 같이 parameter의 type만을 맞게 선언해주는 방식을 사용할 수 있음<br>
<img src="https://user-images.githubusercontent.com/44331989/53092821-16a84a00-3559-11e9-8a64-388c52f211bd.JPG"><br>

위에서는 @RequestParam 어노테이션을 사용했는데 @RequestParam은 parameter로 하나 이상의 parameter를 받을 때(사용된 변수의 이름과 전달되는 parameter의 이름이 다른 경우에 유용하게 사용됨)사용됨, 단점이 있는데 넘어오는 requestParam중 @RequestParam에서 지정한 키값이 존재하지 않다면 BabRequest http 400 에러가 발생함 이를 방지하기 위한 방법 url: https://heavenly-appear.tistory.com/302<br><p>
브라우저에서 http://localhost:9393/sample/ex02?name=dev&age=98 이런식으로 parameter를 추가해서 호출 시 아래 이미지처럼 이전과 동일하게 데이터가 들어온 것을 확인할 수 있음
<img src="https://user-images.githubusercontent.com/44331989/53093336-7a7f4280-355a-11e9-9901-f978303b2ea2.JPG"><br><p>

<hr>

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

<hr>

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

<hr>

### @InitBinder 외에 또 다른 방법
아래와 같이 parameter로 사용되는 인스턴스 변수에 @DateTimeFormat을 적용해도 변환이 가능함(이 경우에는 @InitBinder가 필요 없음)<br>
<img src="https://user-images.githubusercontent.com/44331989/53143942-8bbd6300-35dd-11e9-855f-43e2ba7a0473.JPG"><br>
브라우저에서 dueDate의 형식이 2018/02/21으로 호출(http://localhost:9393/sample/ex03?title=test&dueDate=2018/02/21)할 경우 @InitBinder를 사용했을 때와 동일하게 타입이 변환되어 값이 들어옴<br><p>

<hr>

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

<hr>

### RedirectAttribute
Model 타입과 더불어서 스프링 MVC가 자동으로 전달해 주는 타입 중에는 RedirectAttribute 타입이 존재함<br>
RedirectAttribute는 조금 특별하게도 일회성으로 데이터를 전달하는 용도로 사용됨<br>
RedirectAttribute는 기존에 Servlet에서 response.sendRedirect()를 사용할 때와 동일한 용도로 사용됨<br>
아래 이미지처럼 RedirectAttribute는 Model과 같이 parameter로 선언해서 사용하고 addFlashAttribute(이름, 값) 메서드를 이용해서 화면에 한번만 사용하고 다음에는 사용하지 않는 데이터를 전달하기 위해서 사용됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53148602-946a6500-35ee-11e9-8d84-708e17c159c0.JPG"><br>

<hr>

### Controller의 return type
스프링 MVC의 구조가 기존의 상속과 인터페이스에서 어노테이션을 사용하는 방식으로 변한 이후 가장 큰 변화 중 하나는 return type이 자유로워 졌다는 점임<br>
Controller의 method가 사용할 수 있는 return type은 주로 다음과 같음<br>
* String: jsp를 이용하는 경우에는 jsp파일의 경로와 파일이름을 나타내기 위해서 사용함<br>
* void: 호출하는 URL과 동일한 이름의 jsp를 의미함<br>
* VO, DTO 타입: 주로 JSON type의 데이터를 만들어서 반환하는 용도로 사용됨<br>
* ResponseEntity 타입: response 할 때 Http 헤더 정보와 내용을 가공하는 용도로 사용함<br>
* Model, ModelAndView: Model로 데이터를 반환하거나 화면까지 같이 지정하는 경우에 사용함(최근에는 많이 사용하지 않음)<br>
* HttpHeaders: 응답에 내용 없이 Http 헤더 메시지만 전달하는 용도로 사용함<br>

<hr>

### @ResponseBody
return 되는 데이터가 view를 통해서 출력되지 않고 HTTP Response Body에 직접 쓰여지게 됨<br>
참조 url: https://ismydream.tistory.com/140 <br>

아래와 같은 메서드의 경우는 JSON type으로 객체를 변환해서 브라우저에 전달됨<br>
선행작업이 필요함(pom.xml에 Jackson Databind 라이브러리를 추가해야 함, 안 할 경우 에러 발생)
<img src="https://user-images.githubusercontent.com/44331989/53153073-a3571480-35fa-11e9-8a6b-c3b0198aa169.JPG"><br>

아래와 같이 JSON type으로 변환되어 브라우저에 전달됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53153121-d00b2c00-35fa-11e9-9913-0cb18a21e07b.JPG"><br>

<hr>

### ResponseEntity
HTTP protocol의 header를 다룰 경우에 스프링 MVC에서는 HttpServletRequest나 HttpServletResponse를 직접 핸들링하지 않아도 이런 작업이 가능하게 되어 있음<br>
이러한 처리를 위해 ResponseEntity를 통해서 원하는 헤더 정보나 데이터를 전달할 수 있음<br>
ResponseEntity는 HttpHeaders 객체를 같이 전달할 수 있고, 이를 통해서 원하는 HTTP 헤더 메시지를 가공하는 것이 가능함<br>
아래 이미지는 Controller에서 JSON type의 Header Message와 200 OK라는 상태 코드를 브라우저로 전송하는 method임<br>
<img src="https://user-images.githubusercontent.com/44331989/53153678-39d80580-35fc-11e9-85bc-9318d1592d7e.JPG"><br>

Header Message가 잘 들어왔고 개발자도구에서 보면 200 OK라는 상태코드가 전달됨이 확인 됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53154089-4741bf80-35fd-11e9-9da4-55025caa9ab5.JPG"><br>

<hr>

### Controller의 Exception 처리
Controller를 작성할 때 예외 상황을 고려하면 처리해야 하는 작업이 엄청나게 늘어날 수 밖에 없는데 스프링 MVC에서는 이러한 작업을 다음과 같은 방식으로 처리할 수 있음<br>

* @ExceptionHandler와 @ControllerAdvice를 이용한 처리
* @ResponseEntity를 이용하는 예외 메시지 구성

<hr>

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

<hr>

### 404 에러 페이지 처리
WAS의 구동 중 가장 흔한 에러와 관련된 HTTP 상태코드는 '404'와 '500' 에러코드 임<br>
500 메시지는 'Internal Server Error'이므로 @ExceptionHandler를 이용해서 처리되지만, 잘못된 URL을 호출할 때 보이는 404 에러 메시지의 경우는 조금 다르게 처리하는 것이 좋음<br>

서블릿이나 jsp를 이용했던 개발 시에는 web.xml을 이용해서 별도의 에러 페이지를 지정할 수 있었음, 에러 발생 시 추가적인 작업을 하기는 어렵기 때문에 스프링을 이용해서 404와 같이 WAS 내부에서 발생하는 에러를 처리하는 방식을 알아두는 것이 좋음<br>

스프링 MVC의 모든 요청은 DispatcherServlet을 이용해서 처리되므로 404 에러도 같이 처리할 수 있도록 아래와 같이 web.xml을 수정함<strong>(적색표시된 부분 추가)</strong><br>
<img src="https://user-images.githubusercontent.com/44331989/53158772-bf14e780-3607-11e9-90c1-d61fabc1390f.jpg"><br>

그 후 예외처리 클래스에 다음과 같이 method를 추가함<br>
<img src="https://user-images.githubusercontent.com/44331989/53159083-81fd2500-3608-11e9-9452-53973f9970a3.JPG"><br>
404처리에 해당되는 jsp도 작성 후 브라우저에서 고의로 없는 URL을 호출 하면 정상적으로 작성한 404처리 페이지가 아래처럼 호출됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53159392-25e6d080-3609-11e9-88bb-53fb8d397f00.JPG"><br>

<hr>

## 스프링 MVC프로젝트의 기본 구성
일반적으로 웹 프로젝트는 아래처럼 3-tier(티어)방식으로 구성됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53160902-33518a00-360c-11e9-8c0a-d5549a2ebdf8.jpg"><br>

<img src="https://user-images.githubusercontent.com/44331989/53218435-77da3580-369f-11e9-933a-55319f04b867.jpg"><br>

<hr>

## 단위테스트 jUnit
<strong>단위테스트 하기 위해 매번 Tomcat을 실행하지 않고도 jUnit 테스트 도구를 이용해서 Tomcat 실행 없이 springFramework를 구동시켜서 DB Connection, CRUD등의 테스트가 가능해서 시간도 절약되고 매우 효율적이므로 개발 시에 항상 작성하는 습관을 기르도록 하자<br>
스프링5 버전의 경우 pom.xml에서 jUnit 버전을 4.12 이상으로 해야 한다고 함<br>
<strong> 아래는 HikariCP를 이용한 Connection Pool Test 예제 이미지임<br>
@RunWith, @ContextConfiguration 클래스 상단에 어노테이션등을 사용해서 설정하고 jUnit으로 테스트하려는 method의 상단에 @Test 어노테이션을 작성함<h3>자세한 설명은 구글신에게<h3>
<img src="https://user-images.githubusercontent.com/44331989/53281250-cef80d00-3768-11e9-89b6-db63d2c69c8c.JPG"><br>
root-context-xml 설정은 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/53281367-85a8bd00-376a-11e9-95b1-9764885b79fc.JPG"><br>
결과는 아래와 같이 성공<br>
<img src="https://user-images.githubusercontent.com/44331989/53281400-20a19700-376b-11e9-8cc8-2c2bd61204e1.JPG"><br>

<hr>

## 프레젠테이션(웹)계층의 CRUD 구현 시 MockTest
과거에는 view페이지를 만든 뒤 Tomcat을 실행하고 웹 페이지에서 결과를 확인하는 방식의 코드를 작성해 왔는데, 이 방식은 시간도 오래 걸리고 테스트를 자동화 하기에도 어려움이 많아서 Mock을 이용해서 Controller를 테스트 하는 방식이 있음<br>
MockMvc는 말 그대로 '가짜 mvc'라고 생각하면 되고, 가짜로 URL과 parameter 등을 브라우저에서 사용하는 것처럼 만들어서 Controller를 실행 해 볼 수 있음<br>

우선 아래처럼 BoardController에서 게시글 전체 목록을 가져오는 예제 코드를 작성함<br>
<img src="https://user-images.githubusercontent.com/44331989/53281184-b4716400-3767-11e9-80ea-79a4af66602e.JPG"><br>

그 다음 아래 이미지처럼 테스트 코드는 웹을 개발할 때 매번 URL을 테스트 하기 위해 Tomcat을 실행하는 불편한 단계를 생략하기 위해서 기존과 다르게 작성<br>
<img src="https://user-images.githubusercontent.com/44331989/53281481-6b6fde80-376c-11e9-9faa-84725a3da40b.JPG"><br>
@WebAppConfiguration 어노테이션은 Servlet의 ServletContext를 이용하기 위해서인데, 스프링에서는 WebApplicationContext라는 존재를 이용하기 위해서임<br>

@Before 어노테이션이 적용된 method는 모든 테스트 전에 매번 실행되는 method가 됨, @Before는 import 시 jUnit을 이용해야 함<br>
MockMvcRequestBuilders라는 존재를 이용해서 GET 방식의 호출을 하고 이후에는 BoardController의 getList()에서 반환된 결과를 이용해서 model에 담겨진 데이터들을 확인함<br>

아래는 jUnit으로 테스트 한 결과값임, model에 담긴 값이 정상적으로 확인 됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53281561-f3a2b380-376d-11e9-8723-0b2586dd8874.JPG"><br>

MockMvc를 이용해서 parameter의 값을 전달해야 하는 경우에는 아래와 같이 처리함(parameter의 값 전달 시 문자열로만 처리해야 함<br>
아래는 BoardController의 내용<br>
<img src="https://user-images.githubusercontent.com/44331989/53281880-27cca300-3773-11e9-9668-81a86c176f34.JPG"><br>
url Mapping을 "/board/register"로 하고 parameter로 BoardVO 객체, bno의 값도 전달하기 위해서 RedirectAttributes객체를 받았다<br>

아래는 위의 resister() method를 테스트 하기 위한 테스트 코드임<br>
MockMvcRequestBuilders.post().param("name", "value")이런식으로 추가하면 됨<br>
<img src="https://user-images.githubusercontent.com/44331989/53282001-1ab0b380-3775-11e9-943f-33c6895f02b2.JPG"><br>

위의 테스트 코드를 실행한 결과값은 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/53282025-5c415e80-3775-11e9-93a5-a670f6385d72.JPG"><br>

아래 이미지처럼 실제로 DB에도 정상적으로 등록이 된것을 확인 할 수 있음<br>
<img src="https://user-images.githubusercontent.com/44331989/53282067-dffb4b00-3775-11e9-84c5-e7572b5b69d6.JPG"><br>

#### CDATA
mybatis의 xml파일에서 CDATA 섹션 사용하는 경우가 종종 있는데 이는 XML에서 사용할 수 없는 부등호를 사용하기 위함<br>
XML을 사용하는 경우에는 '<, >' 이런 기호들은 태그로 인식을 하는데 이로 인해 생기는 문제를 막기위해서 CDATA로 감싸는 것임<br>
참조 url:
https://epthffh.tistory.com/entry/Mybatis-%EC%97%90%EC%84%9C-CDATA-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0 <br>

<hr>

#### pagination
이건 스프링에만 국한된 기술은 아니고 웹 전반적으로 쓰이는 기술임<br>
화면에 페이지네이션 처리를 하기 위해서는 우선적으로 크게 다음과 같은 정보들이 필요함<br>
* 현재 페이지 번호(page)
* 이전과 다음으로 이동 가능한 링크의 표시 여부(prev, next)
* 화면에서 보여지는 페이지의 시작 번호와 끝 번호(startPage, endPage)

#### 오라클을 기준으로 설명
1. 인라인뷰에서 rownum이 힌트를 이용해서 DESC 정렬로 1번 ~ 20번까지 데이터를 구함<br>
<img src="https://user-images.githubusercontent.com/44331989/54081290-ab76ba00-4345-11e9-9f39-52fd7b0ac041.JPG"><br>

2. 바깥쪽 SELECT문에서 인라인뷰에서 나온 1 ~ 20번까지의 데이터 중 조건이 rn이 10보다 큰 데이터만 구함(인라인뷰의 결과중 11번부터 20번까지의 데이터만 조회됨<br>
(BNO:10589495 ~ BNO:10589486의 데이터에 해당됨)화면에서 2페이지에 해당되는 데이터임)<br>
<img src="https://user-images.githubusercontent.com/44331989/54081339-d57cac00-4346-11e9-984a-b7fc6ea50268.JPG"><br>

이를 mybatis에서 쿼리로 하면 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/54081392-a0bd2480-4347-11e9-804b-3ff00e836fd5.JPG"><br>

### 페이지네이션처리를 위한 공식 <br>
#### 페이징의 끝번호(endPage)계산 공식 <br>
#### 한페이지에 보여줄 게시글의 갯수는 10개라고 가정 <br>
<img src="https://user-images.githubusercontent.com/44331989/54169570-8b1a3d00-44b6-11e9-899b-4f0ee1662438.JPG"><br>

끝페이지(endPage)는 전체 데이터수(total)에 의해 영향을 받음, 예를 들어 10개씩 보여주는 경우 전체 데이터 수가 80개라고 가정하면 끝번호는 10이 아닌 8이 되어야 함<br>
그러므로 끝번호(endPage)와 한페이지당 출력되는 데이터의 수(amount)의 곱이 전체데이터 수보다 크다면 끝번호는 다시 전체 데이터를 이용해 다시 계산되어야 함<br>
먼저 전체 데이터 수(total)를 이용해서 실제 끝 페이지(realEnd)가 몇 번까지 되는지를 계산한 뒤 만일 실제 끝 페이지가 구해둔 끝번호보다 작다면<br>
실제 끝페이지값을 끝페이지값에 대입해야 함<br>
이전(prev)의 경우는 시작번호(startPage)가 1보다 큰 경우라면 존재하면 됨, 시작번호가 1보다 큰 경우라면 시작번호가 11인 경우부터 해당됨<br>
다음(next)의 경우는 실제 끝번호(realEnd)가 끝번호(endPage)보다 큰 경우에만 존재하면 됨<br>
위와 같이 back단(java)에서 구한 데이터를 가지고 controller에서 model에 담은 뒤 화면단(jsp)으로 보낸 후 화면단에서 해당 데이터를 표시해주면 됨<br>

### UriComponentsBuilder(서버단에서 동적으로 URI 생성)
게시판에서 검색한 결과값이 글 수정, 조회를 해도 계속 페이지에 결과값이 유지되어야 하는 경우가 필요함<br>
이 때 화면에서 form에 input hidden 속성으로 데이터를 담아서 javascript로 동적으로 form action을 변경시켜서 GET방식으로 전송하는데 이 작업을 줄여줄 수 있는게<br>
UriComponentsBuilder 클래스임, 사용방법은 아래와 같음<br>
아래는 검색조건을 유지하는 Criteria class인데 여기에 UriComponentsBuilder를 이용해서 parameter를 연결해서 링크를 생성하는 기능을 추가할 수 있음<br>
<img src="https://user-images.githubusercontent.com/44331989/54660879-79badb80-4b1b-11e9-8e65-d0cd2532a391.PNG"><br>
getListLink()는 페이지번호, 보여줄 페이지 수, 검색타입, 검색 키워드를 parameter의 값으로 넣은 뒤 uri로 변환한 뒤 return한다.<br>

위에서 반환한 uri는 controller에서 아래와 같이 사용하면 됨<br>
<img src="https://user-images.githubusercontent.com/44331989/54661308-00bc8380-4b1d-11e9-8b25-165f72b3f573.PNG"><br>
페이지 수정 후 /board/list로 redirect하면서 cri.getListLink()를 더한값을 return한다<br>

결과는 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/54661508-d1f2dd00-4b1d-11e9-9864-7935ced6a9f6.PNG"><br>
/board/list 뒤에 GET방식과 흡사하게 parameter의 값들이 전달된 것이 확인됨<br>
가장 편리한 점은 한글 처리에 신경 쓰지 않아도 된다고 하고, 주로 javascript를 사용할 수 없는 상황에서 링크를 처리해야 하는 경우에 사용된다고 함<br>

#### REST 방식
Rest는 "Representational State Transfer"의 약어로 하나의 URI는 하나의 고유한 리소스(Resource)를 대표하도록 설계된다는 개념에<br>
전송방식을 결합해서 원하는 작업을 지정함<br>
스프링에서는 @RequestMapping, @ResponseBody와 같은 REST방식의 데이터 처리를 위한 여러 종류의 어노테이션과 기능을 제공함<br>
REST와 관련해서 알아둘 필요가 있는 어노테이션은 아래와 같음<br>
* @RestController: Controller가 REST방식을 처리하기 위함을 명시함<br>
* @ResponseBody: 일반적인 JSP와 같은 뷰로 전달되는 것이 아니라 데이터 자체를 전달하기 위한 용도에 사용<br>
* @PathVariable: URL 경로에 있는 값을 parameter로 추출하려고 할 때 사용<br>
* @CrossOrigin: Ajax의 크로스 도메인 문제를 해결해주는 어노테이션<br>
* RequestBody: JSON 데이터를 원하는 타입으로 바인딩 처리<br>

### REST Conroller의 반환 타입
@RestController는 JSP와 달리 순수한 데이터를 반환하는 형태이므로 다양한 포맷의 데이터를 전송할 수 있음<br>
주로 많이 사용하는 형태는 일반 문자열이나 JSON, XML 등을 사용함<br>
<img src="https://user-images.githubusercontent.com/44331989/54744063-51110f80-4c09-11e9-800d-2f623e58a31b.PNG"><br>
위 Controller는 일반 문자열을 반환하는 예제임, @Getmapping에 사용된 produces 속성은 해당 메서드가 생산하는 MIME 타입을 의미함<br>
위와 같이 문자열로 직접 지정할 수도 있고, produces 메서드내의 MediaType이라는 클래스를 이용할 수도 있음<br>

위의 실행결과는 아래와 같음<br>
<img src="https://user-images.githubusercontent.com/44331989/54747846-51aea380-4c13-11e9-968f-369e1453c279.PNG"><br>

개발자도구를 이용해 브라우저에 전송된 데이터의 produces 속성값이 컨트롤러에서 지정한 'text/plain'인걸 확인할 수 있음<br>
<img src="https://user-images.githubusercontent.com/44331989/54747984-b2d67700-4c13-11e9-8739-bbca1f96f7e3.PNG"><br>

REST Controller의 반환타입은 문자열, VO 객체, 컬렉션타입의 객체(list, map 등)도 가능함<br>

### ResponseEntity 타입
REST 방식으로 호출하는 경우는 화면 자체가 아니라 데이터 자체를 전송하는 방식으로 처리되기 때문에 데이터를 요청한 쪽에서<br>
정상적인 데이터인지 비정상적인 데이터인지를 구분할 수 있는 확실한 방법을 제공해야 하는데<br>
이때 ResponseEntity는 데이터와 함께 HTTP 헤더의 상태 메시지 등을 같이 전달하는 용도로 사용함<br>
HTTP의 상태코드와 에러 메시지등과 함께 데이터를 전달할 수 있기 때문에 받는 입장에서 확실히 결과를 알 수 있음<br>
아래는 예제임<br>
<img src="https://user-images.githubusercontent.com/44331989/54748369-ca622f80-4c14-11e9-9d90-768813f47f51.PNG"><br>
check method는 반드시 parameter의 값으로 'height', 'weight'를 전달 받음, 이 때 height의 값이 150보다 작으면<br>
502(bad gateway)상태코드와 데이터를 전송, 그렇지 않다면 200(ok)코드와 데이터를 전송함<br>

아래는 브라우저에서 호출한 결과값임<br>
<img src="https://user-images.githubusercontent.com/44331989/54748617-7c99f700-4c15-11e9-8f01-2cc32c101f6f.PNG"><br>
위는 브라우저에 전송된 데이터이고

<img src="https://user-images.githubusercontent.com/44331989/54748701-b3700d00-4c15-11e9-831e-53206ce61544.PNG"><br>
위는 브라우저에 전송된 상태코드임, parameter값으로 height가 150이므로 정상적으로 200상태코드가 전송되었음<br>

### @RestController에서 parameter
RestController는 기존의 @Controller에서 사용하던 일반적인 타입이나 사용자가 정의한 타입(class)을 사용함<br>
여기에 추가로 몇 가지 어노테이션을 이용하는 경우가 있음<br>
@PathVariable: 일반 컨트롤러에서도 사용이 가능하지만 REST 방식에서 자주 사용된다고 함, URL 경로의 일부를 paraemter로 사용할 때 이용<br>
@RequestBody: JSON 데이터를 원하는 타입의 객체로 변환해야 하는 경우에 주로 사용<br>

#### @PathVariable
예전에 URL의 '?' 뒤에 추가되는 쿼리 스트링(query string)이라는 형태로 parameter를 이용해서 전달되던 데이터들이<br>
REST 방식에서는 경로의 일부로 차용되는 경우가 많음<br>

Spring MVC에서는 @PathVariable 어노테이션을 이용해서 URL 상에 경로의 일부를 parameter로 사용할 수 있음<br>
http://localhost:9595/sample{sno} <br>
http://localhost:9595/sample/{sno}/page/{pno} <br>
위의 URL에서 '{}'로 처리된 부분은 Controller의 method에서 변수로 처리가 가능함<br>
@PathVariable은 '{}'의 이름을 처리할 때 사용함, REST 방식에서는 URL 자체에 데이터를 식별할 수 있는 정보들을 표현하는 경우가 많으므로<br>
다양한 방식으로 @PathVariable이 사용됨<br>
아래는 예제 method임<br>

<img src="https://user-images.githubusercontent.com/44331989/54749452-e74c3200-4c17-11e9-8160-8aee76f03124.PNG"><br>
@PathVariable을 적용하고 싶은 경우에는 '{}'를 이용해서 변수명을 지정하고, @PathVariable을 이용해서 지정된 이름의 변숫값을 얻을 수 있음<br>
값을 얻을 때에는 int, double과 같은 기본 자료형은 사용할 수 없음<br>

아래는 브라우저에서 호출한 결과값임<br>
<img src="https://user-images.githubusercontent.com/44331989/54749599-6477a700-4c18-11e9-95c7-51f809c4192e.PNG"><br>
위와 같이 호출하면 cat과 pid 변수의 값으로 처리되는 것을 확인할 수 있음<br>

#### @RequestBody
@RequestBody는 전달된 요청(Request)의 내용(body)을 이용해서 해당 parameter의 타입으로 반환을 요구함<br>
내부적으로 HttpMessageConverter 타입의 객체들을 이용해서 다양한 포맷의 입력 데이터를 변환할 수 있음<br>
대부분의 경우에는 JSON 데이터를 서버에 보내서 원하는 타입의 객체로 변환하는 용도로 사용되지만, 경우에 따라서는 원하는 포맷의 데이터를 보내고<br>
이를 해석해서 원하는 타입으로 사용하기도 함<br>
변환을 위한 예제를 위해서 VO로 사용할 Ticket 클래스를 생성했다고 가정함<br>

아래는 Ticket을 사용하는 Controller 예제임<br>
<img src="https://user-images.githubusercontent.com/44331989/54750101-efa56c80-4c19-11e9-9d90-32dcf6587278.PNG"><br>
위의 method는 다른 예제와 다르게 @PostMapping이 적용되었는데 이것은 @RequestBody가 말 그대로 요청(Request)한 내용(body)을 처리하기 때문에<br> 일반적인 parameter 전달방식을 사용할 수 없기 때문임<br>
이 경우에는 JUnit 기반의 테스트에서는 MockMVC를 이용할 수 있음<br>

아래는 jUnit 기반의 테스트 예제임<br>
<img src="https://user-images.githubusercontent.com/44331989/54750351-b0c3e680-4c1a-11e9-837f-c549357a6c2d.PNG"><br>
위의 testConvert()는 SampleController에 작성해 둔 convert()를 테스트하기 위해 작성한 것임<br>
SampleController의 convert()는 JSON으로 전달되는 데이터를 받아서 Ticket 타입으로 변환함, 이를 위해서 해당 데이터가 JSON이라는 것을<br>
명시해 줄 필요가 있어서 MockMvc의 contentType으로 전달하려는 데이터가 JSON이라는 것을 명시함<br>
Gson 라이브러리는 Java 객체를 JSON 문자열로 변환하기 위해서 사용하였음<br>

아래는 위의 코드를 실행한 결과값임<br>
<img src="https://user-images.githubusercontent.com/44331989/54750620-a7874980-4c1b-11e9-9ec9-568dc0f5083f.PNG"><br>
첫번 째는 테스트코드에서 JSON타입으로 담은 데이터이고, 두번 째는 테스트코드에서 전달한 JSON 타입 문자열을 Ticket타입으로 객체로 변환한 값임<br>

























	





















































<hr>
  
<h2>20190219 TIL 출처: 코드로 배우는 스프링 웹 프로젝트(개정판)예제</h2>
  
  
  
  
  
  
  
  
  
 
