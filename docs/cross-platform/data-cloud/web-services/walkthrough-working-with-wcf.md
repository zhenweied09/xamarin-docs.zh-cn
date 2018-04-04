---
title: 演练-使用 WCF
description: 本演练介绍如何使用 Xamarin 生成的移动应用程序可以使用 WCF web 服务使用 BasicHttpBinding 类。
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: 7f6885415e1b5e0c988d13fe331703213b9b8fb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---working-with-wcf"></a>演练-使用 WCF

_本演练介绍如何使用 Xamarin 生成的移动应用程序可以使用 WCF web 服务使用 BasicHttpBinding 类。_


它是移动应用程序能够与后端系统通信的一个常见要求。 有许多选项和选项用于后端框架，其中之一是[Windows Communication Foundation](http://msdn.microsoft.com/en-us/library/ms731082.aspx) (WCF)。 本演练将提供的 Xamarin 移动应用程序可以使用 WCF 服务使用示例`BasicHttpBinding`类。 本演练包括以下主题：

1.  **创建 WCF 服务**-在本部分中，我们将创建具有两种方法非常基本的 WCF 服务。 第一种方法将需要一个字符串参数，而另一种方法将采用 C# 对象。 本部分还将讨论如何配置开发人员的工作站，以允许远程访问 WCF 服务。
1.  **创建一个 Xamarin.Android 应用程序**-一旦创建 WCF 服务后，我们将创建一个简单的 Xamarin.Android 应用程序将使用 WCF 服务。 本部分将介绍如何创建一个 WCF 服务的代理类来促进与 WCF 服务的通信。
1.  **创建一个 Xamarin.iOS 应用程序**-本教程的最后一部分涉及到创建的简单的 Xamarin.iOS 应用程序将使用 WCF 服务。

<a name="Requirements" />

## <a name="requirements"></a>要求

本演练假定你已具备一定的创建和使用 WCF 服务。

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>创建 WCF 服务

前面我们的第一个任务是创建移动应用程序中，与进行通信的 WCF 服务。

1. 启动 Visual Studio 2017，并创建新项目。
1. 在**新项目**对话框中，选择**WCF > WCF 服务库**模板，并将解决方案命名解决方案`HelloWorldService`:

    ![](walkthrough-working-with-wcf-images/new-wcf-service.png "创建新的 WCF 服务库")

1. 在**解决方案资源管理器**，添加一个名为的新类`HelloWorldData`到项目：

    ```csharp
        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }
    ```


1. 在**解决方案资源管理器**，重命名`IService1.cs`到`IHelloWorldService.cs`，并将重命名`Service1.cs`到`HelloWorldService.cs`。
1. 在**解决方案资源管理器**，打开`IHelloWorldService.cs`，将代码替换为以下代码：

    ```csharp
        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }
    ```
  
    此服务提供两种方法 – 其中一个采用字符串参数，另一个使用.NET 对象。

1. 在**解决方案资源管理器**，打开`HelloWorldService.cs`，将代码替换为以下代码：

    ```csharp
        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }
    ```

1. 在**解决方案资源管理器**，打开`App.config`，更新`name`属性`<service>`节点，`contract`属性`<endpoint>`节点，和`baseAddress`属性`<add>`节点：

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>
    ```

1. 生成并运行 WCF 服务。 WCF 测试客户端将承载该服务：

    ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "在测试客户端中运行的 WCF 服务")

1. 运行 WCF 测试客户端，启动浏览器并导航到的 WCF 服务终结点：

    ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "WCF 服务浏览器信息页")

> [!IMPORTANT]
> 以下部分才有必要，如果你需要接受远程连接 Windows 10 工作站上。 如果你有另一种平台上部署 WCF 服务，则可以忽略该节。

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>配置远程访问到 IIS Express

当连接仅来自本地计算机，则承载 WCF 本地已足够。 但是，远程设备 （如 Android 设备或 iPhone） 不会对本地 WCF 服务的任何访问。 因此，本部分介绍如何配置 Windows 10 和 IIS Express 接受远程连接：

1.  **接受远程连接配置 IIS Express** -此步骤包括编辑的 IIS Express，以接受特定端口上的远程连接配置文件，然后设置 IIS Express，以接受的传入流量的规则。
1.  **将异常添加到 Windows 防火墙**-你必须打开通过远程应用程序可以使用与 WCF 服务进行通信的 Windows 防火墙的端口。

    你将需要知道你的工作站的 IP 地址。 此示例的目的，我们将假定我们的工作站具有 192.168.1.143 的 IP 地址。

1. 让我们开始通过配置 IIS Express 以侦听外部请求。 我们可以执行此操作通过 IIS express 在编辑配置文件`[solutiondirectory]\.vs\config\applicationhost.config`，如以下屏幕截图中所示：

    [![](walkthrough-working-with-wcf-images/image05.png "我们可以在此屏幕截图所示执行此操作通过在 solutiondirectory.vsconfigapplicationhost.config，IIS express 编辑配置文件")](walkthrough-working-with-wcf-images/image05.png#lightbox)


    找到`site`同名元素`HelloWorldWcfHost`。 其外观应类似于以下 XML 代码段：

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>
    ```
 
    我们将需要添加另一个`binding`以打开端口 8734 到外部流量。 添加以下 XML 到`bindings`元素，将替换为你自己的 IP 地址的 IP 地址：

    ```xml
    <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
    ```
    
    这会将配置 IIS Express，以接受来自任何远程 IP 地址的端口 8734 上的计算机的外部 IP 地址的 HTTP 流量。 上面的代码段假定运行 IIS Express 的计算机的 IP 地址是 192.168.1.143。 后所做的更改，`bindings`元素应如下所示：

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>
    ```

1. 接下来，我们需要配置 IIS Express 接受端口 8734 上的传入连接。 启动管理命令提示符，并运行此命令：

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. 最后一步是配置 Windows 防火墙以允许在端口 8734 上的外部流量。 从管理命令提示符，运行以下命令：

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    此命令将作为 Windows 10 工作站位于同一子网允许端口 8734 从所有设备上的传入流量。

您已创建非常基本的 WCF 服务承载在 IIS Express 中，将接受来自其他设备或我们子网上的计算机的传入连接。 你可以通过运行你的应用程序以及访问来测试此扩展`http://localhost:8733/Design_Time_Addresses/HelloWorldService/`工作站上和`http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/`从子网上的另一台计算机。

若要允许 IIS Express，以保持运行并提供服务，请关闭**编辑并继续**选项*项目属性 > Web > 调试器*。

## <a name="creating-a-proxy-for-the-web-service"></a>创建 Web 服务代理

应用程序可以使用服务之前，必须为 WCF 服务，创建 web 服务代理。 这可以通过以下操作实现：

1. 添加名为.NET 标准类库`HelloWorldServiceProxy`，和删除项目中的任何类。
1. 运行`HelloWorldService`项目。
1. 与`HelloWorldService`运行项目中，添加一个新**连接的服务**到项目中，使用**Microsoft WCF Web 服务引用的提供程序**。
1. 在**服务终结点**选项卡**配置 WCF Web 服务引用**对话框中，单击**发现**按钮，删除`mex`结尾处检测到中的终结点**URI**下拉列表中，输入`HelloWorldServiceProxy`作为**Namespace**，然后单击**下一步**按钮。
1. 在**数据类型选项**选项卡**配置 WCF Web 服务引用**对话框中，通过单击接受默认设置**下一步**按钮。
1. 在**客户端选项**选项卡**配置 WCF Web 服务引用**对话框中，确保**公共**复选框已选中，然后单击**完成**按钮。
1. 生成`HelloWorldServiceProxy`项目。

> [!NOTE]
> 创建在 Visual Studio 2017 中使用 Microsoft WCF Web 服务引用提供程序的代理的替代方法是使用 ServiceModel 元数据实用工具 (svcutil.exe)。 有关详细信息，请参阅[ServiceModel 元数据实用工具 (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>创建 Xamarin.Android 应用程序

WCF 服务代理可供 Xamarin.Android 应用程序，如下所示：

1. 在 Visual Studio 中，将新的空白 Android 项目添加到解决方案并将其命名`HelloWorld.Android`。
1. 在`HelloWorld.Android`项目中，添加对引用`HelloWorldServiceProxy`项目中和的引用来`System.ServiceModel`命名空间。
1. 在**解决方案资源管理器**，打开`Resources/layout/main.axml`并将现有的 XML 替换为以下 XML:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>
    ```
    
    以下屏幕截图显示了在设计器的 UI:

    [![](walkthrough-working-with-wcf-images/image09.png "这是此 UI 在设计器中的类似的屏幕快照")](walkthrough-working-with-wcf-images/image09.png#lightbox)
    
1. 在**解决方案资源管理器**，打开`Resources/values/Strings.xml`并添加以下 XML:

    ```xml
    <string name="say_hello_world">Say Hello World</string>
    <string name="get_hello_world_data">Get Hello World data</string>
    ```
    
1. 在**解决方案资源管理器**，打开`MainActivity.cs`和替换为以下代码替换现有代码：

    ```csharp
        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }
    ```

    替换`<insert_WCF_service_endpoint_here>`与 WCF 终结点的地址。

1. 在`MainActivity.cs`，修改`OnCreate`方法，以便它包含以下代码：

    ```csharp
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }
    ```
    
    上面的代码中初始化类的实例变量和电线事件处理程序。

1. 在`MainActivity.cs`，通过添加以下两种方法来实例化客户端代理类：

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
    
    上面的代码实例化和初始化`HelloWorldServiceClient`对象。

1. 在`MainActivity.cs`，甚至为添加处理程序中的两个按钮`Activity`:

    ```csharp
        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```
  
1. 运行应用程序，请确保 WCF 服务正在运行，并且两个按钮上单击。 应用程序将 WCF 以异步方式调用，条件是`Endpoint`正确设置字段：

    [![](walkthrough-working-with-wcf-images/image08.png "在 30 秒内响应应接收从每个 WCF 方法和我们的应用程序应类似于此屏幕截图")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>创建 Xamarin.iOS 应用程序

WCF 服务代理可供 Xamarin.iOS 应用程序，如下所示：

1. 在 Visual Studio 中，添加新的 iPhone**单视图应用程序**项目合并为解决方案并将其命名`HelloWorld.iOS`。
1. 在`HelloWorld.iOS`项目中，添加对引用`HelloWorldServiceProxy`项目中和的引用来`System.ServiceModel`命名空间。
1. 在**解决方案资源管理器**，双击`Main.storyboard`以在 iOS 设计器中打开该文件。 然后，添加以下`UIButton`和`UITextView`控件：

    ||名称|标题|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|说出"Hello，World"|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|获取"Hello，World"数据|
    |`UITextView`|`getHelloWorldDataText`||

    添加控件后, UI 应类似于下面的屏幕快照：

    [![](walkthrough-working-with-wcf-images/image12.png "添加控件后, UI 应类似于此屏幕截图")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. 在**解决方案资源管理器**，打开`ViewController.cs`并添加以下代码：

    ```xml
        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }
    ```
  
    替换`<insert_WCF_service_endpoint_here>`与 WCF 终结点的地址。

1. 在`ViewController.cs`，更新`ViewDidLoad`方法，以便它如下所示：

    ```csharp
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }
    ```
  
1. 在`ViewController.cs`，添加`InitializeHelloWorldServiceClient`和`CreateBasicHttpBinding`方法：

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
  
1. 在`ViewController.cs`，添加事件处理程序`TouchUpInside`对两个事件`UIButton`实例：

    ```csharp
        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```

1. 运行应用程序，请确保 WCF 服务正在运行，并且两个按钮上单击。 应用程序将 WCF 以异步方式调用，条件是`Endpoint`正确设置字段：

    [![](walkthrough-working-with-wcf-images/image10.png "在 30 秒内响应应接收从每个 WCF 方法和我们的应用程序应类似于此屏幕截图")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本教程介绍如何使用 Xamarin.Android 和 Xamarin.iOS 的移动应用程序中的 WCF 服务使用。 它介绍了如何创建 WCF 服务，并且说明了如何配置 Windows 10 和 IIS Express 以接受来自远程设备的连接。 然后，介绍了如何生成 WCF 代理客户端，并演示如何在 Xamarin.Android 和 Xamarin.iOS 应用程序中使用客户端代理。


## <a name="related-links"></a>相关链接

- [HelloWorld （示例）](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [使用 WCF 开发面向服务的应用程序](https://docs.microsoft.com/en-us/dotnet/framework/wcf/index)
- [如何： 创建 Windows Communication Foundation 客户端](https://docs.microsoft.com/en-us/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [ServiceModel 元数据实用工具 (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
