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

- Structs：结构

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

- Interfaces：接口

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

- Delegates：委托
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

- Events：事件
  基于 Publisher-Subscriber 设计模式

一般事件的声明与调用

```c#

//首先声明一个委托
public delegate void TaskMessageHandler(string taskMsg);

//基于委托定义事件
public event TaskMessageHandler TaskMessage;

//实际调用
public class Tasks
{
  ...
  protected void OnTaskMessageChange(string newMessage){
    ...
    if(!String.IsNullOrEmpty(newMessage))
      //调用该事件，并传递相关参数
      TaskMessage(newMessage);
    else{
      ...
    }
  }
  ...
}

```

.Net 标准事件模式
标准事件的核心是对 EventArgs 的继承与扩充，然后通过 EventHandler 声明。

```c#
//标准事件委托声明
void OnEventRaised(Object sender, EventArgs args);

//自定义事件参数
public class TaskMessageArgs: EventArgs
{
  public string TaskMessage{get;}
  public TaskMessageArgs(string taskMsg)
  {
    TaskMessage = taskMsg;
  }
}

//声明并引用
public class TaskManager
{
  public event EventHandler<TaskMessageArgs> TaskDone;
  public void FinishTask()
  {
    TaskDone?.Invoke(this, new TaskMessageArgs("Task Done"));
  }
}
```

- Properties：属性，类的成员，提供访问字段的灵活方法

```C#
public class MyClass
{
  //私有变量
  private int _myInt;

  //公开属性
  public int MyInt
  {
    get
    {
      return _myInt;
    }
    set
    {
      _myInt = value;
    }
  }
}
```

- Expressions,Statements,Operators：表达式、语句、操作符
  所有高级语言都有的特性，不举例子了。。略

- Attributes：特性，为程序代码添加元数据或声明性信息，运行时，通过反射可以访问特性信息

```c#
[attribute(some_parameter, other_parameters = value ...)]
class ClassElement{...}
```

.Net 框架提供了三种预定义特性：

1. AttributeUsage：描述了如何使用一个自定义特性类，规定了特性可以应用到的项目的类型。
2. Contitional：标记了一个条件方法，其执行依赖于指定的预处理标识符，如 Debug 或 Trace。[Conditional("DEBUG")]
3. Obsolete：标记了不应该被使用的程序实体[Obsolete(message)]

构建自定义特性：

```c#
  //自定义特性
  [AttributeUsage(AttributeTargets.Class|
  AttributeTargets.Constructor |
  AttributeTargets.Field |
  AttributeTargets.Method |
  AttributeTargets.Property,
  AllowMultiple = true)]

  public class MyAttrib :System.Attribute
  {
    private int _num;
    private string _msg;

    public MyAttrib(int num)
    {
      this._num = num;
    }
    public int Num
    {
      get
      {
        return _num;
      }
    }
    public string Message
    {
      get
      {
        return _msg;
      }
    }
  }

  //应用自定义特性
  [MyAttrib(1, Message="A Normal Class")]
  class Rectangle
  {
    ...

    [MyAttrib(3, Message="A Normal Method")]
    public double GetArea()
    {
      ...
    }
  }
```

- Literals：字面值（或理解为常量值），区别常量，常量是和变量相对的

例如字符串的字面值就是下面的 Hello World！
string s = "Hello World!";

## C\# 2 特性 (VS 2005)

- Generics：泛型

```c#
  List<T> MyList;
```

- Partial types：分部类型，可以将类、结构、接口等类型定义拆分到多个文件中

```c#
  //in file1.cs
  public partial class MyClass
  {
    ...
  }

  //in file2.cs
  public partial class MyClass
  {
    ...
  }

```

- Anonymous methods：匿名方法

匿名方法一般是通过 delegate 关键字创建委托实例来声明的。

```c#
delegate void MyDelegate(int num);
...
MyDelegate md = delegate(int x)
{
  Console.WriteLine("Anonymous Method: {0}", x);
  ...
}
```

