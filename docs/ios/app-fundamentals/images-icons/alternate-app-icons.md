---
title: "备用的应用程序图标"
description: "本文介绍如何在 Xamarin.iOS 中使用备用的应用程序图标。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: ff24a1411a7ddf2ca78c7997f1dc37744013ece4
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="alternate-app-icons"></a>备用的应用程序图标

_本文介绍如何在 Xamarin.iOS 中使用备用的应用程序图标。_

Apple iOS 10.3，允许管理图标将其应用到添加了几项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用程序图标的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`应用程序具有一组备用的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定的备用图标的应用程序的图标。

![](alternate-app-icons-images/icons04.png "示例警报，如果应用程序也会更改其图标")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>向 Xamarin.iOS 项目添加备用图标

若要允许应用程序以切换到一个备用的图标，图标图像的集合将需要包含在 Xamarin.iOS 应用程序项目。 无法将这些映像添加到项目使用的典型`Assets.xcassets`方法，必须将它们添加到**资源**直接文件夹。

请执行以下操作：

1. 在文件夹中选择所需的图标图像、 全选和拖到**资源**文件夹中的**解决方案资源管理器**:

    ![](alternate-app-icons-images/icons00.png "从文件夹中选择图标图像")

2. 出现提示时，选择**复制**，**的所有选定文件使用相同的操作**单击**确定**按钮：

    ![](alternate-app-icons-images/icons02.png "将文件添加到文件夹对话框")

3. **资源**文件夹应如下所示完成：

    ![](alternate-app-icons-images/icons01.png "资源文件夹应如下所示")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>修改 Info.plist 文件

与所需的映像添加到**资源**文件夹， [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13)密钥将需要添加到项目的**Info.plist**文件。 此密钥将定义的新建图标和拆分组合的映像的名称。

请执行以下操作：

1. 在“解决方案资源管理器”中，双击“Info.plist”文件，将其打开进行编辑。
2. 切换到**源**视图。
3. 添加**捆绑图标**密钥并使**类型**设置为**字典**。
4. 添加`CFBundleAlternateIcons`项并将设置**类型**到**字典**。
5. 添加`AppIcons2`项并将设置**类型**到**字典**。 这将是新的备用的应用程序图标集的名称。
6. 添加`CFBundleIconFiles`项并将设置**类型**到**数组**
7. 添加到新的字符串`CFBundleIconFiles`省去扩展每个图标文件的数组和`@2x`， `@3x`，等后缀 (示例`100_icon`)。 对于每个文件组成备用图标集重复此步骤。
8. 添加`UIPrerenderedIcon`键，以`AppIcons2`字典，设置**类型**到**布尔**和值的**否**。
9. 保存对文件所做的更改。

生成**Info.plist**文件应如下所示完成：

![](alternate-app-icons-images/icons03.png "已完成的 Info.plist 文件")

或在文本编辑器中打开此如果类似：

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>管理应用程序的图标 

在使用 Xamarin.iOS 项目中包含图标的图像和**Info.plist**文件正确配置，开发人员可以使用许多新功能添加到 iOS 10.3 之一以控制应用程序的图标。

`SupportsAlternateIcons`属性`UIApplication`类允许开发人员可以查看应用程序是否支持备用图标。 例如:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber`属性`UIApplication`类允许开发人员在获取或设置当前的应用程序图标的徽章数 Springboard。 默认值为零 (0)。 例如:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName`属性`UIApplication`类允许开发人员可以获取当前所选备用的应用程序图标的名称，或者返回`null`如果应用程序使用主图标。 例如:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName`属性`UIApplication`类允许开发人员更改的应用程序图标。 传递的图标以选择的名称或`null`以返回到主图标。 例如:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

当运行该应用程序和用户选择一个备用的图标时，将显示类似于以下警报：

![](alternate-app-icons-images/icons04.png "示例警报，如果应用程序也会更改其图标")

如果用户切换回主图标时，将显示类似于以下警报：

![](alternate-app-icons-images/icons05.png "示例警报，如果应用程序更改为主图标")

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖向 Xamarin.iOS 项目添加备用的应用程序图标并在应用程序内部使用它们。



## <a name="related-links"></a>相关链接

- [iOSTenThree 示例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
