# apache tomcat container 관련 TIL
## container 관련 TIL 정보 기록하는 md입니다.
### 20190130 TIL
* 이클립스에서 tomcat 2개 이상 띄우는 방법
tomcat server OverView에서 Port Name부분의 Tomcat admin port, HTTP/1.1 port, AJP/1.3 port number를 기존의 tomcat과 겹치지 않는 number로 변경 후 띄우면 됨

## 로컬에 테스트 SSL 적용
### keytool -genkey -alias tomcat -keyalg RSA -validity 3650 -keystore tomcat.keystore -> keystore 생성
### 아래와 같이 tomcat 설정파일(server.xml) 변경(keystore 경로 및 비밀번호 설정)
~~~
<server.xml>
<Connector port="8443" protocol="HTTP/1.1" maxThreads="150" scheme="https" secure="true" sslProtocol="TLS"
    	KeystoreFile="C:\eGovCI-3.8.0_64bit\bin\tomcat.keystore" SSLEnabled="true" clientAuth="false" keystorePass="dldks12!@" type="RSA"> 
</Connector>
~~~
참조  : https://dy20c.tistory.com/12
<hr>

## 윈도우 tomcat cmd 한글 깨짐
![image](https://user-images.githubusercontent.com/44331989/129306188-1fdcda01-a453-4688-bc45-bfb4265646a6.png)
![image](https://user-images.githubusercontent.com/44331989/129306217-04ae2901-acfe-4412-aba5-8ffd70416edc.png)
![image](https://user-images.githubusercontent.com/44331989/129306226-6632ed3d-db85-44d0-bf61-167321c1bddd.png)
![image](https://user-images.githubusercontent.com/44331989/129306238-40381e85-b544-496b-9c84-4dbb732f0932.png) <br>
image source : https://dejavuhyo.github.io/posts/tomcat-console-encoding/





































