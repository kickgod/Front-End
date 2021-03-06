### [JavaScript 面向对象](#top) <b id="top"></b> 

----
:white_check_mark: `JavaScript5 没有类 但是有对象，JS对象是一个非常有趣的东西 它就是无序属性的集合 属性可以为基本值 对象 和函数`

- [x] [`Object 属性类型/特性`](#object)
   * `数据属性`
       * `特性` `Object.defineProperty`
   * `访问器属性`
       * `特性` `Object.defineProperties`
- [x] [`创建对象`](#create)
- [x] [`基本定理`](#basic)
  * `工厂模式`
  * `构造函数` ` * 指定这种 其他理解` 
  * `原型模式`
  * `寄生构造函数模式`
  * `稳妥构造函数`
      

##### [Object 属性类型/特性](#top)  <b id="object"></b>
`ECMA 在第五版定义了只有内部采用的特性 描述了属性的各种特性,ECM-262 定义这些特性是否了实现JavaScript 是为了实现JavaScripty殷勤用的,因此Js代码中无法访问他们
为了表示特性是内部值 规范用双中括号表示 [[Enumerable]]`

- [x] `(1). ECMA中有两种属性:数据属性和访问器属性`

##### 数据属性
`包含一个数据值的位置，这个位置可以读取 数据属性有四个描述其行为的特性`

- [x] `(1)`. `[[Configurable]]`:`是否能够通过delete 删除属性从而重新定义属性 默认值为true defineProperties中默认值为false`
- [x] `(2)`. `[[Enumerable]]`: `表示能否通过for-in循环返回属性默认为true defineProperties中默认值为false`
- [x] `(3)`. `[[Writeable]]`:`表示能否修改属性的值 默认为 true defineProperties中默认值为false`
- [x] `(4)`. `[[Value]]`:`包含这个属性的数据值，读取属性值的时候 从这个位置读取 写入属性值的 吧新值保存在这个位置`

```javascript
var person = {
  Name:"Jxkicker" //前三个特性值为 true [[Value]] 为 "Jxkicker"
}
```
##### [Object.defineProperty(obj,propertyName,obj_desc)](#top)
`如果要修改属性默认的特性,必须使用defineProperty方法 接收三个参数 属性所在对象 属性名称 和一个描述符对象 描述符对象的属性必须是  configurable,enumerable
writeable,value 设置一个或者多个值`
* `注意`:`那么不设置的特征值是 默认值 可以多次调用 此方法修改同一个属性但是 但是在吧configurable设置 为false之后就会有错误了 `
```javascript
var person = {} ; 

Object.defineProperty(person,"name",{
    configurable:true,
    writable:false,
    value:"JxKicker"
});

person.name = "Kicker";

console.log(person.name); //JxKicker
```

##### 访问器属性
`它不包含值;他们包含一堆getter setter 函数 在读取属性值的时候调用getter属性 在设置属性值的时候 掉setter值 这个函数负责决定如何处理函数,访问属性有如下四个
特性值`

- [x] `(1)`. `[[Configurable]]`:`是否能够通过delete 删除属性从而重新定义属性 默认值为true defineProperties中默认值为false`
- [x] `(2)`. `[[Enumerable]]`: `表示能否通过for-in循环返回属性默认为true defineProperties中默认值为false`
- [x] `(3)`. `[[Get]]`:`在读取属性的时候调用的函数 默认值 undefined `
- [x] `(4)`. `[[Set]]`:`在设置属性的时候调用的函数 默认值 undefined`

```javascript
let toolBox = {};

Object.defineProperty(toolBox, "_basic_standard_precision", {
    configurable: false,
    writable:false,
    value:3,
    enumerable:false
});

Object.defineProperty(toolBox,"pricision", {
    configurable:true,
    Enumerable:true,
    get() {
        return this._basic_standard_precision;
    },
    set(v) {
        this._basic_standard_precision = v;
    }
})

console.log(toolBox.pricision);
```

##### [Object.defineProperties()](#top)
`一次性定义多个属性 如果有些描述属性没有给与值 那么默认为false`

```node
var book = {};

Object.defineProperties(book,{
    _year:{
        writable:true,
        value:2004
    },
    edition:{
        writable:true, //一定要有 不然要出错 使用 defineProperties 默认值为 false
        value:1
    },
    year:{
        configurable:false, //默认就是false
        enumerable:true,
        get: function(){
            return this._year;
        },
        set: function(newValue){
            if(newValue >= 2004  ){
                this._year = newValue;
                this.edition = newValue - 2003;
            }
            
        }
    }
});
book.year = 2008;

console.log(book.edition);
```

```node
let kingdom = {};
Object.defineProperties(kingdom, {
    name:{ //国民
        writable:true,
        value:'中华人民共和国',
        enumerable:true,
        configurable:true
    },
    long:{ //国祚
        enumerable:true,
        configurable:true,
        get() {
            return (new Date()).getFullYear() - 1949;
        }
    },
    peopleCount:{ //民族数量
        enumerable:true,
        configurable:true,
        value:56,
        writable:false
    }
});
```

##### [读取属性的特性](#top)
`js提供了 Object.getOwnPropertyDescriptor(obj,propertyName);` `第一个参数 对象` `第二个参数 对象的属性名 返回一个描述对象`
```node

let descripter =  Object.getOwnPropertyDescriptor(book,"edition");

console.log(descripter.value);  //5
console.log(descripter.enumerable); //false
console.log(descripter.configurable); //false
console.log(descripter.writable);  //true

let yeardescripter =  Object.getOwnPropertyDescriptor(book,"year");

console.log(yeardescripter.configurable); // false
console.log(yeardescripter.enumerable); // true
console.log(typeof yeardescripter.set); //function
console.log(typeof yeardescripter.get); //function
```
##### [创建对象](#top)  <b id="create"></b>
`以上创建对象的方式 太过于单一，无法提供一个创建对象的模板 还无法实现继承重用效果,怎么实现来 请让我细细道来`

##### [工厂模式](#top)
`这是最基本的模式开发人员 发明一种函数 来封装以特定接口创建对象的细节`
```node
function PersonFatory(name = "",age = 0,sex = true,job ="" ){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.sex = sex;
    o.job = job; 
    return o;
}

var person1 = PersonFatory("JxKicker",28,true,"Worker");
var person2 = PersonFatory("Ninchol",25,false,"Coder");

console.log('person1', person1);
console.log('person2:', person2);

/*
 person1 { name: 'JxKicker', age: 28, sex: true, job: 'Worker' }
 person2: { name: 'Ninchol', age: 25, sex: false, job: 'Coder' }
 */
```
* `他解决了创建多个类似对象的问题 但是没有解决对象识别的问题` `如何知道一个对象的类型`
##### [构造函数](#top)
`JS构造函数可以创建特定类型的对象`
* `构造函数没有return`
* `直接将属性和方法赋值给this对象`
* `没有显示的创建对象`
* `实例化要使用new 操作符返回的是一个新的的对象`
* `每个对象都有一个 constructor[构造函数] 属性指向Person`
```node

function Person(name = "",age = 0,sex = true,job ="" ){
    this.name = name;
    this.age = age;
    this.sex = sex;
    this.job = job; 
    saidHello = function(){
        console.warn('Hello My Name is ', this.name);
    }
}

var person1_ = new Person("JxKicker",28,true,"Worker");
var person2_ = new Person("Ninchol",25,false,"Coder");

console.log('person1_:', person1_);
console.log('person2_:', person2_);
/*
 person1_: Person { name: 'JxKicker', age: 28, sex: true, job: 'Worker' }
 person2_: Person { name: 'Ninchol', age: 25, sex: false, job: 'Coder' }
 */
 // 可以检测出来类型
 console.log('person1_ instanceof Person : ', person1_ instanceof Person); //true
```
`构造函数的问题-同一种功能的函数被反复创建`

##### [原型模式](#top)
`我们创建的每个函数都有一个prototype(原型)属性,这个属性是一个指针,指向一个对象,而这个对象的用途是包含由特定类型的所有势力共享的属性和方法 你可以理解为静态`：`最大的好处: 可以让所有对象势力共享它所包含的属性和方法 也就是说说不必再构造函数中定义方法 也可以将这些方法直接添加到原型对象中`

* `默认指向Object`
* `我们原型模式和构造函数相结构的方法 来创建对象  因为原型对象 里面包含的东西 是静态的 但是又是类似于对象属性 本事上来说 它是公有的 所有对象的公有属性方法`
```node
function Person(name = "",age = 0,sex = true,job ="" ){
    this.name = name;
    this.age = age;
    this.sex = sex;
    this.job = job; 
}

Person.prototype.SaidHello = function(){
    console.log(`${this.name} siad : Hello World!`);
}


var person1_ = new Person("JxKicker",28,true,"Worker");
var person2_ = new Person("Ninchol",25,false,"Coder");

console.log('person1_:', person1_);
console.log('person2_:', person2_);


person1_.SaidHello();
person2_.SaidHello();

```
* `只要创建一个新函数,就会根据一组特定的规则为该函数创建一个prototype 属性,该属性指向函数的原型对象,在默认的情况下,所有的原型对象都会自动获得
一个 constructor[构造函数]属性这个属性包含一个指向 prototype属性所在函数的指针`
##### Object.getPrototypeOf(obj) 得到一个对象的原型
```node
function Person(){}
Person.prototype= {
    name:"Jxkicker",
    age:29,
    job:"Software Engineer",
    sayName:function(){
        alert(this.name);
    }
}

var p1 = new Person();
let pro = Object.getPrototypeOf(p1);
console.log('pro:\n', pro);
/*
pro:
 { name: 'Jxkicker',
  age: 29,
  job: 'Software Engineer',
  sayName: [Function: sayName] }
*/
```
##### 寄生构造模式
* `用来干嘛呀 用来扩展一个现有的对象 例如 Array类型`
```node
//寄生构造函数模式 

function Person(name,age,sex){
    var o = new Object();
    o.name = name;
    o.age = age
    o.sex = sex
    o.sayName = function () {
        return this.name;
    }
    return o;
}

function NumberArray(){
    var array = new Array();
    array.getFirst = function () {
        return this.length > 0 ? this[0]:null;
    }
    return array;
}

var myArray = new NumberArray();
myArray.push(1);
myArray.push(2);
myArray.push(3);

console.log(myArray.getFirst());
```

##### 稳妥构造函数
`你会发现 除了通过哪些方法  是无法访问 name age的 真是稳妥`
```node
function Person(name,age){
    var o = new Object();
    Object.defineProperty(o,"Place",{
        enumerable:false,
        value:"SiChuan",
        writable:false
    });
    o.getName = function () {
        return name;
    }
    o.setName = function (value) {
        name = value
    }

    o.getAge = function () {
        return age;
    }
    o.setAge = function (value) {
        age = value
    }
    return o;
}

var friend = Person("JxKicker",18)

console.log('name:', friend.getName() );
```



##### [属性访问属性](#top)  
`当访问一个属性的,它是如何查找值的呢？ 这和静态语言不同 且等我嘻嘻说来`
* `每当读取某个对象的某个属性的时候,都会执行一次搜索,目标是具有给定名称的属性`
* `1.首先搜索对象实例本身的属性 找到 返回值 找不到 搜索指针指向的原型对象`
* `2.在原型对象中搜素属性 找到返回 找不到 则继续向上查找 原型的原型`
* `所以 如果原型对象属性 和实例对象的属性 同名的时候 会发生覆盖 并且实例属性的优先级高`
##### [基本定理](#top)   <b id="basic"></b>
* `Function是最顶层的构造方法，所有对象都由Function方法构造，包括Object方法，Function方法（有些人把这个认为是Function方法的自生性，不用纠结，不管是什么原因，只要记住Function是方法也是对象就行）`
* `所有的方法（就是克隆类）都继承Function.prototype对象（包括Function方法 通过定理1可得）`
* `不要纠结是先有Object对象，还是先有Function方法，就跟先有鸡还是现有蛋一样，相互依赖，同时诞生`
* `每个对象的_proto_属性（注意不是prototype属性）都指向自己的继承父类，也就是他的克隆类的原型对象，也就是Foo.prototype对象（object对象）`


