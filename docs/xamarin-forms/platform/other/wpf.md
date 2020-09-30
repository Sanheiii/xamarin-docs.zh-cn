---
title: WPF 平台安装
description: Xamarin.Forms 对 WPF 平台具有预览版支持。
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 17db86eb6e6c767498f1d8b550b923377b905364
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557434"
---
# <a name="wpf-platform-setup"></a>WPF 平台安装

![预览](~/media/shared/preview.png)

Xamarin.Forms 在 .NET Framework 上和 .NET Core 3 上，对 Windows Presentation Foundation (WPF) 提供预览版支持。 本文演示如何将面向 .NET Framework 的 WPF 项目添加到 Xamarin.Forms 解决方案中。

> [!IMPORTANT]
> Xamarin.Forms 社区提供对 WPF 的支持。 有关详细信息，请参阅[ Xamarin.Forms 平台支持](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

在开始之前，请 Xamarin.Forms 在 Visual Studio 2019 中创建新的解决方案，或使用现有 Xamarin.Forms 解决方案，例如 [**BoxViewClock**](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)。 只能将 WPF 应用添加到 Xamarin.Forms Windows 中的解决方案。

## <a name="add-a-wpf-application"></a>添加 WPF 应用程序

按照以下说明添加将在 Windows 7、8和10桌面上运行的 WPF 应用程序：

1. 在 Visual Studio 2019 中，右键单击 " **解决方案资源管理器** 中的解决方案名称，然后选择" **添加 > 新项目 ...**"。

2. 在 "**添加新项目**" 窗口中，选择 "**语言**" 下拉菜单中的 " **c #** "，选择 "**平台**" 下拉菜单中的 " **Windows** "，然后在 "**项目类型**" 下拉菜单中选择 "**桌面**"。 在项目类型列表中，选择 " **WPF 应用 ( .NET Framework) **：

    ![添加新的 WPF 项目](wpf-images/add-project.png "添加新的 WPF 项目")

    按 **下 "下一步** " 按钮。

    > [!NOTE]
    > Xamarin.Forms 4.7 包括对 .NET Core 3 上运行的 WPF 应用程序的支持。

3. 在 " **配置新项目** " 窗口中，使用 **wpf** 扩展键入项目的名称，例如 **BoxViewClock**。 单击 " **浏览** " 按钮，选择 " **BoxViewClock** " 文件夹，然后按 " **选择文件夹** " 将 WPF 项目置于与解决方案中的其他项目相同的目录中：

    ![添加新的 WPF 项目](wpf-images/configure-project.png "添加新的 WPF 项目")

    按 " **创建** " 按钮创建项目。

4. 在**解决方案资源管理器**中，右键单击新的 " **BoxViewClock** " 项目，然后选择 "**管理 NuGet 包 ...**"。选择 "**浏览**" 选项卡，然后搜索** Xamarin.Forms 。Platform.string**：

    ![选择 NuGet 包](wpf-images/select-nuget-package.png "选择 NuGet 包")

    选择该包，然后单击 " **安装** " 按钮。

5. 在 **解决方案资源管理器** 中右键单击解决方案名称，然后选择 " **管理解决方案的 NuGet 包**..."。选择 " **更新** " 选项卡，然后选择 **Xamarin.Forms** 包。 选择所有项目，并将其更新为同一 Xamarin.Forms 版本：

    ![更新 NuGet 包](wpf-images/update-nuget-package.png "更新 NuGet 包")

6. 在 WPF 项目中，右键单击 " **引用** "，然后选择 " **添加引用 ...**"。在 " **引用管理器** " 对话框中，选择左侧的 " **项目** "，然后选中 " **BoxViewClock** " 项目旁边的复选框：

    ![引用共享项目](wpf-images/reference-shared-project.png "引用共享项目")

    按 " **确定"** 按钮。

7. 编辑 WPF 项目的 **mainwindow.xaml** 文件。 在 `Window` 标记中，为添加 XML 命名空间声明** Xamarin.Forms 。Platform.string**程序集和命名空间：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    现在，请将 `Window` 标记更改为 `wpf:FormsApplicationPage` 。 将 `Title` 设置更改为应用程序的名称，例如， **BoxViewClock**。 已完成的 XAML 文件应如下所示：

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. 编辑 WPF 项目的 **MainWindow.xaml.cs** 文件。 添加两个新 `using` 指令：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    将的基类从更改 `MainWindow` `Window` 为 `FormsApplicationPage` 。 调用后 `InitializeComponent` ，添加以下两个语句：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    `using`完整的**MainWindows.xaml.cs**文件应如下所示：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

9. 在 **解决方案资源管理器** 中右键单击 WPF 项目，然后选择 " **设为启动项目**"。 按 F5，在 Windows 桌面上通过 Visual Studio 调试器运行程序：

    ![WPF BoxView 时钟](wpf-images/wpf-boxviewclock.png "WPF BoxView 时钟" )

## <a name="platform-specifics"></a>平台细节

可以 Xamarin.Forms 从代码或 XAML 确定应用程序运行的平台。 这使您可以在 WPF 上运行时更改程序特征。 在代码中，将的值 `Device.RuntimePlatform` 与 `Device.WPF` 等于字符串 "WPF" ) 的常量 (进行比较。 如果存在匹配项，则应用程序在 WPF 上运行。

在 XAML 中，可以使用 `OnPlatform` 标记来选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

## <a name="window-size"></a>窗口大小

可以在 WPF **mainwindow.xaml** 文件中调整窗口的初始大小：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>问题

这是一种预览版，因此，你应预计并非一切都准备就绪。 并非的所有 NuGet 包 Xamarin.Forms 都适用于 WPF，某些功能可能无法完全正常工作。

## <a name="related-video"></a>相关视频

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF 支持视频**