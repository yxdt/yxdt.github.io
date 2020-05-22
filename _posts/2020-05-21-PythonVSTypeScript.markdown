---
layout: post
title: TypeScript与Python的语法对照
date: 2020-05-21 13:34:56 +0800
categories: [Language, Python]
tag: [Python, TypeScript]
---

Javascript/TypeScript 和 Python 都属于脚本语言，两者有很多相似的地方，这篇文章就对其进行一个对照，供后续参考，TypeScript 作为 Javascript 的类型加强，极大扩充了 Javascript 语言本身的特性。

<!--more-->

## 1. 变量类型

TypeScript 是对 Javascript 的强化，其中就是对变量的声明时的强化约束，通过 var,let, const 等关键字声明变量。
Python 不需要对变量进行声明，直接赋值即可。

| TypeScript    | Python           | 说明                                                      |
| ------------- | ---------------- | --------------------------------------------------------- |
| any           |                  | 任意类型                                                  |
| boolean \*    | numbers          | python 中布尔值为数值                                     |
| number \*     | numbers.Number   |                                                           |
| number        | numbers.Real     | float                                                     |
| number/bigint | numbers.Integral |                                                           |
|               | numbers.Complex  | 复数： 含 real，imag                                      |
|               | numbers.Rational | 实数： numerator,denominator                              |
| string \*     | String           | 不可变 py: len(), a[i],切片 a[i:j:k]                      |
|               | bytes()          | 不可变 py:转换 bytes.decode(), str.encode()               |
| tuple         | Tuple            | 元组，不可变， 可保存不同类型，但 TS 基于 Js 因此要注意   |
| Array         | List             | TS 可变，对应 Py 的 Sequences 可变部分 用“[]”表示         |
| Array         | bytearray()      | TS 可变，对应 Py 的 Sequences 可变部分 用“[]”表示         |
| Array         | 扩展模块 array   | Py: array('1'), array('d',[1.0,2.0,3.3])                  |
| Array         | set()            | 可变集合 Py: add()                                        |
|               | frozenset()      | Py: 不可变集合                                            |
| Array         | set()            | 可变集合 Py: add()                                        |
| object        | 字典 Dictonaries | 映射 ：Key/value {key1:value1, key2:value2}               |
| enum          | Enum             | Py 中没有枚举类型，可以引用 Enum 模块，用变量来代替       |
| void          | None             | 函数无返回值时可以用 None，也可以不写                     |
| null \*       | None             | 空值                                                      |
| undefined \*  | NotImplemented   |                                                           |
| never         |                  | TS 中 never 是根本没有返回如跳出异常，void 是返回一个空值 |
| symbol        |                  | TS 不可改变且唯一，Py 中没有对应的常量，只能约定          |
| Object        |                  | TS 基本类型(number,string,boolean,bigint,symbol,null)之外 |

\* 为 JS 基本数据类型

## 2. 运算符

两者大同小异，下面只列出不同的运算符。

| 运算符     |     Typescript      |          Python           | 备注                               |
| ---------- | :-----------------: | :-----------------------: | ---------------------------------- |
| 算数运算符 |         ++          |                           | 自增                               |
|            |         --          |                           | 自减                               |
|            |                     |            //             | 整除                               |
| 关系运算符 |         ===         |                           | 全等，类似 Py 的身份运算符         |
| 逻辑运算符 |         &&          |            and            | 与                                 |
|            |        \|\|         |            or             | 或                                 |
|            |          !          |            not            | 非                                 |
| 位运算符   |         >>>         |                           | 无符号右移                         |
| 条件运算符 | \<a\> ?\<b\> :\<c\> | \<b\> if \<a\> else \<c\> | 三元运算符                         |
| 成员运算符 |                     |        in， not in        | 是否在序列中，对应 TS 的 indexOf() |
| 身份运算符 |                     |        is， is not        | 两者 id 值是否相同，类似 TS 的 === |

## 3. 语句

- 条件语句：

  Typescript：

  ```typescript
  if (condition_1) {
    statement;
  } else if (condition_2) {
    statement;
  } else {
    statement;
  }
  ```

  Python:

  ```Python
  if condition_1:
    statements
  elif condition_2:
    statements
  else:
    statements
  ```

- 循环语句：

  - while 循环

    Typescript:

    ```typescript
    while (condition_1) {
      statements;
    }
    ```

    ```typescript
    do {
      statements;
    } while (condition_2);
    ```

    Python:

    ```python
    while condition_1:
      statements
    ```

  - for 循环

    Typescript:

    ```typescript
    for (init; cond; run) {
      statement;
    }
    for (variable in object) {
      statement;
    }
    for (const item of iterable) {
      statement;
    }
    for await (variable of iterable) {
      statement;
    }
    ```

    Python:

    ```python
    for variable in sequence:
      statements
    else:
      statements
    ```

## 4. 函数

- 函数声明

Typescript：

最多 255 个参数

