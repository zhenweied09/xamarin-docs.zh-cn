---
title: "为不同的屏幕创建资源"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/12/2017
ms.openlocfilehash: aeb7115e3c7521f6679e8802eb759d7e56ba1cfe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="creating-resources-for-varying-screens"></a>为不同的屏幕创建资源

本身的 android 设备上运行多个不同，每个都具有各种分辨率、 屏幕大小和屏幕密度。 Android 将执行缩放和调整大小以使应用程序在这些设备上工作，但这可能会导致达不到最佳用户体验。 例如，映像可能出现的模糊，映像可能占用过多 （或没有足够） 屏幕空间这将导致用户界面元素在布局中的位置将重叠或太很远。

<a name="Concepts" />

## <a name="concepts"></a>概念

几个术语和概念很重要了解以支持多个屏幕。

- **屏幕大小**&ndash;用于显示你的应用程序的物理空间量

- **屏幕密度**&ndash;屏幕在任何给定区域的像素数。 典型单位是度量的每英寸点数 (dpi)。

- **解析**&ndash;屏幕上的像素的总数。 当开发应用程序，则解析不与屏幕的大小和密度同样重要。

- **密度无关的像素 (dp)** &ndash;这是虚拟单位度量值以允许布局设计为独立的密度。 若要将分发点转换为屏幕像素请使用以下公式：

    px &equals; dp &times; dpi &divide; 160

- **方向**&ndash;屏幕的方向被视为可横向宽度大于高度时。 与此相反，，纵向时的屏幕是高度大于宽度。 根据用户旋转设备，应用程序的生命周期内可以更改方向。

请注意，这些概念的前三个间相关&ndash;分辨率提高到没有增加密度会增加的屏幕大小。 但是如果增加的密度和解决方法然后屏幕的大小可以保持不变。 此屏幕的大小、 密度和解决方法之间的关系非常快速地使屏幕支持。

为了应对这种复杂性，Android 框架倾向于使用*密度无关的像素 (dp)*对于屏幕布局。 通过使用密度无关的像素，UI 元素将显示给用户以在具有不同密度的屏幕上具有相同的物理大小。

<a name="Supporting_Various_Screen_Sizes_and_Densities" />

## <a name="supporting-various-screen-sizes-and-densities"></a>支持各种屏幕大小和密度

Android 处理大多数呈现布局为每个屏幕配置正确的工作。 但是，有帮助解决系统时可以执行某些操作。

而不是为布局中的实际像素密度无关的像素的用途是适用于大多数情况，以确保密度独立性。
Android 将缩放 drawables 在运行时到相应的大小。
但是，很可能这种扩展将导致显示模糊的位图。 若要避免此问题，可能需要提供不同的密度的备用资源。 设计的多个解决方法和屏幕密度将证明更轻松的设备时开始更高的分辨率或密度映像，然后再然后缩小。 这将阻止任何模糊或调整大小，可能会导致的失真。

<a name="Declare_the_Screen_Size_the_Application_Supports" />

### <a name="declare-the-screen-size-the-application-supports"></a>声明为应用程序支持的屏幕大小

声明的屏幕大小可确保仅受支持的设备可以下载应用程序。 这通过设置实现[支持屏幕](http://developer.android.com/guide/topics/manifest/supports-screens-element.html)中的元素**AndroidManifest.xml**文件。 此元素用于指定应用程序支持何种屏幕大小。 给定的屏幕是当应用程序可以正确需要支持它的布局以填满屏幕。 通过使用此清单元素，该应用程序将不会显示在[ *Google Play* ](https://play.google.com/)设备不满足屏幕规范。 但是，应用程序仍将具有不受支持屏幕设备上运行，但在布局可能出现的模糊和像素化。

为了执行此操作在 Xamarin.Android 中，有必要首先添加**AndroidManifest.xml**文件到项目中，如果不存在：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-vs-sml.png)](resources-for-varying-screens-images/01-android-manifest-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-xs-sml.png)](resources-for-varying-screens-images/01-android-manifest-xs.png)

-----


**AndroidManifest.xml**添加到**属性**目录。 然后编辑该文件以包括[支持屏幕](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加支持屏幕](resources-for-varying-screens-images/02-adding-supports-screens-vs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加支持屏幕](resources-for-varying-screens-images/02-adding-supports-screens-xs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-xs.png)

-----


<a name="Provide_Alternate_Layouts_for_Different_Screen_Sizes" />

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>针对不同屏幕大小提供备用布局

尽管 Android 将调整大小根据屏幕大小，这可能不是在某些情况下足够。 它可能需要增加在更大的屏幕上，一些 UI 元素的大小或更改为较小屏幕的 UI 元素的位置。

从 API 级别 13 (Android 3.2) 开始，屏幕大小支持已弃用使用软件*N*dp 限定符。 此新限定符声明空间给定布局量需要。 强烈建议应运行在 Android 3.2 或更高版本的应用程序应使用这些较新的限定符。

