# JavaScript——当单例模式遇上了赵小刀


## 前言
作为一个单身很久的boy，接触JavaScript的时候，就深深被单例模式所吸引，我只想要个女朋友，不要多，只要一个...如果可能的话，我希望是她，哈哈哈！

![](http://ww3.sinaimg.cn/bmiddle/edadc0ebgy1fi4mo5v2z0j20qo0qo40a.jpg)

## 介绍
 **单例模式** 也称为单子模式，更多的也叫做单体模式，但我更喜欢称它为单例模式。所谓单例，就是只有一个实例的存在；而单例模式，就是保证一个类只有一个实例，并且提供了一个访问它的全局访问点。很多时候我们只要拥有一个去全局对象，才有利于我们去协调整个系统。比如：一个网站，很明显弹出一个登录浮窗，不可能同时存再两个登录窗口的情况，这个时候就需要运用到这个模式。 <br>
 **ECMAScript 6.0**（简称 ES6）是 JavaScript 语言的下一代标准。它出现无疑给前端开发人员带来新的惊喜，并且逐渐在使用它，因为它太方便啦，模板字符串、class、let等一系列新的语法，阮一峰的[《ECMAScript 6 入门》](http://es6.ruanyifeng.com/)值得我们去深入学习!

## 言归正传
### 1. 当你不知道单例模式的时候...
如果你作为一个初学者，当你去创建对象的时候可能是这样的：
```javascript
var Singleton = function(name) {  
      this.name = name;
}
var a = new Singleton('赵丽颖');
console.log(a.name);  //赵丽颖
```
但是你考虑过没，我们**颖宝**只有一个啊，你不可能在去找到第二个！！！
```javascript
var b = new Singleton('我也是赵丽颖');
```
像这种情况，作为*颖火虫*是觉得不允许的，我们就需要一种新的模式来构建对象。<br>
### 2. 这个时候单例模式来了——ES5 
要显示一个单例模式并不复杂，以下代码就可以很简单的去实现它：
```javascript
var Singleton = function(name) {  
      this.name = name;
      this.instance = null;
}
Singleton.getInstance = function(name) {
  if (!this.instance) {
    this.instance = new Singleton(name);
  }
  return this.instance;
}
```
这个时候我们就需要用getInstance方法去创建对象，你会发现无论你再怎么去构建新的对象，就只有一个丽颖了！
```javascript
var a = Singleton.getInstance('赵丽颖');
var b = Singleton.getInstance('我也是赵丽颖');
console.log(a.name);  //赵丽颖
console.log(b.name);  //赵丽颖
```
那么这种情况是怎么发生的呢？首先我们得感谢Singleton中的getInstance方法，这是静态方法，只存在这个类（*JavaScript其实是一门无类的语言，在这里为了更好去说明问题，姑且把它称为类*）中，我们不能通过Singleton的实例对象在访问它。<br>
**当我们第一次调用getInstance方法的时候，因为之前instance为空，我们会new Singleton(name)来创建一个实例对象并将它的赋值给instance<br>当我们第二次去调用getInstance方法的时候,编译器会发现Singleton已经存在了instance（就是我们可爱的赵小刀同学），就会把第一次实例化的对象返回给当前对象！**<br>

### 3.当单例模式遇见了ES6
当看到阮一峰老师的书介绍class的时候，我很惊讶，和java声明类的竟然如此相似！！！如果是从Java转到Javascript的你，可以去好好研究下它们到底是不是亲生的(小编就是从java学到了js)。
```javascript
class Singleton {
  constructor(name) {
    this.name = name;
    this.instance = null;
  }
  getName() {
    alert(this.name);
  }
  static getInstance(name) {
    if(!this.instance) {
      this.instance = new Singleton(name);
    }
    return this.instance;
  }
}
```
class中用static声明的是静态属性，可想而知，它是一个内部属性。其实原理和es5一样，然而写法截然不同！class可以直接用来构造一个类，我不知道js是否在向java看齐，但是这种写法可以让我们更好的去理解js，更加容易去理解面向对象。<br>

### 4. 用闭包来实现单例模式
实现单例模式的方法有很多种，闭包也是其中一种! 首先我们先了解下它们：<br>
**[闭包的原理](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)**：当函数可以记住并访问所在的词法作用域，即是函数是在当前词法作用域之外执行，这时就产生了闭包！*简单的说，说是函数嵌套在函数里面，并且里面的函数能够持续访问到外面的函数的变量，导致变量不会被销毁，而一直被引用*<br>
**[立即执行函数IIFE](http://blog.csdn.net/qq838419230/article/details/8030078)**:它可以让你的函数在定义后立即被执行。它有一个优点就是能够为你的初始化代码提供一个作用域沙箱。*可以这样去理解，当你在代码加载完成后，不得不执行一些设置的工作，比例创建对象啊等等，但是这些代码需药一些临时的变量，初始化过程结束后，就再也不会用到，但是如果这些变量作为全局变量可以不是一个好事，所以我们需要IIFE去将代码包裹在它的局部作用域内，不会让任何变量污染全局*

**用闭包来实现单例模式**
```javascript
var Singleton = function (name) {  //曾探es6
  this.name = name;
}
Singleton.getInstance = (function() { //立即执行函数 嵌套函数 形成了闭包
  var instance = null;
  return function(name) {  //return 将闭包内部的函数 重见天日
    if(!instance) {
      instance = new Singleton(name);
    }
    return this.instance; //instance 如果不为空 说明类的里面已经实例了对象 instance已经赋值了 每次返回都是实例对象
  }
})();
```
1. function(name){} 这个匿名函数被包裹在立即执行函数的内部就形成闭包！instance变量会被匿名函数所访问，它会一直存在。因为闭包的存在，第一次实例化的对象**赵小刀**赋值给instance会一直存在作用域中，无论你在怎么去实例，她都是**赵小刀**。<br>
2. 这里还有一个注意的点就是**return function(name){}** ：就相当于一个西瓜，你不去用刀切开它，你永远不知道这个瓜甜不甜（高手除外）；所以return相当于这把刀，将闭包中的内部函数‘切开’，让它重见天日。Singleton.getInstance得到就是this.instance!!!
## 结语

**JavaScript中的闭包、this、设计模式、数据结构等等这些对于我们初学者来说，都是需要花费很多时间去学习，去深入理解。虽然开始学的很艰难，但是当你打开这一扇门后，你会发现它们也不过如此——致那些正在努力学习的亲们💗！**
附上女神萌图！
![](http://a2.qpic.cn/psb?/V11KKToj0xeyCw/CjEK8f7IRUjf8uDf*9wa4mTgnEIlkrS4DXRAa8bJLNQ!/c/dGwBAAAAAAAA&bo=owFWAQAAAAACB9Y!&rf=viewer_4)

## GitHub
https://github.com/wuyuanlijie/Singleton_Pattern

