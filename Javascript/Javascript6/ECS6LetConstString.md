### <a id="top"  href="#top">JavaScript6 块级作用域.字符串.正则</a>
`JS6新语法提供了let实现块级作用域,字符串也新增了许多功能,增加了箭头函数,默认参数,不定参数`

-----

- [x] <a href="#letconst">`块级作用域绑定`</a>
- [x] <a href="#stringexpend">`字符串扩展`</a>
- [x] <a href="#regularexpend">`模板字符串`</a>
- [x] <a href="#function">`字符串扩展方法`</a>
- [x] <a href="#top">`正则扩展--ES5 中记录着`</a>
----

##### 1. <a id="letconst" href="#top">块级作用域绑定 let,const</a>    
* `块级作用域 是用let 声明变量,可以把变量变成块级`
* *`let`* `可以使得变量避免变量提升,不允许重复声明,使得变量成为块级变量,隔绝在块级作用域之间,而不会混淆`
* `未声明访问会报错,而不是underfine输出`
* `其他特性和C,C++，C# Java等等一样`
```javascript
  //使用实例
  (function UseVar(){
      if(true){
          var let=10; 
          console.log(val);
      }else{
      }
      console.log(val); //这里无法访问val 会报错
  })();
```
* const `和let一样,不同的的就是一旦声明必须初始化,一旦初始化不允许改变.`
* `值类型不允许改变值,引用类型不允许改变引用`
##### 1.2 块级作用域多循环的影响
* `为了打出0~10 js5要做那么多事情 还需要使用立即调用函数表达式`
```javascript

var func= [];
for(var i=0;i<=10;i++){
    func.push(function(){
        console.log(i);
    });
}

func.forEach(function(func_){
    func_();
});

// 10 10 11 11 11 11 11 11 11 11 11 11 11

var funcs= [];

for(var i=0;i<=10;i++){
    funcs.push(
    
    (function(value){
       return function(){
           console.log(value);
       };
    })(i)
    
    );
}

funcs.forEach(function(funcs_){
    funcs_();
});
// 打印出 0~10
```
* `JS6 Let对循环函数的影响 `
```javascript
  var func= []; //把i声明为let 就行
  for(let i=0;i<=10;i++){
      func.push(function(){
          console.log(i);
      });
  }

  func.forEach(function(func_){
      func_();
  });
```
##### 1.3 全局作用域绑定
* `全局作用域不会被绑定到window的全局属性中,所以不会产生覆盖window的属性的情况,`

