---
title: Xamarin.Forms Visual 状态管理器
description: 使用视觉状态管理器在基于从代码中设置的可视状态的 XAML 元素中进行更改。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: f511f5c33b947704a42df850d2772c0b26511173
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin.Forms Visual 状态管理器

_使用视觉状态管理器在基于从代码中设置的可视状态的 XAML 元素中进行更改。_

可视状态管理器 (VSM) 是在 Xamarin.Forms 3.0 中的新增功能。 VSM 提供的结构化的方法，以从代码对用户界面进行可视更改。 在大多数情况下，在 XAML 中，定义应用程序的用户界面和此 XAML 包括描述视觉状态管理器如何影响用户界面的视觉对象的标记。

VSM 引入了的概念_可视状态_。 如 Xamarin.Forms 视图`Button`可以根据其基础的状态具有几个不同的视觉外观&mdash;它处于禁用状态，或按下还是具有输入焦点。 这些是按钮的状态。

可视状态中收集_可视状态组_。 可视状态组中的所有可视状态是互斥的。 可视状态和可视状态组由简单的文本字符串标识。

在其初始版本中，Xamarin.Florms 视觉状态管理器定义一个可视状态组名"CommonStates"为使用三个可视状态：

- "Normal"
- "已禁用"
- "已设定焦点"

