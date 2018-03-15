---
title: "导航"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ec91a7c100f294437bb1498fcd56a35f5b19c399
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="navigation"></a>导航

Xamarin.Forms 包括页面导航、 从用户的交互，与 UI 或从应用本身由于内部逻辑驱动状态更改时，通常会对支持。 但是，导航非常复杂，无法使用模型-视图-视图模型 (MVVM) 模式中，应用程序中实现，必须满足以下挑战：

-   如何识别视图以导航到，使用不会引入的紧密耦合和视图之间的依赖关系的方法。
-   如何协调依据实例化和初始化视图以导航到的过程。 当使用 MVVM，视图和视图模型需要实例化并通过视图的绑定上下文相互关联。 当应用程序正在使用依赖关系注入容器时，视图和查看模型的实例化可能需要的特定构造机制。
-   是否要执行的第一个视图的导航窗格中，或查看模型的第一个导航。 与第一个视图的导航窗格中，页后，可以导航到引用的视图类型的名称。 在导航窗格中，指定的视图是实例化，以及其对应的视图模型和其他依赖服务。 另一种方法是使用视图模型第一个导航页后，可以导航到其中引用视图模型类型的名称。
-   如何为完全分隔跨视图和查看模型的应用程序的导航行为。 MVVM 模式可以提供应用程序的 UI 和其演示文稿和业务逻辑之间的分隔。 但是，应用程序的导航行为通常将跨越应用程序的 UI 和演示文稿部分。 用户通常将启动从视图中，导航和视图将被替换为作为导航结果。 但是，导航可能通常还需要启动或从协调视图模型中。
-   如何将参数传递期间为了进行初始化的导航。 例如，如果用户导航到要更新订单详细信息的视图，将具有的订单数据传递到视图中，以便它可以显示正确的数据。
-   如何合作导航窗格中，以确保遵守某些业务规则。 例如，可能会在离开视图，以便他们可以更正无效的任何数据，或会提示您提交或放弃所做的视图中的任何数据更改前提示用户。

本章通过提供来应对这些挑战`NavigationService`用于执行视图模型第一页导航的类。

> [!NOTE]
> `NavigationService`由应用程序仅用于执行内容页实例之间的层次结构导航。 使用服务其他页面类型之间进行导航可能导致意外行为。

## <a name="navigating-between-pages"></a>页面之间导航

导航逻辑可以驻留在视图的代码隐藏，或在数据绑定视图模型。 虽然将导航逻辑放在视图中可能非常简单的方法，它不是通过单元测试可轻松地测试。 将导航逻辑放在视图模型类意味着，通过单元测试，可以执行过逻辑。 此外，视图模型可以实现对控件导航，以确保会实施某些业务规则的逻辑。 例如，应用程序可能不允许用户离开页面但未事先确保输入的数据是否有效。

A`NavigationService`类通常调用从视图模型，以提升可测试性。 但是，导航到视图中查看模型，将需要引用视图和特别活动视图模型未与，不推荐这种操作关联的视图的视图模型。 因此，`NavigationService`显示此处作为目标后，若要导航到指定的视图模型类型。

EShopOnContainers 移动应用程序使用`NavigationService`类以提供视图模型第一个导航。 此类实现`INavigationService`接口，下面的代码示例中所示：

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

此接口指定实现的类必须提供以下方法：

|方法|目标|
|--- |--- |
|`InitializeAsync`|当启动应用程序时，请执行导航到两个页面之一。|
|`NavigateToAsync`|执行分层导航到指定页。|
|`NavigateToAsync(parameter)`|执行指定的页上，将参数传递到层次结构导航。|
|`RemoveLastFromBackStackAsync`|从导航堆栈中删除前一页。|
|`RemoveBackStackAsync`|从导航堆栈中删除所有的前一页。|

此外，`INavigationService`接口指定实现的类必须提供`PreviousPageViewModel`属性。 此属性返回与前一页导航堆栈中关联的视图模型类型。

