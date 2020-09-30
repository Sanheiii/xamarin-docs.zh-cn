---
title: 活动指示器 Xamarin.Forms
description: ActivityIndicator 控件向用户指出，应用程序参与了长时间的活动，而不提供任何进度指示。 本文介绍如何使用 XAML 和代码中的 ActivityIndicator。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76136067f40d378a6bfa28698f15b6872bb40c93
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562337"
---
# <a name="no-locxamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 控件将显示一个动画，用于显示应用程序参与了长时间的活动。 与不同的 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 是， `ActivityIndicator` 不提供进度指示。 `ActivityIndicator`继承自 [`View`](xref:Xamarin.Forms.View) 。

以下屏幕截图显示了 `ActivityIndicator` iOS 和 Android 上的控件：

![IOS 和 Android 上 ActivityIndicator 的屏幕截图](activityindicator-images/activityindicators-default.png "IOS 和 Android 上 ActivityIndicator 的屏幕截图")

`ActivityIndicator`控件定义以下属性：

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) 定义的 `Color` 显示颜色的值 `ActivityIndicator` 。
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 一个 `bool` 值，该值指示是否 `ActivityIndicator` 应显示、动画显示或隐藏。 如果值为 `false` ，则 `ActivityIndicator` 不可见。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着可以对进行 `ActivityIndicator` 样式化，并使其成为数据绑定的目标。

## <a name="create-an-activityindicator"></a>创建 ActivityIndicator

`ActivityIndicator`可以在 XAML 中实例化类。 其 `IsRunning` 属性确定控件是否可见并进行动画处理。 `IsRunning`属性默认为 `false` 。 下面的示例演示如何 `ActivityIndicator` 使用可选的属性集在 XAML 中实例化 `IsRunning` ：

```xaml
<ActivityIndicator IsRunning="true" />
```

`ActivityIndicator`也可以在代码中创建：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外观属性

`Color`属性定义 `ActivityIndicator` 颜色。 下面的示例演示如何 `ActivityIndicator` 使用属性集在 XAML 中实例化 `Color` ：

```xaml
<ActivityIndicator Color="Orange" />
```

`Color`在代码中创建时，还可以设置属性 `ActivityIndicator` ：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

以下屏幕截图显示了在 `ActivityIndicator` `Color` `Color.Orange` iOS 和 Android 上的属性设置为的：

![IOS 和 Android 上的样式化 ActivityIndicator 的屏幕截图](activityindicator-images/activityindicators-styled.png "IOS 和 Android 上的样式化 ActivityIndicator 的屏幕截图")

## <a name="related-links"></a>相关链接

* [ActivityIndicator 演示](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)