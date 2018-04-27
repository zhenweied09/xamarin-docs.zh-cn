---
title: 添加 Windows Phone 应用
ms.prod: xamarin
ms.assetid: B598FA9D-6818-4CC9-8191-838C156DB9DA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 55bd4bdcfde4c91ad5c9b94bef486207466e135d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="adding-a-windows-phone-app"></a>添加 Windows Phone 应用


首先，如果你使用 Xamarin.Forms PCL 模板，[更新该配置文件](~/xamarin-forms/platform/windows/installation/index.md)，然后按照下面的说明：

1. **右击解决方案 > 添加 > 新建项目...** 并添加**空白应用 (Windows Phone)**

  ![](phone-images/add-wp81.png "添加新项目对话框")

2. **右键单击新创建的项目 > 管理 NuGet 包...** 并添加**Xamarin.Forms**包。

3. **右键单击项目 > 添加 > 引用**和创建共享 Xamarin.Forms 应用程序项目的项目引用。

  ![](phone-images/addref.png "引用管理器对话框")

4. 编辑**App.xaml.cs**包括`Init()`方法调用，在`OnLaunched`行 67 解决方法：

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5。 编辑**MainPage.xaml** -更改根元素`<Page`到`<forms:WindowsPhonePage`*和*定义`xmlns:forms`，它使用：

```xaml
<forms:WindowsPhonePage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPhonePage>
```

 6。 编辑**MainPage.xaml.cs**删除`: PhonePage`继承的类名的说明符。

```csharp
public sealed partial class MainPage  // REMOVE ": PhonePage"
```

 7。 仍在**MainPage.xaml.cs**，添加`LoadApplication`调用`MainPage`（围绕第 28 行） 的构造函数以启动 Xamarin.Forms 应用：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . 双击**Package.appxmanifest**设置通常是需要这些功能：

  * Internet (客户端和服务器)

9。 最后，添加任何本地资源 （如。 图像文件） 所需指定现有平台项目中。

