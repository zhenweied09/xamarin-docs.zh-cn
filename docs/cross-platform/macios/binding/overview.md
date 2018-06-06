---
title: Objective C 绑定的概述
description: 本文档概述了创建 C# Objective C 代码，包括命令行绑定、 绑定项目和目标 Sharpie 绑定的不同方式。 它还介绍了绑定的工作原理。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.openlocfilehash: f9f981a9024ad9b1f780efbadeeb7e1f1636a8ae
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781739"
---
# <a name="overview-of-objective-c-bindings"></a>Objective C 绑定的概述

_绑定过程的工作原理的详细信息_

绑定适用于 Xamarin 的 Objective C 库采用三个步骤：

1. 编写 C#"API 定义"来描述如何本机 API 公开在.NET 和如何映射到基础目标 c。 这是使用标准 C# 构造如`interface`和各种绑定**属性**(请参阅此[简单示例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API))。

2. 编写完后"API 定义"在 C# 中，你将其编译为生成的"绑定"程序集。 可以进行此操作[**命令行**](#commandline)或使用[**绑定项目**](#bindingproject) Visual Studio 中用于 Mac 或 Visual Studio。

3. 为 Xamarin 应用程序项目，然后添加该"绑定"程序集，因此可以访问使用你定义的 API 的本机功能。
  绑定项目是完全独立于应用程序项目。

**注意：** 使用部门的协助可以自动化步骤 1 [**目标 Sharpie**](#objectivesharpie)。 它会检查 OBJECTIVE-C 的 API 并生成建议 C#"API 定义"。 你可以自定义目标 Sharpie 创建的文件并将它们绑定项目中 （或者在命令行上） 来创建绑定程序集。 目标 Sharpie 不会创建单独的绑定，它是只是更大的过程的一个可选部分。

你还可以读取的更多技术细节[原理](#howitworks)，这将帮助你编写你的绑定。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令行绑定

你可以使用`btouch-native`xamarin.ios (或`bmac-native`如果你使用 Xamarin.Mac) 来直接生成绑定。 它传递你已手动创建的 C# API 定义的工作原理 （或使用目标 Sharpie） 到命令行工具 (`btouch-native`适用于 iOS 或`bmac-native`适用于 Mac)。


用于调用这些工具的常规语法是：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上面的命令生成的文件将`cocos2d.dll`在当前目录中，并且它将包含你可以在项目中使用的完全绑定的库。 这是适用于 Mac 的 Visual Studio 用来创建你的绑定，如果你使用的绑定项目的工具 (描述[下面](#bindingproject))。


<a name="bindingproject" />

## <a name="binding-project"></a>绑定项目

绑定项目可以用于 Mac 或 Visual Studio （Visual Studio 仅支持 iOS 绑定） 在 Visual Studio 中创建，并使其更轻松地编辑和生成 API 定义 （而不是使用命令行） 的绑定。

遵循此[入门指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)若要了解如何创建和使用的绑定项目以生成绑定。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>目标 Sharpie

目标 Sharpie 是另一个单独的命令行工具，帮助创建绑定的初始阶段。 它不会单独创建一个绑定，而是它可以自动化的生成目标本机库的 API 定义的初始步骤。

读取[目标 Sharpie 文档](~/cross-platform/macios/binding/objective-sharpie/index.md)若要了解如何将本机库、 本机框架和 CocoaPods 解析到可以在绑定中内置的 API 定义。

<a name="howitworks" />

## <a name="how-binding-works"></a>绑定的工作原理

可以使用[[注册]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)属性， [[导出]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)属性，和[手动 OBJECTIVE-C 的选择器调用](~/ios/internals/objective-c-selectors.md)在一起，以手动绑定新 （以前未绑定） OBJECTIVE-C 的类型。

首先，找到你想要将绑定的类型。 有关讨论目的 （和简单起见），我们将绑定[NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html)类型 (其中已绑定中[Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); 下面的实现是只需例如目的)。

其次，我们需要创建的 C# 类型。 我们可能需要将该的命名空间; 中由于 Objective C 不支持命名空间，我们将需要使用`[Register]`特性更改 Xamarin.iOS 将向 Objective C 运行时注册的类型名称。 C# 类型还必须继承自[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三列，请查看 OBJECTIVE-C 的文档和创建[ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/)每个你想要使用的选择器的实例。 将这些类体内：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四步，你的类型将需要提供构造函数。 你*必须*链接你对基类构造函数的构造函数调用。 `[Export]`属性允许 Objective C 代码调用具有指定的选择器名称的构造函数：

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

第五步： 提供在步骤 3 中声明选择器的每个方法。 这些将使用`objc_msgSend()`来调用本机对象上的选择器。 请注意，使用[Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr))要转换`IntPtr`到适当的类型化`NSObject`（sub） 类型。 如果你想要从 Objective C 的代码，该成员可进行调用的方法*必须*是**虚拟**。

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

将所有集中到一起：

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```

