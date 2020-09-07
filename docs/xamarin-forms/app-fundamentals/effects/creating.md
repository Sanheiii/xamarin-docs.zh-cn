---
title: 创建效果
description: 效果简化了控件的自定义。 本文演示如何创建效果，在指向 Entry 控件时更改其背景颜色。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb4e0c81fb3f2e58be9c44d1ec7fddcf4d72a656
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964592"
---
# <a name="creating-an-effect"></a>创建效果

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_效果简化了控件的自定义。本文演示如何创建效果，在指向 Entry 控件时更改其背景颜色。

在每个特定于平台的项目中创建效果的过程如下：

1. 创建 `PlatformEffect` 类的子类。
1. 替代 `OnAttached` 方法并写入自定义控件的逻辑。
1. 根据需要替代 `OnDetached` 方法并写入清理控件自定义的逻辑。
1. 向效果类添加 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 属性。 此属性为效果设置一个公司范围的命名空间，以避免与同名的其他效果发生冲突。 请注意，每个项目只能应用一次该属性。
1. 向效果类添加 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 属性。 该属性使用 Xamarin.Forms 所用的唯一 ID 以及组名注册效果，以便在将应用于控件之前定位该效果。 该属性接受两个参数 - 效果的类型名称和一个唯一的字符串，该字符串用于在将效果应用于控件之前定位该效果。

然后，可以通过将效果附加到相应控件来使用该效果。

> [!NOTE]
> 可选择在每个平台项目中提供效果。 尝试在未注册时使用效果将返回一个不执行任何操作的非 null 值。

示例应用程序演示了 `FocusEffect`，它在获得焦点时更改控件的背景颜色。 下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![焦点效果项目职责](creating-images/focus-effect.png)

`HomePage` 上的 [`Entry`](xref:Xamarin.Forms.Entry) 控件由特定于平台的各项目中的 `FocusEffect` 类自定义。 每个 `FocusEffect` 类均派生自各平台的 `PlatformEffect` 类。 这导致使用特定于平台的背景颜色呈现 `Entry` 控件，背景颜色在控件获得焦点时变化，如以下屏幕截图所示：

![每个平台上的焦点效应，控制聚焦](creating-images/screenshots-1.png)
![每个平台上的焦点效应，控制未聚焦](creating-images/screenshots-2.png)

## <a name="creating-the-effect-on-each-platform"></a>在各平台上创建效果

以下各部分讨论特定于平台的 `FocusEffect` 类的实现。

## <a name="ios-project"></a>iOS 项目

以下代码示例展示了 iOS 项目的 `FocusEffect` 实现：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached` 方法使用 `UIColor.FromRGB` 方法将控件的 `BackgroundColor` 属性设置为淡紫色，并将该颜色存储在字段中。 效果所附加到的控件没有 `BackgroundColor` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

`OnElementPropertyChanged` 替代响应 Xamarin.Forms 控件上的可绑定属性更改。 当 [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) 属性更改时，如果控件具有焦点，控件的 `BackgroundColor` 属性更改为白色；否则更改为淡紫色。 效果所附加到的控件没有 `BackgroundColor` 属性时，此功能包装在 `try`/`catch` 块中。

## <a name="android-project"></a>Android 项目

以下代码示例展示了 Android 项目的 `FocusEffect` 实现：

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached` 方法调用 `SetBackgroundColor` 方法将控件的背景色设置为浅绿色，并将此颜色存储在字段中。 效果所附加到的控件没有 `SetBackgroundColor` 属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

`OnElementPropertyChanged` 替代响应 Xamarin.Forms 控件上的可绑定属性更改。 当 [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) 属性更改时，如果控件具有焦点，控件的背景色更改为白色；否则更改为淡紫色。 效果所附加到的控件没有 `BackgroundColor` 属性时，此功能包装在 `try`/`catch` 块中。

## <a name="universal-windows-platform-projects"></a>通用 Windows 平台项目

下面的代码示例显示了通用 Windows 平台 (UWP) 项目的 `FocusEffect` 实现：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

`OnAttached` 方法将控件的 `Background` 属性设置为蓝绿色，将 `BackgroundFocusBrush` 属性设置为白色。 效果所附加到的控件没有这些属性时，此功能包装在 `try`/`catch` 块中。 `OnDetached` 方法不提供任何实现，因为不需要进行清理。

## <a name="consuming-the-effect"></a>使用效果

使用 Xamarin.Forms .NET Standard 库或共享库项目中的效果的过程如下：

1. 声明将由效果自定义的控件。
1. 通过将该效果添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中，将其附加到控件。

> [!NOTE]
> 效果实例只能附加到单个控件。 因此，若要在两个控件上使用，必须解析效果两次。

## <a name="consuming-the-effect-in-xaml"></a>在 XAML 中使用效果

下面的 XAML 代码示例显示了附加 `FocusEffect` 的 [`Entry`](xref:Xamarin.Forms.Entry) 控件：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

.NET Standard 库中的 `FocusEffect` 类支持在 XAML 中使用效果，如下面的代码示例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

`FocusEffect` 类将 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 类作为子类，它表示一个独立于平台的效果，该效果包装通常特定于平台的内部效果。 `FocusEffect` 类调用基类构造函数，传入由分辨率组名称（在效果类上使用 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 属性指定）以及唯一 ID（在效果类上使用 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 属性指定）的串联组成的参数。 因此，在运行时初始化 [`Entry`](xref:Xamarin.Forms.Entry) 时，向控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合添加了 `MyCompany.FocusEffect` 的新实例。

还可以使用行为或附加属性将效果附加到控件。 有关使用行为将效果附加到控件的详细信息，请参阅[可重用的 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/effect-behavior.md)。 有关使用附加属性将效果附加到控件的详细信息，请参阅[将参数传递给效果](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>在 C&num; 中使用效果

下面的代码示例介绍了 C# 中的等效 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

将效果添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合，即可将 `FocusEffect` 附加到 `Entry` 实例，如以下代码示例所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 返回指定名称的 [`Effect`](xref:Xamarin.Forms.Effect)，该名称是分辨率组名称（在效果类上使用 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 属性指定）以及唯一 ID（在效果类上使用 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 属性指定）的串联。 如果平台不提供此效果，则 `Effect.Resolve` 方法将返回一个非 `null` 值。

## <a name="summary"></a>总结

本文演示如何创建效果，在指向 [`Entry`](xref:Xamarin.Forms.Entry) 控件时更改其背景颜色。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [效果](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [背景颜色效果（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect)
- [焦点效果（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)
