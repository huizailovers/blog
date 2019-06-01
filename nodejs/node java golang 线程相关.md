### node异步机制

```js
//node 是基于事件驱动来实现的异步。node进程启动的时候，就会启动一个主进程。主进程循环处理队列中的时候，如果是io等操作的话，则将回调函数和请求参数存入到队列中。异步的io操作会从线程池中取出一个线程去执行操作。执行完成后。将事件从新加入到队列中。任务完成
//比较常用的是promise es6 async await

async function timeout(ms) {
    console.log(111)
    await new Promise((resolve) => {
        console.log(333)
      setTimeout(resolve, ms);
    });
    console.log(222)
  }
  
  async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
  }
  
  asyncPrint('hello world', 5000);


//golang 是基于协程。相对于系统级别的线程来说，更加轻便搞笑。MPG模式来提高执行的效率。
go func(){} 
//使用channel 来进行线程之间的通信，不用考虑多线程的安全问题。
//目前 golang 1.9后开始使用 context 来进行任务的管理。 errorGroup 用来将协程处理的error信息返回
type Group struct {
	cancel func()

	wg sync.WaitGroup

	errOnce sync.Once
	err     error
}

//创建context 并且将cancel函数赋值到 group 中
func WithContext(ctx context.Context) (*Group, context.Context) {
	ctx, cancel := context.WithCancel(ctx)
	return &Group{cancel: cancel}, ctx
}
//group.wait 函数具有了 wait 等待协程的机制。同时返回协程的错误信息
func (g *Group) Wait() error {
	g.wg.Wait()
	if g.cancel != nil {
		g.cancel() //调用取消函数，所有的子任务都会contx.done（）
	}
	return g.err
}

.
func (g *Group) Go(f func() error) {
	g.wg.Add(1)

	go func() {
		defer g.wg.Done()

		if err := f(); err != nil {//该协程执行函数
			g.errOnce.Do(func() { //只会执行一次 
				g.err = err//将错误赋值到errGroup
				if g.cancel != nil {
					g.cancel() //取消context
				}
			})
		}
	}()
}

//java1.8 以后也开始有异步控制流程的类 
//java 多线程 实现方式继承 Thread 实现Runnable 等。线程之间的同步使用sychronized 。相对来说
//性能较差，concurrent并发包的出现，lock锁机制的出现和线程池的出现带来了一定的改善。//CompletableFuture
//类似于node的异步机制。
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "hello");

future.complate();
future.complateException();




```

