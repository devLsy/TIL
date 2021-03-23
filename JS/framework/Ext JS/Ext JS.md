# Ext JS 관련 TIL
## 데이터 결과값을 dataStore에 param값으로 전달하는 방법
~~~
Store.proxy.extraParams = action.result.data; // ajax로 조회해 온 action.result.data를 store.proxy.extraParams에 저장하면 전달됨
Store.load(); // 데이터스토어에서 load해서 사용
~~~      

## store에 저장된 데이터 추출하는 방법
~~~
store.each(function(rec) {
    // getData(); 이용해서 데이터만 전부 뽑아 올 수 있음
    var recData = rec.getData();
});
~~~
출처 : https://stackoverflow.com/questions/21643086/retrieve-set-of-records-based-on-same-field-values-from-store
