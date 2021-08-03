# 리눅스 사용 관련
## 파일 뒷부분을 잘라서 저장
### 로그를 잘라서 파일로 저장할 때 유용하게 사용
~~~
#tail -line 수 파일명 | cat > 저장할 파일명
tail -5000 a.catalina.out | cat > test.out
~~~
출처 : https://sound10000w.tistory.com/96

## su, su - 차이
![image](https://user-images.githubusercontent.com/44331989/127940084-ab518b53-c129-467e-8c11-ae68789e0cd1.png)
image source : https://gxnzi.tistory.com/75
