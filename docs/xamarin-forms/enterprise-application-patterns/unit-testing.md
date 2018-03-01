---
title: "单元测试"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0842ce24139da5d963e2c528b440e6592d5910f8
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="unit-testing"></a>单元测试

移动应用都具有唯一桌面和基于 web 的应用程序无需担心的问题。 移动用户，它们使用，通过网络连接，由服务的可用性以及各种其他因素的设备将有所不同。 因此，应该测试的移动应用程序，因为它们将使用在现实生活中以提高其质量、 可靠性和性能。 有应该在应用程序，包括单元测试、 集成测试，以及进行单元测试所测试的最常见形式的用户界面执行的许多类型的测试。

单元测试采用小的一项应用程序中，通常的方法，将它隔离的其余代码，从并验证其行为与预期相同。 其目标是检查每个功能单元执行按预期方式运行，以便不将整个应用中传播错误。 检测的 bug 出现的位置会更加高效观察间接在失败的辅助点 bug 的效果。

软件开发工作流的组成部分时，单元测试将具有最大的影响代码质量。 一旦已写入一个方法，则应编写单元测试用于验证到标准、 边界和不正确情况的输入数据的响应中的方法和检查的行为的代码所做的任何显式或隐式假设。 或者，使用测试驱动开发，单元测试的代码之前编写。 在此方案中，单元测试用作设计文档和功能规范。

> [!NOTE]
> 单元测试是非常有效的防范回归 –，即用于工作，但已分布式通过更新失败的功能。

单元测试通常使用排列 act 断言模式：

-   *排列*的单元测试方法的部分初始化对象并设置传递给待测试方法的数据的值。
-   *作用*部分调用具有所需的参数待测试方法。
-   *断言*部分验证待测试方法的操作行为与预期相同。

遵循此模式可确保单元测试是具可读性且更一致。

## <a name="dependency-injection-and-unit-testing"></a>依赖关系注入和单元测试

采用松散耦合的体系结构的动机之一是它便于单元测试。 注册 Autofac 的类型之一是`OrderService`类。 下面的代码示例演示此类的概述：

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

`OrderDetailViewModel`类具有的依赖关系`IOrderService`类型可在容器解析时它实例化`OrderDetailViewModel`对象。 但是，而不是创建`OrderService`到单元测试的对象`OrderDetailViewModel`类，而是替换`OrderService`出于测试目的的模型对象。 图 10-1 阐释了此关系。

![](unit-testing-images/unittesting.png "实现 IOrderService 接口的类")

**图 10-1:**实现 IOrderService 接口的类

此方法允许`OrderService`对象传递到`OrderDetailViewModel`类在运行时，和的兴趣可测试性，它允许`OrderMockService`类，以将传入`OrderDetailViewModel`类在测试时间。 此方法的主要优点是它使单元测试，而无需庞大的资源，如 web 服务或数据库执行。

## <a name="testing-mvvm-applications"></a>测试 MVVM 应用程序

测试模型和从 MVVM 应用程序的视图模型等同于测试任何其他类，并可以使用相同的工具和技术 – 单元测试和模拟，如。 但是，有一些典型到模型的模式和视图模型类，可以受益于特定的单元测试方法。

> [!TIP]
> 测试与每个单元测试的一件事。 不是行为的想要使单元测试的单元的多个方面的练习。 这样会导致很难读取和更新的测试。 它还可能导致混淆，解释失败时。

