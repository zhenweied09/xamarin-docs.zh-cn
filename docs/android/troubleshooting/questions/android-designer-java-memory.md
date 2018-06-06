---
title: Android 设计器中调整 Java 内存参数
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 691be280b80e379863cc09d0f1bba0ff5882cf21
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732916"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Android 设计器中调整 Java 内存参数

在启动时使用的默认内存参数`java`处理 Android 设计器可能与某些系统配置不兼容。

开始使用 Xamarin Studio 5.7.2.7 （和更高版本、 Visual Studio for Mac） 和 Visual Studio Tools for Xamarin 3.9.344，可以根据每个项目中自定义这些设置。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 设计器属性和相应的 Java 选项

下面的属性名称对应于指示 java[命令行选项](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中打开解决方案。

2.  在解决方案资源管理器中选择一个地的每个 Android 项目，然后单击[显示所有文件](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx)两次上每个项目。 你可以跳过不包含任何项目的项目`.axml`布局文件。 此步骤将确保每个项目目录包含`.csproj.user`文件。

3.  退出 Visual Studio。

4.  找到`.csproj.user`为每个步骤 2 中的项目文件。

5.  编辑每个`.csproj.user`在文本编辑器中的文件。

6.  添加新的 Android 设计器的内存属性内的任意或全部`<PropertyGroup>`元素。 你可以使用现有`<PropertyGroup>`或创建一个新。 下面是一个完整示例`.csproj.user`文件都包括所有 3 属性设置为其默认值：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  保存并关闭所有更新后的`.csproj.user`文件。

8.  重新启动 Visual Studio 并重新打开你的解决方案。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  打开你在 Visual Studio for Mac，以确保解决方案目录中的解决方案包含`.userprefs`文件。

2.  退出 Visual Studio for mac。

3.  找到`.userprefs`解决方案目录中的文件。

4.  编辑`.userprefs`在文本编辑器中的文件。

5.  查找具有以下格式的现有 XML 元素。 此元素名称的最后一部分将与你的项目的名称匹配： 在此示例中的"AndroidApplication1":

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  如果`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`元素不存在，则在封闭的任何位置创建它`<Properties>`元素。 请确保将"AndroidApplication1"替换为你项目的名称。

7.  添加任何或所有新的 Android 设计器的内存属性作为元素的属性。 下面是一个完整示例`.userprefs`文件都包括所有 3 属性设置为其默认值：

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  重复步骤 5-7 用于每个 Android 项目中包含任何的解决方案`.axml`布局文件。 (即，添加一个`<MonoDevelop.Ide.ItemProperties.ProjectName>`每个项目的元素。)

9.  保存并关闭`.userprefs`文件。

10. 适用于 Mac 重新启动 Visual Studio 并重新打开你的解决方案。

-----

