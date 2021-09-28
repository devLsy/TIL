# SVN
## 본사 SVN Server는 윈도우OS입니다.
SVN Server의 프로젝트 목록을 보기 위해서 https://121.165.242.176/svn 으로 접속 후 <br>
svn 계정으로 로그인 하면 아래처럼 확인이 가능합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116394931-7481fc00-a85e-11eb-8f7a-c092ace3498c.png) <br>

## 이클립스에서 SVN Repositories 연동 후 project Check Out
![image](https://user-images.githubusercontent.com/44331989/116504853-4949ec00-a8f4-11eb-9e87-1467e1dd7d5d.png) <br>
SVN Repositories - new - Repository location 실행 <br>

https://121.165.242.176/svn 목록에서 내가 다운받을 프로젝트가 해당된 url 입력 <br>
![image](https://user-images.githubusercontent.com/44331989/116505047-d4c37d00-a8f4-11eb-9dfd-36bfbeb22e18.png) <br>
Save authentication 체크해서 다음에는 비밀번호를 물어보지 않도록 할 수 있습니다. <br>
Finish를 눌러서 끝냅니다. <br>

![image](https://user-images.githubusercontent.com/44331989/116505315-7f3ba000-a8f5-11eb-8a25-8c72e3df91aa.png) <br>
~~~
본사 SVN Server project 구조 : 
1 depth : project 
2 depth : trunk 
3 depth : project 선택 후 우클릭 Check Out을 선택합니다. <br>
~~~

해당 프로젝트가 maven project인 경우 최초 check out 받은 경우 설정을 보면 project facet을 설정할 수 없게 나옵니다. <br>  

![image](https://user-images.githubusercontent.com/44331989/116506872-19511780-a8f9-11eb-8223-174cab1370cb.png) <br>
위처럼 maven project로 convert 해줍니다. <br>

![image](https://user-images.githubusercontent.com/44331989/116507570-739ea800-a8fa-11eb-8b48-67d7b5cf5b03.png) <br>
위처럼 maven으로 convert하면 project facets를 설정할 수 있습니다. <br>

project facets와 java build path의 jdk 버전, tomcat 버전을 맞춰 줍니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116507669-b3fe2600-a8fa-11eb-9d29-2fcf75905e69.png) <br>
그리고 프로젝트 세팅하면서 Problems탭에 에러가 표시되면 잡아줍니다. <br>
잘 작동되는 프로젝트를 체크아웃 받은 경우 정답은 아니지만 <br>
보통 maven lib 문제인 경우가 많습니다.(라이브러리를 찾지 못하거나 pom.xml에 선언되지 않는 라이브러리이거나..) <br>
라이브러리 꼬인 경우는 이클립스를 종료 후 maven dependency 경로의 repository를 지웁니다. <br>

![image](https://user-images.githubusercontent.com/44331989/116508010-6635ed80-a8fb-11eb-9737-60887b1d261b.png) <br>
어차피 다시 빌드하면 다시 다운 받습니다. <br>

repository 삭제 후 다시 이클립스 실행합니다. <br>
자동 빌드설정된 경우 다시 라이브러리를 다운 받을겁니다. <br>
이 방법으로 해결되는 경우도 있고 project - maven - update(force update of snapshots/Releases) 해서 해결 되는 경우도 있습니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116508229-d5134680-a8fb-11eb-9a7b-b9db8af1c9d2.png)
![image](https://user-images.githubusercontent.com/44331989/116508248-dfcddb80-a8fb-11eb-8ed6-a77d6e7457be.png)

간혹 프로젝트가 pom.xml에 설정안한 lib를 참조하는 경우 아래처럼 에러가 발생합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116508529-8b772b80-a8fc-11eb-8a47-28484d44747b.png) <br>

이 경우 아래처럼 java build path에서 jar를 추가 해줍니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116508576-a3e74600-a8fc-11eb-8347-442a284240c5.png) <br>

보통 수동으로 lib를 설정하는 경우 해당 경로는 "/src/main/webapp/WEB-INF/lib" 입니다. <br>
해당 경로의 jar를 직접 추가 해 줍니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116508723-ec066880-a8fc-11eb-8a87-58fa473c0c7c.png)
![image](https://user-images.githubusercontent.com/44331989/116508737-f1fc4980-a8fc-11eb-90ed-80c24a586891.png)

![image](https://user-images.githubusercontent.com/44331989/116509020-81a1f800-a8fd-11eb-8411-8251bcdc001f.png)

## 윈도우 SVN 설치 및 세팅
visual svn 설치
egovframework에 동봉된 svn을 설치해도 되고 visual svn 홈페이지(https://www.visualsvn.com/visualsvn/download/)에서 <br>
다운로드 받아도 됩니다. 설치할 pc의 bit에 맞는 버전을 설치 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862258-71f91b00-ac3f-11eb-94dd-43faa0491487.png)
![image](https://user-images.githubusercontent.com/44331989/116862277-7b828300-ac3f-11eb-9545-4eb820ea2827.png)
![image](https://user-images.githubusercontent.com/44331989/116862285-7f160a00-ac3f-11eb-9b86-0bdb613e4dc8.png)
![image](https://user-images.githubusercontent.com/44331989/116862308-86d5ae80-ac3f-11eb-9ebf-4e28a57a61de.png)
![image](https://user-images.githubusercontent.com/44331989/116862333-8ccb8f80-ac3f-11eb-8091-33105b317ecb.png)
![image](https://user-images.githubusercontent.com/44331989/116862400-a40a7d00-ac3f-11eb-8a19-0e91dcea7c0e.png)
![image](https://user-images.githubusercontent.com/44331989/116862403-a66cd700-ac3f-11eb-9581-2df6588ae990.png)

visual svn 실행 시 처음 모습은 아래와 같습니다.<br>
![image](https://user-images.githubusercontent.com/44331989/116862427-b1c00280-ac3f-11eb-814b-d1662d366eef.png)

새로운 repository를 생성합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862603-f9df2500-ac3f-11eb-8175-610ea6249cd2.png)

기본값으로 생성합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862618-fd72ac00-ac3f-11eb-89c3-ae6ad15c0f60.png)

repository name을 설정합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862638-082d4100-ac40-11eb-8498-d661e1201bcb.png)

비어있는 repository로 설정합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862674-167b5d00-ac40-11eb-8da9-7bbfd0ff649b.png)

내부용이니 모든 사람이 읽고 쓰게 권한을 부여합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862721-2430e280-ac40-11eb-8866-4c22daf1fd48.png)

아래처럼 repository가 생성됩니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862840-52aebd80-ac40-11eb-9117-4d70d9189f34.png)

다른 사람도 svn에 접속하게 하기 위해서는 svn의 url을 해당 pc의 ip로 변경해야 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116862927-75d96d00-ac40-11eb-92e4-bcb1cb80a68b.png)

network탭 클릭 후 Server name을  해당 pc의 ip로 변경해줍니다.(이렇게 해야 다른사람도 접속이 가능합니다.) <br>
![image](https://user-images.githubusercontent.com/44331989/116862940-7d007b00-ac40-11eb-8ef3-2a569a19f1d9.png)

그리고 svn 계정을 생성합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116864461-d669a980-ac42-11eb-9dc6-de42c217f87b.png)

id, password를 입력합니다.(앞으로 이 계정으로 로그인 합니다.)<br>
![image](https://user-images.githubusercontent.com/44331989/116864484-de294e00-ac42-11eb-86d5-9e86f2b9f14a.png)

아래처럼 계정이 생성되었습니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116864526-f600d200-ac42-11eb-8888-60215236403f.png)

외부에서 해당 pc의 svn에 접속하게 하기 위해 방화벽(고급보안이 포함된 Windows Defender 방화벽)을 열고 <br>
인바운드 규칙을 추가 해 줍니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863490-5858d300-ac41-11eb-9819-cd5afbe09f27.png)

포트를 선택 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863505-60187780-ac41-11eb-8ecb-faf59de98df6.png)

svn을 설치할 때 설정했던 포트번호를 입력 후 다음을 누릅니다. (기본값 443) <br>
![image](https://user-images.githubusercontent.com/44331989/116863565-76263800-ac41-11eb-85c1-5c2d0d74c8bb.png)

다음을 누릅니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863607-82aa9080-ac41-11eb-9e08-36dc1836f46c.png)
  
다음을 누릅니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863647-8fc77f80-ac41-11eb-90cd-6d8282372d80.png)

방화벽 관련 이름과 설명을 입력 후 마침을 누릅니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863702-a53ca980-ac41-11eb-8583-b4fc22521121.png)

