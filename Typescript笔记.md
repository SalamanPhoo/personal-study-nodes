# Typescript笔记

## 基础类型

1. **布尔值**

   ~~~javascript
   let flag: boolean = false;
   ~~~

2. **数值**

   ~~~javascript
   let count: number = 10;
   ~~~

3. **字符串**

   ~~~javascript
   let str: string = 'hello'
   ~~~

4. **数组**

   ~~~javascript
   let list: number[] = [1,2,3];
   let list: Array<number> = [1,2,3];
   ~~~

5. **元祖 Tuple**

   ~~~javascript
   let x: [string,number];
   x = ['shandong':1,'shanxi':4]
   ~~~

6. **枚举**

   ~~~javascript
   enum Color {Red, Green, Blue}
   let c: Color = Color.Green;
   ~~~

7. **Any**

   >  在编程阶段还不清楚类型的变量指定一个类型 , 我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 `any`类型来标记这些变量 

8. **Void**

   >  表示没有任何类型 

9. **Null和Undefined**

10. **Never**

    >  表示的是那些永不存在的值的类型 

11. **Object**

    >  `object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型 

12. **类型断言**

    >  类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用 
    >
    > 类型断言有两种形式。 其一是“尖括号”语法：
    >
    > ~~~javascript
    > let someValue: any = "this is a string";
    > 
    > let strLength: number = (<string>someValue).length;
    > ~~~
    >
    > 另一个为`as`语法：
    >
    > ```ts
    > let someValue: any = "this is a string";
    > 
    > let strLength: number = (someValue as string).length;
    > ```



## 变量声明

**`let`和`const`**

> 两者都是块级作用于，不同在于`const`赋值后不能被修改



## 接口

1. **可选属性**

   > 接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。

   ~~~javascript
   interface Person{
       name?: string;
       age?: number;
   }
   ~~~

2. **只读属性**

   ~~~javascript
   interface Point{
       readonly x: number;
       readonly y: number;
   }
   ~~~

   > ` ReadonlyArray<T> ` 类型，它与`Array`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改。但是使用断言可以将`ReadonlyArray`重写为`Array`
   >
   > `readonly` vs `const`
   >
   > 最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用`readonly`。

3. **额外的检查属性**

   ~~~javascript
   interface Person{
       name?: string;
       age?: number;
       [propName: string]: any;		//可以匹配任意名称的属性
   }
   ~~~

4. **函数类型**

   ~~~javascript
   interface printUserFunc {
       (name: string, age: number): string
   }
   //使用函数类型接口
   const printUser: printUserFunc = function (name, age) {
       return '姓名：' + name + '----------年龄：' + age
   }
   document.body.innerHTML = printUser('小明', 12)
   ~~~

   > 为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型

5. **可索引的类型**

   >  TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型 

   ~~~javascript
   interface StringArray {
     [index: number]: string;
   }
   
   interface NumberDictionary {
     [index: string]: string;
     length: number;
     name: string;
   }
   
   interface ReadonlyStringArray {
       readonly [index: number]: string;
   }
   ~~~

   

## 类

> [资料](https://www.tslang.cn/docs/handbook/classes.html)

+ **类的声明**

  > 关键字：`class`

+ **类的继承**

  > 关键字：`extends`

+ **修饰符**

  + `public` 公共，默认修饰符
  + `private` 私有，标记成员不能在声明它的类的外部访问
  + `protected` 受保护的，与修饰符`private`相似，但`protected`成员在派生类（子类）中仍然可以访问

+ **存取器**

  + 属性被`private`修饰
  + `get/set`方法

+ **静态属性**

  > 关键字：`static`

+ **抽象类**

  > 一般作为其他派生类的基类，关键字：` abstract `

+ **构造函数**

  ~~~javascript
  class WorkerMan implements Person {
      name: string;
  
      [propName: string]: any;
  
      constructor(theName, readonly work: string) {
          this.name = theName
      }
  }
  
  let teacher = new WorkerMan('花花', 'teach')
  teacher.age = 11
  document.body.innerText = JSON.stringify(teacher)
  ~~~

  执行结果:

  ~~~
  {"work":"teach","name":"花花","age":11}
  ~~~





## 函数

+ **定义函数类型**

  ~~~javascript
  //完整函数类型
  const addFunc = function (x: number, y: number): number {
      return x + y
  }
  console.log(addFunc(1, 5))		//6
  ~~~

+ **推断类型**

  ~~~javascript
  const addFunc2: (num1: number, num2: number) => number =
      function(x, y) { return x + y; };
  console.log(addFunc2(1, 9))
  ~~~

  > 只要参数类型是匹配的，那么就认为它是有效的函数类型，而不在乎参数名是否正确。 这叫做“按上下文归类”，是类型推论的一种。 它帮助我们更好地为程序指定类型 

+ **可选参数和默认参数**

  ~~~javascript
  function printParams(name: string, age?: number, gender = '男'): string {
      return '姓名：' + name + '   年龄：' + age + '   性别：' + gender
  }
  ~~~

+ **剩余参数**

  ~~~javascript
  function printParams2(name: string, ...hobby: string[]): string {
      return '姓名：' + name + '   爱好：' + hobby.join(' ')
  }
  console.log(printParams2('小光', '篮球', '足球', '橄榄球'))
  ~~~

  > 1. 当需要同时操作多个参数或者不知道将会传入多少参数时使用
  > 2. 剩余参数只能放在参数列表最后

+ ***this***

  + `this`和箭头函数： 箭头函数能保存函数创建时的 `this`值，而不是调用时的值 

  + `this`参数

    > 可以给函数提供一个显示的`this`参数。`this`参数是一个假的参数，位于参数列表最前面

    ~~~javascript
    function f(this: void) {
        // make sure `this` is unusable in this standalone function
    }
    ~~~

  + ***this*参数在回调函数里**

    > 想在回调函数里面正确使用`this`：
    >
    > 1. 使用`this`参数的方式，并指定类型为`void`
    > 2. 箭头函数

+ **重载**

  >   JavaScript里函数根据传入不同的参数而返回不同类型的数据 





## 泛型

<T> [资料](https://www.tslang.cn/docs/handbook/generics.html)



## 枚举

+ **数字枚举**

  ~~~javascript
  enum Gender{
      Man,
      Woman = 3,
      Other
  }
  console.log(Gender.Other)		//4
  ~~~

  > 枚举类内，成员的值从0开始递增，枚举的成员可以初始化，后面的成员的值会自增

+ **字符串枚举**

  ~~~javascript
  enum Gender2 {
      Man = '男',
      Woman = '女',
      Other = '未知'
  }
  ~~~

  > 字符串枚举无法自增，并且每个成员都必须初始化

+ **异构枚举**

  ~~~javascript
  enum Gender3 {
      Man = '男',
      Woman = 1
  }
  ~~~

  > 枚举可以以字母数字混合，但是不推荐

+ **计算的和常量成员**

  > 1. 它是枚举的第一个成员且没有初始化器，这种情况下它被赋予值 `0` 
  > 2. 它不带有初始化器且它之前的枚举成员是一个 *数字*常量。 这种情况下，当前枚举成员的值为它上一个枚举成员的值加1。
  > 3.  枚举成员使用 *常量枚举表达式*初始化（比如加减乘除）





## 类型推论

> Typescript中，有些地方没有明确指出类型的地方，类型推论会帮助提供类型     [资料](https://www.tslang.cn/docs/handbook/type-inference.html)

+ **最佳通用类型**

  >  当需要从几个表达式中推断类型时候，会使用这些表达式的类型来推断出一个最合适的通用类型 。 如果没有找到最佳通用类型的话，类型推断的结果为联合类型，`(A | B | C)`。 

+ **上下文类型**

  >  TypeScript类型推论也可能按照相反的方向进行。 这被叫做“按上下文归类” 





## 类型兼容性

[资料](https://www.tslang.cn/docs/handbook/type-compatibility.html)