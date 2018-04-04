---
title: 情节提要简介
description: 情节提要是外观的可视表示形式和应用程序流。 Xamarin 引入了一个设计器，以允许 Xamarin.iOS 应用程序充分利用情节提要，以便您可以直观地设计你的应用程序屏幕和访问视图中，控制器和 segue 使用 C# 的获得更多控制。
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 647bd7d339dc56978752f7ab29de30cf8acb7e07
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-storyboards"></a>情节提要简介

_情节提要是外观的可视表示形式和应用程序流。Xamarin 引入了一个设计器，以允许 Xamarin.iOS 应用程序充分利用情节提要，以便您可以直观地设计你的应用程序屏幕和访问视图中，控制器和 segue 使用 C# 的获得更多控制。_

本指南中我们将介绍哪些情节提要并检查某些关键组件 – 例如 Segues。 我们将考察可以如何创建和使用，情节提要和开发人员必须哪些优点。

情节提要文件格式的引入了 apple iOS 应用程序的 UI 的可视表示形式之前，开发人员创建 XIB 文件为每个视图控制器，并且手动编程每个视图之间的导航。  使用情节提要允许开发人员定义视图控制器和设计图面上它们之间导航，并提供所见即所得编辑应用程序的用户界面。

情节提要可以创建、 打开和编辑使用 Xamarin iOS 设计器。 本指南也将演练如何使用设计器同时使用 C# 程序导航创建情节提要。


## <a name="requirements"></a>要求

情节提要可以用于 iOS 适用于 Mac 的 Visual Studio 中的设计器或 Visual Studio 2015 和 2017年安装 Xamarin 工作负荷。

## <a name="what-is-a-storyboard"></a>情节提要是什么？