##### 1.4 顶层对象的属性
`let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。`
```node
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

##### 1.5 globalThis 对象
`JavaScript 语言存在一个顶层对象，它提供全局环境（即全局作用域），所有代码都是在这个环境中运行。但是，顶层对象在各种实现里面是不统一的。`

* `浏览器里面，顶层对象是window，但 Node 和 Web Worker 没有window。`
* `浏览器和 Web Worker 里面，self也指向顶层对象，但是 Node 没有self。`
* `Node 里面，顶层对象是global，但其他环境都不支持。`

`同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用this变量，但是有局限性。`

* `1.全局环境中，this会返回顶层对象。但是，Node 模块和 ES6 模块中，this返回的是当前模块。`
* `2.函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined。`
* `3.不管是严格模式，还是普通模式，new Function('return this')()，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么eval、new Function这些方法都可能无法使用。`

`综上所述，很难找到一种方法，可以在所有情况下，都取到顶层对象。`

`ES2020 在语言标准的层面，引入globalThis作为顶层对象。也就是说，任何环境下，globalThis都是存在的，都可以从它拿到顶层对象，指向全局环境下的this。
垫片库global-this模拟了这个提案，可以在所有环境拿到globalThis。`


##### 2. <a id="stringexpend" href="#top">字符串和正则表达式 代理对,normalize,,=></a>    
`ES6 加强了对 Unicode 的支持，允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点。`

`JS5的字符串是基于16位的UTF-16编码进行构建的,每十六位表示一个编程单元,代表一个字符,许多字符串方法和属性都是基于此编程单元的的,但是过去16足够了但是
Unicode引入扩展字符集,编码规则不得不进行变更,所有不再限制在16位,扩展到了32位,一个字符对应一个码位例如 55362 表示 吉这个字符.UTF-16中,前面`2<sup>16</sup>`码位 均以16位的编码单位表示,这个范围被称为基于多文中平面(BMP)`<br/>
`超过这个范围的码位则要归属于某个辅助平面,其中码位仅用16位就无法表示了,为此UTF-16引入` **`代理对`** `其规定用两个16位编码单位表示一个码位,也就是说,字符串里的字符有两种,一种是16位编码单位的BMP字符,一种是两个编码单位的32位字符串,为此产生了很多的问题`
* `1.采用了代理对的一个字符串长度为2,例如` `let val="𠮷"; console.log(val.length);` `结果长度为2`

* `2.不利于对字符串进行排序和比较,要使用normalize() 方法进行等效关系标准化.`

##### 针对代理对编码提供了码点方法
* `codePointAt() 方法,  能够正确处理 4(32位) 个字节储存的字符，返回一个字符的码点。` `charCodeAt() 方法只能返回16位的码点,无法兼容到代理对`
* `ES5 提供String.fromCharCode方法，用于从码点返回对应字符，但是这个方法不能识别 32 位的 UTF-16 字符（Unicode 编号大于0xFFFF）。ES6 提供了String.fromCodePoint方法，可以识别大于0xFFFF的字符，弥补了String.fromCharCode方法的不足。在作用上，正好与codePointAt方法相反。`

```javascript
    let g="𠮷";
    console.log("[𠮷] 长度为:"+g.length);    
    console.log(g.charCodeAt(0));
    console.log(g.codePointAt(0));//Unicode 代理对
    console.log(String.fromCodePoint(134071)); 
    console.log(String.fromCharCode(134071)); 
    //输出
    /*
      [𠮷] 长度为:2
      55362
      134071
      𠮷
      ஷ    
    */
```

```javascript
   //判断是否是代理对
   function is32Bit(code){
       return code.codePointAt(0)>0XFFFF;
   }
```

-----
**`注意`**:`fromCodePoint方法定义在String对象上，而codePointAt方法定义在字符串的实例对象上。`
##### 标准化 normalize
`许多欧洲语言有语调符号和重音符号。为了表示它们，Unicode 提供了两种方法。一种是直接提供带重音符号的字符，比如Ǒ（\u01D1）。另一种是提供合成符号（combining character），即原字符与重音符号的合成，两个字符合成一个字符，比如O（\u004F）和ˇ（\u030C）合成Ǒ（\u004F\u030C）。这两种表示方法，在视觉和语义上都等价，但是 JavaScript 不能识别。JavaScript 将合成字符视为两个字符，导致两种表示方法不相等。`

-----
* `ES6 提供字符串实例的normalize()方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。`

* `normalize方法可以接受一个参数来指定normalize的方式，参数的四个可选值如下。`
   * `NFC，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。`
  * `NFD，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。`
  * `NFKC，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，normalize方法不能识别中文。）`
  * `NFKD，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。`
* `normalize方法目前不能识别三个或三个以上字符的合成，默认 NFD 形式`
```C#
'\u01D1'==='\u004F\u030C' //false

'\u01D1'.length // 1
'\u004F\u030C'.length // 2

 console.log('\u01D1'.normalize() === '\u004F\u030C'.normalize());
// true
'\u004F\u030C'.normalize('NFC').length // 1
'\u004F\u030C'.normalize('NFD').length // 2
// 上面代码表示，NFC参数返回字符的合成形式，NFD参数返回字符的分解形式。
```
```javascript
let values=["𠮷","天","下","王","者","J","i","1","5","q"];
//默认排序
console.log(values.sort());

