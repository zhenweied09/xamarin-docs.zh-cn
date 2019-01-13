---
title: 依赖关系注入
description: 本章介绍了 eShopOnContainers 移动应用程序如何使用依赖关系注入使那些依赖于具体类型的代码与这些类解除耦合。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fb225349b9ffb1c950486a817897b3c26c6ffbe4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242565"
---
# <a name="dependency-injection"></a>依赖关系注入

通常情况下，实例化一个对象时会调用类构造函数，并且对象所需的任何值作为实际参数传递给构造函数。 这是一种依赖关系注入方式，尤其被称为*构造函数注入*。 对象所需的依赖关系被注入到构造函数。

通过指定接口类型作为依赖关系，依赖关系注入使依赖于具体类型的代码与这些类解除耦合。 它通常使用一个包含一系列注册和映射关系的容器，这些关系表示接口和抽象类型，并与实现或扩展这些类型的具体类型之间的注册和映射。

也有其他类型的依赖关系注入，如*属性 setter 注入*，和*方法调用注入*，但它们不太常见。 因此，这一章将只介绍使用了依赖关系注入容器执行构造函数注入的方式。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>依赖关系注入简介

依赖关系注入是一个专门的控制反转 (IoC) 模式，其中关注点被反转的地方就是获取必需的依赖关系的过程。 使用依赖关系注入，另一个类负责将依赖关系在运行时注入到一个对象。 下面的代码示例演示当使用依赖关系注入时，`ProfileViewModel`类是如何被构造的：

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

`ProfileViewModel`构造函数接收一个`IOrderService`实例作为参数，由另一个类注入。 在`ProfileViewModel`类上唯一的依赖项是接口类型。 因此，`ProfileViewModel`类不了解任何负责实例化`IOrderService`对象的类。 负责实例化`IOrderService`对象，并将其到插入`ProfileViewModel`类中的这个类，称为*依赖关系注入容器*。

依赖关系注入容器通过提供一个工具用于初始化类实例，并通过基于容器的配置来管理这些实例的生存期来减少对象之间的耦合。 在对象创建期间，容器将该对象需要的任何依赖关系注入到它。 如果尚未创建这些依赖关系，容器首先创建并解析它们的依赖项。

> [!NOTE]
> 此外可以手动使用工厂方法实现依赖关系注入。 但是，使用容器可以提供额外的功能，例如生命期管理和通过程序集的扫描进行注册。

使用依赖关系注入容器有以下几个优点：

-   容器移除了类查找其依赖项及管理其生存期的需求。
-   容器允许对类依赖项的实现进行映射而不会影响这个类。
-   容器通过允许对依赖项进行模拟 (mock) 以促进可测试性。
-   容器通过允许新类以可轻松添加到应用程序可以提高可维护性。

在使用了 MVVM 的 Xamarin.Forms 应用背景下，通常使用依赖关系注入容器来注册和解析视图模型，以及用于注册服务并将其注入到视图模型。

