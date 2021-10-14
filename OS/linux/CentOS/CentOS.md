# CentOS 관련
## CentOS 관련 내용을 기술합니다.
### 사용자 계정 생성
![image](https://user-images.githubusercontent.com/44331989/137235764-d847fbd8-c41e-47f2-84ad-bf27d62598d4.png) <br>
image source : https://devmg.tistory.com/83 <br>

### 사용자에게 sudo 권한 부여
![image](https://user-images.githubusercontent.com/44331989/137234868-8989c8f9-8b3d-416c-b55c-8e53985d83ec.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137234911-29459954-3051-46f2-90ec-a2f0b49dfa76.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137234944-2a4d0ebf-48fc-46d2-a171-eb7c8956262e.png) <br>
image source : https://m.blog.naver.com/wideeyed/221289811134 <br>

### ssh root 접속 차단
리눅스를 기본 설치할 경우 ssh 접속 시 root계정으로 접근이 가능한데 이는 보안상 취약할 수 있기에 접근을 제한하는걸 권장함 <br>
일반 계정으로 접속 후 su를 통해서 계정을 root로 변경하거나 sudo 권한을 부여받아서 1회성으로 root 권한으로 작업하는 방식이 일반적임 <br>
ssh 접속 시 root제한하기 위해선 아래 명령어로 sshd_config파일을 변경해야 함 <br>
~~~
root로 접속
# vi /etc/ssh/sshd_config
~~~
PermitRootLogin을 찾으면 제일 처음엔 아래처럼 루트 로그인이 yes로 되어 있고
#PermitRootLogin yes이렇게 주석되어 있을건데 아래처럼 주석 해제하고 no로 변경 함
![image](https://user-images.githubusercontent.com/44331989/137238111-b31c551e-88f1-4d9a-871e-5f9c2eb1b9a8.png) <br>

그 후 아래 명령어로 ssh 데몬을 재시작해서 변경된 파일을 적용해야 함 <br>
~~~
# service sshd restart
~~~


