---
title: Xamarin.Forms ProgressBar
description: Xamarin.FormsProgressBar 是一个控件，它以直观的方式将进度表示为根据 float 属性填充的水平栏。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 111b3ad13902b272256464f6f70f0db3dfd015b6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559449"
---
# <a name="no-locxamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 控件将进度直观地表示为水平条，其填充方式为由值表示的百分比 `float` 。 `ProgressBar`类继承自 [`View`](xref:Xamarin.Forms.View) 。

以下屏幕截图显示 iOS 和 Android 上的 `ProgressBar`：

![IOS 和 Android 上 ProgressBar 的屏幕截图](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar`控件定义了两个属性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)`float`表示当前进度的值，该值为0到1之间的值。 `Progress` 小于0的值将被限制为0，大于1的值将限制为1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)`Color`影响表示当前进度的内部条形颜色的。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着可以对进行 `ProgressBar` 样式化，并使其成为数据绑定的目标。

该 `ProgressBar` 控件还定义了一个 `ProgressTo` 方法，该方法将从其当前值到指定值的一种动画效果。 有关详细信息，请参阅对 [ProgressBar 进行动画处理](#animate-a-progressbar)。

> [!NOTE]
> 不 `ProgressBar` 接受用户操作，因此当使用 Tab 键选择控件时，将跳过该操作。

## <a name="create-a-progressbar"></a>创建 ProgressBar

`ProgressBar`可以在 XAML 中实例化。 它的 `Progress` 属性确定内部颜色栏的填充百分比。 默认 `Progress` 属性值为0。 下面的示例演示如何 `ProgressBar` 使用可选的属性集在 XAML 中实例化 `Progress` ：

```xaml
<ProgressBar Progress="0.5" />
```

`ProgressBar`也可以在代码中创建：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 不要将不受约束的水平布局选项（如 `Center` 、或）用于 `Start` `End` `ProgressBar` 。 在 UWP 上， `ProgressBar` 折叠到宽度为零的一条。 `HorizontalOptions` `Fill` `Auto` 在布局中放置时，请保留的默认值，不要使用的宽度 `ProgressBar` `Grid` 。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外观属性

`ProgressColor`属性在属性大于零时定义内部条形颜色 `Progress` 。 下面的示例演示如何 `ProgressBar` 使用属性集在 XAML 中实例化 `ProgressColor` ：

```xaml
<ProgressBar ProgressColor="Orange" />
```

`ProgressColor`在代码中创建时，还可以设置属性 `ProgressBar` ：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

以下屏幕截图显示了在 `ProgressBar` `ProgressColor` `Color.Orange` iOS 和 Android 上的属性设置为的：

![IOS 和 Android 上的样式化 ProgressBar 的屏幕截图](progressbar-images/progressbars-styled.png "IOS 和 Android 上的 ProgressBar 样式")

## <a name="animate-a-progressbar"></a>对 ProgressBar 进行动画处理

`ProgressTo`方法将 `ProgressBar` 从其当前 `Progress` 值到提供的值在一段时间内进行动画处理。 方法接受一个 `float` 进度值（ `uint` 以毫秒为单位）， `Easing` 并返回一个 `Task<bool>` 。 下面的代码演示如何对执行动画处理 `ProgressBar` ：

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

有关枚举的详细信息 `Easing` ，请参阅[中 Xamarin.Forms 的缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相关链接

* [ProgressBar 演示](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)