### timer  iocallback 的事件循环机制

```go
func beginDealTaskQueue( eventloop EventLoop){
    while (!isEmpty(eventLoop)){
        eventLoop.timeout = now();//当前时间戳 1111
        
        //执行定时任务
        runTimerTask(eventLoop);
        //执行io任务
        runIoTask(eventLoop);
    }
    //释放io资源
    freeIoService();
}

func runTimerTask(eventloop Eventloop){
    udealQueue = createQueue();
    for eventLoop.heap.size != 0 {
        runTime := eventLoop.heap.list[0].data;
        if runTime.startTime > eventLoop.timeout {
            break //小顶堆的最近的定时任务还没有到执行的时间，等到下一次循环去执行
        }else{
            //收集已经超时的任务
            runTime =  eventLoop.heap.remove();
            udealQUeue.push(runTime);
        }
    }
    //循环处理setTimeout这种任务
    for udealQueue.length != 0{
        runTask(udealQueue.shift())
    }
    //查看现在小顶堆最近的执行时间 作为wait time
    
    if eventLoop.heap.size != 0 {
        runTime := eventLoop.heap.list[0].data
        
        if runTime.startTime > eventLoop.Timeout {
            eventLoop.wait =  runTime.startTime - eventLoop.timeout
        }else{
            eventLoop.wait = 0 //wait 时间是为了和iocallback的时间比较使用的。
        }
    }
    
}

func runIoTask(EventLoop eventLoop){
    if eventloop.undealIoqueue.length != 0 {
        timeVal := now()
     	// 一直等待io返回结果或者等待超时
        pthread_time_coun(evnet.wait);
    }else{
        //没有任务的话，直接休眠
        sleep(eventLoop.wait());
    }
    //处理所有完成的io任务
    for eventLoop.finshIoqueue.length != 0 {
        handleIoEvent(eventLoop.finshIoqueue.shift());
    }
}

```

