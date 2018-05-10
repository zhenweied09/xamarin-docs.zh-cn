---
title: C# 6 新功能概述
description: 最新版本的 C# 语言 – 版本 6 – 一直发展的要具有样本要少、 改进的清楚起见和更多的一致性的语言。 清理器初始化语法，能够使用 await catch/finally 块和 null 条件中？ 运算符将非常有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: e120b917a106d01067e073219bbe2688c2811448
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="c-6-new-features-overview"></a>C# 6 新功能概述

_最新版本的 C# 语言 – 版本 6 – 一直发展的要具有样本要少、 改进的清楚起见和更多的一致性的语言。清理器初始化语法，能够使用 await catch/finally 块和 null 条件中？运算符将非常有用。_

本文档介绍了 C# 6 的新功能。 单声道编译器完全支持，开发人员可以开始使用所有 Xamarin 目标平台的新功能。

本文包含简短的 C# 6 代码片段，它们说明了基本用法。
示例应用程序是一个命令行程序运行所有 Xamarin 目标平台并执行的各种功能。


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**什么是通过在 C# 6 中，新[Xamarin 大学](https://university.xamarin.com/)**


## <a name="development-environment"></a>开发环境

### <a name="mac"></a>Mac

* **Visual Studio for Mac**支持 C# 6： 你可以生成并编译 Xamarin 应用程序使用 C# 6 功能。
  阅读更多有关[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/)。

### <a name="windows"></a>Windows

* **Visual Studio 2015 和 2017年**和更高版本提供的 C# 6 的完整支持。 Visual Studio 的早期版本将不支持 C# 6。

* **适用于 Windows 的 Xamarin Studio**在编辑器中，不支持 C# 6 功能。



## <a name="compiler"></a>编译器

Mono C# 6 编译器包含在 Mono 4.0 及更高版本，即[免费下载](http://www.mono-project.com/download/)。
适用于 Mac 的 visual Studio 自动更新你的系统上的单声道安装。

