# windows 관련 repository
## windows 10
### windows 10 USB 설치 방법
>OS가 설치되지 않은 깡통 노트북에 부팅디스크를 이용해서 윈도우10을 설치하는 방법을 기술합니다. <br>
>웬만한 개발자는 OS가 미설치된 깡통 노트북에 OS 설치 및 개발환경(framework)을 세팅(jdk <-> IDE 연동, WAS(주로 tomcat container)해서 테스트 페이지를 띄울 수 있어야 합니다. <br>
>OS 설치는 개발자 뿐만 아니라 일반인도 충분히 가능합니다.

※ windows 10 부팅 디스크가 있다는 가정하에 기입합니다. <br> 
우선 제일먼저 컴퓨터의 부팅순서를 변경해야 합니다. <br>
컴퓨터 부팅 후 bios(BIOS; Basic Input/Output System) 진입 해서 설정 변경 가능 -> BIOS 진입방법은 제조사 마다 다른데 구글링하면 됩니다. <br>
"'노트북 모델명' bios" 이런식으로 검색 <br>
보통 부팅 후 f2 연타, f8 연타 등등임<br>

BIOS 예시 이미지 <br>
![image](https://user-images.githubusercontent.com/44331989/135259116-3413b152-a10a-4703-ad38-270dff73f741.png) <br>

바이오스 진입 후 아래처럼 부팅 순서 우선순위를 USB로 변경 <br>
![image](https://user-images.githubusercontent.com/44331989/135261401-d3c86c38-cf0c-4b0d-8635-a54d18505811.png) <br>
부팅 순서 변경 예시 이미지<br>

부팅 순서를 USB로 변경, 변경내용 저장 후 리부팅 <br>
![image](https://user-images.githubusercontent.com/44331989/135261739-58f212b9-5bcb-4701-b31f-b7f9858732f7.png) <br>

부팅순서가 정상적으로 변경 되었을 경우 잠시 후 아래처럼 윈도우 설치 화면이 표시됨 <br>
![image](https://user-images.githubusercontent.com/44331989/135262279-795207f7-d8d0-48d9-8753-8b1d04d819e4.png) <br>

지금설치 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135262498-eed01e22-6de7-4aca-800f-e2388aa21c41.png) <br>

윈도우 설치 후 정품인증 할 것이므로 제품키가 없음 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135262666-5e8b5c7a-743c-4921-8d94-ce29a95f90be.png)

설치할 윈도우 종류 선택 -> 윈도우10 pro 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135262740-db30e6f7-8510-45d5-9ac8-ac657152cbc7.png)

사용조건 동의 <br>
![image](https://user-images.githubusercontent.com/44331989/135262801-7e5f52a6-0c29-4c9e-9ad6-007ffaf09bf5.png)

사용자 지정 : windwos만 설치 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135262841-a5316a11-da15-4ce4-96ec-d32211f134f4.png)

파티션 설정(파티션이 없을 경우 새로 생성 후 할당, 구글링하면 됨) 후 다음 선택<br>
요즘 컴퓨터는 대부분 SSD라서 C드라이브에 윈도우 설치해야 함<br>
![image](https://user-images.githubusercontent.com/44331989/135262931-81c1d909-ad2d-4945-bb39-ec41432f7947.png)

윈도우 설치가 진행됨(컴퓨터 사양에 따라 다르지만 그렇게 오래 안 걸림) <br>
![image](https://user-images.githubusercontent.com/44331989/135263121-b6ea27d4-c71b-4ef8-91d2-8070d6e35174.png)

설치 완료 후 재부팅 됨 <br>
![image](https://user-images.githubusercontent.com/44331989/135263171-8ab8a727-8c93-4dfe-9f76-2d91ec0ce116.png)

이제 윈도우 OS는 설치 되었기 때문에 USB는 제거해도 상관 없음 <br>
재부팅 후 아래처럼 윈도우 설정 화면이 표시됨(지역 선택) <br>
![image](https://user-images.githubusercontent.com/44331989/135263399-b8173f3e-18bc-41a0-b263-d3cc6da4a0f9.png)

키보드 자판배열 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135263672-3eba21be-dd43-4afc-b25a-f8bd91db5810.png)

키보드 레이아웃은 건너뛰기 <br>
![image](https://user-images.githubusercontent.com/44331989/135263847-ee903bc2-6bf0-4965-86df-c93097e2754d.png)

개인으로 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135263889-ca6fc03d-f0d3-4ca3-87e3-51351d06989f.png)

계정 생성(처음엔 그냥 오프라인으로 생성) <br>
![image](https://user-images.githubusercontent.com/44331989/135263953-c3c30b73-1d40-4466-afed-238345ac1642.png) <br>
![image](https://user-images.githubusercontent.com/44331989/135264000-ca0909ff-9525-498a-bf59-55b17ed9d885.png) <br>
![image](https://user-images.githubusercontent.com/44331989/135264015-f78ba87f-2756-46a4-a4a9-8f555d5e2cb3.png) <br>
![image](https://user-images.githubusercontent.com/44331989/135264035-dc355403-e399-442f-9738-cb1dd5125031.png) <br>

아니오 선택 <br>
![image](https://user-images.githubusercontent.com/44331989/135264057-13ff1edc-bc16-4157-b4d0-77bc2810f8b3.png)

별 쓸데없는 것이기에 모두 체크 해제 후 수락 <br>
![image](https://user-images.githubusercontent.com/44331989/135264104-05f2a111-8f82-4785-ae71-5a8b5a94df38.png)

윈도우10이 시작됨 <br>
![image](https://user-images.githubusercontent.com/44331989/135264197-ac3f0d2c-b30a-4f49-9573-389b721df547.png)

설치과정이 위 이미지와 동일하지는 않을 수 있음, 참조용임 <br>
인터넷 설정 및 기타 유틸리티 설치 및 윈도우10 정품인증을 받고 사용하면 됩니다. <br>

<hr>

### windows 10 정품인증
#### 반드시 정품인증 하기 전에 백신 실시간 감시 끄고 진행할 것

Activator파일은 윈도우 원격 pc 아래 경로에 있습니다. <br>
![image](https://user-images.githubusercontent.com/44331989/135385778-1cf157d5-e929-485c-a685-2924ac28f7a3.png) <br>

FileZilla client 다운로드 후 아래처럼 호스트, 아이디, pw 입력 후 윈도우 원격 PC에 접속할 것 <br>
![image](https://user-images.githubusercontent.com/44331989/135386099-a3209265-01dc-4ee8-91ac-a16753a4e1d4.png) <br>
~~~
host : 121.165.242.176
사용자명 : iansystem05
비밀번호 : dldks12!@
포트 : 입력안해도 무방(ftp 기본 21번 포트 사용)
~~~
Activator.cmd 관리자 권한으로 실행 <br>
![image](https://user-images.githubusercontent.com/44331989/135384915-7d207f50-4dfb-4a0a-94b2-ac3be8b3cce5.png) <br>

일반 윈도우10이니까 숫자 2 입력 <br>
![image](https://user-images.githubusercontent.com/44331989/135385279-6d6baf58-c7be-4ec2-8ea9-cc3c903e4177.png) <br>

정품 인증 활성화 하기 위해서 숫자 1 입력 <br>
![image](https://user-images.githubusercontent.com/44331989/135385444-f5147908-c329-4eee-996c-d92cce6ce7cf.png) <br>

아래처럼 정품인증 성공했다고 표시되고 아무키나 입력해서 빠져나오면 됨, 빠져 나오고 창 닫기 <br>
![image](https://user-images.githubusercontent.com/44331989/135385509-3b0c6437-5d6f-401d-bd61-3486d2e878e0.png) <br>

내 PC - 속성 눌러서 실제로 정품 인증 되었는지 확인 <br>
![image](https://user-images.githubusercontent.com/44331989/135386917-30cd0df5-8c46-42d3-be7b-7d11b26c6ca4.png)

회사 윈도우 서버 아래 경로에 웬만한 유틸은 다 있습니다. <br>
![image](https://user-images.githubusercontent.com/44331989/135368842-3e9eadb3-bc50-4f85-b6d5-a5d414d7cdfc.png)
image source : https://www.soft2000.com/12506 <br>

개발자분은 아래 링크를 통해 윈도우10위에 egovframework 진행을 해주시길 바랍니다. <br>





