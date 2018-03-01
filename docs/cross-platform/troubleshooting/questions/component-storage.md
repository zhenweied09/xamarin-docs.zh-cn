---
title: "组件保存在我的计算机的位置？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 9549363d7aeb5ff7a5cfa79eb38443aaa80b7019
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>组件保存在我的计算机的位置？

每当 Xamarin 组件安装到应用程序项目中时，获取将其放在两个位置：

1. 在根级别的解决方案文件夹的组件文件夹中。 如果从解决方案中的所有项目中删除该组件，它将获取从以及此文件夹。

2. 副本也存储在以下位置：
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

因此若要从你的系统完全删除某一组件，它从你的项目/解决方案和删除上面的缓存文件夹。
