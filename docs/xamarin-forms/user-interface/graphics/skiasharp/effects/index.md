---
title: SkiaSharp 效果
description: 了解如何通过渐变、位图平铺、blend 模式、模糊和其他效果更改图形的正常显示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e63d92cde5925561dae4217c3c46af2c2c59f795
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563986"
---
# <a name="skiasharp-effects"></a>SkiaSharp 效果

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp [`SKPaint`](xref:SkiaSharp.SKPaint) 类定义了六个属性，这些属性可根据一般的 _效果_进行分类。 这些是以某种方式更改图形的正常显示方式的属性。 SkiaSharp 效果分为六类：

## <a name="path-effects"></a>[路径效果](../curves/effects.md)

将的 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) 属性设置为 `SKPaint` 类型的对象 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 可以显示虚线，或使用从路径创建的模式来描边或填充区域。 在 SkiaSharp 的文章 [**路径效果**](../curves/effects.md)中，此系列中介绍了该路径效果。

## <a name="shaders"></a>[着色器](shaders/index.md)

将的 [`Shader`](xref:SkiaSharp.SKPaint.Shader) 属性设置 `SKPaint` 为类型的对象， [`SKShader`](xref:SkiaSharp.SKShader) 以显示线性或圆形渐变、平铺位图和 Perlin 干扰模式。

## <a name="blend-modes"></a>[混合模式](blend-modes/index.md)

将的 [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) 属性设置 `SKPaint` 为枚举的成员 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) ，以控制当目标图形显示在目标图形时所发生的情况。 SkiaSharp 支持所有 CSS 组合和混合模式，包括 Porter 模式、分离混合模式和非分离混合模式。

## <a name="mask-filters"></a>[掩码筛选器](mask-filters.md)

将的 [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) 属性设置 `SKPaint` 为类型的对象，以 [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) 提供模糊和其他 alpha 效果。

## <a name="image-filters"></a>[图像筛选器](image-filters.md)

将的 [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) 属性设置 `SKPaint` 为一个类型为的对象，以 [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 对位图进行模糊处理并创建投影、浮雕或 engraving 效果。

## <a name="color-filters"></a>[颜色筛选器](color-filters.md)

[`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) `SKPaint` 使用表或矩阵转换将的属性设置为类型的对象 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) ，从而更改颜色。

这些文章的所有示例代码都位于 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)中。 从主页中，选择 " **SkiaSharp 效果**"。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)