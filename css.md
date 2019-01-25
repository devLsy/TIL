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

<strong>레이아웃</strong> <br>
* 떠 있는 레이아웃: float 사용, 단점은 div전체를 옮겨야 한다는 점과 두개의 컬럼을 만들수 없다는 점<br> 
* 젤로 레이아웃: 페이지에 있는 모든 콘텐츠 주위를 고정된 크기의 div로 감싸고 고정된 레이아웃을 만든 후, margin을 auto로 설정해서 좌우에 동일한 너비의 margin이 있고 가운데 content영역이 있다는 점(많은 블로그가 이런 형태), 단점은 콘텐츠가 브라우저창 전체를 채우려고 확장되지 않는 다는 점이지만 이를 단점이라고 생각하지 않는 사람도 많다고 함 <br>

url: https://kareninssmile.tistory.com/27 <br>


<strong>z-index</strong> <br>
<img src="https://user-images.githubusercontent.com/44331989/51593414-76e5a500-1f35-11e9-9126-97c96ab595d1.JPG"> <br>
<strong>출처: http://div.or.kr/css-studying/z-index%20%EC%86%8D%EC%84%B1</strong><br>

* CSS table display <br>
display 속성값으로 table, table-row, table-cell등을 사용해서 요소를 표(table)처럼 표현하는 방법 <br>
참조 url: https://www.codingfactory.net/10776 <br>
① 테이블 전체를 나타내는 div를 생성 후 이 div내에 행과 컬럼을 중첩시킴 <br>
② 테이블의 행별로 행 콘텐츠를 포함할 div를 생성 <br>
③ 컬럼처럼 동작할 블록요소(div)를 위의 div안에 넣음 <br>
④ CSS에서 전체 테이블에 해당하는 div의 display를 table로 설정, 테이블의 행에 해당하는 div의 display를 display-row로 설정, 행의 컬럼에 해당하는 div의 display를 display-cell로 설정 <br>
<h1>20190124 TIL</h1> <br>
테이블에서의 의사클래스 ==> nth-child <br>
css에서 선택자:nth-child(even) or 선택자:nth-child(odd) 이런식으로 하고 배경색을 바꾸거나 하는데 사용 <br>
tr:nth-child(odd) {background-color: green;} ==> 홀수문단만 녹색 배경 적용 <br> 
혹은 숫자 n을 이용하는 간단한 표현식으로도 가능 ==> tr:nth-child(2n) or (2n+1) {background-color: green;} <br>

li요소 스타일 꾸미기 <br>
li요소에 사용자 정의 마커 사용하는 방법 <br>
css의 list-style-image 속성을 사용해서 이미지를 마커로 설정할 수 있음 <br>
li {list-style-image: url(images/image.gif)} <br>
<hr>
------------------
<h1>20190125 TIL</h1>
일반적 레이아웃에서 폼은 표 형태이므로, 폼 프레젠테이션을 디자인하는 데 CSS table display 레이아웃을 사용하는게 좋음 <br>
<p></p>
<strong>의사요소 선택자 사용법:</strong> <br>
p:first-letter {font-size:3em;}  ==> 문단의 첫글자를 크게 만듬 <br>
p:first-line {font-style:italic;}  ==> 첫번 째 줄을 이탤릭체로 만듬 <br>
<strong>속성 선택자 사용법:</strong> <br>
img[width] {border: black thin solid;} ==> html에서 width속성을 가진 이미지를 모두 선택함 <br>
img[height="300"] {border: red thin solid;}  ==> html에서 height속성이 300인 모든 이미지 선택 <br>
img[alt~="flowers"] {border: #cc thin solid;}  ==> html에서 alt속성에 flowers라는 단어가 포함된 모든 이미지 선택 <br>
<strong>형제요소를 이용한 선택자 사용법: </strong> <br>
선행요소(h1) + 형제요소 형태로 작성: <br>
h1+p {font-style: italic;} ==> , h1요소 다음에 나오는 모든 문단 선택 <br>
<h3>선택자 결합 & 복잡한 선택자 사용법</h3> 
① 아래와 같이 선택하고자 하는 요소에 대한 구문을 먼저 정의함 <br>
div#greentea > blockquote: 아이디가 greentea인 div의 자손선택자 중 blockquote를 선택 <br>
② 그리고 선택하려는 요소를 놓음 <br>
div#greentea > blockquote p(요소): blockquote의 구문에 선택하려는 요소인 p요소를 추가(이 p요소는 blockquote의 자손이어야 하며, greentea란 id를 가진 div의 자식이어야 함) <br>
③ 그리고 의사클래스나 의사요소를 명시함 <br>
div#greentea > blockquote p:first-line {font-style: italic;} ==> id가 greentea인 div요소의 자손 중 blockquote요소의 p요소 중 첫번 째 줄만 선택 <br>




 






  
  
<p><br> 
<p><br>
  
    
<h2>출처: Head First HTML and CSS(개정판)</h2>
