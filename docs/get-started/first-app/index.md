---
title: 生成首个 Xamarin.Forms 应用
description: 视频指南介绍如何在 Visual Studio 中生成你的第一个 Xamarin.Forms 应用程序。
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 05/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e6670faf921248c007c20307fddb6a34057c5202
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436678"
---
# <a name="build-your-first-no-locxamarinforms-app"></a>生成第一个 Xamarin.Forms 应用

观看此视频，然后按照视频中的步骤使用 Xamarin.Forms 创建第一个移动应用Xamarin.Forms。

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Windows 分步说明

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建”>“项目...”，或按“创建新项目...”按钮：

    [![创建新项目](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. 搜索“Xamarin”或从“项目类型”菜单中选择“移动”。 选择“移动应用(Xamarin.Forms)”项目类型：

    [![Xamarin 项目的筛选器](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. 选择项目名称 &ndash; 示例使用“AwesomeApp”：

    [![选择项目名称](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. 单击“空白”项目类型，确保选择了“Android”和“iOS”：

    [![Android 和 iOS 以及 .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. 等到 NuGet 包还原（状态栏中将出现“还原已完成”消息）。

6. 新 Visual Studio 2019 安装不会配置 Android 模拟器。 单击“调试”按钮上的下拉箭头，然后选择“创建 Android Emulator”以启动仿真器创建屏幕：

    ![“创建 Android Emulator”下拉菜单](images/win-2019/debug-dropdown.png)

7. 在仿真器创建屏幕中，使用默认设置并单击“创建”按钮：

    [![Android 仿真器创建屏幕](images/win-2019/create-emulator-sml.png)](images/win-2019/create-emulator.png#lightbox)

8. 创建仿真器会使你返回“设备管理器”窗口。 单击“启动”按钮以启动新仿真器：

    ![“设备管理器”中的 Android 仿真器](images/win-2019/start-emulator.png)

9. Visual Studio 2019 现在应在“调试”按钮上显示新仿真器的名称：

    ![“调试”按钮上的 Android 仿真器名称](images/win-2019/debug-emulator-name.png)

10. 单击“调试”按钮以生成应用程序并将其部署到 Android 仿真器：

    ![显示应用程序的 Android 仿真器](images/win-2019/android-emulator.png)

## <a name="customize-the-application"></a>自定义应用程序

可以自定义应用程序以添加交互功能。 执行以下步骤可向应用程序添加用户交互：

1. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

2. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

3. 调试 Android 上的应用：

    ![Android 应用](images/win/07-sml.png)

> [!NOTE]
> 示例应用程序包括视频中未涵盖的附加交互功能。

## <a name="build-an-ios-app-in-visual-studio-2019"></a>在 Visual Studio 2019 中生成 iOS 应用

可使用联网的 Mac 计算机从 Visual Studio 生成和调试 iOS 应用。 有关详细信息，请参阅[安装说明](~/ios/get-started/installation/windows/index.md)。

此视频介绍在 Windows 上使用 Visual Studio 2019 生成和测试 iOS 应用的过程：

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Windows 分步说明

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建”>“项目...”或按“创建新项目...”按钮，然后选择“Visual C#”>“跨平台”>“移动应用(Xamarin.Forms)”  ：

    [![移动应用 (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略  ：

    [![Android 和 iOS 以及 .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. 等到 NuGet 包还原（状态栏中将出现“还原已完成”消息）。

4. 按调试按钮（或“调试”>“开始调试”菜单项）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 调试 Android 上的应用：

    ![Android 应用](images/win/07-sml.png)

    > [!TIP]
    > 可使用联网的 Mac 计算机从 Visual Studio 生成和调试 iOS 应用。 有关详细信息，请参阅[安装说明](~/ios/get-started/installation/windows/index.md)。

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Mac 分步说明

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建解决方案...”或按“新建项目...”按钮，然后选择“多平台”>“应用”>“空白窗体应用”  ：

    [![空白窗体应用](images/01-sml.png)](images/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略  ：

    [![Android 和 iOS 以及 .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. 右键单击解决方案，还原 NuGet 包：

    ![Android 应用](images/03-sml.png)

4. 按调试按钮（或“运行”>“开始调试”）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 调试 Android 上的应用：

    ![Android 应用](images/07-sml.png)

8. 右键单击，将 iOS 设置为“启动项目”：

    [![将启动项目设置为 IOS](images/08-sml.png)](images/08.png#lightbox)

9. 调试 iOS 上的应用：

    ![iOS 应用](images/09-sml.png)

::: zone-end

可从[示例库](/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)下载或在 [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp) 上查看完整代码。

## <a name="next-steps"></a>后续步骤

- [单页快速入门](~/get-started/quickstarts/single-page.md) &ndash; 生成功能更强大的应用。
- [Xamarin.Forms 示例](~/xamarin-forms/samples/index.md) &ndash; 下载并运行代码示例和示例应用。
- [创建移动应用电子书](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; 深入介绍 Xamarin.Forms 开发的章节（PDF 格式），包括数百个其他示例。