```typescript
//函数定义
function funName(param1 [:type], param2 [:type]) [:returnType] {
  statements;
}

//可选参数 param2
function funName(param1 [:type], param2? [:type]) [:returnType] {
  statements;
}

//默认参数 param2
function funName(param1 [:type], param2 [:type] = defaultValue) [:returnType] {
  statements;
}

//剩余参数 不定长参数
function funName(param1 [:type], ...restParams [:type[]]) [:returnType] {
  statements;
}

//匿名函数 函数表达式
//函数声明在执行之前被解析（提升），表达式在执行时作为语句出现
//效率相对低一些
var varName = function([arguments]){...}
//匿名函数之箭头函数 Lambda 函数
var arrow =(arguments)=>{...}
//IIFE
(function(){...})();

//Function 构造函数
//效率更低, 被创建在全局环境，因此运行时只能访问全局环境和自己的局部变量，
//不能访问相关上下文环境
var res = new Function([arguements]){...}

//函数重载
//函数名字相同，参数类型不同、参数数量不同、参数类型顺序不同
function fun1(n1:type1):type; //first
function fun1(s1:type2):type; //second
function fun1(prm: any):type{ //realize
  ...
}
```

Python

```python
#函数定义
def func(params):
  statements
  return [expression]

#缺省参数
def func(param1, param2=123):
  statements
  return [expression]

#不定长参数
def func([formal_args,] *var_args_tuple):

  statements

  for var in var_args_tuple:
    ...

  return [expression]

#匿名函数
lambda [arg1[,arg2...]]:expression

```

## 5. 变量作用域

| 类型         | typescript                 | python                          |
| ------------ | -------------------------- | ------------------------------- |
| 全局变量     | 未声明的自动提升为全局变量 | 函数体外赋值为全局变量          |
| 局部变量     | 局部声明的变量             | 赋值的变量                      |
| 引用全局变量 | 变量未定义自动提升为全局   | 除非显式 global，否则为局部变量 |

## 6. 模块与命名空间

一般以文件为单位，一个源文件属于统一模块。【TS】如果相似功能集中在一个文件过大，则可以用命名空间来分割到不同文件。

| 模块操作 | TypeScript                               | Python                            |
| -------- | ---------------------------------------- | --------------------------------- |
| 导入     | import module from './ModuleName'        | import func_name                  |
|          | import {func_name} from './ModuleName'   | from module_name import func_name |
|          | import abc from './module'//对应默认导出 |                                   |
|          | import \* as abc from './module'         | import module as abc              |
| 导出     | export                                   |                                   |
| 默认导出 | export default ...                       |                                   |

【PY】

- 使用 import module 时，module 本身被引入，但是保存它原有的命名空间，所以我们需要使用 module.name 这种方式访问它的 函数和变量。
- from module import 这种方式，是将其它模块的函数或者变量引到当前的命名空间中，所以就不需要使用 module.name 这种方式访问其它的模块的方法了。

## 7. 类和对象

JavaScript 本身其实是面向对象 Object 的语言，本身并没有类 Class 的概念，因此产生了很多有别于其他 OOP 语言的特殊之处。后来增加了 class 关键字，让 TypeScript/Javascript 对大多数程序员不再那么另类。

| 说明       | TypeScript                      | Python                                     |
| ---------- | ------------------------------- | ------------------------------------------ |
| 类定义     | class ClassName{...}            | class ClassName:                           |
| 构造方法   | constructor([params])           | 内部函数 \_\_init\_\_(self,[params])       |
| 实例化     | var obj = new Obj([params])     | obj = ClassName([params])                  |
| 属性与方法 | 通过 IIFE 实现对私有变量的封装  | 通过双下划线开始定为私有变量，但其实仍可见 |
| 继承       | class Child extends Parent{...} | class Child(Parent):                       |
| 引用父类   | super                           | Parent                                     |
| 接口       | interface IInterfaceName{...}   |                                            |
| 抽象类     | abstract class Animal{...}      |                                            |
| 静态变量   | static                          |                                            |
| 泛型类     | class SomeClass<T>{...}         |                                            |

JavaScript

```javascript
class Car {
  //静态变量由类名引用
  static num: number;

  engine: string;

  constructor(engine: string) {
    this.engine = engine;
  }

  display(): void {
    console.log("The Engine is:" + this.engine);
  }
}
var car = new Car("XXVTI");
//access field
var theEngine = car.engine;
//access method
car.display();

//类继承
class Sedan extends Car {
  doors: number;
  constructor(engine: string) {
    super(engine);
    this.doors = 4;
  }
  display(): void {
    //调用父类方法
    super.display();
    console.log(`And have ${this.doors} doors`);
  }
}

const sedan = new Sedan("VTI");
sedan.display();
```

Python

```python
class Car:
  engine = ''
  def __init__(self, engine):
    self.engine = engine
  def display(self):
    print("The Engine is: %s"%(self.engine))

car = Car("XXVTI")
theEngine = car.engine
car.display()

class Sedan(Car):
  doors = 4
  def __init__(self,engine):
    Car.__init__(self,engine)
    self.doors = 4
  def display(self):
    Car.display(self)
    print("And have %d doors"%(self.doors))

honda = Sedan("VTI")
honda.display()
print(honda.doors)
print(dir(honda))

```