//标准化后 第一种排序加标准化
let normalized=values.map(function(text){
    return text.normalize();
})
console.log(normalized.sort(function(one,two){
           if(one <two){
               return 1;
           }else if(one == two){
               return 0;
           }else{
               return -1;
           }
    })
);
// 第二种排序加标准化
console.log(values.sort(function(first,second){
    let firstNormalized=first.normalize();
    let secondNormalized=second.normalize();
    if(firstNormalized <secondNormalized){
        return 1;
    }else if(firstNormalized == secondNormalized){
        return 0;
    }else{
        return -1;
    }
}));
```
##### 字符串遍历器
`for...of循环遍历，除了遍历字符串，这个遍历器最大的优点是可以识别大于0xFFFF的码点，传统的for循环无法识别这样的码点。`
```C#
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```
`除了遍历字符串，这个遍历器最大的优点是可以识别大于0xFFFF的码点，传统的for循环无法识别这样的码点。`

```node
let text = String.fromCodePoint(0x20BB7);

for (let i = 0; i < text.length; i++) {
  console.log(text[i]);
}
// " "
// " "

for (let i of text) {
  console.log(i);
}
// "𠮷
```

##### 直接输入
`JavaScript 字符串允许直接输入字符，以及输入字符的转义形式。举例来说，“中”的 Unicode 码点是 U+4e2d，你可以直接在字符串里面输入这个汉字，也可以输入它的转义形式\u4e2d，两者是等价的。`

```javascript
'中' === '\u4e2d' // true
```
`JavaScript 规定有5个字符，不能在字符串里面直接使用，只能使用转义形式。`

* `U+005C`：`反斜杠（reverse solidus)`
* `U+000D`：`回车（carriage return）`
* `U+2028`：`行分隔符（line separator）`
* `U+2029`：`段分隔符（paragraph separator）`
* `U+000A`：`换行符（line feed）`

`举例来说，字符串里面不能直接包含反斜杠，一定要转义写成\\或者\u005c。`

`这个规定本身没有问题，麻烦在于 JSON 格式允许字符串里面直接使用 U+2028（行分隔符）和 U+2029（段分隔符）。这样一来，服务器输出的 JSON 被JSON.parse解析，就有可能直接报错。`

```javascript
const json = '"\u2028"';
JSON.parse(json); // 可能报错
```
`JSON 格式已经冻结（RFC 7159），没法修改了。为了消除这个报错，ES2019 允许 JavaScript 字符串直接输入 U+2028（行分隔符）和 U+2029（段分隔符）。`

```node
const PS = eval("'\u2029'");
```

##### JSON.stringify() 的改造
`根据标准，JSON 数据必须是 UTF-8 编码。但是，现在的JSON.stringify()方法有可能返回不符合 UTF-8 标准的字符串。`

`具体来说，UTF-8 标准规定，0xD800到0xDFFF之间的码点，不能单独使用，必须配对使用。比如，\uD834\uDF06是两个码点，但是必须放在一起配对使用，代表字符𝌆。这是为了表示码点大于0xFFFF的字符的一种变通方法。单独使用\uD834和\uDFO6这两个码点是不合法的，或者颠倒顺序也不行，因为\uDF06\uD834并没有对应的字符。`

`JSON.stringify()的问题在于，它可能返回0xD800到0xDFFF之间的单个码点。`
```node
JSON.stringify('\u{D834}') // "\u{D834}"
```

`为了确保返回的是合法的 UTF-8 字符，ES2019 改变了JSON.stringify()的行为。如果遇到0xD800到0xDFFF之间的单个码点，或者不存在的配对形式，它会返回转义字符串，留给应用自己决定下一步的处理。`

```node
JSON.stringify('\u{D834}') // ""\\uD834""
JSON.stringify('\uDF06\uD834') // ""\\udf06\\ud834""
```

##### <a href="#top" id="regularexpend">模板字符串</a>

`传统的 JavaScript 语言，输出模板通常是这样写的（下面使用了 jQuery 的方法）。`
```javascript
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);
```
`上面这种写法相当繁琐不方便，ES6 引入了模板字符串解决这个问题。`

```node
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```
`模板字符串（template string）是增强版的字符串，用反引号`（&#180;）`标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。`
```
// 普通字符串
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