> [!NOTE]
> `INavigationService`接口通常还指定`GoBackAsync`方法，用于以编程方式返回到以前的页面导航堆栈中。 但是，此方法是缺少 eShopOnContainers 移动应用程序，因为它不是必需的。

### <a name="creating-the-navigationservice-instance"></a>创建 NavigationService 实例

`NavigationService`类，该类实现`INavigationService`接口，注册为单一实例与 Autofac 依赖关系注入容器，如下面的代码示例中所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`接口中已解决`ViewModelBase`类构造函数，如下面的代码示例中所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

这将返回的引用`NavigationService`由创建 Autofac 依赖关系注入容器中存储的对象`InitNavigation`中的方法`App`类。 有关详细信息，请参阅[导航时在应用启动](#navigating_when_the_app_is_launched)。

`ViewModelBase`类存储`NavigationService`实例中`NavigationService`类型的属性， `INavigationService`。 因此，所有查看模型类，该类派生自`ViewModelBase`类中，可以使用`NavigationService`属性访问由指定的方法`INavigationService`接口。 这可以避免将注入的开销`NavigationService`从 Autofac 依赖关系注入容器到每个视图模型类的对象。

### <a name="handling-navigation-requests"></a>处理导航请求

Xamarin.Forms 提供了[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)类，该类实现用户处于能够向前和向后，根据需要翻页导航分层导航体验。 有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

而不是使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)类直接，eShopOnContainers 应用包装`NavigationPage`类`CustomNavigationView`类，如下面的代码示例中所示：

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

此包装的目的是为了便于样式[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)内部 XAML 文件中为类的实例。

导航通过调用其中一个执行视图模型类`NavigateToAsync`方法，指定正在导航到，如下面的代码示例中所示的页面的视图模型类型：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下面的代码示例演示`NavigateToAsync`提供方法`NavigationService`类：

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

每个方法允许派生自任何视图模型类`ViewModelBase`类通过调用执行分层导航`InternalNavigateToAsync`方法。 此外，第二个`NavigateToAsync`方法允许将导航数据指定为参数传递给要导航到，它通常用于执行初始化视图模型。 有关详细信息，请参阅[将参数传递期间导航](#passing_parameters_during_navigation)。

`InternalNavigateToAsync`方法执行导航请求，并在下面的代码示例所示：

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync`方法执行导航到视图模型通过首先调用`CreatePage`方法。 此方法查找对应于指定的视图模型类型，并创建并返回此视图类型的实例的视图。 查找对视图模型类型相对应的视图使用基于约定的方法，这假定：

-   视图是在视图模型类型相同的程序集中。
-   视图是。视图子命名空间。
-   查看模型都中。Viewmodel 子命名空间。
-   视图名称对应于"模型"中删除与查看模型名称。

