---
title: "环境"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ef5cfa2a9eee0d5d01922e5b7b3f89a209396c56
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="environment"></a>环境

*执行环境*是一系列影响程序执行的环境变量。 可以在项目的属性中暂时设置环境变量，或通过将 mtouch 打包工具指定为额外参数来永久设置环境变量。

## <a name="temporary-environment-variables"></a>临时环境变量

临时环境变量在“运行”>“常规”部分中的项目“属性”/“选项”窗口中设置。 这些环境变量仅在使用 Visual Studio for Mac 启动应用程序时生效，如果通过在其上点击它手动启动应用，则不会设置环境变量。

## <a name="permanent-environment-variables"></a>永久环境变量

通过将 mtouch 打包工具指定为额外参数，设置永久环境变量。 这些环境变量被编译为可执行文件，即使不从 Xamarin Studio 中启动应用也会设置。

## <a name="example"></a>示例

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

