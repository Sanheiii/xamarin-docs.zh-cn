---
title: SkiaSharp 图形 Xamarin.Forms
description: 'SkiaSharp 是适用于 .NET 和 c # 的2D 图形系统，由在 Google 产品中广泛使用的开源 Skia 图形引擎提供支持。 本指南说明如何在应用程序中使用 SkiaSharp for 2D 图形 Xamarin.Forms 。'
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ca08815e422b9ec96dcb11d6b069a227bf62866
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555263"
---
# <a name="skiasharp-graphics-in-no-locxamarinforms"></a>SkiaSharp 图形 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_在应用程序中使用 SkiaSharp for 2D 图形 Xamarin.Forms_

SkiaSharp 是适用于 .NET 和 c # 的2D 图形系统，由在 Google 产品中广泛使用的开源 Skia 图形引擎提供支持。 可以在应用程序中使用 SkiaSharp Xamarin.Forms 来绘制2d 矢量图形、位图和文本。 有关 SkiaSharp 库和其他教程的更多常规信息，请参阅 [2D 绘图](~/graphics-games/skiasharp/index.md) 指南。

本指南假定你熟悉 Xamarin.Forms 编程。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**网络研讨会： SkiaSharp Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 初步

的 SkiaSharp Xamarin.Forms 打包为 NuGet 包。 Xamarin.Forms在 Visual Studio 或 Visual Studio for Mac 中创建解决方案后，可以使用 NuGet 包管理器搜索**SkiaSharp**包，并将其添加到解决方案中。 如果在添加 SkiaSharp 之后检查每个项目的 " **引用** " 部分，可以看到已将各种 **SkiaSharp** 库添加到了解决方案中的每个项目。

如果你的 Xamarin.Forms 应用程序面向 ios，请编辑其 **info.plist** 文件，将最低部署目标更改为 ios 8.0。

在任何使用 SkiaSharp 的 c # 页面中，你都需要为 `using` 命名空间包含一个指令 [`SkiaSharp`](xref:SkiaSharp) ，其中包含你将在图形编程中使用的所有 SkiaSharp 类、结构和枚举。 还需要一个 `using` 用于 [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) 特定于的类的命名空间的指令 Xamarin.Forms 。 这是一个很小的命名空间，最重要的类是 [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) 。 此类派生自 Xamarin.Forms `View` 类，并托管你的 SkiaSharp 图形输出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空间还包含一个 `SKGLView` 派生自的类， `View` 但使用 OpenGL 呈现图形。 为了简单起见，本指南将自身限制为 `SKCanvasView` ，但使用 `SKGLView` 的是非常类似的。

## <a name="skiasharp-drawing-basics"></a>[SkiaSharp 绘制基础知识](basics/index.md)

您可以用 SkiaSharp 绘制的一些最简单的图形图是圆、椭圆和矩形。 在显示这些图表时，您将了解 SkiaSharp 坐标、大小和颜色。 文本和位图的显示更复杂，但这些文章还介绍了这些技术。

## <a name="skiasharp-lines-and-paths"></a>[SkiaSharp 线和路径](paths/index.md)

图形路径是一系列连接的直线和曲线。 可以对路径进行描边和/或填充。 本文包含直线绘图的许多方面，包括笔划端和联接，以及虚线和虚线，但会停止短的曲线几何图形。

## <a name="skiasharp-transforms"></a>[SkiaSharp 转换](transforms/index.md)

转换允许对图形对象进行统一转换、缩放、旋转或倾斜。 本文还介绍了如何使用标准的 3 x 3 转换矩阵创建非仿射转换，并将转换应用于路径。

## <a name="skiasharp-curves-and-paths"></a>[SkiaSharp 曲线和路径](curves/index.md)

探索路径将继续向路径对象添加曲线，并利用其他强大的路径功能。 你将了解如何在简洁文本字符串中指定整个路径，如何使用路径效果，以及如何深入了解路径内部。

## <a name="skiasharp-bitmaps"></a>[SkiaSharp 位图](bitmaps/index.md)

位图是与显示设备的像素相对应的位的矩形数组。 这一系列文章演示了如何加载、保存、显示、创建、绘制、绘制 SkiaSharp 位图，以及如何对其进行动画处理。

## <a name="skiasharp-effects"></a>[SkiaSharp 效果](effects/index.md)

效果是指改变图形的正常显示的属性，包括线性和圆形渐变、位图平铺、blend 模式、模糊等等。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp Xamarin.Forms 网络研讨会 (视频) ](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)