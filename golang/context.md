### context的作用

类似于通道来传输数据一样，将context传递给子任务。当父任务取消cancel() 任务的时候，子context全部停止

```go
func TestContext(t *testing.T){

	ctx, cancel := context.WithCancel(context.Background())//创建一个context 带有cancel的context

	for i:=1 ; i < 5 ; i++{
		go exeChildTask(ctx,i)
	}

	cancel()//取消子任务 类似于close（channel）
	time.Sleep(time.Second)
	fmt.Println("main task cancel")
}

func exeChildTask(ctx context.Context , i int){

	for{
		select {
			case <- ctx.Done(): //cancel的时候，子context.Done（） 
				fmt.Println("child task id done" ,i)
				return
			default:

		}
		time.Sleep(time.Millisecond*3)
	}


}
```

### golang中的单例设计模式

```go
type Singleton struct {
	data string
}

var singleInstance *Singleton
var once sync.Once//使用该包

func GetSingletonObj() *Singleton {
	once.Do(func() { //多个协程只会执行一次
		fmt.Println("Create Obj")
		singleInstance = new(Singleton)
	})
	return singleInstance
}

func TestGetSingletonObj(t *testing.T) {
	var wg sync.WaitGroup
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			obj := GetSingletonObj()
			fmt.Printf("%X\n", unsafe.Pointer(obj))
			wg.Done()
		}()
	}
	wg.Wait()
}
```

### errgroup 源码

```go
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
```

