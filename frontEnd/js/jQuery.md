# jQuery 관련
## jQuery 정규표현식 이용해서 키보드 입력값 제한
### 사용자가 입력한 값을 체크해서 알림창을 띄우거나 할 때 jQuery의 keyup()을 사용하면 됨

keyup 이벤트는 키보드의 키를 눌렀다 뗄 때 이벤트를 발생 시킴 <br>
![image](https://user-images.githubusercontent.com/44331989/142164124-9115efe9-d6a7-4f09-82d7-7a972016232c.png) <br>

해당 이벤트는 포커스를 가질 수 있는 요소에만 발생 시킬 수 있음(ex : input) <br>
대부분의 form 요소들에서 사용가능하다고 함 <br>

ex) 사용자가 입력한 값이 한글이 아닌 값일 경우를 체크해서 경고창을 띄우는 코드 예시
~~~
	// 정규표현식 -> 한글만 입력 체크
	function check_input() {
		$("#item").keyup(function() {
        	// 입력값에 숫자, 영문, 특수문자가 올 경우 경고창 표시 후 ""으로 치환
			var regexp = /[a-z0-9]|[ \[\]{}()<>?|`~!@#$%^&*-_+=,.;:\"'\\]/g;
			v = $(this).val();
			if (regexp.test(v)) {
                alert("내용을 입력 해주세요\n한글만 입력가능 합니다.");
                $(this).val(v.replace(regexp, ""));
            }
		});
	}
~~~

출처 : https://api.jquery.com/keyup <br> https://findfun.tistory.com/284 <br>
