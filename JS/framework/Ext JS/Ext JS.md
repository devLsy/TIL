# Ext JS 관련 TIL
## 데이터 결과값을 dataStore에 param값으로 전달하는 방법
~~~
Store.proxy.extraParams = action.result.data; // ajax로 조회해 온 action.result.data를 store.proxy.extraParams에 저장하면 전달됨
Store.load(); // 데이터스토어에서 load해서 사용
~~~      
