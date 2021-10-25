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

