---
title: SkiaSharp 线和路径
description: 本文介绍如何使用 SkiaSharp 在应用程序中绘制线条和图形路径 Xamarin.Forms ，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 380c9d5b8159a1a142ebf005955e4345dbc6e6d8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562894"
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 线和路径

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 绘制线条和图形路径_

[上一部分](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)演示了 SkiaSharp `SKCanvas` 类包含若干方法，用于绘制圆、椭圆、矩形、圆角矩形、文本和位图。 本节和更高部分介绍了与创建和呈现 *图形路径*相关的各种类。

图形路径是在 SkiaSharp 中绘制直线和曲线的最通用方法。 本部分介绍如何使用 [`SKPath`](xref:SkiaSharp.SKPath) 对象绘制直线，并使用一组小直线 (称为 *折线*) 绘制可定义算法的曲线。 后面的有关 [**SkiaSharp 曲线和路径**](../curves/index.md) 的部分介绍了支持的各种曲线种类 `SKPath` 。

此部分中的所有示例程序显示在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序主页的标题**行和路径**下，以及该解决方案的 "[**路径**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths)" 文件夹下。

## <a name="lines-and-stroke-caps"></a>[线和笔划大写字母](lines.md)

了解如何使用 SkiaSharp 来绘制具有不同笔划帽的线条。

## <a name="path-basics"></a>[路径基础知识](paths.md)

浏览 `SKPath` 用于组合线条和曲线的 SkiaSharp 对象。

## <a name="the-path-fill-types"></a>[路径填充类型](fill-types.md)

发现 SkiaSharp 路径填充类型可能产生的不同效果。

## <a name="polylines-and-parametric-equations"></a>[折线和参数等式](polylines.md)

使用 SkiaSharp 可呈现可以使用参数化公式定义的任何行。

## <a name="dots-and-dashes"></a>[点和短划线](dots.md)

主要是在 SkiaSharp 中绘制虚线和虚线的复杂性。

## <a name="finger-painting"></a>[手指绘图](finger-paint.md)

使用手指在画布上绘制。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)