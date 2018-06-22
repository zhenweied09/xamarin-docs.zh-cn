---
title: 如何手动安装所需的 Xamarin.Android.Support 包 Android 支持库？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e760a87cbd1e0220ed5cf3a350d3539ffe29650e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765995"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手动安装所需的 Xamarin.Android.Support 包 Android 支持库？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Xamarin.Android.Support.v4 的示例步骤 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

（例如通过使用 NuGet 包管理器安装它） 中下载所需的 Xamarin.Android.Support NuGet 包。

使用`ildasm`若要检查的哪些版本**android_m2repository.zip** NuGet 包需要：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
示例输出：

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

下载**android\_m2repository.zip**从 Google 使用 URL 从返回**ildasm**。 或者，你可以检查哪个版本的_Android 支持存储库_当前已安装在 Android SDK 管理器：

!["显示 Android 支持存储库版本 32 安装 android SDK Manager"](install-android-support-library-images/sdk-extras.png)

如果版本与匹配所需的 NuGet 包，你不必下载任何新内容。 您可以改为重新进行压缩现有**m2repository**位于下的目录**extras\\android**中_SDK 路径_（如下所示的 Android 顶部SDK 管理器窗口）。

计算从返回的 URL 的 MD5 哈希**ildasm**。 设置要使用全大写字母和没有空格的结果字符串的格式。 例如，调整`$url`作为变量需要然后运行下面的两行代码 (基于[原始 C# 代码从 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) PowerShell 中：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
示例输出：

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

复制**android\_m2repository.zip**到 **%LOCALAPPDATA%\\Xamarin\\快速\\**文件夹。 重命名要使用从上一个 MD5 哈希计算步骤的 MD5 哈希的文件。 例如：

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

（可选）解压缩到文件 **%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\内容\\** (创建**内容\\m2repository**子目录)。 如果你跳过此步骤，然后使用库的第一个生成将会稍长因为它将需要完成此步骤。
子目录的版本号 (**23.4.0.0**在此示例中) 不是作为 NuGet 程序包版本完全相同。 你可以使用`ildasm`查找正确的版本号：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
示例输出：

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

（例如通过使用 NuGet 包管理器安装它） 中下载所需的 Xamarin.Android.Support NuGet 包。

双击_Xamarin.Android.Support.v4_下的程序集_引用_适用于 Mac 以在程序集浏览器中打开程序集的 Visual Studio 中的 Android 项目的一部分。 确保_语言_下拉列表设置为_C#_ 和选择顶级_Xamarin.Android.Support.v4_从程序集浏览器导航树中的程序集。 找到`SourceUrl`下之一属性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`属性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

下载**android\_m2repository.zip**从 Google 使用`SourceUrl`从返回**ildasm**。 或者，你可以检查哪个版本的_Android 支持存储库_当前已安装在 Android SDK 管理器：

!["显示 Android 支持存储库版本 32 安装 android SDK Manager"](install-android-support-library-images/sdk-extras.png)

如果版本与匹配所需的 NuGet 包，你不必下载任何新内容。 您可以改为重新进行压缩现有**m2repository**位于下的目录**extras/android**中_SDK 路径_（如下所示 Android SDK 管理器窗口的顶部）.

计算从返回的 URL 的 MD5 哈希**ildasm**。 设置要使用全大写字母和没有空格的结果字符串的格式。 例如，调整为所需的 URL 字符串，然后在运行以下命令**Terminal.app**命令提示符：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一个选项是使用`csharp`解释程序以运行[C# 使用同一代码本身的 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
为此，请调整`url`作为变量需要，然后在运行以下命令**Terminal.app**命令提示符：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
示例输出：

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

复制**android\_m2repository.zip**到 **$HOME/.local/share/Xamarin/zips/** 文件夹。 重命名要使用从上一个 MD5 哈希计算步骤的 MD5 哈希的文件。 例如：

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

（可选）将它解压缩到文件： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(创建**内容/m2repository**子目录)。 如果你跳过此步骤，然后使用库的第一个生成将会稍长因为它将需要完成此步骤。

子目录的版本号 (**23.4.0.0**在此示例中) 不是作为 NuGet 程序包版本完全相同。 作为 in **ildasm**前面步骤中，可以使用适用于 Mac 的 Visual Studio 中的程序集浏览器以查找正确的版本号。 查找`Version`下之一属性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`属性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>其他参考

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – Inaccurate"下载失败。 请下载 {0} 并将其放到 {1} 目录。" 和"请安装包:"{0}"在 SDK 安装程序中可用"与 Xamarin.Android.Support 包相关的错误消息

### <a name="next-steps"></a>后续步骤

本文档讨论自 2016 年 8 月起的当前行为。 本文档中所述的技术不为 Xamarin，稳定的测试套件的一部分，因此它可能会在将来中断。

有关进一步的帮助，请与我们联系，或如果此问题仍然存在即使利用的上述信息，请参阅[哪些支持选项均可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要的文件的新 bug。