例如，如果布局所需的屏幕宽度最小 700dp，备用布局会在文件夹**布局 sw700dp**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![700dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![700dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


作为参考，以下是一些数字，为各种设备：

- **典型 phone** &ndash; 320dp： 典型电话

- **5"平板电脑 /"tweener"设备** &ndash; 480dp： 如 Samsung 注意

- **7"平板电脑** &ndash; 600dp： 如 Barnes&amp;挺查找

- **10"平板电脑** &ndash; 720dp： 如 Motorola Xoom

为应用程序目标 API 级别最多 12 (Android 3.1)，在布局应转在目录中使用限定符**小**/**正常**/**大型** / **xlarge**作为泛化的大多数设备中可用的各种屏幕大小。 例如，在下图中，有四个不同的屏幕大小的备用资源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![针对四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![针对四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

下面是较旧的前期 API 级别 13 屏幕大小限定符如何与密度无关的像素进行比较的比较：

- 426dp x 320dp 是**小**

- 470dp x 320dp 是**正常**

- 640dp x 480dp 是**大型**

- 960dp x 720dp 是**xlarge**

较新的屏幕大小 API 级别 13 中的限定符，并设置具有优先权要高于 API 级别 12 和更低的较旧屏幕限定符。
对于将跨越旧和新的 API 级别的应用程序，可能需要创建备用资源使用这两个集的限定符，如下面的屏幕快照中所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![使用这两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![使用这两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----


<a name="Provide_Different_Bitmaps_for_Different_Screen_Densities" />

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>为不同的屏幕密度提供不同的位图

尽管 Android 具有可伸缩性根据设备的位图，位图本身可能不像缩放向上或向下： 它们可能变得模糊。 提供适合于屏幕密度的位图将缓解此问题。

例如下, 图是一种布局和外观可能发生的问题时密度，请指定未提供资源。

![不带密度资源的屏幕截图](resources-for-varying-screens-images/06-density-not-provided.png)

比较而言，旨在提供与密度特定资源的布局：

![与密度特定资源的屏幕截图](resources-for-varying-screens-images/07-density-specific-resources.png)

<a name="Create_Varying_Density_Resources_with_Android_Asset_Studio" />

### <a name="create-varying-density-resources-with-android-asset-studio"></a>使用 Android 资产 Studio 创建不同的密度资源

各种密度这些位图创建可能会有点需要很长时间。 在这种情况下，Google 已创建一个在线的实用程序，这会减少某些内容涉及调用这些位图创建麻烦[ **Android 资产 Studio**](https://romannurik.github.io/AndroidAssetStudio/)。

[![Android 资产 Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png)

此网站将帮助创建目标的四个常见屏幕密度通过提供一个图像的位图。 Android Studio 中资产将然后创建一些自定义操作的位图，然后使他们可以作为 zip 文件下载。

<a name="Tips_for_Multiple_Screens" />

## <a name="tips-for-multiple-screens"></a>多个屏幕的提示

Android 上会非常多个设备上，运行和屏幕大小和屏幕密度的组合可能看起来非常艰巨。 以下提示可帮助最大程度减少支持各种设备所需的工作量：

- **仅设计和开发您需要**&ndash;有许多不同的设备，但一些可能需要很大的工作量来设计和开发的极少数窗体因素中存在。 [**屏幕的大小和密度**](http://developer.android.com/resources/dashboard/screens.html)仪表板是提供数据的屏幕大小/屏幕密度矩阵分解的 Google 提供的页。 此分解提供有关如何在支持屏幕上的开发工作的见解。

- **使用分发点，而不是像素**-像素变得让人头痛随着屏幕密度的变化。 请不要对像素值。 避免 dp （独立于密度的像素为单位） 为支持的像素为单位。

- **避免** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **任何位置可能**&ndash;它在 API 级别 3 (Android 1.5) 中已弃用，并将导致脆弱的布局。 不应使用。 相反，尝试使用更灵活的布局小组件，如[ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)， [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，或新[**GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/)。

- **选取一个作为默认设置的布局方向**&ndash;例如，而不是提供的备用资源**布局上关于领土**和**布局端口**，put 的资源在布局**布局**，和资源到纵向**布局端口**。

- **使用高度和宽度的 LayoutParams** -定义在 XML 布局文件中，UI 元素时 Android 应用程序中使用**wrap_content**和**fill_parent**值将具有多个成功确保正确查看跨不同设备比使用像素或密度的独立单元。 这些维度值来根据需要缩放位图资源导致 Android。 鉴于此相同密度无关的单位是最保留时指定边距和填充的 UI 元素。

<a name="Testing_Multiple_Screens" />

## <a name="testing-multiple-screens"></a>测试多个屏幕

Android 应用程序需要将支持的所有配置对其进行测试。 理想情况下设备应在实际设备本身上进行测试，但在许多情况下这是不可能或可行。
在这种情况下为每个设备配置使用仿真程序和 Android 虚拟设备安装程序将很有用。

Android SDK 提供外观可能用于创建 AVD 的某些仿真程序将复制大小、 密度和解决方法的多个设备。
许多硬件供应商同样提供用于其设备的外观。

另一个选项是使用第三方测试服务的服务。
这些服务将采取 APK，在很多不同设备上运行它，然后提供应用程序的工作方式的反馈。
