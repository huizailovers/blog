### 使用伪代码来模拟异步执行的流程

Node.js 实现异步的核心是事件，也就是说，它把每一个任务都当成 **事件** 来处理，然后通过 Event Loop 模拟了异步的效果 

```js
// 使用数组来实现队列 
class globalEventQueue = {
    this.start = 0
    this.end =0
    this.arr = [] //数组
    //往数组中存放
    push(val){
    	this.arr[this.start] = val
    	this.start ++;
	}
	pop(){
    	return this.arr[this.end]
        this.end++;
	}
};
//接收请求的时候 生成事件，添加到队列中
let processHttpRequest = function(request,response){
    var event = creatEvent({
        param : request.param,
        result : null, //存放异步的结果
        callback:function(result){
            response.success = true;
            sendResult(response);
        }
    })
    globalEventQueue.push(event);
}
//事件循环
let event = function(){
    while (globalEventQueue.arr.length >0 ){
        const event = globalEventQueue.push();
        if (isIoTask(event)){
            //交给io线程池来处理
            const thread = getThreadFromThreadPool();
            thread.handleIOTask(event);
        }else{
            //自己处理
            const result =  hanleEvent(event);
            event.callback.call(result);//执行传入的回调函数
        }
    }
}
// io线程处理任务
let handleIOTask =  function(event){
    const curThread = this;
    const opearteDb = function(param ,callback){
        const result = readDataFromDb(params);
        callback.call(null,result)
    }
    optDatabase(event.param,function(result){
        event.result = result
         // 将该事件重新添加到队列的尾部
        this.globalEventQueue.push(event);
         
        // 释放当前线程
        releaseThread(curThread
    })
}
//io 操作完成后将执行的结果形成事件，存放到队列中。等到主线程再次循环到改事件的时候，再次处理事件。

//落实到代码上，事件和循环的机制
 getAccountInfoById:function(id,session,callback){
        var result = {success: false};
        var query={_id:id};
        mongo.db().collection(constants.DB.platform_account).findOne(query,function(err,row){
            if(!err){
                result.row=row;
                result.success=true;
                callback(result);
            }else{
                logger.app.error(err);
                callback(result);
            }
        });
    }
 //上面代码会有两个事件，第一个事件是监听到请求后，同步执行代码，操作数据库的时候是异步的io操作，io操作完成后将新的事件放入到事件队列中，当循环到这个事件队列的时候则执行掉这个事件。任务完成。


```

