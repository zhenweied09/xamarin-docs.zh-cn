---
title: 自定义 ViewCell
description: Xamarin.Forms ViewCell 是可以添加到 ListView 或 TableView，其中包含开发人员定义的视图的单元格。 本文演示如何创建 ViewCell Xamarin.Forms ListView 控件中托管的自定义呈现器。
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: b1ebe2694ad5fa996b8b679cfb31a203588de05c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998994"
---
# <a name="customizing-a-viewcell"></a>自定义 ViewCell

_Xamarin.Forms ViewCell 是可以添加到 ListView 或 TableView，其中包含开发人员定义的视图的单元格。本文演示如何创建 ViewCell Xamarin.Forms ListView 控件中托管的自定义呈现器。这将停止 Xamarin.Forms 布局计算正在从 ListView 滚动期间重复调用。_

每个 Xamarin.Forms 单元格已创建的本机控件实例的每个平台随附的呈现器。 当[ `ViewCell` ](xref:Xamarin.Forms.ViewCell) Xamarin.Forms 应用程序，在 iOS 中呈现`ViewCellRenderer`类实例化时，这反过来实例化本机`UITableViewCell`控件。 在 Android 平台上`ViewCellRenderer`类实例化本机`View`控件。 在通用 Windows 平台 (UWP)，`ViewCellRenderer`类实例化本机`DataTemplate`。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了之间的关系[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)和相应的本机控件实现它：

![](viewcell-images/viewcell-classes.png "ViewCell 控件与实现的本机控件之间的关系")

