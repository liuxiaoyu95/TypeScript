# [Typescript学习笔记（一）基础类型](https://www.cnblogs.com/dh-dh/p/5202000.html)

为了面向ng2和前端未来，开始搞向ts，ts是微软出的一枚语言，作为es6的超集，他出的一些特性还是蛮好用的(略坑)。对于我等纯前端(从开始就接触javascript)的人来说，真想说，这特么什么鬼。部分特性同es6（个人对es6还是蛮有好感的）。ts同期的coffeescript，将js python化，ts也把js搞的向其他语言靠拢。。

[中文学习](https://zhongsp.gitbooks.io/typescript-handbook)，[这里是英文学习](http://www.typescriptlang.org/Handbook)。如果不想看这些东西，那看我笔记学吧。哈哈。

ts的基础类型，布尔值，数字，字符串，数组，元组（Tuple），枚举，任意值，空值。后面几个你不知道的就是新加的类型。看他们怎么定义。

ts定义有Interface，熟悉其他语言可能知道，这为类型的安全。

#### 布尔值

定义为

```
let isDone: boolean = false;
```

#### 数字，

需要注意的是它还支持了es6的2进制和8进制。

```
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

#### 字符串，

```
let name: string = "bob";
name = "smith";
```

还支持es6的模版定义的形式（不懂es6的看阮一峰的es6入门）

```
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;
```

#### 数组,

可以

```
let list: number[] = [1, 2, 3];
```

也可以

```
let list: Array<number> = [1, 2, 3];
```

两种方式。

其实看一下前面这些js原有的类型，定义的时候只是多了一个：类型，如果变量赋值的时候类型不对，编译器就会报错。类型安全orz。（写惯js的我看着不习惯）。编译后的js里面还是原来的类型定义。

 

### 下面看新类型

#### 元组：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 这个元组就是约定类型的已知数量的数组。x[0]是string类型，x[1]是number类型，只要不是编译器就报错。

```
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

 

这里用x[1]是数字类型，没有substr，就报错了。

```
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型

console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString

x[6] = true; // Error, 布尔不是(string | number)类型
```

 

当越界访问的时候，这个东西可以是string | number，别的都报错。如上。

#### 枚举

`enum`类型是对JavaScript标准数据类型的一个补充。

```
enum Color {Red, Green, Blue};
let c: Color = Color.Green;
```

 

先看一下编译器编译的js的Color是什么

![img](https://images2015.cnblogs.com/blog/842714/201602/842714-20160219185959441-723538291.png)

这里定义的c是Color类型，赋值为Color.Green，看上面的图，就知道是1。当你取Color[1]的时候也就是Green。

```
enum Color {Red = 1, Green, Blue};
let c: Color = Color.Green;
```

```
enum Color {Red = 1, Green = 2, Blue = 4};
let c: Color = Color.Green;
```

 

这样都可以。那么对应的序号也会相应的改变。

#### 任意值(any)

当你不知道一个变量是啥类型，这个类型检测就没必要了吧。

```
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

 

那定义这个any就会跳过编译器的检测。

你可能认为`Object`有相似的作用，就像它在其它语言中那样。 但是`Object`类型的变量只是允许你给它赋任意值 -- 但是却不能够在它上面调用任意的方法，即便它真的有这些方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

定义数组，数组里面类型不同的时候也该用any

```
let list: any[] = [1, true, "free"];

list[1] = 100;
```

空值

它表示没有任何类型，就像其他语言里的void，来表示这个函数不返回值。

```
function warnUser(): void {
    alert("This is my warning message");
}
```

 

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```
let unusable: void = undefined;
```

 

### 类型断言

 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用

一种是

```
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

 

还有一种写法是

```
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

 

两种形式是等价的。当然在jsx里面用第一种就不行了。



原文链接:https://www.cnblogs.com/dh-dh/p/5202000.html