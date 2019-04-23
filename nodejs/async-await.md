### async await 示例

```js
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
```

输出结果是111，333 ，222， hellowrold，

注意点：

async中没有await，则就是普通的函数。async返回的是promise对象

await后面跟的是 promise对象

async中使用try-catch 来捕获 promise.reject()的异常

使用async标注的函数是异步的函数，如果不用await等待该函数的话就是异步执行

```js
  async function timeout(ms) {
    console.log(111)
    await new Promise((resolve) => {
        console.log(333)
      setTimeout(resolve, ms);
    });
    console.log(222)
  }
  
  async function asyncPrint(value, ms) {
    timeout(ms); //此处没有使用await
    console.log(value);
  }
  
  asyncPrint('hello world', 5000);
```



输出结果是 111 ， 333,  helloworld , 222

timeout 函数是局部同步的，也就是  111 ，333， 222 是局部同步的