---
title: 如何进行自动 Android NUnit 测试项目？
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: acb213e8c73013bc9b2482afb45296c4e1f61ab5
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何进行自动 Android NUnit 测试项目？

> [!NOTE]
> 此指南介绍了如何设置 Android NUnit 测试项目，而不是 Xamarin.UITest 项目步骤。 找不到 Xamarin.UITest 指南[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

当在 Visual Studio 中创建 Android 的单元测试项目，用于 Mac 或 Visual Studio 中的单元测试应用 (Android) 时，默认情况下它将不自动运行测试。
若要在目标设备上运行 NUnit 测试，我们使用`Android.App.Instrumentation`子类，可以创建并使用执行`adb shell am instrument`命令。

首先，我们创建**TestInstrumentation.cs**文件，它创建的一个子类`Xamarin.Android.NUnitLite.TestSuiteInstrumentation`(中声明`Xamarin.Android.NUnitLite.dll`)。 `TestInstrumentation(IntPtr, JniHandleOwnership)`构造函数_必须_提供，和虚拟`AddTests()`必须重写方法。
`AddTests()` 实际执行的测试的控件。 此文件是很大程度上样本。

接下来，`.csproj`必须对其进行修改，以添加`TestInstrumentation.cs`。

（可选）`.csproj`可能对其进行修改，以添加`RunTests`MSBuild 目标，将允许调用时的单元测试：

```shell
msbuild /t:RunTests Project.csproj
```

使用新的目标不是必需的;可以改为使用相应的 adb 命令：

```shell
adb shell am instrument -w @PACKAGE_NAME@/app.tests.TestInstrumentation
```

替换`@PACKAGE\_NAME@`为相应; 它是存在于值**AndroidManifest.xml** `/manifest/@package`属性。


> [!NOTE]
> *重要*： 与[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)释放，默认包名称 Android 可调用包装器将根据正在导出的类型的程序集限定名称的 MD5SUM。 这样，相同的完全限定名称，以提供从两个不同的程序集，无法获取打包错误。 因此，请确保你使用\`名称\`属性\`检测\`属性生成的可读的 ACW/类名称。

_必须在使用 ACW 名称`adb`命令_。 重命名/重构 C# 类修改将因此需要`RunTests`命令，以便使用正确的 ACW 名称。

添加内容到.csproj 文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Include="TestInstrumentation.cs" />
    </ItemGroup>
    <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
        <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
    </Target>
</Project>
```

**TestInstruments.cs**:

```cs 
using System;
using System.Reflection;
 
using Android.App;
using Android.Content;
using Android.Runtime;
 
using Xamarin.Android.NUnitLite;
 
namespace App.Tests {
 
    [Instrumentation(Name="app.tests.TestInstrumentation")]
    public class TestInstrumentation : TestSuiteInstrumentation {
 
        public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
        {
        }
 
        protected override void AddTests ()
        {
            AddTest (Assembly.GetExecutingAssembly ());
        }
    }
}
```

