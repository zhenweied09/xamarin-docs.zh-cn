---
title: MonoTouch.Dialog 简介
description: MonoTouch.Dialog （保持联系D） 工具包是必不可少的框架，用于快速应用程序 UI 中 Xamarin.iOS 的开发。 保持联系D 可以快速、 轻松地定义复杂的应用程序使用的声明性方法，而不是内容的导航控制器、 表等麻烦的 UI。此外，保持联系D 具有灵活的一组 Api，开发人员提供一个完整的控件或将消息的方法，以及其他功能，例如加载请求刷新背景图像，请搜索支持和 JSON 数据通过动态 UI 生成。 本指南介绍使用保持联系的不同方式D，然后深层深入高级用法。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: be979b35ffdd597dae74f1f661a381ae44433b10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-monotouchdialog"></a>MonoTouch.Dialog 简介

_MonoTouch.Dialog （保持联系D） 工具包是必不可少的框架，用于快速应用程序 UI 中 Xamarin.iOS 的开发。保持联系D 可以快速、 轻松地定义复杂的应用程序使用的声明性方法，而不是内容的导航控制器、 表等麻烦的 UI。此外，保持联系D 具有灵活的一组 Api，开发人员提供一个完整的控件或将消息的方法，以及其他功能，例如加载请求刷新背景图像，请搜索支持和 JSON 数据通过动态 UI 生成。本指南介绍使用保持联系的不同方式D，然后深层深入高级用法。_


