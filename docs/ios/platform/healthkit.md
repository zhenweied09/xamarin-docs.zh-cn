---
title: HealthKit
description: "HealthKit 是中的运行状况相关信息提供集中的、 协调，且安全的数据存储的 iOS 8 中引入的框架。 隐私和安全的运行状况信息，并使用运行状况应用，用户的仪表板，可确保操作系统。 用户的权限后，应用程序可以读取和写入各种运行状况信息。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 4f85f208c12561b6db9800d963e2d7bf32c2a4d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="healthkit"></a>HealthKit

_HealthKit 是中的运行状况相关信息提供集中的、 协调，且安全的数据存储的 iOS 8 中引入的框架。隐私和安全的运行状况信息，并使用运行状况应用，用户的仪表板，可确保操作系统。用户的权限后，应用程序可以读取和写入各种运行状况信息。_

运行状况工具包提供有关用户的运行状况相关信息的安全数据存储。 运行状况工具包应用程序可能，使用用户的显式权限、 读取和写入到此数据存储并添加相关的数据时接收通知。 应用程序可以显示数据，或用户可以使用 Apple 的提供运行状况应用程序以查看其所有数据的仪表板。

由于运行状况相关的数据如此敏感至关重要，运行状况工具包强类型化，使用的度量值和与正在记录的信息 （例如，献血尿糖或核心速率） 的类型的显式关联的单位。 此外，运行状况工具包应用程序必须使用显式的权利，必须请求访问特定类型的信息和用户必须显式授予对这些类型的数据的应用程序访问权限。

本文将介绍：

- 运行状况工具包的安全要求，包括应用程序设置和请求用户权限以访问的运行状况工具包站点数据库。
- 运行状况工具包的类型系统，不应用或错误解释数据; 的可能性降至最低
- 写入的共享的系统范围内的运行状况工具包数据存储。

本文未介绍更高级的主题，例如查询数据库、 单位之间的度量值，转换或接收的新数据的通知。

在本文中，我们将创建一个示例应用程序来记录用户的核心速率：

