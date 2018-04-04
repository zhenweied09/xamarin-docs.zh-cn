---
title: 使用 Visual Basic.NET Xamarin.Forms
description: 可以修改 Xamarin.Forms PCL 项目模板来有效地允许你构建跨平台移动应用程序使用 VB.NET 主要的程序集使用 Visual Basic。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e26d330d62e6ffdfdb3f9b8eab59e57a6377a86c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-using-visual-basicnet"></a>使用 Visual Basic.NET Xamarin.Forms

Xamarin 不直接支持 Visual Basic-按照此页后，可以创建 C# Xamarin.Forms PCL 解决方案，然后使用 Visual Basic 替换常见代码 PCL 项目上的说明。

[![](xamarin-forms-images/hero-sml.png "创建 Xamarin.Forms PCL 解决方案，然后使用 Visual Basic 替换常见代码 PCL 项目中")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 使用 Visual Basic 程序到，必须在 Windows 上使用 Visual Studio。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>使用 Visual Basic 演练 Xamarin.Forms

按照这些步骤创建一个简单的 Xamarin.Forms 项目使用 Visual Basic:

1. 创建一个新*Xamarin.Forms C#*使用可移植类库 (PCL) 的解决方案。
转到**文件 > 新建项目**并在**新项目**窗口导航到**已安装 > 模板 > Visual C# > 跨平台**然后选择**跨平台应用 （Xamarin.Forms 或本机） > Xamarin.Forms**。

2. 右击该解决方案和**添加 > 新建项目**。

3. 选择**Visual Basic > 类库 （可移植）**项目类型：

   [![](xamarin-forms-images/add-vb-2-sml.png "添加新可移植类库项目")](xamarin-forms-images/add-vb-2.png#lightbox)

4. 选择的平台，如所示配置正确的 PCL 配置文件 （请务必包括 Xamarin.iOS 和 Xamarin.Android）：

   ![](xamarin-forms-images/add-vb-3-sml.png "选择以支持的平台")

5. Visual Basic 项目中右击并选择**属性**，然后将更改**默认命名空间**以匹配现有的 C# 项目：

   ![](xamarin-forms-images/add-vb-4s-sml.png "请确保 Visual Basic 根命名空间与匹配 Xamarin.Forms 应用")

6. 右键单击新的 Visual Basic 项目，然后选择**管理 Nuget 包**，然后安装**Xamarin.Forms**和关闭包管理器窗口。

   [![](xamarin-forms-images/add-vb-4-sml.png "窗体和关闭包管理器窗口")](xamarin-forms-images/add-vb-4.png#lightbox)

7. 重命名默认**Class1**文件*和*类到`App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "将默认 Class1 文件和类重命名为应用程序")](xamarin-forms-images/add-vb-5.png#lightbox)

8. 粘贴下面的代码插入**App.vb**文件，将成为 Xamarin.Forms 应用程序的起始点。 请记住包括`Imports Xamarin.Forms`并添加`Inherits Application`到类：

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. 现在，我们需要新的 Visual Basic 项目中指向的 iOS 和 Android 项目。
右键单击**引用**节点中的 iOS 和 Android 的项目以打开**引用管理器**。 取消刻度的 C# 可移植运行库和刻度 VB 可移植运行库 （不要忘记，执行此操作适用于 iOS 和 Android 项目）。

   [![](xamarin-forms-images/add-vb-8-sml.png "删除旧的项目引用中，添加 Visual Basic 参考")](xamarin-forms-images/add-vb-8.png#lightbox)

10. 删除该 C# 可移植项目。 添加新**.vb**用于生成文件 out Xamarin.Forms 应用程序。 新模板`ContentPage`在 Visual Basic 中的 s 如下所示：

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Xamarin.Forms 中的 Visual Basic 的限制

如上所述[可移植 Visual Basic.NET 页](~/cross-platform/platform/visual-basic/index.md)，Xamarin 不支持 Visual Basic 语言。 这意味着在你可以在其中使用 Visual Basic 的一些限制：

 - 无法在 Visual Basic 中编写自定义呈现器，它们必须是用 C# 编写的本机平台项目中。

 - 无法在 Visual Basic 中编写依赖服务实现，它们必须编写 C# 中的本机平台项目中。

 - XAML 页不能包含在 Visual Basic 项目-隐藏代码生成器只能生成 C#。 很可能要包含在一个单独的、 引用，C# 可移植类库中的 XAML 数据绑定用于填充通过 Visual Basic 模型的 XAML 文件 (此示例包含在[示例](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages))。

 - Xamarin 不支持 Visual Basic.NET 语言。

## <a name="related-links"></a>相关链接

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework (Microsoft) 的跨平台开发](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
