# Spring MVC
## 20181218 20:06
1. Client 요청이 들어오면 Dispatcher Servlet이 가장 먼저 요청을 받는다. 주로 *.do
의 형태로 오는 요청을 처리한다.
2. HandlerMapping이 요청에 해당하는 Controller를 리턴한다. @RequestMapping의
형태로 요청되는 URL을 처리할 Controller 메소드가 Mapping된다.
3. Controller는 비즈니스 로직을 호출하고 전달 받은 결과를 ModelAndView에 반영
하여 리턴한다.
4. ViewResolver는 어떤 형태로 출력할 것인지 형태를 결정하고, view name을 받아 해당되는 view객체를 리턴한다. 주로 JSP가 활용되며, XML, JSON등 활용가능하다.
5. View는 Model 객체를 받아 결과를 출력한다.
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50151207-8e60bb00-0303-11e9-847e-f83a69a32834.JPG" width = "800">
</div>Spring MVC의 구성 흐름<br><p>
----------------------------------------------------------------------------------------------------------------------------------------  
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50151525-7fc6d380-0304-11e9-892b-7d031ce9b28e.JPG">
</div><br>
<div>
<img src = "https://user-images.githubusercontent.com/44331989/50151634-cae0e680-0304-11e9-991f-48c327cf34c3.JPG">
</div><br>




























* 출처: 전자정부프레임워크 퍼스트북
