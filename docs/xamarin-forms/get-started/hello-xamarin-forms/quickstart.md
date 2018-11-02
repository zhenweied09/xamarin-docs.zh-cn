---
title: Xamarin.Forms 快速入门
description: 本文介绍了如何创建一个应用程序，将字母数字电话号码（由用户输入）转换为数字电话号码，然后呼叫该号码。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/13/2018
ms.openlocfilehash: fd9b3032166470a960e97f1754d2133a5ef22631
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109362"
---
# <a name="xamarinforms-quickstart"></a>Xamarin.Forms 快速入门

本演练介绍如何创建一个应用程序，它将字母数字电话号码（由用户输入）转换为数字电话号码，然后呼叫该号码。 最终的应用程序如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 应用程序")](quickstart-images/intro-app-examples.png#lightbox "Phoneword Application")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Visual Studio 入门

1. 在“开始”屏幕中，启动 Visual Studio。 这会打开起始页：

    ![](quickstart-images/vs/start-page.png "Visual Studio")

2. 在 Visual Studio 中，单击“创建新项目...”以创建新项目：

    ![](quickstart-images/vs/new-solution.png "新建项目")

3. 在“新建项目”对话框中，单击“跨平台”，选择“移动应用(Xamarin.Forms)”模板，将“名称”设为“Phoneword”，为项目选择合适的位置，然后单击“确定”按钮：

    ![](quickstart-images/vs/new-project.w157.png "跨平台项目模板")

    > [!NOTE]
    > 本快速入门中的 C# 和 XAML 片段要求将解决方案命名为“Phoneword”。
    > 将这些指令中的代码复制到项目中时，使用不同的解决方案名称将导致大量生成错误。

4. 在“新的跨平台应用”对话框中，单击“空白应用”，选择“.NET Standard”作为代码共享策略，然后单击“确定”按钮：

    ![](quickstart-images/vs/new-app.png "新的跨平台应用")

5. 在“解决方案资源管理器”的“Phoneword”项目中，双击 **MainPage.xaml** 将其打开：

    ![](quickstart-images/vs/open-mainpage-xaml.png "打开的 MainPage.xaml")

6. 在“MainPage.xaml”中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    按 **Ctrl+S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

7. 在“解决方案资源管理器”中，展开“MainPage.xaml”，然后双击 **MainPage.xaml.cs** 将其打开：

    ![](quickstart-images/vs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

8. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 如果分别在用户界面中单击“翻译”和“调用”按钮，作为响应，将分别执行 `OnTranslate` 和 `OnCall` 方法：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 尝试在此时构建应用程序将导致稍后要修复的错误。

    按 **Ctrl+S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

9. 在“解决方案资源管理器”中，右键单击“Phoneword”项目，然后选择“添加”>“新建项...”：

    ![](quickstart-images/vs/add-new-item.png "添加新项")

10. 在“添加新项”对话框中，选择“Visual C#”>“代码”>“类”，将新文件命名为 **PhoneTranslator**，然后单击“添加”按钮：

    ![](quickstart-images/vs/add-translator-class.w157.png "添加新类")

11. 在“PhoneTranslator.cs”中，删除所有模板代码并将其替换为以下代码。 此代码会将手机词翻译为电话号码：

    ```csharp
    using System.Text;

    namespace Phoneword
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    通过按 **Ctrl+S**，保存对 **PhoneTranslator.cs** 所做的更改，然后关闭文件。

12. 在“解决方案资源管理器”中，右键单击“Phoneword”项目，然后选择“添加”>“新建项...”：

    ![](quickstart-images/vs/add-new-item.png "添加新项")

13. 在“添加新项”对话框中，选择“Visual C#”>“代码”>“界面”，将新文件命名为 **IDialer**，然后单击“添加”按钮：

    ![](quickstart-images/vs/add-idialer-interface.w157.png "添加新接口")

14. 在“IDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将定义 `Dial` 方法，必须在每个平台上实现此方法，才可拨打翻译后的电话号码：

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```

    通过按 **Ctrl+S**，保存对 **IDialer.cs** 所做的更改，然后关闭文件。

    > [!NOTE]
    > 此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

15. 在“解决方案资源管理器”中，右键单击“Phoneword.iOS”项目，然后选择“添加”>“新建项...”：

    ![](quickstart-images/vs/add-new-item-ios.png "添加新项")

16. 在“添加新项”对话框中，选择“Apple”>“代码”>“类”，将新文件命名为 **PhoneDialer**，然后单击“添加”按钮：

    ![](quickstart-images/vs/new-phone-dialer-ios.w157.png "添加新类")

17. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 <code>Dial</code> 方法，此方法将在 iOS 平台上用于拨打翻译后的电话号码：

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    通过按 **Ctrl+S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

18. 在“解决方案资源管理器”中，右键单击“Phoneword.Android”项目，然后选择“添加”>“新建项...”：

    ![](quickstart-images/vs/add-new-item-android.png "添加新项")

19. 在“添加新项”对话框中，选择“Visual C#”>“Android”>“类”，将新文件命名为 **PhoneDialer**，然后单击“添加”按钮：

    ![](quickstart-images/vs/new-phone-dialer-android.w157.png "添加新类")

20. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 Android 平台上用于拨打翻译后的电话号码：

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionDial);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    请注意，若要更好地理解此代码，需要使用最新 Android API。 通过按 **Ctrl+S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