实例化视图时，它都有其相应的视图模型进行关联。 有关如何发生这种情况的详细信息，请参阅[自动向视图模型定位器创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

如果正在创建的视图是`LoginView`，在的新实例中包装`CustomNavigationView`类，并分配给[ `Application.Current.MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)属性。 否则为`CustomNavigationView`实例检索，并提供其不为 null， [ `PushAsync` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)调用方法以将推送到导航堆栈正在创建的视图。 但是，如果检索`CustomNavigationView`实例是`null`，正在创建的视图包装到的新实例`CustomNavigationView`类，并分配给`Application.Current.MainPage`属性。 此机制可确保，在导航页将添加正确到导航堆栈为空，当和包含数据时。

> [!TIP]
> 请考虑缓存页。 缓存的页面将生成当前未显示的视图的内存消耗中。 但是，而无需页缓存它意味着每次新的页导航到，从而产生复杂的页的性能影响将发生 XAML 分析和页面和其视图模型的构造。 对于不使用过多的控件的设计良好网页，性能应足够。 但是，在页面缓存可能有助于，如果遇到慢的页面加载时间。

创建并导航到，该视图后`InitializeAsync`执行的此视图关联的视图模型的方法。 有关详细信息，请参阅[将参数传递期间导航](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>导航时的应用程序启动

当启动应用程序时，`InitNavigation`中的方法`App`调用类。 下面的代码示例演示此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

该方法将创建一个新`NavigationService`Autofac 依赖关系注入容器中的对象并返回对它，然后再调用的引用其`InitializeAsync`方法。

> [!NOTE]
> 当`INavigationService`通过解决接口`ViewModelBase`类，容器返回的引用`NavigationService`调用 InitNavigation 方法时创建的对象。

下面的代码示例演示`NavigationService``InitializeAsync`方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`导航到，如果应用程序缓存的访问令牌，用于身份验证。 否则为`LoginView`导航到。

有关 Autofac 依赖关系注入容器的详细信息，请参阅[简介依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>在导航过程传递参数

之一`NavigateToAsync`方法，指定`INavigationService`接口，启用导航数据指定为参数传递给要导航到，它通常用于执行初始化视图模型。

例如，`ProfileViewModel`类包含`OrderDetailCommand`当用户选择订单上执行`ProfileView`页。 反过来，这样可以执行`OrderDetailAsync`方法，下面的代码示例中所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

此方法调用导航到`OrderDetailViewModel`，并传递`Order`实例，表示用户选择的顺序`ProfileView`页。 当`NavigationService`类创建`OrderDetailView`、`OrderDetailViewModel`实例化类并将其分配给该视图的[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。 后导航到`OrderDetailView`、`InternalNavigateToAsync`方法执行`InitializeAsync`视图的方法的关联的视图模型。

`InitializeAsync`中定义方法`ViewModelBase`作为一种方法可以重写的类。 此方法指定`object`表示导航操作期间要传递给视图模型的数据的自变量。 因此，想要从导航操作接收数据的视图模型类提供的它们自己实现`InitializeAsync`方法来执行所需的初始化。 下面的代码示例演示`InitializeAsync`方法从`OrderDetailViewModel`类：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

此方法检索`Order`实例，在导航操作时，传递到视图模型并使用它来检索完整的顺序的详细信息`OrderService`实例。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>使用行为的调用导航

导航通常由用户交互触发从视图。 例如，`LoginView`执行以下成功通过身份验证的导航。 下面的代码示例演示如何导航调用行为：

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

在运行时，`EventToCommandBehavior`将响应与交互[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)。 当`WebView`导航到 web 页， [ `Navigating` ](https://developer.xamarin.com/api/event/Xamarin.Forms.WebView.Navigating/)事件将激发，其将执行`NavigateCommand`中`LoginViewModel`。 默认情况下，会将事件的事件自变量传递给命令。 此数据将转换源和目标之间传递中指定转换器`EventArgsConverter`属性，它返回[ `Url` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebNavigationEventArgs.Url/)从[ `WebNavigatingEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebNavigatingEventArgs/)。 因此，当`NavigationCommand`是执行，网页的 Url 作为参数传递到已注册`Action`。

反过来，`NavigationCommand`执行`NavigateAsync`方法，下面的代码示例中所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

此方法调用导航到`MainViewModel`，并按照导航窗格中，删除`LoginView`导航堆栈中的页。

### <a name="confirming-or-cancelling-navigation"></a>确认或取消导航

应用程序可能需要与用户交互在导航操作中，以便用户可以确认或取消导航。 这可能是有必要，例如，当用户尝试在之前已完全完成数据进入页导航。 在此情况下，应用程序应提供通知，它允许用户导航离开页面，或者它发生之前取消导航操作。 这可以通过在视图模型类中使用来自通知的响应来控制调用导航。

## <a name="summary"></a>摘要

Xamarin.Forms 包括页面导航、 从用户界面时，用户的交互或从该应用程序自身，由于进行了逻辑驱动的内部状态更改时，通常会对支持。 但是，导航非常复杂，无法使用 MVVM 模式应用程序中实现。

本章介绍`NavigationService`类，用于执行从查看模型的视图模型第一个导航。 将导航逻辑放在视图模型类意味着，通过自动测试，可以执行过逻辑。 此外，视图模型可以实现对控件导航，以确保会实施某些业务规则的逻辑。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
