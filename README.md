\__proto__ vs prototype
===
首先，每个对象都有一个内部链接到另一个对象，称为它的原型 prototype。该原型对象又有自己的原型，等等，直到达到一个以null为原型的对象。根据定义，null没有原型，并且作为这个原型链 prototype chain中的最终链接。

JavaScript对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依此层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾(prototype为null的时候)。

\__proto-__
---
隐式原型，所有对象有具有的属性，指向构造这个对象的构造函数(构造器)的原型对象(prototype).

prototype
---
每个函数，也就是Function(也可以说是构造器)才具有的属性，这个对象了都有一个属性constructor，并且指回这个构造器本身，如function Func1(), 则Func1.prototype.constructor === Func1。

隐式原型指向创建这个对象的函数(constructor)的prototype

Function
---
这是一个**特殊的对象**，出来和其他对象一样有上述**\_\_proto\_\_**属性之外，还有自己的属性——**原型属性(prototype)**，这个属相是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法(我们把这个对象叫做原型对象)。原型对象也有一个自己的属性，叫constructor(当然也有属性\_\_proto\_\_)，这个属性也是一个指针，指回原构造函数。所以，这个地方会相处一个无限循环，如下图。
![__proto__-vs-prototype-01](http://ocidfae4n.bkt.clouddn.com/f583c0ec9b3f503fa855cbfc7006feea.png)

instanceof
---
<pre><code>object instanceof constructor</code></pre>

**instanceof**运算符用来测试一个对象在其原型链中是否存在该构造函数(即constructor)的prototype(即constructor.prototype存在,或者指向constructor.pototype)属性, 比如func instanceof Func，则测试是否存在Func.prototype属性(即Func.prototype不为false)。

hasOwnProperty()
---
用来判断一个对象是否有你给出的属性或对象。需要注意的是，次方法无法判断该对象的原型链上是否具有该属性，该属性必须是对象本身的一个成员。

isPrototypeOf()
---
该方法用来测试一个对象是否存在于另个对象的原型链上。

Object.getPrototypeOf() 
---
该方法返回指定对象的原型（即, 内部[[Prototype]]属性的值）。

---

```js  
function Func1(){}

Func1.prototype.a = 10;
var func1 = new Func1();
console.log('func1.__proto__=',func1.__proto__)

var obj = { a: 10, b: 10}
var obj = { a: 10, b: 10}  //obj是一个对象，所以具有属性__proto__，但不是函数所以没有prototype，它的构造函数是Object, 所以
                                          //obj.prototype === Object.prototype 结果为true
                                          //Object.constructor===Function 结果为true

console.log('obj.__proto__=== Object.prototype = ', obj.__proto__ === Object.prototype); //true
console.log('Object.constructor === Function = ', Object.constructor === Function); //true

function Func1() {}
Func1.prototype.a = 10;
var func1 = new Func1();
```

Func1.prototype: 是构造器Func1的原型对象，所以没有prototype属性，但有__proto__(每个对象都有，包括Function)和constructor属性，而Func1是由Object这个构造器构造的，所以:
Func1.prototype.prototype === undefined //true  
Func1.prototype.__proto__ === Object.prototype //true  
Func1.prototype.prototype === Function.prototype:  false， 如下图：

```js
console.log('func1 instanceof Func1: ', func1 instanceof Func1); //true  
console.log('func1 instanceof Function: ', func1 instanceof Function); //false, 因为func1.__proto__ ====  Func1.prototype, 所以func1是Func1的实例  
                                                                                                                         //而Func1是一个对象，所以，Func1.prototype,.__proto__ ==== Object.prototype,  
                                                                                                                         //最后Object.prototype.constructor ==== Object  
console.log('func1instanceofObject:',func1instanceofObject);//true  


Function
Function.prototype:  function () { [native code] } 一个空函数  
Function.prototype.constructor === Function //true  
Function.prototype.constructor === Object  //false  
Function.prototype.__proto__ === Object.prototype //true  
Function.__proto__ === Function.prototype //true  
Function instanceof Function === true //因为  
Function instanceof Object === true //true，因为Function.__proto__ === Function.prototype, 虽然Function.prototype === function(){},  
                                                                  //Function.prototype.constructor === Function, 所以 Function instanceof Function === true，
                                                                  //又由于Function.prototype.__proto__ === Object.prototype，
                                                                   //而Object.prototype.constructor = Object  

```

所有构造器/函数的__proto__都指向Function.prototype，它是一个空函数Function()（Empty function），也就是说，他们都是有Function()构造的，以下都是内嵌原生构造器/函数：
```js
Number = new Function()
Boolean = new Function()
String = new Function()
var Object = new Function()  
var Function = new Function()  
var Array = new Function()  
var RegExp = new Function()  
var Error = new Function()  
var Date = new Function()    

//所以，
Number.__proto__ === Function.prototype  // true  
Boolean.__proto__ === Function.prototype // true  
String.__proto__ === Function.prototype    // true  
Object.__proto__ === Function.prototype   // true  
Function.__proto__ === Function.prototype // true  
Array.__proto__ === Function.prototype      // true  
RegExp.__proto__ === Function.prototype  // true  
Error.__proto__ === Function.prototype   // true  
Date.__proto__ === Function.prototype    // true  

Function.__proto__.__proto__ === Object.prototype  // true  

Math.__proto__ === Object.prototype  // true  
JSON.__proto__ === Object.prototype  // true  


//Object
Object.prototype.__proto__ === null  // true
Object.prototype.constructor === Object // true
Object.prototype === Object //false
Object.prototype === Function //false，
Object.prototype instanceof Object: false，//因为Object.prototype.\__proto\__ === null,
                                                                                 //说明Object.prototype是一个新的对象(官方称为Object的原型对象)，
                                                                                 //既不是Object自身，也不由Function

Object.prototype: 是一个对象，这个对象是由原生创建的
Object.prototype.prototype === undefined //true

Boolean/String/Array/RegExp/Error/Date都具有特性和以下Number相同
Number.prototype：包含Number构造期创建实例的共享方法和属性
Number.prototype.__proto__=== Object.prototype， 即只想创建Number.prototype对象的构造期的原型对象

Array.prototype
Array.prototype.__proto__=== Object.prototype
```

Function.prototype也是唯一一个typeof XXX.prototype为 “function”的prototype。其它的构造器的prototype都是一个对象。如下
```js
console.log(typeof Function.prototype) // function  
console.log(typeof Object.prototype)   // object  
console.log(typeof Number.prototype)   // object  
console.log(typeof Boolean.prototype)  // object  
console.log(typeof String.prototype)   // object  
console.log(typeof Array.prototype)    // object  
console.log(typeof RegExp.prototype)   // object  
console.log(typeof Error.prototype)    // object  
console.log(typeof Date.prototype)     // object  
console.log(typeof Object.prototype)   // object  
```
