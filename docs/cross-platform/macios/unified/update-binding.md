---
title: 迁移到统一的 API 绑定
description: 本文介绍如何更新现有 Xamarin 绑定项目以支持统一 Api Xamarin.IOS 和 Xamarin.Mac 应用程序所需的步骤。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57f27bf0d3aaa2a7ba14f23481a8f2bb2d87f2
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="migrating-a-binding-to-the-unified-api"></a>迁移到统一的 API 绑定

_本文介绍如何更新现有 Xamarin 绑定项目以支持统一 Api Xamarin.IOS 和 Xamarin.Mac 应用程序所需的步骤。_

## <a name="overview"></a>概述

从 2015 年 2 月 1 日，Apple 要求所有新提交到 iTunes 和 Mac 应用商店，必须是 64 位应用程序。 因此，任何新的 Xamarin.iOS 或 Xamarin.Mac 应用程序将需要使用新的统一 API 而不现有的经典 MonoTouch 和 MonoMac Api 以支持 64 位。

此外，任何 Xamarin 绑定项目还必须支持新的统一的 Api，要包括在一个 64 位 Xamarin.iOS 或 Xamarin.Mac 项目。 本文将介绍更新现有绑定项目以使用统一 API 所需的步骤。

## <a name="requirements"></a>要求

以下是所需完成这篇文章中提供的步骤：

 -  **Visual Studio for Mac** -最新版本的适用于 Mac 的 Visual Studio 安装并配置开发计算机上。
 -  **Apple Mac** -的 Apple mac 需要才能生成绑定项目类型提供的 iOS 和 mac。

在 Windows 计算机上的 Visual studio 中不支持绑定项目。

## <a name="modify-the-using-statements"></a>修改的 Using 语句

统一的 Api 可以比以往更容易地 Mac 和 iOS 以及从而可以支持具有相同的 32 和 64 位应用程序二进制间共享代码。 通过拖放_MonoMac_和_MonoTouch_前缀从命名空间，更简单共享实现跨 Xamarin.Mac 和 Xamarin.iOS 应用程序项目。

因此，我们将需要修改任何我们绑定协定 (和其他`.cs`我们绑定项目中的文件) 以删除_MonoMac_和_MonoTouch_前缀从我们`using`语句。

例如，假定有以下绑定协定中 using 语句：

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我们将剥离`MonoTouch`前缀结果如下：

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同样，我们将需要执行此操作对于任何`.cs`我们绑定项目文件中的。 进行此更改到位后下, 一步是更新我们的绑定项目以使用新的本机数据类型。