- Iterators：迭代器
  迭代器的作用就是对集合中的对象能够方便的遍历。通过实现两个泛型接口：IEnumerable<T>和 IEnumerator<T>。

  还可以通过 yield 关键字定义迭代器方法：

  ```C#
  public IEnumerable<int> GetNumbers()
  {
    int index;
    yield return 0;
    yield return 1;
    yield return 2;
    yield return 3;
    yield return 4;
    yield return 5;

    index = 100;
    while(index < 110)
    {
      yield return index++;

    }
    //注意： yield return 和 return 不能出现在同一个方法中。
  }
  ```

* Nullable types：可以为 Null 的类型，该类可以是其它值或者 null

```c#
int? varNullable;
```

- Getter/setter separate accessibility：属性访问控制

```c#
  public class MyClass
  {
    //老式写法
    private int _myInt;
    public int MyInt
    {
      get{return _myInt;}
      set{_myInt = value;}
    }
    //新式写法
    public string MyString {get;set;}
  }
```

- Method group conversions (delegates)：方法组转换，可以将声明委托代表一组方法，隐式调用,简化 delegate 的调用方式

  思考：与接口的异同之处

```c#

```

- Covariance and Contra-variance for delegates and interfaces：委托、接口的协变和逆变
  隐式泛型转换， 通过该特性，进一步简化了委托的声明和引用，类型、返回值不再必须完全一致。
  Covariance: 用衍生类型声明并分配给其上级类 见下
  Contravariance： 与协变相反。

```c#
// Assignment compatibility.
string str = "test";
// An object of a more derived type is assigned to an object of a less derived type.
object obj = str;

// Covariance.
IEnumerable<string> strings = new List<string>();
// An object that is instantiated with a more derived type argument
// is assigned to an object instantiated with a less derived type argument.
// Assignment compatibility is preserved.
IEnumerable<object> objects = strings;

// Contravariance.
// Assume that the following method is in the class:
// static void SetObject(object o) { }
Action<object> actObject = SetObject;
// An object that is instantiated with a less derived type argument
// is assigned to an object instantiated with a more derived type argument.
// Assignment compatibility is reversed.
Action<string> actString = actObject;
```

- Static classes：静态类

1、静态类不能实例化
2、仅包含静态成员 static
3、是密封的 sealed，不可继承
4、不能包含实例构造函数

```c#
static class CompanyInfo
{
    public static string GetCompanyName() { return "CompanyName"; }
    public static string GetCompanyAddress() { return "CompanyAddress"; }
}
```

- Delegate inference：委托推断，允许将方法名直接赋给委托变量, 简化了委托的使用。

```c#

using System;

delegate void SomeAction();

class Test
{
    static void Main(string[] args)
    {
        // Without using delegate inference
        SomeAction oldStyle = new SomeAction (SayHello);

        // Now using delegate inference
        SomeAction newStyle = SayHello;
    }
    static void SayHello()
    {
        Console.WriteLine ("Hello");
    }
}
```

## C# 3 特性 (VS 2008)

- Implicitly typed local variables：局部变量隐式类型声明
  即 var 关键字的引入，简化声明，编译器根据实际分配进行强类型定义。

- Object and collection initializers：对象和集合初始化器,即在声明对象的时候直接初始化

```c#
Student student = new Student{Name="Tim", Age=10, Grade=2, Gender='M'};

```

- Auto-Implemented properties：自动属性，自动生成属性方法，声明更简洁

```c#
class Student
{
  //Auto-Implemented properties
  public int Age {get;set;}
  public string Name {get;set;}
  public int Grade {get;set;}
  public char Gender{get;set;}

  public string FirstName {get;set;} = "Tim"; // C# 6.0

}
```

- Anonymous types：匿名类型, 提供一种便利方法将一组只读属性封装到单个对象中

```c#
var v = new {Amount=108, Message="Total"};
...
Console.WriteLine(v.Amount+v.Message);
```

