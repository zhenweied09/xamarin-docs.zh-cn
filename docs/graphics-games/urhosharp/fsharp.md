---
title: 'F # 的编程 UrhoSharp'
description: '如何创建使用 F # 的 Visual Studio 中的 Mac 的简单 UrhoSharp 应用'
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 1fff90056f39660695c1e6d9ed307fad575b5127
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="programming-urhosharp-with-f"></a>F # 的编程 UrhoSharp

_如何创建使用 F # 的 Visual Studio 中的 Mac 的简单 UrhoSharp 应用_

UrhoSharp 可以使用 F # 使用相同的库和 C# 程序员使用的概念进行编程。 [使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文章概述了 UrhoSharp 引擎，应在本文之前读取。

在 c + + 环境中产生的许多库，如许多 UrhoSharp 函数返回布尔值或指示成功或失败的整数。 应使用`|> ignore`若要忽略这些值。

[示例程序](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)UrhoSharp 从 F # 中为"Hello World"。

## <a name="creating-an-empty-project"></a>创建一个空项目

UrhoSharp 没有 F # 模板，但你可以使用任一启动可用，因此，若要创建你自己 UrhoSharp 项目[示例](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)或请按照下列步骤：

1. 从适用于 Mac 的 Visual Studio，创建一个新**解决方案**。 选择**iOS > 应用程序 > 单视图应用**和选择**F #**为所实现的语言。 
1. 删除**Main.storyboard**文件。 打开**Info.plist**文件并在**iPhone / iPod 部署信息**窗格中，删除`Main`字符串中**主界面**下拉列表。
1. 删除**ViewController.fs**以及文件。

## <a name="building-hello-world-in-urho"></a>构建中 Urho 的 Hello World

现在你就可以开始定义你的游戏的类。 至少，你将需要定义的一个子类`Urho.Application`，并重写其`Start`方法。 若要创建此文件，右键单击你的 F # 项目中，选择**添加新的文件...**并将一个空的 F # 类添加到你的项目。 新文件将添加到你的项目中的文件列表的末尾，但你必须将其拖动以使其显示*之前*中使用**AppDelegate.fs**。

1. 添加对 Urho NuGet 包的引用。
1. 从现有 Urho 项目中，将复制 （大） 的目录**出现 CoreData /**和**数据 /**到你的项目的**资源 /**目录。 在 F # 项目中，右键单击**资源**文件夹，然后使用**添加 / 添加现有文件夹**所有这些文件添加到你的项目。

项目结构现在应如下所示：

![](fsharp-images/solutionpane.png "项目结构应现在如下所示")

将你新创建的类定义的子类型为`Urho.Application`，并重写其`Start`方法：

```csharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

此代码是非常简单。 它使用`Urho.Gui.Text`类来显示特定字体和颜色大小的中心对齐字符串。 

此代码可以运行之前，不过，必须初始化 UrhoSharp。 

打开 AppDelegate.fs 文件并修改`FinishedLaunching`方法，如下所示：

```csharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

`ApplicationOptions.Default`提供在横向模式应用程序的默认选项。 将这些信息传递`ApplicationOptions`为的默认构造函数你`Application`子类 (请注意，当你定义`HelloWorld`类，一行`inherit Application(o)`调用基类构造函数)。 

`Run`方法你`Application`启动程序。 它将定义为返回`int`，可以被输送到`ignore`。 

生成的程序应如下所示：

![](fsharp-images/helloworldfsharp.png "生成的程序应如下所示")








## <a name="related-links"></a>相关链接

- [浏览 GitHub （示例） 上](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
