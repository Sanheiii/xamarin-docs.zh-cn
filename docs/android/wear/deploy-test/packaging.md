---
title: 打包应用程序
description: 本文介绍如何打包 Android 应用程序。
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 3263bfd9cea184a7bfbb29099abe2f1b3deb4126
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458051"
---
# <a name="packaging-wear-apps"></a>打包应用程序

> [!WARNING]
> 下面的文档和示例项目可能不再维护。
> 从 [Xamarin 11.1][xa-11.1]版中，不再支持 android 手持型应用程序中自动打包 android 应用程序。 建议改为将 Android 应用程序作为 [独立的应用程序][standalone] 进行分发。

Android 磨损1.0 应用使用完整 Android 应用进行打包，以便 Google Play 上的分发。

Android 磨损2.0 应用可以作为 [独立的应用程序][standalone]提交到 Google Play。

[xa-11.1]: /xamarin/android/release-notes/11/11.1
[standalone]: https://developer.android.com/training/wearables/apps/standalone-apps

## <a name="automatic-packaging"></a>自动打包

从 Xamarin Android 5.0 开始，当你创建从掌上项项目到磨损项目的项目引用时，你的损耗应用会自动打包为你的手持式应用中的资源。 你可以使用以下步骤来创建此关联： 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 如果你的应用程序还不在你的手持式解决方案中，请右键单击 "解决方案" 节点，然后选择 " **添加 > 添加现有项目 ...**"。

2. 导航到你的应用程序的 **.csproj** 文件，选择它，然后单击 " **打开**"。 应用程序项目现在应在你的手持式解决方案中可见。

3. 右键单击 " **引用** " 节点，然后选择 " **添加引用**"。

4. 在 " **引用管理器** " 对话框中，启用您的磨损项目 (单击添加复选标记) ，然后单击 **"确定"**。

5. 更改磨损项目的包名称，使其与掌上项目的包名称匹配 (包名称可在 " **Android 清单) >" 属性** "下进行更改。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 如果你的应用程序还不在你的手持式解决方案中，请右键单击 "解决方案" 节点，然后选择 " **添加 > 添加现有项目 ...**"。

2. 导航到你的应用程序的 **.csproj** 文件，选择它，然后单击 " **打开**"。 应用程序项目现在应在你的手持式解决方案中可见。

3. 右键单击解决方案中的手持式项目节点，然后单击 " **编辑引用 ...**"。

4. 在 " **编辑引用** " 对话框中，启用您的磨损项目 (单击添加复选标记) ，然后单击 **"确定"**。

5. 更改磨损项目的包名称，使其与掌上项目的包名称匹配 (包名称可在 " **Android 应用程序) > 的" 项目选项** "下进行更改。

-----

请注意，如果磨损应用的包名称与手持应用的包名称不匹配，则会出现 **XA5211** 错误。 例如：

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正此错误，请更改该应用程序的包名称，使其与手持应用程序的包名称匹配。

单击 " **生成" >**"生成" 时，该关联会触发将磨损项目自动打包到主要手持式 (Phone) 项目中。 会自动生成应用程序并将其作为资源包含在手持式应用中。

应用程序项目生成的程序集不会用作手持型 (Phone) 项目中的程序集引用。 相反，生成过程将执行以下操作：

- 验证包名称是否匹配。 

- 生成 XML，并将其添加到手持式项目，以将其与应用程序关联。 例如： 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- 将磨损应用作为 **原始** 资源添加到手持项目。 

## <a name="manual-packaging"></a>手动打包

在版本5.0 之前，可以在 Xamarin 中编写 Android 应用程序，但必须遵循以下手动打包说明来分发应用程序： 

1. 确保你的可穿戴项目和手持 (电话) 项目具有相同的版本号和包名称。

2. 手动将可穿戴项目构建为 **发布** 版本。

3. 手动添加发布 **。APK** 从步骤 (2) 到手持式 (手机) 项目的 **资源/原始** 目录。

4. 手动在手持式项目中添加一个新的 XML 资源 **资源/xml/wearable_app_desc.xml** ，该项目引用步骤 (3) 中的可穿戴 **APK** ：

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 将元素手动添加 `<meta-data />` 到手持式项目的 **AndroidManifest.xml** `<application>` 元素，该元素引用新的 XML 资源：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另请参阅 Android 开发人员网站的 [手动 packging 说明](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。