---
title: 打包磨损应用
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: af96c0f8cf862b7a208beb5b91ecbb30598b09d9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="packaging-wear-apps"></a>打包磨损应用

Android 磨损应用来打包于 Google Play 上的分发的完整 Android 应用。 

## <a name="automatic-packaging"></a>自动打包

从开始使用 Xamarin Android 5.0，磨损应用自动打包为手持应用中资源时到磨损项目，从手持项目创建的项目引用。 以下步骤可用于创建此关联： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 如果您磨损的应用程序尚不手持解决方案的一部分，右键单击解决方案节点，然后选择**添加 > 添加现有项目...**.

2. 导航到**.csproj**文件磨损应用程序，选择它，然后单击**打开**。 磨损应用程序项目现在应可以看到手持解决方案中。

3. 右键单击**引用**节点，然后选择**添加引用**。

4. 在**引用管理器**对话框中，启用磨损项目 （单击以添加一个复选标记），然后单击**确定**。

5. 更改磨损项目的包名称，使其匹配手持项目的包名称 (包名称可以更改下**属性 > Android 清单**)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 如果您磨损的应用程序尚不手持解决方案的一部分，右键单击解决方案节点，然后选择**添加 > 添加现有项目...**.

2. 导航到**.csproj**文件磨损应用程序，选择它，然后单击**打开**。 磨损应用程序项目现在应可以看到手持解决方案中。

3. 右键单击解决方案，然后单击中的手持项目节点**编辑引用...**.

4. 在**编辑引用**对话框中，启用磨损项目 （单击以添加一个复选标记），然后单击**确定**。

5. 更改磨损项目的包名称，使其匹配手持项目的包名称 (包名称可以更改下**项目选项 > Android 应用程序**)。

-----


请注意，你将获得**XA5211**错误如果磨损应用的包名称与手持应用的包名称不匹配。 例如：

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正此错误，更改磨损应用的程序包名称，使其匹配手持应用的包名称。

当你单击**生成 > 所有生成**，这种关联触发自动打包的磨损项目到主要 Handheld (Phone) 项目。 磨损应用自动生成并包含为手持应用中的资源。

磨损应用程序项目生成的程序集不用作 Handheld (Phone) 项目中的程序集引用。 相反，生成过程执行以下任务：

-   验证包名匹配。 

-   生成的 XML 并将其添加到手持项目中以将其与磨损应用关联。 例如： 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   添加磨损应用作为**原始**到手持项目的资源。 


## <a name="manual-packaging"></a>手动打包

可以在版本 5.0、 之前在 Xamarin.Android 中编写 Android 磨损应用，但必须遵循这些手动打包说明来分发应用程序： 

1. 确保你 Wearable 项目和 Handheld (Phone) 项目具有相同的版本数和包名称。

2. 手动生成 Wearable 项目作为**版本**生成。

3. 手动添加版本**。APK**从单步 (2) 执行**原始资源/** Handheld (Phone) 项目的目录。

4. 手动添加新的 XML 资源**Resources/xml/wearable_app_desc.xml**在手持项目中它是指可穿戴**APK**步骤 (3) 中：

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手动添加`<meta-data />`到手持项目的元素**AndroidManifest.xml** `<application>`指的是新的 XML 资源的元素：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另请参阅 Android 开发人员站点[手动 packging 说明](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。