console.log(`string text line 1
string text line 2`);

// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```
`上面代码中的模板字符串，都是用反引号表示。如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。`

```node
let greeting = `\`Yo\` World!`;
```
`如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。`

```javascript
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`);
```
`上面代码中，所有模板字符串的空格和换行，都是被保留的，比如<ul>标签前面会有一个换行。如果你不想要这个换行，可以使用trim方法消除它。`

```node
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`.trim());
```
`模板字符串中嵌入变量，需要将变量名写在${}之中。`
```node
function authorize(user, action) {
  if (!user.hasPrivilege(action)) {
    throw new Error(
      // 传统写法为
      // 'User '
      // + user.name
      // + ' is not authorized to do '
      // + action
      // + '.'
      `User ${user.name} is not authorized to do ${action}.`);
  }
}
```
`大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。`

```node
let x = 1;
let y = 2;

`${x} + ${y} = ${x + y}`
// "1 + 2 = 3"

`${x} + ${y * 2} = ${x + y * 2}`
// "1 + 4 = 5"

let obj = {x: 1, y: 2};
`${obj.x + obj.y}`
// "3"
```
`模板字符串之中还能调用函数。`

```node
function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar
```
`如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的toString方法。`

`如果模板字符串中的变量没有声明，将报错。`
```node
// 变量place没有声明
let msg = `Hello, ${place}`;
// 报错
```
`由于模板字符串的大括号内部，就是执行 JavaScript 代码，因此如果大括号内部是一个字符串，将会原样输出。`
```node
`Hello ${'World'}`
// "Hello World"
```
`模板字符串甚至还能嵌套。`

```node
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;
```
`上面代码中，模板字符串的变量之中，又嵌入了另一个模板字符串，使用方法如下。`

```node
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];

console.log(tmpl(data));
// <table>
//
//   <tr><td><Jane></td></tr>
//   <tr><td>Bond</td></tr>
//
//   <tr><td>Lars</td></tr>
//   <tr><td><Croft></td></tr>
//
// </table>
```
`如果需要引用模板字符串本身，在需要时执行，可以写成函数。`

```node
let func = (name) => `Hello ${name}!`;
func('Jack') // "Hello Jack!"
```
`上面代码中，模板字符串写成了一个函数的返回值。执行这个函数，就相当于执行这个模板字符串了。`

###### [模板实例](http://es6.ruanyifeng.com/#docs/string#%E5%AE%9E%E4%BE%8B%EF%BC%9A%E6%A8%A1%E6%9D%BF%E7%BC%96%E8%AF%91)

###### 标签模板
`模板字符串的功能，不仅仅是上面这些。它可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。
这被称为“标签模板”功能（tagged template）。`
```node
alert`123`
// 等同于
alert(123)

let a = 5;
let b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```
* `函数tag依次会接收到多个参数。`
```node
function tag(stringArr, ...values){
  // ...
}
```
```node
let a = 5;
let b = 10;

function tag(s, v1, v2) {
  console.log(s[0]);
  console.log(s[1]);
  console.log(s[2]);
  console.log(v1);
  console.log(v2);

  return "OK";
}

tag`Hello ${ a + b } world ${ a * b}`; //=tag(['Hello ', ' world ', ''], 15, 50)
// "Hello "
// " world "
// ""
// 15
// 50
// "OK"
```
`末班实例`
```node
let total = 30;
let msg = passthru`The total is ${total} (${total*1.05} with tax)`;

