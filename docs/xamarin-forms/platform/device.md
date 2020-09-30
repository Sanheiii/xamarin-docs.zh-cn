---
title: Xamarin.Forms 设备类
description: 本文介绍如何使用 Xamarin.Forms Device 类，以便对每个平台的功能和布局进行精细的控制。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8eff115e894f77aeacff0f6c072bfd338fa19844
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560502"
---
# <a name="no-locxamarinforms-device-class"></a>Xamarin.Forms 设备类

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[`Device`](xref:Xamarin.Forms.Device)类包含多个属性和方法，可帮助开发人员基于每个平台自定义布局和功能。

除了使用特定硬件类型和大小的代码的方法和属性以外，类还 `Device` 包括可用于与后台线程中的 UI 控件交互的方法。 有关详细信息，请参阅 [从后台线程与 UI 交互](#interact-with-the-ui-from-background-threads)。

## <a name="provide-platform-specific-values"></a>提供特定于平台的值

在 Xamarin.Forms 2.3.4 之前，可以通过检查 [`Device.OS`](xref:Xamarin.Forms.Device.OS) 属性，并将其与 [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS) 、 [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) 、 [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone) 和 [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 枚举值进行比较来获取运行应用程序的平台。 同样，其中一个 [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 重载可用于向控件提供平台特定的值。

但是，因为 Xamarin.Forms 2.3.4 已弃用并替换这些 api。 [`Device`](xref:Xamarin.Forms.Device)该类现在包含用于标识平台的公共字符串常量- [`Device.iOS`](xref:Xamarin.Forms.Device.iOS) 、 [`Device.Android`](xref:Xamarin.Forms.Device.Android) 、 `Device.WinPhone` (不推荐使用的) 、 `Device.WinRT` (弃用) 、 [`Device.UWP`](xref:Xamarin.Forms.Device.UWP) 和 [`Device.macOS`](xref:Xamarin.Forms.Device.macOS) 。 同样，  [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 重载已替换为 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 和 [`On`](xref:Xamarin.Forms.On) api。

在 c # 中，可以通过在 `switch` 属性上创建一个语句 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) ，然后 `case` 为所需的平台提供语句，来提供特定于平台的值：

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和 [`On`](xref:Xamarin.Forms.On) 类在 XAML 中提供了相同的功能：

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用与目标类型匹配的属性对其进行实例化 `x:TypeArguments` 。 在 [`On`](xref:Xamarin.Forms.On) 类中， [`Platform`](xref:Xamarin.Forms.On.Platform) 特性可以接受单个 `string` 值或多个以逗号分隔的 `string` 值。

> [!IMPORTANT]
> 在类中提供不正确的 `Platform` 属性值 `On` 不会导致错误。 相反，代码将在不应用特定于平台的值的情况下执行。

此外，还 `OnPlatform` 可以在 XAML 中使用标记扩展来根据每个平台自定义 UI 外观。 有关详细信息，请参阅 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="deviceidiom"></a>Device。

`Device.Idiom`属性可用于根据应用程序运行的设备更改布局或功能。 [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)枚举包含以下值：

- **手机** – IPhone、iPod Touch 和 Android 设备的范围低于600
- **Tablet** -IPad、Windows 设备和 Android 设备，宽于600
- **桌面** –仅在 Windows 10 台式计算机上的 [UWP 应用](~/xamarin-forms/platform/windows/installation/index.md) 中返回 (在 `Phone` 移动 Windows 设备上返回，包括在 Continuum 方案中) 
- **电视** – TIZEN 的电视设备
- **手表** – Tizen Watch 设备
- **不支持** –未使用

*^ dip 不一定是物理像素计数*

`Idiom`属性对于构建利用较大屏幕的布局特别有用，如下所示：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)类在 XAML 中提供了相同的功能：

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

[`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用与目标类型匹配的属性对其进行实例化 `x:TypeArguments` 。

此外，还 `OnIdiom` 可以在 XAML 中使用标记扩展，根据应用程序运行的设备的用法来自定义 UI 外观。 有关详细信息，请参阅 [OnIdiom 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom-markup-extension)。

## <a name="deviceflowdirection"></a>System.windows.flowdirection>

[`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)值检索 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 表示设备当前使用的流动方向的枚举值。 流方向是指页面 UI 元素的浏览方向。 枚举值为：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中， [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 可以使用标记扩展来检索值 `x:Static` ：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

C # 中的等效代码是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

有关流方向的详细信息，请参阅 [从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>设备样式

[ `Styles` 属性](~/xamarin-forms/user-interface/styles/index.md)包含可应用于某些控件的 (如) 属性的内置样式定义 `Label` `Style` 。 可用样式包括：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>GetNamedSize

`GetNamedSize` 可 [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) 在 c # 代码中设置时使用：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Device.GetNamedColor

Xamarin.Forms 4.6 引入了对命名颜色的支持。 命名颜色是一种颜色，它具有不同的值，具体取决于哪个系统模式 (例如，设备上的亮或暗) 处于活动状态。 在 Android 上，可以通过 [R. Color](https://developer.android.com/reference/android/R.color#constants_2) 类访问已命名的颜色。 在 iOS 上，命名颜色称为 [系统颜色](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors)。 在通用 Windows 平台上，名为的颜色称为 [XAML 主题资源](/windows/uwp/design/controls-and-patterns/xaml-theme-resources)。

`GetNamedColor`方法可用于检索 Android、iOS 和 UWP 上的命名颜色。 方法采用 `string` 参数并返回 [`Color`](xref:Xamarin.Forms.Color) ：

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default` 当找不到颜色名称或 `GetNamedColor` 在不受支持的平台上调用时，将返回。

> [!NOTE]
> 由于 `GetNamedColor` 方法返回 `Color` 特定于平台的，因此通常应将其与属性结合使用 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) 。

`NamedPlatformColor`类包含定义适用于 Android、iOS 和 UWP 的命名颜色的常数：

| Android | iOS | macOS | UWP |
| --- | --- | --- | --- |
| `BackgroundDark` | `Label` | `AlternateSelectedControlTextColor` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `ControlAccent` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `ControlBackgroundColor` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `ControlColor` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `DisabledControlTextColor` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `FindHighlightColor` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `GridColor` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `HeaderTextColor` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `HighlightColor` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `KeyboardFocusIndicatorColor` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `Label` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `LabelColor` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `Link` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `LinkColor` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `PlaceholderText` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `PlaceholderTextColor` | `SystemChromeDisabledHighColor` |
| `White` | `SystemOrange` | `QuaternaryLabel`| `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemPink` | `QuaternaryLabelColor` | `SystemChromeHighColor` |
| | `SystemPurple` | `SecondaryLabel` | `SystemChromeLowColor` |
| | `SystemRed` | `SecondaryLabelColor` | `SystemChromeMediumColor` |
| | `SystemTeal` | `SelectedContentBackgroundColor` | `SystemChromeMediumLowColor` |
| | `SystemYellow` | `SelectedControlColor` | `SystemChromeWhiteColor` |
| | `TertiaryLabel` | `SelectedControlTextColor` | `SystemListLowColor` |
| | | `SelectedMenuItemTextColor` | `SystemListMediumColor`|
| | | `SelectedTextBackgroundColor` | |
| | | `SelectedTextColor` | |
| | | `Separator` | |
| | | `SeparatorColor` | |
| | | `ShadowColor` | |
| | | `SystemBlue` | |
| | | `SystemGray` | |
| | | `SystemGreen` | |
| | | `SystemIndigo` | |
| | | `SystemOrange` | |
| | | `SystemPink` | |
| | | `SystemPurple` | |
| | | `SystemRed` | |
| | | `SystemTeal` | |
| | | `SystemYellow` | |
| | | `TertiaryLabel` | |
| | | `TertiaryLabelColor` | |
| | | `TextBackgroundColor` | |
| | | `TextColor` | |
| | | `UnderPageBackgroundColor` | |
| | | `UnemphasizedSelectedContentBackgroundColor` | |
| | | `UnemphasizedSelectedTextBackgroundColor` | |
| | | `UnemphasizedSelectedTextColor` | |
| | | `WindowBackgroundColor` | |
| | | `WindowFrameTextColor` | |

## <a name="devicestarttimer"></a>StartTimer

`Device`类还提供了一 `StartTimer` 种方法，该方法提供了一种简单的方法来触发在 Xamarin.Forms 常见代码（包括 .NET Standard 库）中运行的依赖于时间的任务。 传递 `TimeSpan` 以设置间隔并返回， `true` 使计时器保持运行状态，或在 `false` 当前调用后停止计时器。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果计时器内的代码与用户界面交互 (例如，设置的文本 `Label` 或显示警报) 应在表达式内完成此操作 `BeginInvokeOnMainThread` (参见下面的) 。

> [!NOTE]
> `System.Timers.Timer`和 `System.Threading.Timer` 类是使用方法的 .NET Standard 替代项 `Device.StartTimer` 。

## <a name="interact-with-the-ui-from-background-threads"></a>从后台线程与 UI 交互

大多数操作系统（包括 iOS、Android 和通用 Windows 平台）都将单线程模型用于涉及用户界面的代码。 此线程通常称为 " *主线程* " 或 " *UI 线程*"。 此模型的结果是，所有访问用户界面元素的代码都必须在应用程序的主线程上运行。

应用程序有时会使用后台线程来执行可能长时间运行的操作，例如从 web 服务检索数据。 如果后台线程上运行的代码需要访问用户界面元素，则必须在主线程上运行该代码。

此 `Device` 类包括以下 `static` 方法，可用于与背景线程中的用户界面元素进行交互：

| 方法 | 自变量 | 返回 | 目标 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | `Action`在主线程上调用，而不会等待它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主线程上调用 `Func<T>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主线程上调用 `Action`，并等待其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主线程上调用 `Func<Task<T>>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主线程上调用 `Func<Task>`，并等待其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 返回主线程的 `SynchronizationContext`。 |

下面的代码显示使用方法的示例 `BeginInvokeOnMainThread` ：

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>相关链接

- [设备示例](/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [样式示例](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [设备 API](xref:Xamarin.Forms.Device)