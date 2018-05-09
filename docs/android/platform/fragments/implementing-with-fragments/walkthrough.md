---
title: Xamarin.Android 片段演练的第 1 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 4dae59d113671c9ba1ac35dd8e4189d05a7c319a
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="fragments-walkthrough-ndash-phone"></a>片段演练&ndash;phone]

这是演练的将创建面向纵向的 Android 设备的 Xamarin.Android 应用的第一部分。 本演练将讨论如何在 Xamarin.Android 中创建片段以及如何将它们添加到示例。

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

将此应用程序创建的以下类：

1. `PlayQuoteFragment` &nbsp; 此代码段将显示 William 莎士比亚从 play 引号。 它将通过托管`PlayQuoteActivity`。
1. `Shakespeare` &nbsp; 此类将作为属性保留两个硬编码的数组。
1. `TitlesFragment` &nbsp; 此代码段将显示标题编写的 William 莎士比亚的播放的列表。 它将通过托管`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 将启动`PlayQuoteActivity`以响应用户选择在 play `TitlesFragment`。

## <a name="1-create-the-android-project"></a>1.创建 Android 项目

创建一个名为的新的 Xamarin.Android 项目**FragmentSample**。
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![创建新的 Xamarin.Android 项目](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![创建新的 Xamarin.Android 项目](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建议选择**现代开发**对于本演练。

创建项目之后，重命名文件**layout/Main.axml**到**layout/activity_main.axml**。

-----

## <a name="2-add-the-data"></a>2.添加数据

此应用程序的数据将存储在两个硬编码的字符串数组的属性的类名称`Shakespeare`:

* `Shakespeare.Titles` &nbsp; 此数组将容纳从 William 莎士比亚起到的列表。 这是数据源`TitlesFragment`。
* `Shakespeare.Dialogue` &nbsp; 此数组将保存的列表中包含在播放的一个引号`Shakespeare.Titles`。 这是数据源`PlayQuoteFragment`。

添加一个新 C# 类到**FragmentSample**项目并将其命名**Shakespeare.cs**。 在此文件中，创建一个新 C# 类调用`Shakespeare`具有以下内容

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!" 
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3.创建 PlayQuoteFragment

`PlayQuoteFragment`是将显示上前面在应用程序，用户已选择莎士比亚 play 的引号的 Android 片段此片段将不会使用 Android 布局文件; 相反，它将动态创建其用户界面。 添加新`Fragment`类名为`PlayQuoteFragment`到项目：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加一个新的 C# 类](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加一个新的 C# 类](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然后，更改的代码类似于此代码段中的片段：

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

它是一种常见模式在 Android 应用提供将实例化一个片段的工厂方法。 这可确保片段，将创建与正常的必需参数。 在本演练中，应用程序都应使用`PlayQuoteFragment.NewInstance`方法来选择有引号每次创建新的片段。 `NewInstance`方法将采用单个参数&ndash;以显示所引用内容的索引。

`OnCreateView`时间来呈现在屏幕上的片段时，将由 Android 调用方法。 它将返回 Android`View`片段的对象。 此代码段不使用布局文件创建视图。 相反，它将以编程方式创建视图实例化**TextView**来保存引号，并将显示在该小组件**ScrollView**。

> [!NOTE]
> 片段子类必须具有公共默认构造函数没有参数。

## <a name="4-create-the-playquoteactivity"></a>4.创建 PlayQuoteActivity

片段必须承载在活动内，因此此应用程序要求将承载活动`PlayQuoteFragment`。 活动将其布局在运行时动态添加片段。 将新活动添加到应用程序并将其命名`PlayQuoteActivity`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![将 Android 活动添加到项目](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![将 Android 活动添加到项目](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

编辑中的代码`PlayQuoteActivity`:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

当`PlayQuoteActivity`是创建，它将实例化一个新`PlayQuoteFragment`和加载的上下文中其根视图中该片段`FragmentTransaction`。 请注意此活动不会加载其用户界面的 Android 布局文件。 相反，新`PlayQuoteFragment`添加到应用程序的根视图。 资源标识符`Android.Resource.Id.Content`用于无需知道其特定的标识符引用活动的根视图。

## <a name="5-create-titlesfragment"></a>5.创建 TitlesFragment

`TitlesFragment`将称为专用的片段的子类`ListFragment`封装用于显示逻辑`ListView`片段中。 A`ListFragment`公开`ListAdapter`属性 (由`ListView`以显示其内容) 和事件处理程序名为`OnListItemClick`允许响应单击对已通过显示的行中的片段`ListView`。 

若要开始，将新片段添加到项目并将其命名**TitlesFragment**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加 Android 项目的片段](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加 Android 项目的片段](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

编辑片段内的代码：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

创建活动时将调用 Android`OnActivityCreated`方法片段; 这就是列表适配器`ListView`创建。  `ShowQuoteFromPlay`方法将启动的实例`PlayQuoteActivity`以显示所选 play 的引号。

## <a name="display-titlesfragment-in-mainactivity"></a>在 MainActivity 中显示 TitlesFragment

最后一步是显示`TitlesFragment`内`MainActivity`。 活动不会动态加载片段。 改为该片段将静态加载的活动使用的布局文件中将其声明`fragment`元素。 通过设置标识要加载的片段`android:name`属性设为片段类 (includeing 类型的命名空间)。 例如，若要使用`TitlesFragment`，然后`android:name`将设置为`FragmentSample.TitlesFragment`。

编辑布局文件**activity_mail.axml**，将现有的 XML 替换为以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitleFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> `class`属性是一个有效替代`android:name`。 没有正式的指南在首选的形式，有许多示例将使用的基本代码的`class`互换使用`android:name`。

不没有所需的 MainActivity 任何代码更改。 在该类中的代码应非常类似于此代码段：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>运行应用

现在，代码已完成，若要查看在操作的设备上运行应用程序。

[![到手机上运行的应用程序的屏幕快照。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本演练的第 2 部分](./walkthrough-landscape.md)将 optimtize 设备在横向模式下运行此应用程序。