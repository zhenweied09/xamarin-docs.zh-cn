---
title: 使用本机库
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 0fa66f3a16047c18af19cb7257c778b498bc0c9b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774806"
---
# <a name="using-native-libraries"></a>使用本机库

Xamarin.Android 支持通过标准的 PInvoke 机制的本机库使用。 你还可以捆绑其他本机库，后者不是你.apk 到操作系统的一部分。

若要部署具有 Xamarin.Android 应用程序的本机库，将二进制库添加到项目并设置其**生成操作**到**AndroidNativeLibrary**。

若要部署具有 Xamarin.Android 类库项目的本机库，将二进制库添加到项目并设置其**生成操作**到**EmbeddedNativeLibrary**。

请注意，由于 Android 支持多个应用程序二进制接口 (ABIs)，则 Xamarin.Android 必须知道哪些 ABI 本机库为生成的。
可以通过两种方法完成：

1.  路径"监听"
1.  通过使用`AndroidNativeLibrary/Abi`项目文件中的元素


通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果你添加`lib/armeabi/libfoo.so`到项目中，然后 ABI 将"探查"作为`armeabi`。

或者，你可以编辑项目文件显式指定 ABI 使用：

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

有关使用本机库的详细信息，请参阅[与本机库的互操作](http://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio-2015"></a>使用 Visual Studio 2015 调试本机代码

如果你使用*Visual Studio 2015*，无需修改项目文件 （如上面所述）。
可以生成和调试 c + + 在 Xamarin.Android 解决方案，只需通过添加对 c + + 的项目引用**动态共享库 (Android)** 项目。

Visual Studio c + + 开发人员可以查看[SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)示例尝试调试 c + + 中使用 Xamarin; 的 Visual Studio 2015，请参考我们[博客文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)有关详细信息。



## <a name="related-links"></a>相关链接

- [SanAngeles_NativeDebug （示例）](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
