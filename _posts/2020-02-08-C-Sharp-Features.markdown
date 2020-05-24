---
layout: post
title: "C# 版本特性一览"
date: 2020-02-24 12:34:56 +0800
categories: Language
tag: [Programming, CSharp]
---

C# 作为微软.Net 的核心语言，从 2000 年随着.Net 一起诞生以来，经历的多次升级，语言功能也日趋丰富，为了更好地对语言深入理解，下面按照语言的版本对其特性进行一个梳理，并给出说明和样例。

<!--more-->

[!参考文章链接](https://blog.csdn.net/wlanye/article/details/86495518)

## C\# 1.0 特性

第 1 个版本，编程语言最基础的特性。

- Classes：面向对象特性，支持类类型

```C#
using System;

class Program
{
  static void Main(string[] args)
  {
    Console.WriteLine("Hello World!");
  }
}

```

Structs：结构

```C#
struct DateStruct
{
  public int Year;
  public int Month;
  public int Day;
}
DateStruct ds;
ds.Year = 2020;
ds.Month = 12;

```

Interfaces：接口

```C#
//声明
public Interface IMyInterface{
  public void MyFunc();
}
//使用
public MyClass:IMyInterface{
  ...
  public void MyFunc(){...}
}

```

Delegates：委托
一种引用类型,表示对具有特定参数列表和返回类型的方法的引用，可参考设计模式的“代理人模式”。

```c#
//声明委托
public delegate int DelNum(string theString);
//声明两个同参数及返回值函数
public int StringLength(string theString){...}
public int StringDouble(string theString){...}

//使用委托
DelNum  strOps =  StringLength;
strOps("abc");
strOps = StringDouble;
strOps("defg");
```

Events：事件
基于 Publisher-Subscriber 设计模式

```c#

```

Properties：属性，类的成员，提供访问字段的灵活方法

Expressions,Statements,Operators：表达式、语句、操作符
Attributes：特性，为程序代码添加元数据或声明性信息，运行时，通过反射可以访问特性信息
Literals：字面值（或理解为常量值），区别常量，常量是和变量相对的

C# 2 特性 (VS 2005)

Generics：泛型
Partial types：分部类型，可以将类、结构、接口等类型定义拆分到多个文件中
Anonymous methods：匿名方法
Iterators：迭代器
Nullable types：可以为 Null 的类型，该类可以是其它值或者 null
Getter/setter separate accessibility：属性访问控制
Method group conversions (delegates)：方法组转换，可以将声明委托代表一组方法，隐式调用
Co- and Contra-variance for delegates and interfaces：委托、接口的协变和逆变
Static classes：静态类
Delegate inference：委托推断，允许将方法名直接赋给委托变量
C# 3 特性 (VS 2008)

Implicitly typed local variables：
Object and collection initializers：对象和集合初始化器
Auto-Implemented properties：自动属性，自动生成属性方法，声明更简洁
Anonymous types：匿名类型
Extension methods：扩展方法
Query expressions：查询表达式
Lambda expression：Lambda 表达式
Expression trees：表达式树，以树形数据结构表示代码，是一种新数据类型
Partial methods：部分方法
C# 4 特性 (VS 2010)

Dynamic binding:动态绑定
Named and optional arguments：命名参数和可选参数
Generic co- and contravariance：泛型的协变和逆变
Embedded interop types (“NoPIA”)：开启嵌入类型信息，增加引用 COM 组件程序的中立性
C# 5 特性 (VS 2012)

Asynchronous methods：异步方法
Caller info attributes：调用方信息特性，调用时访问调用者的信息
C# 6 特征 (VS 2015)

Compiler-as-a-service (Roslyn)
Import of static type members into namespace：支持仅导入类中的静态成员
Exception filters：异常过滤器
Await in catch/finally blocks：支持在 catch/finally 语句块使用 await 语句
Auto property initializers：自动属性初始化
Default values for getter-only properties：设置只读属性的默认值
Expression-bodied members：支持以表达式为主体的成员方法和只读属性
Null propagator (null-conditional operator, succinct null checking)：Null 条件操作符
String interpolation：字符串插值，产生特定格式字符串的新方法
nameof operator：nameof 操作符，返回方法、属性、变量的名称
Dictionary initializer：字典初始化
C# 7 特征 (Visual Studio 2017)

Out variables：out 变量直接声明，例如可以 out in parameter
Pattern matching：模式匹配，根据对象类型或者其它属性实现方法派发
Tuples：元组
Deconstruction：元组解析
Discards：没有命名的变量，只是占位，后面代码不需要使用其值
Local Functions：局部函数
Binary Literals：二进制字面量
Digit Separators：数字分隔符
Ref returns and locals：引用返回值和局部变量
Generalized async return types：async 中使用泛型返回类型
More expression-bodied members：允许构造器、解析器、属性可以使用表达式作为 body
Throw expressions：Throw 可以在表达式中使用
C# 7.1 特征 (Visual Studio 2017 version 15.3)

Async main：在 main 方法用 async 方式
Default expressions：引入新的字面值 default
Reference assemblies：
Inferred tuple element names：
Pattern-matching with generics：
C# 8.0 特征 (Visual Studio 2017 version 15.7)

Default Interface Methods 缺省接口实现
Nullable reference type NullableReferenceTypes 非空和可控的数据类型
Recursive patterns 递归模式
Async streams 异步数据流
Caller expression attribute 调用方法表达式属性
Target-typed new
Generic attributes 通用属性
Ranges
Default in deconstruction
Relax ordering of ref and partial modifiers
