---
title: 设备方向
description: 此文章介绍了如何在纵向与横向方向非常好的布局 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 9245a17423d97887d2032856b10427685b25c29b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244346"
---
# <a name="device-orientation"></a>设备方向

请务必考虑将如何使用你的应用程序，以及可以如何纳入横向以改进用户体验。 单个布局可以用于容纳多个方向和最佳使用可用空间。 在应用程序级别中，可以禁用或启用旋转。

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>控制方向

当使用 Xamarin.Forms，控制设备方向的受支持的方法是为每个单独的项目中使用的设置。

### <a name="ios"></a>iOS

在 iOS 上使用的应用程序配置设备方向**Info.plist**文件。 如果应用程序包括它作为目标，此文件将包括方向设置为 iPhone 和 iPod，以及适用于 iPad 的设置。 以下是特定于 IDE 的说明。 使用在本文档的顶部 IDE 选项选择你想要查看哪些说明：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，打开的 iOS 项目并打开**Info.plist**。 该文件将打开到配置面板中，从 iPhone 部署信息选项卡：

![iPhone Visual Studio 中的部署信息](device-orientation-images/orientation-vs-iphone.png)

若要配置 iPad 方向，选择**iPad 部署信息**从右向左面板中，然后选择从可用的方向顶部的选项卡：

