---
title: 的 XAML 热重载 Xamarin.Forms
description: 立即在正在运行的应用程序上重新加载对 XAML 文件所做的更改，这样就无需在 Xamarin.Forms 每次 XAML 更改后生成项目。
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca79a8a4059a303b6f4bfa152d645311a6056ffd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563882"
---
# <a name="xaml-hot-reload-for-no-locxamarinforms"></a>的 XAML 热重载 Xamarin.Forms

XAML 热重载将插入现有的工作流，以提高工作效率并节省时间。 如果不使用 XAML 热重载，则必须在每次想要查看 XAML 更改时生成并部署应用。 如果是热重载，则保存 XAML 文件时，所做的更改将反映在正在运行的应用程序中。 此外，还将保留你的导航状态和数据，从而使你可以快速循环访问 UI，而不会丢失你在应用中的位置。 因此，通过 XAML 热重载，你将花费更少的时间来重新生成和部署应用，以验证 UI 更改。

> [!NOTE]
> 如果要编写 WPF 或 UWP 应用，请参阅 [适用于 UWP 和 WPF 的 XAML 热重载](/visualstudio/debugger/xaml-hot-reload)。
>
> 的 XAML 热重载 Xamarin.Forms 当前 _不适_ 用于 Xamarin.Forms UWP 项目。

## <a name="system-requirements"></a>系统要求

| IDE/框架 | 所需版本 |
|------|------------------|
|Visual Studio 2019 | 16.4 或更高版本
Visual Studio 2019 for Mac | 8.4 或更高版本
Xamarin.Forms | 4.1 或更高版本

## <a name="enable-xaml-hot-reload-for-no-locxamarinforms"></a>为启用 XAML 热重载 Xamarin.Forms

如果从模板开始，则默认情况下将启用 XAML 热重载，并将项目配置为不使用其他设置。 在 Android 或 iOS 模拟器、模拟器或物理设备上调试应用程序，更改 XAML，并保存文件以触发 XAML 热重载。

如果你正在使用现有 Xamarin.Forms 解决方案，则无需进行其他安装即可使用 XAML 热重载，但你可能需要仔细检查配置，以确保获得最佳体验。 首先，在 IDE 设置中启用它：

* 在 Windows 上，选中 "在**工具**选项中**启用 xamarin 热重载**"  >  **选项**"  >  **Xamarin**  >  **热重载**"。
* 在 Mac 上，选中 "在**Visual Studio**中**启用 xamarin 热重载**"  >  **Preferences**  >  **工具以进行 xamarin**  >  **XAML 热重载**。
  * 在早期版本的 Visual Studio for Mac 中，菜单位于**Visual Studio**  >  **首选项**  >  **项目**  >  **Xamarin 热重载**。

然后，在 Android 和 iOS 生成设置中，检查链接器是否已设置为 "不链接" 或 "链接无"。 若要在物理 iOS 设备上使用 XAML 热重载，还必须选中 **"启用 Mono 解释** 器 (visual studio 16.4 及更高版本) 或将 **解释** 器添加到 (visual Studio 16.3) 和更高版本的 **其他 mtouch 参数** 。

您可以使用以下流程图检查您的现有项目的设置，以便与 XAML 热重载一起使用：

![XAML 热重载安装程序](hot-reload-images/hotreloadflowchart.png "XAML 热重载安装流程图")

## <a name="resilient-reloading"></a>复原重装

如果进行了更改，则无法重新加载 XAML 热重载，它将使用 IntelliSense 显示错误消息。 这些更改称为 "强制编辑"，其中包括在 XAML 中键入的内容，或者将控件布线到不存在的事件处理程序。 即使使用强制编辑，也可以继续重新加载，而无需重新启动应用程序-在 XAML 文件中的其他位置进行另一个更改，然后点击 "保存"。 不会重新加载 "强制编辑"，但会继续应用其他更改。

## <a name="reload-on-multiple-platforms-at-once"></a>一次在多个平台上重新加载

XAML 热重载支持在 Visual Studio 中同时进行调试，并 Visual Studio for Mac。 你可以同时部署 Android 和 iOS 目标，以查看在两个平台上同时反映的更改。 若要在多个平台上进行调试，请参阅：
* **Windows** [如何：设置多个启动项目](/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [设置多个启动项目](/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>已知的限制

* Xamarin.Forms尚*不*支持其他目标（如 UWP 和 macOS）。 可在 [此处](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html)跟踪 UWP 支持的进度。
* 在 XAML 热重载会话期间，无法添加、删除或重命名文件或 NuGet 包。 如果添加或删除文件或 NuGet 包，请重新生成并重新部署应用，以便继续使用 XAML 热重载。
* 将链接器设置为 "无" **链接** 或 **链接** ，以获得最佳体验。 **仅限链接 SDK**设置工作正常，但在某些情况下可能会失败。 可在 Android 和 iOS 生成选项中找到链接器设置。
* 在物理 iPhone 上调试需要解释器使用 XAML 热重载。 为此，请打开项目设置，选择 "iOS 生成" 选项卡，并确保启用 **"Mono 解释器"** 设置。 可能需要将属性页顶部的 " **平台** " 选项更改为 " **iPhone**"。
* 通过使用控件的值将控件分配给另一个字段或属性而创建的任何引用 `x:Name` 都不会重新加载。
* 在 AppShell 中更新 Shell 应用程序的可视层次结构可能会导致应用程序的状态保持有问题。 如果遇到问题，请重新生成应用程序以继续重新加载。
* XAML 热重载无法重新加载 c # 代码，包括事件处理程序、自定义控件、页代码隐藏以及其他类。

## <a name="more-resources"></a>更多资源

* [XAML 热重载提示和技巧](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [XAML 热重载 Xamarin.Forms 深入了解： Xamarin Show](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>疑难解答

* 如果 XAML 热重载未能初始化：
  * 更新 Xamarin.Forms 版本。
  * 确保你处于最新版本的 IDE。
  * 将 Android 或 iOS 链接器设置设置为 " **不链接** 项目的生成设置"。
* 如果保存 XAML 文件后没有任何反应，请确保在 IDE 中启用了 XAML 热重载。
* 如果是在物理 iPhone 上进行调试，而应用程序无响应，请检查是否已启用解释器。 若要启用它，请选中 **"启用 Mono 解释**器 (visual studio 16.4/8.4，并向上) 或添加 **--解释**器 (visual studio) 16.3 **Additional mtouch arguments**

若要报告 bug，请使用**帮助**  >  **发送反馈**  >  **报告 Windows 上的问题**，并**帮助**  >  **报告 Mac 上的问题**。