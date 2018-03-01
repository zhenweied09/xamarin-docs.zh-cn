---
title: "在更新 Nuget 包后缺少包错误"
ms.topic: article
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f330590132e4881484eeae3efafe570d991a509a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>在更新 Nuget 包后缺少包错误

此问题主要已报告在 Xamarin.Forms 示例应用程序解决方案，但使用 NuGet 包的任何项目可能发生此问题的可能性。 

如果在更新后在项目或解决方案的 Nuget 包，你将看到如引用旧的包版本号，出现错误：

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)

```

在此示例中*Xamarin.Forms.1.3.1.6296*是通过 Nuget 包更新已删除的旧版本号。

可能的原因是引用旧包的版本号将.csproj 文件中的 XML 元素必须手动添加或编辑，Nuget 将不删除或更新它们，如果它们已被手动添加/编辑，以便项目现在正在寻找已的包删除。 

若要解决此问题，手动编辑.csproj 文件，并删除所有引用旧版本号的元素。 

若要删除 （如果它们具有旧的包版本号） 的示例元素：

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />

```

