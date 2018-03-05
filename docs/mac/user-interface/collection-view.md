---
title: "集合视图"
description: "本指南介绍了使用 Xamarin.Mac 应用中的集合视图。 它涵盖创建和维护 Xcode 和接口生成器中的集合视图和以编程方式使用它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: d8fa54f23dfea063fa25f6e26e2df2c2ed82101e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="collection-views"></a>集合视图

_本指南介绍了使用 Xamarin.Mac 应用中的集合视图。它涵盖创建和维护 Xcode 和接口生成器中的集合视图和以编程方式使用它们。_

当使用 C# 和.NET 在 Xamarin.Mac 应用中，开发人员有权访问相同 AppKit 集合视图控件，开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，开发人员使用 Xcode 的_接口生成器_可以创建和维护集合视图。

A`NSCollectionView`显示一个网格的组织使用的子视图`NSCollectionViewLayout`。 在网格中的每个子视图由`NSCollectionViewItem`后者管理中的视图的内容加载`.xib`文件。

[ ![运行示例应用程序](collection-view-images/intro01.png)](collection-view-images/intro01.png)

本文介绍如何使用 Xamarin.Mac 应用中的集合视图的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和本文通篇使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>有关集合视图

集合视图的主要目标 (`NSCollectionView`) 是以可视方式排列一组使用集合视图的布局方式组织的对象 (`NSCollectionViewLayout`)，与每个单个对象 (`NSCollectionViewItem`) 更大的集合中获取其自己的视图。 集合视图通过数据绑定和键 / 值编码技术工作，在这种情况下，应读取[数据绑定和键 / 值编码](~/mac/app-fundamentals/databinding.md)文档，然后再继续执行本文。

集合视图都有任何标准的内置集合视图项 （类似大纲或表视图执行的操作），因此开发人员负责设计和实现_原型视图_如映像字段中使用其他 AppKit 控件文本字段、 标签等。此原型视图将用于显示和处理与集合视图由每个项并存储在`.xib`文件。

因为开发人员负责收集视图项的外观和感觉，集合视图具有用于突出显示在网格中的某个选定的项没有内置支持。 实现此功能将本文中所述。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定义数据模型

在接口生成器中，键-值编码 (KVC) 绑定集合视图的数据前必须在 Xamarin.Mac 应用程序，使其作为定义键 / 值观察 (KVO) 符合类 /_数据模型_绑定。 数据模型提供所有的数据，将显示在集合并接收对数据的用户运行该应用程序时在 UI 中进行任何修改。

需要的应用程序管理一组员工的示例，可以使用下面的类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

`PersonModel`数据模型将在整个本文的其余部分。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合视图

数据与集合视图为非常 like 绑定，表视图中，作为`NSCollectionViewDataSource`用于找不到提供数据。 由于集合视图不包含的预设的显示格式，是需要提供用户交互反馈，并跟踪用户选择更多工作。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>创建单元格原型

由于集合视图不包含默认单元格原型，开发人员将需要添加一个或多个`.xib`Xamarin.Mac 应用程序定义的布局和各个单元格的内容的文件。

请执行以下操作：

1. 在**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**
2. 选择**Mac** > **视图控制器**，为其提供一个名称 (如`EmployeeItem`在此示例中)，然后单击**新建**按钮以创建： 

    ![添加新视图控制器](collection-view-images/proto01.png)

    这将添加`EmployeeItem.cs`，`EmployeeItemController.cs`和`EmployeeItemController.xib`到项目的解决方案文件。
3. 双击`EmployeeItemController.xib`文件以打开它以在 Xcode 的接口生成器中编辑。
4. 添加`NSBox`，`NSImageView`和两个`NSLabel`到视图的控件和布置这些控件，如下所示：

    ![设计单元格原型的布局](collection-view-images/proto02.png)
5. 打开**助手编辑器中**并创建**Outlet**为`NSBox`以便可以使用它来指示的单元格的选择状态：

    ![公开在 Outlet NSBox](collection-view-images/proto03.png)
6. 返回到**标准编辑器**并选择图像视图。
7. 在**绑定检查器**，选择**将绑定到** > **文件的所有者**并输入**模型项路径**的`self.Person.Icon`:

    ![绑定图标](collection-view-images/proto04.png)
8. 选择的第一个标签和在**绑定检查器**，选择**将绑定到** > **文件的所有者**并输入**模型项路径**的`self.Person.Name`:

    ![绑定名称](collection-view-images/proto05.png)
9. 选择第二个标签并在**绑定检查器**，选择**将绑定到** > **文件的所有者**并输入**模型项路径**的`self.Person.Occupation`:

    ![绑定职业](collection-view-images/proto06.png)
10. 保存对`.xib`文件，并返回到 Visual Studio 来同步更改。

编辑`EmployeeItemController.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

查看此代码中详细信息，该类继承自`NSCollectionViewItem`使它可以作为集合视图单元格的原型。 `Person`属性公开了用于将数据绑定到的图像视图和标签在 Xcode 中的类。 这是实例`PersonModel`上述步骤中创建。

`BackgroundColor`属性是快捷方式`NSBox`控件的`FillColor`将用于显示单元格的选择状态。 通过重写`Selected`属性`NSCollectionViewItem`，下面的代码设置或清除此选择状态：

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>创建集合查看数据源

集合视图的数据源 (`NSCollectionViewDataSource`) 为集合视图中提供的所有数据和创建并填充集合视图单元格 (使用`.xib`原型) 所必需的集合中每个项。

添加新类项目中，调用它`CollectionViewDataSource`并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

查看此代码中详细信息，该类继承自`NSCollectionViewDataSource`和公开的列表`PersonModel`实例通过其`Data`属性。

由于此集合仅有一个部分，该代码重写`GetNumberOfSections`方法，始终返回`1`。 此外，`GetNumberofItems`方法被重写在中，它将返回中的项的数目`Data`属性列表。

`GetItem`只要新的单元格是必需的如下所示调用方法：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem`调用的集合视图的方法以创建或返回的可重用实例`EmployeeItemController`及其`Person`属性设置为项显示在请求的单元格。 

