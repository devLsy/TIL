# Eclipse 관련 TIL
### 이클립스에서 tomcat 실행 시 console에 찍히는 로그를 파일로 남기는 방법
<h4>이클립스에서 tomcat의 overview 실행 후 open launch configuration 선택<br></h4>
<img src="https://user-images.githubusercontent.com/44331989/99866880-2fdf9e00-2bf8-11eb-847b-1608406d33fe.PNG" alt="tomcat_overview"> <br>

<h4>우측의 common - Output File 선택 후 File System(경로 직접 지정) 혹은 Workspace(말 그대로 이클립스의 Workspace)에 로그를 남길 파일명을 지정 후 ok</h4>
<img src="https://user-images.githubusercontent.com/44331989/99866976-13903100-2bf9-11eb-9859-73c891ce0e9c.png" alt="tomcat_edit_configuration"> <br>

<h4>tomcat 실행 시 로그파일을 남긴다는 메시지가 출력되고, 해당 경로로 가면 로그파일이 생성됨, 로그파일 열면 콘솔에 찍히는 내용이 저장된걸 볼 수 있음</h4> <br>
<img src="https://user-images.githubusercontent.com/44331989/99867119-09226700-2bfa-11eb-996d-d23d89b1e02c.png" alt="tomcat_log파일"> <br>
참조 블로그 : https://mine-it-record.tistory.com/208 <br>
<hr>

### workspace 변경
eclipse - file - switch workspace - others 선택해서 변경할 폴더를 선택해서 변경하면 됨(아래 이미지 참조) <br>
<img src="https://user-images.githubusercontent.com/44331989/106349012-cffb2700-630d-11eb-83d3-8f3c0dabc71f.png"> <br>
이 경우 기존의 환경이 초기화가 되는데 기존과 동일한 환경으로 작업하고 싶을 경우 기존 워크스페이스의 ".metadata" 폴더를 변경할 폴더에 복사하면 됨 <br>
그리고 tomcat 설정도 동일하게 사용하려면 기존 workspace의 Servers 폴더도 복사 하면 됨 <br>
그냥 기존의 workspace 폴더안의 .metadata와 Servers폴더를 복사해서 사용하자(아래 이미지 참조) <br>
<img src="https://user-images.githubusercontent.com/44331989/106349061-1ea8c100-630e-11eb-9840-5e70e4e3083d.png"> <br>
출처 : https://frog-hindleg.tistory.com/218

