---
title: SkiaSharp blend 模式
description: 使用 blend 模式来定义图形对象层叠在一起时所发生的情况。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dd1ef993fc2184561f01dad99aa01858251f8f73
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560671"
---
# <a name="skiasharp-blend-modes"></a>SkiaSharp blend 模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

这些文章重点介绍的 [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) 属性 [`SKPaint`](xref:SkiaSharp.SKPaint) 。 `BlendMode`属性的类型为，它是 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) 包含29个成员的枚举。

`BlendMode`属性确定 (经常称为_源_) 的图形对象在 (名为_目标_) 的现有图形对象上呈现时会发生的情况。 通常情况下，我们预计新图形对象会掩盖其下的对象。 但这种情况仅因为默认 blend 模式为 `SKBlendMode.SrcOver` ，这意味着源是在目标 _上_ 绘制的。 其他28个成员 `SKBlendMode` 会导致其他效果。 在图形编程中，以各种方式组合图形对象的方法称为 _组合_。

## <a name="the-skblendmodes-enumeration"></a>SKBlendModes 枚举

SkiaSharp blend 模式与 W3C [**合成和 Blend Level 1**](https://www.w3.org/TR/compositing-1/) 规范中所述的模式密切相关。 Skia [**SkBlendMode 概述**](https://skia.org/user/api/SkBlendMode_Overview) 还提供了有用的背景信息。 对于混合模式的一般介绍，维基百科中的 [**混合模式**](https://en.wikipedia.org/wiki/Blend_modes) 一文是一个不错的开端。 Adobe Photoshop 支持混合模式，因此，在该上下文中有其他有关 blend 模式的在线信息。

枚举的29个成员 `SKBlendMode` 可以分为三个类别：

| Porter-Duff | 可分离    | 不可分离 |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

这三个类别的名称将在以下讨论中采用更多含义。 此处列出成员的顺序与枚举定义中的顺序相同 `SKBlendMode` 。 第一列中的13个枚举成员的整数值为0到12。 第二列是对应于整数13到24的枚举成员，第三列中的成员的值为25到28。

这些混合模式在 W3C**合成和 Blend Level 1**文档中的顺序_大致_相同，但有几个区别：在 `Src` W3C 文档中将模式称为_Copy_ ，并 `Plus` 将其称为_较浅_。 W3C 文档定义不包含在中的 _正常_ 混合模式， `SKBlendModes` 因为它与相同 `SrcOver` 。 `Modulate`混合模式 (在第一列的顶部) 并不包括在 W3C 文档中，并且之前的模式讨论 `Multiply` `Screen` 。

因为 `Modulate` blend 模式对于 Skia 是唯一的，所以它将作为附加的 Porter-Duff 模式和分离模式进行讨论。

## <a name="the-importance-of-transparency"></a>透明度的重要性

从历史上看，组合是与 _alpha 通道_的概念一起开发的。 在显示图面（如 `SKCanvas` 对象和全色位图）中，每个像素都包含4个字节：红色、绿色和蓝色分量分别为1个字节，还有一个用于透明度的其他字节。 此 alpha 组件为0表示完全透明，而0xFF 为完全不透明，在这些值之间具有不同的透明度级别。

许多混合模式都依赖于透明度。 通常，当 `SKCanvas` 首次在处理程序中获取时 `PaintSurface` ，或 `SKCanvas` 创建以在位图上绘图时，第一步是此调用：

```csharp
canvas.Clear();
```

此方法用透明黑色像素替换画布的所有像素，等效于 `new SKColor(0, 0, 0, 0)` 或整数0x00000000。 所有像素的所有字节都初始化为零。

`SKCanvas`在处理程序中获取的的绘制图面 `PaintSurface` 可能看起来具有白色背景，但这只是因为 `SKCanvasView` 本身具有透明背景，而页面具有白色背景。 您可以通过将的 Xamarin.Forms `BackgroundColor` 属性设置 `SKCanvasView` 为颜色来演示这一事实 Xamarin.Forms ：

```csharp
canvasView.BackgroundColor = Color.Red;
```

或者，在派生自的类中 `ContentPage` ，可以设置页面背景色：

```csharp
BackgroundColor = Color.Red;
```

由于 SkiaSharp 画布本身是透明的，因此你将看到 SkiaSharp 图形后的红色背景。

[**SkiaSharp 透明度**](../../basics/transparency.md)一文介绍了一些使用透明度在复合图像中排列多个图形的基本技术。 混合模式超出了这一点，但透明度对于混合模式仍是至关重要的。

## <a name="skiasharp-porter-duff-blend-modes"></a>[SkiaSharp Porter-Duff blend 模式](porter-duff.md)

使用 Porter-Duff blend 模式来基于源和目标映像编写场景。

## <a name="skiasharp-separable-blend-modes"></a>[SkiaSharp 分离混合模式](separable.md)

使用 "可分离混合模式" 更改红色、绿色和蓝色颜色。

## <a name="skiasharp-non-separable-blend-modes"></a>[SkiaSharp 非分离混合模式](non-separable.md)

使用非分离混合模式可更改色调、饱和度或发光度。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)