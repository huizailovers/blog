### 彻底搞懂javaScript 原型链

怎么为对象指定原型： 每个函数都有一个prototype属性，指向一个对象

```js
function f() {}
typeof f.prototype // "object"
```

js继承的设计思想就是 原型对象的所以方法和属性都能被实例对象共享。

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.color = 'white';

var cat1 = new Animal('大毛');
var cat2 = new Animal('二毛');

cat1.color // 'white'
cat2.color // 'white'
```

