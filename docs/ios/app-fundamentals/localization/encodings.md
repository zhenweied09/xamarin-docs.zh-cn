---
title: 国际化编码
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 60cfc5ac8238c853cc066afe2978f91ab9c0755e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="internationalization-encodings"></a>国际化编码

默认情况下，不是所有编码都包含 Xamarin.iOS 类库中。

若要减少应用程序的大小，Xamarin.iOS 不包括任何特定的编码，并且必须以指示 mtouch 包括包含所需的编码的支持的程序集。

这是通过用于 Mac 或 Visual Studio 在 Visual Studio 的 iOS 生成/高级窗格中选择的额外的编码：

 [![](encodings-images/00.png "选择的额外编码")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "选择的额外编码")](encodings-images/00a.png#lightbox)

你可以选择以下方法之一：

-  cjk： 中文、 日语和朝鲜语
-  mideast： 阿拉伯语、 希伯来语、 土耳其语和 Latin5。
-  其他： 西里尔文、 波罗、 越南语、 乌克兰和泰语
-  极少数： EBCDIC 编码和其他少见的代码页
-  西部： 拉丁语言，复活和西欧
-  全部


 <a name="cjk" />


## <a name="cjk"></a>cjk

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>mideast

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>其他

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>极少数

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>西部

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

