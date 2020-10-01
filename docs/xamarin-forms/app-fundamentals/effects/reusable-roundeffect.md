---
title: Xamarin.Forms 可重用 RoundEffect
description: RoundEffect 是一种可重用的效果，可应用于任何从 VisualElement 派生的控件，以将控件呈现为圆形。
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b459945aec4ef8f1c3fa9729efd82461aec0e0
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561958"
---
# <a name="no-locxamarinforms-reusable-roundeffect"></a>Xamarin.Forms 可重用 RoundEffect

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect 简化了将从 VisualElement 派生的任何控件呈现为圆形的过程。 此效果可用于创建圆形图像、按钮和其他控件：

[![IOS 和 Android 上的 RoundEffect 屏幕截图](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>创建共享的 RoutingEffect

必须在共享项目中创建一个效果类才能创建跨平台效果。 该示例应用程序会创建一个空的 `RoundEffect` 类，该类派生自 `RoutingEffect` 类：

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

该类允许共享项目解析对代码或 XAML 中效果的引用，但不提供任何功能。 必须对每个平台实现这种效果。

## <a name="implement-the-android-effect"></a>实现 Android 效果

Android 平台项目定义了一个派生自 `PlatformEffect` 的 `RoundEffect` 类。 该类标记有允许 Xamarin.Forms 解析效果类的 `assembly` 属性：

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

Android 平台使用 `OutlineProvider` 的概念来定义控件的边缘。 该示例项目包括一个派生自 `ViewOutlineProvider` 类的 `CornerRadiusProvider` 类：

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

此类使用 Xamarin.Forms `Element` 实例的 `Width` 和 `Height` 属性来计算半径，该半径是最短尺寸的一半。

定义边框提供程序后，`RoundEffect` 类就可以使用它来实现效果：

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

当效果附加到元素时，将调用 `OnAttached` 方法。 保存现有的 `OutlineProvider` 对象，以便在分离效果时可以将其还原。 将 `CornerRadiusOutlineProvider` 的新实例用作 `OutlineProvider`，并将 `ClipToOutline` 设置为 true，以将溢出的元素剪裁到外部边框。

当从元素中删除效果并还原原始的 `OnDetatched` 值时，将调用 `OutlineProvider` 方法。

> [!NOTE]
> `Control` 属性可以为 null，也可以不为 null，具体取决于元素类型。 如果 `Control` 属性不为 null，则可以直接将圆角应用于控件。 但是，如果该属性为 null，则必须将圆角应用于 `Container` 对象。 `effectTarget` 字段允许将该效果应用于相应的对象。

## <a name="implement-the-ios-effect"></a>实现 iOS 效果

iOS 平台项目定义了一个派生自 `PlatformEffect` 的 `RoundEffect` 类。 该类标记有允许 Xamarin.Forms 解析效果类的 `assembly` 属性：

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

在 iOS 上，控件具有 `Layer` 属性，而该属性又具有 `CornerRadius` 属性。 iOS 上的 `RoundEffect` 类实现计算相应的角半径并更新层的 `CornerRadius` 属性：

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

`CalculateRadius` 方法根据 Xamarin.Forms `Element` 的最小尺寸来计算半径。 将效果附加到控件时，将调用 `OnAttached` 方法，并更新层的 `CornerRadius` 属性。 它将 `ClipToBounds` 属性设置为 `true`，以便将溢出的元素剪裁到控件的边框。 当从控件中删除效果并反转这些更改以还原原始角半径时，将调用 `OnDetatched` 方法。

> [!NOTE]
> `Control` 属性可以为 null，也可以不为 null，具体取决于元素类型。 如果 `Control` 属性不为 null，则可以直接将圆角应用于控件。 但是，如果该属性为 null，则必须将圆角应用于 `Container` 对象。 `effectTarget` 字段允许将该效果应用于相应的对象。

## <a name="consume-the-effect"></a>使用效果

跨平台实现效果后，Xamarin.Forms 控件就可以使用这种效果。 `RoundEffect` 的常见应用是使 `Image` 对象成为圆形。 以下 XAML 显示了应用于 `Image` 实例的效果：

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

该效果也可以应用于代码：

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

`RoundEffect` 类可以应用于派生自 `VisualElement` 的任何控件。

> [!NOTE]
> 对于计算正确半径的效果，所应用的控件必须能够显式调整大小。 因此，应定义 `HeightRequest` 和 `WidthRequest` 属性。 如果受影响的控件出现在 `StackLayout` 中，则其 `HorizontalOptions` 属性不应使用 `LayoutOptions.CenterAndExpand` 等 Expand 值，否则它将没有准确的尺寸。

## <a name="related-links"></a>相关链接

- [RoundEffect 示例应用程序](/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [效果简介](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)