### [模块和NPN包管理工具](#top) <b id="top"></b> 	:maple_leaf:
`在Node.js中，以模块为单位划分所有的功能，并且提供了一个完整的模块加载机制，所以我们可以将应用程序划分为各个不同的部分，并且对这些部分进行很好的协同
管理。通过将各种可重用代码编写在各种模块中的方法，可以大大减少应用程序的代码量，提高应用程序的开发效率以及应用程序代码的可读性。通过模块加载机制，可
以将各种第三方模块引入到我们的应用程序中。`
```node
//加载核心模块
let http = require('http');
//加载自定义模块'
let dataBase = require('./modules/DataBase.js');
```
- [x] [`核心模块`](#moudlecenter) :maple_leaf:
- [x] [`使用exports对象访问`](#exports) :maple_leaf:
- [x] [`组织和管理模块`](#nodemodules) :maple_leaf:
- [x] [`模块对象的属性`](#moduleshuxing) :maple_leaf:

##### [核心模块](#top) <b id="moudlecenter"></b> 	:maple_leaf:
* `node 加载预定于的核心模块速度快,而且只需要使用require 函数输出模块名指定参数就可以`
* `Node.js中,你也可以自己编写或从网上下载以下几种模块文件,记载这些文件需要路径 路径一定要以` `./` `开头 也可以使用 /  Unix 指向根目录,Windows 指向磁盘
根目录`
  * `.js 的javascript脚本文件`
  * `.json的JSON文本文件`
  * `.node的异国编译后的二进制模块文件`
  
##### [使用exports对象访问](#top) <b id="exports"></b> :maple_leaf: 
`在一个模块文件中定义变量、函数或对象只在该模块内有效，当你需要从外部模块引用这些变量、函数或对象时，需要再改模块内，例如，建一个testModule.js，代
码如下：` <br/>
`testModule.js`
```node
var testVar = "Can you see me now ? ";
var funName = function(name){
  console.log('My name is' + name);
};
exports.testVar = testVar ;
exports.funName = funName ;
```
`node`
```node
var test1 = require('./testModule.js');
// 通过test1访问testModule.js模块内的testVar变量 和funName函数
console.log(test1.testVar)
test1.funName('Luckfine')
```
##### [将模块变成类](#top) :maple_leaf:
```node
var _name,_age
var name = '',age = 0;
var foo = function(name,age){
    _name = name ;
    _age = age ;
}
// 获取私有变量_name的变量只
foo.prototype.GetName = function(name){
    return _name;
};
// 设置私有变量_name的变量值
foo.prototype.SetName = function(name){
    _name = name;
}
// 获取私有变量_age的变量只
foo.prototype.GetAge = function(age){
    return _age;
};
// 设置私有变量_name的变量值
foo.prototype.SetAge = function(age){
    _age = age;
}
foo.prototype.name = name;
foo.prototype.age = age;
module.exports = foo;
```
##### [组织和管理模块](#top) <b id="nodemodules"></b> :maple_leaf: 
**`从node_modules目录中加载模块`** <br/>
`如果在require 函数中只是用文件名而不指定路径 例如:` `require('bar.js'),那么Node.js 将该文件视为node_modules目录下的一个文件,例如在一个完整路径
为 /home/ry/project/app.js 中 Node为bar.js模块使用的加载路径一次为`
  * `/home/ry/project/node_modules.js`
  * `/home/ry/node_modules.js`
  * `/home/node_modules.js`
  * `/node_modules.js`<br/>
`如此比使用路径加载模块的方式更加灵活,因为你可以移动模块的所在位置而不需要修改代码中指定的路径`

----
**`使用目录来管理模块`** <br/>
`在Node.js中m可以将目录名指定为模块名称,以便可以通过目录来管理模块,只需要为该目录指定一个入口点`
* `最简单的办法`
   * `在程序根目录新建 node_modules 文件夹`
   * `在node_modules 文件夹里面新建目录 foo文件  然后新添加一个 index.js文件`
   * `index.js里面放入js代码`
   * `然后在app.js 里面加载` `require('foo')`
   * `node 会自动去加载 node_modules 里面的foo文件夹里面的index.js 文件里面的代码`
* `更加科学的方法`
   * `在node_modules文件夹里面定义一个 package.json 文件 定义模块名称和路径`
   
   ```json
    {
      "name":"foo", 
      "main":"./lib/foo.js"
    }
   ```
   `文件目录`
   ```
   app.js  
   node_modules
     |
     --package.json    
     |
     --foo
        |
        --lib
          |
          --foo.js
   ```
   `let foo = require('foo'); 此时node 会查找package.json 然后寻杂目录路径然后 去 node_modules里面的foo文件夹里面的lib文件夹里面的foo.js加载代码`


























