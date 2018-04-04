---
title: 应用程序本地化和字符串资源
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: cfb127500f919b61788087465700dfed213d5eb2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="application-localization-and-string-resources"></a>应用程序本地化和字符串资源

应用程序本地化是一种提供备用的资源，若要针对某个特定区域设置的行为。 例如，您可能为各种国家/地区，提供本地化的语言字符串或颜色或布局以匹配特定区域性可能会更改。 Android 将加载并在无源代码的任何更改的运行时时使用适用于设备的区域设置的资源。

例如下, 图显示了在三个不同的设备区域设置中，运行的相同应用程序，但在每个按钮上显示的文本是特定于每个设备设置为的区域设置：

[![三个不同的区域设置的示例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此示例中，文件的内容布局， **main.axml**如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

在上面的示例中，该按钮的字符串从资源加载的字符串中提供的资源 ID:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![这三种语言的资源字符串](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![这三种语言的资源字符串](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>本地化 Android 应用

读取[简介本地化](~/cross-platform/app-fundamentals/localization.md)有关提示和本地化的移动应用程序的指南。

[本地化 Android 应用](~/android/app-fundamentals/localization.md)指南包含有关如何将字符串翻译和本地化映像使用 Xamarin.Android 的更具体示例。



## <a name="related-links"></a>相关链接

- [本地化 Android 应用](~/android/app-fundamentals/localization.md)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
