---
title: 安装 Windows 项目
description: 将新的 Windows 项目添加到现有 Xamarin.Forms 解决方案
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 071239abc71a41798a240128f8339687026296f9
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="setup-windows-projects"></a>安装 Windows 项目

_将新的 Windows 项目添加到现有 Xamarin.Forms 解决方案_

较旧的 Xamarin.Forms 解决方案 （或在 macOS 上创建的那些类型），它们将通用 Windows 平台 (UWP) 应用程序项目没有。 因此，你将需要手动添加 UWP 项目生成 Windows 10 (UWP) 应用程序。

<a name="pcl" />

## <a name="update-the-pcl-profile"></a>更新 PCL 配置文件

如果你现有的 Xamarin.Forms 应用程序使用可移植类库 (PCL) 模板，则必须更新其配置文件。

1. **右键单击 > 属性**（你的现有设置可能不同）

  ![](images/targets.png "PCL 目标")

2. 单击**更改...** 按钮

3. 确保**Windows 8**和**Windows Phone 8.1**会选择的选项 (和**Windows Phone Silveright**是*取消选择*):

  ![](images/pcl.png "PCL 目标选项")

4. 按**确定**并保存所做的更改。

这相当于**配置文件 111**如果你要在 Visual Studio 中配置你 PCL，适用于 Mac 使用下拉列表。

  ![](images/pcl-xs.png "PCL 配置文件 111")

## <a name="add-a-universal-windows-platform-app"></a>添加通用 Windows 平台应用

你应运行**Visual Studio 2017**上**Windows 10**生成 UWP 应用。 有关通用 Windows 平台的详细信息，请参阅[简介通用 Windows 平台](/windows/uwp/get-started/universal-application-platform-guide/)。

UWP 是可用在 Xamarin.Forms 2.1 及更高版本，并且 Xamarin.Forms.Maps 支持 Xamarin.Forms 2.2 及更高版本。

检查<a href="#troubleshooting">疑难解答</a>很有帮助的提示和技巧的部分。

按照这些说明来添加将在 Windows 10 手机、 平板电脑和台式计算机运行的 UWP 应用：

 1 . 右键单击解决方案并选择**添加 > 新建项目...** 并添加**空白应用 (通用 Windows)** 项目：

  ![](universal-images/add-wu.png "添加新项目对话框")

 2 . 在**新的通用 Windows 平台项目**对话框中，选择应用程序将在运行的 Windows 10 的最小值和目标版本：

  ![](universal-images/target-version.png "新的通用 Windows 平台项目对话框")

 3。 右键单击 UWP 项目并选择**管理 NuGet 包...** 并添加**Xamarin.Forms**包。 确保解决方案中的其他项目也将更新为相同版本的 Xamarin.Forms 包。

 4 . 请确保将生成新的 UWP 项目**生成 > Configuration Manager**窗口 （可能不会发生了此默认情况下）。 刻度**生成**和**部署**通用项目框：

  [![](universal-images/configuration-sml.png "配置管理器窗口")](universal-images/configuration.png#lightbox "配置管理器窗口")

 5。 右键单击项目并选择**添加 > 引用**并创建 Xamarin.Forms 应用程序项目中 （PCL、.NET 标准或共享项目） 的引用。

  ![](universal-images/addref-sml.png "引用管理器对话框")

 6。 在 UWP 项目中，编辑**App.xaml.cs**包括`Init`方法调用置于`OnLaunched`行 52 解决方法：

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7。 在 UWP 项目中，编辑**MainPage.xaml**通过删除`Grid`中包含`Page`元素。

 8 . 在**MainPage.xaml**，添加新`xmlns`条目`Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9。 在**MainPage.xaml**，更改根`<Page`元素`<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10。 在 UWP 项目中，编辑**MainPage.xaml.cs**删除`: Page`继承的类名的说明符 (因为它现在将从继承`WindowsPage`由于上一步中所做的更改):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11。 在**MainPage.xaml.cs**，添加`LoadApplication`调用`MainPage`构造函数以启动 Xamarin.Forms 应用程序：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12。 添加任何本地资源 （如。 图像文件） 所需指定现有平台项目中。

## <a name="troubleshooting"></a>疑难解答

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"针对调用异常"时使用"使用.NET 本机工具链编译"

如果你的 UWP 应用正在引用多个程序集 （例如第三方控件库，或您的应用程序本身拆分成多个库），Xamarin.Forms 可能无法从这些程序集 （如自定义呈现器） 加载对象。

这可能是使用时**使用.NET Native 工具链编译**是适用于 UWP 应用中的选项**属性 > 生成 > 常规**项目的窗口。

可以通过使用的 UWP 特定重载来修复此问题`Forms.Init`调用**App.xaml.cs**中下面的代码所示 (应替换`ClassInOtherAssembly`与实际类代码引用):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

添加对应用引用每个程序集的引用。

#### <a name="dependency-services-and-net-native-compilation"></a>依赖关系服务和.NET 本机编译

使用.NET 本机编译的发布版本可能无法解决的主应用程序可执行文件 （如单独的项目或库） 的外部定义的依赖关系服务。

使用`DependencyService.Register<T>()`方法手动注册依赖关系服务类。 根据上面的示例中，添加如下的 register 方法：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
