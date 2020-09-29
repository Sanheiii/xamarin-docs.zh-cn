---
title: 创建 MonoGame UWP 项目
description: MonoGame 可用于创建通用 Windows 平台的游戏和应用，面向具有一个基本代码和一组内容的多个设备。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 5d970c596d403c7d55ccc23bb5e9ba7e5fbd623a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431973"
---
# <a name="creating-a-monogame-uwp-project"></a>创建 MonoGame UWP 项目

_MonoGame 可用于创建通用 Windows 平台的游戏和应用，面向具有一个基本代码和一组内容的多个设备。_

本演练介绍了 MonoGame 通用 Windows 平台 (UWP) 项目创建和内容加载。 UWP 应用可在所有 Windows 10 设备上运行，包括桌面、平板电脑、Windows phone 和 Xbox。

本演练将创建一个空项目，该项目显示 *青的蓝色* 背景 () 的应用程序的传统背景色。

## <a name="requirements"></a>要求

开发 MonoGame UWP 应用需要：

- Windows 10 操作系统
- 任何版本的 Visual Studio 2017
- Windows 10 开发人员工具
- 将设备设置为开发人员模式
- [MonoGame 3.7.1 For Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) 或更高版本

有关详细信息，请参阅此页面，了解如何 [针对 Windows 10 UWP 开发进行设置](/windows/uwp/get-started/get-set-up)。

Xbox one 游戏可以在零售 Xbox one 硬件上开发。 开发 PC 和 Xbox 上都需要其他软件。 有关配置 Xbox for 游戏开发的信息，请参阅 [设置 xbox](/windows/uwp/xbox-apps/)one 的此页。

## <a name="creating-an-empty-template"></a>创建空模板

在 Windows 10 计算机上安装了所有必需的资源并启用了开发人员模式后，可以按照以下步骤使用 Visual Studio 创建一个新的 MonoGame 项目：

1. 选择“文件” > “新建” > “项目...”
1. 选择 "**已安装**的模板" "  >  **Templates**  >  **Visual c #**  >  **MonoGame** " 类别：

    ![MonoGame 类别](uwp-images/image1.png)

1. 选择 **MonoGame Windows 10 通用项目** 选项：

    ![选择 MonoGame Windows 10 通用项目选项](uwp-images/image2.png)

1. 输入新项目的名称，然后单击 **"确定"**。
如果在单击 "确定" 后 Visual Studio 显示任何错误，请验证是否安装了 Windows 10 工具，以及设备是否处于开发人员模式。

Visual Studio 完成创建模板后，可以运行它来查看运行的空项目：

![Visual Studio 完成创建模板后，运行该模板以查看运行的空项目](uwp-images/image3.png)

角中的数字提供诊断信息。 通过删除方法中的块中的代码，可以删除此信息 `App.xaml.cs` `DEBUG` `OnLaunched` ：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>在 Xbox One 上运行

UWP 项目可从同一项目部署到任何 Windows 10 设备。 设置 Windows 10 开发计算机和 Xbox one 后，可以通过将目标切换到远程计算机并输入 Xbox One 的 IP 地址来部署 UWP 应用：

![可以通过将目标切换到远程计算机并输入 Xbox One 的 IP 地址来部署 UWP 应用](uwp-images/remote.png)

在 Xbox one 上，白色边框表示电视的不安全区域。 有关详细信息，请参阅 [安全区域部分](#safe-area-on-xbox-one)。

![在 Xbox one 上，白色边框表示电视的不安全区域](uwp-images/safearea.png)

### <a name="safe-area-on-xbox-one"></a>Xbox one 上的安全区域

开发控制台游戏需要考虑到安全区域，这是屏幕中心的一个区域，其中应该包含 UI 或 HUD) 等所有关键视觉对象 (。 不保证安全区域外的区域在所有电视上都可见，因此放置在此区域中的视觉对象可能在某些显示器上部分或完全不可见。

Xbox one 的 MonoGame 模板将考虑安全区域，并将其呈现为白色边框。 此区域还会在运行时反映在游戏的 `Window.ClientBounds` 属性中，如 Visual Studio 中的 "监视" 窗口的图像所示。 请注意，客户端边界的高度为1016，尽管1920x1080 显示分辨率：

![请注意，客户端边界的高度为1016，尽管1920x1080 显示分辨率](uwp-images/clientbounds.png)

## <a name="referencing-content-in-uwp-projects"></a>引用 UWP 项目中的内容

MonoGame 项目中的内容可以直接从文件或通过 [MonoGame 内容管道](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)进行引用。 小型游戏项目可以从文件加载的简单性中获益。 使用内容管道优化内容以减少大小和加载时间时，较大的项目将受益。 与 Xbox 360 上的程序种类不同， `System.IO.File` 该类可用于 xbox ONE UWP 应用。

有关使用内容管道加载内容的详细信息，请参阅 [内容管道指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

### <a name="loading-content-from-file"></a>正在从文件加载内容

不同于 iOS 和 Android，UWP 项目可以引用相对于可执行文件的文件。 简单的游戏可以使用此方法加载内容，而无需修改和生成内容管道项目。

`Texture2D`从文件加载：

1. 将 .png 文件添加到 UWP 项目中的 Content 文件夹。 将内容添加到 Content 文件夹是 MonoGame 中的一种约定。
1. 右键单击新添加的 PNG，然后选择 "属性"。
1. 将 " **复制到输出目录** " 更改为 " **如果较新则复制**"。
1. 将以下代码添加到游戏的 Initialize 方法，以加载 `Texture2D` ：

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

有关使用的详细信息 `Texture2D` ，请参阅 [简介 MonoGame 指南](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>总结

本指南介绍了如何在加载文件时创建新的 UWP 项目和 UWP 特定的注意事项。 对创建完整 UWP 游戏感兴趣的开发人员可以在 [MonoGame Guide 简介](~/graphics-games/monogame/introduction/index.md)中了解有关 MonoGame 的详细信息。