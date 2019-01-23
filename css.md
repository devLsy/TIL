# CSS 관련 TIL
## 20190122 TIL
* 외부 스타일시트와 연결하는 link 요소 <br>
  과거: \<link type="text/css" rel="stylesheet" href="#"> <br>
  HTML5: \<link rel="stylesheet" href="#"> type속성이 사라짐 <br>

* CSS 유효성 검사기 <br>
url: http://jigsaw.w3.org/css-validator/ <br>

* RGB(Red, Green, Blue) - 삼원색 : <br>
웹 컬러는 색을 구성할 때 빨간색, 녹색, 파란색이 얼마나 사용되는지에 따라 명시함, 각 값을 0 ~ 100%까지 명시할 수 있고 이들 모두를 합치면 최종색에 도달함 ex)빨간색 100%, 녹색 100%, 파란색 100%를 혼합하면 흰색이 됨 -- RGB가 얼마나 사용되었는지 브라우저에게 알려주기위한 방법이 hex 코드임<br>

css는 검정색, 흰색, 빨간색, 파란색 등을 포함한 16가지 기본색과 확장색 150가지밖에 지원하지 않음<br>
일반적으로 hex 코드(#fc1257)가 웹 컬러를 명시하는 가장 일반적인 방법임 <br>
hex 코드는 #으로 시작하고 #cc(빨간색)66(녹색)00(파란색)을 나타냄.. 여기서 빨간색인 cc는 204를 나타냄 <br>
<img src="https://user-images.githubusercontent.com/44331989/51522035-9ca67780-1e6b-11e9-910f-1666bca011e0.jpg"> <br>
<strong>hex 코드 계산법 </strong><br>
1. cc를 분리해서 가장오른쪽에 있는 숫자의 10진수 값을 적는다 => 12 <br>
2. 왼쪽의 c를 가져다가 10진수로 바꾼 뒤(12) * 16을 한다 => 12 * 16 = 192 <br>
3. 마지막으로 두수를 더한다 => 192 + 12 = 204 <br>
나머지 녹색, 파란색도 동일한 방법으로 계산함 <br>

* body {background-color: rgb(80%, 40%, 0%)}   <== 빨간색, 녹색, 파란색 비율을 사용해서 명시  <br>
* body {background-color: rgb(204, 102, 0)}    <== 0에서 255까지의 숫자를 사용해서 빨간색, 녹색, 파란색의 양을 명시 <br>
* body {background-color: #cc6600;}    <== hex 코드를 사용해서 명시()<br>

* 한개 이상의 요소에 스타일을 주고자 할 때는 class사용, 꾸미고자 하는 스타일이 유일하고 페이지 내에서 오직 한개라면 id를 사용하는 걸 권장 <br>
<hr>
<h1>20190123 TIL</h1>
<h3>css 속성 속기법(ex):</h3> <br>
    padding-top: 0px; <br>
    padding-right: 20px; <br>
    padding-bottom: 30px; <br>
    padding-left: 10px;   <br>
==> padding: 0px(위); 20px(오른쪽); 30px(아래); 10px(왼쪽); <br>
padding과 margin 둘다 같은 속기법 사용 가능 <br>
만일 padding이나 margin이 모든 방향에서 같은 값을 가지면 padding: 20px(위, 오른쪽, 아래, 왼쪽 모두 같은 값) 이런식으로 작성할 수 있음 <br>

padding이나 margin을 단축하는 또 다른 방법: <br>

위아래가 동일하고 우측과 좌측이 동일한 경우 ==> padding: 0px(위, 아래) 20px(우,좌); <br>
<strong>속기법 암기를 위해서는 속기법 순서가 시계방향으로 된다고 생각..위 -> 오른쪽 -> 아래 -> 왼쪽 </strong><br>

* border 속성은 margin이나 padding보다 더 유연한 속기법: <br>
border-width: thin; <br>
border-style: solid <br>
border-color: red <br>
==> border: solid thin red(순서 변경가능); <br>

* 배경에 대한 속기법 <br>
background-color: white; <br>
background-image: url(images/image.gif); <br>
background-repeat: repeat-x; <br>
==> background: white url(images/image.gif) repeate-x(순서 변경 가능); <br>
<strong>속기법을 사용하는 이유? CSS 파일의 크기를 줄여주는 이점이 있고, 입력내용을 줄여 빨리 작성 가능, 하지만 정확하지 않은 값으로 작성할 경우 디버그하기 좀 더 어려운 단점이 있으니 잘 사용할 것을 권장</strong>

* 의사클래스(pseudo-class) <br>
<strong>CSS에서 의사클래스는 선택하고자 하는 HTML요소의 특별한 '상태(status)'를 명시할 때 사용</strong> <br>
<string>문법: 선택자:의사클래스이름 {속성: 속성값;}</strong>
ex)a선택자의 의사클래스 사용 예제<br>
a:link {color: green} ==> 이 선택자는 방문하지 않은 상태일 때의 링크에 적용됨 <br>
a:visited {color: red;}  ==> 이 선택자는 방문했을 때의 링크에 적용됨 <br>
a:hover {color: blue;}  ==> 이 선택자는 마우스를 링크에 올려 놓을 때 적용됨 <br> 
그밖에 많은 의사 클래스 종류가 있음 <br>


  
  
<p><br> 
<p><br>
  
    
<h2>출처: Head First HTML and CSS(개정판)</h2>
