---
title: 'Xamarin Live 重装 (预览) '
description: 查看 XAML 中反映的更改，无需另一个编译和部署。
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdc5b715d4502590b56b41b864835a47076ad518
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556446"
---
# <a name="xamarin-live-reload-preview"></a>Xamarin Live 重装 (预览) 

> [!NOTE]
> Xamarin Live 重装预览已结束，我们希望感谢每个人提供反馈和意见。 
>
> 若要在应用运行时编辑 XAML，请[对 Xamarin.Forms 使用 Xaml 热重载](~/xamarin-forms/xaml/hot-reload.md)。
>

Xamarin Live 重载使你能够 **更改 XAML 并查看它们的实时，无需进行其他编译和部署**。 对 XAML 所做的任何更改将在保存时重新部署，并反映在部署目标上。

## <a name="requirements"></a>要求

* [Visual Studio 2017 版本15.7 或更高版本](https://visualstudio.microsoft.com/vs/) ，附带 .net 工作负载的 **移动开发** 。
* [ Xamarin.Forms 3.0.0 或更高](https://www.nuget.org/packages/Xamarin.Forms/)版本。

## <a name="getting-started"></a>入门
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. 从 Visual Studio Marketplace 安装 Xamarin Live 重载

Xamarin Live 重载是通过 Visual Studio Marketplace 分发的。 若要安装该扩展，请访问 [Visual Studio Marketplace 网站上的 Xamarin Live 重载页面](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) ，并单击 " **下载**"。

打开下载的 .vsix，然后单击 " **安装**"。

![Visual Studio 安装程序 Xamarin Live 重载确认](images/LiveReloadVSIXInstall.png)

或者，你可以在 Visual Studio 中的 "**扩展和更新**" 对话框中的 "**联机**" 选项卡中搜索它。

### <a name="2-configure-your-app-to-use-live-reload"></a>2. 将应用配置为使用实时重载

可以通过以下三个步骤，将实时重载添加到现有移动应用：

1. 确保所有项目都已更新为使用[ Xamarin.Forms 3.0.0 或更高](https://www.nuget.org/packages/Xamarin.Forms/)版本或更高版本。

2. 添加 **LiveReload** NuGet 包：

    a. **.NET Standard** –在 .NET Standard 2.0 库中安装 **LiveReload** NuGet。 不需要在平台项目中安装此功能。 确保 **包源** 设置为 " **全部**"。
    
    b. **共享项目** –将 **LiveReload** NuGet 安装到所有平台项目中， (例如 ANDROID、iOS、UWP 等 ) 。 确保 **包源** 设置为 " **全部**"。

    [![添加 Xamarin Live 重载 NuGet 和 NuGet 包管理器](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. 添加 `LiveReload.Init();` 到类中的构造函数 `Application` ，如下面的代码段所示：

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. 开始实时重新加载

编译并部署你的应用程序。 部署应用后，打开 XAML 文件，进行一些更改，并保存该文件。 您的更改将重新部署到部署目标。

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

实时重载适用于对任何 XAML 文件的更改。 对 c # 或添加/删除 NuGet 包的更改需要新的生成并进行部署才能生效。

## <a name="frequently-asked-questions"></a>常见问题 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Visual Studio for Mac 上是否提供 Xamarin Live 重载？ 

不可以，Xamarin Live 重载的预览版本仅适用于 Visual Studio 2017。

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>这是否适用于所有库，如 Prism？ 

由于你的应用程序已编译，实时重载适用于所有库，如 Prism 和第三方控件库，如 Telerik、Infragistics、Syncfusion、ArcGIS、GrapeCity 和其他控制供应商。

### <a name="what-changes-does-live-reload-redeploy"></a>实时重新部署的更改有哪些？ 

实时重载仅应用对 XAML 或 CSS 所做的更改。 如果更改 c # 文件，则需要重新编译。 

### <a name="what-platforms-are-supported"></a>支持哪些平台？ 

实时重载适用于支持的任何平台 Xamarin.Forms ，包括 Android、iOS 和 UWP。

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>这是否适用于模拟器、模拟器和物理设备？ 

是的，实时重载适用于所有有效的部署目标，包括 Android 仿真程序、iOS 模拟器和物理设备。 部署到设备需要设备和计算机位于同一 Wi-fi 网络上。

### <a name="does-this-work-with-corporate-networks"></a>这是否适用于企业网络？

如果正在调试 Android 模拟器或 iOS 模拟器，Live 重载会使用 localhost 进行通信。 如果要部署到设备，则设备和计算机需要处于同一 Wi-fi 网络上。 如果这是不可能的，你可以 [配置自己的实时重载服务器](#live-reload-server)，这样，无论网络连接设置如何，都可以实时重新加载。

### <a name="does-it-require-debugging-the-app"></a>是否需要调试应用？ 

否。 事实上，您甚至可以在任意数量的设备或模拟器/仿真器上启动所有受支持的应用程序目标 (Android、iOS 和 UWP) ，并同时查看所有更新。 

## <a name="limitations"></a>限制

* 仅支持重新加载 XAML。
* 除非使用 MVVM，否则不能在重新部署之间维护 UI 状态。

## <a name="known-issues"></a>已知问题

* 仅在 Visual Studio 中受支持。
* 必须将链接设置为 **不链接** 或 **链接框架 sdk** 
* 重新加载应用程序范围的资源 (即 **app.xaml** 或共享资源字典) ，将重置应用导航。 
* 重新加载 ContentView 当前需要重新加载包含页。
* 包含 AutomationId 的元素可能会导致重新加载失败。
* 在调试 UWP 的同时编辑 XAML 可能会导致运行时崩溃。 解决方法：使用 "开始执行（ **不调试） (Ctrl + f5) ** 而不是 ** (F5) 启动调试 **。

## <a name="troubleshooting"></a>疑难解答

### <a name="error-codes"></a>错误代码

* **XLR001**： *当前项目引用了 "LiveReload" NuGet 包版本 "[version]"，但 Xamarin Live 重载扩展需要版本 "[version]"。*

  为了允许实时重载功能的快速迭代和演化，NuGet 包和 Visual Studio 扩展必须完全匹配。 将 NuGet 包更新为已安装的相同版本的扩展。

* **XLR002**：在 *从命令行生成时，实时重载至少需要 "MqttHostname" 属性。或者，将 "EnableLiveReload" 设置为 "false" 以禁用该功能。*

  在从命令行生成时，实时重新加载所需的属性不可用 (或持续集成) 中，因此必须显式提供。 

* **XLR003**： *实时重新加载 NuGet 包需要安装 Xamarin Live 重装 Visual Studio 扩展。*

  尝试生成一个项目，该项目引用实时重载 NuGet 包，但未安装视觉对象扩展。  

* *加载程序集时出现异常： System.io.filenotfoundexception：无法加载程序集 "Xamarin. Reload.sql，Version = 0.3.27.0，Culture = 中立，PublicKeyToken ="。*

  宿主项目应使用 `PackageReference` 而不是 `packages.config`

### <a name="app-doesnt-connect"></a>应用未连接

构建应用程序时，"工具" > "工具" 中的信息 **> Xamarin > 实时重载** (主机名称、端口和加密) 密钥将嵌入在应用程序中，以便在 `LiveReload.Init()` 运行时，无需配对或配置即可成功连接。

除了一般的网络问题外 (防火墙、不同网络) 上的设备，应用可能无法成功连接 IDE 的主要原因是它的配置与 Visual Studio 中的不同。 出现这种情况的原因可能是：

* 应用已在另一台计算机上编译。
* 在不同的 Visual Studio 会话中编译并部署了应用，并 () **工具 > 选项 > Xamarin > 实时重载**检查**自动生成的加密密钥**。
* 已更改 Visual Studio 设置 (例如，主机名、端口或加密密钥) ，但未重新生成和部署应用。

这些情况都是通过重新生成并部署应用来解决的。

### <a name="uninstalling-preview-1"></a>卸载预览版1

如果你使用的是较旧的预览版，并且在卸载它时遇到问题，请执行以下步骤：

1. 删除文件夹 **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (注意：请将 "Enterprise" 替换为已安装的版本，将 "Preview" 替换为 "2017" （如果已安装到稳定的 VS) 
2. 打开该 Visual Studio 的 **开发人员命令提示符** ，然后运行 `devenv /updateconfiguration` 。 

## <a name="tips--tricks"></a>技巧 & 技巧

* 只要实时重载设置不会更改 (包括加密密钥，例如，如果关闭 **自动生成的加密密钥**) 并从同一台计算机进行生成，则不需要在初始部署后构建并部署应用，除非更改了代码或依赖项。 你可以重新启动以前部署的应用，它将连接到所使用的最后一个主机。

* 对于可以连接到同一个 Visual Studio 会话的设备有多少限制。 你可以根据需要在任意数量的设备/模拟器中部署和启动应用程序，以查看同时处理所有这些应用程序的实时加载。

* 实时重载只会重新加载应用程序的用户界面部分， *但不会重新创建* 页面，而是将视图模型替换 (或绑定上下文) 。 这意味着 *整个* 应用程序状态始终保留在重新加载范围内（包括注入的依赖项）。

## <a name="live-reload-server"></a>实时重载服务器

在从正在运行的应用程序到你的计算机的连接的情况下 (使用 `localhost` 或 `127.0.0.1` 在 **工具 > Options > > Xamarin** 中使用来表示，) 不可能 (例如防火墙、不同的网络) ，你可以配置远程服务器，而这两个 IDE 和应用都将连接到。

实时重新加载使用标准 [MQTT 协议](https://mqtt.org/) 来交换消息，因而可与 [第三方服务器](https://github.com/mqtt/mqtt.github.io/wiki/servers)通信。 甚至 [公共服务器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (也称为 *代理*) ，可以使用。 实时重载已使用 `broker.hivemq.com` 和 `iot.eclipse.org` 主机名进行测试，以及由 [www.cloudmqtt.com](https://www.cloudmqtt.com) 和 [www.cloudamqp.com](https://www.cloudamqp.com)提供的服务。 你还可以在云中部署你自己的 MQTT 服务器，如 [Azure 上的 HiveMQ](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud)。

你可以配置任何端口，但通常使用远程服务器的默认1883端口。 实时重新加载消息使用强的端到端 AES 对称加密，因此连接到远程服务器是安全的。 默认情况下，会在每个 Visual Studio 会话上重新生成加密密钥和初始化向量 (IV) 。

最简单的方法是在 Azure 中的空白 Ubuntu VM 中安装 [mosquitto](https://mosquitto.org) 服务器：

1. 在 Azure 门户中创建新的 Ubuntu Server VM
2. 在 "网络" 选项卡中为 1883 (默认 MQTT) 端口添加新的入站端口规则
3.  (bash 模式下打开 [Cloud Shell](/azure/cloud-shell/overview)) 
4. `ssh [USERNAME]@[PUBLIC_IP]`使用在 1) 中选择的用户名和 VM 概述页中显示的公共 IP 键入
5. 运行 `sudo apt-get install mosquitto` ，输入你在1中选择的密码) 

现在，可以使用该 IP 连接到自己的 MQTT 服务器。