---
title: 在 Xamarin.iOS HomeKit
description: HomeKit 是 Apple 的框架，用于控制主自动化设备。 本文介绍 HomeKit，并介绍 HomeKit 附件模拟器和编写简单的 Xamarin.iOS 应用程序来与这些附件中的配置测试附件。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 0dfc6e9ba5098df66a72292d6c8b89ea1bbd1f97
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787456"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin.iOS HomeKit

_HomeKit 是 Apple 的框架，用于控制主自动化设备。本文介绍 HomeKit，并介绍 HomeKit 附件模拟器和编写简单的 Xamarin.iOS 应用程序来与这些附件中的配置测试附件。_

[![](homekit-images/accessory01.png "示例 HomeKit 启用应用程序")](homekit-images/accessory01.png#lightbox)

Apple iOS 8 中引入了作为一种方式将来自各种供应商的多个主自动化设备无缝集成到单个、 一致的单元的 HomeKit。 通过将提升用于发现的常见协议，配置和控制家庭自动化设备 HomeKit 就允许设备从非相关供应商一起工作，所有操作无需协调工作量各个供应商。

使用 HomeKit，你可以创建一个没有使用供应商提供的 Api 或应用程序控制任何 HomeKit 启用设备的 Xamarin.iOS 应用程序。 使用 HomeKit，您可以执行以下操作：

- 新 HomeKit 启用家庭自动化设备发现，并将它们添加到的数据库，在所有用户的 iOS 设备将保持都原样。
- 设置、 配置、 显示和控制任何设备中 HomeKit_主页配置数据库_。
- 与任何预配置的 HomeKit 设备通信和命令这些执行各项操作，或者协同，如打开所有灯厨房中工作。

为设备提供服务在启用 HomeKit 应用到主页配置数据库，除了 HomeKit 提供使用 Siri 语音命令的访问。 给定适当配置的 HomeKit 安装程序，用户可以发出语音命令，如"Siri，开启中起居室灯。"

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>主配置数据库

HomeKit 组织成主页集合的给定位置中的所有自动化设备。 此集合提供要分组为有意义，用户可读的标签的逻辑排列单元其家庭自动化设备的用户的方法。

主页集合存储在主页配置数据库将用于所有用户的 iOS 设备会自动通过备份和同步。 HomeKit 提供使用主页配置数据库的以下类：

- `HMHome` -这是在一个物理位置 （如保存所有信息和家庭自动化的所有设备的配置的顶级容器 单个系列的居住地)。 用户可能具有多个居住，例如其主要主页和休假内部。 或者，它们可能具有不同"存储"的相同的属性，如主 house 和通过车库来宾内部。 两种方式的至少一个`HMHome`对象_必须_进行安装程序和存储之前可以输入任何其他 HomeKit 信息。
- `HMRoom` -时可选的`HMRoom`允许用户定义家庭内的特定聊天室 (`HMHome`) 例如： 厨房、 卫生、 车库或起居室。 用户可以分组中的所有主自动化设备中插入其内部的特定位置`HMRoom`并采取它们作为一个单元。 例如，询问 Siri 关闭车库灯。
- `HMAccessory` -这表示个人，物理 HomeKit 启用自动化用户的居住地 （例如智能调温器） 中已安装的设备。 每个`HMAccessory`分配给`HMRoom`。 如果用户尚未配置任何聊天室，HomeKit 将特殊默认聊天室分配附件。
- `HMService` -表示由提供的服务给定`HMAccessory`，如 light 或 （如果支持颜色更改） 其颜色的打开/关闭状态。 每个`HMAccessory`都可以提供多个服务，如还包括一个灯车库门打开工具。 此外，给定`HMAccessory`可能有不受用户控制的服务，如固件更新。
- `HMZone` -允许用户组的集合`HMRoom`分为逻辑区域，如楼上、 Downstairs 或地下室的对象。 尽管都是可选的这允许提出 Siri 交互来启用所有光 downstairs 关闭。

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>设置 HomeKit 应用

由于 HomeKit 施加安全要求，必须正确配置的 Xamarin.iOS 应用程序使用 HomeKit 框架和 Xamarin.iOS 项目文件中 Apple 开发人员门户。

