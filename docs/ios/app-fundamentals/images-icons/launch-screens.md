---
title: "启动屏幕"
description: "此文章介绍了如何在任何分辨率和方向，使用单个统一情节提要创建所有的 iOS 设备，应用程序启动屏幕。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/19/2018
ms.openlocfilehash: e07a6b72f2821e8cc65e0b0d690dbadf1a6713b6
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="launch-screens"></a>启动屏幕

_此文章介绍了如何在任何分辨率和方向，使用单个统一情节提要创建所有的 iOS 设备，应用程序启动屏幕。_

在 iOS 8 之前, 创建为 iOS 应用的启动屏幕需要开发人员提供的各种设备外形规格和应用程序可以在其中运行的解决方法的每个图像资产。 IOS 8 的发布，但是，它以来可以使用单个统一情节提要来创建一个看起来是在所有情况下正确的启动屏幕。

此简要演练介绍如何创建启动屏幕，默认情况下，新项目中提供的任一情节提要或手动添加到现有项目情节提要。 然后，它演示了如何使用 iOS 设计器将图像视图和标签添加到情节提要，以在这些视图上设置约束并验证情节提要查找正确的各种设备和方向。

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>管理与情节提要的启动屏幕

在 iOS 8 （和更高版本），开发人员可以创建一个特殊统一情节提要提供而不是使用一个或多个静态的启动映像启动屏幕。 在创建时启动情节提要 iOS 设计器中，使用大小类和自动布局来定义不同的显示环境的不同布局。 通过使用大小类和自动布局，开发人员可以创建看起来很好在所有设备上的单一启动屏幕，并显示环境。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在适用于 Mac 的 Visual Studio 中创建新项目，通过选择**文件 > 新解决方案**，然后选择**单视图应用**: 

    ![新建项目窗口中，并选择的单个视图应用程序](launch-screens-images/launch01.png)

    - 默认情况下，新的项目包括**LaunchScreen.storyboard**定义的启动屏幕接口的文件。 
    - 若要改为添加到现有项目的启动屏幕情节提要，右键单击项目名称中**解决方案 Pad**选择**添加 > 新文件...** ，然后选择**启动屏幕**:

    ![新文件窗口中，ios 选择的启动屏幕](launch-screens-images/launch01b.png)

    - 命名该文件**LaunchScreen**或你选择的另一个名称。

2. 配置要为其启动屏幕中使用适当的情节提要的项目：

    - 双击**Info.plist**文件中**解决方案 Pad**以将其打开以进行编辑。
    - 在**启动映像**部分中，请确保**启动屏幕**设置为适当的情节提要的名称：

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch02.png)

    - 默认情况下，新的项目配置为使用**LaunchScreen.storyboard**为其启动屏幕。

