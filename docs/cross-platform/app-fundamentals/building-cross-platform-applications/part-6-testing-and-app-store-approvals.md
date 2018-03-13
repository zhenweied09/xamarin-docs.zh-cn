#<a name="---"></a>---
标题:"一部分 6-测试和应用商店审批"ms.topic: article ms.prod: xamarin ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B ms.technology: xamarin 跨平台作者： asb3993 ms.author: amburns ms.date: 03/23/2017年
---

# <a name="part-6---testing-and-app-store-approvals"></a>第 6-部分测试和应用商店审批

<a name="Testing" />


## <a name="testing"></a>正在测试

许多应用程序 （甚至上的 Android 应用，某些存储） 将需要通过审批过程，然后在发布;使测试是很关键，以确保你的应用程序达到市场 （更不用说与您的客户成功）。 测试可以采用许多形式，从开发人员级单元测试，以管理跨各种硬件的测试。

 <a name="Test_on_All_Platforms" />


### <a name="test-on-all-platforms"></a>在所有平台上进行测试

略有差异之间.NET 支持 Windows phone、 平板电脑和桌面设备，以及防止动态生成动态代码的 iOS 上的限制。 或者在计划测试多个平台上的代码，因为你开发，或计划重构的时间和更新你的应用程序项目的末尾的模型部分。

它始终是模拟器/仿真程序用于测试的操作系统以及不同的设备功能/配置多个版本的好办法。

你还应在任意数量的不同的物理硬件设备，因为你可以进行测试。

 <a name="Devices_in_cloud" />


#### <a name="devices-in-cloud"></a>在云中的设备

移动电话和平板电脑生态系统在不断增加所有的时间，使其无法在不断增长的可用设备数上进行测试。 若要解决此问题大量服务，请提供远程控制许多不同的设备，以便应用程序可以安装和测试而无需直接投入大量硬件的功能。

[应用中心测试](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)提供一种简单的方法来测试 iOS 和 Android 应用程序在数百个不同设备上的。

 <a name="Test_Management" />


### <a name="test-management"></a>测试管理

当测试你的组织或使用外部用户管理测试计划中的应用程序，有两个难题：

-   **分发**– 管理设置过程 （特别是对于 iOS 设备） 和测试中获取的软件的更新的版本。
-   **反馈**– 可能发生的任何错误可以收集有关应用程序使用情况信息和详细的信息。


有大量的服务可以帮助解决这些问题中，通过提供基础结构，这内置到应用程序以收集和报告使用情况和错误，并还简化设置过程以帮助注册并管理测试人员和他们的设备.

