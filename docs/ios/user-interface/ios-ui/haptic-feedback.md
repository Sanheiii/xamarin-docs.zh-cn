---
title: 在 Xamarin 中提供 Haptic 的反馈
description: 本文档介绍如何在 Xamarin iOS 应用程序中提供 haptic 反馈。 它讨论了 UIImpactFeedbackGenerator、UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: e6879db1631bc6b58a36142344ff1fba997ee4fb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432041"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>在 Xamarin 中提供 Haptic 的反馈

<a name="Overview"></a>

## <a name="overview"></a>概述

在 iPhone 7 和 iPhone 7 Plus 上，Apple 包含了新的 haptic 响应，这些响应提供了用于以物理方式吸引用户的其他方式。 Haptic 反馈 (通常称为 Haptics) 在用户界面设计中通过强制、vibrations 或运动) 来使用触控 (。 使用这些新的 tactile 反馈选项，使用户注意并强化其操作。

将详细介绍以下主题：

- [关于 Haptic 反馈](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback"></a>

## <a name="about-haptic-feedback"></a>关于 Haptic 反馈

几个内置 UI 元素已经提供了 haptic 的反馈，如选取器、开关和滑杆。 iOS 10 现在添加了使用类的具体子类以编程方式触发 haptics 的功能 `UIFeedbackGenerator` 。

开发人员可以使用以下 `UIFeedbackGenerator` 子类之一以编程方式触发 haptic 反馈：

- `UIImpactFeedbackGenerator` -使用此反馈生成器来补充操作或任务（例如，当视图滑入时显示 "thud"）或两个屏幕对象发生冲突。
- `UINotificationFeedbackGenerator` -将此反馈生成器用于通知，如操作完成、失败或任何其他类型的警告。
- `UISelectionFeedbackGenerator` -对于正在进行的选择（如从列表中选取一项），请使用此反馈生成器。

<a name="UIImpactFeedbackGenerator"></a>

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此反馈生成器可以对某个操作或任务（例如，当视图滑入时提供 "thud" 或两个屏幕对象发生冲突）进行补充。

使用以下代码来触发影响反馈：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

当开发人员创建类的新实例时， `UIImpactFeedbackGenerator` 它们提供了 `UIImpactFeedbackStyle` 指定反馈强度的：

- `Heavy`
- `Medium`
- `Light`

`Prepare`调用的方法 `UIImpactFeedbackGenerator` 可通知系统 haptic 反馈即将发生，以便能够最大程度地减少延迟。

然后，该 `ImpactOccurred` 方法触发 haptic 反馈。

<a name="UINotificationFeedbackGenerator"></a>

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

此反馈生成器用于通知，如操作完成、失败或任何其他类型的警告。

使用以下代码来触发通知反馈：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

创建类的新实例 `UINotificationFeedbackGenerator` ，并 `Prepare` 调用其方法来通知系统 haptic 反馈即将发生，以便能够最大程度地减少延迟。

`NotificationOccurred`调用以使用给定的触发 haptic 反馈 `UINotificationFeedbackType` ：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator"></a>

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

对于正在进行的选择（如从列表中选取一项），请使用此反馈生成器。

使用以下代码来触发选择反馈：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

创建类的新实例 `UISelectionFeedbackGenerator` ，并 `Prepare` 调用其方法来通知系统 haptic 反馈即将发生，以便能够最大程度地减少延迟。

然后，该 `SelectionChanged` 方法触发 haptic 反馈。

## <a name="summary"></a>总结

本文介绍了 iOS 10 中提供的新类型的 haptic 反馈，以及如何在 Xamarin 中实现它们。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)