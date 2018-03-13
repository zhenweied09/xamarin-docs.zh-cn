---
title: "创建一个片段"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: 415c3a5e9446c5db545b62272f3b90a9ac73e401
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="creating-a-fragment"></a>创建一个片段

若要创建一个片段，类必须从继承`Android.App.Fragment`，然后重写`OnCreateView`方法。 `OnCreateView` 如果是在屏幕上，将该片段的时间，并且将返回宿主的活动将调用`View`。 典型`OnCreateView`会创建此`View`通过 inflating 布局文件，然后将其附加到父容器。 容器的特征是重要，因为 Android 将应用于该片段的 UI 布局参数的父级。 下面的示例阐释了这一点：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上面的代码将放大量视图`Resource.Layout.Example_Fragment`，并将其添加到的子视图作为`ViewGroup`容器。


> [!NOTE]
> 片段子类必须具有公共的默认无参数构造函数。

## <a name="adding-a-fragment-to-an-activity"></a>将片段添加到活动

有两种方法可能托管片段内活动：

-   **以声明方式**&ndash;片段可以以声明方式中使用`.axml`使用的布局文件`<Fragment>`标记。

-   **以编程方式**&ndash;片段可以还通过来实例化动态使用`FragmentManager`类的 API。

通过以编程方式使用`FragmentManager`类将在本指南的后面部分讨论。

### <a name="using-a-fragment-declaratively"></a>以声明方式使用片段

添加布局内的一个片段需要使用`<fragment>`标记，然后通过提供可以确定片段`class`属性或`android:name`属性。 下面的代码段演示如何使用`class`特性来声明`fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

此下一个代码段演示如何声明`fragment`使用`android:name`属性用于标识片段类：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Android 时创建活动后，将实例化布局文件中指定每个片段并插入从创建该视图`OnCreateView`代替了`Fragment`元素。
以声明方式添加到活动的片段是静态的将保留在活动中，直到它被销毁;不能动态替换或删除附加到的活动的生命周期内的片段。

每个片段必须分配有唯一标识符：

-  **android: id** &ndash;因为与布局文件中其他 UI 元素，这是唯一的 id。

-  **android： 标记**&ndash;此属性是唯一的字符串。

如果使用任一上述的两种方法，该片段将假定容器视图的 ID。 在下面的示例其中既不`android:id`也不`android:tag`提供 Android 将分配 ID`fragment_container`到片段：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>包名称用例

Android 不允许在包名称; 大写字符尝试放大量视图，如果包名称包含一个大写字符时，它将引发异常。 但是，Xamarin.Android 是详细 forgiving，并且将容忍命名空间中的大写字符。

例如，这两个以下的代码段会使用 Xamarin.Android。 但是，第二个代码段将导致`android.view.InflateException`纯基于 Java 的 Android 应用程序引发。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

或

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>片段生命周期

片段具有其自己生命周期，一定程度上独立的但仍受[托管活动的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)。
例如，当活动停留时，所有其关联的片段都已暂停。 下图概述了片段的生命周期。

[![流示意图片段生命周期](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>片段创建生命周期方法

在创建时，下面的列表，片段的生命周期中显示的各种回调的流：

-   **`OnInflate()`** &ndash; 当片段正在创建的视图布局的一部分时调用。 这可能片段以声明方式创建从 XML 布局文件后立即调用。 片段尚不可其活动与相关联，但**活动**，**捆绑**，和**AttributeSet**从视图层次结构中作为参数传递。 此方法最适合用于分析**AttributeSet**并为保存属性可能会使用更高版本的片段。

-   **`OnAttach()`** &ndash; 调用后片段是与活动关联。 这是第一种方法为该片段为可供使用时运行。 一般情况下，片段不应实现一个构造函数，或重写默认构造函数。 应在此方法中初始化任何组件所需的片段。

-   **`OnCreate()`** &ndash; 要创建片段的活动由调用。 当调用此方法时，托管活动的视图层次结构可能不会完全实例化，所以片段不应依赖于在更高版本在片段的生命周期之前的活动的查看层次结构的任何部分。 例如，不要使用此方法以执行任何优化或调整到 UI 的应用程序。 这是片段可能会开始收集数据所需的最早时间。 片段此时运行在 UI 线程中，因此避免任何时间较长的处理，或在后台线程上执行该处理。 可以跳过此方法，如果**SetRetainInstance(true)**调用。
    将下面更详细地介绍此替代方式。

-   **`OnCreateView()`** &ndash; 创建该片段的视图。
    此方法调用一次活动的**OnCreate()**方法已完成。 此时，可安全地与活动的视图层次结构进行交互。 此方法应返回该片段将使用的视图。

-   **`OnActivityCreated()`** &ndash; 之后调用**Activity.OnCreate**已由托管活动已完成。
    此时，应执行最后的调整，为该用户界面。

-   **`OnStart()`** &ndash; 包含的活动已恢复后调用。 这使得片段对用户可见。 在许多情况下，该片段将包含应的代码**onstart （)**一个活动的方法。

-   **`OnResume()`** &ndash; 这是最后一个用户可以与片段交互之前调用的方法。 应在此方法中执行的代码的类的一个示例将启用的设备的用户可与进行交互，如相机的功能的位置服务。 不过，这些服务可能导致电池消耗过快，并且应用程序应尽量减小其使用保留的电池使用时间。


### <a name="fragment-destruction-lifecycle-methods"></a>片段析构生命周期方法

下一步的列表说明了调用，如正在销毁片段的生命周期方法：

-   **`OnPause()`** &ndash; 用户不再能够与片段进行交互。 这种情况下存在是因为某些其他片段操作修改此代码段，或宿主活动已暂停。 很可能承载此片段的活动可能仍处于可见、 焦点中的活动，即部分透明的或不占用整个屏幕。 此方法将变为活动状态，它时，用户将离开片段的第一个提示。 片段应保存任何更改。

-   **`OnStop()`** &ndash; 片段不再可见。 主机活动可能已停止，或片段操作在活动中修改它。 此回调提供相同的目的**Activity.OnStop**。

-   **`OnDestroyView()`** &ndash; 调用此方法来清理与视图关联的资源。 这称为片段与关联的视图已销毁时。

-   **`OnDestroy()`** &ndash; 不再使用的片段时，调用此方法。 仍与活动关联但片段不再起作用。 此方法应释放正在使用的片段，如任何资源[ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/)可能用于相机。 可以跳过此方法，如果**SetRetainInstance(true)**调用。 将下面更详细地介绍此替代方式。

-   **`OnDetach()`** &ndash; 片段不再与活动关联之前调用此方法。 片段的查看层次结构不再存在，并且应在此时释放片段使用的所有资源。


### <a name="using-setretaininstance"></a>使用 SetRetainInstance

它有可能指定，它不应完全销毁该活动正被重新创建的片段。 `Fragment`类会提供方法`SetRetainInstance`为此目的。 如果`true`传递给此方法，则重新启动活动后，将使用该片段的同一个实例。 如果发生这种情况，则所有回叫方法将调用除`OnCreate`和`OnDestroy`生命周期回调。 此过程上面所示 （通过绿色虚线） 生命周期图所示。


## <a name="fragment-state-management"></a>片段状态管理

片段可能保存和片段生命周期中使用的实例还原其状态`Bundle`。 此捆绑包允许将数据保存为键/值对的片段和适用于不需要多内存的简单数据。 片段可通过调用保存其状态`OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

