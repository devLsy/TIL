# CSS 관련 TIL
## 20190122 TIL
* 외부 스타일시트와 연결하는 link 요소 <br>
  과거: \<link type="text/css" rel="stylesheet" href="#"> <br>
  HTML5: \<link rel="stylesheet" href="#"> type속성이 사라짐 <br>

* CSS 유효성 검사기 <br>
url: http://jigsaw.w3.org/css-validator/ <br>

* RGB(Red, Green, Blue) - 삼원색 : <br>
웹 컬러는 색을 구성할 때 빨간색, 녹색, 파란색이 얼마나 사용되는지에 따라 명시함, 각 값을 0 ~ 100%까지 명시할 수 있고 이들 모두를 합치면 최종색에 도달함 ex)빨간색 100%, 녹색 100%, 파란색 100%를 혼합하면 흰색이 됨 -- RGB가 얼마나 사용되었는지 브라우저에게 알려주기위한 방법이 hex 코드임<br>
css에서 red, blue 이런식으로 정의하는 색은 150가지의 색으로 한정됨 <br>
일반적으로 hex 코드(#fc1257)가 웹 컬러를 명시하는 가장 일반적인 방법임 <br>
hex 코드는 #으로 시작하고 #cc(빨간색)66(녹색)00(파란색)을 나타냄.. 여기서 빨간색인 cc는 204를 나타냄 <br>
<img src="https://user-images.githubusercontent.com/44331989/51522035-9ca67780-1e6b-11e9-910f-1666bca011e0.jpg"> <br>
<strong>hex 코드 계산법 </strong><br>
1. cc를 분리해서 가장오른쪽에 있는 숫자의 10진수 값을 적는다 => 12 <br>
2. 왼쪽의 c를 가져다가 10진수로 바꾼 뒤(12) * 16을 한다 => 12 * 16 = 192 <br>
3. 마지막으로 두수를 더한다 => 192 + 12 = 204 <br>
나머지 녹색, 파란색도 동일한 방법으로   
  
  
  
  
  
<p><br>
<p><br>
  
  
<h2>출처: Head First HTML and CSS(개정판)</h2>