![Visual Studio 中的受支持的设备方向](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在适用于 Mac 的 Visual Studio，打开的 iOS 项目并打开**Info.plist**。 下**应用程序**选项卡上，章节将会可用于设置方向：

![iPhone 适用于 Mac 的 Visual Studio 中的部署信息](device-orientation-images/orientation-xam-ui.png)

如果你想要编辑使用键 / 值编辑器界面，选择的值**源**> 在屏幕底部的选项卡：

![在 Visual Studio for Mac 支持设备方向](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

若要控制在 Android 上的方向，打开**MainActivity.cs**并设置使用属性修饰的方向`MainActivity`类：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android 支持几个选项用于指定方向：

- **横向**&ndash;强制应用程序方向为种种，而不考虑传感器数据。
- **纵向**&ndash;强制应用程序方向纵向，而不考虑传感器数据。
- **用户**&ndash;导致应用程序会使用用户的首选的方向显示。
- **后面**&ndash;会导致应用程序的方向的方向相同[活动](https://developer.xamarin.com/api/type/Android.App.Activity/)背后。
- **传感器**&ndash;导致应用程序的方向将由传感器，即使用户已禁用自动旋转。
- **SensorLandscape** &ndash;会导致应用程序以使用横向同时使用传感器数据来更改屏幕面向 （以便屏幕不被视为倒置） 的方向。
- **SensorPortrait** &ndash;使应用程序使用纵向同时使用传感器数据来更改屏幕面向 （以便屏幕不被视为倒置） 的方向。
- **ReverseLandscape** &ndash;会导致应用程序以使用横向，面向相反方向从往常一样，以便显示"倒置。"
- **ReversePortrait** &ndash;使应用程序使用纵向面向相反方向从往常一样，以便显示"倒置。"
- **FullSensor** &ndash;导致应用程序依赖于传感器数据以选择正确的方向 （带可能 4)。
- **FullUser** &ndash;使应用程序使用用户的方向首选项。 如果启用了自动旋转，则可以使用所有 4 方向。
- **UserLandscape** &ndash; _\[不支持\]_ 将导致应用程序以使用横向，除非用户具有自动旋转启用，在这种情况下它将使用若要确定方向的传感器。 此选项将中断编译。
- **UserPortrait** &ndash; _\[不支持\]_ 将导致应用程序使用纵向，除非用户具有自动旋转启用，在这种情况下它将使用若要确定方向的传感器。 此选项将中断编译。
- **锁定** &ndash; _\[不支持\]_ 使应用程序使用屏幕方向，无论它位于启动，而无需对设备中的更改作出响应的物理方向。 此选项将中断编译。

请注意，本机 Android Api 提供了大量方向中如何托管的控制，包括显式与用户的相抵触选项表示首选项。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP)，在中设置受支持的方向**Package.appxmanifest**文件。 打开清单将显示配置面板受支持的方向可以处于选定状态。

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>对到方向中的更改作出反应

Xamarin.Forms 不提供任何本机事件通知您的应用程序的方向在共享代码中的更改。 但是，`SizeChanged`事件`Page`时，会激发的宽度或高度`Page`更改。 时的宽度`Page`大于高度，在设备处于横向模式。 有关详细信息，请参阅[显示基于屏幕方向的映像](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)。

> [!NOTE]
> 没有用于在共享代码中接收通知的方向更改现有的免费 NuGet 包。 请参阅[GitHub 存储库](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)有关详细信息。

或者，也可以重写[ `OnSizeAllocated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnSizeAllocated(System.Double,System.Double)/)方法`Page`，插入任何布局更改存在逻辑。 `OnSizeAllocated`调用方法时`Page`分配新的大小，这种情况发生的 whenver 将设备旋转。 请注意的基实现`OnSizeAllocated`执行重要布局功能，因此务必要在重写中调用基实现：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

未能执行该步骤将导致在非正常运行的页。

请注意，`OnSizeAllocated`方法时可调用次数多设备旋转。 每次更改你的布局是一种浪费的资源，并且可造成闪烁。 请考虑使用网页中的一个实例变量跟踪是否方向是中横向或纵向，并仅重绘的更改时：

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

一旦检测到在设备方向更改，你可能想要添加或删除附加到/从用户界面以响应的可用空间更改视图。 例如，考虑纵向中每个平台上内置的计算器：

![](device-orientation-images/calculator-portrait.png "英寸纵向计算器应用程序")

与横向：

![](device-orientation-images/calculator-landscape.png "在布局中的计算器应用程序")

请注意应用程序通过在布局中添加更多的功能充分利用可用空间。

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>响应式布局

它是可以使用内置的布局，以便它们正常转换在旋转设备时的设计接口。 设计将继续响应方向中的更改时是有吸引力的接口时请考虑以下一般规则：

- **注意比率**&ndash;方面比率进行某些假设时，方向中的更改可能会导致问题。 例如，将有足够的空间在 1/3 英寸纵向屏幕的垂直空间中的视图可能无法放入 1/3 的布局中的垂直空间。
- **请慎用绝对值**&ndash;有意义的纵向的绝对 （像素） 值中横向可能毫无意义。 需要绝对值时，使用嵌套的布局隔离它们的影响。 例如，它应该合理地使用中的绝对值`TableView``ItemTemplate`时已有保证的统一高度的项模板。

上述规则也适用时通常实现接口针对多个屏幕大小和被视为最佳做法。 此指南的余下部分将介绍使用 Xamarin.Forms 中的每个主布局的响应式布局的具体示例。

> [!NOTE]
> 为清楚起见，以下部分演示如何实现使用的一种类型的响应式布局`Layout`一次。 在实践中，它通常会更简单混合`Layout`s 以实现使用简单得多或最直观的所需的布局`Layout`每个组件。

### <a name="stacklayout"></a>StackLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-stack-portrait.png "纵向中的照片应用程序")

与横向：

![](device-orientation-images/photo-stack-landscape.png "在布局中的照片应用程序")

这是与下面的 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

某些 C# 用于更改的方向`outerStack`基于设备的方向：

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

请注意以下事项：

- `outerStack` 调整以作为具体取决于方向，以最有效地利用可用空间的水平或垂直堆栈中存在的映像和控件。


### <a name="absolutelayout"></a>AbsoluteLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-abs-portrait.png "纵向中的照片应用程序")

与横向：

![](device-orientation-images/photo-abs-landscape.png "在布局中的照片应用程序")

这是与下面的 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

请注意以下事项：

- 由于页面布局的方法，没有无需过程代码以便引入响应能力。
- `ScrollView`用于允许甚至屏幕的高度时的按钮和映像固定高度的总和小于是可见的标签。


### <a name="relativelayout"></a>RelativeLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-rel-portrait.png "纵向中的照片应用程序")

与横向：

![](device-orientation-images/photo-rel-landscape.png "在布局中的照片应用程序")

这是与下面的 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

请注意以下事项：

- 由于页面布局的方法，没有无需过程代码以便引入响应能力。
- `ScrollView`用于允许甚至屏幕的高度时的按钮和映像固定高度的总和小于是可见的标签。

### <a name="grid"></a>Grid

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-grid-portrait.png "纵向中的照片应用程序")

与横向：

![](device-orientation-images/photo-grid-landscape.png "在布局中的照片应用程序")

这是与下面的 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以及以下过程的代码以处理旋转更改：

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

请注意以下事项：

- 由于页面布局的方法，没有方法可以更改网格放置控件。


## <a name="related-links"></a>相关链接

- [布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 示例 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [响应式布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [显示基于屏幕方向的映像](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)
