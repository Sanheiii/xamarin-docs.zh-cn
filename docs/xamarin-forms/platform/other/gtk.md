---
title: 'GTK # 平台设置'
description: 'Xamarin.Forms 现在为 GTK # 平台提供预览版支持'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ced6b54ce91f9b14d16a1baf8f5dd1d85902a42
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558396"
---
# <a name="gtk-platform-setup"></a>GTK # 平台设置

![预览](~/media/shared/preview.png)

Xamarin.Forms 现在为 GTK # 应用提供预览版支持。 GTK # 是一个图形用户界面工具包，它链接了 GTK + 工具包和各种 GNOME 库，允许使用 Mono 和 .NET 开发完全本机的 GNOME 图形应用程序。 本文演示如何将 GTK # 项目添加到 Xamarin.Forms 解决方案。

> [!IMPORTANT]
> Xamarin.Forms 支持 GTK # 由社区提供。 有关详细信息，请参阅[ Xamarin.Forms 平台支持](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

在开始之前，请创建新的 Xamarin.Forms 解决方案，或使用现有 Xamarin.Forms 解决方案，例如 [**GameOfLife**](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)。

> [!NOTE]
> 尽管本文重点介绍如何将 GTK # 应用添加到 Xamarin.Forms VS2017 和 Visual Studio for Mac 中的解决方案，但也可以在适用于 Linux 的 [MonoDevelop](https://www.monodevelop.com/) 中执行此应用。

## <a name="adding-a-gtk-app"></a>添加 GTK # 应用

适用于 macOS 和 Linux 的 GTK # 作为 [Mono](https://www.mono-project.com/download/stable/)的一部分进行安装。 适用于 .NET 的 GTK # 可在具有 [GTK # 安装程序](https://www.mono-project.com/download/stable/#download-win)的 Windows 上安装。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

按照以下说明添加将在 Windows 桌面上运行的 GTK # 应用：

1. 在 Visual Studio 2019 中，右键单击 " **解决方案资源管理器** 中的解决方案名称，然后选择" **添加 > "新建项目 ...**"。

2. 在 " **新建项目** " 窗口中，在左侧选择 " **Visual c #** 和 **Windows 经典桌面**"。 在项目类型列表中，选择 " **类库 (" .NET Framework ") **"，并确保 " **框架** " 下拉列表设置为最小值 .NET Framework 4.7。

3. 使用 **gtk** 扩展键入项目的名称，例如 **GameOfLife**。 单击 " **浏览** " 按钮，选择包含其他平台项目的文件夹，然后按 " **选择文件夹**"。 这会将 GTK 项目与解决方案中的其他项目放在同一目录中。

    ![添加新的 GTK 项目](gtk-images/win/add-new-project.png "添加新的 GTK 项目")

    按 **"确定"** 按钮创建项目。

4. 在 **解决方案资源管理器**中，右键单击新的 GTK 项目，然后选择 " **管理 NuGet 包**"。 选择 " **浏览** " 选项卡，然后搜索 **Xamarin.Forms** 3.0 或更高版本。

    ![选择：： no (Xamarin) ：：： NuGet 包](gtk-images/win/select-forms-nuget-package.png "选择：： no (Xamarin) ：：： NuGet 包")

    选择该包，然后单击 " **安装** " 按钮。

5. 现在搜索** Xamarin.Forms 。Platform.string** 3.0 包或更高版本。

    ![选择：： no (Xamarin。 Forms) ：：：。Platform.string NuGet 包](gtk-images/win/select-forms-platform-nuget-package.png "选择：： no (Xamarin。 Forms) ：：：。Platform.string NuGet 包")

    选择该包，然后单击 " **安装** " 按钮。

6. 在 **解决方案资源管理器**中，右键单击解决方案名称，然后选择 " **管理解决方案的 NuGet 包**"。 选择 " **更新** " 选项卡和 **Xamarin.Forms** 包。 选择所有项目，并将其更新为 Xamarin.Forms GTK 项目使用的同一版本。

7. 在 **解决方案资源管理器**中，右键单击 GTK 项目中的 **引用** 。 在 " **引用管理器** " 对话框中，选择左侧的 " **项目** "，然后选中 .NET Standard 或共享项目旁边的复选框：

    ![引用共享项目](gtk-images/win/reference-shared-project.png "引用共享项目")

