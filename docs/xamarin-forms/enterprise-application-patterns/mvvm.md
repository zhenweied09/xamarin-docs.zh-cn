---
title: 模型-视图-视图模型模式
description: 本章介绍 eShopOnContainers 移动应用程序如何使用 MVVM 模式来完全分离其用户界面中的应用程序的业务和演示文稿逻辑。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fe2cace6a0fc3a1d901f55556eed09380f8f2006
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245427"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型-视图-视图模型模式

在 XAML 中，创建用户界面，然后添加的用户界面进行操作的代码隐藏，通常涉及到 Xamarin.Forms 开发人员的体验。 在应用程序进行修改，和增长大小和范围内时，会产生复杂的维护问题。 这些问题包括 UI 控件和业务逻辑，这会增加使 UI 修改和困难的此类代码进行单元测试的成本之间的紧密耦合。

模型-视图-视图模型 (MVVM) 模式可帮助完全分隔其用户界面 (UI) 中的应用程序的业务和演示文稿逻辑。 维护应用程序逻辑和 UI 之间完全分离有助于解决许多开发问题，并且可以使应用程序更轻松地测试、 维护和发展。 它还将大大提高代码重用的机会，并允许开发人员和开发其相应部分的应用程序时，UI 设计器能够更轻松地进行协作。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三个核心组件： 模型、 视图和视图模型。 每个用于不同目的。 图 2-1 显示了三个组件之间的关系。

![](mvvm-images/mvvm.png "MVVM 模式")

**图 2-1**: MVVM 模式

除了外了解每个组件的责任，我们还必须了解与彼此交互的方式。 在高级别，视图"就会了解有关"视图模型和视图模型"就会了解有关"模型，但模型不知道的视图模型中，并且视图模型不能识别视图。 因此，视图模型隔离从模型中，视图，并允许独立于视图时的发展方式的模型。

使用 MVVM 模式的好处如下所示：

-   如果没有现有模型实现封装现有业务逻辑，它可以是难以查看或更改它的风险。 在此方案中，视图模型充当模型类适配器，并使你可以避免对模型代码进行任何重大更改。
-   开发人员可以创建单元测试视图模型和模型，而无需使用该视图。 视图模型的单元测试可以运用所使用的视图相同的功能。
-   前提是完全在 XAML 中实现视图，则可以不必触动代码，重新设计应用程序 UI。 因此，新视图的版本应使用的现有视图模型。
-   设计人员和开发人员可以处理独立和并发其组件在开发过程中。 在开发人员可以使用视图模型和模型组件的同时，设计者可以专注于该视图。

有效地使用 MVVM 的关键在于了解如何考虑为正确的类中，应用程序代码并了解如何进行交互的类。 以下各节讨论的每个 MVVM 模式中的类的责任。

### <a name="view"></a>视图

视图是负责定义结构、 布局和外观的用户所见的内容在屏幕上。 理想情况下，每个视图在 XAML 中，定义有有限代码隐藏的不包含业务逻辑。 但是，在某些情况下，隐藏代码可能包含用户界面逻辑实现难以在 XAML 中，例如动画 express 的可视行为。

