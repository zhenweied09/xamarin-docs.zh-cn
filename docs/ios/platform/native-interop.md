---
title: "引用本机库"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/28/2016
ms.openlocfilehash: 9299d2b37825298d3defa18a9f5137e11b29f6ce
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="referencing-native-libraries"></a>引用本机库

Xamarin.iOS 支持本机 C 库和 Objective C 库链接。 本文档讨论了如何将链接与你的 Xamarin.iOS 项目你本机 C 库。 有关执行相同的 Objective C 库的信息，请参阅我们[绑定 Objective C 类型](~/ios/platform/binding-objective-c/index.md)文档。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>生成通用本机库 （i386、 ARMv7 和 ARM64）

它通常是需要为每个 iOS 开发受支持的平台生成本机库 (i386 针对模拟器和 ARMv7/ARM64 自己的设备)。 如果你已为你的库具有 Xcode 项目，这是真的普通执行。

若要生成本机库的 i386 版本，请从终端运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

这将导致本机静态库下`MyProject.xcodeproj/build/Release-iphonesimulator/`。 复制 （或移动） 到某个地方安全供以后使用，为其指定唯一名称的库存档文件 (libMyLibrary.a) (如**libMyLibrary i386.a**)，以便它不能与你将同一个库的 arm64 和 armv7 版本冲突生成下一步。

若要生成本机库的 ARM64 版本，请运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

生成本机库将位于此时间`MyProject.xcodeproj/build/Release-iphoneos/`。 同样，复制 （或移动） 到一个安全位置，重命名为类似于此文件**libMyLibrary arm64.a** ，以便它不会冲突。

现在能够生成 ARMv7 版本的库：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

复制 （或移动） 生成的库文件到同一个位置移其他 2 个版本的库中，重命名为类似于**libMyLibrary armv7.a**。

若要使一个通用二进制，可以使用`lipo`工具如下所示：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

这将创建`libMyLibrary.a`这将是一个通用的 (fat) 库，其中将不适用于要用于所有的 iOS 开发目标。


### <a name="missing-required-architecture-i386"></a>缺少所需的体系结构 i386

如果您处于`does not implement methodSignatureForSelector`或`does not implement doesNotRecognizeSelector`在运行时输出时尝试可能使用 OBJECTIVE-C 的库，在 iOS 模拟器中，你的库中的消息不编译为 i386 体系结构 (请参阅[构建通用本机库](#building_native)上面一节)。

若要检查给定 library 所支持的体系结构，请在终端中使用以下命令：

```bash
lipo -info /full/path/to/libraryname.a
```

其中`/full/path/to/`是已使用的库的完整路径和`libraryname.a`存在问题的库的名称。

如果具有到库，你将需要编译和捆绑为 i386 体系结构，如果你想要测试你的应用在 iOS 模拟器上。

### <a name="linking-your-library"></a>链接你的库

你使用的任何第三方库需要与你的应用程序静态链接。 

如果你想要静态链接库"libMyLibrary.a"从 Internet 或使用 Xcode 生成获取，你需要做一些事情：

-  将库导入你的项目
-  配置 Xamarin.iOS 将库链接
-  从库中访问的方法。


到**将库导入你的项目**，选择项目从解决方案资源管理器和按**命令 + 选项 + a**。 导航到 libMyLibrary.a 并将其添加到项目。 出现提示时，告知 Visual Studio for Mac 或 Visual Studio，以将其复制到项目。 之后将其添加，查找 libFoo.a 项目中，右键单击它，并设置**生成操作**到**无**。

到**配置 Xamarin.iOS 将库链接**，你需要添加在最终可执行文件 （不在库自身，但最终的程序） 的项目选项上**iOS 生成**的额外自变量 （这些都是你项目的选项的一部分）"-gcc_flags"选项后跟带引号的字符串，其中包含所有额外的库所需的程序，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上面的示例将链接**libMyLibrary.a**

你可以使用`-gcc_flags`指定要传递给用于执行最终的链接可执行文件则 GCC 编译器的命令行任何的参数组。 例如，此命令行，也引用 CFNetwork framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果本机库中包含你还必须传递-cxx 标志中"额外的参数"以便 Xamarin.iOS 知道要使用正确的编译器的 c + + 代码。 C + + 前选项将如下所示：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>从 c&#35; 访问 C 方法

有两种类型的本机库可在 iOS 上：

-  共享库的操作系统的一部分。

-  该应用程序附带的静态库。


若要访问的那些之一中定义的方法，你可以使用[Mono 的 P/Invoke 功能](http://www.mono-project.com/Interop_with_Native_Libraries)它是你将在.NET 中，这是大致相同技术：

-  确定你想要调用的 C 函数
-  确定其签名
-  确定居住在哪个库
-  写入相应的 P/Invoke 声明


当你使用 P/Invoke 时需要指定了要链接的库路径。 使用 iOS 共享库，你可以硬编码路径，或可以使用在中定义了我们的方便常量时我们[常量类](https://developer.xamarin.com/api/type/Constants/)，这些常量应涵盖的 iOS 共享库。

例如，如果你想要调用从 Apple 的 UIKit 库具有此签名在 c: UIRectFrameUsingBlendMode 方法

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

P/Invoke 声明将如下所示：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants.UIKitLibrary 是仅作为定义的常量"/ System/Library/Frameworks/UIKit.framework/UIKit"，公开不同的名称在 C# 中，较短者时的入口点供我们 （可选） 指定的外部名称 (UIRectFramUsingBlendMode)RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>访问 C Dylibs

如果你具有需要使用 C Dylib Xamarin.iOS 应用程序中，没有的额外安装程序前，需要先调用位`DllImport`属性。

例如，如果我们有`Animal.dylib`与`Animal_Version`我们将在我们的应用程序中调用的方法，我们需要在尝试使用它之前通知 Xamarin.iOS 的库的位置。

若要执行此操作，编辑`Main.CS`文件并使其如下所示：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是已使用 Dylib 的完整路径。 使用此代码中的位置，我们可以然后将链接到`Animal_Version`方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>静态库

因为你仅可以在 iOS 上使用静态库，没有任何外部的共享的库，以链接，因此 DllImport 特性中的路径参数需要使用的特殊名称`__Internal`（请注意双下划线字符开头的名称） 而不是路径名称中。

这将强制 DllImport 要查找其符号的引用在当前程序中，而不是尝试从共享库加载它的方法。