8. 在 " **引用管理器** " 对话框中，按 " **浏览** " 按钮，浏览到 **C:\Program files (x86) \gtksharp\2.12\lib** 文件夹，然后选择 **atk-sharp.dll**、 **gdk-sharp.dll**、 **glade-sharp.dll**、 **glib-sharp.dll**、 **gtk-dotnet.dll**、 **gtk-sharp.dll** 文件。

    ![引用 GTK # 库](gtk-images/win/reference-gtk-libraries.png "引用 GTK # 库")

    按 **"确定"** 按钮添加引用。

9. 在 GTK 项目中，将 **Class1.cs** 重命名为 **Program.cs**。

10. 在 GTK 项目中，编辑 **Program.cs** 文件，使其类似于以下代码：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 并 Xamarin.Forms 创建应用程序窗口，并运行应用。

11. 在 **解决方案资源管理器**中，右键单击 GTK 项目，然后选择 " **属性**"。

12. 在 " **属性** " 窗口中，选择 " **应用程序** " 选项卡，并将 " **输出类型** " 下拉箭头更改为 " **Windows 应用程序**"。

    ![更改项目输出类型](gtk-images/win/change-project-output-type.png "更改项目输出类型")

13. 在 **解决方案资源管理器**中，右键单击 GTK 项目，然后选择 " **设为启动项目**"。 按 F5，在 Windows 桌面上通过 Visual Studio 调试器运行程序：

    ![GTK # 生活游戏](gtk-images/win/gtk-gameoflife.png "GTK # 生活游戏")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

按照以下说明添加将在 Mac 桌面上运行的 GTK # 应用：

1. 在 Visual Studio for Mac 中，右键单击 Xamarin.Forms 解决方案，然后选择 " **添加 > 添加新项目 ...**"。

2. 在 " **新建项目** " 窗口中，选择 " **其他 > .net > Gtk # 2.0 项目** ，然后按" **下一步**"。

3. 键入具有 **GTK** 扩展的项目的名称，例如 **GameOfLife**，并按 " **创建**"。

4. 在 **Solution Pad**中，右键单击 "包" > 为 GTK 项目 **添加包 ...** "，并添加 Xamarin.Forms 3.0 预发行版 NuGet 包或更高版本。

    ![选择：： no (Xamarin) ：：： NuGet 包](gtk-images/mac/select-forms-nuget-package.png "选择：： no (Xamarin) ：：： NuGet 包")

5. 在 **Solution Pad**中，右键单击 > 为 GTK 项目 **添加包 ...** "的包，然后添加 Xamarin.Forms 。Platform.string 3.0 预发行 NuGet 包或更高版本。

    ![选择：： no (Xamarin。 Forms) ：：：。Platform.string NuGet 包](gtk-images/mac/select-forms-platform-nuget-package.png "选择：： no (Xamarin。 Forms) ：：：。Platform.string NuGet 包")

6. 更新其他平台项目，以使用 GTK 项目使用的相同 Xamarin.Forms 版本。

7. 在 **Solution Pad**中，右键单击 "引用" > GTK 项目的 " **编辑引用 ...** "，并添加对项目的引用， Xamarin.Forms (".NET Standard" 或 "共享项目) "。

    ![引用共享项目](gtk-images/mac/reference-shared-project.png "引用共享项目")

8. 编辑 GTK 项目的 **Program.cs** 文件，使其类似于以下代码：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 并 Xamarin.Forms 创建应用程序窗口，并运行应用。

9. 在 **Solution Pad**中，右键单击 GTK 项目，然后选择 " **设为启动项目**"。

10. 在 Visual Studio for Mac 工具栏中，按 " **启动** " 按钮 (与 "播放" 按钮) 的三角形按钮，以启动应用程序。

    ![GTK # 生活游戏](gtk-images/mac/gtk-gameoflife.png "GTK # 生活游戏")

-----

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

您可以 Xamarin.Forms 通过 XAML 或代码确定您的应用程序在哪个平台上运行。 这使你可以更改在 GTK # 上运行时的程序特征。 在代码中，将的值 `Device.RuntimePlatform` 与 `Device.GTK` 等于字符串 "GTK" ) 的常量 (进行比较。 如果有匹配项，则应用程序在 GTK # 上运行。

在 XAML 中，可以使用 `OnPlatform` 标记来选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>应用程序图标

可以在启动时设置应用程序图标：

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>主题

有多种可用于 GTK # 的主题，可以在应用中使用这些主题 Xamarin.Forms ：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>本机窗体

本机窗体允许 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 派生的页面由本机项目使用，包括 GTK # 项目。 这可以通过 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用扩展方法创建派生页面的实例并将其转换为本机 GTK # 类型来实现 `CreateContainer` ：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

有关本机窗体的详细信息，请参阅 [本机窗体](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>问题

这是一种预览版，因此，你应预计并非一切都准备就绪。 有关当前的实现状态，请参阅 [状态](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)和当前的已知问题，请参阅 [挂起 & 已知问题](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。