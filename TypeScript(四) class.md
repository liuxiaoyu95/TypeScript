# [Typescript学习笔记（四）class 类](https://www.cnblogs.com/dh-dh/p/5210241.html)

typescript的类，与c#，java等语言的类类似。也是包含了一大部分的es6的实现。我会用最通俗的语言讲一下对coding有用的地方。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

这是一个简单的类的定义。 Greeter为类名，这个类里面有greeting属性和greet方法。constructor方法，熟悉es6的同学应该清楚，constructor是这个类的构造函数，即每次实例化这个类的时候都会执行一下这个构造函数。注意ts对于es6的提升，在greeting属性定义的时候进行类型检测。后面实例化类与js相同。

### class的继承:

和大多数语言一样，class的继承用extends关键字。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}
class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

可以看到`Horse`和`Snake`类是基类`Animal`的子类，并且可以访问其属性和方法。在这里，子类重写了父类的move方法，这是继承的多样性。对于没有其他语言基础的人来说，值得一提的是，

super，super是代指父类。super（）执行一遍父类的构造函数，而super.move则是调用父类的move方法。

接下来，讲一下类中的一些修饰字，比如public，private，protected，static ，abstract，大体就这些了，这些东西什么用呢？

先写出来：public，定义类的公有成员，就是说我们可以自由访问这些成员，如果不写，默认就是public。

　　　　private，定义类的私有成员，就是说不能在声明它的类的外部访问它，当然在实例里也不行。

　　　　 protected，定义类的保护成员，与楼上的区别就是他在实例里可以访问。

　　　 　static，定义类的静态属性。存在于类本身上面而不是类的实例上，所以访问的时候要加该类名。

　　　 abstract,定义抽象类，它们不会被实例化，仅提供继承

#### 1， public，公有成员，默认都是这个。

#### 2，private 

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // Error: 'name' is private;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 

 

当实例调用他的属性就报错。

这里有一点就是当我们比较两种不同的类型时，并不在乎它们从哪儿来的，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。

然而，当我们比较带有`private`或`protected`成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个`private`成员，那么只有当另外一个类型中也存在这样一个`private`成员， 并且它们是来自同一处声明时，我们才认为这两个类型是兼容的。

protected也一样。

show个例子。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // Error: Animal and Employee are not compatible
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 因为animal和employee不同源，所以不能相互赋值，虽然他们都长得一样。Animal的子类的实例就可以赋值了，因为他们同源，也存在name属性。

（ 因为`Animal`和`Rhino`共享了来自`Animal`里的私有成员定义`private name: string`，因此它们是兼容的。 然而`Employee`却不是这样。当把`Employee`赋值给`Animal`的时候，得到一个错误，说它们的类型不兼容。 尽管`Employee`里也有一个私有成员`name`，但它明显不是`Animal`里面定义的那个）

#### 3.protected

改一下上面的例子

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Animal {
    protected name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
    getName():string {
        return this.name;
    }
}
let animal = new Rhino( );
console.log(animal.getName());
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

这样可以取到animal的name属性，但是你改成private就不行了，因为他不允许子类使用的。这就是protected与private的区别。

#### 4.static

这是类的静态属性，这些属性存在于类本身上面而不是类的实例上。这就明确了，调用的通过类名。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 Grid.origin这样调用。

#### 5.abstract

抽象类，仅用来继承，就像sass里面的占位符选择器%，继承才用到，下面的%ir

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
%ir{
  color: transparent;
  text-shadow: none;
  background-color: transparent;
  border: 0;
}
#header{
  h1{
    @extend %ir;
    width:300px;
  }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

不同于接口，抽象类可以包含成员的实现细节。 `abstract`关键字是用于定义抽象类和在抽象类内部定义抽象方法。

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。抽象方法必须使用`abstract`关键字并且可以包含访问符。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // constructors in derived classes must call super()
    }
   printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: method doesn't exist on declared abstract type
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 AccountingDepartment 类继承Department抽象类，printName抽象方法，在子类中重定义了，generateReports方法在抽象类中不存在，被拒绝了。当然，创建抽象类的子类也被拒绝了。

参数属性：

不知道上面那个例子的constructor看懂没，其实这个

```
  constructor(public name: string) { }
```

就是

```
public name: string
constructor(name: string) { this.name=name;}
```

的简写。知道就好。 

存取器：

类的存取器，跟es6一样，其实就是个内置的属性拦截器，或者叫装饰器。上一下例子就会了。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

在属性面前加get是获取的时候的拦截，加set是设置的时候拦截。 alert(employee.fullName)就是获取了，就会触发get后面的那个函数。employee.fullName = "Bob Smith";赋值就触发了set，触发后面的函数。这个例子是在设置fullName的时候先验证密码，错误就不允许设置。

注意：若要使用存取器，要求设置编译器输出目标为ECMAScript 5或更高。

### 高级技巧：

构造函数：当你在TypeScript里定义类的时候，实际上同时定义了很多东西。 首先是类的*实例*的类型。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

我们写了`let greeter: Greeter`，意思是`Greeter`类实例的类型是`Greeter。`

 我们也创建了一个叫做*构造函数*的值。 这个函数会在我们使用`new`创建类实例的时候被调用。 下面我们来看看，上面的代码被编译成JavaScript后是什么样子的：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
let Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

let greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

上面的代码里，`let Greeter`将被赋值为构造函数。

当我们使用`new`并执行这个函数后，便会得到一个类的实例。 这个构造函数也包含了类的所有静态属性。

换个角度说，我们可以认为类具有实例部分与静态部分这两个部分。

改一下例子

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet());

let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
let greeter2:Greeter = new greeterMaker();
console.log(greeter2.greet());
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

这个例子里，`greeter1`与之前看到的一样。 我们实例化`Greeter`类，并使用这个对象。 与我们之前看到的一样。

再之后，我们直接使用类。 我们创建了一个叫做`greeterMaker`的变量。 这个变量保存了这个类或者说保存了类构造函数。 然后我们使用`typeof Greeter`，意思是取Greeter类的类型，而不是实例的类型。

或者更确切的说，"告诉我`Greeter`标识符的类型"，也就是构造函数的类型。 这个类型包含了类的所有静态成员和构造函数。

之后，就和前面一样，我们在`greeterMaker`上使用`new`，创建`Greeter`的实例。



原文链接:https://www.cnblogs.com/dh-dh/p/5210241.html