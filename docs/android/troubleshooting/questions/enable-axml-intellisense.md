---
title: "如何启用 Android.axml 文件中的 Intellisense？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: c68ec5a6d369e8673c8764e01943e513489775b0
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="how-do-i-enable-intellisense-in-android-axml-files"></a>如何启用 Android.axml 文件中的 Intellisense？

Visual Studio 中的 android Intellisense 需要两个 XML 架构文件适当地工作。 若要查找这些文件，请转到此文件夹：

`C:\Program Files (x86)\Xamarin Studio\AddIns\MonoDevelop.MonoDroid\schemas`*

* (以前： `C:\Program Files (x86)\MSBuild\Xamarin\Android`)

内，你将找到两个.xsd 文件：

1. android-layout-xml.xsd
2. schemas.android.com.apk.res.android.xsd

Visual Studio 会将所有 XML 架构保存在相应文件夹中：

`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Xml\Schemas`

你可以选择将这两个.xsd 文件移动到的位置更高版本，或只需只需添加这些架构在 Visual Studio 中。 然后，可以"添加"通过在 Visual Studio 这些架构**XML > 架构 > 添加**对话框。






