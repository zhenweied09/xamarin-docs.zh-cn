---
title: Xamarin.Forms 应用程序基础知识
description: 浏览 Xamarin.Forms 应用程序开发，其中包括与所有的所需的核心概念，通过完成收尾工作，如可访问性和本地化的基础知识。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: f843f4b1c20daba2a504bd0e70ae51f4d0405c06
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240241"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 应用程序基础知识

## <a name="accessibilityaccessibilityindexmd"></a>[辅助功能](accessibility/index.md)

若要将访问功能 （如支持屏幕读取工具） 合并使用 Xamarin.Forms 的提示。

## <a name="app-classapplication-classmd"></a>[应用类](application-class.md)

`Application`类是 Xamarin.Forms 的起始点 – 每个应用程序需要实现子类`App`若要将初始页。 它还提供了`Properties`集合进行简单的数据存储。 可以在 C# 或 XAML 中定义它。

## <a name="app-lifecycleapp-lifecyclemd"></a>[应用生命周期](app-lifecycle.md)

`Application`类`OnStart`， `OnSleep`，和`OnResume`方法，以及模式导航事件，使您能够处理应用程序生命周期事件的自定义代码。

## <a name="behaviorsbehaviorsindexmd"></a>[行为](behaviors/index.md)

用户界面控件可轻松扩展不子类化的情况下，使用行为来添加功能。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自定义呈现器](custom-renderer/index.md)

自定义呈现，开发人员可以替代 Xamarin.Forms 控件以自定义其外观和行为 （如果需要，请使用本机 Sdk） 每个平台上的默认呈现。

## <a name="data-bindingdata-bindingindexmd"></a>[数据绑定](data-binding/index.md)

数据绑定链接两个对象的属性才会自动反映在另一个属性的一个属性中允许更改。 数据绑定是不可或缺的组成部分模型-视图-视图模型 ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 应用程序体系结构。

## <a name="dependency-servicedependency-serviceindexmd"></a>[依赖项服务](dependency-service/index.md)

`DependencyService`提供简单的定位符，以便可以在你的共享代码的代码到接口，还可以提供特定于平台的实现自动解决，从而便于引用 Xamarin.Forms 中的特定于平台的功能。

## <a name="effectseffectsindexmd"></a>[效果](effects/index.md)

效果允许进行自定义，每个平台上的本机控件，通常用于小样式更改。

## <a name="gesturesgesturesindexmd"></a>[手势](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)类支持用户界面控件上的 tap、 捏合和平移手势。

## <a name="localizationlocalizationindexmd"></a>[本地化](localization/index.md)

内置的.NET 本地化框架可以用于构建跨平台使用 Xamarin.Forms 的多语言应用程序。

## <a name="local-databasesdatabasesmd"></a>[本地数据库](databases.md)

Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。

## <a name="messaging-centermessaging-centermd"></a>[消息中心](messaging-center.md)

Xamarin.Forms`MessagingCenter`启用视图模型和其他组件以与通信而无需知道任何有关每个其他除了简单的消息协定。

## <a name="navigationnavigationindexmd"></a>[导航](navigation/index.md)

Xamarin.Forms 提供了多种不同的页导航体验，具体取决于`Page`键入正在使用。

## <a name="templatestemplatesindexmd"></a>[模板](templates/index.md)

数据模板提供受支持的控件上定义数据的表示形式的功能时，控件模板将提供轻松主题和重新主题的功能在运行时，应用程序页。

## <a name="triggerstriggersmd"></a>[触发器](triggers.md)

通过响应属性更改和 XAML 中的事件来更新控件。


## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