请执行以下操作：

1. 登录到[Apple 开发人员门户](http://developer.apple.com)。
2. 单击**证书、 标识符和配置文件**。
3. 如果你尚未这样做，请单击**标识符**和创建的应用程序 ID (例如`com.company.appname`)，否则编辑你现有的 id。
4. 确保**HomeKit**给定 id 已检查服务： 

    [![](homekit-images/provision01.png "启用给定 ID 的 HomeKit 服务")](homekit-images/provision01.png#lightbox)
5. 保存更改。
4. 单击**预配配置文件** > **开发**并创建新的开发设置你的应用程序配置文件： 

    [![](homekit-images/provision02.png "创建新的开发设置应用程序配置文件")](homekit-images/provision02.png#lightbox)
5. 下载和安装新的预配配置文件或使用 Xcode 下载和安装配置文件。
6. 编辑你的 Xamarin.iOS 项目选项，并确保你正在使用你刚刚创建的预配配置文件： 

    [![](homekit-images/provision03.png "选择刚创建的预配配置文件")](homekit-images/provision03.png#lightbox)
7. 接下来，编辑你**Info.plist**文件，并确保你正在使用用于创建预配配置文件的应用程序 ID: 

    [![](homekit-images/provision04.png "设置的应用程序 ID ")](homekit-images/provision04.png#lightbox)
8. 最后，编辑你**Entitlements.plist**文件，并确保**HomeKit**已选择授权： 

    [![](homekit-images/provision05.png "启用的 HomeKit 授权")](homekit-images/provision05.png#lightbox)
9. 将所做的更改保存到的所有文件。

使用就地这些设置，应用程序现在已准备好访问 HomeKit Framework Api。 有关设置的详细信息，请参阅我们[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)和[设置你的应用](~/ios/get-started/installation/device-provisioning/index.md)指南。

> [!IMPORTANT]
> 测试 HomeKit 启用应用的开发需要已正确设置的实际的 iOS 设备。 无法从 iOS 模拟器测试 HomeKit。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 附件模拟器

提供一种方法来测试所有可能的家庭自动化设备和服务，而无需拥有一物理设备，Apple 创建_HomeKit 附件模拟器_。 使用此模拟器，可以设置和配置虚拟 HomeKit 设备。

### <a name="installing-the-simulator"></a>安装模拟器

Apple 提供 HomeKit 附件模拟器作为单独的下载从 Xcode，因此将需要在继续之前安装它。

请执行以下操作：

1. 在 web 浏览器中，请访问[的 Apple 开发人员的下载](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下载**Xcode xxx 的其他工具**（其中 xxx 是已安装的 Xcode 的版本）： 

    [![](homekit-images/simulator01.png "Xcode 下载这些其他工具")](homekit-images/simulator01.png#lightbox)
3. 打开磁盘映像和安装中的工具你**应用程序**目录。

与安装 HomeKit 附件模拟器，可以为测试创建虚拟附件。

### <a name="creating-virtual-accessories"></a>创建虚拟附件

若要开始 HomeKit 附件模拟器并创建几个虚拟附件，请执行以下操作：

1. 从应用程序文件夹中，启动 HomeKit 附件模拟器： 

    [![](homekit-images/simulator02.png "HomeKit 附件模拟器")](homekit-images/simulator02.png#lightbox)
2. 单击**+** 按钮，然后选择**新附件...**: 

    [![](homekit-images/simulator03.png "添加一个新附件")](homekit-images/simulator03.png#lightbox)
3. 填写有关新附件的信息并单击**完成**按钮： 

    [![](homekit-images/simulator04.png "填写有关新附件的信息")](homekit-images/simulator04.png#lightbox)
4. 单击**添加服务...** 按钮，然后从下拉列表中选择服务类型： 

    [![](homekit-images/simulator05.png "从下拉列表中选择服务类型")](homekit-images/simulator05.png#lightbox)
5. 提供**名称**为服务和单击**完成**按钮： 

    [![](homekit-images/simulator06.png "输入服务的名称")](homekit-images/simulator06.png#lightbox)
6. 你可以为服务提供可选的特征，通过单击**添加特征**按钮和配置所需的设置： 

    [![](homekit-images/simulator07.png "配置所需的设置")](homekit-images/simulator07.png#lightbox)
7. 重复上述步骤以创建一个每种类型的虚拟家庭自动化 HomeKit 支持的设备。

某些示例虚拟 HomeKit 附件创建并配置，现在可以使用并从 Xamarin.iOS 应用程序控制这些设备。

## <a name="configuring-the-infoplist-file"></a>配置 Info.plist 文件

用于 iOS 10 的新功能 （和更高版本），开发人员将需要添加`NSHomeKitUsageDescription`到应用程序的密钥`Info.plist`文件，并提供应用程序需要访问用户的 HomeKit 数据库为什么字符串声明。 此字符串将显示给运行该应用的用户的第一个时间：

[![](homekit-images/info01.png "HomeKit 权限对话框")](homekit-images/info01.png#lightbox)

若要设置此密钥，请执行以下操作：

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 在屏幕的底部，切换到**源**视图。
3. 添加新**条目**到列表。
4. 从下拉列表中，选择**隐私-HomeKit 使用率描述**: 

    [![](homekit-images/info02.png "选择隐私-HomeKit 使用率描述")](homekit-images/info02.png#lightbox)
5. 输入应用程序需要访问用户的 HomeKit 数据库的原因的说明： 

    [![](homekit-images/info03.png "输入的描述")](homekit-images/info03.png#lightbox)
6. 保存对文件所做的更改。

> [!IMPORTANT]
> 设置失败`NSHomeKitUsageDescription`中的键`Info.plist`文件将导致应用程序_以静默方式失败_（正在关闭系统在运行时） 而无需 iOS 10 （或更高版本） 中运行时的错误。

## <a name="connecting-to-homekit"></a>连接到 HomeKit

若要与 HomeKit 通信，你的 Xamarin.iOS 应用程序需要首先实例化的实例`HMHomeManager`类。 主页管理器是 HomeKit 中央入口点，并负责提供一份可用家庭更新和维护该列表和返回用户的_主主页_。

`HMHome`对象包含有关给予家庭包括任何聊天室、 组或区域，它可能包含，以及安装了任何家庭自动化附件的所有信息。 可以在 HomeKit，至少一个执行任何操作之前`HMHome`必须创建并分配为主主页。

你的应用程序负责检查是否存在主主页和创建如果不分配。

### <a name="adding-a-home-manager"></a>添加主管理器

若要将 HomeKit 感知添加到一个 Xamarin.iOS 应用程序，编辑**AppDelegate.cs**文件对其进行编辑，并使其如下所示：

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

当第一次运行应用程序时，系统将要求用户，他们想要允许应用程序访问其 HomeKit 信息：

[![](homekit-images/home01.png "将要求用户，他们想要允许应用程序访问其 HomeKit 信息")](homekit-images/home01.png#lightbox)

如果用户接听**确定**，则应用程序将能够使用其 HomeKit 附件否则它将不能并且对 HomeKit 任何调用将失败并出错。

与主页管理器到位，接下来应用程序将需要已配置主主页，而且如果没有，提供用户后，可以创建和分配一个方法。

### <a name="accessing-the-primary-home"></a>访问主主页

如上面所述，则必须创建主主页，并将其配置之前 HomeKit 并且它是应用程序的负责提供一种方法，用户可以创建和分配主家庭如果一个尚不存在。

当你的应用程序首次启动，或者返回从后台时，它需要监视`DidUpdateHomes`事件`HMHomeManager`类，以检查是否存在的主主页。 如果不存在，它应提供一个接口，使用户能够创建一个。

下面的代码可以添加到要检查有主主页的视图控制器：

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

当主页管理器将连接到 HomeKit，`DidUpdateHomes`事件将触发，任何现有的家庭将被加载到的住房管理器的集合以及将加载主主页，如果可用。

### <a name="adding-a-primary-home"></a>添加主主页

如果`PrimaryHome`属性`HMHomeManager`是`null`后`DidUpdateHomes`事件，你需要为用户创建并在继续之前分配主主页提供的方法。

通常的应用程序将提供用于用户命名新的主页，然后获取传递到主管理器为主主页的安装程序的窗体。 有关**HomeKitIntro**示例应用程序，模式视图已在 IOS 设计器中创建并且由调用`AddHomeSegue`segue 从应用程序的主要接口。

它提供了用户输入的名称的新的主页和一个按钮来添加主页的文本字段。 当用户点击**添加主页**按钮，下面的代码调用主管理器中，以添加主页：

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

`AddHome`方法尝试将创建新的主页，并将其返回到给定的回调例程。 如果`error`属性不是`null`出现错误，它应呈现给用户。 最常见的错误而引起的非唯一的主名称或主管理器无法与 HomeKit 通信。

如果已成功创建主页，你需要调用`UpdatePrimaryHome`方法以设置新家为主主页。 同样，如果`error`属性不是`null`出现错误，它应呈现给用户。

你还应监视主页管理器的`DidAddHome`和`DidRemoveHome`应用程序的用户界面所需的事件和更新。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`在上面的示例代码中使用的方法是在 HomeKitIntro 发出的应用程序使用 iOS 警报更轻松的帮助程序类。


## <a name="finding-new-accessories"></a>查找新附件

你的 Xamarin.iOS 应用程序后已定义或从主管理器加载主主页，可以调用`HMAccessoryBrowser`查找任何新的主自动化附件，并将其添加到主页。

调用`StartSearchingForNewAccessories`方法以启动寻找新附件和`StopSearchingForNewAccessories`方法完成后。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 应保持不运行较长时间的时间，因为它将产生负面影响的电池使用时间和的 iOS 设备的性能。 Apple 提供的建议调用`StopSearchingForNewAccessories`后的分钟或仅搜索时查找附件 UI 呈现给用户。

`DidFindNewAccessory`时发现新附件，并且它们将添加到将调用事件`DiscoveredAccessories`附件浏览器中的列表。

`DiscoveredAccessories`列表将包含一套`HMAccessory`定义授予 HomeKit 对象启用家庭自动化设备和其可用的服务，例如灯或车库门控件。

一旦已发现新的访问器，它应呈现给用户并因此它们可以选择它，并将其添加到主页。 示例:

[![](homekit-images/accessory01.png "查找新附件")](homekit-images/accessory01.png#lightbox)

调用`AddAccessory`方法将所选的附件添加到的主服务器的集合。 例如：

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

如果`err`属性不是`null`出现错误，它应呈现给用户。 否则，将要求用户输入要添加的设备的安装程序代码：

[![](homekit-images/accessory02.png "输入要添加的设备的安装程序代码")](homekit-images/accessory02.png#lightbox)

在 HomeKit 附件模拟器可以在下找到此数字**设置代码**字段：

[![](homekit-images/accessory03.png "HomeKit 附件模拟器中的安装程序代码字段")](homekit-images/accessory03.png#lightbox)

有关真实 HomeKit 附件，设置代码也将打印的标签上的设备本身，产品包装盒或访问器的用户手册中。

你应监视附件浏览器的`DidRemoveNewAccessory`事件和更新用户界面，用户已将其添加到其主页集合后，从可用列表将附件。

## <a name="working-with-accessories"></a>使用附件

一次主家庭和已建立并且附件已添加到它，你可以要使用的用户提供的附件 （和 （可选） 聊天室） 的列表。

`HMRoom`对象包含有关给定聊天室的所有信息和任何附件属于它。 聊天室 （可选） 可以放入一个或多个区域中。 A`HMZone`包含有关给定区域的所有信息和所有聊天室属于它。

对于此示例中，我们将保留操作简单和工作与家庭的附件直接，而不是将它们组织成聊天室或区域。

`HMHome`对象包含可以在向用户显示的分配附件的列表及其`Accessories`属性。 例如：

[![](homekit-images/accessory04.png "示例附件")](homekit-images/accessory04.png#lightbox)

此处窗体中，用户可以选择给定的附件，并使用它提供服务。

## <a name="working-with-services"></a>使用服务

当用户与给定的 HomeKit 已启用家庭自动化设备交互时，通常是通过它提供的服务。 `Services`属性`HMAccessory`类包含一套`HMService`设备提供了定义的服务的对象。

服务是等灯、 调温器、 车库门器供电、 交换机或锁。 某些设备 （如车库门打开工具） 将提供多个服务，例如浅色和能够打开或关闭门。

给定的附件提供的特定服务，除了包含每个附件`Information Service`，它定义属性，例如其名称、 制造商、 型号和序列号。

### <a name="accessory-service-types"></a>附件的服务类型

以下服务类型是可通过`HMServiceType`枚举：

- **AccessoryInformation** -提供有关给定家庭自动化设备 （附件） 的信息。
- **AirQualitySensor** -定义无线质量传感器。
- **电池**-定义附件的电池的状态。
- **CarbonDioxideSensor** -定义减少碳排放的二氧化碳传感器。
- **CarbonMonoxideSensor** -定义一氧化碳传感器。
- **ContactSensor** -定义联系人传感器 （如正在打开或关闭窗口）。
- **门**-定义门状态传感器 （例如打开或关闭）。
- **风扇**-定义远程受控的风扇。
- **GarageDoorOpener** -定义车库门打开工具。
- **HumiditySensor** -定义湿度传感器。
- **LeakSensor** -定义泄漏传感器 （如热水器或洗衣机时）。
- **LightBulb** -定义独立 light 或 light 属于另一个附件 （如车库门打开工具）。
- **LightSensor** -定义浅色传感器。
- **LockManagement** -定义管理自动的门锁的服务。
- **LockMechanism** -定义 （如门锁） 的远程控制的锁。
- **MotionSensor** -定义运动传感器。
- **OccupancySensor** -定义占用传感器。
- **Outlet** -定义远程受控墙上插座。
- **安全系统**-定义了一个家庭安全系统。
- **StatefulProgrammableSwitch** -定义仍处于给定状态 （如翻转的交换机） 一次触发的可编程交换机。
- **StatelessProgrammableSwitch** -定义在触发 （如按钮） 后返回到其初始状态的可编程交换机。
- **SmokeSensor** -定义冒烟传感器。
- **切换**-定义与标准墙交换机相似的打开/关闭开关。
- **温度传感器**-定义温度传感器。
- **调温器**-定义用于控制的 HVAC 系统的智能调温器。
- **窗口**-定义种情况下为远程打开或关闭自动的窗口。
- **WindowCovering** -定义涵盖，如遮蔽可以打开或关闭远程控制的窗口。

### <a name="displaying-service-information"></a>显示服务信息

加载后`HMAccessory`可以查询个人`HNService`对象提供，并向用户显示该信息：

[![](homekit-images/accessory05.png "显示服务信息")](homekit-images/accessory05.png#lightbox)

你应始终应检查`Reachable`属性`HMAccessory`然后再尝试使用它。 附件可以是设备的无法访问该用户不在范围内或是否已被拔出。

一旦选择了一种服务，用户可以查看或修改该服务能够监视或控制给定家庭自动化设备的一个或多个特征。

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>使用特征

每个`HMService`对象可以包含一套`HMCharacteristic`对象，可提供有关 （如正在打开或关闭门） 服务的状态信息或允许用户调整 （如同设置光的颜色） 的状态。

`HMCharacteristic` 不仅提供了有关某一特性和其状态的信息，而且还提供了这些方法用于处理与通过状态_特征元数据_(`HMCharacteristisMetadata`)。 此元数据可以提供当向用户或允许在它们若要修改状态显示信息时非常有用的属性 （如最小和最大值的范围）。

`HMCharacteristicType`枚举提供了一套特征元数据值可以定义或修改，如下所示：

 - AdminOnlyAccess
 - AirParticulateDensity
 - AirParticulateSize
 - AirQuality
 - AudioFeedback
 - BatteryLevel
 - 亮度
 - CarbonDioxideDetected
 - CarbonDioxideLevel
 - CarbonDioxidePeakLevel
 - CarbonMonoxideDetected
 - CarbonMonoxideLevel
 - CarbonMonoxidePeakLevel
 - ChargingState
 - ContactState
 - CoolingThreshold
 - CurrentDoorState
 - CurrentHeatingCooling
 - CurrentHorizontalTilt
 - CurrentLightLevel
 - CurrentLockMechanismState
 - CurrentPosition
 - CurrentRelativeHumidity
 - CurrentSecuritySystemState
 - CurrentTemperature
 - CurrentVerticalTilt
 - FirmwareVersion
 - HardwareVersion
 - HeatingCoolingStatus
 - HeatingThreshold
 - HoldPosition
 - 色调
 - Identify
 - InputEvent
 - LeakDetected
 - LockManagementAutoSecureTimeout
 - LockManagementControlPoint
 - LockMechanismLastKnownAction
 - 日志
 - 制造商
 - 模型
 - MotionDetected
 - name
 - ObstructionDetected
 - OccupancyDetected
 - OutletInUse
 - OutputState
 - PositionState
 - PowerState
 - RotationDirection
 - RotationSpeed
 - 饱和度
 - 序列号
 - SmokeDetected
 - SoftwareVersion
 - StatusActive
 - StatusFault
 - StatusJammed
 - StatusLowBattery
 - StatusTampered
 - TargetDoorState
 - TargetHeatingCooling
 - TargetHorizontalTilt
 - TargetLockMechanismState
 - TargetPosition
 - TargetRelativeHumidity
 - TargetSecuritySystemState
 - TargetTemperature
 - TargetVerticalTilt
 - TemperatureUnits
 - 版本

### <a name="working-with-a-characteristics-value"></a>使用特性的值

若要确保你应用具有给定特征的最新状态，调用`ReadValue`方法`HMCharacteristic`类。 如果`err`属性不是`null`出现错误，它可能或可能不显示给用户。

特征的`Value`属性包含作为给定特性的当前状态`NSObject`，且这种情况下无法得到与直接在 C# 中。

若要读取的值，以下帮助器类已添加到**HomeKitIntro**示例应用程序：

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter`应用程序需要读取某一特性的当前状态时，需要使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

以上行将转换的值转换为`float`，然后可以使用 Xamarin C# 代码中。

若要修改`HMCharacteristic`，调用其`WriteValue`方法，并将包装中的新值`NSObject.FromObject`调用。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果`err`属性不是`null`，已发生错误，操作应呈现给用户。

### <a name="testing-characteristic-value-changes"></a>测试特征的值更改

使用时`HMCharacteristics`和模拟的附件、 修改`Value`属性可以监视内 HomeKit 附件模拟器。

与**HomeKitIntro**在实际的 iOS 设备硬件，更改的特性的值上运行的应用程序应几乎立即看到 HomeKit 附件模拟器中。 例如，更改在 iOS 应用光的状态：

[![](homekit-images/test01.png "更改光在 iOS 应用程序中的状态")](homekit-images/test01.png#lightbox)

应更改灯 HomeKit 附件模拟器中的状态。 如果值不会更改，在编写新特征的值时检查错误消息的状态，并确保访问器仍然可以访问。

## <a name="advanced-homekit-features"></a>高级的 HomeKit 功能

本文已覆盖使用 HomeKit 附件 Xamarin.iOS 应用程序中的所需的基本功能。 但是，有几个高级的功能 HomeKit 对本简介中均未覆盖：

- **聊天室**-HomeKit 启用附件 （可选） 可以组织到最终用户的聊天室。 这样，便于用户了解和使用的方式存在附件 HomeKit。 有关创建和维护聊天室的详细信息，请参阅 Apple 的[HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom)文档。
- **区域**-聊天室可以根据需要放入区域被最终用户。 区域引用的用户可能将视为单个单元的聊天室的集合。 例如： 楼上、 Downstairs 或地下室。 同样，这允许 HomeKit 为附件向最终用户有意义的方式处理和呈现。 有关创建和维护区域的详细信息，请参阅 Apple 的[HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone)文档。
- **操作和操作设置**-操作修改附件服务特征和可以分组为集。 操作集充当脚本进行控制的附件的组并协调其操作。 例如，"监视电视"脚本可能关闭遮蔽、 dim 灯，并启用电视和其声音的系统。 有关创建和维护操作和操作集的详细信息，请参阅 Apple 的[HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction)和[HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet)文档。
- **触发器**-触发器可以激活一个或多个操作时设置给定的一组条件均已满足。 例如，打开 portch light 并获取外部深色时锁所有外部的库门。 有关创建和维护触发器的详细信息，请参阅 Apple 的[HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger)文档。

由于这些功能使用上面介绍的相同技术，它们应该很容易实现由以下 Apple [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)， [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)和[HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)。

## <a name="homekit-app-review-guidelines"></a>HomeKit 应用查看准则

在提交 HomeKit 之前启用的 Xamarin.iOS 应用程序向 iTunes Connect 在 iTunes 应用商店中的版本确保按照 Apple 的 HomeKit 启用应用程序的指导原则：

 - 应用程序的主要目的_必须_如果使用 HomeKit framework 会家庭自动化。
 - 应用程序的市场营销文本必须通知用户正在使用 HomeKit 并且他们必须提供隐私策略。
 - 收集用户信息或用于广告 HomeKit 严格禁止。

对于完整查看准则，请参阅 Apple 的[应用商店查看准则](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>在 iOS 9 中是新建什么

Apple 已发布了以下更改和添加到 HomeKit ios 9:

- **维护现有对象**-如果修改现有附件，主页管理器 (`HMHomeManager`) 将通知你已修改的特定项。
- **永久标识符**-所有相关 HomeKit 类现在包括`UniqueIdentifier`属性唯一地标识给定的项跨 HomeKit 启用应用程序 （或相同的应用程序的实例）。
- **用户管理**-添加有权 HomeKit 设备的主要用户的主页中的用户通过提供用户管理的内置的视图控制器。
- **用户功能**-HomeKit 用户现在有了一组控制他们就能够在 HomeKit 中使用哪些功能的特权和 HomeKit 启用附件。 你的应用程序应仅显示与当前用户的相关的功能。 例如，只有管理员应该能够维护其他用户。
- **预定义的场景**-为四个常见发生的事件的平均 HomeKit 用户创建了预定义的场景： 启动、 保留、 返回、 转到平台。 无法从家庭中删除这些预定义的场景。
- **场景和 Siri** -Siri 都更深入地支持 iOS 9 并可在后台识别任何场景 HomeKit 中定义的名称。 用户可以执行场景，只需通过讲到 Siri 其名称。
- **附件类别**-已添加到所有附件并且帮助来标识要添加到家庭的附件类型或在你的应用内处理从一组预定义的类别。 这些新类别附件安装程序均可用。
- **Apple Watch 支持**-HomeKit 现已可供 watchOS 和 Apple Watch 将能 HomeKit 附近监视正在 iPhone 未启用设备的控件。 有关 watchOS HomeKit 支持以下功能： 查看家庭、 控制附件和执行的场景。
- **新事件触发器类型**-除了支持 iOS 8、 iOS 9 现在支持事件触发器基于附件状态 （例如传感器数据） 或地理位置中的计时器类型触发器。 事件的触发器使用`NSPredicates`设置为其执行的条件。
- **远程访问**-与远程访问，用户现在就可以控制其 HomeKit 启用主页自动化附件，当它们在远程位置 house 离开。 IOS 8 中此仅支持如果用户具有第三代 Apple TV 在家中。 在 iOS 9 中，此限制则提升，并且通过 iCloud 和 HomeKit 附件协议 (HAP) 支持远程访问。
- **新的蓝牙低能量 （的） 性能**-HomeKit 现在支持更多的附件类型可通过蓝牙低能量 （的） 协议进行通信。 使用 HAP 安全隧道，HomeKit 附件可以公开另一个蓝牙附件通过 Wi-fi （如果它是超出蓝牙范围）。 在 iOS 9 中的附件具有完全支持通知和元数据。
- **新附件类别**-Apple 在 iOS 9 中添加以下新附件类别： 窗口 Coverings、 电动门和 Windows、 警报系统、 传感器和可编程开关。

有关 HomeKit 在 iOS 9 中的新功能的详细信息，请参阅 Apple 的[HomeKit 索引](https://developer.apple.com/homekit/)和[What's New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210)视频。

## <a name="summary"></a>总结

这篇文章中引入了 Apple 的 HomeKit 主自动化框架。 它介绍了如何设置和配置测试设备使用 HomeKit 附件模拟器以及如何创建简单的 Xamarin.iOS 应用程序发现，与通信并控制使用 HomeKit 家庭自动化设备。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)
