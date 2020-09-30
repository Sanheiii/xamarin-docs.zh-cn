---
title: 颜色 Xamarin.Forms
description: Xamarin.Forms 提供灵活的跨平台颜色类。 本文介绍了 Color 类提供的功能，以及如何使用它。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b4e44cb667335ecca7a0c7e5ec77d6e14ed975c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557226"
---
# <a name="colors-in-no-locxamarinforms"></a>颜色 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms 提供灵活的跨平台颜色类。_

本文介绍 [`Color`](xref:Xamarin.Forms.Color) 可在中使用类的各种方法 Xamarin.Forms 。

[`Color`](xref:Xamarin.Forms.Color)类提供多种方法来生成 `Color` 实例：

- **命名颜色** -常见命名颜色的集合，包括 `Red` 、 `Green` 和 `Blue` 。
- `FromHex` -与 HTML 中使用的语法类似的字符串值，例如 "00FF00"。 可以选择将 Alpha 指定为 ( "CC00FF00" ) 的第一对字符。
- `FromHsla` -色调、饱和度和发光度 `double` 值，其 alpha 值 (0.0-1.0) 。
- `FromHsv` -色调、饱和度和值 `int` `double` 。
- `FromHsva` -色调、饱和度和值 `int` `double` 。
- `FromRgb` -红色、绿色和蓝色 `int` 值 (0-255) 。
- `FromRgba` -红色、绿色、蓝色和 alpha  `int` 值 (0-255) 。
- `FromUint` -设置 `double` 表示 **argb**的单个值。

下面是一些示例颜色，分配给某些标签的，其中 `BackgroundColor` 使用了允许语法的不同变体：

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

以下各平台显示了这些颜色。 请注意，最终颜色 `Accent` 是适用于 iOS 和 Android 的路子颜色; 此值由定义 Xamarin.Forms 。

 [![颜色演示](colors-images/colors-sml.png "颜色演示")](colors-images/colors.png#lightbox "颜色演示")

## <a name="colordefault"></a>Color。默认值

使用 `Default` 设置 (或重新设置) 颜色值返回到平台默认值 (了解这对于每个属性) 在每个平台上表示不同的基础颜色。

开发人员可以使用此值来设置 `Color` 属性，但 **不** 应在此实例的组件 RGB 值中查询 (它们都设置为-1) 。

## <a name="colortransparent"></a>Color。透明

将颜色设置为 "清除"。

## <a name="coloraccent"></a>颜色。着色

在 iOS 和 Android 上，此实例设置为在默认背景上可见但不同于默认文本颜色的对比颜色。

## <a name="additional-methods"></a>其他方法

[`Color`](xref:Xamarin.Forms.Color) 实例包括以下附加方法：

- `AddLuminosity` -通过用 `Color` 提供的增量修改发光度来返回。
- `MultiplyAlpha` - `Color` 通过修改 alpha 返回，并将其乘以提供的 alpha 值。
- `ToHex` -返回的十六进制 `string` 表示形式 `Color` 。
- `WithHue` -返回 `Color` ，将色相替换为提供的值。
- `WithLuminosity` -返回 `Color` ，将发光度替换为提供的值。
- `WithSaturation` -返回 `Color` ，将饱和度替换为提供的值。

## <a name="implicit-conversions"></a>隐式转换

`Xamarin.Forms.Color`可以执行与类型之间的隐式转换 `System.Drawing.Color` ：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>RuntimePlatform

此代码片段使用 `Device.RuntimePlatform` 属性有选择地设置的颜色 `ActivityIndicator` ：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>从 XAML 使用

使用定义的颜色名称或此处显示的十六进制表示形式，还可以在 XAML 中引用颜色：

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> 使用 XAML 编译时，颜色名称不区分大小写，因此可以用小写形式编写。 有关 XAML 编译的详细信息，请参阅 [XAML 编译](~/xamarin-forms/xaml/xamlc.md)。

## <a name="related-links"></a>相关链接

- [ColorsSample](/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [可绑定选取器 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)