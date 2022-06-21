# TypeScript 最令人困惑的地方

[<<写给大家看的 TypeScript 教程>>](https://xiaoshude.github.io/typescript-tutorial/)，从写下这个教程三年过去了，期间帮不少同事解决 TS 问题、解答使用中的困惑，发现疑问基本都集中在几个。

口口相传的方式毕竟有限，整理下这些高频问题，希望能帮到更多人。

## 类名为什么是对象的类型而不是类的类型

很拗口，看例子：

```ts
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
```

上面 new Greeter 返回对象的类型为 Greeter。

如果我们要封装一个工厂函数来生成 Greeter 对象：

```ts
function createGreeter(constuctor): Greeter {
  return new Greeter()
}
```

怎么标注 `constructor` 的类型呢？

这就要用到 **ts 中的 typeof 操作符**：

```ts
function createGreeter(constuctor: typeof Greeter): Greeter {
  return new Greeter()
}
```

### 分析

1. ts 中的 class 定义同时做了两件事：

- 声明了一个值：js 中的构造函数；
- 声明一个类型：ts 中的 type，但是这个类型并不是构造函数的类型，而是**构造函数生成对象的类型**。

2. typeof 操作符，在 ts 中会被增强：

- 可以继续像 js 中，返回一个字符串值；
- 根据值生成一个 ts 类型，上面的例子即是这种用法；


### 常见疑惑

#### 1. Greeter 为什么同时作为构造函数的名字，又作为类型的名字，同名不冲突吗？

值和类型，属于不同类别，同一标识符可用于不同类别，用两个例子理解。

1. [Function Overloads](https://www.typescriptlang.org/docs/handbook/2/functions.html#function-overloads)

```ts
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
```

为什么上面的函数可以用一个名字 makeDate 呢？

因为函数类型由：函数名 + 入参类型（注意没有返回类型，想想为什么）共同标识。所以函数名可以重，只要入参类型不同，就能区分。

ts 类型的 overload 概念借鉴自其他语言，用 c++ 理解更直白。

2. c++ 函数重载

```c++
// function with float type parameter
float absolute(float var){
    if (var < 0.0)
        var = -var;
    return var;
}

// function with int type parameter
int absolute(int var) {
     if (var < 0)
         var = -var;
    return var;
}

int main() {
    
    // call function with int type parameter
    cout << "Absolute value of -5 = " << absolute(-5) << endl;

    // call function with float type parameter
    cout << "Absolute value of 5.5 = " << absolute(5.5f) << endl;
    return 0;
}
```

两个函数完全可以使用同一名字 `absolute`，编译器并不会搞混：
调用的时候，入参一定有具体类型，所以也一定能匹配到一个具体实现，
而返回值类型对匹配没作用，所以，返回值类型无法作为 overload 的标识之一。

#### 2.为什么 typeof 可以一会返回字符串、一会返回类型？

这就像一词多义：中，可以表示【好】，表示【平均】，表示【得到】。我们会根据语境选择合适的含义。

同样，编译器也可以根据 typeof 被使用的上下文，得知返回字符串还是类型：

```ts
const str = 'a'
const a = typeof str; // 这里求值
type A = typeof str; // 这里求类型
console.log('a', a) // "string" 
```

#### 3. 为什么要区分类型和值？

ts 中，类型和值的区别十分重要：

- 不同实体是不能互相运算的，应该放值的地方不能放类型；
- 类型在 js 中不存在，也就是，类型在最终产物中会消失，比如：

```ts
const str = 'a'
const a = typeof str;

type A = typeof str;

console.log('a', a)
```

编译后：

```js
const str = 'a';
const a = typeof str;
console.log('a', a);
```

意识到这一点很重要。

### 知识要点

- [通过定义 class 来定义类的实例类型](https://xiaoshude.github.io/typescript-tutorial/basic/type.html)；
- [语法增强：声明、类](https://xiaoshude.github.io/typescript-tutorial/basic/syntax-enhance.html)

## 续

篇幅有点长，其他问题再新开一篇。
