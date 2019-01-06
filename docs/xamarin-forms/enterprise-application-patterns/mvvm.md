---
title: 模型-视图-视图模型模式
description: 本章介绍 eShopOnContainers 移动应用程序如何使用 MVVM 模式从应用程序的用户界面中清晰隔离业务和展现逻辑。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: c947ec0c2fffbd9038ee58211c77bd947c445b6e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998437"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型-视图-视图模型模式

Xamarin.Forms 开发人员通常通过在 XAML 创建用户界面，并且在 code-behind 文件中添加代码来操作用户界面。 随着应用程序进行修改，代码量和范围的增加，可能出现复杂的维护问题。 这些问题包括 UI 控件和业务逻辑之间的紧密耦合，这会增加修改 UI 的代价和进行单元测试的难度。

模型-视图-视图模型 (MVVM) 模式有助于从应用程序的用户界面 (UI) 清晰隔离业务和展现逻辑。 维护应用程序逻辑与 UI 之间的清晰隔离有助于解决许多开发问题，并可以使应用程序更易于测试、 维护和改进。 它还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发一个应用程序的各自部分时更轻松地进行协作。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三个核心部分： 模型（Model）、 视图（View）和视图模型（ViewModel）。 每个用于不同用途。 图 2-1 显示了三个部分之间的关系。

![](mvvm-images/mvvm.png "MVVM 模式")

**图 2-1**:: MVVM 模式

除了了解每个部分的职责，还有必要了解它们如何互相交互。 概括的来说，视图"知道"视图模型的存在，以及视图模型"知道"模型，但模型不会意识到视图模型的存在，并且视图模型也不会意识到视图。 因此，视图模型把视图从模型中隔离开来，并允许模型独立于视图发展。

使用 MVVM 模式的好处是，如下所示：

-   如果一个现有模型实现封装了现有业务逻辑，它可以难以修改或者带来的风险较高。 在这样的情景下，视图模型可以充当模型类的适配器，并使你可以避免对模型代码进行任何重大更改。
-   开发人员可以为视图模型和模型创建单元测试，而无需使用对应的视图。 针对视图模型的单元测试可以运用跟视图中所使用的一致的功能。
-   应用程序 UI 可以被重新设计，而不必触动代码，前提是该视图完全在 XAML 中实现。 因此，新版本的视图应该适用于已有的视图模型。
-   设计人员和开发人员在开发过程中可以独立地并且同时在各自的部分工作。 设计人员可以集中精力在视图中，而开发人员可以工作在视图模型和模型部分。

有效地使用 MVVM 的关键在于了解如何将应用代码放到的正确类，并了解这些类如何进行交互。 以下章节将讨论每个类在 MVVM 模式中的职责。

### <a name="view"></a>视图

视图负责定义结构、 布局和外观的用户在屏幕上看到的内容。 理想情况下，每个视图都在 XAML 中定义，具有有限的 code-behind 代码，不包含业务逻辑。 但是，在某些情况下，code-behind 代码可能包含一些 XAML 中难以表达的实现了可视行为的 UI 逻辑，如动画。

在 Xamarin.Forms 应用程序中，视图通常是从[ `Page` ](xref:Xamarin.Forms.Page)-派生或[ `ContentView` ](xref:Xamarin.Forms.ContentView)-派生的类。 但是，视图也可以通过数据模板来表示，在显示对象时，它指定UI元素以可视化方式表示对象。 数据模板作为视图不具有任何 code-behind 代码，旨在绑定到特定的视图模型类型。

> [!TIP]
> 避免在 code-behind 代码中启用和禁用 UI 元素。 请确保视图模型负责定义那些在某些方面会影响视图显示的逻辑状态变更，例如一个命令是否可用，或指示一个操作处于挂起状态。 因此，应通过绑定视图模型属性来启用和禁用UI元素，而不是在 code-behind 代码中启用和禁用它们。

有多个方式来执行视图模型上的代码以响应在视图上的交互，如按钮单击或选择某个条目。 如果一个控件支持命令，那么该控件的`Command`属性可以被数据绑定到视图模型的`ICommand`属性上。当控件的命令被调用时，将执行在视图模型中的代码。 除了命令，行为也可以被附加到视图中的对象，并且可以侦听要调用的命令或被引发的事件。 作为响应，这些行为然后可以调用视图模型上的`ICommand`或方法。

### <a name="viewmodel"></a>视图模型

视图模型实现的属性和命令可以数据绑定到视图，并通过变更通知事件来通知视图关于任何状态的更改。 视图模型提供的属性和命令定义了可提供给 UI 的功能，但视图决定了这些功能的显示方式。

