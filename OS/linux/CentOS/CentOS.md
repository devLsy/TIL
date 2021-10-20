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

### source의미
![image](https://user-images.githubusercontent.com/44331989/137708220-f2848cf6-7e6f-4548-adf6-34f15f085488.png) <br>
image source : https://klero.tistory.com/entry/source-%EB%AA%85%EB%A0%B9%EC%96%B4%EB%9E%80 <br>

### 서비스 등록
![image](https://user-images.githubusercontent.com/44331989/137892531-81f97c6a-4c64-4dc1-a4f5-61bae2616d94.png) <br>
image source : https://victorydntmd.tistory.com/215 <br>

#### journalctl
![image](https://user-images.githubusercontent.com/44331989/137893166-76d29df6-e9b5-460b-94b1-29a1178f9fe8.png) <br>
image source : https://sysops.tistory.com/115 <br>

### systemctl start 에러
~~~
ex)
systemctl start postgresql-9.6.service
Job for postgresql-9.6.service failed because the control process exited with error code. See "systemctl status postgresql-9.6.service" and "journalctl -xe" for details.
~~~
systemctl에 등록된 서비스 실행 시 위와 같은 에러를 발견하는 운 나쁜 경우가 생기면 <br>
systemctl status postgresql-9.6.service(등록된 서비스명)과 journalctl -xe를 통해서 로그를 확인해서 문제를 해결해야 함 <br>

회사에서 postgreSQL가 root로 systemctl start로 실행 시(원래 잘 되다가 오라클 설치하면서 리눅스 설정을 좀 변경한게 원인 인 듯) <br>
나의 경우 postgresql-9.6.service를 실행 한 경우 아래처럼 에러가 발생 했었음 <br>
~~~
[root@centos7 ~]# systemctl status postgresql-9.6.service
● postgresql-9.6.service - PostgreSQL 9.6 database server
   Loaded: loaded (/usr/lib/systemd/system/postgresql-9.6.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since 화 2021-10-19 15:30:09 KST; 50s ago
     Docs: https://www.postgresql.org/docs/9.6/static/
  Process: 30514 ExecStart=/usr/pgsql-9.6/bin/postmaster -D ${PGDATA} (code=exited, status=1/FAILURE)
  Process: 30507 ExecStartPre=/usr/pgsql-9.6/bin/postgresql96-check-db-dir ${PGDATA} (code=exited, status=0/SUCCESS)
 Main PID: 30514 (code=exited, status=1/FAILURE)

10월 19 15:30:08 centos7 systemd[1]: Starting PostgreSQL 9.6 database server...
10월 19 15:30:09 centos7 postmaster[30514]: < 2021-10-19 15:30:09.071 KST > 로그:  redirecting log output to logging… process
10월 19 15:30:09 centos7 postmaster[30514]: < 2021-10-19 15:30:09.071 KST > 힌트:  Future log output will appear in …pg_log".
10월 19 15:30:09 centos7 systemd[1]: postgresql-9.6.service: main process exited, code=exited, status=1/FAILURE
10월 19 15:30:09 centos7 systemd[1]: Failed to start PostgreSQL 9.6 database server.
10월 19 15:30:09 centos7 systemd[1]: Unit postgresql-9.6.service entered failed state.
10월 19 15:30:09 centos7 systemd[1]: postgresql-9.6.service failed.
Hint: Some lines were ellipsized, use -l to show in full.
~~~

구글링 해 본 결과.. 정보가 많이 부족 했었지만 어떤 블로그를 참조해서 아래처럼 에러가 발생하는 경로에 직접 들어가서 서비스를 실행 해 봤음 <br>
~~~
/usr/pgsql-9.6/bin/postmaster
~~~

그 결과 권한 문제라고 아래처럼 나왔음 <br>
~~~
시스템 보안 관련 문제로, PostgreSQL server를 "root" ID로 실행할 수 없습니다.
반드시 일반 사용자 ID(시스템 관리자 권한이 없는 ID)로 서버를 실행하십시오.
Server를 어떻게 안전하게 기동하는가 하는 것은 문서를 참조하시기 바랍니다.
~~~

그래서 sudo 권한 있는 일반 사용자 계정으로 systemctl start 서비스명을 입력 했는데 되네... <br>
systemctl은 원래 root로만 실행이 가능, sudo 권한 있는 계정으로 실행 시 root 비밀번호를 입력하라고 나오는데
root 비밀번호 입력 후 정상 실행이 됨 <br>

![image](https://user-images.githubusercontent.com/44331989/138075494-9f252628-ba6c-4e2b-bbb7-40f42df9eb40.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138075541-7e42df72-09d7-47bd-89a9-50d318438857.png) <br>

systemctl 에러를 안 만나길 바라지만 만일 에러를 만날 경우 경로에 직접 들어가서 실행해 본 다음 에러로그를 보면서 문제 해결이 필요하다고 생각됨 <br>






