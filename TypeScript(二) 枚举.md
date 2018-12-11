# [Typescript学习笔记（二）枚举](https://www.cnblogs.com/dh-dh/p/5202780.html)

 

跟随handbook的脚步，详细介绍一下枚举。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
enum Direction {
```

```
    Up = 1,
    Down,
    Left,
    Right
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

一个枚举类型可以包含零个或多个枚举成员,每个枚举成员可以是一个常数，也可以是计算变量。

如果没有初始化就会被当作常数，这是第一项为0，其余为前一项加一。

如果项具有ts表达式，在编译的时候就会被计算出来。比如

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write
    // computed member
    G = "123".length
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

当然，反向映射是木有问题的。

```
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[Enum.A]; // "A"
```

 

它被编译成：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
var Enum;
(function (Enum) {
    Enum[Enum["A"] = 0] = "A";
})(Enum || (Enum = {}));
var a = Enum.A;
var nameOfA = Enum[Enum.A]; // "A"
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### 常数枚举

常数枚举是在`enum`关键字前使用`const`修饰符

```
const enum Enum {
    A = 1,
    B = A * 2
}
```

 

你编译出来会看到js什么也没有，是的，它是在编译阶段会被删除，而且它和名字一样只能使用常数表达式。（常数枚举成员在使用的地方被内联进来。 这是因为常数枚举不可能有计算成员）。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
const enum Enum {
    A = 1*8,
    B = A * 2
}
var re=Enum.A;
console.log( re);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

你会看到编译出来是

```
var re = 8 /* A */;
console.log(re);
```

 

#### 外部枚举

外部枚举就像这样，用declare关键字定义

```
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```

 

引用中文book的话说：外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
declare enum Enum {
    A = 1,
    B,
    C = 2
}
var re=Enum.A;
 console.log( re); 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

如上的外部枚举，编译出来为

```
var re = Enum.A;
console.log(re);
```



原文链接:https://www.cnblogs.com/dh-dh/p/5202780.html