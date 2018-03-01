---
title: "管理片段"
ms.topic: article
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: 33f8b44a1213df4a8a1c75e2df66cc0b007a5749
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="managing-fragments"></a>管理片段

帮助管理片段，Android 提供`FragmentManager`类。 每个活动具有的实例`Android.App.FragmentManager`，将查找或动态更改其片段。 这些更改的每组称为*事务*，并使用该类中包含的 Api 之一执行`Android.App.FragmentTransation`，它由托管`FragmentManager`。 活动可能会启动事务如下：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

在中执行这些更改到片段`FragmentTransaction`实例使用方法类似于`Add()`，`Remove(),`和`Replace().`所做的更改使用，然后应用`Commit()`。 在事务中的更改不会立即执行。
相反，它们被计划尽快在活动的 UI 线程上运行。

下面的示例演示如何将片段添加到现有的容器：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果事务提交后`Activity.OnSaveInstanceState()`是调用，就将引发异常。 这是因为当该活动将保存其状态，Android 还将保存的任何托管片段的状态。 如果在此点后，任何片段的事务都已提交，这些事务的状态时还原该活动将会丢失。

可以将片段事务保存到活动的[后退堆栈](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)通过到调用`FragmentTransaction.AddToBackStack()`。 这允许用户通过向后导航片段更改时**回**按下按钮。 此方法的调用中，将删除的片段将销毁而无需将用户导航返回到活动的情况下不可用。

下面的示例演示如何使用`AddToBackStack`方法`FragmentTransaction`替换一个片段中，同时保留后的堆栈上的第一个片段的状态：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```

<a name="Communicating_with_Fragments" />

## <a name="communicating-with-fragments"></a>与片段通信

*FragmentManager*知道有关所有附加到活动的片段，并提供两种方法来帮助查找这些片段：

-   **FindFragmentById** &ndash;此方法来找到片段时片段已添加为事务的一部分使用在布局文件中指定的 ID 或容器 ID。

-   **FindFragmentByTag** &ndash;此方法用于查找在布局文件中提供的或已在事务中添加一个标记的片段。

片段和活动引用`FragmentManager`，因此相同的技术用于它们之间来回的通信。 应用程序可能通过使用这两种方法之一查找片段的引用，强制转换为适当的类型，该引用，然后在片段上直接调用方法。 下面的代码段提供了一个示例：

你也可使用的活动的`FragmentManager`查找片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

<a name="Communicating_with_the_Activity" />

### <a name="communicating-with-the-activity"></a>与活动通信

若要使用的片段可能`Fragment.Activity`属性来引用其主机。 通过强制转换为更具体的类型的活动，它是可能调用方法和属性在其主机上的活动，如下面的示例中所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