3. 将映像添加到**Assets.xcassets**资产目录，以便它可用于启动屏幕。 有关详细信息，请参阅[将映像添加到的资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)部分[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

4. 打开**LaunchScreen.storyboard**由在中双击程序编辑**解决方案 Pad**。

5. 选择设备和预览启动屏幕情节提要 iOS 设计器中所依据的方向。 打开设备选择面板底部工具栏上的，然后选择**iPhone 4S**和**纵向**。

    ![设备选择工具栏](launch-screens-images/launch05.png)

    - 请注意选择的设备和方向仅更改 iOS 设计器如何预览的设计。 此处所做的选择，无论新添加的约束应用跨所有设备和方向，除非**编辑特征**已使用按钮来指定，否则。 

6. 设置**后台**的视图控制器的主视图的颜色。 通过单击视图控制器的中间选择的视图并调整背景颜色使用**属性填充**:

    ![具有紫色背景色的单个视图](launch-screens-images/launch06.png)

7. 添加**图像视图**到启动屏幕，然后设置其源**映像**:

    - 拖动**图像视图**从**工具箱 Pad**到视图的中心。
    - 与**图像视图**选择，在**小组件**部分**属性填充**设置**映像**到映像已设置的属性添加到**Assets.xcassets**资产目录。 重新定位并调整其大小**图像视图**所需的方式：
    
    ![其映像属性设置图像视图](launch-screens-images/launch07.png)

8. 添加**标签**下面**图像视图**并用**属性填充**若要设置其属性： 

    ![具有其文本和颜色的组的标签](launch-screens-images/launch08.png)

9. 通过使用中的右侧按钮切换到约束编辑模式**约束工具栏**:
    
    ![约束编辑模式按钮](launch-screens-images/launch09.png)

10. 将约束添加到**图像视图**、 设置其高度和宽度和水平和垂直居中它：

    ![使用布局约束图像视图](launch-screens-images/launch10.png)

    - 有关如何添加约束的详细信息，请参阅[与适用于 iOS 的 Xamarin 设计器的自动数据布局](~/ios/user-interface/designer/designer-auto-layout.md)。

11. 将约束添加到**标签**、 水平居中、 高度和宽度，同时为其和定位固定从垂直距离**图像视图**:

    ![具有布局约束的标签](launch-screens-images/launch11.png)

12. 测试其他设备和以验证设计查找按预期在所有情况下的方向。 其中需要为特定设备或方向进行调整的情况下，使用**编辑特征**按钮以添加特定大小类的约束：

    ![呈现为 X 使用横向 iPhone 启动屏幕](launch-screens-images/launch12.png)

13. 将所做的更改保存到情节提要。 模拟器或设备上运行应用并启动应用程序启动屏幕将可见。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 创建新项目。 在 Visual Studio 中，选择**文件 > 新建 > 项目**，然后选择**单视图应用 (iPhone)**:
    
    ![新项目，与单个视图应用程序 (iPhone) 所选的窗口](launch-screens-images/launch01-vs.png)

    - 将项目命名，选择一个位置，然后选择**确定**。

2. 如果**资源 > LaunchScreen.xib**中存在**解决方案资源管理器**，通过右键单击文件并选择将其删除**删除**。 此文件将替换为在下一步情节提要。

3. 创建要用作启动屏幕情节提要。 在**解决方案资源管理器**，右键单击项目，然后选择**添加 > 新建项...**跟**空情节提要**。 命名此情节提要**LaunchScreen.storyboard**单击**添加**:

    ![添加新项窗口中，并选择空情节提要](launch-screens-images/launch03-vs.png)

4. 配置项目以使用**LaunchScreen.storyboard**作为其启动屏幕情节提要：

    - 在“解决方案资源管理器”中，双击 **Info.plist** 文件，打开它进行编辑。 
    - 上**可见资产**选项卡上，设置**启动屏幕**到**LaunchScreen**。

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch04-vs.png)