在 Xamarin.Forms 应用中，视图通常是[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-派生或[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)-派生类。 但是，也可以由数据模板，它指定要用于以可视方式表示的对象，它显示时的 UI 元素表示视图。 为视图的数据模板不具有任何代码隐藏，并旨在将绑定到特定视图模型类型。

> [!TIP]
> 避免启用和禁用 UI 元素的代码隐藏文件中。 请确保查看模型都负责定义会影响该视图的显示器，例如命令是否可用，或指示操作处于挂起状态的某些方面的逻辑状态更改。 因此，启用和禁用 UI 元素的绑定，以查看模型属性，而不是启用和禁用它们，代码隐藏文件中。

有多种选项可使在交互在视图中，响应中的视图模型上执行代码，例如单击按钮或项选择。 如果控件支持命令，该控件`Command`属性可以是数据绑定到`ICommand`视图模型上的属性。 调用控件的命令时，将执行视图模型中的代码。 除了命令，行为可以附加到视图中的对象，并且可以侦听要调用的命令或引发事件。 在响应中，然后可以调用行为`ICommand`视图模型或视图模型上的方法上。

### <a name="viewmodel"></a>视图模型

视图模型实现属性和该视图可以数据绑定的命令，并通知通过更改通知事件的任何状态更改的视图。 属性和视图模型提供的命令中定义 UI，通过提供的功能，但视图确定该功能的显示方式。

> [!TIP]
> 使 UI 保持响应状态，使用异步操作。 移动应用程序应保持以提高用户的角度看，性能不阻止 UI 线程。 因此，在视图模型中，对于 I/O 操作中使用异步方法和引发事件以异步通知的属性更改视图。

视图模型也是负责协调与任何模型类所需的视图的交互。 通常是视图模型和模型类之间的一个对多关系。 视图模型可以选择公开直接到视图模型类，以便让控件视图中的可用于将直接与它们的数据绑定。 在这种情况下，模型类将需要用于支持数据绑定并更改通知事件。

每个视图模型提供从该视图可以轻松地使用窗体中的模型的数据。 若要实现此目的，视图模型有时执行数据转换。 将此数据转换放在视图模型是一个不错的主意，因为它提供了该视图可以将绑定到的属性。 例如，视图模型可能组合两个属性，以简化显示视图的值。

> [!TIP]
> 集中转换层中的数据转换。 还有可能要转换器用作视图模型和视图之间位于一个单独的数据转换层。 这可以是必要的例如，当数据需要特殊格式设置也不提供视图模型。

为了使视图模型参与与视图的双向数据绑定，其属性必须引发`PropertyChanged`事件。 查看模型通过实现满足此要求`INotifyPropertyChanged`接口，然后引发`PropertyChanged`属性更改时的事件。

对于集合，对视图友好`ObservableCollection<T>`提供。 此集合实现的集合更改通知，这使不必开发人员无需实现`INotifyCollectionChanged`对集合的接口。

### <a name="model"></a>模型

模型类是封装应用程序的数据的非视觉类。 因此，该模型可以被视为表示应用程序的域模型中，通常包括数据模型以及业务和验证逻辑。 模型对象的示例包括数据传输对象 (Dto)、 纯旧 CLR 对象 (POCOs) 和生成的实体和代理对象。

结合服务或存储库，用于封装各种数据访问和缓存通常使用模型类。

## <a name="connecting-view-models-to-views"></a>连接到视图的视图模型

视图模型可以连接到视图中，通过使用 Xamarin.Forms 的数据绑定功能。 有多种方法可以用于构造视图和查看模型并将它们关联在运行时。 这些方法分为两类，称为复合视图第一个和第一个复合视图模型。 第一个复合视图和模型的第一个组合是的首选项和复杂性的问题的视图之间进行选择。 但是，所有方法都共享相同的目的是，这是具有分配给其 BindingContext 属性视图模型的视图。

与视图第一个组合应用程序从概念上讲组成连接到它们依赖于查看模型的视图。 此方法的主要优点是，它能够轻松地构造松散耦合，单元可测试应用因为视图模型具有不依赖于本身的视图。 也很容易通过按照其可视的结构，而无需跟踪代码执行，以了解如何创建和关联类了解应用程序的结构。 此外，视图的第一个构造符合 Xamarin.Forms 导航系统负责构建页导航时，这使得视图模型的第一个组合复杂且与平台未对齐。

与视图模型第一个组合应用程序从概念上讲视图模型组成，与服务正在负责查找视图模型的视图。 视图模型第一个组合感觉有些开发人员，更自然，因为视图创建可以为抽象，从而允许它们能够专注于应用程序的逻辑非 UI 结构。 此外，它允许查看模型创建的其他视图模型。 但是，此方法通常很复杂，可能会难以理解如何创建和关联的应用程序的各个部分。

> [!TIP]
> 使视图模型和视图独立。 绑定到数据源中的属性的视图应为其相应的视图模型上的视图的主体依赖项。 具体而言，不引用视图类型，如[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)和[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)，从视图模型。 按照此处所述的原则，查看模型可以进行测试以隔离方式，因此通过限制范围减少软件缺陷的可能性。

以下各节讨论连接到视图的查看模型的主要方式。

### <a name="creating-a-view-model-declaratively"></a>以声明方式创建视图模型

最简单方法是以声明方式实例化其相应的视图模型，在 XAML 中的视图。 构造视图时，将还在构造相应的视图模型对象。 在下面的代码示例演示了这种方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

当[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)创建的一个实例`LoginViewModel`自动进行构造并将设置为视图的[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。

此声明性构造和分配由视图的视图模型的优点它很简单，但它需要视图模型中的默认 （无参数） 构造函数的缺点。

### <a name="creating-a-view-model-programmatically"></a>以编程方式创建视图模型

可以分配给视图模型会导致代码隐藏文件中具有代码视图其[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)属性。 这是通常视图的构造函数中完成，如下面的代码示例中所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

以编程方式构造和视图模型中的视图的代码隐藏的分配都有很简单的优点。 但是，此方法的主要缺点是需要可以为任何必需的依赖项提供视图模型视图。 使用依赖关系注入容器可帮助维护松散耦合的视图和视图模型之间。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>创建视图定义为数据模板

视图可以定义为数据模板，并与视图模型类型关联。 数据模板可定义为资源，或它们可以是控件将显示视图模型内的以内联方式定义。 控件的内容就是视图模型实例，并且数据模板用于以可视方式表示。 此方法是在该视图模型实例化首先，视图的创建后跟的情况下一个示例。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>自动向视图模型定位器创建视图模型

视图模型定位符是管理的查看模型和其关联到视图的实例化的自定义类。 在 eShopOnContainers 移动应用中，`ViewModelLocator`类具有一个附加的属性， `AutoWireViewModel`，用于将视图模型与视图相关联。 在视图的 XAML 中，此附加的属性设置为 true，以指示视图模型应自动连接到视图中，如下面的代码示例中所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`属性是可绑定属性的已初始化为 false，并且其值更改时`OnAutoWireViewModelChanged`调用事件处理程序。 此方法会解析视图的视图模型。 下面的代码示例演示如何实现这的：

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

`OnAutoWireViewModelChanged`方法尝试解析视图模型使用基于约定的方法。 此约定可假定：

-   查看模型都在视图类型相同的程序集中。
-   视图是。视图子命名空间。
-   查看模型都中。Viewmodel 子命名空间。
-   视图模型名称与视图名称的对应关系，并以"ViewModel"结尾。

最后，`OnAutoWireViewModelChanged`方法设置[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)到解析的视图模型类型的视图类型。 有关解决视图模型类型的详细信息，请参阅[解析](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

此方法具有应用程序必须具有单个类，它负责的查看模型和其连接到视图的实例化的优点。

> [!TIP]
> 为了便于替换使用视图模型定位符。 视图模型定位符也可作为替换备用实现的依赖关系，如单元测试或设计时间数据。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>在响应中的基础的更改更新视图查看模型

所有视图模型和访问视图的模型类应都实现`INotifyPropertyChanged`接口。 在视图模型或模型类中实现此接口允许类以提供到任何数据绑定的控件视图中的更改通知，基础属性值更改时。

应用应由满足以下条件构建的属性更改通知的正确用法：

-   始终引发`PropertyChanged`如果公共属性的值发生更改的事件。 不会假定该引发`PropertyChanged`由于 XAML 绑定如何发生的情况的知识可以忽略事件。
-   始终引发`PropertyChanged`任何事件计算其值由在视图中的其他属性的属性模型。
-   始终引发`PropertyChanged`末尾的方法，使属性更改，或当已知对象处于安全状态的事件。 引发事件通过以同步方式调用事件处理程序将中断该操作。 如果操作期间发生这种情况，它可能会处于不安全，已部分更新状态时公开的回调函数的对象。 此外，它是级联更改触发的可能`PropertyChanged`事件。 级联更改通常需要更新的级联更改安全地执行之后才能完成。
-   永远不会引发`PropertyChanged`如果属性不会更改的事件。 这意味着你必须在引发之前比较新旧值`PropertyChanged`事件。
-   永远不会引发`PropertyChanged`期间视图模型的构造函数，如果要初始化属性的事件。 不具有订阅视图中的数据绑定控件，若要在此点接收更改通知。
-   永远不会引发多个`PropertyChanged`事件和类的公共方法的单个同步调用中将相同属性名称自变量。 例如，给定`NumberOfItems`其后备存储为的属性`_numberOfItems`字段中，如果方法递增`_numberOfItems`五十时间期间执行循环时，它应只引发属性更改通知上`NumberOfItems`属性一次，所有工作完成之后。 异步方法，引发`PropertyChanged`异步延续链中的每个同步段中的给定的属性名称的事件。

EShopOnContainers 移动应用程序使用`ExtendedBindableObject`类以提供更改通知，这下面的代码示例中所示：

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Xamarin.Form 的[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)类实现`INotifyPropertyChanged`接口，并提供[ `OnPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.OnPropertyChanged/p/System.String/)方法。 `ExtendedBindableObject`类提供`RaisePropertyChanged`方法来调用属性更改通知，并在此情况下使用提供的功能`BindableObject`类。

EShopOnContainers 移动应用程序中的每个视图模型类派生自`ViewModelBase`类，该类又派生自`ExtendedBindableObject`类。 因此，每个视图模型类使用`RaisePropertyChanged`中的方法`ExtendedBindableObject`类以提供属性更改通知。 下面的代码示例演示如何 eShopOnContainers 移动应用程序使用 lambda 表达式调用属性更改通知：

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

请注意，在这种方式中使用 lambda 表达式包括小的性能开销，原因是 lambda 表达式的计算每个调用。 虽然的性能开销很小，并且通常不会影响应用程序，则在有许多更改通知时，可以会产生费用。 但是，此方法的好处在于，它提供编译时类型安全和重构支持，在重命名属性时。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行为的用户界面交互

在移动应用中，操作通常调用以响应用户操作，例如单击按钮，可以通过在代码隐藏文件中创建事件处理程序实现。 但是，在 MVVM 模式中，实现操作的责任在于视图模型中，并且应当避免放置代码的代码隐藏文件中。

命令提供一种简便的方式来表示可绑定到控件在 UI 中的操作。 它们封装实现操作的代码，并有助于保持它从视图中其可视表示形式分离。 Xamarin.Forms 包括控件可以以声明方式连接到一个命令，并在用户与控件交互时，这些控件将调用该命令。

行为还允许以声明方式连接到命令的控件。 但是，可以使用行为来调用由控件引发的事件的范围与关联的操作。 因此，行为可以解决许多为命令启用控件的同一方案时提供更高程度的灵活性和控制。 此外，行为，还可以使用来将命令对象或方法关联了不是专门设计与命令进行交互的控件。

### <a name="implementing-commands"></a>实现命令

查看模型通常公开命令属性，从视图中，绑定所实现的对象实例`ICommand`接口。 大量的 Xamarin.Forms 控件提供`Command`属性，可以是数据绑定到`ICommand`视图模型提供的对象。 `ICommand`接口定义`Execute`方法，它封装操作本身，`CanExecute`方法，该值指示是否可以调用该命令，与`CanExecuteChanged`更改出现影响是否时发生的事件应执行该命令。 [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)和[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)提供的 Xamarin.Forms，这类实现`ICommand`接口，其中`T`是的自变量的类型`Execute`和`CanExecute`。

在视图模型中，应类型的对象[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)或[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)类型的视图模型中每个公共属性`ICommand`。 `Command`或`Command<T>`构造函数需要`Action`具有时调用的回调对象`ICommand.Execute`调用方法。 `CanExecute`方法是一个可选的构造函数参数，并为`Func`返回`bool`。

下面的代码演示如何[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)通过指定的委托构造实例，它表示注册命令，`Register`查看模型方法：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

该命令就会遭受通过返回的引用的属性的视图`ICommand`。 当`Execute`方法调用[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)对象，它只需将转发到视图模型通过中指定的委托中的方法调用`Command`构造函数。

异步方法可以通过使用调用的命令`async`和`await`关键字指定该命令时`Execute`委托。 这表示是回调`Task`和应等待。 例如，下面的代码演示如何[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)通过指定的委托构造实例，它表示在登录命令，`SignInAsync`查看模型方法：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

可以将参数传递到`Execute`和`CanExecute`使用操作[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)类实例化该命令。 例如，下面的代码演示如何`Command<T>`实例用于指示`NavigateAsync`方法则会要求类型自变量`string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在这两[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)和[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)类到委托`CanExecute`中每个构造函数的方法是可选的。 如果未指定委托，`Command`将返回`true`为`CanExecute`。 但是，视图模型可以指示该命令中的更改`CanExecute`通过调用状态`ChangeCanExecute`方法`Command`对象。 这将导致`CanExecuteChanged`引发事件。 然后，任何用户界面中的控件绑定到该命令将更新其已启用的状态以反映数据绑定命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>调用视图中的命令

下面的代码示例演示如何[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)中`LoginView`将绑定到`RegisterCommand`中`LoginViewModel`类通过[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)实例：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

命令参数也可以根据需要定义使用[ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.CommandParameter/)属性。 预期自变量的类型中指定`Execute`和`CanExecute`目标方法。 [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)将自动调用目标命令，在用户交互与附加的控件时。 命令参数，如果提供，作为参数传递给命令的`Execute`委托。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>实现行为

行为允许功能添加到 UI 控件，而无子类化它们。 相反，功能是行为类中实现并附加到控件，就像它是该控件本身的一部分。 行为，可以实现你通常需要编写作为代码隐藏，因为它直接与 API 中的方式。 可以确切地附加到控件，并跨多个视图或应用程序打包以供重复使用的控件进行交互的代码。 在上下文中 MVVM，行为是很有用的方法，用于连接到命令的控件。

附加到通过附加属性的控件的行为称为*附加行为*。 然后，该行为可以使用公开的 API，它附加到将功能添加到该控件或该视图的可视化树中的其他控件的元素。 EShopOnContainers 移动应用包含`LineColorBehavior`类，它是一个附加的行为。 有关此行为的详细信息，请参阅[显示验证错误](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

Xamarin.Forms 行为是派生自的类[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，其中`T `是行为将应用到该控件的类型。 这些类提供`OnAttachedTo`和`OnDetachingFrom`方法，应重写以提供该行为是附加到，从控件中分离时，将执行的逻辑。

在 eShopOnContainers 移动应用中，`BindableBehavior<T>`类派生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类。 用途`BindableBehavior<T>`类旨在为需要的 Xamarin.Forms 行为的基类[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)设置为附加的控件的行为。

`BindableBehavior<T>`类提供可重写`OnAttachedTo`设置的方法[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的行为，且可以重写`OnDetachingFrom`方法清除的`BindingContext`。 此外，类存储到中的附加控件的引用`AssociatedObject`属性。

EShopOnContainers 移动应用程序包括`EventToCommandBehavior`类，该类在事件发生时的响应中执行命令。 此类派生自`BindableBehavior<T>`类，使该行为可以绑定到，执行`ICommand`指定的`Command`属性时使用行为。 以下代码示例演示 `EventToCommandBehavior` 类：

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

`OnAttachedTo`和`OnDetachingFrom`方法用于注册和取消注册的事件处理程序中定义的事件`EventName`属性。 然后，当事件触发，`OnFired`调用方法时，其中执行该命令。

使用的优点`EventToCommandBehavior`执行命令时触发事件时，是命令可以与未设计为与命令进行交互的控件相关联。 此外，此事件处理将代码移动到查看模型，它可以在其中进行单元测试。

#### <a name="invoking-behaviors-from-a-view"></a>调用视图中的行为

`EventToCommandBehavior`很适合用于将命令连接到不支持命令的控件。 例如，`ProfileView`使用`EventToCommandBehavior`执行`OrderDetailCommand`时[ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)上激发事件[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ，它列出用户的订单，如所示在下面的代码：

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

在运行时，`EventToCommandBehavior`将响应与交互[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 当中选择一项`ListView`、 [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)事件将激发，其将执行`OrderDetailCommand`中`ProfileViewModel`。 默认情况下，会将事件的事件自变量传递给命令。 此数据将转换源和目标之间传递中指定转换器`EventArgsConverter`属性，它返回[ `Item` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemTappedEventArgs.Item/)的`ListView`从[ `ItemTappedEventArgs`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemTappedEventArgs/). 因此，当`OrderDetailCommand`执行时，所选`Order`作为参数传递到已注册的操作。

有关各种行为的详细信息，请参阅[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>总结

模型-视图-视图模型 (MVVM) 模式可帮助完全分隔其用户界面 (UI) 中的应用程序的业务和演示文稿逻辑。 维护应用程序逻辑和 UI 之间完全分离有助于解决许多开发问题，并且可以使应用程序更轻松地测试、 维护和发展。 它还将大大提高代码重用的机会，并允许开发人员和开发其相应部分的应用程序时，UI 设计器能够更轻松地进行协作。

使用 MVVM 模式的应用程序 UI 和基础的演示文稿和业务逻辑分为三个单独的类： 视图，它封装用户界面和用户界面逻辑;封装表示逻辑和状态，则为与视图模型和封装应用程序的业务逻辑和数据模型。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