- Extension methods：扩展方法:
  向现有类型“添加”方法而不需要创建新的派生类、重新编译或者用其他方式修改原始类型。扩展方法是一种静态方法，但可以像扩展类型上的实例方法一样进行调用。

```C#
namespace ExtensionMethods
{
  public static class MyExtensions
  {
    public static int WordCount(this String str)
    {
      return str.Split(new char[]{' ', '.','?'},StringSplitOptions.RemoveEmptyEntries).Length;
    }

  }
}

//using it
using ExtensionMethods;

...
//in some method
string s = "Hello Extension Methods";
int i = s.WordCount();

```

- Query expressions：查询表达式

```c#
int hiScoreCnt =
    (from score in scores
     where score > 80
     select score)
     .Count();
```

- Lambda expression：Lambda 表达式

```C#
(input_parameters) => expression

(input_parameters) => {<sequence of statements>}

Func<int ,int, bool> testForEquality = (x,y)=>x==y;
```

- Expression trees：表达式树

  以树形数据结构表示代码，是一种新数据类型，其中每一个节点都是一种表达式
  [参考文章](!https://www.jianshu.com/p/a0b8a4808890)

  构建表达式树：(x, y)=>Math.Sin(x)+Math.Cos(y)

  ```c#
  ParameterExpression ParmsX=Expression.Parameter(typeof(double),"x"); //参数X
  ParameterExpression ParmsY=Expression.Parameter(typeof(double ),"y");  //参数Y

  var left=Expression.Call(null,typeof(Math).GetMethod("Sin"),ParmsX);  //树左边节点
  var right=Expression.Call(null,typeof(Math).GetMethod("Cos"),ParmsY);//树右边节点
  var body=Expression.Add(left,right);  //合成表达式树主体

  LambdaExpression lambda=Expression.Lambda<Func<double,double,double>>(body,new ParameterExpression []{ParmsX,ParmsY});
  //转成的表达式树，在编译后，就可以调用委托所指向的方法
  ```

- Partial methods：分部方法
  与分部类（Partial Class）应用场景类似。可以解决自动生成代码的更新覆盖问题
  主要目的：
  1. 隔离用户代码和自动生成的代码
  2. 隔离开发人员的代码实现关注点

分部方法必须以 partial 开头，返回必须为 void。
分部方法可以有 in 或 ref 参数，但不能有 out 参数。
分部方法为隐式 private 方法，因此不能为 virtual 方法。
分部方法不能为 extern 方法，因为主体的存在确定了方法是在定义还是在实现。
分部方法可以有 static 和 unsafe 修饰符。
分部方法可以是泛型的。约束将放在定义分部方法声明上，但也可以选择重复放在实现声明上。参数和类型参数名称在实现声明和定义声明中不必相同。
你可以为已定义并实现的分部方法生成委托，但不能为已定义但未实现的分部方法生成委托。

```c#
//Definition in file1.cs
partial void onNameChanged();

//Implementation in file2.cs
partial void onNameChanged()
{
  //Method body statements.
}
```

## C# 4 特性 (VS 2010)

- Dynamic binding:动态绑定

```c#
dynamic d = GetSomeObject();
d.quack(); //编译时，编译器不知道哪里有quack，但在运行时会有的。

```

- Named and optional arguments：命名实参和可选实参
  有了命名实参，可以不必记住函数参数的顺序，按名称赋值即可：

PrintOrderDetails(seller: "Gift shop", 31, product:"Red Mug");

- Generic co- and contravariance：泛型的协变和逆变

- Embedded interop types (“NoPIA”)：开启嵌入类型信息，增加引用 COM 组件程序的中立性

设为 True，实际上就是不引入互操作集（编译时放弃 COM 程序集），仅编译用户代码的程序集。

## C# 5 特性 (VS 2012)

- Asynchronous methods：异步方法
  异步的好处在于非阻塞，异步编程的核心是 Task 和 Task<T>对象，这两个对象对异步操作建模。接受关键字 async 和 await 支持。

  - 对 I/O 绑定代码，等待一个在 async 方法中返回 Task 或者 Task<T>的操作。
  - 对 CPU 绑定代码，等待一个使用 Task.Run 方法在后台线程启动的操作。

  async 和 await 配合使用

  ```c#
  private readonly HttpClient _httpClient = new HttpClient();

  downloadButton.Clicked += async (o, e)=>{

    // this line will yield control to the UI as the request
    // from the web service is happing
    //
    // the UI thread is now free to perform other work.

    var stringData = await _httpClient.GetStringASync(URL);
    DoSomethingWithData(stringData);
  }
  ```

- Caller info attributes：调用方信息特性，调用时访问调用者的信息

CallerMemberNameAttribute
The parameter with this attribute will be filled with calling method name.

CallerLineNumberAttribute
The parameter with this attribute will be filled with the line number on source file where this method is called.

CallerFilePathAttribute
The parameter with this attribute will be filled with a complete file path at compile time of the class where this method is called.

```c#
using System;
using System.Runtime.CompilerServices;
namespace CSharpFeatures
{
    class CallerInfoExample
    {
        static void Main(string[] args)
        {
            // Calling method
            show();
        }
        // We must specify optional parameters to get caller info.
        static void show([CallerMemberName] string callerName = null,
            [CallerFilePath] string callerFilePath = null,
            [CallerLineNumber] int callerLine = -1)
        {
            Console.WriteLine("Caller method Name: {0}", callerName);
            Console.WriteLine("Caller method File location: {0}", callerFilePath);
            Console.WriteLine("Caller method Line number: {0}", callerLine);
        }
    }
}

// output:
Caller method Name: Main
Caller method File location: f:\C#\C# Features\CSharpFeatures\CallerInfoExample.cs
Caller method Line number: 10
```

## C# 6 特征 (VS 2015)

- Compiler-as-a-service (Roslyn)
  “编译器为服务”， Roslyn 提供了开源的拥有丰富的代码分析 API 的 C#和 Visual Basic 编译器。它允许编译与 Visual Studio 相同 API 的代码分析工具。
  [Github 地址](!https://github.com/dotnet/roslyn)

- Import of static type members into namespace：支持仅导入类中的静态成员 using static 指令

```c#
//原代码
...
  public double CircleArea(double Radius)
  {
    return Math.PI * Math.Pow(Radius, 2);
  }

//新代码
using System;
//here
using static System.Math;

...
  public double CircleArea(double Radius)
  {
    return PI * Pow(Radius, 2);
  }

```

- Exception filters：异常过滤器

```c#
try
{
  ...
}
catch (Exception e) if(e.InnerException != null)
{
  ...
}
```

- Await in catch/finally blocks：支持在 catch/finally 语句块使用 await 语句
  对于耗时的操作可以通过 await 进行异步

```c#
public async Task SubmitDataToServer()
{
  try
  {
    ...
  }
  catch
  {
    await LogExceptionAsync();
  }
  finally
  {
    await CloseConnectionAsync();
  }
}
```

- Auto property initializers：自动属性初始化

```c#
public class Point
{
  public int X {get;set;} = 100; // get and set
  public int Y {get;} = 200;      // read-only auto-property with intializer
  public string Name {get; protected set;} = "Point"; //
  public int Area {get;set;} = CalcArea(100,200);

  public ICollection<UserDto> Users {get;} = new HashSet<UserDto>();

  public static double CalcArea(int length, int width)
  {
    return length * width;
  }
}
```

- Default values for getter-only properties：设置只读属性的默认值
  见上

- Expression-bodied members：支持以表达式为主体的成员方法和只读属性

PropertyType PropertyName => expression;

```C#
using System;

public class Person
{
  public Person(string firstName, string lastName)
  {
    fname = firstName;
    lname = lastName;
  }
  private string fname;
  private string lname;

  public override string ToString() => $"{fname} {lname}".Trim();
  public void DisplayName() =>Console.WriteLine(ToString());
}
```

- Null propagator (null-conditional operator, succinct null checking)：Null 条件操作符

```C#
using System;
namespace CSharpFeatures
{
  class Student
  {
    public string Name{get;set;}
  }
  class NullConditionalOperator
  {
    public static void Main(string[] args)
    {
      Student student = new Student(){Name="Peter", Email = "peter@abc.com";}
      Console.WriteLine(student?.Name?.ToUpper()??"Name is empty");
    }
  }
}
```

- String interpolation：字符串插值，产生特定格式字符串的新方法

```c#
string name = "Mark";
var date = DateTime.Now;

// Composite formatting:
Console.WriteLine("Hello, {0}! Today is {1}, it's {2:HH:mm} now.", name, date.DayOfWeek, date);
// String interpolation:
Console.WriteLine($"Hello, {name}! Today is {date.DayOfWeek}, it's {date:HH:mm} now.");
// Both calls produce the same output that is similar to:
// Hello, Mark! Today is Wednesday, it's 19:40 now.
```

- nameof operator：nameof 操作符，返回方法、属性、变量的名称

```c#
Console.WriteLine(nameof(System.Collections.Generic));  // output: Generic
Console.WriteLine(nameof(List<int>));  // output: List
Console.WriteLine(nameof(List<int>.Count));  // output: Count
Console.WriteLine(nameof(List<int>.Add));  // output: Add

var numbers = new List<int> { 1, 2, 3 };
Console.WriteLine(nameof(numbers));  // output: numbers
Console.WriteLine(nameof(numbers.Count));  // output: Count
Console.WriteLine(nameof(numbers.Add));  // output: Add
```

- Dictionary initializer：字典初始化

```c#
  ...
  Dictionary<int, Student> dictionary = new Dictionary<int, Student>()
  {
    { 1, new Student(){ ID = 101, Name = "Rahul Kumar", Email = "rahul@example.com"} },
    { 2, new Student(){ ID = 102, Name = "Peter", Email = "peter@example.com"} },
    { 3, new Student(){ ID = 103, Name = "Irfan", Email = "irfan@abc.com"} }
  };
  foreach (KeyValuePair<int, Student> kv in dictionary)
  {
    Console.WriteLine("Key = "+kv.Key + " Value = {" + kv.Value.ID +", "+ kv.Value.Name +", "+kv.Value.Email+"}");
  }
```

## C# 7 特征 (Visual Studio 2017)

- out variables：out 变量直接声明，例如可以 out int parameter
  在此之前，out 参数必须先声明才可以引用，该特性出来后，可以直接在引用的时候声明
  ```c#
  //原有写法
  string userName;
  GetUser(out userName);
  ...
  //新写法
  GetUser(out string userName);
  ...
  ```
- Pattern matching：模式匹配，根据对象类型或者其它属性实现方法派发

```c#
  //before
  if(shape is Square)
  {
    var s = (Square) shape;
    return s.Side * s.Side;
  }

  //now
  if(shape is Square s)
  {
    return s.Side * s.Side;
  }
  //扩展了switch ，原来switch只支持常量，现在支持类型
  switch(shape)
  {
    case Square s:
      return s.Side*s.Side;
      ...
  }
  //case 中的when
  switch(shape)
  {
    case Square s when s.Side == 0:
      return 0;
    case Square s:
      return s.Side*s.Side;
    ...
  }
```

- Tuples：元组, 引入元组就是为了方法返回时对多个值进行打包

```C#
var unnamed = ("one", "two");
var named = (first: "one", second:"two");

var sum = 100;
var count = 3;
var accumulation = (sum, count);

var left = (a: 5, b:10);
var right = (a:5, b:10);
Console.WriteLine(left == right);//"true"

(int a, int b)? nullableTuple = right;
Console.WriteLine(left == nullableTuple); //"true"

(int? a, int? b) nullableMembers = (5,10);
Console.WriteLine(left == nullableMembers); //true

(long a, long b) longTuple = (5,10);
Console.WriteLine(left == longTuple); //true

(long a, int b) longFirst = (5,10);
(int a, long b) longSecond = (5,10);
Console.WriteLine(longFirst, longSecond); //true

//嵌套
(int (int, float)) nestedTuple = (1, (2, 3.2));

//转换
var unnamed = (42, "the meaning of life");
var anonymous = (16, "a perfect square");
var named = (Answer: 42, Message: "the meaning of life");
var differentNamed =(secretConstant: 42, Label: "the meaning of life");

anonymous = named; //可以赋值
named = unnamed; //可以赋值 named 的字段名称仍然保留
Console.WriteLine($"{named.Answer}, {named.Message}");

named = differentNamed //字段名不会改变
Console.WriteLine($"{named.Answer}, {named.Message}");

//析构
...
(int count, double sum, double sumOf) = ComputeSumAndSumOf(sequence);
//or
var (count, sum, sumOf) = ComputeSumAndSumOf(sequence);
(int count, var sum, var sumOf) = ComputeSumAndSumOf(sequence);

Console.WriteLine($"{count}, {sum}, {sumOf}");

Public class Person
{
  public string FirstName{get;}
  public string LastName{get;}
  public Person(string first, string last)
  {
    FirstName = first;
    LastName = last;
  }
  public void Deconstruct(out string firstName, out string lastName)
  {
    firstName = FirstName;
    lastName = LastName;
  }
}
var p = new Person("Max", "Payne");
var (first, last) = p;

//as an out param
Dictionary<int, (int, string)> dict = new Dictionary<int, (int, string)>();
dict.Add(1,(123,"First"));
dict.Add(2,(124,"Second"));
dict.Add(3,(125,"Last"));

dict.TryGetValue(2, out (int num, string place) pair);
Console.WriteLine($"{pair.num}: {pair.place}");

```

- Deconstruction：元组解析
  见上

- Discards：弃元， 没有命名的变量，只是占位，后面代码不需要使用其值
  (_, _, area) = city.GetCityInformation(cityName);

- Local Functions：局部函数 / 本地函数
  是嵌套在另一成员中的类型的私有方法。仅可被其包含成员调用。
  <modifiers: async|unsafe> <return-type> <method-name> <parameter-list>

```c#
class Example
{
  static void Main(string[] args)
  {
    ....
  }

  private static string GetText(string path, string filename)
  {
    var sr = File.OpenText(AppendPathSeparator(path)+filename);
    ...

    //local function
    string AppendPathSeparator(string filepath)
    {
      if(!filepath.EndsWith(@"\"))
      {
        filepath += @"\";
      }
      return filepath;
    }
  }
}
```

- Binary Literals：二进制字面量

```c#
int nineteen = 0b10011;
```

- Digit Separators：数字分隔符

- Ref returns and locals：引用返回值和局部变量
- Generalized async return types：async 中使用泛型返回类型
- More expression-bodied members：允许构造器、解析器、属性可以使用表达式作为 body
- Throw expressions：Throw 可以在表达式中使用

## C# 7.1 特征 (Visual Studio 2017 version 15.3)

- Async main：在 main 方法用 async 方式
- Default expressions：引入新的字面值 default
- Reference assemblies：
- Inferred tuple element names：
- Pattern-matching with generics：

## C# 8.0 特征 (Visual Studio 2017 version 15.7)

- Default Interface Methods 缺省接口实现
- Nullable reference type NullableReferenceTypes 非空和可控的数据类型
- Recursive patterns 递归模式
- Async streams 异步数据流
- Caller expression attribute 调用方法表达式属性
- Target-typed new
- Generic attributes 通用属性
- Ranges
- Default in deconstruction
- Relax ordering of ref and partial modifiers