[![](healthkit-images/image01.png "一个示例应用程序来记录用户核心速率")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>惠?

以下被需完成这篇文章中提供的步骤：

- **Xcode 7 和 iOS 8 （或更高版本）** – Apple 的最新 Xcode 以及 iOS Api 需要安装并配置开发人员计算机上。
- **用于 Mac 或 Visual Studio 的 visual Studio** – 应安装最新版本的适用于 Mac 的 Visual Studio，并将其开发人员计算机上配置。
- **iOS 8 （或更高版本） 的设备**– 8 或更高版本的测试运行 iOS 的最新版本的 iOS 设备。

> [!IMPORTANT]
> **注意：** iOS 8 中引入了运行状况工具包。 目前，运行状况工具包不是可在 iOS 模拟器上和调试需要连接到物理 iOS 设备。




## <a name="creating-and-provisioning-a-health-kit-app"></a>创建并设置运行状况工具包应用程序
Xamarin iOS 8 应用程序可以使用 HealthKit API 之前，它必须正确配置它们并将其设置中。 本部分将介绍正确设置你的 Xamarin 应用程序所需的步骤。

运行状况工具包应用需要：

- 显式**应用程序 ID**。
- A**预配配置文件**关联与显式**应用程序 ID**与**运行状况工具包**权限。
- `Entitlements.plist`与`com.apple.developer.healthkit`类型的属性`Boolean`设置为`Yes`。
- `Info.plist`其`UIRequiredDeviceCapabilities`密钥包含一个条目以及`String`值`healthkit`。
- `Info.plist`还必须具有适当的隐私说明条目：`String`密钥的说明`NSHealthUpdateUsageDescription`如果应用程序将要写入数据和`String`密钥的说明`NSHealthShareUsageDescription`如果应用程序将读取运行状况工具包数据。

若要了解有关设置 iOS 应用程序，[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)Xamarin 的中的文章**入门**系列描述开发人员证书，应用程序 Id 之间的关系预配配置文件，并应用权利。

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>显式应用 ID 和预配配置文件

创建显式**应用程序 ID**且相应**预配配置文件**Apple 的中完成[iOS 开发人员中心](https://developer.apple.com/devcenter/ios/index.action)。 

你当前**应用 Id**中列出[证书、 标识符和配置文件](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action)的开发人员中心的部分。 通常情况下，此列表将显示为**ID**值`*`，以指示，**应用程序 ID*- **名称**可以用于任意数量的后缀。 此类*通配符应用 Id*不能使用运行状况工具包。
 
若要创建显式**应用程序 ID**，单击 **+** 中右上方以使你转到按钮**注册 iOS 应用程序 ID**页：


[![](healthkit-images/image02.png "注册 Apple 开发人员门户上的应用程序")](healthkit-images/image02.png#lightbox)

创建应用程序说明之后，更高版本，图中所示，使用**显式应用 ID**节以创建你的应用程序的 ID。 在**应用程序服务**部分，选中**运行状况工具包**中**启用服务**部分。

完成后，按**继续**按钮以注册**应用程序 ID**中你的帐户。 你将回到返回**证书、 标识符和配置文件**页。 单击**预配配置文件**转到你当前的预配配置文件的列表并单击 **+** 你转到右上角的按钮**添加 iOS预配配置文件**页。 选择**iOS 应用程序开发**选项，然后单击**继续**可用于访问**选择应用程序 ID**页。 在此处，选择显式**应用程序 ID**事先指定：


[![](healthkit-images/image03.png "选择显式的应用程序 ID")](healthkit-images/image03.png#lightbox)

单击**继续**和工作通过剩余的屏幕，将在其中指定你**开发人员证书**，**设备**，和一个**名称**此**预配配置文件**:

[![](healthkit-images/image04.png "生成预配配置文件")](healthkit-images/image04.png#lightbox)

单击**生成**和 await 的你的配置文件创建。 下载文件，并双击它以安装在 Xcode 中。 你可以确认其安装在**Xcode > 首选项 > 帐户 > 查看详细信息...** 你应看到你刚刚安装的配置文件，并且它应该图标中的任何其他特殊服务运行状况工具包以及其**权利**行：

[![](healthkit-images/image05.png "在 Xcode 中查看配置文件")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>将关联的应用程序 ID 和预配配置文件与你的 Xamarin.iOS 应用程序

一旦创建并安装相应**预配配置文件**如下所述，它通常是在 Visual Studio 中创建的解决方案，用于 Mac 或 Visual Studio 的时间。 运行状况工具包访问可供任何 iOS C# 或 F # 项目。

而不是逐步完成手动创建 Xamarin iOS 8 项目的过程，打开示例应用程序附加到这篇文章 （其中包括预构建的情节提要和代码）。 若要将示例应用程序使用启用你运行状况工具包**预配配置文件**，请在**解决方案 Pad**，右击项目并打开其**选项**对话框。 切换到**iOS 应用程序**面板和输入显式**应用程序 ID**你之前创建作为应用程序的**捆绑标识符**:

[![](healthkit-images/image06.png "输入显式应用 ID")](healthkit-images/image06.png#lightbox)

现在切换到**iOS 捆绑签名**面板。 你最近安装**预配配置文件**，与关联到显式**应用程序 ID**，现在可作为**预配配置文件**:

[![](healthkit-images/image07.png "选择预配配置文件")](healthkit-images/image07.png#lightbox)

如果**预配配置文件**不可用，请仔细检查**捆绑标识符**中**iOS 应用程序**与在中指定的面板**iOS开发人员中心**且**预配配置文件**安装 (**Xcode > 首选项 > 帐户 > 查看详细信息...**).

当启用了运行状况工具包的**预配配置文件**是处于选中状态，单击**确定**以关闭项目选项对话框。

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist 和 Info.plist 值

示例应用程序包含`Entitlements.plist`（这是必需，运行状况工具包已启用应用程序） 的文件，并且不包含在每个项目模板。 如果你的项目不包括权利，右键单击你项目中，选择**文件 > 新建文件 … > iOS > Entitlements.plist**手动添加一个。

从根本上讲，你`Entitlements.plist`必须具有以下键和值对：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

同样，`Info.plist`应用程序必须具有值为`healthkit`与关联`UIRequiredDeviceCapabilities`密钥：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文所附带的示例应用程序包括一个预配置`Entitlements.plist`，包含所有所需的密钥。

<a name="programming" />

## <a name="programming-health-kit"></a>编程运行状况工具包

运行状况工具包数据存储是在应用之间共享的专用、 特定于用户的数据存储。 由于运行状况信息是非常敏感的用户必须采取正的步骤，以允许访问数据。 这种访问可以是分部 (写但不是读取、 对于某些类型的数据而非任何其他的访问权限等) 并可能随时吊销。 运行状况工具包应用程序应使用多个用户将有关存储其运行状况相关的信息不愿意了解防御，编写。

运行状况工具包数据仅限于 Apple 指定的类型。 这些类型严格定义： 一些，如献血类型，而其他人将与一套度量值 （例如元语法、 热量和升） 组合将量则限于枚举 Apple 提供的特定值。 共享一个兼容度量单位的甚至数据通过来区分其`HKObjectType`; 例如，类型系统将捕获错误的尝试存储`HKQuantityTypeIdentifier.NumberOfTimesFallen`字段期待值`HKQuantityTypeIdentifier.FlightsClimbed`即使它们都使用` HKUnit.Count`度量单位。

可在运行状况工具包数据存储中存储的类型是所有的子类`HKObjectType`。 `HKCharacteristicType` 对象存储生物性别、 献血类型和出生日期。 不过，都更常见`HKSampleType`对象，表示在特定时间或时间段内进行采样的数据。 

[![](healthkit-images/image08.png "HKSampleType 对象图表")](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的具有四个具体的子类。 目前只有一个类型`HKCategoryType`睡眠分析的数据。 大多数的运行状况工具包中的数据属于类型`HKQuantityType`，并将存储在其数据`HKQuantitySample`使用熟悉的工厂设计模式创建的对象：

[![](healthkit-images/image09.png "大多数的运行状况工具包中的数据的类型 HKQuantityType 并将其数据存储在 HKQuantitySample 对象")](healthkit-images/image09.png#lightbox)

`HKQuantityType` 类型的范围从`HKQuantityTypeIdentifier.ActiveEnergyBurned`到`HKQuantityTypeIdentifier.StepCount`。 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>从用户请求权限

最终用户必须执行正的步骤，以允许应用程序读取或写入运行状况工具包数据。 这是通过在 iOS 8 设备可能已预安装的运行状况应用。 第一次运行运行状况工具包应用程序，则用户会看到与系统控制**运行状况访问**对话框：

[![](healthkit-images/image10.png "用户会看到一个系统控制的运行状况访问对话框")](healthkit-images/image10.png#lightbox)

更高版本，用户可以更改使用运行状况应用程序的权限**源**对话框：

[![](healthkit-images/image11.png "用户可以更改使用运行状况应用源对话框权限")](healthkit-images/image11.png#lightbox)

由于运行状况信息是极其敏感，应用程序开发人员应写入其程序防御，权限将拒绝和应用程序正在运行时更改的假定条件下。 最常见惯用语法是请求中的权限`UIApplicationDelegate.OnActivated`方法，然后修改相应的用户界面。

### <a name="permissions-walkthrough"></a>权限演练

在运行状况工具包设置项目中，打开`AppDelegate.cs`文件。 请注意，语句使用`HealthKit`; 上的文件的顶部。


下面的代码与运行状况工具包权限：

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

所有这些方法中的代码可以进行内联`OnActivated`，但示例应用程序使用单独的方法可以使它们更清楚的意图：`ValidateAuthorization()`具有特定正在编写的类型 （和读取，如果应用程序所需） 以请求访问权限所必需的步骤和`ReactToHealthCarePermissions()`后 Health.app 中的权限对话框交互用户已激活的回调。

作业`ValidateAuthorization()`是生成的一套`HKObjectTypes`应用程序会写入，请求授权，以更新该数据。 在示例应用中，`HKObjectType`用于密钥`KHQuantityTypeIdentifierKey.HeartRate`。 此类型添加到集`typesToWrite`，而集`typesToRead`保留为空。 这些设置和的引用来`ReactToHealthCarePermissions()`回调，传递给`HKHealthStore.RequestAuthorizationToShare()`。

`ReactToHealthCarePermissions()`用户权限对话框中的问题，并传递两条信息后，将调用回调：`bool`值将成为`true`用户如果具有交互权限对话框和`NSError`，如果非 null，表示某种类型的与提供权限对话框关联的错误。

> [!IMPORTANT]
> **注意：**明确了解此函数的自变量就显得：_成功_和_错误_参数不会指示用户是否已授予权限以访问运行状况工具包数据 ！ 它们仅指示用户已被授予机会允许对数据的访问。

若要确认该应用程序是否有权访问数据，`HKHealthStore.GetAuthorizationStatus()`使用时，传入`HKQuantityTypeIdentifierKey.HeartRate`。 根据返回的状态，应用程序启用或禁用输入数据的能力。 没有访问拒绝处理没有标准用户体验，有许多可能的选项。 在示例应用中，将状态设置上`HeartRateModel`单一实例对象，反过来，引发相关的事件。

## <a name="model-view-and-controller"></a>模型、 视图和控制器

若要查看`HeartRateModel`单一实例对象，打开`HeartRateModel.cs`文件：

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

第一部分是用于创建泛型事件和处理程序的样板文件代码。 初始部分的`HeartRateModel`类也是用于创建线程安全的单一实例对象的样本。

然后，`HeartRateModel`公开 3 事件： 

- `EnabledChanged` -指示已启用或禁用核心速率存储 （请注意存储最初处于禁用状态）。 
- `ErrorMessageChanged` -对于此示例应用程序中，我们具有非常简单的错误处理模型： 最后一个错误的字符串。 
- `HeartRateStored` -核心速率存储在运行状况工具包数据库时引发。

请注意，每当触发这些事件，它都是通过`NSObject.InvokeOnMainThread()`，这样，订阅服务器以更新 UI。 或者，无法为在后台线程上引发记录事件，并确保兼容的责任无法从左到其处理程序。 线程注意事项非常重要运行状况工具包应用程序中，因为异步的函数，如权限请求中，许多客户端，并在非主线程上执行其回调。

中的运行状况工具包特定代码`HeartRateModel`处于两个函数`HeartRateInBeatsPerMinute()`和`StoreHeartRate()`。 

`HeartRateInBeatsPerMinute()` 将其自变量转换为强类型的运行状况工具包`HKQuantity`。 指定的数量的类型是`HKQuantityTypeIdentifierKey.HeartRate`和数量的单位为`HKUnit.Count`除以`HKUnit.Minute`(换而言之，单位是*每分钟匹敌*)。 

`StoreHeartRate()`函数采用`HKQuantity`(在示例应用中，创建一个由`HeartRateInBeatsPerMinute()`)。 若要验证其数据，它使用`HKQuantity.IsCompatible()`方法，它返回`true`如果对象的单元可以转换为自变量中的单元。 如果使用数量创建了`HeartRateInBeatsPerMinute()`很明显，这将返回`true`，但它也将返回`true`数量被创建为，例如，如果*匹敌每小时*。 更常见`HKQuantity.IsCompatible()`可以用于验证大容量，距离和能量用户或设备可能输入或显示的度量值 （如皇室单位） 的一个系统中，但它可能保存在另一个系统 （如度量单位）。 

一旦经过验证的数量的兼容性，`HKQuantitySample.FromType()`工厂方法用于创建强类型`heartRateSample`对象。 `HKSample` 对象具有开始和结束日期;对于即时读数，这些值应该是相同的因为它们位于该示例。 该示例还不设置任何键 / 值数据其`HKMetadata`自变量，但一个都可以使用如下面的代码的代码来指定传感器位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

一次`heartRateSample`已创建，代码将创建新的连接到数据库具有使用块。 在该块中，`HKHealthStore.SaveObject()`方法尝试对数据库进行的异步写入。 对 lambda 表达式的结果调用触发相关事件，或者`HeartRateStored`或`ErrorMessageChanged`。

现在，已进行编程模型，它是时间就可以看到如何控制器反映模型状态。 打开`HKWorkViewController.cs`文件。 构造函数只需连线`HeartRateModel`到事件处理方法的单一实例 （同样，这可以进行内联用 lambda 表达式，但单独的方法明确的意图变得更加明显）：

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

下面是相关的处理程序：

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

显然，到一个控制器的应用程序，它就可以避免创建单独的模型对象和事件用于控制流，但模型对象的用途是更适合于实际的应用。

## <a name="running-the-sample-app"></a>运行示例应用程序

IOS 模拟器不支持运行状况工具包。 必须运行 iOS 8 的物理设备上调试。

将正确设置 iOS 8 开发设备附加到你的系统。 选择它作为 Visual Studio 中的部署目标适用于 Mac 和从菜单中选择**运行 > 调试**。

> [!IMPORTANT]
> **注意：**与设置相关的错误将在此时呈现。 若要解决错误，请查看创建和运行状况工具包应用上述设置。 组件包括： 
>
> - **iOS 开发人员中心**-显式应用 ID 和运行状况工具包启用预配配置文件。 
> - **项目选项**的捆绑标识符 (显式应用 ID) 和预配配置文件。
> - **源代码**-Entitlements.plist 和 Info.plist

假设设置已正确设置，将启动你的应用程序。 在它到达其`OnActivated`方法，它会请求运行状况工具包授权。 由操作系统，遇到的这个第一次你的用户将看到与以下对话框：


[![](healthkit-images/image12.png "用户将出现此对话框")](healthkit-images/image12.png#lightbox)

启用应用程序更新数据的核心速率，并且你的应用程序将重新出现。 `ReactToHealthCarePermissions`回调将以异步方式激活。 这将导致`HeartRateModel’s``Enabled`属性可以更改，将引发`EnabledChanged`事件，这将导致`HKPermissionsViewController.OnEnabledChanged()`事件处理程序来运行，它使`StoreData`按钮。 下图显示了顺序：


[![](healthkit-images/image13.png "此图显示了事件的顺序")](healthkit-images/image13.png#lightbox)

按**记录**按钮。 这将导致`StoreData_TouchUpInside()`处理程序以运行，将尝试分析值`heartRate`文本字段中，转换到`HKQuantity`通过前面所述`HeartRateModel.HeartRateInBeatsPerMinute()`函数并传递到该数量`HeartRateModel.StoreHeartRate()`。 如前文所述，这将尝试将数据存储，并将引发或者`HeartRateStored`或`ErrorMessageChanged`事件。

双击**主页**按钮在你的设备上并打开运行状况的应用。 单击**源**选项卡上，你将看到列出的示例应用。 选择它，不允许更新数据的核心速率的权限。 双击**主页**按钮并切换回你的应用程序。 同样，`ReactToHealthCarePermissions()`将调用，但这一次，由于访问被拒绝， **StoreData**按钮将被禁用 （请注意，这在以异步方式发生，并且用户界面中的更改可能会对最终用户可见）。

## <a name="advanced-topics"></a>高级主题

读取数据从运行状况工具包数据库是非常类似于将数据写入： 一个指定的一个尝试访问，请求授权的数据类型，以及授予该授权后，数据是否可用，使用自动转换为兼容的单位度量值。

有大量的更复杂的查询函数允许基于谓词的查询和更新相关的数据时执行更新的查询。 

运行状况工具包应用程序的开发人员应查看 Apple 的运行状况工具包部分[应用查看准则](https://developer.apple.com/app-store/review/guidelines/#healthkit)。

后可理解的安全性和类型系统模型、 存储和读取共享的运行状况工具包数据库中的数据是非常简单。 许多运行状况工具包中的函数以异步方式运行，并且应用程序开发人员必须相应地编写其程序。

截止本文撰写时，目前尚无等效项为 Android 或 Windows Phone 中的运行状况工具包。

## <a name="summary"></a>摘要

在本文中我们已了解如何运行状况工具包允许应用程序存储，检索、 和共享运行状况相关的信息，同时还提供标准的运行状况应用程序允许用户访问和控制此数据。 

此外，我们还已了解如何隐私、 安全性和数据完整性要重写与运行状况相关的信息的问题并使用运行状况工具包的应用程序必须处理增加的应用程序管理方面 （预配） 编码 （运行状况工具包的类型中的复杂系统），和用户体验 （通过系统对话框和运行状况应用程序的权限的用户控件）。 

最后，我们讲到使用的包含的示例应用程序将检测信号数据写入运行状况工具包存储，异步感知设计的运行状况工具包的简单实现。

## <a name="related-links"></a>相关链接

- [HKWork （示例）](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