渲染过程时可以执行利用通过创建自定义呈现器为实现特定于平台的自定义[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Cell)Xamarin.Forms 自定义单元格。
1. [使用](#Consuming_the_Custom_Cell)Xamarin.Forms 中的自定义单元格。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的单元格的自定义呈现器。

每个项将现在讨论反过来，实现`NativeCell`呈现器都使用了 Xamarin.Forms 中承载的每个单元的特定于平台的布局[ `ListView` ](xref:Xamarin.Forms.ListView)控件。 这会阻止被重复调用期间的 Xamarin.Forms 布局计算`ListView`滚动。

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>创建自定义单元格

可以通过子类化创建一个自定义单元格控件[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)类，如下面的代码示例中所示：

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
`NativeCell`类在.NET Standard 库项目中创建和定义自定义单元格的 API。 自定义单元格公开`Name`， `Category`，和`ImageFilename`可以通过数据绑定显示的属性。 若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>使用自定义单元格

`NativeCell`自定义单元格可以是在 Xaml 中引用.NET Standard 库项目中通过声明其位置的命名空间和自定义单元格元素上使用的命名空间前缀。 下面的代码示例演示如何将`NativeCell`自定义单元格可供 XAML 页：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配的自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义单元格。

下面的代码示例演示如何将`NativeCell`自定义单元格可供 C# 页：

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)控件用于显示数据，通过填充一系列[ `ItemSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)属性。 [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略尝试最大程度减少`ListView`通过回收列表单元格占用的内存和执行速度。 有关详细信息，请参阅[缓存策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

在列表中的每一行都包含三个项的数据-名称、 类别和图像文件名。 在列表中每行的布局由定义`DataTemplate`通过引用[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)可绑定属性。 `DataTemplate`定义会在列表中的数据的每个行`NativeCell`，它显示其`Name`， `Category`，和`ImageFilename`通过数据绑定的属性。 有关详细信息`ListView`控件，请参阅[ListView](~/xamarin-forms/user-interface/listview/index.md)。

自定义呈现器现在可以添加到自定义的每个单元的特定于平台的布局的每个应用程序项目。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建一个子类`ViewCellRenderer`呈现自定义单元格的类。
1. 重写呈现自定义单元格的特定于平台的方法并编写逻辑以其进行自定义。
1. 添加`ExportRenderer`属性到自定义呈现器类，以指定它将用于呈现 Xamarin.Forms 自定义单元格。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，它是可选提供每个平台项目中的自定义呈现器。 如果未注册的自定义呈现器，则将使用默认的呈现器的控件的基类。 但是，自定义呈现器呈现时所需的每个平台项目中[ViewCell](xref:Xamarin.Forms.ViewCell)元素。

下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](viewcell-images/solution-structure.png "NativeCell 自定义呈现器项目职责")

`NativeCell`自定义单元格呈现的特定于平台的呈现器类，它们都派生自`ViewCellRenderer`为每个平台的类。 这会导致每个`NativeCell`自定义单元格呈现特定于平台的布局，如以下屏幕截图中所示：

![](viewcell-images/screenshots.png "每个平台上 NativeCell")

`ViewCellRenderer`类公开特定于平台的方法来呈现自定义单元格。 这是`GetCell`iOS 平台上的方法`GetCellCore`Android 平台上的方法和`GetTemplate`UWP 上的方法。

每个自定义呈现器类用修饰`ExportRenderer`与 Xamarin.Forms 结合注册呈现器的属性。 该属性采用两个参数-Xamarin.Forms 单元格所呈现的类型名称和自定义呈现器的类型名称。 `assembly`到的属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 ios 设备上创建自定义呈现器

下面的代码示例显示了为 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCell`调用方法来生成要显示每个单元格。 每个单元格是`NativeiOSCell`实例，用于定义单元和其数据的布局。 操作`GetCell`方法所依赖[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略：

- 当[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略是[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)，则`GetCell`方法将调用的每个单元。 一个`NativeiOSCell`将为每个创建实例`NativeCell`最初在屏幕显示的实例。 当用户通过滚动`ListView`，`NativeiOSCell`实例将重新使用。 有关 iOS 单元格重复使用的详细信息，请参阅[单元格重用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。

  > [!NOTE]
  > 此自定义呈现器代码将执行一些单元格重复使用，即使[ `ListView` ](xref:Xamarin.Forms.ListView)设置保留单元格。

  每个显示的数据`NativeiOSCell`实例，新创建的或重复使用，是否将更新从每个数据`NativeCell`实例通过`UpdateCell`方法。

  > [!NOTE]
  > `OnNativeCellPropertyChanged`方法将永远不会调用何时[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略设置为保留的单元格。

- 当[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略是[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)，则`GetCell`最初在屏幕上显示每个单元格将调用方法。 一个`NativeiOSCell`将为每个创建实例`NativeCell`最初在屏幕显示的实例。 每个显示的数据`NativeiOSCell`中的数据将更新实例`NativeCell`实例通过`UpdateCell`方法。 但是，`GetCell`不会调用方法，当用户滚动通过`ListView`。 相反，`NativeiOSCell`实例将重新使用。 `PropertyChanged` 将在引发事件`NativeCell`实例时数据发生更改，并`OnNativeCellPropertyChanged`事件处理程序将更新的数据在每个重复使用`NativeiOSCell`实例。

下面的代码示例演示`OnNativeCellPropertyChanged`方法调用时`PropertyChanged`引发事件：

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

此方法将更新所显示的数据重新使用`NativeiOSCell`实例。 检查已更改的属性变得很，因为可以多次调用方法。

`NativeiOSCell`类定义每个单元格的布局，并在下面的代码示例所示：

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

此类定义用于呈现该单元格的内容和其布局的控件。 类实现[ `INativeElementView` ](xref:Xamarin.Forms.INativeElementView)接口，这是需要[ `ListView` ](xref:Xamarin.Forms.ListView)使用[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略。 此接口指定的类必须实现[ `Element` ](xref:Xamarin.Forms.INativeElementView.Element)属性，它应返回回收单元格的自定义单元格数据。

`NativeiOSCell`构造函数初始化的外观`HeadingLabel`， `SubheadingLabel`，和`CellImageView`属性。 这些属性用于显示中存储的数据`NativeCell`实例，与`UpdateCell`方法被调用来设置每个属性的值。 此外，当[ `ListView` ](xref:Xamarin.Forms.ListView)使用[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略，所显示的数据`HeadingLabel`， `SubheadingLabel`，和`CellImageView`属性可以是通过更新`OnNativeCellPropertyChanged`中自定义呈现器的方法。

通过执行单元格布局`LayoutSubviews`重写，该设置的坐标`HeadingLabel`， `SubheadingLabel`，和`CellImageView`单元格内。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCellCore`调用方法来生成要显示每个单元格。 每个单元格是`NativeAndroidCell`实例，用于定义单元和其数据的布局。 操作`GetCellCore`方法所依赖[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略：

- 当[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略是[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)，则`GetCellCore`方法将调用的每个单元。 一个`NativeAndroidCell`将为每个创建`NativeCell`最初在屏幕显示的实例。 当用户通过滚动`ListView`，`NativeAndroidCell`实例将重新使用。 有关 Android 单元格重复使用的详细信息，请参阅[重复使用行视图](~/android/user-interface/layouts/list-view/populating.md)。

  > [!NOTE]
  > 请注意此自定义呈现器代码将执行一些单元格重复使用，即使[ `ListView` ](xref:Xamarin.Forms.ListView)设置保留单元格。

  每个显示的数据`NativeAndroidCell`实例，新创建的或重复使用，是否将更新从每个数据`NativeCell`实例通过`UpdateCell`方法。

  > [!NOTE]
  > 请注意，当`OnNativeCellPropertyChanged`方法将调用何时[ `ListView` ](xref:Xamarin.Forms.ListView)是设置为保留单元，它将不会更新`NativeAndroidCell`属性值。

- 当[ `ListView` ](xref:Xamarin.Forms.ListView)缓存策略是[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)，则`GetCellCore`最初在屏幕上显示每个单元格将调用方法。 一个`NativeAndroidCell`将为每个创建实例`NativeCell`最初在屏幕显示的实例。 每个显示的数据`NativeAndroidCell`中的数据将更新实例`NativeCell`实例通过`UpdateCell`方法。 但是，`GetCellCore`不会调用方法，当用户滚动通过`ListView`。 相反，`NativeAndroidCell`实例将重新使用。  `PropertyChanged` 将在引发事件`NativeCell`实例时数据发生更改，并`OnNativeCellPropertyChanged`事件处理程序将更新的数据在每个重复使用`NativeAndroidCell`实例。

下面的代码示例演示`OnNativeCellPropertyChanged`方法调用时`PropertyChanged`引发事件：

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

此方法将更新所显示的数据重新使用`NativeAndroidCell`实例。 检查已更改的属性变得很，因为可以多次调用方法。

`NativeAndroidCell`类定义每个单元格的布局，并在下面的代码示例所示：

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

此类定义用于呈现该单元格的内容和其布局的控件。 类实现[ `INativeElementView` ](xref:Xamarin.Forms.INativeElementView)接口，这是需要[ `ListView` ](xref:Xamarin.Forms.ListView)使用[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略。 此接口指定的类必须实现[ `Element` ](xref:Xamarin.Forms.INativeElementView.Element)属性，它应返回回收单元格的自定义单元格数据。

`NativeAndroidCell`构造函数增大`NativeAndroidCell`布局，并将初始化`HeadingTextView`， `SubheadingTextView`，和`ImageView`夸大布局中控件的属性。 这些属性用于显示中存储的数据`NativeCell`实例，与`UpdateCell`方法被调用来设置每个属性的值。 此外，当[ `ListView` ](xref:Xamarin.Forms.ListView)使用[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略，所显示的数据`HeadingTextView`， `SubheadingTextView`，和`ImageView`属性可以是通过更新`OnNativeCellPropertyChanged`中自定义呈现器的方法。

下面的代码示例显示的布局定义`NativeAndroidCell.axml`布局文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

此布局指定两个`TextView`控件和一个`ImageView`控件用于显示单元格的内容。 这两个`TextView`控件是在垂直方向`LinearLayout`控件，与中所包含的所有控件`RelativeLayout`。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

下面的代码示例显示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

`GetTemplate`调用方法以返回要呈现列表中的数据的每一行的单元格。 它会创建`DataTemplate`为每个`NativeCell`实例将与显示在屏幕上，`DataTemplate`定义外观和单元格的内容。

`DataTemplate`存储在应用程序级资源字典中，并在下面的代码示例所示：

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

`DataTemplate`指定用来显示该单元格，其布局和外观的内容的控件。 两个`TextBlock`控件和一个`Image`控件用于显示通过数据绑定的单元格的内容。 此外，实例`ConcatImageExtensionConverter`用于连接`.jpg`文件扩展名为每个图像文件名称。 这可确保`Image`控件可以加载并呈现图像时`Source`属性设置。

## <a name="summary"></a>总结

本文演示了如何创建自定义呈现器[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)位于 Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)控件。 这会阻止被重复调用期间的 Xamarin.Forms 布局计算`ListView`滚动。


## <a name="related-links"></a>相关链接

- [ListView 性能](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
