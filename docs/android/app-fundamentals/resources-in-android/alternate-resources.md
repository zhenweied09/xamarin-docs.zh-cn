---
title: "备用资源"
ms.topic: article
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e421a52b1ae97b0beef59352a756401ed661051e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="alternate-resources"></a>备用资源

备用资源是针对某个特定设备或如当前语言、 特定屏幕的大小或像素密度的运行时配置这些资源。 如果 Android 可以匹配特定设备或配置默认的资源比更具体的资源，则将改为使用该资源。 如果它未找到匹配的当前配置的备用资源，则将加载默认资源。 Android 决定如何将更详细地，在资源位置部分介绍应用程序将使用哪些资源

备用资源都组织为根据资源类型，就像默认资源的资源文件夹内的子目录。 替代资源子目录的名称采用的形式： _ResourceType_-_限定符_

*限定符*是用于标识特定的设备配置的名称。
在名称每个通过短划线分隔可能有多个限定符。 例如，下面的屏幕截图显示一个简单的项目具有对各种配置，如区域设置、 屏幕密度、 屏幕大小和方向的备用资源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![备用资源](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![备用资源](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

将限定符添加到资源类型时，将应用以下规则：

1. 可能有多个限定符，使用通过短划线分隔每个限定符。

2. 可能只能指定一次限定符。

3. 下表中显示的顺序必须为限定符。

有关参考下面列出了可能的限定符：

- **MCC 和 mnc 是否** &ndash; [移动国家/地区代码](http://en.wikipedia.org/wiki/List_of_mobile_country_codes)(MCC) 和 （可选）[移动网络代码](http://en.wikipedia.org/wiki/Mobile_Network_Code)（mnc 是否）。 SIM 卡将提供 MCC，而设备连接到的网络将提供 mnc。 虽然可以使用移动国家/地区代码的目标区域设置到，建议方法是使用下面指定的语言限定符。 例如，为德国，目标资源是限定符`mcc262`。 对在美国，T 移动的目标资源限定符是`mcc310-mnc026`。
  有关移动国家/地区代码和移动网络代码的完整列表请参阅<http://mcclist.com/>。

- **语言**&ndash;两个字母[ISO 639-1 语言代码](http://en.wikipedia.org/wiki/ISO_639-1)和 （可选） 跟两个字母[ISO 3166-alpha 2 区域代码](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)。 
  如果提供了这两个限定符，则由分隔`-r`。 例如，为目标讲法语的区域设置则的限定符`fr`使用。 若要面向加拿大法语的区域设置中，`fr-rCA`将使用。 有关语言代码和地区代码的完整列表，请参阅[的表示形式的名称的语言代码](http://www.loc.gov/standards/iso639-2/php/English_list.php)和[国家/地区名称和代码元素](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm)。

- **最小宽度**&ndash;指定应用程序旨在上执行的最小的屏幕宽度。 中的更详细地介绍[创建不同的屏幕的资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别 13 (Android 3.2) 及以上版本可用。 例如，限定符`sw320dp`将用来为其高度和宽度至少为的目标设备 320dp。

- **可用宽度**&ndash;格式 w 中的屏幕的最小宽度*N*dp，其中*N*是无关的像素的宽度以密度。
  用户旋转设备时可能会变化此值。 中的更详细地介绍[创建不同的屏幕的资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在中可用在 API 级别 13 (Android 3.2) 和更高版本。 示例： 使用限定符 w720dp 面向具有的最小 720dp 宽度的设备。

- **可用高度**&ndash;格式 h 中的屏幕的最小高度*N*dp，其中*N*是 dp 中的高度。 用户旋转设备时可能会变化此值。 中的更详细地介绍[创建不同的屏幕的资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在中可用在 API 级别 13 (Android 3.2) 和更高版本。 例如，限定符 h720dp 用于面向具有的最小 720dp 高度的设备

- **屏幕大小**&ndash;此限定符是适用于这些资源的屏幕大小的泛化。 它中的更详细地介绍[创建不同的屏幕的资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可能值为 `small`、`normal`、`large` 和 `xlarge`。 在 API 级别 9 (Android 2.3/Android 2.3.1/Android 2.3.2) 中添加

- **屏幕方面**&ndash;这取决于纵横比，不屏幕方向。 长屏幕的宽度。 在 API 级别 4 (Android 1.6) 中添加。 可能的值为 long 类型的值和 notlong。

- **屏幕方向**&ndash;纵向或横向朝屏幕方向。 这可能会更改应用程序的生命周期内。
  可能的值为 `port` 和 `land`。

- **停靠模式**&ndash;一辆汽车中的设备停靠或支持停靠。 在 API 级别 8 (Android 2.2.x) 中添加。 可能的值为 `car` 和 `desk`。

- **夜间模式**&ndash;是否运行该应用程序在夜间或一天中。 这可能会更改应用程序的生命周期内，而且旨在为开发人员提供机会在夜间中使用的接口的更深版本。 在 API 级别 8 (Android 2.2.x) 中添加。 可能的值为 `night` 和 `notnight`。

- **屏幕像素密度 (dpi)** &ndash;中的物理屏幕上的给定区域的像素数。 通常以每英寸点数 (dpi) 来表示。 可能的值有：

    - `ldpi` &ndash; 低密度的屏幕。

    - `mdpi` &ndash; 中等规模的密度的屏幕

    - `hdpi` &ndash; 高的密度的屏幕

    - `xhdpi` &ndash; 特高的密度的屏幕

    - `nodpi` &ndash; 缩放的资源

    - `tvdpi` &ndash; 在 API 级别 13 (Android 3.2) 屏幕的 mdpi 和 hdpi 之间中引入。

- **触摸屏类型**&ndash;指定的设备可能有的触摸屏的类型。 可能的值为`notouch`（没有触摸屏） `stylus` （电阻式触摸屏适合触笔），和`finger`（触摸屏）。

- **键盘可用性**&ndash;指定使用哪种类型的键盘。 这可能会更改应用程序的生命周期内&ndash;例如当用户打开的硬件键盘。 可能的值有：

    - `keysexposed` &ndash; 该设备具有可用的键盘。 如果没有启用的软件键盘，然后才使用此选项时打开硬件键盘。

    - `keyshidden` &ndash; 设备已的硬件键盘但其处于隐藏状态，然后不启用任何软件键盘。

    - `keyssoft` &ndash; 设备必须启用的软件键盘。

- **主文本的输入法**&ndash;用于指定哪些类型的硬件密钥为可用于输入。 可能的值有：

    - `nokeys` &ndash; 不没有输入任何硬件密钥。

    - `qwerty` &ndash; 没有可用的键盘键盘。

    - `12key` &ndash; 没有 12 密钥硬件键盘


- **导航键可用性**&ndash;为时 5 方式还是 d pad （双向-键盘） 导航，则可用。 这可能会更改你的应用程序的生命周期内。 可能的值有：

    - `navexposed` &ndash; 导航键可供用户

    - `navhidden` &ndash; 导航的密钥也不可用。

-  **主要的非触摸导航方法**&ndash;导航设备上的可用的类型。 可能的值有：

    - `nonav` &ndash; 可用的唯一导航工具是触摸屏幕

    - `dpad` &ndash; d 板 （双向-键盘） 是可用于导航

    - `trackball` &ndash; 该设备具有轨迹球上导航

    - `wheel` &ndash; 其中有一个或多个方向轮可用少见的方案

-  **平台版本 （API 级别）** &ndash;格式 v 中的设备支持的 API 级别*N*，其中*N*是为目标的 API 级别。 例如，v11 将面向 API 级别 11 (Android 3.0) 设备。


有关资源的更完整信息限定符请参阅[提供资源](http://developer.android.com/guide/topics/resources/providing-resources.html)Android 开发人员网站上。


## <a name="how-android-determines-what-resources-to-use"></a>Android 如何确定使用哪些资源

它是非常有可能的并且可能 Android 应用程序将包含许多资源。 请务必了解在该设备上运行 Android 选择应用程序的资源的方式。

Android 确定基的资源通过循环访问规则的以下测试：

- **消除矛盾限定符**&ndash;例如，如果设备方向纵向，然后所有横向资源目录将被都拒绝。

- **忽略不支持的限定符**&ndash;并非所有的限定符可供所有 API 级别。 如果资源目录包含的设备不支持的限定符，则将忽略该资源目录。

- **标识的下一步的最高优先级限定符**&ndash;引用表中选择下一步的最高优先级识别符 （从上到下）。

- **保持限定符任何资源目录**&ndash;是否存在任何匹配到上述表限定符的资源目录选择下一步的最高优先级识别符 （从上到下）。

这些规则也图下列流程图中所示：

[![资源流程图](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png)

当系统寻找密度特定资源，但找不到它们时，它将尝试查找其他密度特定资源并缩放它们。 Android 一定不能使用默认资源。
例如，当寻找低密度资源和它不可用，Android 可能对默认或中等密度资源选择高密度的资源版本。 它这样做是因为高密度资源可以按比例缩小按 0.5，这将导致比缩小需要 0.75 因素的中型密度资源更少的可见性问题的系数。

作为示例，请考虑具有以下可绘制资源目录的应用程序：

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

和现在在进行以下配置设备上运行应用程序：

- **区域设置** &ndash; EN-GB
- **方向**&ndash;端口
- **屏幕密度** &ndash; hdpi
- **触摸屏类型** &ndash; notouch
- **主要输入的方法** &ndash; 12key

开始时，将消除的法语资源，因为它们与区域设置的冲突`en-GB`，离开我们提供：

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

接下来，从上面的限定符表选择的第一个限定符： MCC 和 mnc 是否。 没有任何包含此限定符，因此忽略 MCC/mnc 代码的资源目录。

选择下一步的限定符，即语言。 有相匹配的语言代码的资源。 不匹配的语言代码的所有资源目录`en`被拒绝，以便的资源的列表现在是：

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

存在的下一步限定符是为屏幕方向，因此所有与屏幕方向的不匹配的资源目录`port`将消除：

    drawable-en-port
    drawable-en-port-ldpi

接下来是屏幕密度的限定符`ldpi`，这将导致一个排除多个资源目录：

    drawable-en-port-ldpi

此过程中，由于 Android 将可绘制在中使用资源的资源目录`drawable-en-port-ldpi`设备。

> [!NOTE]
> **注意：**的屏幕大小限定符提供到此选择过程的一个例外。 很可能适用于 Android，若要选择为比当前设备提供较小屏幕所设计的资源。 例如，大屏幕设备可能使用的资源提供正常大小屏幕。 但是此反之不成立： 同一个大屏幕设备将不使用 xlarge 屏幕提供的资源。 如果 Android 找不到与给定的屏幕大小匹配的资源集，该应用程序将崩溃。
