---
title: Xamarin.Forms 多屏显示快速入门
description: 本文介绍如何通过添加第二个屏幕来扩展 Phoneword 应用程序，以跟踪应用程序的调用历史记录。
ms.prod: quickstart
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: c931b4f74fbfbbb7396e492cb7ad7ae5d0097bad
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242386"
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Xamarin.Forms 多屏显示快速入门

本快速入门演示了如何通过添加第二个屏幕来扩展 Phoneword 应用程序，以跟踪应用程序的呼叫历史记录。 最终的应用程序如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 应用程序")](quickstart-images/intro-app-examples.png#lightbox "Phoneword Application")

扩展 Phoneword 应用程序，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio。 在起始页上，单击“打开项目…”，然后在“打开项目”对话框中选择 Phoneword 项目的解决方案文件：

    ![](quickstart-images/vs/open-solution.png "打开项目")

2. 在“解决方案资源管理器”中，右键单击“Phoneword”项目，然后选择“添加”>“新建项...”：

    ![](quickstart-images/vs/add-new-item.png "添加新项")

3. 在“添加新项”对话框中，选择“Visual C# 项”>“Xamarin.Forms”>“内容页”，将新项命名为“CallHistoryPage”，然后单击“添加”按钮。 这会将一个名为 **CallHistoryPage** 的页面添加到项目：

    ![](quickstart-images/vs/add-callhistorypage-class.png "Xamarin.Forms 项目模板")

