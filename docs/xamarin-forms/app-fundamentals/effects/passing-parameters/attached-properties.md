---
title: 将效果参数作为附加属性传递
description: 附加属性可用于定义响应运行时属性更改的效果参数。 本文演示如何使用附加属性将参数传递给效果，以及如何在运行时更改参数。
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c014396f9670ece93295c2c8da1738f9dc7dae9e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555978"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>将效果参数作为附加属性传递

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_附加属性可用于定义响应运行时属性更改的效果参数。本文演示如何使用附加属性将参数传递给效果，以及如何在运行时更改参数。_

创建响应运行时属性更改的效果参数的过程如下：

1. 创建 `static` 类，其中包含针对要传递给效果的每个参数的附加属性。
1. 向该类添加额外的附加属性，用于控制该类将附加到的控件的效果的添加或删除。 确保此附加属性注册 `propertyChanged` 委托，该委托将在属性值更改时执行。
1. 为每个附加属性创建 `static` getter 和 setter。
1. 在 `propertyChanged` 委托中实现逻辑，以便添加和删除效果。
1. 在 `static` 类中实现嵌套类，该嵌套类以效果命名并且是 `RoutingEffect` 类的子类。 对于构造函数，调用基类构造函数，传入解析组名称以及每个特定于平台的效果类上指定的唯一 ID 的串联。

随后可通过将附加属性和属性值添加到适当的控件来将参数传递给效果。 此外，可以通过指定新的附加属性值，在运行时更改参数。

> [!NOTE]
> 附加属性是一类特殊的可绑定属性，它在一个类中定义，但附加到其他对象，并且在 XAML 中可识别为包含以句点分隔的类和属性名称的特性。 有关详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

示例应用程序展示了向 [`Label`](xref:Xamarin.Forms.Label) 控件显示的文本添加阴影的 `ShadowEffect`。 此外，可在运行时更改阴影的颜色。 下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![阴影效果项目职责](attached-properties-images/shadow-effect.png)

`HomePage` 上的 [`Label`](xref:Xamarin.Forms.Label) 控件由特定于平台的各项目中的 `LabelShadowEffect` 自定义。 参数通过 `ShadowEffect` 类中的附加属性传递给每个 `LabelShadowEffect`。 每个 `LabelShadowEffect` 类均派生自各平台的 `PlatformEffect` 类。 这就使阴影被添加到 `Label` 控件显示的文本中，如以下屏幕截图所示：

