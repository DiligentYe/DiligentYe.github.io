---
layout: post
title:  "JavaScript中函数中的this到底是什么"
date:   2018-10-05 20:20:29 +0800
categories: Javascript
---

#### 目录
1. 函数中this是什么
2. 如何改变this的指向（call, apply, bind）
3. 上述方法的不同点
4. bind方法的实现原理

#### 正文
#####  1. 函数中this是什么
this，是指向当前函数的运行环境，也就是执行上下文。

1. 当直接在浏览器全局环境中调用函数，那么，this指向的就是Window对象；

```` 
function simple() {
	console.log(this);
}
// 直接在全局找那个调用
simple();
````

![全局中调用函数中的this](http://upload-images.jianshu.io/upload_images/5796375-bf9de464b00d2c2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 当**直接调用**特定对象中的方法时，此时这个方法就运行在这个特定对象的中；
````
var runEnv = {
	name: 'yyp',
	age: 18,

	printThis: function() {
		console.log(this);
	}
}

// 运行runEnv对象中printThis方法
runEnv.printThis();
````

![特定对象中方法的this](http://upload-images.jianshu.io/upload_images/5796375-01f5edff36496bd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 特殊情况：如果我们在全局中保留了特定对象中方法的引用后， 直接在全局中执行，这是，由于此时的方法，是在全局环境中执行的，因此this指向全局；
````
var runEnv = {
	name: 'yyp',
	age: 18,

	printThis: function() {
		console.log(this);
	}
}

// 在全局中保留runEnv对象中printThis方法的引用
var cloneFun = runEnv.printThis;

// 在全局中运行这个函数
cloneFun();
````

![在全局中运行保存的新引用](http://upload-images.jianshu.io/upload_images/5796375-c2765af61cb441a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 使用new进行调用
使用new调用情况则有所不同，首先会先创建一个新对象，然后将函数的this指向指向这个新对象，然后函数中所有语句都是在这个运行作用域上执行，最后返回这个新对象。
````
function Person(name, age) {
    this.name = name;
    this.age = age;
}
// 调用Person构造函数
var person = new Person('yyp', 12);
````

![刚进如函数时this和执行完两条语句后的this](http://upload-images.jianshu.io/upload_images/5796375-4bfc76156c9486ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面四种情况可以得出，this在一个动态的概念，相对于运行过程的。

##### 2. 如何改变this的指向（call, apply, bind）
以上，是this的一些常规指向情况，在实际开发中，可以根据实际的需求，改变函数中的this指向。call, apply, bind都是函数原型上的方法，因此每个函数都可以调用这些方法。

##### 2 使用方法简介
1. call方法
**语法：** fun.call(thisArg[, arg1[, arg2[, ...]]])
thisArg：运行时this的指向
 arg1, arg2, ...： 可选传递给函数的参数
````
var person1 = {
	name: 'yyp'
}

var person2 = {
	name: 'wg'
}

function sayHi() {
	console.log('Hi, ' + this.name);
}

// 绑定this指向person1
sayHi.call(person1);

// 绑定this指向person2
sayHi.call(person2);
````

![最终运行结果](http://upload-images.jianshu.io/upload_images/5796375-06f78cf68000d8f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. apply方法
**语法：** fun.apply(thisArg, [argsArray])
thisArg：运行时this的指向
argsArray： 传递给函数的参数列表**数组**
````
var person1 = {
	name: 'yyp'
}

var person2 = {
	name: 'wg'
}

function sayHi() {
	console.log('Hi, ' + this.name);
}

// 绑定this指向person1
sayHi.apply(person1);

// 绑定this指向person2
sayHi.apply(person2);
````
上面列出的代码，和上一次的代码只是将call方法变为apply，具体apply和call用户不同，下文会单独进行解释。

3. bind方法
**语法：** fun.bind(thisArg[, arg1[, arg2[, ...]]])；
thisArg：运行时this的指向
 arg1, arg2, ...： 可选传递给函数的参数
````
var person1 = {
	name: 'yyp'
}

var person2 = {
	name: 'wg'
}

function sayHi() {
	console.log('Hi, ' + this.name);
}

// 绑定this指向person1，返回一个函数
var hello_person1 = sayHi.bind(person1);
// 执行函数
hello_person1();

// 绑定this指向person2，返回一个函数
var hello_person2 = sayHi.bind(person2);
// 执行函数
hello_person2();
````
最终的运行结果和上面两种情况中一样。

##### 3. 上述方法的不同点
1.  call和apply方法的不同之处
call，apply方法主要是在传递参数的方式上不同，上面举例的函数不需要提供参数，因此，两个可以交替使用。
但是当函数调用需要传递参数时，两者的使用方法就不一样了，有上面提供的语法格式可以知道，**call方法将需要传递的参数平铺，一个一个的传递，而apply方法，则需要将所有需要传递的参数放到一个数组中，然后传递给函数**。
````
var runEnv = {
	name: 'yyp'
}

function sayHi(str) {
	console.log(str + ' ' + this.name);
}

// 使用call方法绑定this，并传递参数，将参数依次传递
sayHi.call(runEnv, 'Hi!');

// 使用bind方法绑定this，并传递参数，所有传入的参数，需要先组成数组，然后作为第二个参数传入
sayHi.apply(runEnv, ['Hello!']);
````
**在使用过程中如何选择**
1.1 如果给定参数列表是数组形式，选用apply
````
var items = [1, 2, 3, 4];

// Math对象中的max方法，可以接受多个参数
// 如果需要梳理的元素是一个数组，那么我们可以使用apply使用方法
// 不用将数组中的元素一个个提取出来，在进行处理
var max = Math.max.apply(null, items);

console.log(max);
````
1.2 从性能方面考虑

![ECMA-262文档中call方法定义](http://upload-images.jianshu.io/upload_images/5796375-62cd08d9be00d3f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![ECMA-262文档中apply方法定义](http://upload-images.jianshu.io/upload_images/5796375-2e6321f1913b6334.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从这两个文档中我们可以发现，调用call方法，如果传入参数，直接从第二个参数从左向右将参数添加到argList中即可，而apply方法要调用一个CreateListFromArrayLike方法，将传入的数组元素处理为合法的argList，因此在可能存在性能上的差异。

jsPerf中对其运行性能进行对比发现[对比结果](https://github.com/coderwin/__/issues/6)。在参数较少(1-3)个时采用call的方式调用（lodash就是采用这种方式重写的）。

2. bind方法和其他两种方法的不同之处
bind方法，是ES5才提出的，其主要的不同就在于，call和apply方法，**是在指定的作用域上直接运行函数**，而bind方法是**创建一个新的函数**供后期直接使用，其传入的参数，也会直接绑定到这个新函数上。

2.1 bind函数的运行作用域
即使在全局作用域中运行，或者用call或者apply绑定其他作用域，都不会改变其运行作用域。
````
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// 创建一个空对象
var emptyObj = {};
// 将函数的this指向emptyObj，获取一个新的函数
var bindPerson = Person.bind(emptyObj);
// 在全局中运行该函数
bindPerson('yyp', 18);
````

![运行后emptyObj对象内容](http://upload-images.jianshu.io/upload_images/5796375-27877ff3252be3a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行绑定后获取的新函数时，此时我们已经将this绑定到制定的空对象上，运行bindPerson函数，相当于运行Person.call(emptyObj, 'yyp', 18);因此会在emptyObj上添加两个属性name和age。

2.2 bind时绑定提前绑定参数
如果在调用bind函数时，提供传入参数时，此时这些参数，将会直接绑定到新函数上，后续执行新函数传入的参数将会追加到这些参数后面。
````
function Person(name, age) {
	this.name = name;
	this.age = age;
}

var emptyObj = {};
// 此时提供一个参数
var bindPerson = Person.bind(emptyObj, 'yyp');
// 调用时提供一个参数，此时效果和前一个保持一致
bindPerson(18);

// 提供两个参数时，根据实际参数情况，会丢掉后面的参数
// bindPerson('yyp', 18);
````
2.3 使用new调用函数时，出现的问题
使用new函数进行调用时，this不会指向bind时设定的对象，而是和直接使用new调用原始函数行为保持一致，但是之前提前绑定的参数，还是会生效。
````
function Person(name, age) {
    this.name = name;
    this.age = age;
}

var emptyObj = {};
var bindPerson = Person.bind(emptyObj);

var person = new bindPerson('yyp', 18);
````

![使用new调用函数时运行结果](http://upload-images.jianshu.io/upload_images/5796375-52a6407612cd98e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面运行结果可以发现，使用new调用时，绑定的作用于失效，this原先的this，因此emptyObj还是为空。

##### 4. bind方法的实现原理
````
Function.prototype.bind = function(oThis) {
	// 判断调用这个方法的对象是不是一个函数
	if (typeof this !== 'function') {
		// closest thing possible to the ECMAScript 5
		// internal IsCallable function
		throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
	}
	// 处理传入的参数部分
	// aArgs：保存绑定时传入的参数
	// fToBind：指向需要绑定的函数
	// fNOP： 空函数
	// fBound：将要返回的函数引用
	var aArgs = Array.prototype.slice.call(arguments, 1),
		fToBind = this,
		fNOP = function() {},
		fBound = function() {
			// 绑定函数执行时运行的处理逻辑
			// 如果当前任何环境中运行，执行函数中的this为之前制定的作用域，即作用域不会做二次绑定
			// 如果使用new进行调用时，执行函数中的this不改变
			return fToBind.apply(this instanceof fNOP ?
				this :
				oThis,
				// 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
				aArgs.concat(Array.prototype.slice.call(arguments)));
		};

	// 维护原型关系
	if (this.prototype) {
		// Function.prototype doesn't have a prototype property
		fNOP.prototype = this.prototype;
	}

	// 返回的函数fBound继承fNOP，是fNOP的一个实例，
	// 作用：1. 维持原型链
	// 2. 用new进行该函数调用时，此时的this是fNOP的一个实例
	fBound.prototype = new fNOP();

	return fBound;
};
````    
由上面代码可以了解到，调用bind函数进行绑定后，会返回一个新的函数，并且将调用时传入的参数保存起来。当调用这个绑定函数时，先判断this的指向，如果是使用new调用，this将会是bind函数的实例(绑定函数又是内部fNOP的实例)，直接使用当前this；如果是其他情况，则直接在之前绑定的运行作用域上执行。