4. 在 **CallHistoryPage.xaml** 中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>
    ```

    通过按 **Ctrl+S**，保存对 **CallHistoryPage.xaml** 所做的更改，然后关闭文件。

5. 在“解决方案资源管理器”中，双击共享“Phoneword”项目中的“App.xaml.cs”文件打开该文件：

    ![](quickstart-images/vs/open-app-class.png "打开的 App.xaml.cs")

6. 在 **App.xaml.cs** 中，导入 `System.Collections.Generic` 命名空间，添加 `PhoneNumbers` 属性的声明，初始化 `App` 构造函数中的属性，并将 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 属性初始化为 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)。 使用 `PhoneNumbers` 集合存储应用程序呼叫的每个已翻译的电话号码列表：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

7. 在“解决方案资源管理器”中，双击共享“Phoneword”项目中的“MainPage.xaml”文件打开该文件：

    ![](quickstart-images/vs/open-mainpage-xaml.png "打开的 MainPage.xaml")

8. 在 **MainPage.xaml** 中，在 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控件末尾处添加 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控件。 此按钮用于导航到呼叫历史记录页：

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    按 **Ctrl+S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

9. 在“解决方案资源管理器”中，双击 **MainPage.xaml.cs** 将其打开：

    ![](quickstart-images/vs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

10. 在 **MainPage.xaml.cs** 中，添加 `OnCallHistory` 事件处理程序方法，并修改 `OnCall` 事件处理程序方法，从而将已翻译的电话号码添加到 `App.PhoneNumbers` 集合并启用 `callHistoryButton`（前提是 `dialer` 变量不为 `null`）：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    按 **Ctrl+S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

11. 在 Visual Studio 中，选择“生成”>“生成解决方案”菜单项，或按 **Ctrl+Shift+B**。 将生成应用程序，Visual Studio 状态栏中将显示一条成功消息：

    ![](quickstart-images/vs/build-successful.png "生成成功")

    如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成应用程序。

12. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动应用程序：

    ![](quickstart-images/vs/start.png "Visual Studio 工具栏")
    ![](quickstart-images/vs/phone-result-uwp.png "Phoneword 应用程序 UWP")

13. 在“解决方案资源管理器”中，右键单击“Phoneword.Droid”项目，然后选择“设为启用项目”。
14. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 Android 模拟器内的应用程序。
15. 如果拥有 iOS 设备并符合 Xamarin.Forms 开发的 Mac 系统要求，请使用类似技术将应用部署到 iOS 设备。 或者，将应用部署到 [iOS 远程模拟器](~/tools/ios-simulator.md)。

    注意：所有模拟器都不支持电话呼叫。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac。 在起始页上单击“打开…”，然后在对话框中选择 Phoneword 项目的解决方案文件：

    ![](quickstart-images/xs/open-solution.png "打开解决方案")

2. 在**解决方案面板**中，选择“Phoneword”项目，右键单击并选择“添加”>“新文件...”：

    ![](quickstart-images/xs/add-new-file.png "添加新文件")

3. 在“新建文件”对话框中，选择“Forms”>“Forms ContentPage Xaml”，将新文件命名为 **CallHistoryPage**，然后单击“新建”按钮。 这会将一个名为 **CallHistoryPage** 的页面添加到项目：

    ![](quickstart-images/xs/add-callhistorypage-class.png "添加 Forms ContentPage")

4. 在“Solution Pad”中，双击 **CallHistoryPage.xaml** 将其打开：

    ![](quickstart-images/xs/open-callhistorypage-xaml.png "打开的 CallHistoryPage.xaml")

5. 在 **CallHistoryPage.xaml** 中，删除所有模板代码并将其替换为以下代码。 此代码以声明方式定义页面上的用户界面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>      
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **CallHistoryPage.xaml** 所做的更改，然后关闭文件。

6. 在“Solution Pad”中，双击 **App.xaml.cs**将其打开：

    ![](quickstart-images/xs/open-app-class.png "打开的 App.xaml.cs")

7. 在 **App.xaml.cs** 中，导入 `System.Collections.Generic` 命名空间，添加 `PhoneNumbers` 属性的声明，初始化 `App` 构造函数中的属性，并将 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 属性初始化为 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)。 使用 `PhoneNumbers` 集合存储应用程序呼叫的每个已翻译的电话号码列表：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

8. 在“Solution Pad”中，双击 **MainPage.xaml**将其打开：

    ![](quickstart-images/xs/open-mainpage-xaml.png "打开的 MainPage.xaml")

9. 在 **MainPage.xaml** 中，在 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控件末尾处添加 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控件。 此按钮用于导航到呼叫历史记录页：

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

10. 在“Solution Pad”中，双击 **MainPage.xaml.cs** 将其打开：

    ![](quickstart-images/xs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

11. 在 **MainPage.xaml.cs** 中，添加 `OnCallHistory` 事件处理程序方法，并修改 `OnCall` 事件处理程序方法，从而将已翻译的电话号码添加到 `App.PhoneNumbers` 集合并启用 `callHistoryButton`（前提是 `dialer` 变量不为 `null`）：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

12. 在 Visual Studio for Mac 中，选择“生成”>“生成所有”菜单项，或按 **&#8984; + B**。 应用程序将生成，并会在 Visual Studio for Mac 工具栏中显示一条成功消息：

    ![](quickstart-images/xs/build-successful.png "生成成功")

    如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成应用程序。

13. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 iOS 模拟器内的应用程序：

    ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具栏")
    ![](quickstart-images/xs/phone-result-ios.png "iOS 模拟器")

    注意：iOS 模拟器不支持电话呼叫。

14. 在**解决方案面板**中，选择“Phoneword.Droid”项目，右键单击并选择“设为启动项目”：

    ![](quickstart-images/xs/set-startup-project.png "设为启动项目")

15. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动 Android 模拟器内的应用程序：

    ![](quickstart-images/xs/phone-result-android.png "Android 仿真器")

    注意：Android 模拟器不支持电话呼叫。

-----

祝贺你！你已完成多屏幕 Xamarin.Forms 应用程序。 本指南的[下一个主题](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md)将回顾此演练中采用的步骤，以深入了解使用 Xamarin.Forms 进行页面导航和数据绑定的知识。


## <a name="related-links"></a>相关链接

- [Phoneword（示例）](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
- [PhonewordMultiscreen（示例）](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/)
