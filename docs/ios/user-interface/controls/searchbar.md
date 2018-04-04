---
title: 搜索栏
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 4ea39f6f935adba2c035884c625c57c0039b1165
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="search-bar"></a>搜索栏

UISearchBar 用于搜索的值列表。 

它包含三个主要组件： 

- 用于输入文本的字段。 用户可以利用此选项可输入其搜索词。
- 清除按钮，以从搜索字段中删除任何文本。
- 取消按钮中，若要退出搜索功能。

![搜索栏](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>实现搜索栏

若要实现方法是实例化一个新的搜索栏开始：

```csharp
searchBar = new UISearchBar();
```

然后将其放。 下面的示例演示如何将其放在导航栏或表的 HeaderView 中：

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

在搜索栏上设置属性：

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![搜索栏属性](searchbar-images/image6.png)

引发`SearchButtonClicked`事件时按下搜索按钮。 这将调用你的搜索逻辑：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

有关管理表示法的搜索栏和搜索结果的信息，请参阅[搜索控制器](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)配方。

## <a name="using-the-search-bar-in-the-designer"></a>使用设计器中的搜索栏

设计器提供了两个选项用于在设计器中实现一个搜索栏

- 搜索栏
- （已弃用） 的搜索显示控制器的搜索栏

![在设计器中的搜索栏控件](searchbar-images/image2.png)

使用属性面板来在搜索栏上设置属性

![搜索栏属性设计器](searchbar-images/image3.png)

下面解释了这些属性：

- **文本，占位符，提示**– 使用这些属性的建议和指导用户如何使用搜索栏。 例如，如果你的应用程序显示商店列表你无法使用提示属性建议，用户可以"输入市/县、 情景名称或邮政编码"
- **搜索样式**– 你可以设置搜索栏将**Prominent**或**最小**。 使用突出显示将色调淡在屏幕上，除条形图、 导致焦点要绘制到搜索栏搜索其他所有内容。 最小样式搜索栏将混合使用其周围。
- **功能**– 启用这些属性仅显示用户界面元素。 对于这些通过引发如所述正确的事件必须实现的功能[搜索栏 API 文档](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - 显示搜索结果 / 书签按钮-在搜索栏中显示搜索结果或书签图标
    - 显示取消按钮-允许用户退出搜索功能。 建议选择此。
    - 显示作用域栏 – 这样用户就可以限制其搜索的作用域。 例如，搜索音乐应用中时，用户可以选择是否他们想要搜索 Apple 音乐或其库中的寻找特定歌曲或艺术家。 若要显示各种选项，请将添加到标题数组**ScopeBarTitles**属性。
    ![搜索作用域标题栏](searchbar-images/image4.png)

- **文本行为**– 这些选项用于进行寻址时正在键入的用户输入的格式设置如何。 大小写将设置的每个单词或句子，启动或为大写形式的每个字符。 更正和拼写检查功能提示用户提供的单词的拼写建议，在键入时。
- **键盘**– 控件的键盘样式显示对于输入，并因此何种密钥都是键盘上可用。 这包括数 Pad、 Phone Pad、 电子邮件、 URL 以及其他选项。
- **外观**– 控制键盘的外观样式并将任一深色或浅色主题。
- **返回密钥**– 更改要更好地反映不会执行哪些操作的返回密钥上的标签。 支持的值包括转到、 联接、 下一步、 路由，完成后，和搜索。
- **安全**– 标识是否屏蔽输入 （如密码输入）。

## <a name="related-links"></a>相关链接

- [搜索控制器](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