EShopOnContainers 移动应用程序使用[xUnit](https://xunit.github.io/)执行单元测试，它支持两种不同类型的单元测试：

-   事实是属于始终为 true，测试的测试固定条件。
-   仅适用于一组特定的数据的测试是理论。

EShopOnContainers 移动应用中包含的单元测试事实测试，并因此每个单元测试方法用修饰`[Fact]`属性。

> [!NOTE]
> xUnit 测试通过的测试运行程序执行。 若要执行测试运行程序，运行所需平台 eShopOnContainers.TestRunner 项目。

### <a name="testing-asynchronous-functionality"></a>测试异步功能

实现 MVVM 模式时，查看模型通常以异步方式通常调用在服务上，操作。 测试通常调用这些操作的代码用作 mock 替换的实际服务。 下面的代码示例演示如何通过将模拟服务传入到视图模型测试异步的功能：

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

此单元测试，检查`Order`属性`OrderDetailViewModel`实例将具有值后的`InitializeAsync`调用方法。 `InitializeAsync`在视图模型的相应视图导航到时均会调用方法。 有关导航的详细信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

当`OrderDetailViewModel`创建实例，期望`OrderService`实例指定为参数。 但是，`OrderService`从 web 服务中检索数据。 因此，`OrderMockService`实例，这是模拟版本的`OrderService`类中，指定为参数`OrderDetailViewModel`构造函数。 然后，当视图模型`InitializeAsync`调用方法时，这样就可调用`IOrderService`操作，模拟数据是检索，而不是与 web 服务通信。

### <a name="testing-inotifypropertychanged-implementations"></a>测试 INotifyPropertyChanged 实现

实现`INotifyPropertyChanged`接口允许视图以响应来自视图的更改模型和模型。 这些更改并不局限于控件中显示数据 – 它们还用于控制视图中，如导致动画要启动或要禁用的控件的视图模型状态。

可以直接通过单元测试更新的属性可以通过附加到事件处理程序测试`PropertyChanged`事件并检查是否在设置属性的新值后引发该事件。 下面的代码示例演示此类的测试：

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

此单元测试时，将调用`InitializeAsync`方法`OrderViewModel`类，这将导致其`Order`要更新的属性。 单元测试将通过，前提`PropertyChanged`为引发事件`Order`属性。

### <a name="testing-message-based-communication"></a>测试基于消息的通信

视图模型使用[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类通信松散耦合类之间可以进行单元测试通过订阅到受测情况下，代码正在发送的消息，如下面的代码示例中所示：

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

此单元测试，检查`CatalogViewModel`发布`AddProduct`消息以响应其`AddCatalogItemCommand`正在执行。 因为[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类支持多播的消息订阅、 单元测试可以订阅`AddProduct`消息，然后接收它的响应中执行的回调委托。 此回调委托，指定为 lambda 表达式，设置`boolean`字段由`Assert`语句以验证测试的行为。

### <a name="testing-exception-handling"></a>测试异常处理

单元测试也可以写入检查特定异常引发的无效操作或所有输入，如下面的代码示例中所示：

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

此单元测试，将会引发异常，因为[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控件不具有名为事件`OnItemTapped`。 `Assert.Throws<T>`方法是泛型方法其中`T`是预期异常的类型。 自变量传递给`Assert.Throws<T>`方法是一个 lambda 表达式，将引发异常。 因此，将传递单元测试，前提是 lambda 表达式将引发`ArgumentException`。

>💡 **提示**： 避免编写检查异常消息字符串的单元测试。 异常消息字符串随着时间推移，可能会更改，因此依赖于其存在状态的单元测试它们被认为是薄弱。

### <a name="testing-validation"></a>测试验证

测试验证实现的两个方面： 测试正确实现的任何验证规则，并测试`ValidatableObject<T>`类按预期执行。

验证逻辑是通常简单，若要测试，，因为它通常是一个自包含的过程，其中输出取决于输入。 上调用的结果应测试`Validate`上具有至少一个的关联的验证规则，如下面的代码示例中所示的每个属性的方法：

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

此单元测试，检查验证成功时两个`ValidatableObject<T>`中的属性`MockViewModel`这两个实例具有的数据。

检查验证成功，以及验证单元测试还应检查的值`Value`， `IsValid`，和`Errors`每个属性`ValidatableObject<T>`实例，以验证类按预期执行。 下面的代码示例演示了可完成此单元测试：

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

此单元测试，检查验证失败时`Surname`属性`MockViewModel`没有任何数据，与`Value`， `IsValid`，和`Errors`每个属性`ValidatableObject<T>`是否正确设置了实例。

## <a name="summary"></a>摘要

单元测试采用小的一项应用程序中，通常的方法，将它隔离的其余代码，从并验证其行为与预期相同。 其目标是检查每个功能单元执行按预期方式运行，以便不将整个应用中传播错误。

可以通过将替换 mock 模拟依赖对象的行为的对象的依赖对象隔离所测试的对象的行为。 这使单元测试，而无需庞大的资源，如 web 服务或数据库执行。

测试模型和从 MVVM 应用程序的视图模型等同于测试任何其他类，并可以使用相同的工具和技术。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
