---
title: 依赖关系注入
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 8db8e5b756fe770bdf292ec03c28eb5ed54acf9e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="dependency-injection"></a>依赖关系注入

通常情况下，实例化对象时调用类构造函数和对象所需的任何值作为参数传递给构造函数。 这是一种依赖关系注入和专门被称为*构造函数注入*。 对象所需的依赖关系注入到构造函数。

通过指定接口类型作为依赖关系，依赖关系注入使依赖于这些类型的代码中的具体类型的分离。 它通常使用实现或扩展这些类型的具体类型并包含一系列的注册和接口和抽象类型之间的映射的容器。

也有其他类型的依赖关系注入，如*属性 setter 注入*，和*方法调用注入*，但它们不太常见。 因此，这一章将重点介绍仅执行与依赖关系注入容器的构造函数注入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>依赖关系注入简介

依赖关系注入是一个专门控制反向 (IoC) 模式，其中正在反转引人关注的是获取必需的依赖关系的过程。 使用依赖关系注入，另一个类负责将依赖关系注入到运行时处的对象。 下面的代码示例演示如何`ProfileViewModel`类在结构上使用依赖关系注入时：

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

`ProfileViewModel`构造函数接收`IOrderService`作为参数，插入由另一个类的实例。 在唯一的依赖项`ProfileViewModel`类是接口类型上。 因此，`ProfileViewModel`类不具有任何了解负责实例化的类`IOrderService`对象。 负责实例化类`IOrderService`对象，并将其到插入`ProfileViewModel`类中，称为*依赖关系注入容器*。

依赖关系注入容器减少通过提供一个工具用于实例化类实例和管理基于容器的配置其生存期的对象之间的耦合。 对象创建期间，容器将该对象需要的任何依赖关系注入到它。 如果尚未创建这些依赖关系，容器创建并首先解决它们的依赖项。

> [!NOTE]
> 此外可以手动使用工厂实现依赖关系注入。 但是，使用容器提供其他功能，例如生命期管理和扫描的程序集的注册。

有以下几个使用依赖关系注入容器优点：

-   容器中移除需类来查找其依赖项和管理其生存期。
-   容器，而不会影响类的实现依赖项的映射。
-   容器通过允许依赖项，以将模拟帮助实现可测试性。
-   容器通过允许新类，以可轻松添加到应用程序可以提高可维护性。

用于注册和解析查看模型，以及用于注册服务和注入查看模型，通常将在 Xamarin.Forms 应用编写使用 MVVM 的上下文中，使用依赖关系注入容器。

