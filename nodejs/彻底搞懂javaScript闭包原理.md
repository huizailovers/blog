###2.彻底搞懂js闭包

以前也看过闭包，总以为明白了，但是还是理解的不是很透彻，因此从内存的角度来分析闭包。

一下内存参考自
http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html

```js
var fun = function(){
  var count = 0;
  return function(){
    return count +=1;
  }
}
var fn = fun();
console.log(fn());
console.log(fn());
```

上面代码的正确输出值是多少呢？

答案是： 1， 2，3

原因是： 返回值是一个函数，函数每次调用的时候都会count ++ ，闭包的作用有两个，一个是访问函数内部的变量， 另一个作用是是父级函数中的这些变量始终保持在内存中。因此 count已经保持在了内存中，每次调用的时候，不会重新赋值为0。

猜一下下面代码的执行结果

```js
var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};

　　　　}

　　};

　　alert(object.getNameFunc()());
==== 对比 ====
var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　var that = this;
　　　　　　return function(){
　　　　　　　　return that.name;
　　　　　　};

　　　　}

　　};

　　alert(object.getNameFunc()());
```

