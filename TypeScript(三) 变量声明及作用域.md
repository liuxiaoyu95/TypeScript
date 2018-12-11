# [Typescript学习笔记（三）变量声明及作用域](https://www.cnblogs.com/dh-dh/p/5202824.html)

ts的变量声明有var，let和const，这尼玛完全跟es6一样嘛。就稍微介绍一下。

大多数js开发者对于var很熟悉了，原生js里没有块级作用域，只有函数作用域和全局作用域，还存在var的变量提升的问题，导致一些不熟悉js的开发者会发现一些怪异事件。[点击查看关于es6的let与const](http://es6.ruanyifeng.com/#docs/let)

let声明

let声明和var一样的写法，大家看前面的博客里面相信也看到了。

拣重要的说，let和var的区别就是let使js实现了它的块级作用域，即*词法作用域*或*块作用域。*

而且它不存在变量提升。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function f(input: boolean) {
    let a = 100;

    if (input) {
        // Still okay to reference 'a'
        let b = a + 1;
        return b;
    }

    // Error: 'b' doesn't exist here
    return b;
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

内部块一样能访问外部块，反之就不行了。这一特性使for循环的计数器就比较好用了，

```
for(let i = 0; i < arr.length; i++){}

console.log(i)
//ReferenceError: i is not defined
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
// 报错
function () {
  let a = 10;
  var a = 1;
}

// 报错
function () {
  let a = 10;
  let a = 1;
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 let是不允许重复声明的。

需要注意的是，现在的ts编译器都是吧let直接编译为var，所以let的特性在浏览器支持之前还是无法试用的。

在js里面输出10个10的例子，在let下就不一样了。

```
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
```

不仅是在循环里引入了一个新的变量环境，而是针对*每次迭代*都会创建这样一个新作用域。 这就是我们在使用立即执行的函数表达式时做的事。

他会和我们预想的一样输出1-10。

const

const和let基本一致，只是const声明的变量被赋值后不能再改变（所以对于const来说，只声明不赋值，就会报错），作用域同let。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};

// all "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
const a = [];
a.push("Hello"); // 可执行
a.length = 0;    // 可执行
a = ["Dave"];    // 报错
```

 

这个数组和对象本身是可写的，但是如果将另一个数组赋值给const变量，就会报错。

使用的法则：

所有变量除了你计划去修改的都应该使用`const`



原文链接:https://www.cnblogs.com/dh-dh/p/5202824.html