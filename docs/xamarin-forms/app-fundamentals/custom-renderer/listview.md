---
title: "自定义 ListView"
description: "Xamarin.Forms ListView 是作为垂直列表中显示的数据集合的视图。 本文演示如何创建自定义呈现器，用于封装特定于平台的列表控件和本机单元格的布局，允许更好地控制本机列表控件的性能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: eb4cf0285585351db5c45dc34a382236e6805c99
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-a-listview"></a>自定义 ListView

_Xamarin.Forms ListView 是作为垂直列表中显示的数据集合的视图。本文演示如何创建自定义呈现器，用于封装特定于平台的列表控件和本机单元格的布局，允许更好地控制本机列表控件的性能。_

Xamarin.Forms 中的每个视图已随附的呈现器针对每个平台创建的本机控件的实例。 当[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) Xamarin.Forms 应用程序，在 iOS 中呈现`ListViewRenderer`该类进行实例化，这反过来实例化一个本机`UITableView`控件。 在 Android 平台上，`ListViewRenderer`类实例化一个本机`ListView`控件。 在 Windows Phone 和通用 Windows 平台 (UWP) 上`ListViewRenderer`类实例化一个本机`ListView`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控件和相应的本机控件实现它：

![](listview-images/listview-classes.png "ListView 控件的实现的本机控件之间的关系")

呈现进程可以拍摄利用来实现特定于平台的自定义项创建的自定义呈现器[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_ListView_Control)Xamarin.Forms 自定义控件。
1. [使用](#Consuming_the_Custom_Control)向 Xamarin.Forms 的自定义控件。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的控件的自定义呈现器。

每个项将现在讨论反过来，若要实现`NativeListView`利用特定于平台的列表控件和本机单元格的布局的呈现器。 移植现有的本机应用程序包含列表和可以重复使用的单元格代码时，此方案很有用。 此外，它允许列表控件功能，可能会影响性能，比如数据虚拟化的详细自的定义。

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>创建自定义 ListView 控件

自定义[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)可以通过子类化创建控件`ListView`类，如下面的代码示例中所示：

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

`NativeListView`在可移植类库 (PCL) 项目中创建并定义自定义控件的 API。 此控件可公开`Items`属性用于填充`ListView`数据，并可通过绑定到的数据显示目的。 它还公开`ItemSelected`每当特定于平台的本机列表控件中选择项将激发的事件。 若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自定义控件

`NativeListView`自定义控件可以引用 Xaml 中 PCL 项目中通过声明其位置的命名空间并在控件上使用的命名空间前缀。 下面的代码示例演示如何`NativeListView`自定义控件可供 XAML 页：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
        <Grid>
          <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*" />
          </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
          <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

下面的代码示例演示如何`NativeListView`自定义控件可供 C# 页：

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

`NativeListView`自定义控件使用特定于平台的自定义呈现器来显示数据，通过填充该字段的列表`Items`属性。 在列表中的每一行都包含三个项的数据 – 一个名称、 类别和图像文件名。 由特定于平台的自定义呈现器定义的列表中每个行的布局。

> [!NOTE]
> 因为`NativeListView`将使用包括滚动功能的特定于平台的列表控件呈现自定义控件、 自定义控件应不承载在可滚动的布局控件如[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)。

自定义呈现器现在可以添加到每个应用程序项目来创建特定于平台的列表控件和本机单元格的布局。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`ListViewRenderer`呈现自定义控件的类。
1. 重写`OnElementChanged`呈现它进行自定义的自定义控件和写入的逻辑的方法。 此方法调用时相应 Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)创建。
1. 添加`ExportRenderer`到自定义呈现器类，以指定，它将用于呈现 Xamarin.Forms 自定义控件属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> 它是可选若要提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用该单元格的基类的默认呈现器。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](listview-images/solution-structure.png "NativeListView 自定义呈现器项目职责")

`NativeListView`自定义控件呈现特定于平台的呈现器类，该类的所有派生自通过`ListViewRenderer`为每个平台的类。 这将导致每个`NativeListView`自定义控件呈现特定于平台的列表控件与本机单元格的布局，如以下屏幕截图中所示：

