---
title: 如何进行自动 Android NUnit 测试项目？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763766"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何进行自动 Android NUnit 测试项目？

> [!NOTE]
> 本指南说明如何自动执行一个 Android NUnit 测试项目，而不是 Xamarin.UITest 项目。 找不到 Xamarin.UITest 指南[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

当你创建**单元测试应用 (Android)** Visual Studio 中的项目 (或**Android 单元测试**适用于 Mac 的 Visual Studio 项目中)，此项目将不会自动运行测试的默认值。
若要在目标设备上运行 NUnit 测试，可以创建[Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/)子类，它开始通过使用以下命令： 

```shell
adb shell am instrument 
```

以下步骤介绍了此过程：

1.  创建新的文件称为**TestInstrumentation.cs**: 

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
    在此文件中， [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (从**Xamarin.Android.NUnitLite.dll**) 进行了子类化来创建`TestInstrumentation`。

2.  实现[TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数和[AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29)方法。 `AddTests`方法控件实际执行哪些测试。

3.  修改`.csproj`文件以添加**TestInstrumentation.cs**。 例如：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

3.  使用以下命令来运行单元测试。 替换`PACKAGE_NAME`替换为应用程序的包名称 (在应用程序的找不到包名称`/manifest/@package`属性位于**AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  或者，您可以修改`.csproj`文件以添加`RunTests`MSBuild 目标。 这使得可以调用如下所示的命令使用单元测试：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (请注意，使用此新的目标不是必需的; 早期版本`adb`命令可用于而不是`msbuild`。)

有关使用`adb shell am instrument`命令来运行单元测试，请参阅 Android 开发人员[使用 adb 的位置运行测试](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)主题。


> [!NOTE]
> 与[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)释放，默认包名称 Android 可调用包装器将根据正在导出的类型的程序集限定名称的 MD5SUM。 这样，相同的完全限定名称，以提供从两个不同的程序集，无法获取打包错误。 因此，请确保你使用`Name`属性`Instrumentation`属性生成的可读的 ACW/类名称。

_必须在使用 ACW 名称`adb`上述命令_。
重命名/重构 C# 类修改将因此需要`RunTests`命令，以便使用正确的 ACW 名称。

