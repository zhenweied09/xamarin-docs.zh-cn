---
title: 映射应用程序
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2017
ms.openlocfilehash: b94c65c079b28fe042a42ec04357c11f3516d205
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="maps-application"></a>映射应用程序

使用在 Xamarin.Android 的地图的最简单方法是利用内置地图应用程序如下所示：

[![内置的 Google 映射应用的示例屏幕快照](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

当使用地图应用程序时，该映射不会应用程序的一部分。 相反，你的应用程序将启动映射应用程序，并加载外部的映射。 下一节讨论如何使用 Xamarin.Android 启动与上述的地图。


## <a name="creating-the-intent"></a>创建意图

使用图应用程序是使用相应的 URI 创建为打算、 将操作设置为 ActionView，并调用 StartActivity 方法一样轻松。 例如，下面的代码将启动以给定的纬度和经度中心映射应用程序：

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此代码是所有所需启动上面的屏幕快照中所示的映射。 除了指定纬度和经度，地图的 URI 方案支持几个其他选项。


## <a name="geo-uri-scheme"></a>异地 URI 方案

上面的代码中使用异地方案来创建一个 URI。 此 URI 方案支持几种格式，如下所示：

-   `geo:latitude,longitude` &ndash; 打开以 lat/lon 中心映射应用程序。 

-   `geo:latitude,longitude?z=zoom` &ndash; 打开应用程序在 lat/lon 居中和缩放后为指定级别的映射。 缩放级别范围可以从 1 到 23: 1 显示整个地球并 23 是最接近的缩放级别。

-   `geo:0,0?q=my+street+address` &ndash; 将打开到街道地址的位置地图应用程序。 

-   `geo:0,0?q=business+near+city` &ndash; 打开映射应用程序并显示带批注的搜索结果。 


执行查询 （即街道的地址或搜索条款） 的 uri 的版本使用 Google 的 geocoder 服务检索然后显示在地图的位置。 例如，URI`geo:0,0?q=coop+Cambridge`导致映射如下所示：

[![显示与搜索词的 Google 映射的示例屏幕截图](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



有关地域 URI 方案的详细信息，请参阅[在地图上显示位置](http://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道视图

除了异地方案中，Android 还支持从为打算加载街道视图。 从 Xamarin.Android 启动街道视图应用程序的示例所示：

[![街道视图的示例屏幕快照](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

若要启动的街道视图，只需使用`google.streetview`URI 方案中，如下面的代码中所示：

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

上面使用的 google.streetview URI 方案采用以下形式：

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如你所见，有几个参数支持，如下所示：

-   `lat` &ndash; 在街道视图中显示的位置的纬度。

-   `lng` &ndash; 在街道视图中显示的位置的经度。

-   `pitch` &ndash; 垂直向上街道视图全景，从中度 90 度是直接向下和-90 度的中心算起的角度。

-   `yaw` &ndash; 中心的视图的街道视图全景沿顺时针方向度量以从北部度为单位。

-   `zoom` &ndash; 缩放的街道视图全景乘数，其中 1.0 = 正常缩放，2.0 = 缩放的 2 x 3.0 = 缩放的 4 x，等等。

-   `mz` &ndash; 当从街道视图转到地图应用程序时将使用地图缩放级别。


使用内置映射应用程序或街道视图是快速添加映射支持的简单办法。 但是，Android 的地图 API 提供更好地控制映射体验。
