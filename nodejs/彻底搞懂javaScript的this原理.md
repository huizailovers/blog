### 1.彻底搞懂javaScript的this原理

   为什么会有this关键字？
```js
var f = function () {
  console.log(x);
};
var x = 1;
var obj = {
  f: f,
  x: 2,
};

// 单独执行
f() // 1

// obj 环境执行
obj.f() // 2
```
函数在不同的运行环境中执行，使用到的是当前环境的变量。所以需要一种机制能够获取到当前的运行环境，this的设计目的就是在函数体内部，指代函数当前的运行环境。

看到上面的结果也就不奇怪了，obj.f() 是在obj的环境中执行的。所以this指向的是obj。f() 是在全局环境中执行的。this指向的是全局环境。

下面代码是在真实的考验场景

```js
var person = {
	age:18,
    say:function(){
      return this.age;
    }
}
var say = person.say;
console.log(person.say(20));//18
console.log(person.say());//18
console.log(say());//undefined
console.log(person.say({age:20})); //18
console.log(say.call(person));//18
person.age +=1
console.log(say.call(person));//19
console.log(say.apply(person,[32]));//19
```

上面代码分别考察了this的指向问题，function.call()  ， function.apply() 的作用是更改this的指向。