![](listview-images/screenshots.png "每个平台上的 NativeListView")

`ListViewRenderer`类会公开`OnElementChanged`方法，该创建 Xamarin.Forms 自定义控件呈现相应的本机控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序，`OldElement`属性将为`null`和`NewElement`属性将包含对引用`NativeListView`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行本机控件自定义的位置。 可以通过访问在平台上使用本机控件的类型化的引用`Control`属性。 此外，可以通过获取对呈现 Xamarin.Forms 控件的引用`Element`属性。

订阅中的事件处理程序时必须小心`OnElementChanged`方法，如下面的代码示例中所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

本机控件应仅配置并自定义呈现器附加到新的 Xamarin.Forms 元素事件处理程序订阅。 同样，仅当元素呈现器附加到更改时，任何已订阅的事件处理程序应为取消订阅。 采用此方法将有助于创建不会受到内存泄漏的自定义呈现器。

重写的版本`OnElementPropertyChanged`中每个特定于平台的呈现器类，方法是 Xamarin.Forms 自定义控件上的可绑定的属性更改作出响应的位置。 更改的属性应始终会进行检查，如可以多次调用此重写。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

下面的代码示例演示为 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

`UITableView`控件配置通过创建的实例`NativeiOSListViewSource`类，前提是自定义呈现器附加到新 Xamarin.Forms 元素。 此类提供了数据到`UITableView`通过重写控件`RowsInSection`和`GetCell`方法从`UITableViewSource`类，并通过公开`Items`属性，它包含的数据要显示的列表。 此类还提供`RowSelected`时，将调用的方法重写`ItemSelected`事件由`NativeListView`自定义控件。 有关重写的有关方法的详细信息，请参阅[子类化 UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell`方法返回`UITableCellView`，在列表中，每行数据填充，并在下面的代码示例所示：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

此方法创建`NativeiOSListViewCell`将屏幕显示的数据的每一行的实例。 `NativeiOSCell`实例定义的每个单元格和单元格的数据布局。 当从由于滚动屏幕上消失单元格时，该单元格将可供重复使用。 这样就避免了通过确保仅有的浪费内存`NativeiOSCell`要显示在屏幕上，而不是所有在列表中的数据的数据的实例。 有关单元格重复使用的详细信息，请参阅[单元格重用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell`方法还读取`ImageFilename`属性的每行数据，提供它存在，并读取图像，并将其存储为`UIImage`实例，在更新前的`NativeiOSListViewCell`实例 （名称、 类别和映像） 的数据行。

`NativeiOSListViewCell`类定义每个单元格的布局，并在下面的代码示例所示：

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

此类定义用于呈现该单元格的内容和其布局的控件。 `NativeiOSListViewCell`构造函数创建的实例`UILabel`和`UIImageView`控件，并初始化其外观。 这些控件用于显示每个行的数据的`UpdateCell`方法用于将此数据设置上`UILabel`和`UIImageView`实例。 设置这些实例的位置由重写的`LayoutSubviews`方法，通过指定其单元格内的坐标。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>响应上自定义控件的属性更改

如果`NativeListView.Items`属性由于项被添加到更改，或从列表中删除，自定义呈现器需要以便通过显示所做的更改做出响应。 这可以通过重写实现`OnElementPropertyChanged`方法，下面的代码示例中所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

该方法将创建的新实例`NativeiOSListViewSource`提供到的数据的类`UITableView`控件中，提供的可绑定`NativeListView.Items`属性已更改。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

本机`ListView`控件配置为提供的自定义呈现器附加到新 Xamarin.Forms 元素。 此配置涉及到创建的实例`NativeAndroidListViewAdapter`于本机提供数据的类`ListView`控制，并注册事件处理程序处理`ItemClick`事件。 反过来，将调用此处理程序`ItemSelected`事件由`NativeListView`自定义控件。 `ItemClick`如果 Xamarin.Forms 元素呈现器附加到更改从订阅的事件，将取消。

`NativeAndroidListViewAdapter`派生自`BaseAdapter`类并公开`Items`属性，它包含的数据要显示的列表，以及重写`Count`， `GetView`， `GetItemId`，和`this[int]`方法。 有关这些方法重写的详细信息，请参阅[实现 ListAdapter](~/android/user-interface/layouts/list-view/populating.md)。 `GetView`方法返回的每一行，用数据填充的视图，并在下面的代码示例所示：

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

`GetView`调用方法以返回要呈现的单元格作为`View`，列表中的数据的每一行。 它将创建`View`实例将显示在屏幕上，具有的外观的数据的每一行`View`正在的布局文件中定义的实例。 当从由于滚动屏幕上消失单元格时，该单元格将可供重复使用。 这样就避免了通过确保仅有的浪费内存`View`要显示在屏幕上，而不是所有在列表中的数据的数据的实例。 有关视图重复使用的详细信息，请参阅[行视图重新使用](~/android/user-interface/layouts/list-view/populating.md)。

`GetView`方法还使用填充`View`具有数据，包括从中指定的文件名读取图像数据实例`ImageFilename`属性。

通过本机每个单元格 dispayed 布局`ListView`中定义`NativeAndroidListViewCell.axml`布局文件，它被放大`LayoutInflater.Inflate`方法。 下面的代码示例演示布局定义：

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

此布局指定两个`TextView`控件和`ImageView`控件用于显示单元格的内容。 这两个`TextView`控件是垂直方向内`LinearLayout`控件，与正在中包含的所有控件`RelativeLayout`。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>响应上自定义控件的属性更改

如果`NativeListView.Items`属性由于项被添加到更改，或从列表中删除，自定义呈现器需要以便通过显示所做的更改做出响应。 这可以通过重写实现`OnElementPropertyChanged`方法，下面的代码示例中所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

该方法将创建的新实例`NativeAndroidListViewAdapter`于本机提供数据的类`ListView`控件中，提供的可绑定`NativeListView.Items`属性已更改。

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>在 Windows Phone 上创建自定义呈现器和 UWP

下面的代码示例显示了 Windows Phone 和 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeWinPhoneListViewRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class NativeWinPhoneListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

本机`ListView`控件配置为提供的自定义呈现器附加到新 Xamarin.Forms 元素。 此配置涉及设置如何本机`ListView`控件将响应项选择，由该控件，定义的外观和内容的每个单元格，并注册事件处理程序处理填充数据显示`SelectionChanged`事件。 反过来，将调用此处理程序`ItemSelected`事件由`NativeListView`自定义控件。 `SelectionChanged`如果 Xamarin.Forms 元素呈现器附加到更改从订阅的事件，将取消。

外观和内容的每个本机`ListView`由定义单元格`DataTemplate`名为`ListViewItemTemplate`。 这`DataTemplate`存储在应用程序级别的资源字典中，并在下面的代码示例所示：

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

`DataTemplate`指定用来显示该单元格，及其布局和外观的内容的控件。 两个`TextBlock`控件和`Image`控件用于显示通过数据绑定的单元格的内容。 此外，实例`ConcatImageExtensionConverter`用于串联`.jpg`文件扩展名为每个图像文件名称。 这样可确保`Image`控件可以加载并呈现图像时`Source`属性设置。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>响应上自定义控件的属性更改

如果`NativeListView.Items`属性由于项被添加到更改，或从列表中删除，自定义呈现器需要以便通过显示所做的更改做出响应。 这可以通过重写实现`OnElementPropertyChanged`方法，下面的代码示例中所示：

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

该方法重新填充本机`ListView`控件的已更改的数据，提供的可绑定`NativeListView.Items`属性已更改。

## <a name="summary"></a>摘要

本文演示了如何创建自定义呈现器，用于封装特定于平台的列表控件和本机单元格的布局，允许更好地控制本机列表控件的性能。


## <a name="related-links"></a>相关链接

- [CustomRendererListView （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