![每个平台上的阴影效果](attached-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>创建效果参数

应创建 `static` 类来表示效果参数，如以下代码示例所示：

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

`ShadowEffect` 包含五个附加属性，以及每个附加属性的 `static` getter 和 setter。 其中四个属性表示要传递给每个特定于平台的 `LabelShadowEffect` 的参数。 `ShadowEffect` 类还定义了 `HasShadow` 附加属性，用于控制 `ShadowEffect` 类附加到的控件的效果的添加或删除。 该附加属性注册属性值更改时执行的 `OnHasShadowChanged` 方法。 此方法根据 `HasShadow` 附加属性的值添加或删除效果。

嵌套 `LabelShadowEffect` 类是 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 类的子类，支持添加和删除效果。 `RoutingEffect` 类表示独立于平台的效果，该效果包装通常特定于平台的内部效果。 这简化了效果删除过程，因为对于特定于平台的效果，没有对类型信息的编译时访问。 `LabelShadowEffect` 构造函数调用基类构造函数，传入由解析组名称以及每个特定于平台的效果类上指定的唯一 ID 的串联组成的参数。 这样可以在 `OnHasShadowChanged` 方法中添加和删除效果，如下所示：

- **效果添加**：将 `LabelShadowEffect` 的新实例添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中。 这取代了使用 [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 方法来添加效果。
- **效果删除**：检索并删除控件 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中 `LabelShadowEffect` 的第一个实例。

## <a name="consuming-the-effect"></a>使用效果

可通过向 [`Label`](xref:Xamarin.Forms.Label) 控件添加附加属性来使用每个特定于平台的 `LabelShadowEffect`，如以下 XAML 代码示例所示：

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

将 `ShadowEffect.HasShadow` 附加属性设置为 `true` 会执行向 [`Label`](xref:Xamarin.Forms.Label) 控件添加或删除 `LabelShadowEffect` 的 `ShadowEffect.OnHasShadowChanged` 方法。 在两个代码示例中，`ShadowEffect.Color` 附加属性提供特定于平台的颜色值。 有关详细信息，请参阅 [Device 类](~/xamarin-forms/platform/device.md)。

此外，[`Button`](xref:Xamarin.Forms.Button) 允许在运行时更改阴影颜色。 单击 `Button` 时，以下代码通过设置 `ShadowEffect.Color` 附加属性来更改阴影颜色：

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>通过样式使用效果

可通过向控件添加附加属性进行使用的效果还可以被样式使用。 以下 XAML 代码示例展示了阴影效果的*显式*样式，该样式可应用于 [`Label`](xref:Xamarin.Forms.Label) 控件：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) 可通过使用 `StaticResource` 标记扩展将 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 属性设置为 `Style` 实例来应用于 [`Label`](xref:Xamarin.Forms.Label)，如以下代码示例所示：

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.ShadowRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

`OnAttached` 方法调用使用 `ShadowEffect` getter 检索附加属性值的方法，以及将`Control.Layer` 属性设置为用于创建阴影的属性值的方法。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任何 `ShadowEffect` 附加属性值在运行时更改，则效果需要通过显示更改来进行响应。 在特定于平台的效果类中，`OnElementPropertyChanged` 方法的替代版本是响应可绑定属性更改的位置，如以下代码示例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法更新阴影的半径、颜色或偏移，前提是相应的 `ShadowEffect` 附加属性值已更改。 应始终检查已更改的属性，因为可多次调用此替代。

### <a name="android-project"></a>Android 项目

以下代码示例展示了 Android 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

`OnAttached` 方法调用使用 `ShadowEffect` getter 检索附加属性值的方法，并调用对使用属性值创建阴影的 [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) 方法进行调用的方法。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任何 `ShadowEffect` 附加属性值在运行时更改，则效果需要通过显示更改来进行响应。 在特定于平台的效果类中，`OnElementPropertyChanged` 方法的替代版本是响应可绑定属性更改的位置，如以下代码示例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法更新阴影的半径、颜色或偏移，前提是相应的 `ShadowEffect` 附加属性值已更改。 应始终检查已更改的属性，因为可多次调用此替代。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台项目

以下代码示例展示了通用 Windows 平台 (UWP) 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

通用 Windows 平台不提供阴影效果，因此，两个平台上的 `LabelShadowEffect` 实现通过在主 `Label` 后面添加第二个偏移 [`Label`](xref:Xamarin.Forms.Label) 来进行模拟。 `OnAttached` 方法创建新的 `Label` 并在 `Label` 上设置一些布局属性。 它随后调用使用 `ShadowEffect` getter 检索附加属性值的方法，并通过设置用于控制 `Label` 的颜色和位置的 [`TextColor`](xref:Xamarin.Forms.Label.TextColor)、[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 属性来创建阴影。 `shadowLabel` 随后插入主 `Label` 后面并偏移。 效果所附加到的控件没有 `Control.Layer` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任何 `ShadowEffect` 附加属性值在运行时更改，则效果需要通过显示更改来进行响应。 在特定于平台的效果类中，`OnElementPropertyChanged` 方法的替代版本是响应可绑定属性更改的位置，如以下代码示例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法更新阴影的颜色或偏移，前提是相应的 `ShadowEffect` 附加属性值已更改。 应始终检查已更改的属性，因为可多次调用此替代。

## <a name="summary"></a>总结

本文演示了如何使用附加属性将参数传递给效果，以及如何在运行时更改参数。 附加属性可用于定义响应运行时属性更改的效果参数。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [效果](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [阴影效果（示例）](/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)