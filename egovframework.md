# Spring MVC(전자정부프레임워크 기준 )
## 20181218 20:06
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
  
  
  
  






























* 출처: 전자정부프레임워크 퍼스트북
