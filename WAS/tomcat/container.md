# container 관련 TIL
## container 관련 TIL 정보 기록하는 md입니다.
### 20190130 TIL
* 이클립스에서 tomcat 2개 이상 띄우는 방법
tomcat server OverView에서 Port Name부분의 Tomcat admin port, HTTP/1.1 port, AJP/1.3 port number를 기존의 tomcat과 겹치지 않는 number로 변경 후 띄우면 됨
<hr>
## 로컬에 테스트 SSL 적용
### keytool -genkey -alias tomcat -keyalg RSA -validity 3650 -keystore tomcat.keystore -> keystore 생성
### 아래와 같이 tomcat 설정 변경(keystore 경로 및 비밀번호 설정)
~~~
<server.xml>
<Connector port="8443" protocol="HTTP/1.1" maxThreads="150" scheme="https" secure="true" sslProtocol="TLS"
    	KeystoreFile="C:\eGovCI-3.8.0_64bit\bin\tomcat.keystore" SSLEnabled="true" clientAuth="false" keystorePass="dldks12!@" type="RSA"> 
</Connector>
~~~




































