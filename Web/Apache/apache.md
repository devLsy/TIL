# Apache Web Server 관련 TIL
## Apache 설치 
### <a href="https://proger.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B010-64bit%EC%97%90%EC%84%9C-Apache-24-%EC%84%A4%EC%B9%98%EC%99%80-%EC%84%A4%EC%A0%951" title="apache setup" target="_blank">참조 블로그</a><br>
<hr>

## Apache SSL 설정
APACHE SSL 설치하는 법

* 방화벽 허용(443 port) <br>
* SSL 인증서와 ssl_auth.sh 파일 <br>
* httpd.conf, ssl.conf <br>

1. ssl 인증서(SSLCertificateFile, SSLCertificateKeyFile, SSLCertificateChainFile)와 ssl_auth.sh 파일을 정해진 경로에 위치 <br>
ex) /NCIA/APACHE/jboss-ews-2.1/httpd/ssl 폴더 밑에 위치 <br>

2. ssl.conf(웹서버의 환경설정 파일 - ssl설치 관련 정보) 파일을 서버에 맡게 설정 <br>
ex) /NCIA/APACHE/jboss-ews-2.1/httpd/conf.d 폴더 밑에 위치 <br>

 - Listen 443 주석 해제 <br>
 
 - NameVirtualHost *:443 주석처리 할 것(apache 2.4이상 지원안함) <br>
 - SSLMutex default 주석처리 할 것(apache 2.4이상 지원안함) <br>

 - <VirtualHost _default_:443> 부분의 인증서 경로를 서버에 맡게 설정 <br>
 - DocumentRoot 를 메인jsp가 있는 경로로 수정 ex)/var/www/html <br>

 - #SSLPassPhraseDialog  builtin 의 주석 해제(비밀번호를 불러오는 문구) <br>
 
 와일드카드 인증서 사용시, serverName을 xxx.xxx.xx.xx:443으로 변경 <br>

3. httpd.conf 파일을 서버에 맡게 설정 <br>
ex) /NCIA/APACHE/jboss-ews-2.1/httpd/conf 폴더 밑에 위치 <br>

 - Listen 443이 선언되어 있는데 주석처리 할 것(ssl.conf에 있음)<br>

* ssl.conf에 지정된 로그 파일로 로그 보면서 반영하면 됨 <br>


