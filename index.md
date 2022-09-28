## JavaScript基础

### 变量和类型

#### 1.JavaScript规定了几种数据类型

js目前共定义了8种数据类型，其中包括：Undefined,Null,Boolean，Number，String，Object， Symbol,BigInt

#### 2.JavaScript对象的底层数据结构是什么

JavaScript基本类型数据都是直接按值存储在栈中的(Undefined、Null、不是new出来的布尔、数字和字符串)，每种类型的数据占用的内存空间的大小是确定的，并由系统自动分配和自动释放。这样带来的好处就是，内存可以及时得到回收，相对于堆来说 ，更加容易管理内存空间。

JavaScript引用类型数据被存储于堆中 (如对象、数组、函数等，它们是通过拷贝和new出来的）。其实，说存储于堆中，也不太准确，因为，引用类型的数据的地址指针是存储于栈中的，当我们想要访问引用类型的值的时候，需要先从栈中获得对象的地址指针，然后，在通过地址指针找到堆中的所需要的数据。

#### 3.Symbol类型在实际开发中的应用、可手动实现一个简单的Symbol

1.使用Symbol来替代常量

```
const TYPE_AUDIO = Symbol()
const TYPE_VIDEO = Symbol()
const TYPE_IMAGE = Symbol()
```

2.使用Symbol来作为对象属性名(key)

```
const PROP_NAME = Symbol()
const PROP_AGE = Symbol()
let obj = {
  [PROP_NAME]: "一斤代码"
}
obj[PROP_AGE] = 18
```

3.使用Symbol定义类的私有属性/方法

```
// a.js
const PASSWORD = Symbol()
class Login {
  constructor(username, password) {
    this.username = username
    this[PASSWORD] = password
  }

  checkPassword(pwd) {
      return this[PASSWORD] === pwd
  }
}
export default Login
// b.js
import Login from './a'
const login = new Login('admin', '123456')
login.checkPassword('123456')  // true
login.PASSWORD  // oh!no!
login[PASSWORD] // oh!no!
login["PASSWORD"] // oh!no!

```

由于Symbol常量PASSWORD被定义在a.js所在的模块中，外面的模块获取不到这个Symbol，也不可能再创建一个一模一样的Symbol出来（因为Symbol是唯一的），因此这个PASSWORD的Symbol只能被限制在a.js内部使用，所以使用它来定义的类属性是没有办法被模块外访问到的，达到了一个私有化的效果。

4.注册和获取全局Symbol window中创建的Symbol实例总是唯一的，而我们需要的是在所有这些window环境下保持一个共享的Symbol。这种情况下，我们就需要使用另一个API来创建或获取Symbol，那就是Symbol.for()，它可以注册或获取一个window间全局的Symbol实例：

```
let gs1 = Symbol.for('global_symbol_1')  //注册一个全局Symbol
let gs2 = Symbol.for('global_symbol_1')  //获取全局Symbol
gs1 === gs2  // true

```

#### 4.JavaScript中的变量在内存中的具体存储形式

基本类型是保存在栈内存中的简单数据段，它们的值都有固定的大小，保存在栈空间，通过按值访问
 引用类型是保存在堆内存中的对象，值大小不固定，栈内存中存放的该对象的访问地址指向堆内存中的对象，JavaScript不允许直接访问堆内存中的位置，因此操作对象时，实际操作对象的引用

```
let a1 = 0; // 栈内存
let a2 = "this is string" // 栈内存
let a3 = null; // 栈内存
let b = { x: 10 }; // 变量b存在于栈中，{ x: 10 }作为对象存在于堆中
let c = [1, 2, 3]; // 变量c存在于栈中，[1, 2, 3]作为对象存在于堆中

```

![image.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/16/173566d894b7ea60~tplv-t2oaga2asx-watermark.awebp)

#### 5.基本类型对应的内置对象，以及他们之间的装箱拆箱操作

**内置对象：** Object是 JavaScript 中所有对象的父对象 数据封装类对象：Object、Array、Boolean、Number 和 String 其他对象：Function、Math、Date、RegExp、Error。 特殊的基本包装类型(String、Number、Boolean) arguments: 只存在于函数内部的一个类数组对象 **装箱：** 把基本数据类型转化为对应的引用数据类型的操作，装箱分为隐式装箱和显示装箱 **隐式装箱**

```
let a = 'sun'
let b = a.indexof('s') // 0 // 返回下标

```

上面代码在后台实际的步骤为：

```
let a = new String('sun')
let b = a.indexof('s')
a = null

```

**实现机制：**

1.创建String类型的一个实例； 2.在实例上调用指定的方法； 3.销毁这个实例；

**显示装箱** 通过内置对象可以对Boolean、Object、String等可以对基本类型显示装箱 let a = new String('sun')

**拆箱：** 拆箱和装箱相反，就是把引用类型转化为基本类型的数据，通常通过引用类型的valueof()和toString（）方法实现

```
let name = new String('sun')
let age = new Number(24)
console.log(typeof name) // object
console.log(typeof age) //  object
// 拆箱操作
console.log(typeof age.valueOf()); // number // 24  基本的数字类型
console.log(typeof name.valueOf()); // string  // 'sun' 基本的字符类型
console.log(typeof age.toString()); // string  // '24' 基本的字符类型
console.log(typeof name.toString()); // string  // 'sun' 基本的字符类型

```

#### 6.理解值类型和引用类型

![image.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/16/173566d8945f5ec2~tplv-t2oaga2asx-watermark.awebp)

#### 7.null和undefined的区别

##### null表示"没有对象"，即该处不应该有值。典型用法是:

（1） 作为函数的参数，表示该函数的参数不是对象。
（2） 作为对象原型链的终点。

##### undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。典型用法是：

 （1）变量被声明了，但没有赋值时，就等于undefined。
 （2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
 （3）对象没有赋值的属性，该属性的值为undefined。
 （4）函数没有返回值时，默认返回undefined。

#### 8.至少可以说出三种判断JavaScript数据类型的方式，以及他们的优缺点，如何准确的判断数组类型

##### 1、typeof:（可以对基本类型做出准确的判断，但对于引用类型，用它就有点力不从心了）

typeof 返回一个表示数据类型的字符串，返回结果包括：number、boolean、string、object、undefined、function等6种数据类型。

##### 2、instanceof

instanceof是用来判断A是否为B的实例时，表达式为：A instanceof B,如果 A是B的实例，则返回true; 否则返回false 在这里特别注意的是 instanceof检测的是原型

##### 3 Object.prototype.toString

toString是Object原型对象上的一个方法，该方法默认返回其调用者的具体类型，更严格的讲，是 toString运行时this指向的对象类型, 返回的类型格式为[object,xxx],xxx是具体的数据类型，其中包括：String,Number,Boolean,Undefined,Null,Function,Date,Array,RegExp,Error,HTMLDocument,… 基本上所有对象的类型都可以通过这个方法获取到。 **4 constructor 查看对象对应的构造函数** construvtor在对应对象的原型下面，是自动生成的，当我们写一个构造函数的时候，程序自动添加，构造函数名.prototype.constructor = 构造函数名 ![image.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/16/173566d894e55e92~tplv-t2oaga2asx-watermark.awebp)

#### 9.可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用

![image.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/16/173566d897f4aba3~tplv-t2oaga2asx-watermark.awebp)

#### 10.出现小数精度丢失的原因，JavaScript可以存储的最大数字、最大安全数字，JavaScript处理大数字的方法、避免精度丢失的方法

0.1+0.2不等于0.3，是因为计算机进行计算时先转化成二进制，二浮点数用二进制表示时是无穷位的，IEEE754标准中用64位表示（1位用来表示符号位，11用来表示指数，52位表示尾数）会截断后面的位数，再转化成十进制，就有了误差。

**最大数字：**

对于整数，前端出现问题的几率可能比较低，毕竟很少有业务需要需要用到超大整数，只要运算结果不超过 Math.pow(2, 53) 就不会丢失精度。

对于小数，前端出现问题的几率还是很多的，尤其在一些电商网站涉及到金额等数据。解决方式：把小数放到位整数（乘倍数），再缩小回原来倍数（除倍数）

最安全的数字-Math.pow(2, 53)-1,到+Math.pow(2, 53)-1

### 原型和原型链

**原型:** 对象中固有的`__proto__`属性，该属性指向对象的`prototype`原型属性。

**原型链:** 当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是`Object.prototype`所以这就是我们新建的对象为什么能够使用`toString()`等方法的原因。

**特点:** `JavaScript`对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变。

#### 1.instanceof的底层实现原理，手动实现一个instanceof

```
function new_instance_of(leftVaule, rightVaule) {     
  let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值    
  leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值    
  while (true) {    	
    if (leftVaule === null) {           
      return false;	        
    }        
    if (leftVaule === rightProto) {            
      return true;	        
    }         
    leftVaule = leftVaule.__proto__     
  }
  }
```

#### 2.实现继承的几种方式以及他们的优缺点

```
// 原型继承。第一种是以`原型链的方式来实现继承`，但是这种实现方式存在的缺点是，在包含有引用类型的数据时，会被所有的实例对象所共享，容易造成修改的混乱。还有就是在创建子类型的时候不能向超类型传递参数。
```function SuperType(){    this.property = true;}SuperType.prototype.getSuperValue = function(){    return this.property;}function SubType(){    this.subProty =false;}SubType.prototype = new SuperType();var instance = new SubType();console.log(instance.getSuperValue())```
// 借用构造函数。第二种方式是使用`借用构造函数`的方式，这种方式是通过在子类型的函数中调用超类型的构造函数来实现的，这一种方法解决了不能向超类型传递参数的缺点，但是它存在的一个问题就是无法实现函数方法的复用，并且超类型原型定义的方法子类型也没有办法访问到。function SuperType(name) {    this.name = name;}function SubType(){    SuperType.call(this, 'demo');    this.age = 18;}var instance = new SubType();console.log(instance.name);console.log(instance.age);
// 组合继承。第三种方式是`组合继承`，组合继承是将原型链和借用构造函数组合起来使用的一种方式。通过借用构造函数的方式来实现类型的属性的继承，通过将子类型的原型设置为超类型的实例来实现方法的继承。这种方式解决了上面的两种模式单独使用时的问题，但是由于我们是以超类型的实例来作为子类型的原型，所以调用了两次超类的构造函数，造成了子类型的原型中多了很多不必要的属性。function SuperType(name){    this.name = name;    this.colors = ['red'];}SuperType.prototype.sayName = function(){    console.log(this.name);}function SubType(name,age) {    SuperType.call(this,name);    this.age = age;}SubType.prototype = new SuperType();SubType.prototype.sayAge = function(){    console.log(this.age);}var instance = new SubType('demo',18);instance.sayAge();instance.sayName();
// 原型式继承。第四种方式是`原型式继承`，原型式继承的主要思路就是基于已有的对象来创建新的对象，实现的原理是，向函数中传入一个对象，然后返回一个以这个对象为原型的对象。这种继承的思路主要不是为了实现创造一种新的类型，只是对某个对象实现一种简单继承，ES5 中定义的 Object.create() 方法就是原型式继承的实现。缺点与原型链方式相同。function object(o) {    function F(){};    F.prototype = o;    return new F();}var person = {    name: 'tom'}var anotherPerson = object(person)console.log(anotherPerson.name)// 寄生式继承。第五种方式是`寄生式继承`，寄生式继承的思路是创建一个用于封装继承过程的函数，通过传入一个对象，然后复制一个对象的副本，然后对象进行扩展，最后返回这个对象。这个扩展的过程就可以理解是一种继承。这种继承的优点就是对一个简单对象实现继承，如果这个对象不是我们的自定义类型时。缺点是没有办法实现函数的复用。function createAnother(original){    var clone =Object.create(original);    clone.sayHi = function () {        console.log('hi');    }    return clone;}var person = {    name: 'tom'}var anotherPerson = createAnother(person);console.log(anotherPerson.name)anotherPerson.sayHi();// 寄生组合式继承。第六种方式是`寄生式组合继承`，组合继承的缺点就是使用超类型的实例做为子类型的原型，导致添加了不必要的原型属性。寄生式组合继承的方式是使用超类型的原型的副本来作为子类型的原型，这样就避免了创建不必要的属性。function SuperType(name) {    this.name = name;}SuperType.prototype.sayName = function(){    console.log(this.name);}function SubType(name,age){    SuperType.call(this,name);    this.age = age;}function inheritPrototype(subType,superType){    var prototype = Object.create(superType.prototype);    prototype.constructor =subType;    subType.prototype = prototype;}inheritPrototype(SubType,SuperType);var person = new SubType('zhangsan',18);person.sayName()
```

#### 3.可以描述new一个对象的详细过程，手动实现一个new操作符

```
function Person (name,age){    this.name = name;    this.age = age;    this.say = function () {        console.log("I am " + this.name)    }}function realizeNew(){    let obj = {};    let Con = [].shift.call(arguments);    obj.__proto__ = Con.prototype;    let result = Con.apply(obj,arguments);    return typeof result === 'object'? result : obj}var person1 =realizeNew(Person,'张三')
```

#### 4.理解es6 class构造以及继承的底层实现原理

### 作用域和闭包

闭包是指有权访问另一个函数作用域中的变量的函数 ——《JavaScript高级程序设计》

当函数可以记住并访问所在的词法作用域时，就产生了闭包，

即使函数是在当前词法作用域之外执行 ——《你不知道的JavaScript》

- 闭包用途：
  1. 能够访问函数定义时所在的词法作用域(阻止其被回收)
  2. 私有化变量
  3. 模拟块级作用域
  4. 创建模块
- 闭包缺点：会导致函数的变量一直保存在内存中，过多的闭包可能会导致内存泄漏

#### 1.理解词法作用域和动态作用域

**词法作用域**，函数的作用域在函数定义的时候就决定了（取决于函数定义的位置）
 **动态作用域**，函数的作用域在函数调用的时候就决定了（取决于函数的调用） js采用的是词法作用域

#### 2.理解JavaScript的作用域和作用域链

作用域就是一个独立的地盘，让变量不会外泄、暴露出去。也就是说作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。 ES6 之前 JavaScript 没有块级作用域,只有全局作用域和函数作用域。ES6的到来，为我们提供了‘块级作用域’,可通过新增命令let和const来体现。

```
function outFun2() {    var inVariable = "内层变量2";}outFun2();//要先执行这个函数，否则根本不知道里面是啥console.log(inVariable); // Uncaught ReferenceError: inVariable is not defined
```

**作用域链** 在 JavaScript 中使用变量时，JavaScript 引擎将尝试在当前作用域中查找变量的值。如果找不到变量，它将查找外部作用域并继续这样做，直到找到变量或到达全局作用域为止。

如果仍然找不到变量，它将在全局作用域内隐式声明变量（如果不是在严格模式下）或返回错误。 ####3.理解JavaScript的执行上下文栈，可以应用堆栈信息快速定位问题 执行上下文是当前 JavaScript 代码被解析和执行时所在环境的抽象概念。

**执行上下文的类型** 执行上下文总共有三种类型
 **全局执行上下文**：只有一个，浏览器中的全局对象就是 window 对象，this 指向这个全局对象。
 **函数执行上下文**：存在无数个，只有在函数被调用的时候才会被创建，每次调用函数都会创建一个新的执行上下文。
 **Eval 函数执行上下文**： 指的是运行在 eval 函数中的代码，很少用而且不建议使用。

**执行栈** 执行栈，也叫调用栈，具有 LIFO（后进先出）结构，用于存储在代码执行期间创建的所有执行上下文。 首次运行JS代码时，会创建一个全局执行上下文并Push到当前的执行栈中。每当发生函数调用，引擎都会为该函数创建一个新的函数执行上下文并Push到当前执行栈的栈顶。 根据执行栈LIFO规则，当栈顶函数运行完成后，其对应的函数执行上下文将会从执行栈中Pop出，上下文控制权将移到当前执行栈的下一个执行上下文。

**执行上下文的创建** 执行上下文分两个阶段创建：
 1）创建阶段；
 2）执行阶段
 **创建阶段**
 1、确定 this 的值，也被称为 This Binding。
 2、LexicalEnvironment（词法环境） 组件被创建。
 3、VariableEnvironment（变量环境） 组件被创建。
 **This Binding:**
 在全局执行上下文中，this 的值指向全局对象，在浏览器中，this 的值指向 window 对象。 在函数执行上下文中，this 的值取决于函数的调用方式。如果它被一个对象引用调用，那么 this 的值被设置为该对象，否则 this 的值被设置为全局对象或 undefined（严格模式下）
 **词法环境**
 在词法环境中，有两个组成部分：
（1）环境记录（environment record）
 （2）对外部环境的引用 环境记录是存储变量和函数声明的实际位置。 对外部环境的引用意味着它可以访问其外部词法环境。
 **变量环境:**
 它也是一个词法环境，其 EnvironmentRecord 包含了由  VariableStatements 在此执行上下文创建的绑定。 如上所述，变量环境也是一个词法环境，因此它具有上面定义的词法环境的所有属性。 在 ES6 中，LexicalEnvironment 组件和 VariableEnvironment 组件的区别在于前者用于存储函数声明和变量（ let 和 const ）绑定，而后者仅用于存储变量（ var ）绑定。 在创建阶段，代码会被扫描并解析变量和函数声明，其中函数声明存储在环境中，而变量会被设置为 undefined（在 var 的情况下）或保持未初始化（在 let 和 const 的情况下） 这就是为什么你可以在声明之前访问 var 定义的变量（尽管是 undefined ），但如果在声明之前访问 let 和 const 定义的变量就会提示引用错误的原因。

这就是我们所谓的变量提升。

#### 4.this的原理以及几种不同使用场景的取值

##### 一、this原理

this 既不指向函数自身，也不指函数的词法作用域，而是调用函数时的对象！

##### 二、使用场景

**一）普通函数的调用，this指向的是Window**

```
var name = '卡卡';
function cat(){    
  var name = '有鱼';    
  console.log(this.name);//卡卡    
  console.log(this);//Window {frames: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …}
 }
 cat();
```

**（二）对象的方法，this指的是该对象**

1、一层作用域链时，this指的该对象

```
var name = '卡卡';
var cat = {    
  name:'有鱼',    
  eat:function(){        
    console.log(this.name);//有鱼    
  }
}
cat.eat();
```

2、多层作用域链时，this指的是距离方法最近的一层对象

```
var name = '卡卡';
var cat = {    
  name:'有鱼',    
  eat1:{        
    name:'年年',        
    eat2:function(){           
      console.log(this.name);//年年        
    }    
   }
 }
 cat.eat1.eat2();
```

这里需要注意一个情况，如果cat.eat1.eat2这个结果赋值给一个变量eat3，则eat3()的值是多少呢？

```
var eat3 = cat.eat1.eat2;eat3(); // 卡卡
```

答案是[卡卡]，这个是因为经过赋值操作时，并未发起函数调用，eat3()这个才是真正的调用，而发起这个调用的是根对象window，所以this指的就是window，this.name=卡卡

**（三）构造函数的调用，this指的是实例化的新对象**

```
var name = '卡卡';
function Cat(){    
  this.name = '有鱼';    
  this.type = '英短蓝猫';
}
var cat1 = new Cat();
console.log(cat1);// 实例化新对象 
Cat {name: "有鱼", type: "英短蓝猫"}
console.log(cat1.name);// 有鱼
```

**（四）apply和call调用时，this指向参数中的对象**

```
var name = '有鱼';
function eat(){    
  console.log(this.name);
}
var cat = {    
  name:'年年',
}
var dog = {    
  name:'高飞',
}
eat.call(cat);// 年年
eat.call(dog);// 高飞
```

**（五）匿名函数调用，指向的是全局对象**

```
var name = '卡卡';
var cat = {    
  name:'有鱼',    
  eat:(function(){        
    console.log(this.name);//卡卡    
    })()
}
cat.eat;
```

**（六）定时器中调用，指向的是全局变量**

```
var name = '卡卡';
var cat = setInterval(function(){    
  var name = '有鱼';    
  console.log(this.name);// 卡卡    
  clearInterval(cat);
},500);
```

总结：
 ①普通函数的调用，this指向的是window
 ②对象方法的调用，this指的是该对象，且是最近的对象
 ③构造函数的调用，this指的是实例化的新对象
 ④apply和call调用，this指向参数中的对象
 ⑤匿名函数的调用，this指向的是全局对象window
 ⑥定时器中的调用，this指向的是全局变量window

#### 5.闭包的实现原理和作用，可以列举几个开发中闭包的实际应用

包就是能够读取其他函数内部变量的函数 它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

```
function f1() {    
  var n = 999;    
  nAdd = function () { n += 1 }    
  function f2() { alert(n); }    
  return f2;
}
var result = f1();result(); // 999nAdd();result(); // 1000
```

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。 为什么会这样呢？
原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。 这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。

#### 6.理解堆栈溢出和内存泄漏的原理，如何防止

**1、内存泄露**：是指申请的内存执行完后没有及时的清理或者销毁，占用空闲内存，内存泄露过多的话，就会导致后面的程序申请不到内存。因此内存泄露会导致内部内存溢出 **2、堆栈溢出**：是指内存空间已经被申请完，没有足够的内存提供了

常见的手段是将一个变量置为null，该变量就会被下一轮垃圾回收机制回收。 常见的内存泄露的原因：

- 全局变量引起的内存泄露
- 闭包
- 没有被清除的计时器

解决方法：

- 减少不必要的全局变量
- 严格使用闭包（因为闭包会导致内存泄露）
- 避免死循环的发生

### 执行机制

#### 1.JavaScript如何实现异步编程，可以详细描述EventLoop机制

![image.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/16/173566d89a98fbad~tplv-t2oaga2asx-watermark.awebp) 任务队列实际上有两个，一个是宏任务队列，一个是微任务队列，当主线程执行完毕，如果微任务队列中有微任务，则会先进入执行栈，当微任务队列没有任务时，才会执行宏任务的队列。

#### 2.宏任务和微任务分别有哪些

微任务包括： 原生Promise(有些实现的promise将then方法放到了宏任务中),Object.observe(已废弃), MutationObserver， MessageChannel；只有promise调用then的时候，then里面的函数才会被推入微任务中 宏任务包括：setTimeout, setInterval, setImmediate, I/O；

#### 3.使用Promise实现串行

```
function execute(tasks){    
  return tasks.reduce((previousPromise, currentPromise)=>previousPromise.then(resultList=>{        
    return new Promise(resolve=>{ 
      currentPromise().then(result=>{
        resolve(resultList.concat(result))
        }).catch(()=>{                resolve(resultList.concat(null))            })        })    },Promise.resolve([])))}const execute = (tasks = []) => {    const resultList = [];    for(task of tasks){        try{            resultList.push(await tasks())        }catch(err){            resultList.push(null);        }    }    return resultList;}
```

#### 4.Node与浏览器EventLoop的差异

**Node 10以前：** 执行完一个阶段的所有任务 执行完nextTick队列里面的内容 然后执行完微任务队列的内容 Node **11以后：** 和浏览器的行为统一了，都是每执行一个宏任务就执行完微任务队列。

### 语法和API

#### 1.理解ECMAScript和JavaScript的关系

ECMAScript和JavaScript的关系是，前者是后者的规格，后者是前者的一种实现

#### 2.setInterval需要注意的点，使用settimeout实现setInterval

```
function  mySetInterval(fn,mil){    
  function interval(){
    setTimeout(interval,mil);        
    fn();    
  }
 }
setTimeout(interval,mil)}mySetInterval(function(){console.log(1)},1000)
```

#### 3.什么是防抖和节流？有什么区别？如何实现？

**防抖**
 防抖就是n时间内函数只会执行一次，如果n时间内多次触发则会重新计算时间

```
 function debounce(fn){          
  let timeout =null;          
  return function(){ 
    clearTimeout(timeout);              
    timeout =setTimeout(function(){                  
      fn.apply(this,arguments)              
    },500)
  }
 }      
 function say(){ console.log('防抖') }      
 var myInput = document.getElementById('hello');      
 myInput.addEventListener('input',debounce(say))
```

**节流**
 n时间内只会执行一次

```
 function throttle(fn){          
  let canFlag = true;          
  return function(){              
    if(!canFlag) return;              
    canFlag = false;              
    setTimeout(()=>{  
      fn.apply(this,arguments);
      canFlag = true 
    },1000)          
   }      
  }      
 function say(){ console.log('节流',new Date()) }      
 var myInput = document.getElementById('hello');      
 myInput.addEventListener('input',throttle(say))
```

#### 4.介绍下 Set、Map、WeakSet 和 WeakMap 的区别？

**WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。**

- WeakSet 的成员只能是对象，而不能是其他类型的值
- WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中

**WeakMap结构与Map结构类似，也是用于生成键值对的集合**

- WeakMap只接受对象作为键名（null除外），不接受其他类型的值作为键名
- WeakMap的键名所指向的对象，不计入垃圾回收机制

#### 5.事件冒泡、捕获(委托)

- **事件冒泡**指在在一个对象上触发某类事件，如果此对象绑定了事件，就会触发事件，如果没有，就会向这个对象的父级对象传播，最终父级对象触发了事件。
- **事件委托**本质上是利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到目标节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件，这种方式称为**事件代理**。

`event.stopPropagation()` 或者 ie下的方法 `event.cancelBubble = true;` //阻止事件冒泡

`event.preventDefault()`// 阻止元素的默认事件。

### 6.箭头函数和普通函数的区别

1. 箭头函数没有`prototype`(原型)，所以箭头函数本身没有this
2. 箭头函数的this在定义的时候继承自外层第一个普通函数的this。
3. 如果箭头函数外层没有普通函数，严格模式和非严格模式下它的this都会指向`window`(全局对象)
4. 箭头函数本身的this指向不能改变，但可以修改它要继承的对象的this。
5. 箭头函数的this指向全局，使用arguments会报未声明的错误。
6. 箭头函数的this指向普通函数时,它的`argumens`继承于该普通函数
7. 使用`new`调用箭头函数会报错，因为箭头函数没有`constructor`
8. 箭头函数不支持`new.target`
9. 箭头函数不支持重命名函数参数,普通函数的函数参数支持重命名
10. 箭头函数相对于普通函数语法更简洁优雅

**箭头函数的注意事项**：

1. 箭头函数一条语句返回对象字面量，需要加括号
2. 箭头函数在参数和箭头之间不能换行
3. 箭头函数的解析顺序相对`||`靠前

**不适用场景**：箭头函数的this意外指向和代码的可读性。