5. 将映像添加到项目中资产目录中，以便它不用于在启动屏幕上使用：

    - 在**解决方案资源管理器**，右键单击**资产目录**和选择**添加资产目录**。 命名此新的资产目录**资产**:

    ![添加新项窗口中，并选择的资产目录](launch-screens-images/launch05-vs.png)

    - 添加新映像集到**资产**资产目录中, 所述[将映像添加到的资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)部分[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

6. 打开**LaunchScreen.storyboard**由在中双击程序编辑**解决方案资源管理器**。

    - 若要编辑的情节提要文件时，Visual Studio 需要与 Mac 生成主机的活动连接。 请参阅[连接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)有关详细信息的指南。

7. 选择设备和预览启动屏幕情节提要 iOS 设计器中所依据的方向。 打开设备选择面板底部工具栏上的，然后选择**iPhone 4S**和**纵向**: 
 
    ![设备选择工具栏](launch-screens-images/launch07-vs.png)

    - 请注意选择的设备和方向仅更改 iOS 设计器如何预览的设计。 此处所做的选择，无论新添加的约束应用跨所有设备和方向，除非**编辑特征**已使用按钮来指定，否则。 

8. 添加**视图控制器**通过拖动其中一个从情节提要**工具箱**拖到设计图面： 

    ![一个空视图控制器添加到设计图面](launch-screens-images/launch08-vs.png)

9. 设置**后台**的视图控制器的主视图的颜色。 通过单击视图控制器的中间选择的视图并调整背景颜色使用**属性窗口**:
    
    ![具有紫色背景色的单个视图](launch-screens-images/launch09-vs.png)

10. 添加**图像视图**到启动屏幕，然后设置其源**映像**:

    - 拖动**图像视图**从**工具箱**到视图的中心。
    - 与**图像视图**仍然选择，在**小组件**部分**属性窗口**设置**映像**映像设置属性已添加到**资产**资产目录。 重新定位并调整其大小**图像视图**所需的方式：
    
    ![其映像属性设置图像视图](launch-screens-images/launch10-vs.png)

11. 添加**标签**下面**图像视图**:

    - 拖动**标签**从**工具箱**拖到设计图面中，将它放入以下**图像视图**。
    - 设置属性**标签**使用**属性窗口**:

    ![具有其文本和颜色的组的标签](launch-screens-images/launch11-vs.png) 

12. 通过使用中的右侧按钮切换到约束编辑模式**约束工具栏**:
    
    ![约束编辑模式按钮](launch-screens-images/launch12-vs.png) 

13. 将约束添加到**图像视图**、 设置其高度和宽度和水平和垂直居中它：

    ![使用布局约束图像视图](launch-screens-images/launch13-vs.png) 

    - 有关添加约束的信息，请参阅[与适用于 iOS 的 Xamarin 设计器的自动数据布局](~/ios/user-interface/designer/designer-auto-layout.md)。

14. 将约束添加到**标签**、 水平居中、 高度和宽度，同时为其和定位固定从垂直距离**图像视图**:
    
    ![具有布局约束的标签](launch-screens-images/launch14-vs.png) 

15. 测试其他设备和以验证设计查找按预期在所有情况下的方向。 其中需要为特定设备或方向进行调整的情况下，使用**编辑特征**按钮以添加特定大小类的约束：

    ![呈现为 X 使用横向 iPhone 启动屏幕](launch-screens-images/launch15-vs.png) 

16. 将所做的更改保存到情节提要。 模拟器或设备上运行应用并启动应用程序启动屏幕将可见。

-----

> [!NOTE]
> 用作启动屏幕情节提要_必须_包括仅简单、 内置 UI 元素和**无法**执行任何计算或从某个自定义的类派生。

有关使用统一的情节提要创建启动屏幕的详细信息，请参阅[动态启动屏幕](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)部分[统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="migrating-to-launch-screen-storyboards"></a>迁移以启动屏幕情节提要

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在更新现有应用程序用于情节提要其启动屏幕，请右键单击**项目名称**中**解决方案资源管理器**和选择**添加** > **新文件...**.选择**iOS** > **启动屏幕**单击**新建**按钮：

![](launch-screens-images/storyboard02.png "选择 iOS 启动屏幕")

接下来，双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。 下**启动屏幕**，选择上面创建的新情节提要文件。

![](launch-screens-images/storyboard09.png "选择上面创建的新情节提要文件")


若要为启动屏幕中使用的新情节提要，请执行以下操作：

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 滚动到**通用的启动映像**一部分编辑器中，打开**启动屏幕**上面下拉列表中，然后选择创建的情节提要的名称： 

    ![](launch-screens-images/storyboard08.png "情节提要设置启动屏幕")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 右键单击项目名称中**解决方案资源管理器**和选择**添加** > **新文件...**: 

    ![](launch-screens-images/image012.png "添加新文件")
2. 输入启动屏幕的名称，然后单击**添加**按钮： 

    ![](launch-screens-images/image013.png "输入启动屏幕的名称")
3. 在**解决方案资源管理器**，双击要打开进行编辑的新创建的情节提要文件。
4. 确保**大小类**设置为**任何： 任何**和**视图作为**是**泛型**: 

    ![](launch-screens-images/image016.png "确保大小类设置为任何： 任何而且查看方式是通用")
5. 程序集的大小类，简单的 UI 元素的启动屏幕 (如`UIImageView`) 和应用程序的捆绑中包含的图像： 

    ![](launch-screens-images/image017.png "程序集在 iOS 设计器中启动屏幕")
6. 将所做的更改保存到情节提要。

-----

## <a name="related-links"></a>相关链接

- [动态启动屏幕 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [统一的情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 设计器基本知识](~/ios/user-interface/designer/index.md)
- [添加图像到资产目录映像设置](~/ios/app-fundamentals/images-icons/displaying-an-image.md#asset-catalogs)
- [与 iOS 的 Xamarin 设计器的自动数据布局](~/ios/user-interface/designer/designer-auto-layout.md)
- [人机接口指南： 启动屏幕](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
