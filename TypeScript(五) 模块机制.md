# [Typescript学习笔记（五） 模块机制](https://www.cnblogs.com/dh-dh/p/5219629.html)

javascript从es5之前都缺少一种模块机制，无法通过js引入文件，于是requirejs等等的加载器应运而生。这些加载器的使用也并不统一，产生了amd，commonjs，umd等等的规范，各有所长，直到es6的发布，js自身引入的模块机制，将会在以后逐渐被应用起来。

Typescrit的模块机制与es6的模块基本类似，也提供了转换为amd，es6，umd，commonjs，system的转换，一会我们通过一个简单的例子来运行一下ts的模块。

ts特殊的内联标签。

```
/// <reference path="revence.ts" /> 
```

三个斜线，reference标签，path指向引入的文件，引入的文件呢，需要定义一个命名空间来引用定义的东西。

revence.ts

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 namespace Validation {
    const ac = "abc";
    export  function a(){
        console.log("module be loaded");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

定义一个名为Validation的命名空间，导出用export导出。这里Validation下的a函数被导出，引用的时候就可以用Validation.a()来引用它。

text.ts

```
/// <reference path="revence.ts" /> 
Validation.a();
```

main文件是这个text.ts,引用了revence文件。编译器怎么编译它呢？

两种方法：一，把所有的输入文件编译为一个输出文件，需要使用`--outFile`标记：

```
tsc text.ts --outFile a.js  
```

这样输出文件都会在a.js中，网页script引入了。

　　　　二，我们可以编译每一个文件（默认方式），那么每个源文件都会对应生成一个JavaScript文件。 然后，在页面上通过``标签把所有生成的JavaScript文件按正确的顺序引进来，比如：

```
tsc --outFile sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

```
　　 <script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
```

接下来就是ts的模块机制，而非script加载机制了。

关键词：import，export。同es6。

通过export关键词导出，包括`interface的任何东西都会被导出。比如`

```
export const numberRegexp = /^[0-9]+$/;
```

```
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

```
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}
```

不用关心导出内容是什么，反正通过export已把这些东西都导出了。

1。重新导出。as相当于重命名，from之后的是导入的地址，这相当于从./ZipCodeValidator中把ZipCodeValidator导入，重命名为RegExpBasedZipCodeValidator再导出。记住用法即可。

```
// 导出原先的验证器但做了重命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
```

2。整合导出。*相当于全部导出。下面代码整合了3个文件，并全部导出。

```
export * from "./StringValidator"; // exports interface StringValidator
export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
export * from "./ZipCodeValidator";  // exports class ZipCodeValidator
```

导入关键字是import。

### import基本用法

```
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();
```

这里./ZipCodeValidator导出的东西里面有个名字叫ZipCodeValidator的，用它的名字导入。这里{xxx}={123}，就是这种形式，其实用到了es6的解构赋值。不懂的转战es6。调用用其名字直接调用就可以了。

#### 1。导入重命名 

```
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();
```

 as语法，重命名为ZCV

#### 2。导入全部

```
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```

这里导入了./ZipCodeValidator文件的所有导出，而不是上个例子仅导入ZipCodeValidator。as validator相当于给了所有导出一个名字，可以理解为所有导出的命名空间。

Tips：

尽管不推荐这么做，一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出。 使用下面的方法来导入这类模块：

```
import "./my-module.js";
```

#### 3。默认导出。default

比如

```
declare let $: JQuery;
export default $;
```

引用

```
import $ from "JQuery";

$("button.continue").html( "Next Step..." );
```

default只能导出一项，仅导出一个函数，或者一个接口，或者任何一个单项的东西，default是最佳实践。因为在import的时候不指定导入的东西默认导入default。看下面这个例子

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import validator from "./ZipCodeValidator";

let validator = new validator();
```

#### 4。`export =` 和 `import = require()`

 TypeScript模块支持`export =`语法，以配合传统的CommonJS和AMD的工作流。

`export =`语法定义一个模块的导出对象。 它可以是类，接口，命名空间，函数或枚举。

若要导入一个使用了`export =`的模块时，必须使用TypeScript提供的特定语法`import let = require("module")`。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip.ZipCodeValidator();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 5。编译为其他模块标准。

##### SimpleModule.ts

```
import m = require("mod");
export let t = m.something + 1;
```

##### AMD / RequireJS SimpleModule.js

```
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
    exports.t = mod_1.something + 1;
});
```

##### CommonJS / Node SimpleModule.js

```
let mod_1 = require("./mod");
exports.t = mod_1.something + 1;
```

##### UMD SimpleModule.js

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        let v = factory(require, exports); if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./mod"], factory);
    }
})(function (require, exports) {
    let mod_1 = require("./mod");
    exports.t = mod_1.something + 1;
});
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

##### System SimpleModule.js

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
System.register(["./mod"], function(exports_1) {
    let mod_1;
    let t;
    return {
        setters:[
            function (mod_1_1) {
                mod_1 = mod_1_1;
            }],
  execute: function() {
            exports_1("t", t = mod_1.something + 1);
        }
    }
});
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

##### Native ECMAScript 2015 modules SimpleModule.js

```
import { something } from "./mod";
export let t = something + 1;
```

命令行的命令就是加 --module或者-m加要编译的规范名称。比如编译为amd

```
tsc text.ts -m amd
```

用tsc --help可以看到-m的各项参数。

### 高级加载

typescript的按需加载。也叫动态加载。看我博客前几篇的webpack中，用的require.ensure做按需加载，感觉比较麻烦。然而ts的按需加载的简单得益于它的省略引用。即：

编译器会检测是否每个模块都会在生成的JavaScript中用到。 如果一个模块标识符只在类型注解部分使用，并且完全没有在表达式中使用时，就不会生成`require`这个模块的代码。 省略掉没有用到的引用对性能提升是很有益的，并同时提供了选择性加载模块的能力。

这种模式的核心是`import id = require("...")`语句可以让我们访问模块导出的类型。 模块加载器会被动态调用（通过`require`）

##### 示例：Node.js里的动态模块加载

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
    let validator = new ZipCodeValidator();
    if (validator.isAcceptable("...")) { /* ... */ }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

##### 示例：require.js里的动态模块加载

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
declare function require(moduleNames: string[], onLoad: (...args: any[]) => void): void;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    require(["./ZipCodeValidator"], (ZipCodeValidator: typeof Zip) => {
        let validator = new ZipCodeValidator();
        if (validator.isAcceptable("...")) { /* ... */ }
    });
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

##### 只要if条件不成立，模块是不加载的。

​	出个简单的例子：

text.ts

```
import m= require("revence");
export let t=1;;
```

revence.ts

```
export = {
    mod:1
}
```

编译为amd模块：根本没有引入刚才的模块。

```
define(["require", "exports"], function (require, exports) {
    exports.t = 1;
    ;
});
```

现在改为text.ts

```
import m= require("revence");
export let t=m.mod+1;;
```

编译出来为

```
define(["require", "exports", "revence"], function (require, exports, m) {
    exports.t = m.mod + 1;
    ;
});
```

### 模块加载的最佳实践

##### 1。尽可能地在顶层导出

用户应该更容易地使用你模块导出的内容。 嵌套层次过多会变得难以处理，因此仔细考虑一下如何组织你的代码。

##### 2。模块里避免使用命名空间

模块中使用命名空间是不必要的，在模块中导出的东西肯定不能重名，而导入时使用者肯定会为其命名或者直接使用，也不存在重名，使用命名空间是多余的。

##### 3。如果仅导出单个 `class` 或 `function`，使用 `export default。`

如刚才所说，default是比较好的实践。

##### 4。如果要导出多个对象，把它们放在顶层里导出

```
export class SomeType { /* ... */ }
export function someFunc() { /* ... */ }
```

##### 5。导入时明确地列出导入的名字

```
import { SomeType, SomeFunc } from "./MyThings";
let x = new SomeType();
let y = someFunc();
```

##### 6。导入大量模块时使用命名空间

注意是导入时。

```
export class Dog { ... }
export class Cat { ... }
export class Tree { ... }
export class Flower { ... }
```

```
import * as myLargeModule from "./MyLargeModule.ts";
let x = new myLargeModule.Dog();
```

##### 7。使用重新导出进行扩展

你可能经常需要去扩展一个模块的功能。 JS里常用的一个模式是JQuery那样去扩展原对象。 如我们之前提到的，模块不会像全局命名空间对象那样去*合并*。 推荐的方案是*不要*去改变原来的对象，而是导出一个新的实体来提供新的功能。

## 危险信号

以下均为模块结构上的危险信号。重新检查以确保你没有在对模块使用命名空间：

- 文件的顶层声明是`export namespace Foo { ... }` （删除`Foo`并把所有内容向上层移动一层）
- 文件只有一个`export class`或`export function` （考虑使用`export default`）
- 多个文件的顶层具有同样的`export namespace Foo {` （不要以为这些会合并到一个`Foo`中！）



原文链接: https://www.cnblogs.com/dh-dh/p/5219629.html