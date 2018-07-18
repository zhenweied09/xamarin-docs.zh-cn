---
title: Xamarin.Forms 应用程序基础知识
description: 了解 Xamarin.Forms 应用程序开发，包括所有所需的核心概念，通过完成收尾工作了，如可访问性和本地化的基础知识。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 515dbd2683619cfcfb7a6c8ecac6bc147265ef7d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995607"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 应用程序基础知识

## <a name="accessibilityaccessibilityindexmd"></a>[辅助功能](accessibility/index.md)

若要使用 Xamarin.Forms 合并可访问功能 （如支持屏幕阅读工具） 的提示。

## <a name="app-classapplication-classmd"></a>[应用类](application-class.md)

`Application`类是适用于 Xamarin.Forms 的起始点 – 每个应用程序需要实现一个子类`App`设置初始页。 它还提供了`Properties`集合进行简单的数据存储。 可以在 C# 或 XAML 中对其进行定义。

## <a name="app-lifecycleapp-lifecyclemd"></a>[应用生命周期](app-lifecycle.md)

`Application`类`OnStart`， `OnSleep`，和`OnResume`方法，以及模式导航事件，使您能够处理的自定义代码的应用程序生命周期事件。

## <a name="behaviorsbehaviorsindexmd"></a>[行为](behaviors/index.md)

用户界面控件可以轻松地扩展而无需子类化使用行为来添加功能。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自定义呈现器](custom-renderer/index.md)

自定义呈现器让开发人员 override Xamarin.Forms 控件以自定义其外观和行为 （如果需要使用本机 Sdk） 每个平台上的默认呈现。

## <a name="data-bindingdata-bindingindexmd"></a>[数据绑定](data-binding/index.md)

数据绑定链接两个对象的属性允许在一个属性，才会自动反映在另一个属性中的更改。 数据绑定是模型-视图-视图不可或缺的一部分 ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 应用程序体系结构。

## <a name="dependency-servicedependency-serviceindexmd"></a>[依赖关系服务](dependency-service/index.md)

`DependencyService`提供了一个简单的定位符，以便可以共享代码中的代码到接口，并提供特定于平台的实现会自动得到解决，轻松地引用 Xamarin.Forms 中的特定于平台的功能。

## <a name="effectseffectsindexmd"></a>[效果](effects/index.md)

效果允许进行自定义，每个平台上的本机控件，通常用于较小的样式更改。

## <a name="filesfilesmd"></a>[文件](files.md)

可以使用代码在.NET Standard 库中，或通过使用嵌入的资源来实现处理用于 Xamarin.Forms 的文件。

## <a name="gesturesgesturesindexmd"></a>[手势](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)类上的用户界面控件支持 tap、 pinch 和平移手势。

## <a name="localizationlocalizationindexmd"></a>[本地化](localization/index.md)

可以使用内置的.NET 本地化框架构建跨平台使用 Xamarin.Forms 的多语言应用程序。

## <a name="local-databasesdatabasesmd"></a>[本地数据库](databases.md)

Xamarin.Forms 支持使用 SQLite 数据库引擎，这使得可以加载并将对象保存在共享代码中的数据库驱动的应用程序。

## <a name="messaging-centermessaging-centermd"></a>[消息中心](messaging-center.md)

Xamarin.Forms`MessagingCenter`启用视图模型和其他组件以与通信而无需知道有关彼此的任何除了简单的消息约定。

## <a name="navigationnavigationindexmd"></a>[导航](navigation/index.md)

Xamarin.Forms 提供了许多不同的页导航体验，具体取决于`Page`键入正在使用。

## <a name="templatestemplatesindexmd"></a>[模板](templates/index.md)

控件模板让你可以轻松设计或 re 主题在运行时，应用程序页时数据模板提供的功能支持的控件上定义的数据表示形式。

## <a name="triggerstriggersmd"></a>[触发器](triggers.md)

通过响应属性更改和 XAML 中的事件来更新控件。


## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
