---
title: 将效果参数作为公共语言运行时属性传递
description: 公共语言运行时 (CLR) 属性可用于定义不响应运行时属性更改的效果参数。 本文演示如何使用 CLR 属性将参数传递给效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 13527bd8da1322df15bde45bcc8162f62a1f556a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555913"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>将效果参数作为公共语言运行时属性传递

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

公共语言运行时 (CLR) 属性可用于定义不响应运行时属性更改的效果参数。本文演示如何使用 CLR 属性将参数传递给效果。

创建不响应运行时属性更改的效果参数的过程如下：

1. 创建一个 `public` 类，它将 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 类作为子类。 `RoutingEffect` 类表示独立于平台的效果，该效果包装通常特定于平台的内部效果。
1. 创建一个调用基类构造函数的构造函数，传递解析组名称以及每个特定于平台的效果类上指定的唯一 ID 的串联。
1. 为要传递给效果的每个参数向类添加属性。

在实例化效果时，为每个属性指定值可将参数传递给效果。

示例应用程序展示了向 [`Label`](xref:Xamarin.Forms.Label) 控件显示的文本添加阴影的 `ShadowEffect`。 下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![阴影效果项目职责](clr-properties-images/shadow-effect.png)

`HomePage` 上的 [`Label`](xref:Xamarin.Forms.Label) 控件由特定于平台的各项目中的 `LabelShadowEffect` 自定义。 参数通过 `ShadowEffect` 类中的属性传递给每个 `LabelShadowEffect`。 每个 `LabelShadowEffect` 类均派生自各平台的 `PlatformEffect` 类。 这就使阴影被添加到 `Label` 控件显示的文本中，如以下屏幕截图所示：

![每个平台上的阴影效果](clr-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>创建效果参数

应创建将 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 类作为子类的 `public` 类，用于表示效果参数，如以下代码示例所示：

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

`ShadowEffect` 包含四个属性，它们表示要传递给每个特定于平台的 `LabelShadowEffect` 的参数。 类构造函数调用基类构造函数，传入由解析组名称以及每个特定于平台的效果类上指定的唯一 ID 的串联组成的参数。 因此，在实例化 `ShadowEffect` 时，将向控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合添加 `MyCompany.LabelShadowEffect` 的新实例。

## <a name="consuming-the-effect"></a>使用效果

下面的 XAML 代码示例演示附加了 `ShadowEffect` 的 [`Label`](xref:Xamarin.Forms.Label) 控件：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

下面的代码示例介绍了 C# 中的等效 [`Label`](xref:Xamarin.Forms.Label)：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

在这两个代码示例中，先通过为每个属性指定值来实例化 `ShadowEffect` 类的实例，然后再将其添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 请注意，`ShadowEffect.Color` 属性使用特定于平台的颜色值。 有关详细信息，请参阅 [Device 类](~/xamarin-forms/platform/device.md)。

## <a name="creating-the-effect-on-each-platform"></a>在各平台上创建效果

以下各部分讨论特定于平台的 `LabelShadowEffect` 类的实现。

### <a name="ios-project"></a>iOS 项目

以下代码示例展示了 iOS 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.ShadowRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached` 方法检索 `ShadowEffect` 实例，并将 `Control.Layer` 属性设置为指定的属性值以创建阴影。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

### <a name="android-project"></a>Android 项目

以下代码示例展示了 Android 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached` 方法检索 `ShadowEffect` 实例，并调用 [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) 方法使用指定的属性值创建阴影。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台项目

以下代码示例展示了通用 Windows 平台 (UWP) 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

通用 Windows 平台不提供阴影效果，因此，两个平台上的 `LabelShadowEffect` 实现通过在主 `Label` 后面添加第二个偏移 [`Label`](xref:Xamarin.Forms.Label) 来进行模拟。 `OnAttached` 方法检索 `ShadowEffect` 实例，创建新的 `Label`，并在 `Label` 上设置一些布局属性。 然后通过设置 [`TextColor`](xref:Xamarin.Forms.Label.TextColor)、[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 属性来控制 `Label` 的颜色和位置，从而创建阴影。 `shadowLabel` 随后插入主 `Label` 后面并偏移。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

## <a name="summary"></a>总结

本文演示如何使用 CLR 属性将参数传递给效果。 CLR 属性可用于定义不响应运行时属性更改的效果参数。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [效果](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [阴影效果（示例）](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)