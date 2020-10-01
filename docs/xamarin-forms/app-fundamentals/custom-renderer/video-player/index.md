---
title: 实现视频播放器
description: 本文介绍如何使用 Xamarin.Forms 实现视频播放器应用程序。
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a4b092d90b3afa2197c1db576545d233eb6e208d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563505"
---
# <a name="implementing-a-video-player"></a>实现视频播放器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

有时需要在 Xamarin.Forms 应用程序中播放视频文件。 本系列文章介绍如何为名为 `VideoPlayer` 的 Xamarin.Forms 类编写适用于 iOS、Android 和通用 Windows 平台 (UWP) 的自定义呈现器。

在 [VideoPlayerDemos](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 示例中，所有实现和支持 `VideoPlayer` 的文件都位于名为 `FormsVideoLibrary` 的文件夹中，并使用 `FormsVideoLibrary` 命名空间或以 `FormsVideoLibrary` 开头的命名空间进行标识。 通过这种组织结构和命名方式，可以轻松地将视频播放器文件复制到自己的 Xamarin.Forms 解决方案中。

`VideoPlayer` 可播放来自以下三类源的视频文件：

- 使用 URL 的 Internet
- 嵌入在平台应用程序中的资源
- 设备的视频库

视频播放器需具备传输控件（即用于播放和暂停视频的按钮）和定位条（用于显示视频进度并允许用户快速跳转到不其他位置）。 `VideoPlayer` 可使用平台提供的传输控件和定位条（如下所示），也可使用你提供的自定义传输控件和定位条。 下面是 iOS、Android 和通用 Windows 平台上运行的程序：

[![播放 Web 视频](web-videos-images/playwebvideo-small.png "播放 Web 视频")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 视频")

当然，也可横向播放，扩大画面视图。

复杂型视频播放器还具备其他功能，例如音量控制，来电时中断视频的机制以及在播放期间保持屏幕活动的方式。

以下系列文章逐步演示如何生成平台呈现器和支持类：

## <a name="creating-the-platform-video-players"></a>[创建平台视频播放器](player-creation.md)

每个平台都需要 `VideoPlayerRenderer` 类，用于创建和维护平台支持的视频播放器控件。 本文介绍了呈现器类的结构，以及创建播放器的方式。

## <a name="playing-a-web-video"></a>[播放 Web 视频](web-videos.md)

Internet 或许是视频播放器最常见的视频来源。 本文介绍如何引用 Web 视频并将其用作视频播放器的源。

## <a name="binding-video-sources-to-the-player"></a>[将视频源绑定到播放器](source-bindings.md)

本文使用 `ListView` 提供要播放的视频集合。 一个程序显示代码隐藏文件如何设置视频播放器的视频源；而另一个程序显示如何在 `ListView` 和视频播放器之间使用数据绑定。

## <a name="loading-application-resource-videos"></a>[加载应用程序资源视频](loading-resources.md)

视频可作为资源嵌入到平台项目中。 本文介绍如何存储这些资源，然后将其加载到将使用视频播放器播放的程序中。

## <a name="accessing-the-devices-video-library"></a>[访问设备的视频库](accessing-library.md)

使用设备摄像头创建视频后，视频文件将存储在设备的图像库中。 本文介绍如何访问设备的图像选取器并选择视频，然后使用视频播放器进行播放。

## <a name="custom-video-transport-controls"></a>[自定义视频传输控件](custom-transport.md)

虽然每个平台上的视频播放器都提供自己的传输控件（即“播放”和“暂停”按钮），但你可以不使用这些按钮并提供自己的按钮 。 本文介绍如何进行此操作。

## <a name="custom-video-positioning"></a>[自定义视频定位](custom-positioning.md)

每个平台视频播放器都具备定位条，用于显示视频进度并可跳过或返回特定位置。 本文演示如何使用自定义控件替换定位条。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)