[Xamarin Insights 预览](http://xamarin.com/insights)为此问题，提供崩溃报告和复杂应用程序使用情况信息的第二部分提供了解决方案。


 <a name="Test_Automation" />


### <a name="test-automation"></a>测试自动化

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)可以用于创建测试脚本，以便可以本地运行或上载到的自动化的用户界面[应用 Center 测试](https://docs.microsoft.com/appcenter/test-cloud/)。


 <a name="Unit_Testing" />


## <a name="unit-testing"></a>单元测试

 <a name="Touch.Unit" />


#### <a name="touchunit"></a>Touch.Unit

Xamarin.iOS 包括调用 Touch.Unit 按照编写测试 JUnit/NUnit 样式的单元测试框架。

请参阅我们[使用 Xamarin.iOS 进行单元测试](~/ios/deploy-test/touch.unit.md)有关编写的测试，运行 Touch.Unit 的详细信息的文档。

 <a name="Andr.Unit" />


#### <a name="andrunit"></a>Andr.Unit

没有适用于 Android 调用 Andr.Unit Touch.Unit 开放源代码等效项。 你可以下载它从[github](https://github.com/spouliot/Andr.Unit)和上阅读有关该工具[@spouliot的博客](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/)。

 <a name="Windows_Phone" />


#### <a name="windows-phone"></a>Windows Phone

下面是一些链接，以帮助安装程序进行单元测试 Windows Phone:

-   [http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/](http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/)
-   [http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/](http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/)
-   [http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7](http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7)
-   [http://mobile.dzone.com/articles/unit-testing-your-windows](http://mobile.dzone.com/articles/unit-testing-your-windows)


&nbsp;

 <a name="App_Store_Approvals" />


## <a name="app-store-approvals"></a>应用商店审批

Apple 和 Microsoft 操作平台上的唯一存储： 应用商店和 Marketplace 分别。 同时使用锁定其设备并实施严格的应用程序查看过程，以控制应用程序可供下载的质量。 Android 的打开性质意味着有大量的从 Google Play，是广泛可用并且有没有审核过程中，为 Android 和特定于硬件的恢复所需的已分发的更受限制的 Samsung 应用的 Amazon 的 Appstore 的存储选项和实现审批过程。

等待要审阅的应用可以是大的压力-商业压力通常意味着与在"目标"发布日期之前的错误很少距的应用程序提交以供审核。 过程本身可能需要最多两周，不一定是透明： 有限的反馈上没有你的应用程序的进度直到最后被拒绝或批准它。 尤其是它的发生一次和周之间原始的启动日期传递和最后批准应用程序时拒绝可能意味着缺少的机会，市场营销窗口。

 <a name="Be_prepared" />


### <a name="be-prepared"></a>准备

建议的第一条： 提前规划你的应用程序启动并进行补偿可能拒绝并重新提交。 每个存储要求你在提交您的应用程序之前创建帐户-尽早执行此操作。
Google Play 注册仅需几分钟，如果你的应用程序可用，而进程获取大量更多地涉及，如果您销售产品，并且需要提供银行和税务信息。 Apple 和 Microsoft 的进程是同时比 Google 的复杂得多，它可能需要一周或详细信息以获得批准帐户因此等因素如何影响这一次启动计划。

你的帐户具有批准后，你已准备好提交应用程序。 以下文档中介绍了提交应用程序的实际过程：

-   [发布到 Apple 的 iOS 应用商店](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
-   [对于 Google Play 准备应用](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
-  Windows 开发人员应访问[Windows 开发人员中心](https://developer.microsoft.com/en-us/windows/windows-apps)若要了解提交其应用。


本部分的其余部分讨论你应当考虑以确保你的应用程序批准而无需任何短暂的操作。

 <a name="Quality" />


### <a name="quality"></a>质量

这听起来很明显，但应用程序将通常被拒绝，因为它们不符合特定级别的质量： 毕竟，这是为什么策展的存储具有审批过程首先原因 ！

崩溃是拒绝的常见原因。 如果它是很容易会导致应用出现故障，它具有保证被拒绝。 大多数开发人员未提交的假定条件下，它们将崩溃，但它们通常实现其应用。 提交它，将精力集中不只是在确保一切正常，但还你处理常见的移动的错误情况，例如网络问题和资源约束，如内存或存储空间之前，应彻底测试你的应用程序。 使用模拟器和物理设备测试-无论程度代码在模拟器中运行，只有设备可以演示应用程序的实际性能。 使用任意多个不同的设备，因为你可以查找，并登记 beta 测试人员的团队，如果你可以-第三方服务可帮助管理 beta 分布和反馈。

所有的移动操作系统将终止不足够快的速度启动应用程序。 允许的时间长度而异，但在一般情况下应用的目标应是进行中几秒钟后处于响应状态，并使用后台任务来完成任何工作将花较长时间。 应用程序花费太长时间才能加载或不足够的响应能力中经常使用的是将被拒绝。 在后台，内容发生的情况或应用程序将显示已损坏，同样，被拒绝时，始终提供用户反馈。

 <a name="Check_Your_Edge_Cases" />


### <a name="check-your-edge-cases"></a>检查边缘情况

开发人员的常见陷阱故障到地址边缘的情况下，尤其是那些需要重新配置其模拟器或设备可供测试正确。 将很容易忘记，并非每个客户会转到"允许"您的应用程序能够访问它们的位置，因为开发人员已接受请求一次后，它们将永远不会再次提示输入。 权限和网络使用情况是专门 focussed 上在批准过程中，这意味着这些领域内的小监督可能导致拒绝。

下面的列表是一个很好的起始点，检查可能已错过的边缘情况：

-   **客户可能拒绝对服务的访问**-尤其是在 iOS 中，对数据的访问，例如，如果用户授予对你的应用程序的权限，则仅会提供地理位置信息。 应用程序测试人员经常应重新安装它的初始状态中的应用程序，不允许任何权限请求，以确保应用程序运行正常。 在切换权限并关闭以验证正确的行为，如客户改变其主意。
-   **客户无处**– 不要想当然地城市或国家/地区开发它将仅使用某个应用 ！ 可与 GPS 坐标、 日期和时间值和货币的代码可以所有受客户的位置和区域设置。 所有平台都可以提供，你可以指定不同的位置和区域设置的模拟器都使用它来测试其他两个半球，并且具有不同设置日期和货币的格式的区域性的位置。 纬度和经度值可以是正数或负数、 小数分隔符可能是一个句点或逗号，和日期可进行格式设置大量的方式-处理与其 ！
-   **网络连接速度缓慢**– 应用程序开发人员通常在理想 world 快速、 始终使用网络连接，显然不是这种情况在现实世界中工作。 测试与慢速网络连接 （如较差的 3g 连接） 以及无法访问网络是至关重要的确保你不提供错误的应用程序。 审批过程将始终与设备处于飞行模式将包含测试，因此请确保已测试了，为您自己。
-   **硬件而异**– 请记住，在你打算支持的最旧、 最慢硬件上进行测试。 有可能影响你的应用的两个方面： 性能，这可能是在较旧的设备和硬件功能，如相机、 麦克风、 GPS、 陀螺或其他可选组件的支持上不可用。 应用程序应会降低正常 （和不崩溃） 时组件是不可用。


 <a name="Guidelines_are_more_than_just_a_‘guide’" />


### <a name="guidelines-are-more-than-just-a-guide"></a>准则是远不止指南 》

Apple 是有名正在严格遵守其人机接口指南有关，因为其平台的关键优势之一是一致性 （和可用性的感知的增加）。 Microsoft 已与实现 Metro 风格用户界面的 Windows 应用程序采取类似的方法。 这两个平台的审批流程会涉及到您的应用程序在其遵守相关的设计理念进行评估。

这并不是说，用户界面创新不受支持或建议，但你"只是不应执行操作"的某些事项，或你的应用程序将被拒绝。

在 iOS 上这包括误用内置图标或使用其他确立的隐喻以非一致的方式;有关创建新的内容之外的任何内容，例如使用编写图标。

Windows 开发人员应同样小心;一个常见错误失败以完全支持硬件返回根据 Microsoft 的指导原则的按钮。

鼓励你的设计器，阅读并按照每个平台的设计准则。

 <a name="Implementing_Platform-Specific_Features" />


### <a name="implementing-platform-specific-features"></a>实现特定于平台的功能

尤其是在 iOS 上实现特定于平台的服务时，情况就稍有更严格。 若要避免通过 Apple 的自动拒绝，有一些规则来接下来可使用以下 iOS 功能：

-   **应用内购买**– 应用程序必须实现数字产品包括中的游戏货币、 应用程序功能、 杂志订阅和更多的外部支付机制。 iOS 应用必须使用 Apple 的 iTunes 基于服务，用于此类功能。 没有一个漏洞-如 Kindle 读取器和某些基于订阅的应用程序，可以购买其他位置的内容获取附加到"客户"可以通过应用来访问应用程序，但是在这种情况下不包含应用程序必须链接或对引用扩展的应用购买过程 （或，同样，将被拒绝）。
-   **iCloud 备份**– iCloud 随着，Apple 的审阅者更严格关于了应用如何使用存储资源 （以确保客户的远程备份体验是愉快）。 应用程序，废物可备份的存储空间可能被拒绝，因此使用的缓存文件夹相应地，并按照 Apple 的其他与存储相关的准则。
-   **Newsstand** – 报纸和杂志应用为非常适合于 Apple 的 Newsstand，但应用程序必须实现至少一个自动续订订阅和支持后台下载将被批准。
-   **映射**– 将叠加和其他功能添加到移动映射，但是请注意不影响地图信用额度信息 （如 iOS5 的 Google 徽标） 因为这样会导致拒绝的做法越来越普遍。


 <a name="Manage_Your_Metadata" />


### <a name="manage-your-metadata"></a>管理你的元数据

除了明显的技术问题可能会导致应用程序被拒绝，还有一些您可能会导致拒绝，尤其是在元数据 （说明、 关键字和市场营销映像） 你的提交的一些更难以捉摸方面提交你的应用程序在应用商店或应用商店中的显示。

-   **图像**– 遵循该平台的指南，应用程序图标并存储图片。 不要使用商标字的映像，我们已了解应用程序获取拒绝，因为其图标特色的 iPhone 绘图 ！
-   **商标**– 请避免使用你自己的以外的任何商标。 应用具有已拒绝针对一提的是应用程序说明中，或甚至在 Apple 的应用商店的关键字的商标。
-   **说明**– 不使用 word beta 或以任何方式指示应用程序未准备好进行质数时间。 （即使你的应用程序是跨平台的），未涉及其他移动平台。 最重要的是，请确保应用程序的功能完全什么你假定它执行。 如果你在您的说明中列出了大量功能，它具有更好地很明显如何使用这些功能的每个，否则将显示"未实现应用程序的说明中提到的功能"拒绝。


将放到开发和测试应用程序的元数据到尽可能多工作量。 获取应用程序拒绝的元数据中的次要侵权行为，因此值得花时间使其正确。

 <a name="App_Stores:_Not_For_Everyone" />


### <a name="app-stores-not-for-everyone"></a>应用商店： 不适用于所有人

每个平台上的存储的主要重点是使用者分发-能够尽可能访问尽可能多的客户。 面向但是并非所有应用程序使用者，在没有快速增长的需要有限的分发给员工、 供应商或客户的内部和 extranet 类似的应用程序的基类。 这些应用程序不是"for 销售"，并且不需要批准，因为开发人员控件分发给已关闭的一组用户。
部署此类型的支持因平台而异。

Android 提供最大的灵活性，在这一点: （只要该设备的配置允许它），可以直接从 URL 或电子邮件附件安装应用程序。 这意味着它是普通创建并分发内部企业应用程序或应用程序发布到特定客户或供应商。

Apple 在 iOS Developer Enterprise Program，也不能绕过应用商店审批过程，公司可以分配给他们的员工的内部应用中注册的开发人员提供一个内部部署选项。
遗憾的是此许可证不能解决需 extranet 类似的应用程序分发到客户或供应商的其他已关闭组。 [企业版 （和临时） 部署](~/ios/deploy-test/app-distribution/ipa-support.md)


 <a name="App_Store_Summary" />


### <a name="app-store-summary"></a>应用商店摘要

评审过程可能会极其困难的但如开发生命周期的其余部分可帮助确保成功使用一些规划和注意到详细信息。 其所有涉及到几个简单步骤： 阅读并理解用户界面指南必须遵循以，遵循的规则，如果你要实现特定于平台的功能，应彻底测试 （然后测试更多） 和最后确保你的应用程序元数据在提交之前，则是正确的。

一个最后一个单词的 Google Play 上发布的开发人员的建议： 在缺乏审批过程可能看起来就像它使您的工作更轻松的但你的客户会评审团队的更多要求。 如你的应用程序无法获取拒绝，否则它将为你执行拒绝的客户，请遵循以下准则。