在创建一个片段的新实例，保存在状态`Bundle`将变为可用到新的实例通过`OnCreate`， `OnCreateView`，和`OnActivityCreated`方法的新实例。
下面的示例演示如何检索值`current_choice`从`Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

重写`OnSaveInstanceState`是用于保存暂时性数据在片段中跨方向的更改，如适当机制`current_choice`在上面的示例的值。 但是的默认实现`OnSaveInstanceState`负责在分配的 id 的每个视图的 UI 中保存暂时性数据。 例如，查看的应用程序具有`EditText`，如下所示在 XML 中定义的元素：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由于`EditText`控件具有`id`分配，该片段自动将数据保存在小组件时`OnSaveInstanceState`调用。


### <a name="bundle-limitations"></a>捆绑限制

尽管使用`OnSaveInstanceState`使它更容易保存暂时性数据，使用此方法有一些限制：

-  如果该片段未添加到后的堆栈，则当用户按下时，其状态将不会还原**回**按钮。

-  当该绑定用于保存数据时，该数据被序列。 这可能导致处理延迟。


## <a name="contributing-to-the-menu"></a>导致菜单

片段可能影响其托管活动的菜单项。
活动首先处理菜单项。 如果活动没有处理程序，然后该事件将传递到片段，它将处理它。

若要将项添加到活动的菜单，片段必须执行两项操作。
首先，该片段必须实现的方法`OnCreateOptionsMenu`并将其项放入菜单上，如下面的代码中所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

从下面的 XML，位于文件中扩充上面的代码段中的菜单`menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下来，必须调用片段`SetHasOptionsMenu(true)`。 对此方法的调用向 Android 通知片段具有促成选项菜单的菜单项。 除非进行对此方法的调用，片段的菜单项将不会添加到活动的选项菜单。 这通常是生命周期方法中`OnCreate()`，下面的代码片段中所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

下面的屏幕显示此菜单的外观：

[![我行程应用显示菜单项的示例屏幕快照](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