情节提要是应用程序中的所有屏幕的可视表示。 它包含的场景，其中每个场景表示序列*视图控制器*及其*视图*。 这些视图可能包含的对象和[控件](~/ios/user-interface/controls/index.md)，将允许用户与你的应用程序进行交互。 视图和控件的此集合 (或*子视图*) 被称为*内容查看层次结构*。 连接场景通过 segue 对象，表示视图控制器之间的转换。 这是通常通过创建的初始视图上的对象和连接的视图之间 segue。 下图中，在设计图面上的关系图所示：

 [![](images/storyboardsview.png "在此图说明了在设计图面上的关系")](images/storyboardsview.png#lightbox)

如所示，情节提要将使用已呈现的内容设置布局每个你场景，并演示了它们之间的连接。  值得在此情况下，注意，当我们谈场景在 iPhone 上，则可以安全地假设一个*场景*情节提要上等同于一个*屏幕*的设备上的内容。 但是，与 iPad 很可能有多个场景出现在一次 – 例如，使用 Popover 视图控制器。

有很多好处使用情节提要来创建应用程序的 UI，尤其是在使用 Xamarin。 首先，它是为所有对象 – 包括可视表示形式 UI，[自定义控件](~/ios/user-interface/designer/ios-designable-controls-overview.md)– 在设计时呈现。 这意味着，在生成或部署应用程序可以可视化其外观和流之前。 以上图中，为例。 我们可以告诉从快速了解一下设计曲面有多少场景、 每个视图的布局和如何相关的所有内容。 这是使情节提要功能功能强大。

尤其是在使用 iOS 设计器时，事件是与情节提要，更易于管理。 大多数 UI 控件将在属性填充中有可能的事件的列表。 事件处理程序可以在此处添加并在视图控制器类中的分部方法完成...

情节提要的内容存储为 XML 文件。 在生成时，任何`.storyboard`文件编译到名为 nibs 的二进制文件。 在运行时，这些 nibs 初始化并实例化以创建新视图。

## <a name="segues"></a>Segue

A *Segue*，或*Segue 对象*，在 iOS 开发中使用来表示场景之间的转换。 若要创建 segue，按住**Ctrl**键和单击并拖动一个场景到另一个。 我们拖动我们鼠标，则会显示蓝色连接器，指出其中 segue 都会产生与下图所示：

 [![](images/createsegue.png "蓝色连接器会出现，指示其中 segue 会在此图中所示")](images/createsegue.png#lightbox)

在鼠标弹起菜单将显示给出选择我们 segue 的操作。 它可能类似于下面的映像： 

**预 iOS 8 和大小类**:

[![](images/segue1.png "而无需大小类操作 Segue 下拉列表中")](images/segue1.png#lightbox)

**使用大小类和自适应 Segue 时**:

[![](images/16new.png "使用大小类操作 Segue 下拉列表中")](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果使用 VMWare 为你的 Windows 虚拟机，按住 Ctrl 并单击映射为_右键单击_默认情况下的鼠标按钮。 若要创建 Segue，编辑你键盘的首选项通过**首选项** > **键盘和鼠标** > **鼠标快捷方式**并将重新映射你**辅助按钮**如下所示：
> 
> [![](images/image22.png "键盘和鼠标首选项设置")](images/image22.png#lightbox)
> 
> 你现在应能够将像平时一样你视图控制器之间 segue 添加。

有不同类型的转换，每个给定控制如何向用户显示的新视图控制器和它如何与情节提要中的其他视图控制器交互。 下面对它们进行了解释。 还有可能子类 segue 对象，若要实现自定义转换为：

-  **显示 / 推送**– 推送 segue 将视图控制器添加到导航堆栈。 它假设发起推送的视图控制器与将被添加到堆栈视图控制器相同的导航控制器的一部分。 这执行同样的操作作为`pushViewController`，而通常用于在屏幕上的数据之间存在某种关系时。 使用推送 segue 可拥有的后退按钮和标题添加到允许向下查看层次结构中的导航钻取在堆栈上的每个视图的导航栏的 luxury。
-  **模式**– 模式 segue 创建您的项目，使用显示的动画转换选项中任何两个视图控制器之间的关系。 子视图控制器将完全遮盖父视图控制器时放入视图。 与不同的是推送 segue，这为我们; 添加后退按钮当使用在安装结束时 segue`DismissViewController`必须使用以返回到以前的视图控制器。
-  **自定义**– 任何自定义的一个子类，可创建 segue ` UIStoryboardSegue`。
-  **展开**– 一个展开 segue 可用于导航回通过推送或模式 segue – 例如，通过解除以模式方式显示的视图控制器来。 除此之外，你可以展开通过不只一个，但推送和模式的一系列 segue，并返回与单个导航层次结构中的多个步骤展开操作。 若要了解如何使用展开 segue iOS，读取中[创建展开 Segue](https://developer.xamarin.com/recipes/ios/general/storyboard/unwind_segue/)配方。
-  **Sourceless** – sourceless segue 指示包含初始视图控制器的场景，因此其查看用户将首先看到。 它表示由 segue 如下所示：  

    [![](images/sourcelesssegue.png "Sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

 iOS 8 引入[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)若要允许使用所有可用的屏幕大小，使开发人员能够创建用于所有的 iOS 设备的一个 UI iOS 情节提要文件。 默认情况下，所有新的 Xamarin.iOS 应用程序将使用大小类。 若要使用较旧项目的大小类，请参见[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南。 
 
使用大小类任何应用程序还将使用新[*自适应 Segue*](~/ios/user-interface/storyboards/unified-storyboards.md)。 使用大小类时，请记住，我们不直接指定的我们将使用 iPhone 或 iPad。 换而言之，我们将创建将始终具有相同的外观，而不考虑它必须使用多少房地产的一个 UI。 通过判断环境，并确定如何最大程度呈现内容的自适应 Segues 发挥作用。 自适应 Segue 如下所示： 

[![](images/adaptivesegue.png "自适应 Segue 的下拉列表中")](images/adaptivesegue.png#lightbox)

|Segue|描述|
|--- |--- |
|显示|这是非常类似于推送 segue，但它会考虑屏幕的内容。|
|显示详细信息|如果应用显示 （例如，在 iPad 上的拆分视图控制器） 的 master 和详细信息视图，内容将替换详细信息视图。 如果应用显示仅 master 或详细信息，内容将替换视图控制器堆栈的顶部。|
|展示|这类似于模式 segue，并允许针对选择的演示文稿和转换样式。|
|Popover 演示文稿|此显示内容，并且 popover|

### <a name="transferring-data-with-segues"></a>传输数据与 Segue

转换使用最终不 segue 的好处。 它们还可以用于管理视图控制器之间的数据传输。 这通过重写实现`PrepareForSegue`初始视图控制器和自行处理数据的方法。 触发 segue 后 – 例如，按下按钮 – 与应用程序将调用此方法，提供机会来准备新的视图控制器*之前*发生任何导航。 下面的代码，从[Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)示例，说明了这一点： 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
}
```

在此示例中，`PrepareForSegue`时由用户触发 segue，则将调用方法。 我们首先需要创建的接收的视图控制器实例并将此设为 segue 的目标视图控制器。 这可通过以下代码行：

```csharp
var callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

该方法现在已设置的属性的能力`DestinationViewController`。 在此示例中我们执行了利用此功能通过将传递一个名为列表`PhoneNumbers`到`CallHistoryController`并将其分配到具有相同名称的对象：

```csharp
if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
```

完成转换后，用户将看到`CallHistoryController`填充的列表。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>向非情节提要项目中添加情节提要

有时你可能需要添加到以前非提要文件的情节提要。 通过执行以下步骤中，可以简化一次执行此操作在 Visual Studio for Mac:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 通过浏览到创建一个新的情节提要文件**文件 > 新建文件 > iOS > 情节提要**，如下所示： 
    
    [![](images/new-storyboard-xs.png "新的文件对话框")](images/new-storyboard-xs.png#lightbox)

2. 添加到你的情节提要名称**主界面**部分**Info.plist**，如下所示：
    
    [![](images/infoplist.png "Info.plist 编辑器")](images/infoplist.png#lightbox)
    
    此命令的作用等效于实例化中的初始视图控制器`FinishedLaunching`内应用程序委托的方法。 设置此选项，应用程序将实例化一个窗口 （见下文）、 加载主情节提要，并将情节提要的初始视图控制器 （sourceless Segue 旁边的一个） 的实例指定为`RootViewController`窗口，然后使属性屏幕上可见的窗口。

3. 在`AppDelegate`，重写默认值`Window`方法替换为以下代码以实现窗口属性：
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 通过右键单击项目中创建一个新的情节提要文件**添加 > 新文件 > iOS > 空情节提要**，如下所示： 
    
    [![](images/new-storyboard-vs.png "新建项对话框")](images/new-storyboard-vs.png#lightbox)

2. 添加到你的情节提要名称**主界面**部分中的 ios 应用程序，如下所示：
    
    [![](images/ios-app.png "Info.plist 编辑器")](images/ios-app.png#lightbox)
    
    此命令的作用等效于实例化中的初始视图控制器`FinishedLaunching`内应用程序委托的方法。 设置此选项，应用程序将实例化一个窗口 （见下文）、 加载主情节提要，并将情节提要的初始视图控制器 （sourceless Segue 旁边的一个） 的实例指定为`RootViewController`窗口，然后使属性屏幕上可见的窗口。

3. 在`AppDelegate`，重写默认值`Window`方法替换为以下代码以实现窗口属性：

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>使用 iOS 设计器创建情节提要

对于 iOS，已无缝地与集成 Visual Studio 针对 Mac 和 Visual Studio 中使用 Xamarin 设计器，可以创建情节提要。

若要开始使用 iOS 设计器来创建情节提要，请按照[Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指南。 在本演练中将探讨使用 Segues，以及如何处理你的控件上的事件的视图控制器之间导航。

## <a name="instantiate-storyboards-manually"></a>手动实例化情节提要

情节提要完全替换单个 XIB 文件在项目中，但是在情节提要中的单独的视图控制器可以仍使用实例化`Storyboard.InstantiateViewController`。

有时应用程序具有不与设计器提供的内置情节提要转换处理的特殊要求。 例如，如果要创建的应用程序将启动从相同按钮，具体取决于应用程序的当前状态的不同屏幕我们可能想要手动，实例化的视图控制器和自己程序转换。

下面的屏幕截图显示两个我们设计图面上没有的视图控制器 segue 它们之间。 下一节将引导完成如何该转换可以设置在代码中。

 [![](images/viewcontrollerspink.png "此屏幕快照显示在设计图面上没有的两个视图控制器 segue 它们之间")](images/viewcontrollerspink.png#lightbox)

1. 添加_空 iPhone 情节提要_到现有项目项目：
    
    [![](images/add-storyboard1.png "添加情节提要")](images/add-storyboard1.png#lightbox)

2. 双击新创建的情节提要，以打开它，并添加一个新**导航控制器**到设计图面。 因为导航控制器是无用户界面，默认情况下它是否会向根视图控制器，如下所示：

    [![](images/uinavigationcontroller.png "Segue 的视图的域控制器")](images/uinavigationcontroller.png#lightbox)

3. 选择_视图控制器_通过单击底部的黑色栏。 在设计器的**属性填充**下**标识**我们可以为视图控制器中指定的自定义类，以及一个唯一的 ID。 设置**类名**和**情节提要 ID**到`MainViewController`。

    [![](images/identitypanelnew.png "指定自定义类")](images/identitypanelnew.png#lightbox)

4. 更高版本，我们将需要实例化我们查看控制器从情节提要，并将使用情节提要 ID 在我们的代码中引用它们。 设置要与情节提要 ID 匹配的还原 ID 可以确保如果需要为还原状态视图控制器获取重新创建正确。

5. 我们当前仅有一个视图控制器。 将另一个视图控制器拖到设计图面上。 在**属性填充**，在标识下设置的类和情节提要 ID `PinkViewController`，如下所示：

    [![](images/pinkvcnew.png "属性填充")](images/pinkvcnew.png#lightbox)
    
    IDE 将创建这些自定义类的视图控制器。 可以在查看这些**解决方案 Pad**，如下面的屏幕截图中所示：
    
    [![](images/solution-pad.png "解决方案填充")](images/solution-pad.png#lightbox)

6. 在`PinkViewController`，通过单击向控制器的框的中心选择的视图。 在属性区中，视图下更改**后台**洋红色到：
    
    [![](images/pinkcontroller.png "设置背景色")](images/pinkcontroller.png#lightbox)

7. 最后，将从按钮拖动**工具箱**到`MainViewController`。 在属性区中，将其命名`PinkButton`和标题 GoToPink，如下所示：

    [![](images/pinkbutton.png "设置按钮名称")](images/pinkbutton.png#lightbox)

情节提要已完成，但如果我们现在将项目部署，我们将获取空白的屏幕。 这是因为我们仍需要告知 IDE 使用我们情节提要，并设置根视图控制器，以充当第一个视图。 通常这可以通过我们项目选项中，如上所示。 但是在此示例中我们将通过相同的结果在代码中，添加以下**AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
    {
        UIWindow window;
        public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
        public static UIViewController initialViewController;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            window = new UIWindow (UIScreen.MainScreen.Bounds);

            initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

            window.RootViewController = initialViewController;
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

这就是大量的代码，但不熟悉几行。 首先，我们注册与我们情节提要**AppDelegate**通过传入情节提要的名称， **MainStoryboard**。 接下来，我们告知应用程序的调用来从情节提要的初始视图控制器实例化`InstantiateInitialViewController`上我们情节提要，我们将该视图控制器设置为我们的应用程序的根视图控制器。 此方法可确定该用户将看到，第一个屏幕，并创建该视图控制器的新实例。

请注意，在 IDE 已创建的解决方案窗格`MainViewcontroller.cs`类，并将其`corresponding designer.cs`我们的类名称添加到步骤 4 中的属性板时。 我们可以看到此类创建包括基类的特殊构造函数：

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


创建时使用设计器情节提要，IDE 将自动添加[[注册]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)顶部属性`designer.cs`类，并传入的字符串标识符，这与中指定的情节提要 ID 的相同上一步。 此选项将链接的 C# 到情节提要中的相关场景。

有时你可能想要添加现有类曾经**不**在设计器中创建。 在这种情况下，会将该类注册为正常：

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

有关注册的类和方法的详细信息，请参阅[类型注册机构](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/)文档。

此类中的最后一步是连接的按钮和转换到粉红色视图控制器。 我们将实例化`PinkViewController`从情节提要; 然后，我们将程序推送 segue 与`PushViewController`，如下面的示例代码所示：

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {

    }

    public override void AwakeFromNib ()
    {
    // Called when loaded from xib or storyboard.

    this.Initialize ();
    }

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

运行应用程序产生 2 屏幕应用程序：

![](images/finishedstoryboard.png "示例应用程序运行屏幕")

## <a name="conditional-segues"></a>Segue 的条件

通常，将从一个视图控制器移到下一步是依赖于特定的条件。 例如，如果我们已进行简单的登录屏幕我们只想要将移动到下一个屏幕*如果*具有已验证的用户名和密码。

在下一步的示例中我们将在上面的示例中添加密码字段。 用户将只能访问*PinkViewController*如果用户输入了正确的密码，否则将会显示错误。

开始之前，遵循步骤 1 – 8 上面。 在这些步骤，我们创建我们情节提要，开始创建我们的 UI，并告知我们的应用程序委托的视图控制器，用于按原样 RootViewController。

1. 现在，让我们构建我们的 UI，并添加到列出的其他视图`MainViewController`以使其在下面的屏幕截图中的外观：

    - UITextField
        - 名称： PasswordTextField
        - 输入密钥密码占位符：
    - UILabel
        - 文本: 错误： 错误的密码。 您不应将传递 ！
        - 颜色： 红色
        - 对齐方式： Center
        - 行： 2
        - 隐藏复选框已选中 
        
    [![](images/passwordvc.png "Center 行")](images/passwordvc.png#lightbox)
    
2. 创建转粉红色按钮和通过 Ctrl 将中的视图控制器之间 Segue *PinkButton*到*PinkViewController*，并选择**推送**鼠标弹起上. 

3. 单击 Segue 并为其提供*标识符* `SegueToPink`:

    [![](images/namesegue.png "单击 Segue 并为其提供标识符 SegueToPink")](images/namesegue.png#lightbox)  
    

4. 最后，添加以下 ShouldPerformSegue 方法`MainViewController`类：

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {
        
        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

在此代码中，我们已匹配到 segueIdentifier 我们`SegueToPink`segue，因此我们随后可以测试条件; 在此情况下有效的密码。 如果我们条件返回`true`，Segue 将执行，并会显示`PinkViewController`。 如果`false`，不会出现新的视图控制器。

通过检查 ShouldPerformSegue 方法 segueIdentifier 自变量，我们可以应用于此视图控制器上的任何 Segue 这种方法。 在这种情况下我们只能有一个 Segue 标识符 – `SegueToPink`。

请参阅中的 Storyboards.Conditional 解决方案[手动情节提要示例](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/)有关工作示例。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

情节提要引用，您可以采用大型、 复杂的情节提要设计并将其分解为较小的情节提要获取从原始引用，因此删除删除复杂性并使生成单个演示图板变得更容易设计和维护。

此外，可以提供情节提要引用_定位点_到相同的情节提要或上一个不同的特定场景中的另一个场景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加对外部的情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**  >  **iOS** > **情节提要**。 输入**名称**为新的情节提要单击**新建**按钮：
    
    [![](images/ref01.png "新的文件对话框")](images/ref01.png#lightbox)
    
2. 当你通常将并保存所做的更改，请设计新情节提要的场景的布局： 
    
    [![](images/ref02.png "新的场景的布局")](images/ref02.png#lightbox)
    
3. 打开想要在 iOS 设计器中添加对引用情节提要。

4. 拖动**情节提要引用**从**工具箱**拖到设计图面： 
    
    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在**小组件**选项卡**属性资源管理器**，选择的名称**情节提要**上面创建的： 

    [![](images/ref04.png "小组件选项卡")](images/ref04.png#lightbox)
    
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建： 

    [![](images/ref05.png "创建 segue")](images/ref05.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

将显示当运行该应用程序，用户单击 UI 元素上从来自外部的情节提要情节提要引用中指定的初始视图控制器创建 Segue。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

要添加对特定场景的引用外部情节提要，（而不是初始的视图控制器），执行以下操作：

1. 在**解决方案资源管理器**，双击外部的情节提要可以打开进行编辑。

2. 添加新的场景，并像通常那样设计其布局： 

    [![](images/ref07.png "新的场景布局")](images/ref07.png#lightbox)
    
3. 在**小组件**选项卡**属性资源管理器**，输入**情节提要 ID**为新场景的视图控制器： 

    [![](images/ref08.png "输入新的后台视图控制器情节提要 ID")](images/ref08.png#lightbox)
    
3. 打开想要在 iOS 设计器中添加对引用情节提要。

4. 拖动**情节提要引用**从**工具箱**拖到设计图面： 

    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在**小组件**选项卡**属性资源管理器**，选择的名称**情节提要**和**引用 ID** (情节提要 ID) 的上面创建的场景： 

    [![](images/ref09.png "小组件选项卡 ")](images/ref09.png#lightbox)
    
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建： 

    [![](images/ref10.png "创建 segue")](images/ref10.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

当在应用正在运行且用户单击的场景从创建 Segue 在 UI 元素上给定**情节提要 ID**将显示从情节提要引用中指定外部情节提要。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用相同的情节提要中的特定场景

若要添加到特定的场景相同情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**，双击情节提要可以打开进行编辑。

2. 添加新的场景，并像通常那样设计其布局： 

    [![](images/ref11.png "新的场景布局")](images/ref11.png#lightbox)

3. 在**小组件**选项卡**属性资源管理器**，输入**情节提要 ID**为新场景的视图控制器： 

    [![](images/ref12.png "小组件选项卡")](images/ref12.png#lightbox)
    
3. 拖动**情节提要引用**从**工具箱**拖到设计图面： 

    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在**小组件**选项卡**属性资源管理器**，选择**引用 ID** (情节提要 ID) 的上述步骤中创建的场景： 

    [![](images/ref13.png "小组件选项卡")](images/ref13.png#lightbox)
    
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建： 

    [![](images/ref14.png "创建 segue")](images/ref14.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

当在应用正在运行且用户单击的场景从创建 Segue 在 UI 元素上给定**情节提要 ID**将显示在相同的情节提要情节提要引用中指定。

## <a name="summary"></a>总结

本文介绍的情节提要和如何它们将是有益的开发中的 iOS 应用程序的概念。 它讨论场景、 查看控制器、 视图和视图层次结构和场景是如何与不同类型 Segues 配合相链接。  它还探讨了实例化的视图控制器手动从情节提要，并创建条件 Segues。



## <a name="related-links"></a>相关链接

- [手动情节提要 （示例）](https://developer.xamarin.com/samples/ManualStoryboard/)
- [IOS 设计器简介](~/ios/user-interface/designer/introduction.md)
- [转换到情节提要](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