function passthru(literals) {
  let result = '';
  let i = 0;

  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i];
    }
  }

  return result;
}
```


##### <a href="#top" id="function" >字符串扩展方法</a>

##### String.fromCodePoint()
`ES5 提供String.fromCharCode()方法，用于从 Unicode 码点返回对应字符，但是这个方法不能识别码点大于0xFFFF的字符。`

```node
String.fromCharCode(0x20BB7)
// "ஷ"
```
`上面代码中，String.fromCharCode()不能识别大于0xFFFF的码点，所以0x20BB7就发生了溢出，最高位2被舍弃了，最后返回码点U+0BB7对应的字符，而不是码点U+20BB7对应的字符。`

`ES6 提供了String.fromCodePoint()方法，可以识别大于0xFFFF的字符，弥补了String.fromCharCode()方法的不足。在作用上，正好与下面的codePointAt()方法相反。`

```node
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
```
`上面代码中，如果String.fromCodePoint方法有多个参数，则它们会被合并成一个字符串返回。`

`注意，fromCodePoint方法定义在String对象上，而codePointAt方法定义在字符串的实例对象上。`

##### codePointAt() 
`JavaScript 内部，字符以 UTF-16 的格式储存，每个字符固定为2个字节。对于那些需要4个字节储存的字符（Unicode 码点大于0xFFFF的字符），JavaScript 会认为它们是两个字符。`

```node
var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1) // ''
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271
```
`面代码中，汉字“𠮷”（注意，这个字不是“吉祥”的“吉”）的码点是0x20BB7，UTF-16 编码为0xD842 0xDFB7（十进制为55362 57271），需要4个字节储存。对于这种4个字节的字符，JavaScript 不能正确处理，字符串长度会误判为2，而且charAt()方法无法读取整个字符，charCodeAt()方法只能分别返回前两个字节和后两个字节的值。`

`ES6 提供了codePointAt()方法，能够正确处理 4 个字节储存的字符，返回一个字符的码点。`

```node
let s = '𠮷a';

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271

s.codePointAt(2) // 97
```

```node
let arr = [...'𠮷a']; // arr.length === 2
arr.forEach(
  ch => console.log(ch.codePointAt(0).toString(16))
);
// 20bb7
// 61
```

##### 其他方法
* `str.includes(string [,indexStart])` ：`返回布尔值，表示是否找到了参数字符串。`
* `str.startsWith(string [,indexStart])` ：`返回布尔值，表示参数字符串是否在原字符串的头部。`
* `str.endsWith(string [,indexStart])` ：`返回布尔值，表示参数字符串是否在原字符串的尾部。`
* `str.repeat(number)` :`方法返回一个新字符串，表示将原字符串重复n次。`
* `str.padStart(time,string)` : `用于头部补全`
* `str.padEnd(time,string)` : `用于尾部补全`
* `str.matchAll(pattern)`:`方法返回一个正则表达式在当前字符串的所有匹配`
```javascript
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```
* `这三个方法都支持第二个参数，表示开始搜索的位置。`

```javascript
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```
* `repeat`
```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```
* `padStart`
```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

##### [String.raw()](#top)
`ES6 还为原生的 String 对象，提供了一个raw方法。往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。`
```node
String.raw`Hi\n${2+3}!`;
// 返回 "Hi\\n5!"

String.raw`Hi\u000A!`;
// 返回 "Hi\\u000A!"

String.raw`Hi\\n`
// 返回 "Hi\\\\n"
```
`作为函数，String.raw的代码实现基本如下。`

```node
String.raw({ raw: 'test' }, 0, 1, 2);
// 't0e1s2t'

// 等同于
String.raw({ raw: ['t','e','s','t'] }, 0, 1, 2);

String.raw = function (strings, ...values) {
  let output = '';
  let index;
  for (index = 0; index < values.length; index++) {
    output += strings.raw[index] + values[index];
  }

  output += strings.raw[index]
  return output;
}
```









