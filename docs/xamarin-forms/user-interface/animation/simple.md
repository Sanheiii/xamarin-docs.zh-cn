---
title: 简单动画 Xamarin.Forms
description: ViewExtensions 类提供了可用于构建简单动画的扩展方法。 本文演示如何使用 ViewExtensions 类创建和取消动画。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2336a4fbd92a7e9158d332de1ea2e2b018d144ce
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556654"
---
# <a name="simple-animations-in-no-locxamarinforms"></a>简单动画 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_ViewExtensions 类提供了可用于构建简单动画的扩展方法。本文演示如何使用 ViewExtensions 类创建和取消动画。_

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类提供以下扩展方法，这些方法可用于创建简单动画：

- [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.exception、 Xamarin.Forms system.object。缓动) # A3 对的和属性进行动画处理 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 对的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleXTo` 对的 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleYTo` 对的 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的属性应用动画增量增加或减少 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的属性应用动画增量增加或减少 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateYTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 对的 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 属性进行动画处理 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

默认情况下，每个动画将需要250毫秒。 但是，可以在创建动画时指定每个动画的持续时间。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类还包括 [ `CancelAnimations` ] (x： Xamarin.Forms 。ViewExtensions. CancelAnimations (Xamarin.Forms 。VisualElement) # A3 方法，可用于取消任何动画。

> [!NOTE]
> [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)类提供 [ `LayoutTo` ] (x： Xamarin.Forms 。ViewExtensions. LayoutTo (Xamarin.Forms 。VisualElement、 Xamarin.Forms 。Rectangle， Xamarin.Forms system.object。缓动) # A3 扩展方法。 但是，此方法旨在供布局用来对包含大小和位置更改的布局状态之间的转换进行动画处理。 因此，它应仅由子类使用 [`Layout`](xref:Xamarin.Forms.Layout) 。

类中的动画扩展方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 都是异步的，并返回一个 `Task<bool>` 对象。 `false`如果动画完成，则返回值为; `true` 如果取消动画，则返回值。 因此，通常应将动画方法与运算符一起使用，这样就可以 `await` 轻松确定动画的完成时间。 此外，它还可以创建具有在上一方法完成后执行的后续动画方法的连续动画。 有关详细信息，请参阅 [复合动画](#compound-animations)。

如果需要让动画在后台完成，则 `await` 可以省略运算符。 在这种情况下，动画扩展方法将在启动动画后快速返回，动画将在后台发生。 创建复合动画时，可以利用此操作。 有关详细信息，请参阅 [复合动画](#composite-animations)。

有关运算符的详细信息 `await` ，请参阅 [异步支持概述](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>单个动画

中的每个扩展方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 实现一个动画操作，该操作在一段时间内从一个值逐渐更改一个值到另一个值。 本部分将探讨每个动画操作。

### <a name="rotation"></a>旋转

下面的代码示例演示如何使用 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法，以对的属性进行动画处理 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此代码 [`Image`](xref:Xamarin.Forms.Image) 通过在2秒内最多旋转360度 (2000 毫秒) 来对实例进行动画处理。 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法获取 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 动画起始处的当前属性值，然后将该值从该值旋转到其第一个参数 (360) 。 动画完成后，图像的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 属性将重置为0。 这可确保在 `Rotation` 动画结束后属性不会保持为360，这会阻止增加旋转。

以下屏幕截图显示每个平台上正在进行的旋转：

![旋转动画](simple-images/rotateto.png)

> [!NOTE]
> 除了 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法，还有 [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A7 和 [ `RotateYTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A11 方法，分别对和属性进行动画处理 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 。

### <a name="relative-rotation"></a>相对旋转

下面的代码示例演示如何使用 [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法来增量增加或减少的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 属性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelRotateTo (360, 2000);
```

此代码 [`Image`](xref:Xamarin.Forms.Image) 通过在2秒 (2000 毫秒) 的起始位置旋转360度来对实例进行动画处理。 [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法获取 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 动画起始处的当前属性值，然后将该值从该值加上其第一个参数 (360) 。 这可确保每个动画都将始终从起始位置旋转360度。 因此，如果在动画已正在进行时调用了新动画，则会从当前位置开始，并可能以不是360度增量的位置结束。

以下屏幕截图显示每个平台上正在进行的相对旋转：

![相对旋转动画](simple-images/relrotateto.png)

### <a name="scaling"></a>扩展

下面的代码示例演示如何使用 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法对的属性进行动画处理 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.ScaleTo (2, 2000);
```

此代码通过将 [`Image`](xref:Xamarin.Forms.Image) 实例的大小向上扩展到2秒 (2000 毫秒) 来对实例进行动画处理。 此 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法获取 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 动画开始 (默认值为 1) 的当前属性值，然后将该值从该值缩放 (2) 的第一个参数。 这可以将图像的大小扩展到大小的两倍。

下面的屏幕截图显示每个平台上正在进行的缩放：

![缩放动画](simple-images/scaleto.png)

> [!NOTE]
> 除了方法之外，还有一些 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) `ScaleXTo` 方法， `ScaleYTo` 分别用于对和属性进行动画处理 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 。

### <a name="relative-scaling"></a>相对缩放

下面的代码示例演示如何使用 [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法，以对的属性进行动画处理 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelScaleTo (2, 2000);
```

此代码通过将 [`Image`](xref:Xamarin.Forms.Image) 实例的大小向上扩展到2秒 (2000 毫秒) 来对实例进行动画处理。 [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法获取 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 动画起始处的当前属性值，然后将该值从该值加上其第一个参数 (2) 。 这可确保每个动画将始终从起始位置缩放2。

### <a name="scaling-and-rotation-with-anchors"></a>通过定位点进行缩放和旋转

[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 属性设置和属性的缩放或旋转中心 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 。 因此，它们的值还会影响 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 和 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法。

如果已将 [`Image`](xref:Xamarin.Forms.Image) 放置到布局的中心，下面的代码示例演示了如何通过设置其属性来围绕布局中心旋转图像 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ：

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

若要 [`Image`](xref:Xamarin.Forms.Image) 围绕布局中心旋转实例，则 [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 属性必须设置为相对于宽度和高度的值 `Image` 。 在此示例中，的中心 `Image` 定义为位于布局的中心，因此，0.5 的默认 `AnchorX` 值不需要更改。 但是，将 `AnchorY` 属性重新定义为从顶部 `Image` 到布局中心点的值。 这可确保在 `Image` 布局的中心点围绕中心点生成360度的完整旋转，如以下屏幕截图所示：

![带有锚点的旋转动画](simple-images/rotate-anchors.png)

### <a name="translation"></a>翻译

下面的代码示例演示如何使用 [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.exception、 Xamarin.Forms system.object。缓动) # A3 方法，以对的和属性进行动画处理 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此代码 [`Image`](xref:Xamarin.Forms.Image) 通过将其沿水平和垂直方向转换， (1000 毫秒) ，对实例进行动画处理。 [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.exception、 Xamarin.Forms system.object。缓动) # A3 方法同时将100像素的图像向上转换为左、100像素。 这是因为第一个和第二个参数均为负数。 提供正数会将图像向右和向下转换。

以下屏幕截图显示每个平台上正在进行的转换：

![翻译动画](simple-images/translateto.png)

> [!NOTE]
> 如果某个元素最初在屏幕上进行布局，然后在屏幕上转换，则在转换后，该元素的输入布局将保留在屏幕之外，并且用户无法与之交互。 因此，建议视图应在其最终位置布局，然后执行所需的任何翻译。

### <a name="fading"></a>淡入淡出

下面的代码示例演示如何使用 [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法，以对的属性进行动画处理 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此代码 [`Image`](xref:Xamarin.Forms.Image) 通过在超过4秒 (4000 毫秒) 对实例进行动画处理。 [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法获取 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 动画起始处的当前属性值，然后将该值从该值淡入第一个参数 (1) 。

以下屏幕截图显示每个平台上的淡化：

![褪色动画](simple-images/fadeto.png)

## <a name="compound-animations"></a>复合动画

复合动画是动画的顺序组合，可使用运算符创建， `await` 如以下代码示例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此示例中， [`Image`](xref:Xamarin.Forms.Image) 转换超过6秒 (6000 毫秒) 。 的转换 `Image` 使用5个动画， `await` 运算符指示每个动画按顺序执行。 因此，在上一个方法完成后，后续的动画方法会执行。

## <a name="composite-animations"></a>复合动画

复合动画是同时运行两个或多个动画的动画的组合。 可以通过混合等待和非等待的动画来创建复合动画，如以下代码示例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此示例中， [`Image`](xref:Xamarin.Forms.Image) 将缩放并同时旋转 (4000 毫秒) 。 的缩放将 `Image` 使用两个连续的动画，这两个动画同时作为旋转的同时出现。 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法无需 `await` 运算符即可执行，并立即返回第一个 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 动画，然后开始。 `await`第一 `ScaleTo` 种方法调用的运算符将延迟第二个 `ScaleTo` 方法调用，直到第一个 `ScaleTo` 方法调用完成。 此时， `RotateTo` 动画完成了半路， `Image` 将旋转180度。 在最后2秒内 (2000 毫秒) ，两个 `ScaleTo` 动画和 `RotateTo` 动画都完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>并发运行多个异步方法

`static` `Task.WhenAny` 和 `Task.WhenAll` 方法用于并发运行多个异步方法，因此可用于创建复合动画。 这两个方法都返回一个 `Task` 对象，并接受每个返回对象的方法的集合 `Task` 。 `Task.WhenAny`方法在其集合中的任何方法完成执行时完成，如下面的代码示例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此示例中， `Task.WhenAny` 方法调用包含两个任务。 第一个任务将此图像旋转 (4000 毫秒) ，第二个任务在2秒内将图像缩放 (2000 毫秒) 。 当第二个任务完成时， `Task.WhenAny` 方法调用完成。 不过，即使 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3 方法仍在运行，第二种 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法可以开始。

此 `Task.WhenAll` 方法在其集合中的所有方法都已完成时完成，如下面的代码示例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此示例中， `Task.WhenAll` 方法调用包含三个任务，每个任务的执行时间超过10分钟。 每个 `Task` 为 [ `RotateTo` ] (x：增加了不同数量的360度循环–307旋转 Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A3、251旋转 [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A7 和199旋转 [ `RotateYTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement、system.string、 Xamarin.Forms system.object。缓动) # A11。 这些值是质数，因此确保旋转不会同步，因此不会导致重复的模式。

以下屏幕截图显示每个平台上正在进行的多个旋转：

![复合动画](simple-images/multiple-rotations.png)

## <a name="canceling-animations"></a>取消动画

应用程序可以通过调用 `static` [ `ViewExtensions.CancelAnimations` ] (x：来取消一个或多个动画 Xamarin.Forms 。ViewExtensions. CancelAnimations (Xamarin.Forms 。VisualElement) # A3 方法，如以下代码示例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

这会立即取消当前正在实例上运行的所有动画 [`Image`](xref:Xamarin.Forms.Image) 。

## <a name="summary"></a>总结

本文演示了如何使用类创建和取消动画 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 此类提供扩展方法，这些方法可用于构造旋转、缩放、平移和淡化实例的简单动画 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [基本动画 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)