此可视状态组支持的所有类的派生自[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，这是类的基类[ `View` ](xref:Xamarin.Forms.View)和[ `Page` ](xref:Xamarin.Forms.Page)。 

你还可以定义你自己的可视状态组和可视状态，作为本文将演示。

> [!NOTE]
> Xamarin.Forms 开发人员熟悉[触发器](~/xamarin-forms/app-fundamentals/triggers.md)知道触发器还可以对基于视图的属性或事件的激发中的更改的用户界面中的视觉对象进行更改。 但是，使用触发器来处理这些更改的各种组合可以变得容易混淆。 从历史上看，在基于 Windows XAML 的环境，以减少可视状态的组合导致混淆引入了视觉状态管理器。 利用 VSM，可视状态组中的可视状态始终是互相排斥的。 在任何时候，每个组中的只有一个状态是当前的状态。

## <a name="the-common-states"></a>常见的状态

在其初始版本中，视觉状态管理器，可包括如果视图是正常的或已禁用，或具有输入的焦点，则可以更改视图的可视外观的 XAML 文件中的部分。 这些被称为_常见状态_。

例如，假设你有`Entry`在页面上的视图。 下面是你希望的可视外观的如何`Entry`更改：

- `Entry`应有粉红色后台时`Entry`处于禁用状态。
- `Entry`应通常具有绿色背景。
- `Entry`它具有输入焦点时应展开正常高度的两倍。

可以将 VSM 标记附加到一个单独的视图，或者如果它适用于多个视图可以定义其样式。 接下来的两部分介绍这些方法。

### <a name="vsm-markup-on-a-view"></a>对视图的 VSM 标记

若要将附加到 VSM 标记`Entry`视图中，首先分隔`Entry`到开始和结束标记：

```xaml
<Entry FontSize="18">

</Entry>
```

它具有给定显式字体大小，因为状态之一时，将使用`FontSize`属性为双精度中的文本的大小`Entry`。

接下来，插入`VisualStateManager.VisualStateGroups`这些标记之间的标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

这可能看起来有点奇怪。 通常情况下，此类的两个标记之间显示的唯一标记是对于内容或属性的元素，与`VisualStateManager.VisualStateGroups`标记既不是。

这是合法的 XAML 语法，因为[ `VisualStateGroups` ](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)是由一个附加可绑定属性[ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager)类。 (有关可绑定的附加属性的详细信息，请参阅文章[附加属性](~/xamarin-forms/xaml/attached-properties.md)。)这是如何`VisualStateGroups`属性附加到`Entry`对象。

`VisualStateGroups`属性属于类型[ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList)，这是一套[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)对象。 在`VisualStateManager.VisualStateGroups`标记，插入一对`VisualStateGroup`标记为要包含的可视状态的每个组：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

请注意，`VisualStateGroup`标记具有`x:Name`属性，该值指示组的名称。 `VisualStateGroup`类定义`Name`可以改为使用的属性：

```xaml
<VisualStateGroup Name="CommonStates">
```

`VisualStateGroup`类定义一个名为属性[ `States` ](xref:Xamarin.Forms.VisualStateGroup.States)，这是一套[ `VisualState` ](xref:Xamarin.Forms.VisualState)对象。 `States` 作为内容属性的`VisualStateGroups`，你可以包含`VisualState`之间直接标记`VisualStateGroup`标记。

下一步是要包含在该组中的一对每个可视状态的标记。 这些还可以用标识`x:Name`或`Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` 定义一个名为属性[ `Setters` ](xref:Xamarin.Forms.VisualState.Setters)，这是一套[ `Setter` ](xref:Xamarin.Forms.Setter)对象。 它们是相同`Setter`中使用的对象[ `Style` ](xref:Xamarin.Forms.Style)对象。

`Setters` 是_不_的内容属性`VisualState`，因此必须包括有关属性元素标记`Setters`属性：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

你现在可以将插入一个或多个`Setter`对象之间的每对`Setters`标记。 这些是`Setter`定义前面所述的可视状态的对象：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

每个`Setter`当前该状态时，标记指示特定属性的值。 引用的任何属性`Setter`对象必须由可绑定属性。

与此类似的标记是基础**对视图的 VSM**页面**[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** 示例程序。 该页面包括三个`Entry`视图，但仅第二个已附加到它的 VSM 标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

请注意，第二个`Entry`还有`DataTrigger`作为的一部分其`Trigger`集合。 这将导致`Entry`直到内容键入到第三个要禁用`Entry`。 下面是在 iOS、 Android 和通用 Windows 平台 (UWP) 上运行的启动页：

[![对视图的 VSM： 禁用](vsm-images/VsmOnViewDisabled.png "VSM 上视图-禁用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

当前的可视状态为"Disabled"因此背景的第二个`Entry`为 iOS 和 Android 的屏幕上粉红色。 UWP 实现`Entry`不允许设置背景颜色时`Entry`处于禁用状态。 

当你输入的内容到第三个`Entry`，第二个`Entry`向"正常"状态和背景的交换机现为浅：

[![对视图的 VSM： 正常](vsm-images/VsmOnViewNormal.png "VSM 上视图-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

当您按第二个`Entry`，它获取输入的焦点。 它将切换到"已设定焦点"状态，并将扩展到两次窗体的高度：

[![对视图的 VSM： 已设定焦点](vsm-images/VsmOnViewFocused.png "VSM 对视图的已设定焦点")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

请注意，`Entry`获取输入的焦点时，不会保留为浅背景。 可视状态之间切换视觉状态管理器，以前的状态设置的属性是取消设置。 请记住可视状态是互相排斥。 "正常"状态并不意味着仅`Entry`已启用。 这意味着，`Entry`已启用且不具有输入的焦点。 

如果你想`Entry`如果希望为浅背景中"已设定焦点"状态的对象，请添加另一个`Setter`到该可视状态：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

为了使这些`Setter`对象才能正常工作，`VisualStateGroup`必须包含`VisualState`对于该组中的所有状态的对象。 如果没有任何可视状态`Setter`对象，仍作为空标记包括：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="vsm-markup-in-a-style"></a>VSM 标记样式

它通常是共享相同的视觉状态管理器标记在两个或多个视图之间所需的。 在这种情况下，你将需要将标记放入`Style`定义。

下面是现有隐式`Style`为`Entry`中的元素**VSM 上视图**页：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

添加`Setter`的标记`VisualStateManager.VisualStateGroups`附加可绑定属性：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

内容属性`Setter`是`Value`，因此的值`Value`可以直接在这些标记内指定属性。 属性属于类型`VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

在这些标记可以包含一项或多`VisualStateGroup`对象：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

VSM 标记的其余部分是与之前相同。

下面是**样式 VSM**显示完整的 VSM 标记页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

现在所有`Entry`此页上的视图响应其可视状态的相同方法。 "已设定焦点"状态现在包括第二个另请注意`Setter`，它将授予每个`Entry`为浅还后台时，它具有输入焦点：

[![样式 VSM](vsm-images/VsmInStyle.png "VSM 样式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定义你自己的可视状态

每个类都派生自`VisualElement`支持三个常见状态"正常"、"已设定焦点，"和"已禁用"。 在内部， [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)类检测何时启用或已禁用，或已设定焦点或失去焦点，变得越来越，并可在调用静态[ `VisualStateManager.GoToState` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualStateManager.GoToState/p/Xamarin.Forms.VisualElement/System.String/)方法如下：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

这是重要的方法中，并且它是将在中找到的唯一视觉状态管理器代码`VisualElement`类。 因为`GoToState`基于每个类 tht 每个对象派生自调用`VisualElement`，您可以使用视觉状态管理器以及任何`VisualElement`对象的这些更改进行响应。

有趣的是，可视状态组"CommonStates"的名称未显式引用中`VisualElement`。 组名称不是用于视觉状态管理器的 API 的一部分。 在显示到目前为止，你可以更改为任何其他，值从"CommonStates"组的名称以及该程序仍将起作用的两个示例程序之一。 组名称是仅在该组中的状态的常规说明。 隐式理解任何组中的可视状态是互相排斥： 一个状态，并且只有一个状态是当前在任何时间。

如果你想要实现自己的可视状态，你将需要调用`VisualStateManager.GoToState`代码中。 通常，你将使此调用代码隐藏文件的页类。

**VSM 验证**页面**[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** 示例演示如何使用与输入验证视觉状态管理器。 XAML 文件包含两个`Label`元素， `Entry`，和`Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 标记附加到第二个`Label`(名为`helpLabel`) 和`Button`(名为`submitButton`)。 有两个互相排斥状态，名为"有效"和"无效"。 （你将看到很快设置这些状态的代码隐藏文件）。请注意，每两个"ValidationState"组包含`VisualState`的标记"有效值"和"无效"，尽管它们之一是在每个用例中为空。 

如果`Entry`不包含有效的电话号码，则当前状态是"无效"。 第二个`Label`可见和`Button`处于禁用状态：

[![VSM 验证： 无效的 State](vsm-images/VsmValidationInvalid.png "VSM 验证-无效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

当输入有效的电话号码时，然后当前状态将变为"有效"。 第二个`Entry`消失和`Button`现已启用：

[![VSM 验证： 有效状态](vsm-images/VsmValidationValid.png "VSM 验证的有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

代码隐藏文件是用于处理处`TextChanged`事件从`Entry`。 处理程序使用正则表达式来确定输入的字符串是否有效。 名为的隐藏代码文件中的方法`GoToState`调用静态`VisualStateManager.GoToState`方法同时`helpLabel`和`submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

此外请注意，`GoToState`从构造函数可将状态初始化调用方法。 应始终有一个当前状态。 但无代码中有任何引用的可视状态组中，名称尽管它在 XAML 中的引用作为"ValidationStates"为目的的清楚起见。 

请注意代码隐藏文件必须执行的每个对象的帐户受影响的页面上，通过这些可视状态，并调用`VisualStateManager.GoToState`为每个这些对象。 在此示例中，它是仅有两个对象 (`Label`和`Button`)，但它可能是几个更多。

你可能想知道： 如果的代码隐藏文件必须引用受这些可视状态的页面上的每个对象，为何无法的代码隐藏文件只需访问对象直接？ 它肯定无法。 但是，通过使用视觉状态管理器，可以控制不同的可视状态完全在 XAML 中，它保存在一个位置的所有用户界面设计做出这些对象的响应。

它很可能要考虑派生的类从`Entry`并可能定义一个属性，可以设置为外部验证函数。 派生自的类`Entry`然后，可以调用`VisualStateManager.GoToState`方法。 此方案会不错，但仅当`Entry`已受不同的可视状态的唯一对象。 在此示例中，`Label`和`Button`也会受到影响。 没有方法为 VSM 标记附加到`Entry`以 VSM 标记附加到这些其他对象，若要从另一个对象引用可视状态更改控件的网页和没有方法上的其他对象。

<a name="adaptive-layout" />

## <a name="using-the-vsm-for-adaptive-layout"></a>用于自适应布局 VSM

到手机上运行的是 Xamarin.Forms 程序通常可以查看纵向或横向朝的纵横比，并在桌面上运行的是 Xamarin.Forms 程序可以调整大小，以采用许多不同的大小和纵横比。 设计良好的应用程序可能会显示其内容以不同方式为这些不同的页或窗口窗体因素。 

此方法有时称为_自适应布局_。 因为自适应布局只是涉及到程序的视觉对象，它将是理想的视觉状态管理器应用。

一个简单的示例是一个显示会影响应用程序的内容的按钮 a 小型集合的程序。 在纵向模式下，这些按钮可能显示在页面顶部的水平行：

[![VSM 自适应布局： 纵向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 自适应布局-纵向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在横向模式下，可能会移到一侧，并显示在列中的按钮数组：

[![VSM 自适应布局： 横向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自适应布局-横向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

从顶部到底部，在通用 Windows 平台、 Android 和 iOS 上运行程序。

这是视觉状态管理器的作业。 **VSM 自适应布局**页面[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)示例定义两个名为"纵向"和"横向"的可视状态与名为"OrientationStates"的组。 （更复杂的方法可能基于多个不同的页或窗口宽度。） 

VSM 标记显示在 XAML 文件中的四个位置中。 `StackLayout`名为`mainStack`包含菜单和内容，这是`Image`元素。 这`StackLayout`应有纵向模式中的垂直方向和水平方向在横向模式中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

内部`ScrollView`名为`menuScroll`和`StackLayout`名为`menuStack`实现按钮的菜单。 这些布局的方向是相反的`mainStack`: 菜单应纵向模式中水平和垂直在横向模式中。

隐式样式的按钮本身是 VSM 标记的第四个块区。 此标记将设置`VerticalOptions`， `HorizontalOptions`，和`Margin`特定于 portait 与横向 orienations 属性。

代码隐藏文件集`BindingContext`属性`menuStack`实现`Button`命令，并还会将附加的处理程序`SizeChanged`页的事件：

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

`SizeChanged`处理程序调用`VisualStateManager.GoToState`对这两个`StackLayout`和`ScrollView`元素，然后循环访问的子级`menuStack`调用`VisualStateManager.GoToState`为`Button`元素。

首先，它可能看起来就像代码隐藏文件可以更直接通过在 XAML 文件中，设置的元素的属性处理方向更改，但是视觉状态管理器肯定是更结构化的方法。 所有视觉对象将保留在 XAML 文件，其中它们变得更容易检查，维护，并修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>与 Xamarin.University 的可视状态管理器

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Forms 3.0 可视状态管理器，也可由[Xamarin 大学](https://university.xamarin.com/)**

## <a name="related-links"></a>相关的链接

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

