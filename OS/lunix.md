# 리눅스 사용 관련
## 파일 뒷부분을 잘라서 저장
### 로그를 잘라서 파일로 저장할 때 유용하게 사용
~~~
#tail -line 수 파일명 | cat > 저장할 파일명
tail -5000 a.catalina.out | cat > test.out
~~~
출처 : https://sound10000w.tistory.com/96