有关统一 API 的详细信息，请参阅[统一 API](~/cross-platform/macios/unified/index.md)文档。 有关更多背景支持 32 和 64 位应用程序和有关框架的信息，请参阅[32 位和 64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档。

## <a name="update-to-native-data-types"></a>更新本机数据类型

Objective C 将映射`NSInteger`数据类型设置为`int32_t`在 32 位系统上并且为`int64_t`在 64 位系统上。 若要匹配此行为，新的统一 API，将替换以前使用的`int`(.NET 中其定义为始终`System.Int32`) 到新的数据类型： `System.nint`。

以及新`nint`数据类型，统一 API 引入了`nuint`和`nfloat`类型，映射到`NSUInteger`和`CGFloat`以及类型。

鉴于上述，我们需要查看我们的 API 并确保的任何实例`NSInteger`，`NSUInteger`和`CGFloat`我们以前映射到`int`，`uint`和`float`获取更新到新`nint`， `nuint`和`nfloat`类型。

例如，给定的 OBJECTIVE-C 的方法定义：

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

如果以前的绑定协定具有以下定义：

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

我们将更新要的新绑定：

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
如果我们要映射到较新版本第三方库比什么我们具有最初链接到，我们需要查看`.h`库和查看是否有任何标头文件正在退出，显式调用`int`， `int32_t`， `unsigned int`，`uint32_t`或`float`都已升级为`NSInteger`，`NSUInteger`或`CGFloat`。 如果是这样，相同修改到`nint`，`nuint`和`nfloat`类型将需要对其映射以及。

若要了解有关这些数据类型更改的详细信息，请参阅[本机类型](~/cross-platform/macios/nativetypes.md)文档。

## <a name="update-the-coregraphics-types"></a>更新 CoreGraphics 类型

与使用的点、 大小和矩形数据类型`CoreGraphics`使用 32 位或 64 位具体取决于设备运行。 Xamarin 最初绑定的 iOS 和 Mac Api 时我们使用碰巧匹配中的数据类型的现有数据结构`System.Drawing`(`RectangleF`例如)。

支持 64 位和新的本机数据类型的要求，因此以下调整将需要对现有代码进行调用时`CoreGraphic`方法：

- **CGRect** -使用`CGRect`而不是`RectangleF`定义浮点点时的矩形区域。
- **CGSize** -使用`CGSize`而不是`SizeF`定义浮点点大小 （宽度和高度） 时。
- **CGPoint** -使用`CGPoint`而不是`PointF`定义浮点点位置 （X 和 Y 坐标） 时。

鉴于上述，我们将需要查看我们的 API 并确保的任何实例`CGRect`，`CGSize`或`CGPoint`，以前绑定到`RectangleF`，`SizeF`或`PointF`更改为本机类型`CGRect`，`CGSize`或`CGPoint`直接。

例如，给定一个 Objective C 初始值设定项：

```csharp
- (id)initWithFrame:(CGRect)frame;
```

如果我们以前的绑定包含以下代码：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

我们将更新到该代码：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

使用的所有代码更改现在，我们都需要修改我们绑定项目或使文件针对统一 Api 进行绑定。

## <a name="modify-the-binding-project"></a>修改绑定项目

作为更新我们的绑定项目以使用统一的 Api 的最后一步，我们需要修改`MakeFile`我们用来生成项目或 Xamarin 项目类型 （如果我们要将绑定从 Visual Studio 中适用于 Mac） 并指示_btouch_针对而不是经典的统一 Api 进行绑定。


### <a name="updating-a-makefile"></a>更新生成文件

如果我们将使用生成文件来生成我们的项目中绑定到 Xamarin。DLL，我们将需要包括`--new-style`命令行选项和调用`btouch-native`而不是`btouch`。

因此假定有以下`MakeFile`:

```csharp
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```

我们需要从调用切换`btouch`到`btouch-native`，因此我们将调整我们宏的定义，如下所示：

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

我们将更新对的调用`btouch`并添加`--new-style`选项，如下所示：

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

我们现在可以执行我们`MakeFile`照常来生成我们的 API 的新的 64 位版本。

### <a name="updating-a-binding-project-type"></a>更新绑定项目类型

如果我们将使用 Visual Studio for Mac 绑定项目模板来生成我们的 API，我们将需要更新到最新的统一 API 版本的绑定项目模板。 若要这样做最简单方法是重新开始的所有现有的代码和设置一个新的统一的 API 绑定项目并复制。

请执行以下操作：

1. 启动 Visual Studio for mac。
2. 选择**文件** > **新** > **解决方案...**
3. 在新的解决方案对话框中，选择**iOS** > **统一 API** > **iOS 绑定项目**: 

    [![](update-binding-images/image01new.png "在新的解决方案对话框中，选择 iOS / 统一 API / iOS 绑定项目")](update-binding-images/image01new.png#lightbox)
4. 在配置你的新项目对话框中输入**名称**新绑定项目和单击**确定**按钮。
5. 包括想要创建绑定的 Objective C 库的 64 位版本。
6. 从现有 32 位经典 API 绑定项目复制对源代码 (如`ApiDefinition.cs`和`StructsAndEnums.cs`文件)。
7. 对源代码文件进行上述设定的更改。

所有这些更改后，你可以生成新的 64 位版本的 api，就像 32 位版本。

## <a name="summary"></a>总结

这篇文章中我们已经演示了对现有的 Xamarin 绑定项目以支持新的统一的 Api 和 64 位设备所需要的更改并需要的步骤来生成 API 的新的 64 位兼容版本。



## <a name="related-links"></a>相关链接

- [Mac 和 iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)
- [升级现有的 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
