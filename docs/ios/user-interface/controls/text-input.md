---
title: "文本输入"
ms.topic: article
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: ce1014616d0cf5f6cd5228d69976dfeca546b382
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="text-input"></a>文本输入

接受用户文本输入实现的`UITextField`单行输入和多行可编辑文本的 UITextView。 你可以将任一这些控件拖到屏幕，然后双击以设置的初始文本。

下面的屏幕截图显示位于 Visual Studio 中的工具箱板适用于 Mac 的这些控件的图标：

 [ ![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png)

 [ ![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png)

具有名为电源插座并保存情节提要文件，则适用于 Mac 的 Visual Studio 将更新后`.designer.cs`分部类和您可以添加引用到您的类文件控件的 C# 代码。 每个控件具有其自己唯一的属性和事件可以在 C# 代码中访问。

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

`UITextField`控件通常用来接受单个行的文本输入源，如用户名或密码。 某些可用于自定义控件的选项如下所示：

 [ ![](text-input-images/image15a.png "UITextField 属性")](text-input-images/image15a.png)

下面解释了这些控件：

-  **占位符**– 这是可选的。 如果设置，它会显示的文本字段为空，通常以向用户解释应有哪些输入时。
-  **清除按钮**– 此设置控制当标准清除按钮 （带有 (X) 灰色的圆圈） 出现在文本字段中，用户快速清除文本的方法。 它可以是永久隐藏、 永久可见，或所示，具体取决于正在编辑字段。
-  **最小字号**和**调整为适合页面**– 允许的字体大小自动调整以适应更长的文本，并防止截断，但限制为 no 小于指定大小。
-  **大小写**– 是否自动将大写单词、 句子或所有输入。
-  **更正**– 是否启用了拼写检查和建议。
-  **键盘**– 控件的键盘样式显示对于输入，并因此何种密钥都是键盘上可用。 这包括数 Pad、 Phone Pad、 电子邮件、 URL 以及其他选项。
-  **外观**– 控制键盘的外观样式并将任一深色或浅色主题。
-  **返回密钥**– 更改要更好地反映不会执行哪些操作的返回密钥上的标签。 支持的值包括转到、 联接、 下一步、 路由，完成后，和搜索。
-  **安全**– 标识是否屏蔽输入 （如密码输入）。


如果 UITextField 调用`textfield1`已添加到屏幕与设计器中，您可以设置或更改其属性在 C# 中，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS 提供枚举，以便可以方便地选择你想要的设置，如在适当的位置`UIKeyboardType`和`UIReturnKeyType`上面的代码段中。

### <a name="display-text-programmatically"></a>以编程方式显示文本

如果不想使用设计器设计屏幕，或者如果你想要在运行时动态添加一些文本，可以创建并显示以编程方式在 UITextField`ViewDidLoad`如下的视图控制器方法：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

`UITextView`控件可以用于显示只读的文本或接受多个行文本输入。 它具有许多与相同的选项`UITextField`（如大小写，更正，等等）。

 [ ![](text-input-images/image16a.png "UITextView 属性")](text-input-images/image16a.png)

特定属性包括：

-  **行为**– 是否的文本是可编辑还是只读的。
-  **检测**– 检测并将转换为可单击的元素所的数据如电话号码可触发调用中，地址成为链接到映射，在 Safari 中打开的 Url 或日期和时间的日历中成为事件。


如果 UITextView 已添加到与设计器的屏幕，你可以设置或更改其属性如下：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