`EmployeeItemController`必须事先使用下面的代码的集合视图控制器注册：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

**标识符**(`EmployeeCell`) 中使用`MakeItem`调用_必须_与已注册集合视图的视图控制器的名称匹配。 此步骤将在下面将详细介绍。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>处理项选择

若要处理的选择和的项在集合中，取消选择`NSCollectionViewDelegate`都会要求提供。 由于此示例将使用内置在`NSCollectionViewFlowLayout`布局类型`NSCollectionViewDelegateFlowLayout`都会要求提供此委托的特定版本。

将新类添加到项目中，调用它`CollectionViewDelegate`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

`ItemsSelected`和`ItemsDeselected`方法重写，并将其用于设置或清除`PersonSelected`的视图控制器的用户选择或取消选择某个项时正在处理集合视图的属性。 这将显示在下面的详细信息。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在接口生成器中创建集合视图

所有所需的支持部分都准备就绪，可以编辑主情节提要和集合视图添加到它。

请执行以下操作：

1. 双击`Main.Storyboard`文件中**解决方案资源管理器**打开以进行编辑在 Xcode 中的接口生成器。
2. 将集合视图拖动到 Main 视图并调整其大小以填充视图：

    ![将集合视图添加到布局](collection-view-images/collection01.png)
3. 与所选的集合视图，使用约束编辑器将其固定到视图中，它是调整大小时：

    ![将约束添加](collection-view-images/collection02.png)
4. 确保集合视图是否已选中在**设计图面**(而不**界定滚动视图**或**剪辑视图**包含它)，切换到**助手编辑器中**并创建**Outlet**集合视图：

    ![将约束添加](collection-view-images/collection03.png)
5. 保存所做的更改并返回到 Visual Studio 来同步。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>使其所有共同

所有支持的部分现在已放入到位的一个类，使其作为数据模型 (`PersonModel`)、 一个`NSCollectionViewDataSource`已添加，用于提供数据，`NSCollectionViewDelegateFlowLayout`旨在处理项选择和`NSCollectionView`已添加到 Main Storyboard和公开为 Outlet (`EmployeeCollection`)。

最后一步是编辑包含集合视图的视图控制器并将所有合在一起，在集合中填入并处理项选择。

编辑`ViewController.cs`文件并使其如下所示：

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

中的详细信息，此代码在讲到`Datasource`属性定义为托管实例的`CollectionViewDataSource`，将为集合视图提供数据。 A`PersonSelected`属性定义以容纳`PersonModel`表示集合视图中的当前选定的项。 此属性也会引发`SelectionChanged`事件所选内容更改时。

`ConfigureCollectionView`类用于注册就像单元格原型使用以下行的集合视图的视图控制器：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

请注意，**标识符**(`EmployeeCell`) 用于注册一个调用中的原型匹配`GetItem`方法`CollectionViewDataSource`上面定义：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，视图控制器的类型**必须**的名称匹配`.xib`定义原型文件**完全**。 在此示例中，`EmployeeItemController`和`EmployeeItemController.xib`。

集合视图中的项的实际布局由集合视图布局类控制，可以将动态更改在运行时通过将分配到的新实例`CollectionViewLayout`属性。 更改此属性更新的集合视图的外观，不会对更改进行动画处理。

Apple 附带两个内置的布局类型与集合视图将处理最常见用法：`NSCollectionViewFlowLayout`和`NSCollectionViewGridLayout`。 如果开发人员所需的自定义格式，如布局圆圈，圆圈中签出, 项则可以创建的自定义实例`NSCollectionViewLayout`并重写所需的方法，以实现所需的效果。

此示例使用默认流布局，因此它创建的实例`NSCollectionViewFlowLayout`类并将其配置，如下所示：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize`属性集合中定义的各个单元格的大小。 `SectionInset`属性定义从单元格将放置在中集合的边缘的嵌入。 `MinimumInteritemSpacing` 定义项之间的最小间距和`MinimumLineSpacing`定义集合中的线之间的最小间距。

布局被分配给集合视图和实例`CollectionViewDelegate`附加到处理项选择：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData`方法创建的新实例`CollectionViewDataSource`、 使用数据填充它、 将其附加到的集合视图和调用`ReloadData`方法以显示各项：

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

`ViewDidLoad`方法被重写，且调用`ConfigureCollectionView`和`PopulateWithData`方法来向用户显示最终的集合视图：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>摘要

本文已了解在 Xamarin.Mac 应用程序中使用集合视图的详细的信息。 首先，它看起来在通过使用键 / 值编码 (KVC) 和键 / 值观察 (KVO) 公开到 Objective C 的 C# 类。 接下来，它说明了如何使用 KVO 符合类和数据将其绑定到在 Xcode 的接口生成器中的集合视图。 最后，它还介绍了如何在 C# 代码中与集合视图进行交互。

## <a name="related-links"></a>相关链接

- [MacCollectionNew （示例）](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 用户界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