설정한 방화벽을 확인 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863827-d3ba8480-ac41-11eb-8fa3-cf50ea39e122.png)

외부 pc에서 telnet 등 을 이용해서 svn이 설치된 pc의 포트가 허용 되었는지 확인 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863924-f9e02480-ac41-11eb-9db2-eb1927c74b49.png)

해당 repository를 브라우저에서 실행 해봅니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863039-a5887500-ac40-11eb-8e51-d8afa10e72a7.png)

아래처럼 나오면 정상 접속 된겁니다. <br>
고급을 누른 다음 192.168.0.17(안전하지 않음)(으)로 이동을 눌러서 접속합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116863988-17ad8980-ac42-11eb-9797-36ec2bee8f50.png)

아까 만든 계정으로 로그인합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116864120-4fb4cc80-ac42-11eb-8433-b64e84eefaf5.png)

아래처럼 repository를 확인 할 수 있습니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116864979-b981a600-ac43-11eb-936c-4259e06308fa.png)

## eclipse svn 연동
이클립스에서 위에서 만든 svn server를 연동하기 위한 가이드입니다. <br>

svn과 연동하기전에 svn commit 시 ignored 할 리소스들을 선택합니다. <br>
보통 .project, .classpath, .settings 등은 해당 pc의 환경파일이라 commit 안합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116866679-cc49aa00-ac46-11eb-9ff7-4d4719391679.png) <br>
위의 패턴에 해당되는 파일들을 commit 시 제외시킵니다. <br>

