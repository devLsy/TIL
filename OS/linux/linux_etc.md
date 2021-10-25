# 리눅스 사용 관련
## 리눅스 사용 관련 팁을 정리
### 로그를 잘라서 파일로 저장할 때 유용하게 사용
~~~
#tail -line 수 파일명 | cat > 저장할 파일명
tail -5000 a.catalina.out | cat > test.out
~~~
출처 : https://sound10000w.tistory.com/96

### su, su - 차이
![image](https://user-images.githubusercontent.com/44331989/127940084-ab518b53-c129-467e-8c11-ae68789e0cd1.png)
image source : https://gxnzi.tistory.com/75

### 심볼릭 링크
![image](https://user-images.githubusercontent.com/44331989/136323974-2e545268-7512-4ff2-a7a2-5ebb9c2b9928.png) <br>
![image](https://user-images.githubusercontent.com/44331989/136324115-a654d774-da5d-43f1-a1a8-b37cc6d78792.png) <br>
![image](https://user-images.githubusercontent.com/44331989/136324058-26eabbad-cf0d-4e98-8b44-c9aa90a7b4df.png) <br>
![image](https://user-images.githubusercontent.com/44331989/136325081-092fcab9-c4f6-47ce-9222-93576b430e1b.png)
image source : https://server-talk.tistory.com/140, <br>
               https://bio-info.tistory.com/33 <br>

#### 심볼릭 링크 사용할 경우
리눅스에서 /에 남은 용량이 30G인 서버에서 /oracle폴더가 90% 이상을 차지해서 /에 남은 용량이 2G밖에 없는 상태 <br>
이 경우 심볼릭링크를 활용할 수 있는데 오라클 서비스를 중지한 다음 
/oracle의 폴더를 /DATA(옮길 경로)밑으로 이동한 다음 /DATA/oracle(원본파일)의 심볼릭링크를 /oracle로 걸게 되면
/oracle -> /DATA/oracle 이런식으로 링크가 걸리게 되고
오라클 DBMS에서 바라보는 오라클 설정경로는 /oracle 그대로이지만 실제 데이터가 쌓이는 경로는 /DATA/oracle에 쌓이게 되어서
용량 이슈를 처리할 수 있음, 원래는 오라클 데이터 덤프 한 다음 폴더를 옮기는 게 원칙이지만 리스크가 크고 시간도 오래걸릴 수 있음 <br>
그리고 보통 오라클과 계약을 체결 후 오라클 전문 엔지니어가 작업하는게 일반적인 사항 <br>
만일 정말 운 나쁘게 개발자가 이를 처리할 수 밖에 없는 상황이 오면..일단 운영이 아닌 개발 서버에서 동일하게 시뮬레이션을 해봐야 함
오라클 덤프를 해서 데이터를 백업 해 두고, 오라클 서비스 내리고 tar로 묶어서 폴더를 옮긴 다음 심볼릭 링크를 걸고 오라클 재시작 후 테스트 <br>

### 크론탭
![image](https://user-images.githubusercontent.com/44331989/138198398-cbdda3aa-f59a-41a5-b90a-3268a77a4d6f.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138198516-8258a259-59c9-408e-ab7b-864727428e42.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138197998-b13cf7db-6ea7-4b8f-98ab-36e39187f94a.png) <br>
image source : https://m.blog.naver.com/anysecure3/221739040530 <br> 
 
### 그룹
#### 그룹관리
##### 그룹에 사용자 추가
![image](https://user-images.githubusercontent.com/44331989/138622342-8aa020b5-dda6-4691-874d-7d692b1e7e9d.png) <br>
image source : https://webdir.tistory.com/134 <br>
  
### chmod(change mod) 폴더, 파일 권한 
리눅스에서 폴더 또는 파일의 권한을 변경하기 위해선 chmod 명령어를 사용함 <br>
![image](https://user-images.githubusercontent.com/44331989/138634760-10433bf8-e930-4254-b0f2-be0d32d4f874.png) <br>
r : 읽기, <br> 
w : 쓰기(생성, 삭제, 수정 포함) <br> , 
x : 실행 <br> 
![image](https://user-images.githubusercontent.com/44331989/138646649-1cf1cfd3-b6f7-454a-b36e-74e0e5e9a248.png) <br>
~~~
ex) 
drwxr-xr-x. 
위 권한의 의미 : 
디렉토리를 이며 소유주는 읽고 쓰고 실행 모두 가능, 소유그룹은 읽기, 실행만 가능, other역시 읽기 실행만 가능하다는 소리임
~~~
위처럼 테스트폴더의 내용이 있음 <br>
해당 폴더 또는 파일의 권한을 변경하기 위해선 보통 최고관리자인 root 계정으로 작업을 함 <br>
~~~
chmod 
~~~
![image](https://user-images.githubusercontent.com/44331989/138635495-a564211d-5d9a-4489-b9ed-f7ebcf9aa6bf.png) <br>
image source : https://victorydntmd.tistory.com/214 <br>
![image](https://user-images.githubusercontent.com/44331989/138636122-871619ce-5d0c-42df-9de5-63a97c9d2a69.png) <br>
모든 권한 부여는 7(4+2+1)
~~~
chmod 755(소유주는 모든 권한 부여, 소유그룹과 기타는 읽고 실행만 가능)
chmod 777(소유주, 소유그룹, 기타 모두 읽고 쓰고 실행 모두 가능)
~~~
이런식으로 8진수로 표현할 수도 있고 직접 chmod +w 이런식으로 문자로도 표현 가능 <br>
![image](https://user-images.githubusercontent.com/44331989/138647328-eb6a1ebb-2734-4906-bc36-f465412d84ac.png) <br>

### 디렉토리 및 파일 목록 출력(ls)
아마 리눅스를 배우면서 제일 먼저 배우는 명령어중 하나일 듯 싶음 <br>
list의 약자로 ls 명령어를 입력 시 현재 위치한 경로의 디렉토리, 파일 목록들이 표시되는데 보통 -옵션을 넣어서 많이 사용함 <br>

![image](https://user-images.githubusercontent.com/44331989/138637613-33e0555e-daea-4796-8318-e38c7957e2a3.png) <br>
위처럼 기본 ls 명령어는 디렉토리, 파일 목록만 표시됨 <br>

![image](https://user-images.githubusercontent.com/44331989/138637658-b5b6ac5d-1f72-4a42-bea7-e107ba3d8cc2.png) <br>
-a 옵션 사용 시 숨긴 항목 포함 모든 목록이 표시됨 <br>

![image](https://user-images.githubusercontent.com/44331989/138637722-f082b49e-e868-47f2-85e2-9f5032468d24.png) <br>
-l 옵션 사용 시 폴더 및 파일의 소유권 목록이 표시됨 <br>

![image](https://user-images.githubusercontent.com/44331989/138637789-9ecba699-664a-4fa9-ad47-502e2d663e14.png) <br>
ls -al(소유권 및 숨긴 항목까지 표시)를 주로 많이 쓰고 상황에 따라서 다른 옵션도 섞어서 사용 <br>

### mkdir(폴더 생성)
![image](https://user-images.githubusercontent.com/44331989/138638777-b43d8867-708c-4177-8d2c-af3d2d981eb7.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138638814-5c7eb176-5f79-476f-85d4-c839a40ee501.png) <br>
image source : https://withcoding.com/92 <br>

### touch(새파일 생성)
리눅스에서 파일을 생성하거나 갱신하는 명령어임, 새로운 파일 만들 때 존재하는 파일명 지정하면 파일명으로 신규 파일이 생성되고 <br>
존재하는 파일명을 지정하면 파일의 수정시간이 현재시간으로 업데이트 됨 <br>

### rm(삭제)
![image](https://user-images.githubusercontent.com/44331989/138639752-a2433948-60bf-46ae-bcf4-bd603d3ed509.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138639771-0dea1870-864f-40a8-8bf0-a0e07109c2c8.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138639829-014523ba-d6a4-4666-93d9-af4bc8f869dd.png) <br>
image source : https://withcoding.com/95 <br>

### cd(change directory)
![image](https://user-images.githubusercontent.com/44331989/138639997-d2dcbf70-9725-4949-94c5-21574af7df91.png) <br>
image source : https://shaeod.tistory.com/597 <br>

### mv(이동 또는 파일명 변경)
![image](https://user-images.githubusercontent.com/44331989/138641451-eb10701c-a06d-4324-897e-70a9b1f7d476.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138641481-01feac69-9b84-480d-85eb-44b5b3e00ec1.png) <br>
image source : https://coding-factory.tistory.com/751 <br>

### cp(복사)
![image](https://user-images.githubusercontent.com/44331989/138642010-84c66a49-60cf-4c84-9974-b92205e66537.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138642084-7b91764e-4795-404c-83be-045526fff6b3.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138642106-c327325c-a4b0-4269-a263-c2700f1128ce.png) <br>
image source : https://withcoding.com/93 <br>

### 환경변수
![image](https://user-images.githubusercontent.com/44331989/138643498-c122f807-8986-4f82-ac75-d8623a6b6023.png) <br>
image source : https://galid1.tistory.com/211 <br>

### cat(파일 내용 출력, 파일 생성, 파일 병합)
![image](https://user-images.githubusercontent.com/44331989/138644005-c24e80eb-0e6c-439c-a5f4-78de4e70d727.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138644273-bc70c16e-5afd-4f13-a34a-b1e558c35241.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138645664-19af1f90-8a51-4fa5-b382-ff05aa031397.png) <br>
image source : https://withcoding.com/109 <br>

### head(파일의 제일 윗부분부터 출력)
![image](https://user-images.githubusercontent.com/44331989/138648360-7be2684b-f8c8-4076-a3b6-0145f739c022.png) <br>
image source : https://arer.tistory.com/148 <br>

### tail(파일의 마지막부터 출력)
![image](https://user-images.githubusercontent.com/44331989/138645934-984acbb4-5108-4b78-b668-c515855a3cd8.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138645975-a33163aa-dfa1-4f64-8834-e90f8731d01e.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138646029-b54b26a0-b916-4b8d-a4b8-ff54dd2aabaf.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138646071-4680c143-00c0-4a1b-872f-aa01072acb74.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138646083-cbae9ed0-604b-417c-b8a8-d503f41e0f36.png) <br>
image source : https://sisiblog.tistory.com/218 <br>

### grep(특정 문자열 검색)
정말 많이 사용하는 기능 <br>
![image](https://user-images.githubusercontent.com/44331989/138648717-fa208715-dbf5-427f-b72e-83b08017219f.png) <br>
image source : https://jybaek.tistory.com/704 <br>

### less(파일 뷰어 용도)
![image](https://user-images.githubusercontent.com/44331989/138656812-7f11d2eb-7cc9-4d8d-a668-465770978be2.png) <br>
서버에 로그를 확인 해야 하는데 로그의 크기가 매우 클 때, 현재 잔여 메모리가 얼마 남지 않은 상태에 vi가 아닌 less로 열면 효율적 <br>
서버의 자원이 여유롭지 않은 상태에서 vi를 열게 되면 메모리 부하, 만일 원격에서 열었을 경우 네트워크 트래픽 부하, 그래서 이 경우는 less를 사용하자 <br>
1gb의 파일을 vi로 열게 되면 메모리에 1gb만큼의 공간이 할당됨, 만일 메모리 여유공간이 부족하면 생각만 해도 끔찍 <br>
하지만 less로 열게 되면 less로 연 부분만큼만 메모리에 할당됨 <br>
읽기 전용 에디터임 <br>
![image](https://user-images.githubusercontent.com/44331989/138657484-114a8e21-5b6b-4dcf-b30a-d3cc95797727.png) <br>
image souece : https://tychejin.tistory.com/94 <br>

### tar(파일, 디렉토리 압축)
![image](https://user-images.githubusercontent.com/44331989/138657935-d7807222-ce67-4262-b299-acd51f22ccc4.png) <br>
~~~
tar cvfz(옵션) backup.tar.gz(압축파일명) ./dir3 ./file3 ./system.log
./dir3 ./file3 ./system.log를 tar라는 명령어로 파일을 묶어서 gzip으로 backup.tar.gz의 파일명으로 압축하였다는 의미
~~~
![image](https://user-images.githubusercontent.com/44331989/138658202-30fb5c2c-bf6f-4c4f-b760-c308adfe4d27.png) <br>
image source : https://clansim.tistory.com/50 <br>
![image](https://user-images.githubusercontent.com/44331989/138658435-0f0790f1-2934-4ea8-994d-ccc9a5cba364.png) <br>
image source : https://recipes4dev.tistory.com/146 <br>
※ tar 자체는 압축의 의미가 아니고 하나로 모은다는 소리이고 z라는 옵션을 줘야 gzip으로 압축을 한다는 소리임 <br>

![image](https://user-images.githubusercontent.com/44331989/138659137-d9e65b5d-45b5-4b83-8029-ad74acfde766.png) <br>
~~~
tar zvfz "압축파일명"
이렇게 되면 압축파일을 압축 
~~~

### find
※ find와 grep을 햇갈리지 말자, find는 파일명 또는 디렉토리를 검색, grep은 문자열로 파일명을 검색 <br> 
![image](https://user-images.githubusercontent.com/44331989/138669915-7b00d87e-66f6-49cb-92be-845fb44e4413.png) <br>
~~~
find 경로 조건 target
ex) find . -name system.log -> 현재 경로(.)에서 파일명이 system.log인 것을 찾아라
~~~
권한 없는 디렉토리 검색 시 sudo 입력 필요 <br>
![image](https://user-images.githubusercontent.com/44331989/138670253-7a7dc426-9503-4a3f-8d9a-ff6bbeb7005d.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138670680-134a93db-ae87-4c6a-b680-76492d42ce20.png) <br>
![image](https://user-images.githubusercontent.com/44331989/138671008-e6d9f243-ce12-45bc-a498-0cc0f5e4b8a8.png) <br>
image source : https://recipes4dev.tistory.com/156 <br>

### which(명령어 경로 확인)
![image](https://user-images.githubusercontent.com/44331989/138671358-8b6acd5b-e5cb-405c-9201-87cc17f23270.png) <br>
image source : https://webdir.tistory.com/158 <br>

### top(자원 상태 확인)
![image](https://user-images.githubusercontent.com/44331989/138672294-4cd59d9a-d5cd-4e6c-9cb9-533cabf7ac2c.png) <br>
더 자세한 건 구글링 <br>

### w, who(서버 접속자 정보 확인)
![image](https://user-images.githubusercontent.com/44331989/138672650-9288ab35-0931-4058-bcbc-bed4e37c06b7.png) <br>
현재 리눅스에 접속한 사용자에 대한 정보 <br>
사용자 아이디, 아이피, 로그인 시간, cpu 사용률, 현재 작업목록 <br>
w보다 좀더 간략하게 표현하려면 who <br>

### ping(Packet INternet Groper)
주로 네트워크 설정 이후 네트워크 확인하기 위해 날리곤 함 <br>
~~~
ping 아이피 또는 도메인
~~~
![image](https://user-images.githubusercontent.com/44331989/138673475-ff32eb86-b46c-43df-accd-cc0e645f1d05.png) <br>
위는 구글 서버로 ping을 날린 상태 <br>
제일 우측의 time은 ping을 날린 서버에서 타켓 서버까지의 응답속도를 나타내는데 당연히 time값이 적을 수록 인터넷이 빠르다는 의미임 <br>


