---
title: SiriKit
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/07/2017
ms.openlocfilehash: a712f7da0d57e81872d7f779cf0eb52c30ee3bc2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="sirikit"></a>SiriKit

SiriKit 在 iOS 10，引入了大量的服务 （包括锻炼，持续一段时间预订，以及调用） 的域。 请参阅[SiriKit 部分](~/ios/platform/sirikit/index.md)SiriKit 概念以及如何在你的应用程序中实现 SiriKit。

![Siri 任务列表演示](sirikit-images/sirikit.png)

在 iOS 11 SiriKit 添加这些新的和更新的意向域：

- [**列出并说明**](#listsnotes) – 新 ！ 提供对于应用程序使用 API 处理任务和说明。
- **可视化代码**– 新 ！ Siri 可以显示要共享的联系信息或参与付款交易的 QR 代码。
- **付款**– 添加付款交互的搜索和传输意向。
- **写预订**– 添加取消持续一段时间和反馈意向。

其他新功能包括：

- [**备用的应用名称**](#alternativenames) – 提供别名，以帮助客户告诉 Siri 通过提供其他名称/发音面向你的应用程序。
- **启动锻炼**– 提供在后台启动测验的功能。

下面解释了其中一些功能。 有关其他详细信息，请参阅[Apple 的 SiriKit 文档](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes" />

## <a name="lists-and-notes"></a>列表和说明

新的列表和说明域将提供一个 API，使应用程序来处理任务和通过使用 Siri 语音请求的说明。

**任务**

- 具有标题和完成状态。
- 根据需要包括截止时间和位置。

**备注**

- 具有标题和内容的字段。

任务和说明可以组织到组中。 本部分的其余部分介绍如何实现 SiriKit，此新的域使用[TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)。

### <a name="how-to-process-a-sirikit-request"></a>如何处理 SiriKit 请求

处理 SiriKit 请求通过执行以下步骤：

1. **解决**– 验证参数和请求更多用户信息 （如果需要）。
2. **确认**– 最终验证和验证可以处理该请求。
3. **处理**– 执行操作 （更新数据或执行网络操作）。

前两个步骤是可选 （尽管建议使用），则需要执行最后一个步骤。
更多详细说明，在[SiriKit 部分](~/ios/platform/sirikit/index.md)。

### <a name="resolve-and-confirm-methods"></a>解决并确认方法

这些可选的方法让你从用户执行验证，选择默认值或请求其他信息的代码。

例如，对于`IINCreateTaskListIntent`接口，所需的方法是`HandleCreateTaskList`。 有四个提供更好地控制 Siri 交互的可选方法：

- `ResolveTitle` – 验证标题、 设置默认标题 （如果适用），或发出信号，不需要的数据。
- `ResolveTaskTitles` – 验证用户说出的任务的列表。
- `ResolveGroupName` – 验证组名称、 选择一组默认或发出信号，不需要的数据。
- `ConfirmCreateTaskList` – 验证你的代码可以执行请求的操作，但不会执行它 (仅`Handle*`方法应修改数据)。

### <a name="handle-the-intent"></a>句柄意图

有六个意向列表和说明域中的，三个用于任务和三个用于说明。
为处理这些意向而必须实现这些方法包括：

- 对于任务：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 说明：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每个方法具有特定意向类型传递给它，其中包含使用 Siri 从用户的请求已分析的所有信息 (可能在并更新`Resolve*`和`Confirm*`方法)。
你的应用程序必须分析的数据提供，则执行某些操作来存储或以其他方式处理数据，并返回结果，Siri 说出并向用户显示。

### <a name="response-codes"></a>响应代码

所需`Handle*`和可选`Confirm*`方法指示的响应代码，它们将传递对对象的值设置为其完成处理程序。 响应来自`INCreateTaskListIntentResponseCode`枚举：

- `Ready` – 在确认阶段返回 (即。 从`Confirm*`方法，但不是能从`Handle*`方法)。
- `InProgress` – 用于长时间运行任务 （如网络/服务器操作）。
- `Success` – 与成功的操作的详细信息进行响应 (仅从`Handle*`方法)。
- `Failure` – 意味着出错，并且无法完成该操作。
- `RequiringAppLaunch` -无法处理的意图，但是该操作可能在应用程序。
- `Unspecified` -不要使用： 将向用户显示错误消息。

了解有关这些方法和在 Apple 的响应[SiriKit 列出并说明文档](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>实现列表和说明

[TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)使用以下步骤将 SiriKit 支持添加到空白 iOS 应用创建的。

首先，添加 SiriKit 支持，请执行以下步骤为你的 iOS 应用：

1. 刻度**SiriKit**中**Entitlements.plist**。
2. 添加**隐私 – Siri 使用率描述**键，以**Info.plist**，以及你的客户的消息。
3. 调用`INPreferences.RequestSiriAuthorization`在应用中，以提示用户允许使用 Siri 交互的方法。
4. 将 SiriKit 添加到你在开发人员门户上的应用程序 ID 并重新创建你预配配置文件以包括新的授权。

然后将新的扩展项目添加到你的应用能处理 Siri 请求：

1. 右键单击你的解决方案，然后选择**添加 > 添加新项目...**.
2. 选择**iOS > 扩展 > 意向扩展**模板。
3. 将添加两个新项目： 意向和 IntentUI。 自定义 UI 是可选的因此该示例仅包含中的代码**意向**项目。

扩展项目是将其中处理所有 SiriKit 请求。 为单独的扩展，它不自动具有任何方式来与主应用程序 – 通信通常解决这个问题通过实现共享的文件存储使用应用组。

#### <a name="configure-the-intenthandler"></a>配置 IntentHandler

`IntentHandler`类是入口点，对于使用 Siri 请求 – 每个意向传递给`GetHandler`方法，返回一个可以处理该请求的对象。

下面的代码演示一个简单的实现：

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

此类必须继承自`INExtension`，因为示例转到处理列表和说明意向，它还会实现`IINNotebookDomainHandling`。

> [!NOTE]
> - 接口的.NET 带有大写字母前缀中没有约定`I`，其中从 iOS SDK 绑定协议时 Xamarin 遵循。
> - Xamarin 还会保留从 iOS 的类型名称，Apple 将前两个字符用在类型名称以反映具有类型属于 framework。
> - 有关`Intents`框架中，将类型前缀为`IN*`（如。 `INExtension`) 但它们是_不_接口。
> - 它还会依照，协议 （就在 C# 中的接口） 结束，两个`I`s，如`IINAddTasksIntentHandling`。

#### <a name="handling-intents"></a>处理颜色

每个意向 （添加任务、 设置任务属性，等等） 的实现单个方法类似于下面所示。 此方法应执行三项主要功能：

1. **处理意图**– 通过使用 Siri 分析的数据中可`intent`对象特定于类型的意图。 你的应用程序可能验证了该数据使用可选`Resolve*`方法。
2. **验证和更新数据存储区**– 保存到文件系统 （使用应用组以便主 iOS 应用程序也可以访问它），或通过网络请求的数据。
3. **提供响应**– 使用`completion`处理程序以将响应发送回 Siri 读取/显示给用户：

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

请注意，`null`传递的第二个参数的响应 – 这是用户活动参数，并且在未提供时, 将使用默认值。
你可以设置自定义活动类型处理程序，但前提是你的 iOS 应用支持通过`NSUserActivityTypes`中的键**Info.plist**。 然后，你可以打开你的应用程序时，处理这种情况，并执行特定操作 （如打开到相关的视图控制器以及使用 Siri 操作从加载的数据）。

该示例还 hardcodes`Success`结果，但在实际方案中，应添加正确的错误报告。

### <a name="test-phrases"></a>测试短语

以下测试短语应在示例应用程序中运行：

- "在 TasksNotes 使同类、 香蕉，与梨杂货列表"
- "添加任务 WWDC 中 TasksNotes"
- "添加任务 WWDC 到中 TasksNotes 培训列表"
- "标记为已完成在 TasksNotes 参加 WWDC"
- "在 TasksNotes 再提醒我时主页获取购买 iphone"
- "标记为已完成 TasksNotes 购买 iPhone"
- "提醒我要在上午 8 中 TasksNotes 主页保留"

![创建新的列表示例](sirikit-images/createtasklist-sml.png) ![设置任务作为完整示例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> 测试与使用 Siri （而不像早期版本中），iOS 11 模拟器支持。
>
> 如果测试在真实设备上，不要忘记配置你应用程序 ID 和预配 SiriKit 支持的配置文件。

<a name="alternativenames" />

## <a name="alternative-names"></a>可选名称

此新的 iOS 11 功能意味着你可以将配置应用程序，以帮助用户与使用 Siri 正确触发它的可选名称。 添加到以下项**Info.plist** iOS 应用程序项目的文件：

![Info.plist 显示替代的应用程序名称键和值](sirikit-images/alternative-names.png)

与其他应用程序名称集相同，以下短语也适用于示例应用程序 (实际名**TasksNotes**):

- "请同类、 香蕉，与在梨杂货列表_MonkeyNotes_"
- "添加任务中的 WWDC _MonkeyTodo_"


## <a name="troubleshooting"></a>疑难解答

运行示例，或将 SiriKit 添加到自己的应用程序时，你可能会遇到一些错误：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Objective C 引发异常。名称： NSInternalInconsistencyException 原因： 使用类 < INPreferences: 0x60400082ff00 > 从应用程序需要授权 com.apple.developer.siri。是否 Xcode 项目中已启用 Siri 功能？_

- 中勾选了 SiriKit **Entitlements.plist**。
- **Entitlements.plist**中配置**项目选项 > 生成 > iOS 捆绑签名**。

  [![显示权利正确设置的项目选项](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- （适用于设备部署）应用程序 ID 已启用的 SiriKit 和预配配置文件下载。


## <a name="related-links"></a>相关链接

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [什么是新建中 SiriKit (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/214/)