Windows 用户必须具有[Visual Studio 2015 或 2017年 ^](https://www.visualstudio.com/)安装为 C# 6 代码编译 （即使你为 IDE 选择 Xamarin Studio for Windows）。

^ 或 *[Microsoft 生成工具 2015年](http://www.microsoft.com/download/details.aspx?id=48159)* 命令行编译或生成服务器，例如。

## <a name="using-c-6"></a>使用 C# 6

C# 6 编译器所有最新版本的 Visual Studio 中使用的 mac。
那些使用命令行编译器应确认`mcs --version`返回 4.0 或更高版本。
Visual Studio for Mac 用户可以检查它们具有 Mono 4 （或更高版本） 安装通过引用**有关 Visual Studio for Mac > Visual Studio for Mac > 显示详细信息**。

## <a name="less-boilerplate"></a>样本要少
### <a name="using-static"></a>using static
枚举和如某些类`System.Math`，是主要的静态值和函数的拥有者。 在 C# 6，你可以导入与单个类型的所有静态成员`using static`语句。 比较 C# 5 和 C# 6 中的典型三角函数函数：

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` 不会使公共`const`字段，如`Math.PI`和`Math.E`，直接访问：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>使用扩展方法使用静态

`using static`设施稍有以不同方式运行的扩展方法。 虽然扩展方法使用编写`static`，它们没有意义而无需对其运行的实例。 因此，在`using static`用于定义扩展方法，在其目标类型上可用的扩展方法的类型 (该方法的`this`类型)。 例如，`using static System.Linq.Enumerable`可用于扩展的 API`IEnumerable<T>`而不想在所有 LINQ 类型的对象：

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

前一个示例演示的行为差异： 扩展方法`Enumerable.Where`数组的静态方法时与关联`String.Join`可以无需对引用调用`String`类型。

### <a name="nameof-expressions"></a>nameof 表达式
有时，你想要引用已向名称授予的变量或字段。 在 C# 6 中，`nameof(someVariableOrFieldOrType)`将返回字符串`"someVariableOrFieldOrType"`。 例如，当引发`ArgumentException`你很有可能会想要将命名的参数无效：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

主要优势`nameof`表达式是它们是类型检查，而与工具支持重构兼容。 类型检查的`nameof`表达式是在情况下特别受欢迎其中`string`用于动态将类型相关联。 例如，在 iOS 中`string`用于指定类型的原型`UITableViewCell`中的对象`UITableView`。 `nameof` 可以确保此关联不会由于拼写错误或草率重构失败：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

虽然可传递的限定的名称`nameof`，仅的最后一个元素 (后的最后一个`.`) 返回。 例如，可以将数据绑定添加 Xamarin.Forms 中：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

有两个调用到`SetBinding`传递相同的值：`nameof(ReactiveType.StringField)`是`"StringField"`，而不`"ReactiveType.StringField"`最初可以推测。

## <a name="null-conditional-operator"></a>Null 条件运算符
向 C# 的以前的更新引入了可以为 null 的类型和 null 合并运算符的概念`??`减少样板文件代码以处理可以为 null 值时。 C# 6 继续使用"null 条件运算符"此主题`?.`。 当表达式的右侧上的对象上使用，null 条件运算符将返回的成员值，如果对象不是`null`和`null`否则为：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(同时`length0`和`length1`被推断为的类型为`int?`)

中的上一示例所示的最后一行`?`null 条件运算符结合`??`null 合并运算符。 新的 C# 6 null 条件运算符返回`null`数组，此时 null 合并运算符都会启动，并提供到 0 中的第二个元素上`lengths`数组 （是否适当或不是，当然，具体问题）。

Null 条件运算符应极大地减少样本 null 检查中许多应用程序需要的量。

有一些限制由于歧义而该 null 条件运算符。 不能紧跟`?`具有用圆括号括起来参数列表，当你可能希望使用委托来执行操作：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

但是，`Invoke`可以用于分隔`?`从参数列表和相比仍是标记的改进`null`-检查的样本的块：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字符串内插
`String.Format`函数具有传统上用作索引占位符在格式字符串，例如， `String.Format("Expected: {0} Received: {1}.", expected, received`)。 当然，添加一个新的值总是涉及到向上自变量计数、 重新编号操作占位符，并将新的自变量插入参数列表中的正确顺序烦人少任务。

C# 6 的新字符串内插功能大大改进了`String.Format`。 现在，你可以直接名称中带有前缀的字符串的变量`$`。 例如：

```csharp
$"Expected: {expected} Received: {received}."
```

当然，检查变量，并且拼写错误或非可用变量会导致编译器错误。

占位符不需要是简单变量，它们可以是任何表达式。 在这些占位符，你可以使用引号*而无需*转义这些引用。 例如，请注意`"s"`在下面的示例：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字符串内插支持的对齐方式和格式设置语法`String.Format`。 就像你以前编写`{index, alignment:format}`，在你编写的 C# 6 `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
中的结果：

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

字符串内插是有关的语法糖`String.Format`： 它不能与使用`@""`字符串和不符合`const`，即使使用没有占位符：

```csharp
const string s = $"Foo"; //Error : const requires value
```

通常使用的情况下生成与字符串内插的函数自变量的你仍需谨慎转义、 编码和区域性问题。 SQL 和 URL 查询至关重要，当然，若要清理。 与`String.Format`，字符串内插使用`CultureInfo.CurrentCulture`。 使用`CultureInfo.InvariantCulture`是稍微罗嗦：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C# 6 提供多种简洁的方式，可指定属性、 字段和成员。

### <a name="auto-property-initialization"></a>自动属性初始化

现在可以在与字段相同的简洁方法中初始化自动属性。 可以使用仅 getter 编写不可变的自动属性：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在构造函数中，你可以设置仅限 getter 的自动属性的值：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

此初始化自动属性既是一项常规节省空间的功能，也是开发人员希望以强调其对象中的不可变性一件好事。

### <a name="index-initializers"></a>索引初始值设定项

C# 6 引入了索引初始值设定项，它可以通过在具有一个索引器类型中设置的键和值。 通常，这是为`Dictionary`-样式数据结构：

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>表达式正文函数成员

Lambda 函数具有以下几个好处，其中之一只需节省了空间。 同样，表达式正文类成员允许小函数比起以前版本的 C# 6 可能变得更加简洁表示。

表达式正文函数成员使用 lambda 箭头语法而不是传统的数据块语法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

请注意，lambda 箭头语法不使用显式`return`。 为函数将该返回`void`，表达式还必须是一条语句：

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

表达式正文成员仍受到规则，`async`支持的方法，但不是属性：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>异常

没有任何有关它的两种方式： 异常处理是难以正确。 C# 6 中的新功能使异常处理更灵活且一致。

### <a name="exception-filters"></a>异常筛选器

根据定义，异常会出现在极少情况下，而且它可以是非常困难的原因和代码有关*所有*特定类型的异常可能发生的方式。 C# 6 引入了使用运行时计算的筛选器防止执行处理程序的功能。 这可通过添加`when (bool)`后正常模式`catch(ExceptionType)`声明。 在下面的示例，筛选器区分与相关分析错误`date`而不是其他分析错误的参数。

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>在 catch...await 最后...

`async` C# 5 中引入的功能已被语言游戏转换器。 在 C# 5 中，`await`中不允许使用`catch`和`finally`阻止给定的值令人烦恼`async/await`功能。 C# 6 中删除此限制，允许异步结果，如下面的代码段中所示，一致地等待通过计划：

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>总结

C# 语言一直发展来提高开发人员提高工作效率，同时还将提升好的做法，并支持工具。 本文档已授予在 C# 6 中的新语言功能的概述，并简要演示如何使用它们。

## <a name="related-links"></a>相关链接

- [C# 6 中的新语言功能](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [使用 C# 6 工作簿](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)