> [!TIP]
> 使用异步操作来保持 UI 响应。 移动应用程序应保持 UI 线程通畅来提高用户对性能的感知度。 因此，在视图模型中，异步方法用于 I/O 操作并引发事件异步地通知视图进行属性变更。

视图模型也负责协调视图与所需的一些模型类之间的交互。 视图模型和模型类之间通常是一对多的关系。 视图模型可能会选择直接公开模型类给视图，以便在视图中的控件可以直接与这些模型进行数据绑定。 在这种情况下，模型类将需要被设计为支持数据绑定以及变更通知事件。

每个视图模型从模型获取数据并以容易读写的方式提供给视图。 若要实现此目的，视图模型有时会执行数据转换。 将此数据转换放入视图模型是一个好主意，因为它提供给试图可以实现绑定的属性。 例如，视图模型可能组合两个属性，以简化在视图上显示。

> [!TIP]
> 把数据转换集中在转换层。 也可以利用转换器作为一个位于视图模型与视图之间的独立数据转换层。 这也许是有必要的，例如，当数据需要特殊格式设置而视图模型不提供时。

为了使视图模型参与跟视图双向数据绑定，其属性必须引发`PropertyChanged`事件。 视图模型通过实现`INotifyPropertyChanged`接口来满足这个要求，并在属性变更时引发`PropertyChanged`事件。

对于集合，则为其提供了对视图友好的`ObservableCollection<T>`类。 此集合实现集合变更通知，从而避免开发人员在集合上实现`INotifyCollectionChanged`的接口。

### <a name="model"></a>模型

模型类是封装应用程序的数据的非可视类。 因此，该模型可以认为的代表应用的领域模型，通常包括数据模型以及业务和验证逻辑。 模型对象的示例包括数据传输对象 (Dto)、 普通旧 CLR 对象 (Poco) 和生成的实体以及代理对象。

模型类通常与封装数据访问和缓存的服务 (services) 或存储库（repositories）一起使用。

## <a name="connecting-view-models-to-views"></a>连接视图模型到视图

视图模型可以通过使用 Xamarin.Forms 的数据绑定功能连接到视图中。 有许多方法可用于构造视图和视图模型，并将其在运行时相关联。 这些方法划分为两个类别，称为视图优先（view first）组合和视图模型优先 (view model first) 组合。 两者之间的选择取决于编好和复杂度。 但是，所有方法都有一个相同的目的，这就是要有给视图的 BindingContext 属性分配一个视图模型。

对于视图优先组合的应用程序从概念上讲是由视图以及连接到它们所依赖的视图模型组成。 这种方法的主要优点是，它可用于轻松地构建松散耦合，可单元测试应用程序因为视图模型不依赖于视图本身。 它也很容易遵循其可视结构来了解应用程序的结构，而无需跟踪代码执行来了解类如何创建和关联。 此外，视图优先的构造方法符合 Xamarin.Forms 导航系统，它负责在导航发生时构造页面，这样可使视图模型优先组合的方法变得复杂且与平台偏离。

对于视图模型优先组合的应用程序从概念上讲是由视图模型组成，有一个服务来负责查找对应的视图给视图模型。 视图模型优先组合对于有些开发人员来说感觉更自然，因为视图创建可以被抽象化，让他们可以专注于应用程序的非 UI 逻辑结构。 此外，它允许视图模型通过其他视图模型来创建。 但是，这种方法通常比较复杂并且它可能会让了解应用程序的各个部分如何创建和关联变得困难。

> [!TIP]
> 保持视图模型和视图独立。 绑定视图到数据源中的属性应该是视图对于其相应的视图模型的主要依赖。 具体而言，不要从视图模型引用视图类型，如[ `Button` ](xref:Xamarin.Forms.Button)和[ `ListView` ](xref:Xamarin.Forms.ListView)。 按照此处列出的原则，视图模型可以被隔离测试，因此通过限制作用域来减少软件缺陷的可能性。

以下各节讨论连接视图模型到视图的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以声明方式创建视图模型

最简单方法是在视图 XAML 中以声明方式实例化其对应的视图模型。 当构造视图时，还将构造相应的视图模型对象。 下面的代码示例演示了此方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

