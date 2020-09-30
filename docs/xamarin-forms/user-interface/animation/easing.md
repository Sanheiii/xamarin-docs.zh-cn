---
title: 缓动函数 Xamarin.Forms
description: Xamarin.Forms 包含一个缓动类，可用于指定一个传输函数，该函数控制动画在运行时的速度如何提高或减速。 本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1769a1eb562d4ae0551598493afa9b04491a42d5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562790"
---
# <a name="easing-functions-in-no-locxamarinforms"></a>缓动函数 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms 包含一个缓动类，可用于指定一个传输函数，该函数控制动画在运行时的速度如何提高或减速。本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。_

[`Easing`](xref:Xamarin.Forms.Easing)类定义了可由动画使用的多个缓动函数：

- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)缓动函数开始时，会弹跳动画。
- [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)缓动函数在末尾弹跳动画。
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)缓动函数会慢慢加快动画的速度。
- [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)缓动函数在开始时加快动画的速度，并减速动画的结尾。
- [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)缓动函数快速减速动画。
- [`Linear`](xref:Xamarin.Forms.Easing.Linear)缓动函数使用恒定速度，并且是默认缓动函数。
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)缓动函数能平稳地加速动画。
- [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)缓动函数会在一开始就平稳地加速动画，并平稳地减速动画结束。
- [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)缓动函数会平稳地减速动画。
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)缓动函数使动画的速度非常快。
- [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)缓动函数使动画快速减速。

`In`和 `Out` 后缀指示缓动函数所提供的效果在动画的开头、结尾还是同时非常明显。

此外，还可以创建自定义缓动函数。 有关详细信息，请参阅 [自定义缓动函数](#custom-easing-functions)。

## <a name="consuming-an-easing-function"></a>使用缓动函数

类中的动画扩展方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 允许将缓动函数指定为最后一个方法参数，如以下代码示例所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

通过指定动画的缓动函数，动画速度变为非线性，并产生缓动函数所提供的效果。 在创建动画时省略缓动函数会使动画使用默认 [`Linear`](xref:Xamarin.Forms.Easing.Linear) 缓动函数，这将产生线性速度。

有关在类中使用动画扩展方法的详细信息 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) ，请参阅 [简单动画](~/xamarin-forms/user-interface/animation/simple.md)。 类也可以使用缓动函数 [`Animation`](xref:Xamarin.Forms.Animation) 。 有关详细信息，请参阅 [自定义动画](~/xamarin-forms/user-interface/animation/custom.md)。

## <a name="custom-easing-functions"></a>自定义缓动函数

有三种主要方法可用于创建自定义缓动函数：

1. 创建一个方法，该方法采用 `double` 参数并返回 `double` 结果。
1. 创建 `Func<double, double>`。
1. 将缓动函数指定为 [`Easing`](xref:Xamarin.Forms.Easing) 构造函数的参数。

在所有三种情况下，自定义缓动函数应为0的参数返回0，对于参数1，则返回1。 但是，可以在参数值0和1之间返回任何值。 现在，每种方法都将依次讨论。

### <a name="custom-easing-method"></a>自定义缓动方法

自定义缓动函数可定义为方法，该方法采用 `double` 参数并返回 `double` 结果，如以下代码示例所示：

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

`CustomEase`方法将传入值截断到值0、0.2、0.4、0.6、0.8 和1。 因此， [`Image`](xref:Xamarin.Forms.Image) 实例在离散跳转中进行转换，而不是平滑。

### <a name="custom-easing-func"></a>自定义缓动函数

自定义缓动函数也可以定义为 `Func<double, double>` ，如下面的代码示例所示：

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

`CustomEaseFunc`表示一个缓动函数，该函数可快速启动、减缓并反转课程，然后再次反转课程以快速加速。 因此，虽然实例的总体移动 [`Image`](xref:Xamarin.Forms.Image) 朝下，但它也会暂时反转动画的一半。

### <a name="custom-easing-constructor"></a>自定义缓动构造函数

自定义缓动函数也可定义为构造函数的参数 [`Easing`](xref:Xamarin.Forms.Easing) ，如以下代码示例所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自定义缓动函数指定为构造函数的 lambda 函数参数 [`Easing`](xref:Xamarin.Forms.Easing) ，并使用 `Math.Cos` 方法来创建由方法阻碍的慢速删除效果 `Math.Exp` 。 因此， [`Image`](xref:Xamarin.Forms.Image) 实例将被转换，以使其显示在其最终的静止位置。

## <a name="summary"></a>总结

本文演示了如何使用预定义的缓动函数，以及如何创建自定义缓动函数。 Xamarin.Forms 包含一个 [`Easing`](xref:Xamarin.Forms.Easing) 类，它允许您指定一个传输函数，该函数控制动画在运行时的速度如何提高或减速。

## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [缓动函数 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [减轻](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)