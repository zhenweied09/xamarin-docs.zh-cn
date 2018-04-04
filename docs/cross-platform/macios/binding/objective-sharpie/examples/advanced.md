---
title: 高级 （手动） 的真实世界示例
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 96a8f77124fcd2a011e499e1088650ff7664fa71
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-manual-real-world-example"></a>高级 （手动） 的真实世界示例


**此示例使用[POP 库从 Facebook](https://github.com/facebook/pop)。**


本部分介绍使用更高级的方法进行绑定，我们将在其中使用 Apple 的`xcodebuild`工具以首先生成 POP 项目，然后手动推导目标 Sharpie 的输入。 这实质上是包含目标 Sharpie 实质上一节中执行的操作。

```csharp
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

因为 POP 库具有 Xcode 项目 (`pop.xcodeproj`)，我们只需使用`xcodebuild`生成 POP。 此过程可能反过来又会生成目标 Sharpie 可能需要分析的标头文件。 这是生成之前绑定是重要的原因。 通过生成时`xcodebuild`传递相同的 SDK 标识符和体系结构，请确保你想要将传递给目标 Sharpie （然后请记住，目标 Sharpie 3.0 可以执行此通常操作 ！）：

```csharp
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

作为的一部分将有大量的控制台中的生成信息输出`xcodebuild`。 如上所示，我们可以看到"CpHeader"目标已运行其中标头文件被复制到生成输出目录。 这通常是这种情况，并轻松绑定： 作为本机库生成的一部分，标头文件通常在将复制到"公开"可耗用位置这可以使分析变得更加简单绑定。 在这种情况下，我们知道 POP 的标头文件位于`build/Headers`目录。

现在我们就可以将绑定 POP。 我们知道，我们想要生成适用于 SDK`iphoneos8.1`与`arm64`体系结构和我们很关心在标头文件放入`build/Headers`下 POP git 签出。 如果我们`build/Headers`目录中，我们将看到一个标头文件数：

```csharp
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

如果我们看一下`POP.h`，我们可以看到这是库的主顶级标头文件`#import`s 其他文件。 因此，我们只需传递`POP.h`到目标 Sharpie 和 clang 将执行在幕后的其余部分：

```csharp
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

你将注意到，我们传递`-scope build/Headers`目标 Sharpie 自变量。 因为 C 和 Objective C 库必须`#import`或`#include`是实现详细信息的库和不想要将绑定，请的 API 的其他标头文件`-scope`参数告诉目标 Sharpie，若要忽略中未定义任何 API文件中的某处`-scope`目录。

你将找到`-scope`自变量通常是可选的完全实现的库，但是没有显式提供任何影响。

此外，我们指定`-c -Ibuild/headers`。 首先，`-c`参数告诉目标 Sharpie 停止解释命令行自变量并将任何后续自变量传递_直接到 clang 编译器_。 因此，`-Ibuild/Headers`是指示要搜索的 clang clang 编译器参数将包含下`build/Headers`，即 POP 标头居住在哪里。 而不使用此参数，clang 将不知道在何处查找文件，`POP.h`是`#import`运算结果。 _使用目标 Sharpie 几乎所有"问题"归结为了解如何通过 clang_。

### <a name="completing-the-binding"></a>完成绑定

现在已生成目标 Sharpie`Binding/ApiDefinitions.cs`和`Binding/StructsAndEnums.cs`文件。

这些是目标 Sharpie 基本第一次绑定，并且在某些情况下它可能是你只需。 如上所述但是，开发人员通常需要手动修改生成的文件目标 Sharpie 完成到后[修复任何问题](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)，可能不会自动处理由该工具。

完成更新后，这两个文件现在可以添加适用于 Mac 到 Visual Studio 中的绑定项目，或直接传递`btouch`或`bmac`工具以生成最终的绑定。

有关绑定过程的全面说明，请参阅我们[完整操作实例说明](~/ios/platform/binding-objective-c/walkthrough.md)。

