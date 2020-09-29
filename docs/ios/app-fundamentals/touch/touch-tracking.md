---
title: Xamarin 中的多点触控 Finger 跟踪
description: 本文档介绍如何在 Xamarin iOS 应用程序中跟踪多点触控笔势中的单个手指。 它围绕手指绘制应用程序示例。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: deda3a96272db42af17221e613822b858d57abb1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436334"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Xamarin 中的多点触控 Finger 跟踪

_本文档演示如何从多个手指跟踪触摸事件_

有时，多点触控应用程序需要在屏幕上同时移动时跟踪各个手指。 一个典型的应用程序就是一个手指画图程序。 您希望用户能够用一个手指绘图，还可以一次使用多个手指进行绘制。 当程序处理多个触控事件时，它需要区分这两个手指。

当手指首次触摸屏幕时，iOS 将 [`UITouch`](xref:UIKit.UITouch) 为该手指创建一个对象。 此对象保持与手指在屏幕上移动的相同位置，然后从屏幕中抬起，此时对象被释放。 为了跟踪手指，程序应避免直接存储此 `UITouch` 对象。 相反，它可以使用 [`Handle`](xref:Foundation.NSObject.Handle) 类型的属性 `IntPtr` 来唯一标识这些 `UITouch` 对象。

几乎始终会有一次跟踪单个手指的程序维护用于触摸跟踪的字典。 对于 iOS 程序，字典键是用于 `Handle` 标识特定手指的值。 字典值依赖于应用程序。 在 [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) 程序中，每个 finger 笔划 (从触控到 release) 与一个对象相关联，该对象包含呈现用该手指绘制的线条所需的所有信息。 程序出于此目的定义了一个小型 `FingerPaintPolyline` 类：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

每个折线都有一种颜色、描边宽度和一个 iOS 图形 [`CGPath`](xref:CoreGraphics.CGPath) 对象，可在绘制时聚合并渲染行的多个点。

下面显示的所有代码剩余部分都包含在 `UIView` 名为的导数中 `FingerPaintCanvasView` 。 在 `FingerPaintPolyline` 当前由一个或多个手指绘制时，该类将维护类型为的对象的字典：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典允许视图根据 `FingerPaintPolyline` 对象的属性快速获取与每个手指关联的信息 `Handle` `UITouch` 。

`FingerPaintCanvasView`类还 `List` 会为已完成的折线维护一个对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此中的对象的 `List` 顺序与它们的绘制顺序相同。

`FingerPaintCanvasView` 覆盖定义的五个方法 `View` ：

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

各种 `Touches` 重写累计构成折线的点。

[ `Draw` ] 重写绘制已完成的折线，然后绘制正在进行的折线：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

每个 `Touches` 替代都可能会报告多个手指的操作，这些操作由一个或多个 `UITouch` 存储在方法的参数中的对象指示 `touches` 。 `TouchesBegan`重写循环遍历这些对象。 对于每个 `UITouch` 对象，方法创建并初始化一个新的 `FingerPaintPolyline` 对象，包括存储从方法获取的指针的初始位置 `LocationInView` 。 `FingerPaintPolyline` `InProgressPolylines` 使用 `Handle` 对象的属性作为字典键，将此对象添加到字典中 `UITouch` ：

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

方法通过调用 `SetNeedsDisplay` 生成对 `Draw` 重写的调用并更新屏幕来结束。

手指或手指在屏幕上移动时，将 `View` 获取对其重写的多次调用 `TouchesMoved` 。 此重写类似于循环遍历 `UITouch` 参数中存储的对象 `touches` ，并将手指的当前位置添加到图形路径：

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

该 `touches` 集合仅包含 `UITouch` 自上次调用或以来已移动的手指的那些对象 `TouchesBegan` `TouchesMoved` 。 如果你需要 `UITouch` 与屏幕当前联系的 *所有* 手指对应的对象，则可通过方法的参数的属性获取该信息 `AllTouches` `UIEvent` 。

`TouchesEnded`重写有两个作业。 它必须向图形路径添加最后一个点，并将该 `FingerPaintPolyline` 对象从字典传输 `inProgressPolylines` 到列表中 `completedPolylines` ：

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

`TouchesCancelled`只需放弃字典中的对象即可处理此重写 `FingerPaintPolyline` ：

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

同时，此处理允许 [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) 程序跟踪单个手指并在屏幕上绘制结果：

[![跟踪单个手指并在屏幕上绘制结果](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

现在，你已了解如何在屏幕上跟踪各个手指并区分它们。

## <a name="related-links"></a>相关链接

- [等效的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (示例) ](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)