当[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)创建时，一个`LoginViewModel`的实例会自动构造并设置为视图的[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。

此声明性构造和由该视图分配的视图模型的优点是它很简单，但它的缺点是需要视图模型中有一个默认 （无参数） 构造函数。

### <a name="creating-a-view-model-programmatically"></a>以编程方式创建视图模型

视图可以在 code-behind 文件中有用于分配视图模型给其 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性的代码。 通常在视图的构造函数中完成此操作，如下面的代码示例中所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

在视图的 code-behind 文件中以编程方式构造和分配视图模型的优点是简单。 但是，这种方法的主要缺点是视图需要向视图模型提供所需依赖项。 使用依赖关系注入容器有助于在视图和视图模型之间保持松散耦合。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>创建一个定义为数据模板的视图

视图可以被定义为一个数据模板并与一个视图模型类型相关联。 数据模板可以定义为资源，或者它们可以在将要显示视图模型的控件中以内联方式定义。 控件的内容是视图模型的实例，并且数据模板用于以可视化的方式表示它。 这种方法是在视图模型优先实例化，然后创建该视图的情况下的一个示例。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>使用视图模型定位器自动创建视图模型

视图模型定位器是管理视图模型实例化及其关联到视图的一个自定义类。 在 eShopOnContainers 移动应用中，`ViewModelLocator`类有一个附加属性， `AutoWireViewModel`，用于将视图模型与视图相关联。 在该视图的 XAML，此附加属性设置为 true 以指示视图模型应会自动连接到视图，如下面的代码示例中所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`属性是一个已初始化为 false 的可绑定属性，并且其值更改时`OnAutoWireViewModelChanged`事件处理程序会被调用。 此方法为视图解析视图模型。 下面的代码示例演示如何实现此目的：

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

`OnAutoWireViewModelChanged`方法尝试使用基于约定的方法来解析视图模型。 此约定可假定：

-   视图模型与视图类型处于相同的程序集中。
-   视图位于 .Views 子命名空间。
-   视图模型位于 .ViewModels 子命名空间。
-   视图模型名称与视图名称一致并以"ViewModel"结尾。

最后，`OnAutoWireViewModelChanged`方法设置解析到的视图模型类型到视图类型的[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 有关解析视图模型类型的详细信息，请参阅[解析方法](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

此方法具有的优点是应用具有单个类来负责视图模型的实例化及其到视图的连接。

> [!TIP]
> 使用视图模型定位器来方便替换。 视图模型定位器可以用于作为依赖项的备用实现的替换点，如单元测试或设计时数据。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新视图以响应其视图模型与模型的变更

所有在视图中可访问的视图模型和模型类都应实现`INotifyPropertyChanged`接口。 视图模型或模型类中实现此接口允许其属性值变更时提供变更通知到视图中的任何数据绑定控件。

应用程序应通过满足以下要求，以被架构为正确使用属性变更通知：

-   如果公共属性的值发生变更，则始终引发`PropertyChanged`事件。 不要因为了解 XAML 绑定如何发生就假定引发`PropertyChanged`事件可以被忽略。
-   对于任何需要通过计算的属性，其值被视图模型或模型中的其他属性使用时，则始终引发`PropertyChanged`。
-   始终在一个导致属性变更的方法的结尾处，或当对象已知处于安全状态时引发`PropertyChanged`事件。 通过以同步方式调用事件处理程序来引发事件会中断其操作。 如果操作期间发生这种情况，它可能会在对象时处于不安全的、 部分更新的状态时公开给回调函数。 此外，`PropertyChanged`事件可能会触发级联变更。 级联变更通常要求在安全地执行其（随后的）级联更改后才会在完成更新。
-   如果属性没有变更，则永远不要引发`PropertyChanged`事件。 这意味着您必须在引发`PropertyChanged`事件之前比较旧的和新的值。
-   如果你正在初始化一个属性，则永远不要在视图模型的构造函数中引发`PropertyChanged`事件。 视图中的数据绑定控件不会在此时就订阅了接收变更通知。
-   永远不要一个类的公共方法的单个同步调用中引发多个具有相同属性名称参数的`PropertyChanged`事件。 例如，给定`NumberOfItems`属性其后备存储是`_numberOfItems`字段，如果一个方法在执行一个循环的过程中对`_numberOfItems`递增50次，它应在所有工作完成后在`NumberOfItems`属性上只引发一次变更通知。 对于异步方法，在一个异步连续链的每个同步段中引发给定的属性名称的`PropertyChanged`事件。

eShopOnContainers 移动应用使用`ExtendedBindableObject`类来提供变更通知，下面的代码示例所示：

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

Xamarin.Form 的[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类实现了`INotifyPropertyChanged`接口，并提供[ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String))方法。 `ExtendedBindableObject`类提供了`RaisePropertyChanged`方法来调用属性变更通知，并在此过程中使用了`BindableObject`类提供的功能。

在 eShopOnContainers 移动应用中的每个视图模型类派生自`ViewModelBase`类，后者又派生自`ExtendedBindableObject`类。 因此，每个视图模型类使用`ExtendedBindableObject`类中的`RaisePropertyChanged`方法以提供属性变更通知。 下面的代码示例显示了如何在 eShopOnContainers 的移动应用通过使用 lambda 表达式调用属性变更通知：

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

请注意，在这种方式中使用 lambda 表达式增加了少许的性能开销，原因是 lambda 表达式必须为每个调用进行解析。 虽然性能开销很小，并且通常不会影响应用程序，但是当有很多变更通知时这些开销会累积起来。 然而，此方法的好处是，它提供了编译时类型安全和重命名属性时的重构支持。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令 (Commands) 和行为 (Behaviors) 的 UI 交互

在移动应用中，通常包括响应用户操作，例如按钮单击，可以通过在 code-behind 文件中创建一个事件处理程序来实现的调用操作。 但是，在 MVVM 模式下，实现操作的职责在于视图模型，并且应避免在 code-behind 中的放置代码。

命令提供了一种可方便地表示可以绑定到 UI 中控件的操作的方式。 它们封装实现操作的代码，并帮助以使其从视图中其可视表示形式中分离。 Xamarin.Forms 具有控件可以以声明方式连接到一个命令，并在用户与控件交互时，这些控件将调用该命令。

行为还允许控件以声明方式被连接到命令。 然而，可以使用行为调用一个操作，这个操作与由某个控件引发的一系列事件相关联。 因此，对于控件，行为就像命令一样可以解决许多相同的场景，同时提供更高的灵活性和控制。 此外，行为还可以被用于把命令对象或方法关联到那些不专门设计为与命令交互的控件。

### <a name="implementing-commands"></a>实现命令

视图模型通常公开命令属性，用于从视图中绑定，这些属性是实现`ICommand`接口的对象实例。 许多的 Xamarin.Forms 控件提供了`Command`属性，可以被数据绑定到视图模型提供的`ICommand`对象。 `ICommand`接口定义了`Execute`方法，它封装了操作本身，`CanExecute`方法，它指示是否可以调用该命令，和`CanExecuteChanged`事件，它在影响是否应执行该命令的变更发生时才出现。 [ `Command` ](xref:Xamarin.Forms.Command)和[ `Command<T>` ](xref:Xamarin.Forms.Command) 类，由 Xamarin.Forms 提供，实现了`ICommand`接口，其中`T`是`Execute`和`CanExecute`方法的参数类型。

在视图模型中，对于视图模型中每个公共属性`ICommand`都应有一个类型为[ `Command` ](xref:Xamarin.Forms.Command)或[ `Command<T>` ](xref:Xamarin.Forms.Command)的对象。 `Command`或`Command<T>`构造函数需要一个`Action`回调对象，它在`ICommand.Execute`方法被调用时被使用。 `CanExecute`方法是一个可选的构造函数参数，并是一个返回`bool`的`Func`。

下面的代码演示一个表示注册命令的[ `Command` ](xref:Xamarin.Forms.Command)实例，是如何通过指定一个委托到名为`Register`的视图模型方法来构造的：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

该命令通过一个返回`ICommand`引用的属性暴露给视图。 当`Execute`方法在[ `Command` ](xref:Xamarin.Forms.Command)对象上被调用时，它只是将该调用通过`Command`构造函数中指定的委托转发到视图模型中的方法。

当指定了一个命令的`Execute`委托时，异步方法可以通过使用`async`和`await`关键字被该命令调用。 这表示回调是一个`Task`并且应处于等待状态。 例如，下面的代码演示一个表示登录命令的[ `Command` ](xref:Xamarin.Forms.Command)实例，是如何通过指定一个委托到名为`SignInAsync`的视图模型方法来构造的：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

可以通过使用[ `Command<T>` ](xref:Xamarin.Forms.Command)类将参数传递给`Execute`和`CanExecute`操作来实例化该命令。 例如，下面的代码演示一个`Command<T>`实例是如何用于指示`NavigateAsync`方法将要求`string`类型的参数:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在[ `Command` ](xref:Xamarin.Forms.Command)和[ `Command<T>` ](xref:Xamarin.Forms.Command)类中，对于`CanExecute`方法的委托在每个构造函数中都是可选的。 如果未指定一个委托，`Command`将返回`true`为`CanExecute`。 但是，视图模型可以指示该命令的变化`CanExecute`通过调用状态`ChangeCanExecute`方法`Command`对象。 这将导致`CanExecuteChanged`事件被引发。 任何 UI 中的控件绑定到该命令将更新其已启用的状态以反映数据绑定命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>从视图调用命令

下面的代码演示了`LoginView`上的[`Grid`](xref:Xamarin.Forms.Grid)如何通过使用[`TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) 实例来绑定`LoginViewModel`类中的`RegisterCommand`：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