이클립스에서 svn과 연동할 프로젝트를 선택 후 우클릭 - team -share project 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/116866165-e8008080-ac45-11eb-8d85-f8cd9a1c25a8.png)

svn 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/116866183-efc02500-ac45-11eb-8abc-ffd6ae699130.png)

새로운 repository 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/116866222-ff3f6e00-ac45-11eb-89a1-9e3f5939b024.png)

svn의 url, user, password를 입력 후 next,(이 때 user, password는 save하는걸 권장) <br>
![image](https://user-images.githubusercontent.com/44331989/116866257-0d8d8a00-ac46-11eb-9592-441e02c9d587.png)

project repository layout은 multiple projects...을 선택 후 next <br>
![image](https://user-images.githubusercontent.com/44331989/116866384-46c5fa00-ac46-11eb-8e89-f8c833425ad0.png)

finish <br>
![image](https://user-images.githubusercontent.com/44331989/116866459-6826e600-ac46-11eb-93ef-63cd61b1a3b6.png)

commit test 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116866508-796ff280-ac46-11eb-9740-a32b4bd3b3d6.png)

show history로 히스토리를 확인 합니다. <br>
![image](https://user-images.githubusercontent.com/44331989/116866773-f0a58680-ac46-11eb-8d3e-de422d54622c.png)
![image](https://user-images.githubusercontent.com/44331989/116866937-2185bb80-ac47-11eb-931f-4a178873ebbc.png)

다른 pc의 이클립스에서 해당 프로젝트를 Check Out 후 프로젝트를 확인 합니다. <br>
팀원들끼리 추가 commit, update test를 해서 최종적으로 연동 확인 합니다. <br>

### eclipse svn branch 생성
프로젝트를 진행하다 보면 같은 프로젝트를 다르게 관리해야 되는 경우가 생김, 이 때 브랜치를 만들어서 관리하면 효율적임 <br>
브랜치를 따로 가져가고 최종 반영 시 메인 브랜치인 trunk 브랜치와 merge해서 통합시키면 됨 <br>
* eclipse에서 생성하는 걸 가정
![image](https://user-images.githubusercontent.com/44331989/134853551-b944abf5-0f6d-48d5-bab3-79a2afd2b9fc.png)
![image](https://user-images.githubusercontent.com/44331989/134853729-c33227f2-7e27-4d5f-b131-1303a0f5db74.png)
![image](https://user-images.githubusercontent.com/44331989/134854243-f018df1d-4739-483c-9ea7-f21d1137ed44.png)
그리고 기존 프로젝트와 동일한 프로젝트명은 기존 프로젝트를 덮어 쓰게 되니 branch아래의 프로젝트 명을 변경해주는 걸 권장 <br>
![image](https://user-images.githubusercontent.com/44331989/134854327-e114cf9f-5c4f-4636-8ab4-86f9b302d172.png)
![image](https://user-images.githubusercontent.com/44331989/134854367-10fd06e0-a80e-4021-bfcd-ab5809b6fbac.png)
![image](https://user-images.githubusercontent.com/44331989/134854704-c80f9094-d777-44c1-b7e6-b1bd94c9676c.png) <br>
바뀐 프로젝트명으로 check out 받아서 작업하면 됨 <br>


* 위 처럼 프로젝트명을 변경해서 check out받는 방법도 있지만 이 방법 보다는 svn의 switch 기능을 이용해서 main에서 다른 branch로 변경해서
  관리하는 방법이 더 효율적임 <br>
![image](https://user-images.githubusercontent.com/44331989/135028884-9694cf6f-44ad-4169-858d-9752d6780710.png) <br>
위처럼 210928_nssp라는 branch를 새로 생성 한 상태이고 메인에서 이 branch로 변경하려면 아래처럼 switch를 이용함 <br>
![image](https://user-images.githubusercontent.com/44331989/135029916-467faf27-ba20-4d2f-bd35-142f5fa9ad47.png) <br>

![image](https://user-images.githubusercontent.com/44331989/135029108-9e65c5f3-e3ed-4768-a849-8192a23ce4d1.png) <br>
switch To창에서 바꿀 browse 선택 <br>

※ 바꿀 branch밑의 프로젝트를 선택 후 ok
![image](https://user-images.githubusercontent.com/44331989/135029213-b034948a-bc0f-4c91-9ec4-804391948da4.png) <br>

browse에서 선택한 branch가 선택이 된걸 확인 할 수 있음 <br>
![image](https://user-images.githubusercontent.com/44331989/135030114-62b1e662-0a02-4350-9793-9d30cec050d7.png) <br>

switch 작업이 완료되면 아래처럼 project의 branch가 바뀜 <br>
![image](https://user-images.githubusercontent.com/44331989/135030279-822aa02b-4446-4293-b470-eaea54fb1a01.png)

이 branch의 프로젝트는 main인 trunk에 영향을 끼치지 않는 branch니까 마음 껏 테스트 하면 됨 <br>
이 다음도 중요한데 소스코드를 수정 후 반드 시 commit을 해야만 main branch에 영향을 끼치지 않음 <br>
![image](https://user-images.githubusercontent.com/44331989/135030438-1e540fb3-5570-4b3c-8cea-6a89886287de.png) <br>
※ 소스코드를 위처럼 수정했으면 commit을 해서 branch별로 버전관리를 해줘야만 함 <br>
만일 다른 branch에서 소스코드 수정 후 바뀐 내용을 commit 하지 않는 경우 원치 않는 내용이 trunk의 프로젝트에 적용됨 <br>

아래처럼 반드시 commit을 해서 버전관리를 다르게 해야 함 <br>
![image](https://user-images.githubusercontent.com/44331989/135030673-8fa7c87e-97d8-4fbd-8b1e-ed0aa092c5d1.png)

그리고 다른 branch에서 수정한 내용을 trunk에 최종 반영 할 때는 merge 기능을 이용해서 main과 병합, 이 때 branch별로 소스코드가 상이하기에 에러가 발생할 텐데 <br>
다른 부분을 수정해서 잘 반영할 것 <br>

정답은 없기 때문에 branch를 따로 생성 후 프로젝트를 하나 더 check out받을 지, switch로 변경해서 사용할지 적절히 선택해서 사용하면 됨 <br>
  




