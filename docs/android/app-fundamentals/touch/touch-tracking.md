---
title: Xamarin 中的多点触控 Finger 跟踪
description: 本主题演示如何从多个手指跟踪触摸事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8c2e06d2700cd7b61c16fc993d807ca4d042a063
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455113"
---
# <a name="multi-touch-finger-tracking"></a>多点触控 Finger 跟踪

_本主题演示如何从多个手指跟踪触摸事件_

有时，多点触控应用程序需要在屏幕上同时移动时跟踪各个手指。 一个典型的应用程序就是一个手指画图程序。 您希望用户能够用一个手指绘图，还可以一次使用多个手指进行绘制。 当程序处理多个触控事件时，它需要区分每个手指对应的事件。 Android 为此目的提供 ID 代码，但获取和处理这些代码可能有点棘手。

对于与特定手指关联的所有事件，ID 代码保持不变。 当手指首次触摸屏幕时，将分配 ID 代码，并在手指从屏幕上提起后变为无效。
这些 ID 代码通常是非常小的整数，Android 重用它们以用于以后的触控事件。

几乎始终会有一次跟踪单个手指的程序维护用于触摸跟踪的字典。 字典键是标识特定手指的 ID 代码。 字典值依赖于应用程序。 在 [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) 程序中，每个 finger 笔划 (从触控到 release) 与一个对象相关联，该对象包含呈现用该手指绘制的线条所需的所有信息。 程序出于此目的定义了一个小型 `FingerPaintPolyline` 类：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

每个折线都有一种颜色、描边宽度和一个 Android 图形 [`Path`](xref:Android.Graphics.Path) 对象，可在绘制时聚合并渲染行的多个点。

下面显示的代码的其余部分包含在 `View` 名为的导数中 `FingerPaintCanvasView` 。 在 `FingerPaintPolyline` 当前由一个或多个手指绘制时，该类将维护类型为的对象的字典：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典允许视图快速获取 `FingerPaintPolyline` 与特定手指关联的信息。

`FingerPaintCanvasView`类还 `List` 会为已完成的折线维护一个对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此中的对象的 `List` 顺序与它们的绘制顺序相同。

`FingerPaintCanvasView` 重写由定义的两种方法 `View` ： [`OnDraw`](xref:Android.Views.View.OnDraw*)
和 [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在其 `OnDraw` 重写中，视图绘制已完成的折线，然后绘制正在进行的折线。

方法的重写 `OnTouchEvent` 通过 `pointerIndex` 从属性获取值开始 `ActionIndex` 。 此 `ActionIndex` 值在多个手指之间区分，但在多个事件之间并不一致。 出于此原因，请使用 `pointerIndex` `id` 从方法获取指针值 `GetPointerId` 。 此 ID *在* 多个事件之间保持一致：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

请注意，重写使用 `ActionMasked` 语句中的属性 `switch` 而不是 `Action` 属性。 原因如下：

当处理多点触控时， `Action` 属性的值 `MotionEventsAction.Down` 为，第一个手指触摸屏幕，然后将和的值 `Pointer2Down` `Pointer3Down` 作为第二个和第三个手指同时触摸屏幕。 由于第四个和第五个手指联系，因此 `Action` 属性具有数值，甚至不与枚举的成员相对应 `MotionEventsAction` ！ 需要检查值中的位标志的值，以解释它们的含义。

同样，由于手指与屏幕保持联系，因此该 `Action` 属性的值为， `Pointer2Up` 第 `Pointer3Up` 二个和第三个手指为 `Up` 第一个手指。

`ActionMasked`属性所采用的值数量更少，因为它应与属性结合使用 `ActionIndex` 以区分多个手指。 手指触摸屏幕时，属性只能 `MotionEventActions.Down` 与第一个手指和下一根手指相等 `PointerDown` 。 在手指离开屏幕时，将的 `ActionMasked` 值 `Pointer1Up` 显示为后面的手指和 `Up` 第一个手指的值。

使用时 `ActionMasked` ，可通过下一 `ActionIndex` 根手指来区分屏幕，但通常不需要使用该值，除非作为对象中其他方法的参数 `MotionEvent` 。 对于多点触控， `GetPointerId` 上面的代码中调用了这些方法中的最重要的一种方法。 该方法返回一个值，该值可用于字典键，以将特定事件与手指相关联。

`OnTouchEvent` [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程序中的重写 `MotionEventActions.Down` `PointerDown` 通过创建新的 `FingerPaintPolyline` 对象并将其添加到字典中来处理和事件：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

请注意， `pointerIndex` 还用于获取该指针在视图中的位置。 所有触摸信息都与值相关联 `pointerIndex` 。 `id`唯一标识跨多个消息的手指，以便用于创建字典条目。

同样， `OnTouchEvent` 重写还 `MotionEventActions.Up` `Pointer1Up` 会通过将已完成的折线传输到集合来处理和相同， `completedPolylines` 以便在重写过程中绘制它们 `OnDraw` 。 此代码还会 `id` 从字典中删除该项：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

现在为棘手部分。

在停机和向上事件之间，通常有很多 `MotionEventActions.Move` 事件。 这些将在对的单个调用中捆绑在一起 `OnTouchEvent` ，它们的处理方式必须与和事件的处理方式不同 `Down` `Up` 。 `pointerIndex`之前从属性中获取的值 `ActionIndex` 必须被忽略。 相反，该方法必须 `pointerIndex` 通过循环0与属性来获取多个值 `PointerCount` ，然后获取其中 `id` 每个 `pointerIndex` 值的：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

这种类型的处理允许 [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) 程序跟踪单个手指并在屏幕上绘制结果：

[![FingerPaint 示例中的示例屏幕截图](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

现在，你已了解如何在屏幕上跟踪各个手指并区分它们。

## <a name="related-links"></a>相关链接

- [等效的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)