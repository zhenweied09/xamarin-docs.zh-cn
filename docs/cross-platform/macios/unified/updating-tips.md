---
title: 代码更新为统一的 API 的提示
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b23a84c990eb2418e94b414cc9750b3060c572ad
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>代码更新为统一的 API 的提示

使用 Visual Studio 中的自动化的迁移工具，为 Mac 会将转换 iOS 和 Mac 项目以引用统一 API （Xamarin.iOS.dll 或 Xamarin.Mac.dll），并为你进行了大量的所需的代码更改 (请参阅[Xamarin Studio 版本说明部分 'Classic 到统一 API 代码迁移工具'](http://developer.xamarin.com/releases/studio/xamarin.studio_5.7/xamarin.studio_5.7/)有关特定的详细信息)。

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>找不到 NSInvalidArgumentException 情节提要错误

没有[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)适用于 Mac 使用自动的迁移工具将你的项目转换为统一 Api 后可能会发生的 Visual Studio 的当前版本中。 更新后，如果在窗体中收到一条错误消息：

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...

```

你可以执行以下操作来解决此问题，找到以下的生成目标文件：

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

在此文件中，你需要查找以下目标声明：

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

并添加`DependsOnTargets="_CollectBundleResources"`属性设为它。 如：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

保存该文件、 适用于 Mac 的重新启动 Visual Studio 以及执行干净和重新生成你的项目。 针对此问题的修复程序应通过 Xamarin 很快就会发布。

## <a name="useful-tips"></a>有用提示

在使用迁移工具之后, 可能仍会需手动干预的某些编译器错误。
可能需要手动修复某些事项包括：

* 比较`enum`s 可能需要`(int)`强制转换。

* `NSDictionary.IntValue` 现在返回`nint`，没有`Int32Value`可以使用代替。

* `nfloat` 和`nint`类型不能标记为`const`;  `static readonly nint`是一个合理的替代方法。

* 用于直接在操作`MonoTouch.`命名空间现通常位于`ObjCRuntime.`命名空间 (例如：`MonoTouch.Constants.Version`现`ObjCRuntime.Constants.Version`)。

* 尝试序列化时，将对象序列化的代码可能中断`nint`和`nfloat`类型。 请务必检查你的序列化代码按预期在迁移后运行。

* 有时自动的工具未命中内的代码`#if #else`条件编译器指令。 在这种情况下，你将需要手动进行修补程序 （请参阅下面的常见错误）。

* 使用手动导出的方法`[Export]`可能不会自动修复迁移工具，例如在你必须手动更新返回类型为此代码 snippert `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * 统一的 API 不提供 NSDate 和.NET 日期时间之间的隐式转换，因为它不是无损转换。 若要防止与相关的错误`DateTimeKind.Unspecified`转换.NET`DateTime`向本地或之前强制转换为 UTC `NSDate`。

 * Objective C 类别方法现在将生成为统一 API 中的扩展方法。 例如，代码之前使用`UIView.DrawString`现在将引用`NSString.DrawString`统一 API 中。

 * 代码使用具有 AVFoundation 类`VideoSettings`应改为使用`WeakVideoSettings`属性。 这要求`Dictionary`，这是可用作属性上设置类中，例如：

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * NSObject`.ctor(IntPtr)`构造函数已从公开给受保护 ([以防止不正确使用](~/cross-platform/macios/unified/index.md#NSObject_ctor))。

 * `NSAction` 已[替换](~/cross-platform/macios/unified/index.md#NSAction)与 starndard.NET `Action`。 一些简单 （单个参数） 委托还已替换`Action<T>`。

最后，请参阅[经典 v 统一的 API 差异](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)查找在代码中对 Api 的更改。 搜索[本页](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)将帮助查找经典 Api 并且什么它们已更新为。

**注意：** `MonoTouch.Dialog`命名空间迁移后将保持相同。 如果你的代码使用**MonoTouch.Dialog**应继续使用该命名空间-执行*不*更改`MonoTouch.Dialog`到`Dialog`！

## <a name="common-compiler-errors"></a>常见编译器错误

与解决方案一起，下面列出了常见错误的其他示例：

**错误 CS0012： 未被引用程序集中定义的类型 MonoTouch.UIKit.UIView。**

修复： 这通常意味着该项目引用的组件或还未生成统一 API 使用的 NuGet 包。 你应删除并重新添加所有组件和 NuGet 包。 如果此操作未修复错误，外部库可能尚不支持统一 API。

**错误 MT0034： 不能包含 monotouch.dll 和 Xamarin.iOS.dll 中相同的 Xamarin.iOS 项目-Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 Xamarin.Mobile，Version = 0.6.3.0，区域性 = neutral，PublicKeyToken = null。**

修复： 删除导致此错误的组件，并重新添加到项目。

**错误 CS0234： 类型或命名空间名称基础中的命名空间 MonoTouch 不存在。是否缺少程序集引用？**

修复： 适用于 Mac 的 Visual Studio 中的自动化的迁移工具*应*更新所有`MonoTouch.Foundation`引用`Foundation`，但在某些情况下，这些将需要手动更新。 对于以前中包含的其他命名空间可能会出现类似的错误`MonoTouch`，如`UIKit`。

**错误 CS0266： 无法将类型 double 到 System.float 隐式转换**

修复： 更改类型，并强制转换为`nfloat`。 对于 64 位等效项与其他类型，也可能出现此错误 (如`nint`，)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**错误 CS0266： 无法将类型 CoreGraphics.CGRect 到 System.Drawing.RectangleF 隐式转换。显式转换存在 （是否缺少强制转换？）**

修复： 更改实例`RectangleF`到`CGRect`，`SizeF`到`CGSize`，和`PointF`到`CGPoint`。 命名空间`using System.Drawing;`应替换为`using CoreGraphics;`（如果尚未存在）。

**错误 CS1502： 最佳重载的方法匹配 CoreGraphics.CGContext.SetLineDash (System.nfloat、 System.nfloat[]) 有一些无效自变量**

修复： 数组将类型更改为`nfloat[]`和显式强制转换`Math.PI`。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**错误 CS0115: WordsTableSource.RowsInSection （UIKit.UITableView，int）' 标记为重写，但找重写任何适合的方法**

修复： 更改到返回的值和参数类型`nint`。 这通常发生在方法重写，例如那些`UITableViewSource`，包括`RowsInSection`， `NumberOfSections`， `GetHeightForRow`， `TitleForHeader`， `GetViewForHeader`，等等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**错误 CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)**

修复： 当返回的类型更改为`nint`，将返回值强制转换`nint`。

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**错误 CS1061： 类型 'CoreGraphics.CGPath 不包含的定义，为 AddElipseInRect**

修复： 更正拼写到`AddEllipseInRect`。 其他名称更改包括：

* 将 Color.Black 更改为`NSColor.Black`。
* 将 MapKit AddAnnotation 更改为`AddAnnotations`。
* 将 AVFoundation DataUsingEncoding 更改为`Encode`。
* 将 AVFoundation AVMetadataObject.TypeQRCode 更改为`AVMetadataObjectType.QRCode`。
* 将 AVFoundation VideoSettings 更改为`WeakVideoSettings`。
* 更改到 PopViewControllerAnimated `PopViewController`。
* 将 CoreGraphics CGBitmapContext.SetRGBFillColor 更改为`SetFillColor`。

**错误 CS0546： 无法重写，因为 MapKit.MKAnnotation.Coordinate 不具有可重写的 set 访问器 (CS0546)**

在创建时的自定义批注通过子类化 MKAnnotation 坐标字段都有没有 setter，仅 getter。

[修复](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* 添加要跟踪的坐标的字段
* 返回此字段中的坐标属性 getter
* 覆盖 SetCoordinate 方法，并设置您的字段
* 在与传入的坐标参数你 ctor 调用 SetCoordinate

其外观应类似如下：

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>相关链接

- [更新应用](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
