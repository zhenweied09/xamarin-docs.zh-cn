---
title: "Xamarin.Forms 快速入门"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: 434a12c26a5823b082751c95c2090b22c39cf081
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinforms-quickstart"></a>Xamarin.Forms 快速入门

本演练介绍如何创建一个应用程序，它将字母数字电话号码（由用户输入）转换为数字电话号码，然后呼叫该号码。 最终的应用程序如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 应用程序")](quickstart-images/intro-app-examples.png#lightbox "Phoneword Application")

创建 Phoneword 应用程序，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“开始”屏幕中，启动 Visual Studio。 这会打开起始页：

  ![](quickstart-images/vs/start-page.png "Visual Studio")

1. 在 Visual Studio 中，单击“创建新项目...”以创建新项目：

  ![](quickstart-images/vs/new-solution.png "新建项目")

1. 在“新建项目”对话框中，单击“跨平台”，选择“跨平台应用(Xamarin.Forms)”模板，将“名称”和“解决方案”名称设为 `Phoneword`，为项目选择合适的位置，然后单击“确定”按钮：

  ![](quickstart-images/vs/new-project.png "跨平台项目模板")

1. 在“新的跨平台应用”对话框中，单击“空白应用”，选择“Xamarin.Forms”作为 UI 技术，选择“.NET Standard”作为代码共享策略，然后单击“确定”按钮：

  ![](quickstart-images/vs/new-app.png "新的跨平台应用")

1. 在“解决方案资源管理器”的“Phoneword”项目中，双击 **MainPage.xaml** 将其打开：

  ![](quickstart-images/vs/open-mainpage-xaml.png "打开的 MainPage.xaml")

1. 在“MainPage.xaml”中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.MainPage">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <Label Text="Enter a Phoneword:" />
              <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
              <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
              <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
            </StackLayout>
        </ContentPage>

  按 **Ctrl+S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，展开“MainPage.xaml”，然后双击 **MainPage.xaml.cs** 将其打开：

  ![](quickstart-images/vs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

1. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 如果分别在用户界面中单击“翻译”和“调用”按钮，作为响应，将分别执行 `OnTranslate` 和 `OnCall` 方法：

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
                    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
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

  > [!NOTE]
  > 尝试在此时构建应用程序将导致稍后要修复的错误。

  按 **Ctrl+S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，展开“App.xaml”，然后双击 **App.xaml.cs** 将其打开：

  ![](quickstart-images/vs/open-app-class.png "打开的 App.xaml.cs")

1. 在“App.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 `App` 构造函数会将 `MainPage` 类设置为页面，该页面将在应用程序启动时显示：

        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public App()
                {
                    InitializeComponent();
                    MainPage = new MainPage();
                }

                protected override void OnStart()
                {
                    // Handle when your app starts
                }

                protected override void OnSleep()
                {
                    // Handle when your app sleeps
                }

                protected override void OnResume()
                {
                    // Handle when your app resumes
                }
            }
        }

  通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，右键单击“Phoneword”项目，然后选择“添加”>“新建项...”：

  ![](quickstart-images/vs/add-new-item.png "添加新项")

1. 在“添加新项”对话框中，选择“Visual C#”>“代码”>“类”，将新文件命名为 **PhoneTranslator**，然后单击“添加”按钮：

  ![](quickstart-images/vs/add-translator-class.png "添加新类")

1. 在“PhoneTranslator.cs”中，删除所有模板代码并将其替换为以下代码。 此代码会将手机词翻译为电话号码：

        using System.Text;

        namespace Core
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

  通过按 **Ctrl+S**，保存对 **PhoneTranslator.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，右键单击“Phoneword”项目，然后选择“添加”>“新建项...”：

  ![](quickstart-images/vs/add-new-item.png "添加新项")

1. 在“添加新项”对话框中，选择“Visual C#”>“代码”>“界面”，将新文件命名为 **IDialer**，然后单击“添加”按钮：

  ![](quickstart-images/vs/add-idialer-interface.png "添加新接口")

1. 在“IDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将定义 `Dial` 方法，必须在每个平台上实现此方法，才可拨打翻译后的电话号码：

        namespace Phoneword
        {
            public interface IDialer
            {
                bool Dial(string number);
            }
        }

  通过按 **Ctrl+S**，保存对 **IDialer.cs** 所做的更改，然后关闭文件。

  > [!NOTE]
> 此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

