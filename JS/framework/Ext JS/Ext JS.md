# Ext JS 관련 TIL
## 데이터 결과값을 dataStore에 param값으로 전달하는 방법
~~~
Store.proxy.extraParams = action.result.data; // ajax로 조회해 온 action.result.data를 store.proxy.extraParams에 저장하면 전달됨
Store.load(); // 데이터스토어에서 load해서 사용
~~~      

## store에 저장된 데이터 전부 추출하는 방법
~~~
// 스토어를 반복문 돌려서 데이터를 전부 
store.each(function(rec) {
    // getData(); 이용해서 데이터만 전부 뽑아 올 수 있음
    var recData = rec.getData();
});
~~~
출처 : https://stackoverflow.com/questions/21643086/retrieve-set-of-records-based-on-same-field-values-from-store

## 이벤트 리스너에서 store의 데이터를 param값으로 넘기는 방법
~~~
var testContainer = Ext.create('Ext.container.Container',{
     id       : 'testContainer',
     region   : 'center',
     margin   : '5 30 5 20',
     defaults : { layout : 'vbox' },
     items    : [	
          { xtype    : 'grid', 	
           title : 'test list', 
           margin : '10 28 15 0', 	
           width : '100%', 
           height : 150, 
           store: testStore,	
           columns  :  {	
							 items    : [
							  {text: 'No', xtype : 'rownumberer', width : 40, minWidth : 30, align : 'center' },
							  {text: '제목',  dataIndex:'title', id:'title', width : 50},
							  //{text: '작성자',  dataIndex:'writer', width : 430},
							  {text: '작성일', dataIndex:'regdate', id:'regdate', width : 50},
							      ]			
							 },	
               forceFit: true,	
               renderTo: Ext.getBody(),
               listeners : {	 	
            	   	// 게시글 상세 클릭 시 출력된 record의 데이터만 param값으로 넘김, itemclick에서 view, record, item, index, e, eOpts등을 사용 할 수 있음
            	   	itemclick : function (view, record, item, index, e, eOpts){
                        // 아이템 영역 클릭 시 detail() 함수를 실행하면서 인자값으로 record의 데이터를 넘김
            	   		fnChildChk.detail(record.data);	
                    }				
               }		
          },	
         ],	
         // end item
});	
~~~
