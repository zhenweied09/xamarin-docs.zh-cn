---
title: "使用表视图"
description: "本文介绍如何设计和表视图和表视图控制器使用内 Xamarin.tvOS 应用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: af562ac03f2cd5f293f99c7509000499ad5deaa4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-table-views"></a>使用表视图

_本文介绍如何设计和表视图和表视图控制器使用内 Xamarin.tvOS 应用。_

TvOS，在表视图呈现为滚动 （可选） 可以组织到组或部分的行的单个列中。 当您需要向用户，以明文了解方式高效地显示大量的数据时，应使用表视图。

表视图中的一侧通常显示[拆分视图](~/ios/tvos/user-interface/split-views.md)作为导航窗格中，使用中的另一侧显示的选定项的详细信息：

[![](table-views-images/intro01.png "示例表视图")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>有关表视图

A`UITableView`作为 （可选） 可以组织到组或部分的信息的分层列表中显示单个列的可滚动的行： 

[![](table-views-images/table01.png "选定的项")](table-views-images/table01.png#lightbox)

Apple 具有使用表的以下建议：

- **要注意的宽度**-尝试表宽度实现正确的平衡。 如果太宽表，它可能很难从远处扫描，并可以从可用的内容区域。 如果表是太窄，它可能会导致信息被截断或换行，再次将非常困难，用户可以在同一房间从读取。
- **显示快速表内容**-有关的数据的大型列表，延迟加载内容并开始向用户显示表时，就会立即显示信息。 如果在该表中考虑到长，无法加载，用户可能会丢失你的应用或认为它锁定感兴趣。
- **通知用户的长内容负载**-如果长表加载时间是不可避免的存在[进度栏或活动指示器](~/ios/tvos/user-interface/progress-indicators.md)以便它们知道应用程序未锁定。

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>表视图单元格类型

A`UITableViewCell`用于表示单个行的表视图中的数据。 Apple 已定义的多个默认表单元格类型：

- **默认**-此类型显示图像的单元格中，然后在右侧左对齐标题左侧的一个选项。 
- **副标题**-此类型提供的第一行和较小的左对齐标题左对齐副标题在下一行。
- **值 1** -此类型显示具有较轻彩色、 右对齐副标题左对齐标题上同一行。
- **值 2** -此类型显示具有较轻彩色、 左对齐副标题右对齐标题上同一行。

所有默认表视图单元格类型还支持图形元素，如泄露指示器或复选标记。 

此外，你可以定义**自定义**表视图单元格类型，并且存在_原型单元格_，你可以创建接口设计器中或通过代码。

Apple 具有使用表视图单元格的以下建议：

- **避免文本剪辑**-保留独立的短的文本行，以便最终它们不被截断。 截断的词或短语很难让用户在同一房间从分析。
- **请考虑已设定焦点的行状态**-因为某一行变得更大，了更多舍入具有焦点的角，你需要进行测试所有状态中的单元格的外观。 图像或文本可能会变得剪切或处于已设定焦点状态查找不正确。
- **应谨慎可编辑表使用**-移动或删除表行是花费更多时间上 tvOS 于 iOS。 你需要仔细决定，是否此功能将添加或偏离 tvOS 应用程序。
- **创建自定义单元格类型适用**-而内置的表视图单元格类型则非常适合于许多情况下，请考虑创建自定义单元格类型的非标准的信息以更好地控制和更好地呈现到的信息用户。

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>使用表视图

Xamarin.tvOS 应用中使用的表视图的最简单方法是创建和修改其外观接口设计器中。

若要开始，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在适用于 Mac 的 Visual Studio，启动一个新的 tvOS 应用程序项目，然后选择**tvOS** > **应用** > **单视图应用**单击**下一步**按钮： 

    [![](table-views-images/table02.png "选择单一视图应用")](table-views-images/table02.png#lightbox)
1. 输入**名称**为应用程序并单击**下一步**: 

    [![](table-views-images/table03.png "输入应用程序的名称")](table-views-images/table03.png#lightbox)
1. 可以调整**项目名称**和**解决方案名称**或者接受默认设置，然后单击**创建**按钮以创建新的解决方案： 

    [![](table-views-images/table04.png "项目名称和解决方案名称")](table-views-images/table04.png#lightbox)
1. 在**解决方案 Pad**，双击`Main.storyboard`文件以在 iOS 设计器中打开它： 

    [![](table-views-images/table05.png "Main.storyboard 文件")](table-views-images/table05.png#lightbox)
1. 选择并删除**默认视图控制器**: 

    [![](table-views-images/table06.png "选择并删除默认视图控制器")](table-views-images/table06.png#lightbox)
1. 选择**拆分视图控制器**从**工具箱**和将其拖到设计图面。
1. 默认情况下，你将获得[拆分视图](~/ios/tvos/user-interface/split-views.md)与**导航视图控制器**和**表视图控制器**中的左侧和**视图控制器**右下方。 这是 Apple 的建议的用法中 tvOS 的表视图： 

    [![](table-views-images/table08.png "添加拆分视图")](table-views-images/table08.png#lightbox)
1. 你将需要选择表视图的每个部分并将其分配一个自定义**类名**中**小组件**选项卡**属性资源管理器**，以便你可以在 C# 中，更高版本访问它代码。 例如，**表视图控制器**: 

    [![](table-views-images/table09.png "指定一个类名称")](table-views-images/table09.png#lightbox)
1. 确保你创建的自定义类**表视图控制器**、**表视图**和任何**原型单元格**。 适用于 Mac 的 visual Studio 将添加到项目树的自定义类，因为它们创建： 

    [![](table-views-images/table10.png "项目树中的自定义类")](table-views-images/table10.png#lightbox)
1. 接下来，在设计图面中选择表视图，然后根据需要调整它的属性。 如数**原型单元格**和**样式**（纯或分组）： 

    [![](table-views-images/table11.png "小组件选项卡")](table-views-images/table11.png#lightbox)
1. 每个**原型单元格**，选择它并分配一个唯一**标识符**中**小组件**选项卡**属性资源管理器**。 此步骤是_非常重要_因为你稍后将需要此标识符时你填充表。 例如`AttrCell`: 

    [![](table-views-images/table12.png "小组件选项卡")](table-views-images/table12.png#lightbox)
1. 你还可以选择提供作为一个单元格[默认表视图单元格类型](#Table-View-Cell-Types)通过**样式**下拉列表中或将其设置为**自定义**并使用设计图面的布局单元格通过将从其他 UI 小组件中拖动**工具箱**: 

    [![](table-views-images/table13.png "单元格的布局")](table-views-images/table13.png#lightbox)
1. 为分配唯一**名称**中的所有 UI 元素中的原型单元格设计**小组件**选项卡**属性资源管理器**以便你可以访问它们更高版本中的 C# 代码： 

    [![](table-views-images/table14.png "指定一个名称")](table-views-images/table14.png#lightbox)
1. 原型单元格的表视图中的所有重复上述步骤。
1. 接下来，将自定义类分配给 UI 设计、 布局的详细信息视图和分配唯一的其余部分**名称**查看详细信息中每个 UI 元素，以便你可以访问它们在 C# 中以及。 例如： 

    [![](table-views-images/table15.png "UI 布局")](table-views-images/table15.png#lightbox)
1. 将所做的更改保存到情节提要。
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在 Visual Studio 中，启动一个新的 tvOS 应用程序项目并选择**tvOS** > **单视图应用**并输入你的应用程序的名称。 单击**好**按钮以创建新的解决方案： 

    [![](table-views-images/table02-vs.png "选择单一视图应用")](table-views-images/table02-vs.png#lightbox)
1. 在**解决方案资源管理器**，双击`Main.storyboard`文件以在 iOS 设计器中打开它： 

    [![](table-views-images/table05-vs.png "Main.storyboard 文件")](table-views-images/table05-vs.png#lightbox)
1. 选择并删除**默认视图控制器**: 

    [![](table-views-images/table06-vs.png "选择并删除默认视图控制器")](table-views-images/table06-vs.png#lightbox)
1. 选择**拆分视图控制器**从**工具箱**和将其拖到设计图面： 

    [![](table-views-images/table07-vs.png "拆分视图控制器")](table-views-images/table07-vs.png#lightbox)
1. 默认情况下，你将获得[拆分视图](~/ios/tvos/user-interface/split-views.md)与**导航视图控制器**和**表视图控制器**中的左侧和**视图控制器**右下方。 这是 Apple 的建议的用法中 tvOS 的表视图： 

    [![](table-views-images/table08-vs.png "布局 UI")](table-views-images/table08-vs.png#lightbox)
1. 你将需要选择表视图的每个部分并将其分配一个自定义**类名**中**小组件**选项卡**属性资源管理器**，以便你可以在 C# 中，更高版本访问它代码。 例如，**表视图控制器**: 

    [![](table-views-images/table09-vs.png "小组件选项卡")](table-views-images/table09-vs.png#lightbox)
1. 确保你创建的自定义类**表视图控制器**、**表视图**和任何**原型单元格**。 适用于 Mac 的 visual Studio 将添加到项目树的自定义类，因为它们创建： 

    [![](table-views-images/table10-vs.png "项目树中的自定义类")](table-views-images/table10-vs.png#lightbox)
1. 接下来，在设计图面中选择表视图，然后根据需要调整它的属性。 如数**原型单元格**和**样式**（纯或分组）： 

    [![](table-views-images/table11-vs.png "小组件选项卡")](table-views-images/table11-vs.png#lightbox)
1. 每个**原型单元格**，选择它并分配一个唯一**标识符**中**小组件**选项卡**属性资源管理器**。 此步骤是_非常重要_因为你稍后将需要此标识符时你填充表。 例如`AttrCell`: 

    [![](table-views-images/table12-vs.png "分配一个标识符")](table-views-images/table12-vs.png#lightbox)
1. 你还可以选择提供作为一个单元格[默认表视图单元格类型](#Table-View-Cell-Types)通过**样式**下拉列表中或将其设置为**自定义**并使用设计图面的布局单元格通过将从其他 UI 小组件中拖动**工具箱**: 

    [![](table-views-images/table13-vs.png "样式下拉列表")](table-views-images/table13-vs.png#lightbox)
1. 为分配唯一**名称**中的所有 UI 元素中的原型单元格设计**小组件**选项卡**属性资源管理器**以便你可以访问它们更高版本中的 C# 代码： 

    [![](table-views-images/table14-vs.png "小组件选项卡")](table-views-images/table14-vs.png#lightbox)
1. 原型单元格的表视图中的所有重复上述步骤。
1. 接下来，将自定义类分配给 UI 设计、 布局的详细信息视图和分配唯一的其余部分**名称**查看详细信息中每个 UI 元素，以便你可以访问它们在 C# 中以及。 例如： 

    [![](table-views-images/table15.png "UI 布局")](table-views-images/table15.png#lightbox)
1. 将所做的更改保存到情节提要。
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>设计数据模型

使用表视图将变得更容易显示的信息以及要简化了将提供的详细信息 （如用户选择，或将突出显示的表视图中的行），创建一个自定义类或类，使其作为数据模型的信息显示.

以列出中的一个旅行预订应用程序为例**城市**，每个包含的唯一列表**焦点**，用户可以选择。 用户将能够将标记为引力*收藏*，选择此选项可获取*方向*到引力和*预订航班*给定所在的城市。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要创建数据模型，用于**引力**，右键单击项目名称中**解决方案 Pad**和选择**添加** > **新文件...**.输入`AttractionInformation`为**名称**单击**新建**按钮： 

[![](table-views-images/data01.png "为名称输入 AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要创建数据模型，用于**引力**，右键单击项目名称中**解决方案资源管理器**和选择**添加** > **新项...**.选择**类**并输入`AttractionInformation`为**名称**单击**添加**按钮： 

[![](table-views-images/data01-vs.png "选择的类并为名称输入 AttractionInformation")](table-views-images/data01-vs.png#lightbox)

-----

编辑`AttractionInformation.cs`文件并使其如下所示：

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

此类提供的属性来存储有关的信息给定**引力**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下来，右键单击项目名称中**解决方案 Pad**再次选择**添加** > **新文件...**.输入`CityInformation`为**名称**单击**新建**按钮： 

[![](table-views-images/data02.png "为名称输入 CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下来，右键单击项目名称中**解决方案资源管理器**再次选择**添加** > **新建项...**.输入`CityInformation`为**名称**单击**添加**按钮： 

[![](table-views-images/data02-vs.png "为名称输入 CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

编辑`CityInformation.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

此类可包含的所有信息有关目标**城市**的集合**焦点**对于该城市，并提供了两个帮助器方法 (`AddAttraction`) 以使其更轻松地添加到环境城市。

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>表视图数据源

每个表视图需要数据源 (`UITableViewDataSource`) 表中提供的数据并生成所需的行，为所需的表视图。

对于上述示例中，右键单击项目名称中**解决方案资源管理器**，选择**添加** > **新文件...**和调用它`AttractionTableDatasource`单击**新建**按钮以创建。 接下来，编辑`AttractionTableDatasource.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

让我们看看类的几个部分中详细信息。

首先，我们定义一个常量，以保存原型单元格 （这是在上面接口设计器中分配的同一标识符） 的唯一标识符、 快捷方式将重新添加到表视图控制器并为我们的数据创建存储：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下来，我们保存表视图控制器，然后生成并填充我们 （使用上面定义数据模型） 的数据源时创建的类：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

示例中，为了`PopulateCities`方法只需创建数据模型对象在内存中但无法从数据库或 web 服务中实际的应用程序轻松地被读取这些：

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

`NumberOfSections`方法返回表中的节的数目：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

有关**纯**设置样式表视图，始终返回 1。

`RowsInSection`方法在当前的部分中返回的行数：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

再次，为**纯**表视图，在数据源中返回的总项数。

`TitleForHeader`方法返回的标题，给定部分：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

有关**纯**表视图类型，请将保留为空标题 (`""`)。

最后，当请求的表视图，创建然后填充原型单元格使用`GetCell`方法： 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

有关详细信息使用`UITableViewDatasource`，请参阅 Apple 的[UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941)文档。

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>表视图委托

每个表视图需要委托 (`UITableViewDelegate`) 来响应用户交互或表上的其他系统事件。

对于上述示例中，右键单击项目名称中**解决方案资源管理器**，选择**添加** > **新文件...**和调用它`AttractionTableDelegate`单击**新建**按钮以创建。 接下来，编辑`AttractionTableDelegate.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

在详细信息，让我们看此类的多个部分组成。

首先，我们创建的快捷方式表视图控制器时创建的类：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然后，当选择行时 （用户单击的 Touch 面上 Apple 远程） 我们想要将标记**引力**由为收藏的所选行：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下来，用户 （通过给予它焦点使用 Apple 远程 Touch 面） 突出显示某行时我们希望将显示的详细信息**引力**所对应的行详细信息部分中我们拆分视图控制器：

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

`CanFocusRow`针对即将获得焦点的表视图中每一行中调用方法。 返回`true`如果行可以获得焦点，否则返回`false`。 在此示例中，我们已创建了自定义`AttractionHighlighted`收到焦点时将在每一行引发的事件。

有关详细信息使用`UITableViewDelegate`，请参阅 Apple 的[UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942)文档。

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>表视图单元格

对于每个原型单元格添加到接口设计器中的表视图，还会创建表视图单元格的自定义实例 (`UITableViewCell`) 以便您可以按其创建填充新的单元格 （行）。

用于示例应用中，双击`AttractionTableCell.cs`文件打开进行编辑，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

此类提供了存储引力数据模型对象 (`AttractionInformation`如上文所定义) 显示在给定行中：

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

`UpdateUI`方法填充 UI 小组件 （即已添加到接口设计器中的单元格的原型） 根据需要：

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

有关详细信息使用`UITableViewCell`，请参阅 Apple 的[UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938)文档。

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>表视图控制器

表视图控制器 (`UITableViewController`) 管理已添加到接口设计器通过情节提要表视图。

用于示例应用中，双击`AttractionTableViewController.cs`文件打开进行编辑，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

让我们仔细看一下此类。 首先，我们已创建快捷方式，以更加轻松地访问表视图`DataSource`和`TableDelegate`。 我们将使用这些更高版本中左侧的拆分视图的表视图和权限中的详细信息视图之间进行通信。

最后，当表视图加载到内存中，我们创建的实例`AttractionTableDatasource`和`AttractionTableDelegate`（不管是上述步骤中创建） 并将它们附加到表视图。

有关详细信息使用`UITableViewController`，请参阅 Apple 的[UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)文档。

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>将它放在所有一起

如本文档的开头所述，表视图中的一侧通常显示[拆分视图](~/ios/tvos/user-interface/split-views.md)作为导航窗格中，使用中的另一侧显示的选定项的详细信息。 例如: 

[![](table-views-images/intro01.png "运行示例应用")](table-views-images/intro01.png#lightbox)

由于这是中 tvOS 的标准模式，让我们看一下的最后几步将所有内容组合到一起，并进行相互交互左右两侧拆分视图。

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>详细信息视图

对于旅行应用程序的示例显示上面的自定义类 (`AttractionViewController`) 定义为在作为详细信息视图拆分视图的右侧提供标准视图控制器：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

在这里，我们提供了**引力**(`AttractionInformation`) 被显示为一个属性，然后创建`UpdateUI`填充 UI 小组件的方法添加到接口设计器中的视图。

我们还定义了一种快捷方式回拆分视图控制器 (`SplitView`)，我们将使用通信将变回表视图 (`AcctractionTableView`)。

最后，自定义操作 （事件） 已添加到三个`UIButton`接口设计器中创建的实例，允许用户将标记为引力_收藏_，获取_方向_到引力和_预订航班_给定所在的城市。

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>导航视图控制器

由于表视图控制器嵌套在左侧的拆分视图中的导航视图控制器，因此导航视图控制器已分配的自定义类 (`MasterNavigationController`) 中接口设计器和定义，如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

同样，此类仅定义几个快捷方式，以更加轻松地通过拆分视图控制器的两个方面进行通信：

* `SplitView` -为拆分视图控制器的链接 (`MainSpiltViewController`) 属于导航视图控制器。
* `TableController` -获取表视图控制器 (`AttractionTableViewController`)，会显示为导航的视图控制器中的顶部视图。

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>拆分视图控制器

拆分视图控制器是我们的应用程序的基类，因为我们创建了一个自定义类 (`MasterSplitViewController`) 接口设计器中为其和，如下所示定义：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

首先，我们创建的快捷方式**详细信息**侧的拆分视图 (`AttractionViewController`) 并对其**Master**端 (`MasterNavigationController`)。 同样，这样它更容易两条边更高版本之间进行通信。

接下来，拆分视图加载到内存中，则我们将拆分视图控制器附加到双方拆分视图并响应用户突出显示的表视图中引力 (`AttractionHighlighted`) 通过显示在新引力**详细信息**侧的拆分视图。

请参阅[tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/)示例应用程序内拆分视图的表视图的完整实现。

## <a name="table-views-in-detail"></a>在详细信息的表视图

因为 tvOS 建立从 iOS 中移出，表视图和表视图控制器设计，以类似的方式工作。 有关详细使用表视图中的 Xamarin 应用的信息，请参阅我们的 iOS[处理表和单元格](~/ios/user-interface/controls/tables/index.md)文档。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖设计和表视图在 Xamarin.tvOS 应用内部使用。 并提供了使用表视图内拆分视图，即 tvOS 应用中的表视图的典型用法的示例。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
