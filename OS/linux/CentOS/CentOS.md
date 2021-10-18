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
그 후 터미널을 껐다가 다시 실행 후 root로 직접 접근이 되는지 확인 <br>

### CentOS에 Oracle DBMS 설치
출처 : https://xxsiyoung.tistory.com/3 <br>

### CentOS 재시작 시 Oracle 자동 재시작
![image](https://user-images.githubusercontent.com/44331989/137681872-fcfd71bc-8ce4-4647-a845-802521305cc0.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137681901-65004164-0ba3-464e-b669-91ae32eb56c4.png) <br>
~~~
#!/bin/bash
#
# chkconfig: - 70 30
# description: oracle for GCLB
# ORA_HOME 경로는 설치환경에 맞게 설정한다.
ORA_HOME="/home/oracle/db/product/12.1.0/dbhome_1"
# ORA_OWNER도 설치 환경에 맞게 설정
ORA_OWNER="oracle"
if [! -f $ORA_HOME/bin/dbstart -o ! -d $ORA_HOME]
then
        echo "Oracle Startup: failed"
        exit 1
fi
case "$1" in
start)
        echo -n "Oracle Start: "
        su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl start"
        su - $ORA_OWNER -c $ORA_HOME/bin/dbstart
        touch /var/lock/subsys/oracle
        echo "OK"
        ;;
stop)
        echo -n "ORACLE Shutdown: "
        su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl stop"
        su - $ORA_OWNER -c $ORA_HOME/bin/dbshut
        rm -f /var/lock/subsys/oracle
        echo "OK"
        ;;
restart)
        $0 stop
        $0 start
        ;;
*)
        echo "Usage: $0 start|stop|restart"
        exit 1
esac
exit 0
~~~
위 스크립트 작성 후 권한 부여 <br>
![image](https://user-images.githubusercontent.com/44331989/137682053-121c78bf-7e5c-4d50-9025-49a428dc2095.png) <br> 
![image](https://user-images.githubusercontent.com/44331989/137682088-06824273-586a-43c1-b084-1708da5279ba.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137682119-e49f30b2-2e00-4120-b6fe-93c006003768.png) <br>

### 환경변수
![image](https://user-images.githubusercontent.com/44331989/137296534-f1301cd2-f616-4096-9d73-cc369478afb4.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137296575-22805fc2-09e0-491a-a934-885ab60ea991.png) <br>
![image](https://user-images.githubusercontent.com/44331989/137296604-6634ddb6-a97a-4325-a7f5-c95d5bd97882.png) <br>
image source : https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jeong2091&logNo=221995920586 <br>

### ssh 타임아웃 설정
vim .bash_profile로 환경변수 설정파일을 오픈 <br>
~~~
#아래처럼 사용자 계정 환경변수 파일에 TMOUT 설정(단위 : 초)
.bash_profile에서 export TMOUT=1800
~~~
source .bash_profile로 환경변수 적용 <br>




