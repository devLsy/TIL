# 람다 표현식(Lambda Expression)
![image](https://user-images.githubusercontent.com/44331989/125906063-ded2a3a9-f677-4d6c-a81d-b8948250e398.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125906101-1e098bf8-c692-49eb-ada4-2408dc6e35d5.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125906237-75d2a348-7c9f-4b5a-bde4-bd57b86647f3.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125906452-e7e1e388-2117-434e-9e2a-624a4343eb12.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125906515-a411e88c-092e-49ec-80f4-dfd263dfc9c6.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125906772-ab7431d1-6156-4d30-b7c6-c1c2c2a6f6fe.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125907032-f2669709-689c-4fc6-a10a-600d77d9d6fe.png) <br>
![image](https://user-images.githubusercontent.com/44331989/125907408-6e87a82d-6639-4ec2-b0be-faf3308957a5.png) <br>
## 람다식 구현 핵심
* 람다식을 사용하기 위해서는 구현할 인터페이스(반드시 함수적 인터페이스만 가능)가 필요 -> @FunctionallInterface 어노테이션으로 명시 가능 <br>
* @FunctionallInterface가 적용된 인터페이스는 한개의 추상 메소드만 선언할 수 있음, 메서드 추가 시 컴파일 에러 발생 <br>
~~~
문법
(타입 매개변수, ...) -> {실행문;...}
~~~
출처 : https://multifrontgarden.tistory.com/124 <br>