21. 在“解决方案资源管理器”的“Phoneword.Android”项目中，双击“MainActivity.cs”将其打开，然后删除所有模板代码并将其替换成下列代码：

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);
                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }
    ```

    按 Ctrl+S，保存对 MainActivity.cs 所做的更改，然后关闭文件。

22. 在“解决方案资源管理器”的“Phoneword.Android”项目中，双击“属性”，然后选择“Android 清单”选项卡：

    ![](quickstart-images/vs/android-manifest.png "打开的 Android 清单")

23. 在“所需的权限”部分中，启用“CALL_PHONE”权限。 这将向应用程序授予进行电话呼叫的权限：

    ![](quickstart-images/vs/android-manifest-changed.png "启用 CallPhone 权限")

    通过按 **Ctrl+S**，保存对清单所做的更改，然后关闭文件。

24. 右键单击 Android 应用程序项目并选择“设为启动项目”。

25. 使用“绿色箭头”工具栏按钮运行 Android 应用或在菜单中选择“调试”>“开始调试”。

    > [!WARNING]
    > 所有模拟器都不支持电话呼叫，因此该功能可能无效。

26. 如果拥有 iOS 设备并符合 Xamarin.Forms 开发的 Mac 系统要求，请使用类似技术将应用部署到 iOS 设备。 或者，将应用部署到 [iOS 远程模拟器](~/tools/ios-simulator/index.md)。

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Visual Studio for Mac 入门

1. 启动 Visual Studio for Mac，然后在起始页上单击“新建项目...”创建新项目：

    ![](quickstart-images/xs/new-solution.png "新建解决方案")

2. 在“为新项目选择一个模板”对话框中，单击“多平台”>“应用”，选择“空白窗体应用”模板，然后单击“下一步”按钮：

    ![](quickstart-images/xs/choose-template.png "选择模板")

3. 在“配置空白表单应用”对话框中，将新应用命名为“Phoneword”，确保选中“使用 .NET Standard”单选按钮，然后单击“下一步”按钮：

    ![](quickstart-images/xs/configure-app.png "配置 Forms 应用程序")

4. 在“配置新空白表单应用”对话框中，将“解决方案”和“项目”名称保留设置为“Phoneword”，为项目选择合适的位置，然后单击“创建”按钮创建项目：

    ![](quickstart-images/xs/configure-project.png "配置 Forms 项目")

    > [!NOTE]
    > 本快速入门中的 C# 和 XAML 片段要求将解决方案命名为“Phoneword”。
    > 将这些指令中的代码复制到项目中时，使用不同的解决方案名称将导致大量生成错误。

5. 在“Solution Pad”中，双击 **MainPage.xaml**将其打开：

    ![](quickstart-images/xs/open-mainpage-xaml.png "打开的 MainPage.xaml")

6. 在“MainPage.xaml”中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

7. 在“Solution Pad”中，双击 **MainPage.xaml.cs** 将其打开：

    ![](quickstart-images/xs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

8. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 如果分别在用户界面上单击“翻译”和“调用”按钮，作为响应，将分别执行 `OnTranslate` 和 `OnCall` 方法：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 尝试在此时构建应用程序将导致稍后要修复的错误。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

9. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

    ![](quickstart-images/xs/add-new-translator-file.png "添加新文件")

10. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneTranslator**，然后单击“新建”按钮：

    ![](quickstart-images/xs/add-translator-class.png "添加新类")

11. 在“PhoneTranslator.cs”中，删除所有模板代码并将其替换为以下代码。 此代码会将手机词翻译为电话号码：

    ```csharp
    using System.Text;

    namespace Phoneword
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneTranslator.cs** 所做的更改，然后关闭文件。

12. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

    ![](quickstart-images/xs/add-new-interface.png "添加新文件")

13. 在“新建文件”对话框中，选择“常规”>“空界面”，将新文件命名为 **IDialer**，然后单击“新建”按钮：

    ![](quickstart-images/xs/add-idialer-interface.png "添加新接口")

14. 在“IDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将定义 `Dial` 方法，必须在每个平台上实现此方法，才可拨打翻译后的电话号码：

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```
    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **IDialer.cs** 所做的更改，然后关闭文件。

    > [!NOTE]
    > 此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

15. 在“Solution Pad”中，选择“Phoneword.iOS”项目，右键单击并选择“添加”>“新建文件...”：

    ![](quickstart-images/xs/add-new-file-ios.png "添加新文件")

16. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneDialer**，然后单击“新建”按钮：

    ![](quickstart-images/xs/new-phonedialer-ios.png "添加新类")

17. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 iOS 平台上用于拨打翻译后的电话号码：

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

18. 在“Solution Pad”中，选择“Phoneword.Droid”项目，右键单击并选择“添加”>“新建文件...”：

    ![](quickstart-images/xs/add-new-file-android.png "添加新文件")

19. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneDialer**，然后单击“新建”按钮：

    ![](quickstart-images/xs/new-phonedialer-android.png "添加新类")

20. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 Android 平台上用于拨打翻译后的电话号码：

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionDial);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    请注意，若要更好地理解此代码，需要使用最新 Android API。 通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

21. 在“Solution Pad”的“Phoneword.Droid”项目中，双击“MainActivity.cs”将其打开，然后删除所有模板代码并将其替换成下列代码：

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);
                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }
    ```

    选择“文件”>“保存”（或按 &#8984;+S），保存对 MainActivity.cs 所做的更改，然后关闭文件。

22. 在“Solution Pad”中，展开“属性”文件夹，然后右键单击“AndroidManifest.xml”文件：

    ![](quickstart-images/xs/android-manifest.png "打开的 Android 清单")

23. 在“所需的权限”部分中，启用“CallPhone”权限。 这将向应用程序授予进行电话呼叫的权限：

    ![](quickstart-images/xs/android-manifest-changed.png "启用 CallPhone 权限")

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **AndroidManifest.xml** 所做的更改，然后关闭文件。

24. 在 Visual Studio for Mac 中，选择“生成”>“生成所有”菜单项，或按 **&#8984; + B**。 应用程序将生成，并在 Visual Studio for Mac 工具栏中显示一条成功消息。

    ![](quickstart-images/xs/build-successful.png "生成成功")

25. 如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成应用程序。
26. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 iOS 模拟器内的应用程序：

    ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具栏")
    ![](quickstart-images/xs/phoneword-result-ios.png "iOS 模拟器")

    注意：iOS 模拟器不支持电话呼叫。

27. 在**解决方案面板**中，选择“Phoneword.Droid”项目，右键单击并选择“设为启动项目”：

    ![](quickstart-images/xs/set-startup-project.png "设为启动项目")

28. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 Android 模拟器内的应用程序：

    ![](quickstart-images/xs/phoneword-result-android.png "Android 仿真器")

    > [!WARNING]
    > Android Emulator 不支持电话呼叫。

::: zone-end

祝贺你完成 Xamarin.Forms 应用程序。 本指南的[下一个主题](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md)将回顾此演练中采用的步骤，以深入了解使用 Xamarin.Forms 开发应用程序的基础知识。

## <a name="related-links"></a>相关链接

- [通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword（示例）](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
