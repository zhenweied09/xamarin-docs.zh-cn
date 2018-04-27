---
title: 添加 Windows 应用程序
ms.prod: xamarin
ms.assetid: ADF99B78-F1BC-48DF-9128-01B93C4411C1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0d2ef44896c9352776443c2fec318d40d27d7539
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="adding-a-windows-app"></a>添加 Windows 应用程序


1. **右击解决方案 > 添加 > 新建项目...** 并添加**空白应用 (Windows)**

 ![](tablet-images/add-wu.png "添加新项目对话框")

2. **右键单击项目 > 管理 NuGet 包...** 并添加**Xamarin.Forms**包。

3. **右键单击项目 > 添加 > 引用**和创建共享 Xamarin.Forms 应用程序项目的项目引用。

  ![](tablet-images/addref.png "引用管理器对话框")

4. 编辑**App.xaml.cs**包括`Init()`方法调用置于`OnLaunched`围绕行 65 方法

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5。 编辑**MainPage.xaml** -更改根元素`<Page`到`<forms:WindowsPage`*和*定义`xmlns:forms`，它使用：

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPage>
```


 6。 编辑**MainPage.xaml.cs**删除`: Page`继承的类名的说明符。

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 7。 仍在**MainPage.xaml.cs**，添加`LoadApplication`调用`MainPage`（围绕第 28 行） 的构造函数以启动 Xamarin.Forms 应用：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . 双击**Package.appxmanifest**设置通常是需要这些功能：

  功能集：

  * Internet（客户端）
  * 位置

9。 最后，添加任何本地资源 （如。 图像文件） 所需指定现有平台项目中。