MonoTouch.Dialog，称为保持联系D 简称，是一个快速 UI 开发工具包，使开发人员可以构建应用程序屏幕和使用的信息，而不是内容的创建视图控制器、 表等麻烦的导航。在这种情况下，它提供用户界面开发和代码降低显著的简化。 例如，考虑下面的屏幕快照：

 [![](images/image1.png "例如，考虑此屏幕截图")](images/image1.png#lightbox)

下面的代码用于定义此整个屏幕：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

在使用在 iOS 中的表，是通常大量重复代码。
例如，每次需要一个表，则数据源需要来填充该表。 已通过导航控制器连接的两个表基于屏幕的应用程序，每个屏幕共享相同的代码很多。

保持联系D，从而简化，将所有代码封装到一个泛型 API 为创建表。 然后，它提供了基于该 API，从而使绑定可以更容易的语法声明性对象的抽象。 在这种情况下，两个 Api 中有可用保持联系D:

-   **低级别元素 API** –*元素 API*基于创建表示屏幕和及其组件的元素的分层树。 元素 API 为开发人员提供了最大的灵活性和控制中创建 Ui。 此外，元素 API 具有高级通过 JSON，这允许非常快地声明，以及从服务器的动态 UI 生成的声明性定义的支持。 
-   **高级反射 API** – 也称为*绑定**API*中的类进行批注与 UI 提示，然后保持联系D 自动创建基于对象的屏幕，并提供什么之间的绑定已显示 （并根据需要编辑） 在屏幕上，并且基础对象备份。 上面的示例所示使用反射 API。 此 API 不提供细粒度控制元素 API 执行，但它通过自动构建基于类属性的元素层次结构减少了复杂性甚至可更进一步。 


保持联系D 来自与大量的已打包的屏幕创建 UI 元素中生成，但它还可以识别所需自定义的元素和高级的屏幕布局。 在这种情况下，可扩展性是第一类特色甜 api。 开发人员可以扩展现有元素或创建新的和无缝集成。

此外，保持联系D 具有大量的内置的如"请求刷新"支持、 异步图像加载，以及搜索支持的常见 iOS UX 功能。

本文将全面了解在使用保持联系D，包括：

-   **保持联系D 组件**– 这将重点介绍了解构成保持联系类若要启用获取快速掌握的 D。 
-   **元素引用**– 保持联系的内置元素的完整列表D。 
-   **高级用法**– 这包含高级的功能，如请求刷新、 搜索、 背景图像加载、 使用 LINQ 将构建出元素层次结构和创建自定义元素，单元格，并为控制器将用于保持联系D。 

## <a name="understanding-the-pieces-of-mtd"></a>了解保持联系的部分D

即使是在使用反射 API，保持联系D 创建实质上，元素层次结构，就像已通过元素 API 直接创建。 此外，在上一节中提到的 JSON 支持以及创建元素。 因此，务必要保持联系的构成部分的基本了解D。

保持联系D 生成使用以下四个部分的屏幕：

-  **DialogViewController**
-  **RootElement**
-  **节**
-  **元素**


### <a name="dialogviewcontroller"></a>DialogViewController

A *DialogViewController*，或*DVC*简称，为继承自`UITableViewController`，因此代表与表的屏幕。 DVCs 可以推送到一样正则 UITableViewController 导航控制器上。

### <a name="rootelement"></a>RootElement

A *RootElement*是进入 DVC 的项的顶级容器。 它包含部分，然后可以包含的元素。 不会呈现 RootElements;相反，它们是只需实际获取呈现效果的容器。 RootElement 分配给 DVC，然后 DVC 呈现及其子级。

### <a name="section"></a>节

部分是一组表中的单元格。 如与普通表部分中，它可根据需要使用页眉和页脚可以是文本或甚至是自定义视图，如下面的屏幕截图所示：

 [![](images/image2.png "与普通表部分中，它可根据需要为页眉和页脚可以是文本或甚至是自定义视图，如下所示此屏幕截图")](images/image2.png#lightbox)

### <a name="element"></a>元素

元素表示表中的实际的单元格。 保持联系D 中有多种表示不同的数据类型或不同的输入的元素。 例如，以下屏幕快照说明了几个可用的元素：

 [![](images/image3.png "例如，此屏幕快照说明了几个可用元素")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>多个启用的部分以及 RootElements

现在让我们讨论 RootElements 和部分中更多详细信息。

### <a name="rootelements"></a>RootElements

启动 MonoTouch.Dialog 过程需要在至少一个 RootElement。

如果使用部分/元素值初始化 RootElement 然后此值用于查找子元素将提供的配置，呈现显示右侧的摘要。 例如，下面的屏幕截图显示一个表左侧与包含详细信息屏幕右侧，"餐后甜点"，与所选 desert 的值一起的标题单元格。

 [![](images/image4.png "与包含详细信息屏幕右侧，餐后甜点，与所选 desert 的值一起的标题单元格左侧此屏幕截图显示了一个表")](images/image4.png#lightbox) [ ![ ](images/image5.png "这与包含详细信息屏幕右侧，餐后甜点，与所选 desert 的值一起的标题单元格左侧以下屏幕截图显示了一个表")](images/image5.png#lightbox)

根元素可以还可用于在部分内触发正在加载新的嵌套的配置页，如上所示。 在此模式下使用时提供的标题在节内在呈现时使用，并还用作标题子页。 例如：

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

在上述示例中，当用户点击"餐后甜点"MonoTouch.Dialog 将创建新的页并导航到与正在"餐后甜点"，并且具有三个值单选按钮组的根。

在此特定示例中，单选按钮组将选择"餐后甜点"部分中的"巧克力鱼"，因为我们传递的值"2"到 RadioGroup。 这意味着选取列表 （零索引） 上的第三项。

调用的 Add 方法或使用 C# 4 初始值设定项语法添加部分。
插入方法可用于插入部分及其动画。

如果你使用的组实例 （而不是 RadioGroup) 创建 RootElement RootElement 时显示的部分中的摘要值将的累计计数的所有 BooleanElements 和 CheckboxElements 具有 Group.Key 值与相同的密钥。

### <a name="sections"></a>部分

节是用于对在屏幕中的元素进行分组，并且它们是 RootElement 的唯一有效的直接子级。 部分可以包含的任何标准的元素，包括新 RootElements。

RootElements 嵌入在某个部分中用于导航到新的更深入地级别。

为字符串，或作为 UIViews，可以页眉和页脚部分。
通常将仅使用字符串，但若要创建自定义用户界面可以使用任何 UIView 作为页眉或页脚。 你可以使用字符串来创建它们如下：

```csharp
var section = new Section ("Header", "Footer")
```

若要使用视图，只需将视图传递给构造函数中：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>接收通知

#### <a name="handling-nsaction"></a>处理 NSAction

保持联系D 曲面`NSAction`作为处理回调的委托。
例如，假设你想要处理表格单元格创建保持联系触摸事件D。 使用保持联系创建元素时D，只需提供一个回调函数，如下所示：

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>检索元素值

结合`Element.Value`属性，该回调可以检索其他元素中设置的值。 例如，考虑以下代码：

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

如下所示，此代码将创建一个 UI。 此示例的完整演练，请参阅[元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教程。

 [![](images/image6.png "与 Element.Value 属性结合使用，该回调可以检索其他元素中设置的值")](images/image6.png#lightbox)

当用户按下表单元格时，代码中的匿名函数的执行，写入从值`element`到实例**应用程序输出**填充在 Visual Studio for mac。

## <a name="built-in-elements"></a>内置元素

保持联系D 附带的内置表格单元格项目称为元素数。
这些元素用于在如字符串、 浮点数、 日期和甚至映像，只需少量的名称的表单元格中显示各种不同类型。 每个元素负责正确显示的数据类型。 例如，布尔值的元素将显示要切换其值的交换机。 同样，float 元素将显示滑块，以更改的 float 值。

有更复杂的元素以支持更丰富的数据类型，如图像和 html。 例如，html 元素，这将打开 UIWebView 加载网页选择时，表单元格中显示的标题。

### <a name="working-with-element-values"></a>处理元素值

用于捕获用户输入的元素公开公共`Value`随时保留当前值的元素的属性。 它将自动更新的因为用户使用应用程序。

这是属于 MonoTouch.Dialog，元素的所有行为，但都不需要用户创建的元素。

### <a name="string-element"></a>字符串元素

A`StringElement`表格单元格和单元格右侧的字符串值的左侧显示标题。

 [![](images/image7.png "StringElement 表格单元格和单元格右侧的字符串值的左侧显示标题")](images/image7.png#lightbox)

若要使用`StringElement`视为按钮，提供委托。

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "若要将用作按钮 StringElement，提供委托")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>带样式的字符串元素

A`StyledStringElement`允许使用任一内置表格单元格样式呈现的字符串或与自定义格式设置。

 [![](images/image9.png "StyledStringElement 允许使用任一内置表格单元格样式呈现的字符串或与自定义格式设置")](images/image9.png#lightbox)

`StyledStringElement`类派生自`StringElement`，但允许开发人员自定义少量的属性，如字体、 文本颜色、 单元格背景色、 行换行模式、 若要显示的行数，以及是否应显示附件。

### <a name="multiline-element"></a>多行的元素

 [![](images/image10.png "多行的元素")](images/image10.png#lightbox)

### <a name="entry-element"></a>条目元素

`EntryElement`、 同名意味着、 用于获取用户输入。 它支持普通字符串或字符隐藏其中的密码。

 [![](images/image11.png "EntryElement 用于获取用户输入")](images/image11.png#lightbox)

初始化具有三个值：

-  将向用户显示的项标题。
-  （这是为用户提供提示的灰扩展文本） 的占位符文本。 
-  将文本的值。


占位符和值可以为 null。 但是，标题是必需的。

在任何时刻，访问其值属性可以检索的值`EntryElement`。

此外`KeyboardType`属性可以设置在创建时所需的数据条目的键盘类型样式。 这可以用于配置使用的值的键盘`UIKeyboardType`如下所示：

-  Numeric
-  电话
-  URL
-  电子邮件


### <a name="boolean-element"></a>布尔值元素

 [![](images/image12.png "布尔值元素")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>复选框元素

 [![](images/image13.png "复选框元素")](images/image13.png#lightbox)

### <a name="radio-element"></a>单选元素

A`RadioElement`需要`RadioGroup`中指定`RootElement`。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "RadioElement 需要 RadioGroup RootElement 中指定")](images/image14.png#lightbox)

 `RootElements` 此外用于协调单选元素。 `RadioElement`成员可以跨多个部分 （例如为了实现类似于环音选择器和单独的自定义铃声从系统铃声）。 摘要视图将显示当前所选的单选元素。 若要使用此开关，创建`RootElement`使用组构造函数，如下：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

中的组的名称`RadioGroup`用于显示包含的页 （如果有） 中的选定的值和值，该值在此情况下为零，是第一个选定项的索引。

### <a name="badge-element"></a>徽章元素

 [![](images/image15.png "徽章元素")](images/image15.png#lightbox)

### <a name="float-element"></a>Float 元素

 [![](images/image16.png "Float 元素")](images/image16.png#lightbox)

### <a name="activity-element"></a>活动元素

 [![](images/image17.png "活动元素")](images/image17.png#lightbox)

### <a name="date-element"></a>日期元素

 ![](images/image18.png "日期元素")

选中对应于 DateElement 的单元格时，日期选取器会看到如下所示：

 [![](images/image19.png "如所示选中对应于 DateElement 的单元格时，显示日期选取器")](images/image19.png#lightbox)

### <a name="time-element"></a>时间元素

 [![](images/image20.png "时间元素")](images/image20.png#lightbox)

选中对应于 TimeElement 的单元格时，时间选取器会看到如下所示：

 [![](images/image21.png "如所示选中对应于 TimeElement 的单元格时，显示时间选取器")](images/image21.png#lightbox)

### <a name="datetime-element"></a>DateTime 元素

 [![](images/image22.png "DateTime 元素")](images/image22.png#lightbox)

选中对应于 DateTimeElement 的单元格时，日期时间选取器会看到如下所示：

 [![](images/image23.png "如所示选中对应于 DateTimeElement 的单元格时，显示日期时间选取器")](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 元素

 [![](images/image24.png "HTML 元素")](images/image24.png#lightbox)

`HTMLElement`显示的值其`Caption`表单元格中的属性。 选择，即可`Url`分配给此元素是在中加载`UIWebView`控制如下所示：

 [![](images/image25.png "即可选择，如下所示，分配给此元素的 Url 加载 UIWebView 控件中")](images/image25.png#lightbox)

### <a name="message-element"></a>消息元素

 [![](images/image26.png "消息元素")](images/image26.png#lightbox)

### <a name="load-more-element"></a>加载多个元素

使用此元素可允许用户加载更多项列表中。 你可以自定义普通和加载标题以及字体和文本颜色。
`UIActivity`指示器将启动对进行动画处理，并加载标题显示在用户点击该单元格时，然后`NSAction`传入执行构造函数。 一次中的代码`NSAction`完成之后，`UIActivity`指示器停止动画处理，并且正常的标题会再次显示。

### <a name="uiview-element"></a>UIView 元素

此外，任何自定义`UIView`可以使用显示`UIViewElement`。

### <a name="owner-drawn-element"></a>所有者描述的元素

此元素必须要子类化，因为它是一个抽象类。 应重写`Height(RectangleF bounds)`方法应在其中返回的元素的高度，以及`Draw(RectangleF bounds, CGContext context, UIView view)`并在这你应在给定限制之内，所有自定义的绘制使用上下文和视图的参数。 此元素会执行繁重的子类化`UIView`，并将其放在单元格中才能返回，让你仅需要实现两个简单的替代。 你可以看到的示例应用程序中的更佳示例实现`DemoOwnerDrawnElement.cs`文件。

下面是实现此类的一个非常简单示例：

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
 }
```

### <a name="json-element"></a>JSON 元素

`JsonElement`是的一个子类`RootElement`扩展`RootElement`要能够从本地或远程 url 加载的嵌套子内容。

`JsonElement`是`RootElement`，可以在两种形式中实例化。 一个版本创建`RootElement`，将加载按需内容。 这些通过使用创建`JsonElement`在结束时，要加载的内容的 url 中需要的额外参数的构造函数：

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

其他窗体创建从本地文件或一个现有数据`System.Json.JsonObject`已具有分析：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

有关在 JSON 中使用保持联系的详细信息D，请参阅[JSON 元素演练](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough)教程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>请求刷新支持

 *请求-到-* *刷新*视觉效果最初位于*Tweetie2*变得在很多应用程序间的常用影响的应用程序。

若要将自动刷新请求支持添加到你的对话框中，只需执行两项操作： 挂钩的事件处理程序，用户将提取数据时收到通知并通知`DialogViewController`何时已加载数据返回到其默认状态。

挂接通知非常简单;只需将连接到`RefreshRequested`上的事件`DialogViewController`，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然后在你的方法`OnUserRequestedRefresh`，你将队列某些数据加载、 请求 net 中的某些数据或启动的线程来计算数据。 一旦已加载了数据，必须通知`DialogViewController`新数据位于中，并为其默认状态还原为视图，应执行此操作通过调用`ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜索客户支持联系

若要支持搜索，设置`EnableSearch`属性你`DialogViewController`。 你还可以设置`SearchPlaceholder`属性要用作搜索栏中的水印文本。

搜索将在用户键入时更改视图的内容。 它搜索在可见字段，并显示到用户。 `DialogViewController`公开三个方法，以编程方式启动、 终止或触发新的筛选器操作的结果。 下面列出了这些方法：

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


系统是可扩展的，因此如果你想，可以更改此行为。

### <a name="background-image-loading"></a>背景图像加载

MonoTouch.Dialog 包含[TweetStation](https://github.com/migueldeicaza/TweetStation)应用程序的图像加载程序。 此图像加载程序可以用来加载图像在后台，缓存的支持，并且可以在加载映像时通知你的代码。

它还将限制传出的网络连接数。

图像加载程序实现中`ImageLoader`类，你需要做是调用`DefaultRequestImage`方法，你将需要为你想要加载，映像的实例提供的 Uri`IImageUpdated`接口将时调用映像 ha已加载的 s。

如下面的代码从到的 Url 加载图像时`BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

ImageLoader 类公开的清除方法，则可以调用你想要发布的所有当前在内存中缓存的图像。 当前代码有一个缓存为 50 个映像。 如果你想要使用不同的缓存大小 （例如，如果你预期会太大，50 个映像将是巨大的映像），你刚刚创建的 ImageLoader 实例并传递你想要保留在缓存中的图像数量。

## <a name="using-linq-to-create-element-hierarchy"></a>使用 LINQ 创建元素层次结构

通过 LINQ 和 C# 初始化语法的更巧妙地使用情况，可以使用 LINQ 来创建元素层次结构。 例如，下面的代码从某些字符串数组创建屏幕和句柄单元格通过传递到每一个匿名函数的选择`StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

这无法轻松地结合的 XML 数据存储或数据库几乎完全从数据创建复杂的应用程序中的数据。

## <a name="extending-mtd"></a>扩展保持联系D

### <a name="creating-custom-elements"></a>创建自定义元素

你可以创建自己的元素，通过从现有元素或通过从根类元素派生。

若要创建自己的元素，你将想要重写以下方法：

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

如果您的元素可以有变量的大小，则需要实现`IElementSizing`接口，其中包含一种方法：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果你打算在实现你`GetCell`方法通过调用`base.GetCell(tv)`并且自定义返回的单元格，需要进行还重写`CellKey`属性以返回一个用于对您的元素，是唯一的键如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

对于大多数元素，但不是能为此方法的有效`StringElement`和`StyledStringElement`那些为各种呈现应用场景使用其自己的密钥集。 必须将复制这些类中的代码。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

反射和元素 API 使用相同`DialogViewController`。 有时你将想要自定义视图的外观或你可能想要使用的某些功能`UITableViewController`，超出基本 Ui 创建。

`DialogViewController`是仅的一个子类`UITableViewController`和你可以在相同的方式将自定义自定义它`UITableViewController`。

例如，如果你想要更改列表样式，可以是以下之一`Grouped`或`Plain`，无法将此值设置通过更改属性，当你创建的控制器，如下：

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

有关详细信息高级自定义项的`DialogViewController`，如设置其背景，就像子类它并重写了正确的方法，如下面的示例中所示：

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

自定义的另一点是中的以下虚拟方法`DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

此方法应返回的一个子类`DialogViewController.Source`其中单元格大小相当，用例或的一个子类`DialogViewController.SizingSource`单元格是否不相等。

你可以使用此替代捕获的任何`UITableViewSource`方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation)使用它来跟踪用户具有滚动到顶部，并相应地更新未读推文的数。

## <a name="validation"></a>验证

元素未提供验证本身作为也非常适合网页的模型和桌面应用程序执行不直接映射到 iPhone 交互模型。

如果你想要执行数据验证，应执行此操作，当用户输入的数据触发操作时。 例如<span class="ui">完成</span>或<span class="ui">下一步</span>上按钮顶部的工具栏中，或某些`StringElement`用作按钮转到下一个阶段。

这是将执行基本的输入的验证，其中可能更为复杂如检查与服务器的用户/密码组合的有效性的验证。

如何通知用户是错误的特定于应用程序。 无法弹出`UIAlertView`或显示提示。

## <a name="summary"></a>总结

本文介绍了大量的 MonoTouch.Dialog 有关的信息。 它讨论如何的基础知识保持联系D 配合使用和涵盖各种组件构成了保持联系D。 它还介绍了多种不同的元素和表的自定义项受保持联系D 和讨论如何保持联系D 可以用自定义元素扩展。 此外，它所述中保持联系的 JSON 支持允许从 JSON 动态创建元素的 D。


## <a name="related-links"></a>相关链接

- [段视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [段视频-使用方便地创建 iOS 用户界面 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [演练：使用元素 API 创建应用程序](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [演练：使用反射 API 创建应用程序](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [演练：使用 JSON 元素创建用户界面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON Markup](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [在 Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 类引用](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