命令的参数也可以选择性地通过[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)属性进行定义。 期望的参数类型在`Execute`和`CanExecute`目标方法中被指定。 当用户与附加控件交互时，[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)将自动调用目标命令。 如果提供了命令的参数，将会作为实参传递给命令的`Execute`委托。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>实现行为

行为允许添加功能到 UI 控件，而无需作为它们的子类。 相反，该功能是在行为类中实现并附加到控件，就像它是控件本身的一部分。 行为，可实现您通常必须编写在code-behind中的代码，因为它直接与控件的 API 进行交互，这样可以简洁地附加到控件，并可打包以供多个视图或应用程序重复使用。 在 MVVM 的上下文中，行为是用于连接控件到命令的一种很有用的方法。

行为通过附加属性附加到控件被称为*附加行为*。 该行为可以使用它连接到的元素的公开 API 以添加功能到该控件，或该视图的可视化树中的其他控件。 eShopOnContainers 移动应用中包含`LineColorBehavior`类，该类是一个附加行为。 有关此行为的详细信息，请参阅[显示验证错误](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

一个 Xamarin.Forms 行为是派生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类的一个类，其中`T `是该行为要应用到的控件类型。 这些类提供`OnAttachedTo`和`OnDetachingFrom`方法，这些方法应被重写以提供行为被附加到控件，或从控件中分离时，将执行的逻辑。

在 eShopOnContainers 移动应用中，`BindableBehavior<T>`类派生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类。 `BindableBehavior<T>`类目的是为那些需要设置其[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)为附加的控件的 Xamarin.Forms 行为提供基类。

`BindableBehavior<T>`类提供了可重写的`OnAttachedTo`方法以设置行为的[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)，以及可重写的`OnDetachingFrom`方法来清理`BindingContext`。 此外，类还存储了附加控件的引用在`AssociatedObject`属性中。

eShopOnContainers 移动应用包含`EventToCommandBehavior`类，该类在事件发生的响应中执行命令。 此类派生自`BindableBehavior<T>`类，当使用行为时，使该行为可以绑定到或执行由`Command`属性指定的`ICommand`。 以下代码示例演示 `EventToCommandBehavior` 类：

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

`OnAttachedTo`和`OnDetachingFrom`方法用于注册或取消注册在`EventName`属性中定义的事件的事件处理程序。 然后，当触发事件时，`OnFired`被调用，它将执行已绑定的命令。

当事件触发时使用`EventToCommandBehavior`来执行命令的优点，是命令可以与那些没有被设计为使用命令进行交互的控件相关联。 此外，这将事件处理代码移动到视图模型，它可以在其中进行单元测试。

#### <a name="invoking-behaviors-from-a-view"></a>从视图调用行为

`EventToCommandBehavior`将命令附加到不支持命令的控件时特别有用。 例如，在[ `ListView` ](xref:Xamarin.Forms.ListView)上触发[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件列出用户的订单时，`ProfileView`使用`EventToCommandBehavior`执行`OrderDetailCommand`，它在下面的代码所示：

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

在运行时，`EventToCommandBehavior`将会响应与[ `ListView` ](xref:Xamarin.Forms.ListView)交互。 当在`ListView`中选择其中一项时，则会触发[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，其将执行`ProfileViewModel`中的`OrderDetailCommand`。 默认情况下，会将该事件的事件自变量传递给命令。 此数据将在`EventArgsConverter`属性中指定的转换器进行转换，就像它在转换器中的源和目标之间传递，它从[ `ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)返回`ListView`的[ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item)。因此，当`OrderDetailCommand`执行时，所选`Order`作为参数传递给已注册的操作。

有关行为的详细信息，请参阅[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>总结

模型-视图-视图模型 (MVVM) 模式有助于从应用程序的用户界面（UI）清晰地分离业务和展现逻辑。 维护应用程序逻辑与 UI 之间的清晰分离有助于解决许多开发问题，并可以使应用程序更易于测试、 维护和改进。 它还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员 在开发应用程序的相应部分时更轻松地进行协作。

使用 MVVM 模式，应用的 UI 及其之下的展现和业务逻辑被分为三个单独的类： 视图，它封装 UI 和 UI 逻辑; 视图模型，它封装展现逻辑和状态；模型，则封装了应用程序的业务逻辑和数据。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