有多个依赖关系注入容器可以，用 eShopOnContainers 移动应用中使用 Autofac 管理视图模型的实例化和服务应用程序中的类。 Autofac 促进构建松耦合应用程序，并提供所有依赖关系注入容器，包括注册类型映射和对象实例的方法中的功能通常解析对象、 管理对象生存期和插入到了它解决的对象的构造函数的依赖对象。 有关 Autofac 的详细信息，请参阅[Autofac](http://autofac.readthedocs.io/en/latest/index.html) readthedocs.io 上。

在 Autofac，`IContainer`接口提供的依赖关系注入容器。 图 3-1 显示依赖关系，使用此容器、 实例化时`IOrderService`对象并将其到插入`ProfileViewModel`类。

![](dependency-injection-images/dependencyinjection.png "使用依赖关系注入时的依赖关系示例")

**图 3-1:**时使用依赖关系注入的依赖关系

在运行时，容器必须知道的哪一种实现`IOrderService`接口它应实例化，它可以实例化之前`ProfileViewModel`对象。 这包括：

-   决定如何实例化实现的对象的容器`IOrderService`接口。 这称为*注册*。
-   实例化实现的对象的容器`IOrderService`接口，与`ProfileViewModel`对象。 这称为*解析*。

最终，应用程序将完成使用`ProfileViewModel`对象和它将变得可用于垃圾回收。 此时，垃圾回收器应释放`IOrderService`实例如果其他类不共享同一个实例。

> [!TIP]
> 编写容器不可知的代码。 始终尝试写入容器无关的代码将分离正在使用的特定依赖项容器中的应用程序。

## <a name="registration"></a>注册

依赖项可以将其插入一个对象之前，依赖项的类型必须首先注册与该容器。 注册类型通常涉及传递容器，一个接口和实现接口的具体类型。

有两种方法在容器可以通过代码中注册类型和对象的：

-   与该容器中注册的类型或映射。 在需要时，容器将生成指定类型的实例。
-   将在容器中的现有对象注册为单一实例。 在需要时，容器将返回对现有对象的引用。

> [!TIP]
> 依赖关系注入容器并不总是适合。 依赖关系注入引入了附加的复杂性和可能不合适或有用到小型应用程序的要求。 如果类没有任何依赖项，或不是其他类型的依赖项，则它可能毫无意义，将其放在容器。 此外，如果类具有单个类型不可或缺的一部分的依赖项的设置和将永远不会更改，它可能毫无意义上将其放在容器。

应在一个应用程序中的一个方法中执行需要依赖关系注入的类型的注册，且应在应用程序的生命周期，以确保让应用程序都知道其类之间的依赖关系的早期调用此方法。 EShopOnContainers 移动应用程序中这由`ViewModelLocator`类，哪些生成`IContainer`对象以及是否保存对该对象的引用的应用中的唯一类。 下面的代码示例演示如何 eShopOnContainers 移动应用程序声明`IContainer`对象在`ViewModelLocator`类：

```csharp
private static IContainer _container;
```

在中注册类型和实例`RegisterDependencies`中的方法`ViewModelLocator`类。 这通过先创建`ContainerBuilder`实例，下面的代码示例所示：

```csharp
var builder = new ContainerBuilder();
```

然后注册类型和实例`ContainerBuilder`对象，将下面的代码示例演示类型注册的最常见形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

`RegisterType`此处显示的方法将接口类型映射到具体的类型。 它告诉要实例化的容器`RequestProvider`对象时它实例化的对象需要注入`IRequestProvider`通过构造函数。

具体类型也可不从接口类型，映射的情况下直接注册，如下面的代码示例中所示：

```csharp
builder.RegisterType<ProfileViewModel>();
```

当`ProfileViewModel`类型解决，容器将插入其必需的依赖项。

Autofac 还允许实例注册的容器是负责维护对一种类型的单一实例的引用。 例如，下面的代码示例显示如何 eShopOnContainers 移动应用注册时要使用的具体类型`ProfileViewModel`实例需要`IOrderService`实例：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

`RegisterType`此处显示的方法将接口类型映射到具体的类型。 `SingleInstance`方法配置注册，以便每个依赖对象接收的相同的共享的实例。 因此，只有一个`OrderService`实例将存在于容器，需要的注入的对象由共享`IOrderService`通过构造函数。

此外可以使用执行实例注册`RegisterInstance`方法，下面的代码示例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

`RegisterInstance`此处显示的方法创建一个新`OrderMockService`实例并将其注册与该容器。 因此，只有一个`OrderMockService`由需要注入的对象共享的容器中存在实例`IOrderService`通过构造函数。

以下类型和实例注册`IContainer`必须生成对象，这下面的代码示例中所示：

```csharp
_container = builder.Build();
```

调用`Build`方法`ContainerBuilder`实例生成包含已注册的新依赖关系注入容器。

>💡 **提示**： 请考虑`IContainer`视为不可变。 而 Autofac 提供`Update`应尽可能避免使用方法来更新现有容器，调用此方法中的注册。 没有为容器后修改已生成，特别是当已使用容器的风险。 有关详细信息，请参阅[作为不可变的容器，请考虑](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)readthedocs.io 上。

<a name="resolution" />

## <a name="resolution"></a>解决方法

类型已注册后，它可以解析或插入作为依赖项。 当正在解析类型，并且容器需要创建的新实例时，它会将任何依赖关系注入到实例。

通常情况下，在某种得以解决后，三个操作之一发生情况：

1.  如果该类型尚未注册，容器将引发异常。
1.  如果该类型具有已注册为 singleton，容器将返回的单独实例。 如果这是在首次类型调用为，容器将创建它必要情况下，并维护对它的引用。
1.  如果该类型没有已注册为 singleton，容器将返回的新实例，并不会继续对它的引用。

下面的代码示例演示如何`RequestProvider`Autofac 以前注册的类型可以是已解决：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此示例中，Autofac 需要解决的具体类型`IRequestProvider`类型，以及任何依赖关系。 通常情况下，`Resolve`需要特定类型的实例时调用方法。 有关控制解析对象的生存期的信息，请参阅[管理解析的对象生存期](#managing_the_lifetime_of_resolved_objects)。

下面的代码示例演示如何 eShopOnContainers 移动应用程序实例化视图模型类型和其依赖项：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此示例中，要求 Autofac 解析请求的视图模型，该视图模型类型和容器还会解析任何依赖关系。 在解析时`ProfileViewModel`类型，依赖关系，以解决是`IOrderService`对象。 因此，Autofac 首先构造`OrderService`对象，然后将其传递给的构造函数`ProfileViewModel`类。 有关如何 eShopOnContainers 移动应用程序构造视图的详细信息建模和将其关联到视图，请参阅[自动向视图模型定位器创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 注册和解析与容器的类型具有一定的性能开销由于用于创建每种类型，反射的容器的使用，特别是如果正在进行的应用中每个页面导航重构依赖关系。 如果有许多或深入的依赖关系，则创建的成本会显著增加。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>解析对象的生存期管理

注册后一种类型，Autofac 的默认行为是要创建已注册类型的新实例每次类型得到解决，或当依赖项机制都注入其他类的实例。 在此方案中，该容器不保存对已解析的对象的引用。 但是，注册实例时, Autofac 的默认行为是对象的管理为 singleton 的生存期。 因此，该实例保持在范围内，容器是位于范围内，而当容器都超出范围且被垃圾回收时释放或在代码显式释放容器时。

Autofac 实例作用域可以用于指定 Autofac 创建从已注册类型的对象的单一实例行为。 Autofac 实例作用域管理容器来实例化的对象生存期。 默认实例范围`RegisterType`方法是`InstancePerDependency`作用域。 但是，`SingleInstance`作用域可以与使用`RegisterType`方法，以便在容器创建或返回类型的单一实例时调用`Resolve`方法。 下面的代码示例演示如何指示 Autofac 若要创建的单一实例`NavigationService`类：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

第一次`INavigationService`接口处于已解决状态，容器创建一个新`NavigationService`对象，并保留它的引用。 上的任何后续解决方案`INavigationService`接口，容器返回的引用`NavigationService`先前创建的对象。

> [!NOTE]
> SingleInstance 作用域容器被释放后释放创建的对象。

Autofac 包括其他实例作用域。 有关详细信息，请参阅[实例范围](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html)readthedocs.io 上。

## <a name="summary"></a>总结

依赖关系注入可以实现的具体类型来自依赖于这些类型的代码分离。 它通常使用实现或扩展这些类型的具体类型并包含一系列的注册和接口和抽象类型之间的映射的容器。

Autofac 促进构建松耦合应用程序，并提供所有依赖关系注入容器，包括注册类型映射和对象实例的方法中的功能通常解析对象、 管理对象生存期和插入到它解析的对象的构造函数的依赖对象。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