1. 在“解决方案资源管理器”中，右键单击“Phoneword.iOS”项目，然后选择“添加”>“新建项...”：

  ![](quickstart-images/vs/add-new-item-ios.png "添加新项")

1. 在“添加新项”对话框中，选择“Apple”>“代码”>“类”，将新文件命名为 **PhoneDialer**，然后单击“添加”按钮：

  ![](quickstart-images/vs/new-phone-dialer-ios.png "添加新类")

1. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 <code>Dial</code> 方法，此方法将在 iOS 平台上用于拨打翻译后的电话号码：

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

  通过按 **Ctrl+S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，右键单击“Phoneword.Android”项目，然后选择“添加”>“新建项...”：

  ![](quickstart-images/vs/add-new-item-android.png "添加新项")

1. 在“添加新项”对话框中，选择“Visual C#”>“Android”>“类”，将新文件命名为 **PhoneDialer**，然后单击“添加”按钮：

  ![](quickstart-images/vs/new-phone-dialer-android.png "添加新类")

1. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 Android 平台上用于拨打翻译后的电话号码：

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

                    var intent = new Intent (Intent.ActionCall);
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

  通过按 **Ctrl+S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”的“Phoneword.Android”项目中，双击“MainActivity.cs”将其打开，然后删除所有模板代码并将其替换成下列代码：

        using Android.App;
        using Android.Content.PM;
        using Android.OS;

        namespace Phoneword.Droid
        {
            [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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

  按 Ctrl+S，保存对 MainActivity.cs 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”的“Phoneword.Android”项目中，双击“属性”，然后选择“Android 清单”选项卡：

  ![](quickstart-images/vs/android-manifest.png "打开的 Android 清单")

1. 在“所需的权限”部分中，启用“CALL_PHONE”权限。 这将向应用程序授予进行电话呼叫的权限：

  ![](quickstart-images/vs/android-manifest-changed.png "启用 CallPhone 权限")

  通过按 **Ctrl+S**，保存对清单所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”中，右键单击“Phoneword.UWP”项目，然后选择“添加”>“新建项...”：

  ![](quickstart-images/vs/add-new-item-uwp.png "添加新项")

1. 在“添加新项”对话框中，选择“Visual C#”>“代码”>“类”，将新文件命名为 **PhoneDialer**，然后单击“添加”按钮：

  ![](quickstart-images/vs/new-phone-dialer-uwp.png "添加新类")

1. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 和 helper 方法，这些方法将在通用 Windows 平台上用于拨打翻译后的电话号码：

        using Phoneword.UWP;
        using System;
        using System.Threading.Tasks;
        using Windows.ApplicationModel.Calls;
        using Windows.UI.Popups;
        using Xamarin.Forms;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.UWP
        {
            public class PhoneDialer : IDialer
            {
                bool dialled = false;

                public bool Dial(string number)
                {
                    DialNumber(number);
                    return dialled;
                }

                async Task DialNumber(string number)
                {
                    var phoneLine = await GetDefaultPhoneLineAsync();
                    if (phoneLine != null)
                    {
                        phoneLine.Dial(number, number);
                        dialled = true;
                    }
                    else
                    {
                        var dialog = new MessageDialog("No line found to place the call");
                        await dialog.ShowAsync();
                        dialled = false;
                    }
                }

                async Task<PhoneLine> GetDefaultPhoneLineAsync()
                {
                    var phoneCallStore = await PhoneCallManager.RequestStoreAsync();
                    var lineId = await phoneCallStore.GetDefaultLineAsync();
                    return await PhoneLine.FromIdAsync(lineId);
                }
            }
        }

  通过按 **Ctrl+S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

1. 在“解决方案资源管理器”的“Phoneword.UWP”项目中，右键单击“引用”，然后选择“添加引用...”：

  ![](quickstart-images/vs/uwp-add-reference.png "添加引用")

1. 在“引用管理器”对话框中，选择“通用 Windows”>“扩展”>“适用于 UWP 的 Windows Mobile 扩展”，然后单击“确定”按钮：

  ![](quickstart-images/vs/uwp-add-reference-extensions.png "添加适用于 UWP 的 Windows 移动扩展")

1. 在“解决方案资源管理器”的“Phoneword.UWP”项目中，双击“Package.appxmanifest”：

  ![](quickstart-images/vs/uwp-manifest.png "打开 UWP 清单")

1. 在“功能”页上，启用“电话呼叫”功能。 这将向应用程序授予进行电话呼叫的权限：

  ![](quickstart-images/vs/uwp-manifest-changed.png "启用“电话呼叫”功能")

  通过按 **Ctrl+S**，保存对清单所做的更改，然后关闭文件。

1. 在 Visual Studio 中，选择“生成”>“生成解决方案”菜单项，或按 **Ctrl+Shift+B**。 将生成应用程序，Visual Studio 状态栏中将显示一条成功消息：

  ![](quickstart-images/vs/build-successful.png "生成成功")

  如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成应用程序。

1. 在“解决方案资源管理器”中，右键单击“Phoneword.UWP”项目，然后选择“设为启用项目”：

  ![](quickstart-images/vs/uwp-set-as-startup-project.png "设为启动项目")

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动应用程序：

  ![](quickstart-images/vs/start.png "Visual Studio 工具栏")
  ![](quickstart-images/vs/phone-result-uwp.png "Phoneword 应用程序 UWP")

1. 在“解决方案资源管理器”中，右键单击“Phoneword.Android”项目，然后选择“设为启用项目”。
1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 Android 模拟器内的应用程序。
1. 如果拥有 iOS 设备并符合 Xamarin.Forms 开发的 Mac 系统要求，请使用类似技术将应用部署到 iOS 设备。 或者，将应用部署到 [iOS 远程模拟器](~/tools/ios-simulator.md)。

  注意：所有模拟器都不支持电话呼叫。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac，然后在起始页上单击“新建项目...”创建新项目：

  ![](quickstart-images/xs/new-solution.png "新建解决方案")

1. 在“为新项目选择一个模板”对话框中，单击“多平台”>“应用”，选择“空白窗体应用”模板，然后单击“下一步”按钮：

  ![](quickstart-images/xs/choose-template.png "选择模板")

1. 在“配置空白窗体应用”对话框中，为新应用 `Phoneword` 命名，确保“使用可移植类库”单选按钮已选中，并确保“将 XAML 用于用户界面文件”复选框已选中，然后单击“下一步”按钮：

  ![](quickstart-images/xs/configure-app.png "配置 Forms 应用程序")

1. 在“配置新空白窗体应用”对话框中，将“解决方案”和“项目”名称保留设置为 `Phoneword`，为项目选择合适的位置，然后单击“创建”按钮创建项目：

  ![](quickstart-images/xs/configure-project.png "配置 Forms 项目")

1. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

  ![](quickstart-images/xs/add-new-file.png "添加新文件")

1. 在“新建文件”对话框中，选择“Forms”>“Forms ContentPage Xaml”，将新文件命名为 **MainPage**，然后单击“新建”按钮。 这会将一个名为 **MainPage** 的页面添加到项目：

  ![](quickstart-images/xs/add-mainpage-class.png "添加新的 ContentPage")

1. 在“Solution Pad”中，双击 **MainPage.xaml**将其打开：

  ![](quickstart-images/xs/open-mainpage-xaml.png "打开的 MainPage.xaml")

1. 在“MainPage.xaml”中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.MainPage">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <Label Text="Enter a Phoneword:" />
              <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
              <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
              <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
            </StackLayout>
        </ContentPage>

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

1. 在“Solution Pad”中，双击 **MainPage.xaml.cs** 将其打开：

  ![](quickstart-images/xs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

1. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 如果分别在用户界面上单击“翻译”和“调用”按钮，作为响应，将分别执行 `OnTranslate` 和 `OnCall` 方法：

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
                    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
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

  > [!NOTE]
  > 尝试在此时构建应用程序将导致稍后要修复的错误。

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

1. 在“Solution Pad”中，双击 **App.xaml.cs**将其打开：

  ![](quickstart-images/xs/open-app-class.png "打开的 App.xaml.cs")

1. 在“App.xaml.cs”中，删除所有模板代码并将其替换为以下代码。 `App` 构造函数会将 `MainPage` 类设置为页面，该页面将在应用程序启动时显示：

        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public App()
                {
                    InitializeComponent();
                    MainPage = new MainPage();
                }

                protected override void OnStart()
                {
                    // Handle when your app starts
                }

                protected override void OnSleep()
                {
                    // Handle when your app sleeps
                }

                protected override void OnResume()
                {
                    // Handle when your app resumes
                }
            }
        }

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **Phoneword.cs** 所做的更改，然后关闭文件。

1. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

  ![](quickstart-images/xs/add-new-translator-file.png "添加新文件")

1. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneTranslator**，然后单击“新建”按钮：

  ![](quickstart-images/xs/add-translator-class.png "添加新类")

1. 在“PhoneTranslator.cs”中，删除所有模板代码并将其替换为以下代码。 此代码会将手机词翻译为电话号码：

        using System.Text;

        namespace Core
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

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneTranslator.cs** 所做的更改，然后关闭文件。

1. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

  ![](quickstart-images/xs/add-new-interface.png "添加新文件")

1. 在“新建文件”对话框中，选择“常规”>“空界面”，将新文件命名为 **IDialer**，然后单击“新建”按钮：

  ![](quickstart-images/xs/add-idialer-interface.png "添加新接口")

1. 在“IDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将定义 `Dial` 方法，必须在每个平台上实现此方法，才可拨打翻译后的电话号码：

        namespace Phoneword
        {
            public interface IDialer
            {
                bool Dial(string number);
            }
        }

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **IDialer.cs** 所做的更改，然后关闭文件。

  > [!NOTE]
> 此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

1. 在“Solution Pad”中，选择“Phoneword.iOS”项目，右键单击并选择“添加”>“新建文件...”：

  ![](quickstart-images/xs/add-new-file-ios.png "添加新文件")

1. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneDialer**，然后单击“新建”按钮：

  ![](quickstart-images/xs/new-phonedialer-ios.png "添加新类")

1. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 iOS 平台上用于拨打翻译后的电话号码：

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

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

1. 在“Solution Pad”中，选择“Phoneword.Droid”项目，右键单击并选择“添加”>“新建文件...”：

  ![](quickstart-images/xs/add-new-file-android.png "添加新文件")

1. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 **PhoneDialer**，然后单击“新建”按钮：

  ![](quickstart-images/xs/new-phonedialer-android.png "添加新类")

1. 在“PhoneDialer.cs”中，删除所有模板代码并将其替换为以下代码。 此代码将创建 `Dial` 方法，此方法将在 Android 平台上用于拨打翻译后的电话号码：

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

                    var intent = new Intent (Intent.ActionCall);
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

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **PhoneDialer.cs** 所做的更改，然后关闭文件。

1. 在“Solution Pad”的“Phoneword.Droid”项目中，双击“MainActivity.cs”将其打开，然后删除所有模板代码并将其替换成下列代码：

        using Android.App;
        using Android.Content.PM;
        using Android.OS;

        namespace Phoneword.Droid
        {
            [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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

  选择“文件”>“保存”（或按 &#8984;+S），保存对 MainActivity.cs 所做的更改，然后关闭文件。

1. 在“Solution Pad”中，展开“属性”文件夹，然后右键单击“AndroidManifest.xml”文件：

  ![](quickstart-images/xs/android-manifest.png "打开的 Android 清单")

1. 在“所需的权限”部分中，启用“CallPhone”权限。 这将向应用程序授予进行电话呼叫的权限：

  ![](quickstart-images/xs/android-manifest-changed.png "启用 CallPhone 权限")

  通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **AndroidManifest.xml** 所做的更改，然后关闭文件。

1. 在“Solution Pad”中，从“Phoneword”项目中删除“PhonewordPage”类。 创建项目时将自动添加此页面，因此不再需要此页面。
1. 在 Visual Studio for Mac 中，选择“生成”>“生成所有”菜单项，或按 **&#8984; + B**。 应用程序将生成，并在 Visual Studio for Mac 工具栏中显示一条成功消息。

  ![](quickstart-images/xs/build-successful.png "生成成功")

1. 如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成应用程序。
1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 iOS 模拟器内的应用程序：

  ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具栏")
  ![](quickstart-images/xs/phoneword-result-ios.png "iOS 模拟器")

  注意：iOS 模拟器不支持电话呼叫。

1. 在**解决方案面板**中，选择“Phoneword.Droid”项目，右键单击并选择“设为启动项目”：

  ![](quickstart-images/xs/set-startup-project.png "设为启动项目")

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 Android 模拟器内的应用程序：

  ![](quickstart-images/xs/phoneword-result-android.png "Android 仿真器")

  注意：Android 模拟器不支持电话呼叫。

-----

祝贺你完成 Xamarin.Forms 应用程序。 本指南的[下一个主题](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md)将回顾此演练中采用的步骤，以深入了解使用 Xamarin.Forms 开发应用程序的基础知识。


## <a name="related-links"></a>相关链接

- [通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword（示例）](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