有许多依赖关系注入容器可以使用，在 eShopOnContainers 移动应用中使用了 Autofac 管理视图模型和服务类的实例化。 Autofac 促进了构建松耦合应用程序，并提供了依赖关系注入容器所有常见的功能，包括注册类型映射和对象实例的方法、对象解析、 管理对象生存期和注入已解析的依赖对象到对象的构造函数。 有关 Autofac 的详细信息，请参阅 readthedocs.io 上的[Autofac](http://autofac.readthedocs.io/en/latest/index.html) 。

在 Autofac 中，`IContainer`接口提供了依赖关系注入容器。 图 3-1 显示了使用此容器时的依赖关系， 它实例化了`IOrderService`对象并将其到注入`ProfileViewModel`类。

![](dependency-injection-images/dependencyinjection.png "使用依赖关系注入时的依赖关系示例")

**图 3-1:** 使用依赖关系注入时的依赖关系

在运行时，在实例化`ProfileViewModel`对象之前，容器必须知道哪一种`IOrderService`接口的实现应该被实例化。 这包括：

-   容器决定如何实例化一个实现了`IOrderService`接口的对象的过程。 这称为*注册*。
-   容器进行实例化实现`IOrderService`接口的对象，及`ProfileViewModel`对象的过程。 这称为*解析*。

最终，应用程序将完成使用`ProfileViewModel`对象并且它将变得可被垃圾回收。 此时，如果其他类没有共享同一个`IOrderService`的实例，垃圾回收器应释放这个实例。

> [!TIP]
> 编写容器不可知的代码。 始终尝试写容器无关的代码来将应用程序从正在使用的特定依赖项容器中分离。

## <a name="registration"></a>注册

在可以将其依赖项注入到一个对象之前，依赖项的类型必须首先在该容器注册。 注册类型通常包括传递一个接口和实现该接口的具体类型到这个容器。

有两种方法可以在容器中通过代码注册类型和对象：

-   在容器中注册一个类型或映射。 在需要时，容器将生成指定类型的一个实例。
-   在容器中注册一个现有的对象为单例模式。 在需要时，容器将返回现有对象的一个引用。

> [!TIP]
> 依赖关系注入容器并不总是合适的。 依赖关系注入引入了额外的复杂性并且可能不合适小型应用程序或作用不大。 如果一个类没有任何依赖项，或不作为其他类型的依赖项，那么将其放在容器可能毫无意义。 此外，如果类具有单一一组依赖项，作这个类不可或缺的一部分并且将永远不会更改，那么将其放在容器上可能毫无意义。

应在一个应用程序中的一个方法中，执行那些需要依赖关系注入的类型的注册，并且这个方法应在应用程序的早期生命周期中进行调用，以确保让应用程序都知道其类之间的依赖关系。 在 eShopOnContainers 移动应用程序中，这由`ViewModelLocator`类执行，它生成了`IContainer`对象并且它是应用中的保存对该对象的引用的唯一类。 下面的代码示例演示了 eShopOnContainers 移动应用程序如何在`ViewModelLocator`类中声明`IContainer`对象：

```csharp
private static IContainer _container;
```

类型和实例是在`ViewModelLocator`类中的`RegisterDependencies`方法进行注册。 这通过先创建一个`ContainerBuilder`实例来完成，如下面的代码示例所示：

```csharp
var builder = new ContainerBuilder();
```

然后类型和实例使用`ContainerBuilder`对象进行注册，下面的代码示例将演示类型注册的最常见形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

此处显示的`RegisterType`方法将接口类型映射到具体的类型。 当它实例化一个需要通过构造函数注入的`IRequestProvider`对象时，它将告诉容器来实例化一个`RequestProvider`对象。

具体类型也可在没有接口类型映射的情况下直接注册，如下面的代码示例中所示：

```csharp
builder.RegisterType<ProfileViewModel>();
```

当`ProfileViewModel`类型被解析时，容器将注入其必需的依赖项。

Autofac 还允许实例注册，容器负责维护一个类型的单例模式实例的引用。 例如，下面的代码示例显示了当`ProfileViewModel`实例需要`IOrderService`实例时， eShopOnContainers 移动应用如何注册要使用的具体类型：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

此处显示的`RegisterType`方法将接口类型映射到具体的类型。 `SingleInstance`方法配置在这个注册，以便每个依赖对象接收到相同的共享实例。 因此，只有一个`OrderService`实例将会存在于这个容器，它将在那些需要通过构造函数注入`IOrderService`的对象间进行共享。

此外也可以使用`RegisterInstance`方法执行实例注册，如下面的代码示例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

此处显示的`RegisterInstance`方法创建了一个新的`OrderMockService`实例并将其在该容器注册。 因此，只有一个`OrderMockService`实例存在于该容器，它将在那些需要通过构造函数注入`IOrderService`的对象间进行共享。

以下类型和实例的注册，必须生成`IContainer`对象，如下面的代码示例中所示：

```csharp
_container = builder.Build();
```

在`ContainerBuilder`实例上调用`Build`方法生成一个包含已注册内容的新依赖关系注入容器。

>💡 **提示**： 请考虑将`IContainer`视为不可变类型。 而 Autofac 提供`Update`方法来更新现有容器的注册，应尽可能避免使用。 在容器生成后修改该容器会有风险，特别是当该容器已被使用时。 有关详细信息，请参阅 readthedocs.io 上的[请考虑将容器作为不可变类型](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)。

<a name="resolution" />

## <a name="resolution"></a>解析

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
