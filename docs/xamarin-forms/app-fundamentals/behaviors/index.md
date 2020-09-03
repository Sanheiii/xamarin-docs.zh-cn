---
title: Xamarin.Forms 行为
description: 通过行为可将功能添加到用户界面控件，且无需将其子类化。 行为由代码编写，并以 XAML 或代码的形式添加到控件中。
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d917d7d6421cfae7fc877c81023a835573fa99b1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964618"
---
# <a name="no-locxamarinforms-behaviors"></a>Xamarin.Forms 行为

通过行为可将功能添加到用户界面控件，且无需将其子类化。行为由代码编写，并以 XAML 或代码的形式添加到控件中。

## <a name="introduction-to-behaviors"></a>[行为简介](introduction.md)

行为使开发人员可以实现那些通常必须以代码隐藏形式编写的代码，因为它直接与控件的 API 进行交互，这样便可简洁地将其附加到控件。 本文介绍行为。

## <a name="attached-behaviors"></a>[附加行为](attached.md)

附加行为是具有一个或多个附加属性的 `static` 类。 本文演示如何创建和使用附加行为。

## <a name="no-locxamarinforms-behaviors"></a>[Xamarin.Forms 行为](creating.md)

Xamarin.Forms 行为由 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类派生创建而成。 本文演示如何创建和使用 Xamarin.Forms 行为。

## <a name="reusable-effectbehavior"></a>[可重用 EffectBehavior](effect-behavior.md)

行为是一种非常有用的方法，用于向控件添加效果、从代码隐藏文件中删除冗余重复的效果处理代码。 本文演示如何创建 Xamarin.Forms 行为并将其用于向控件添加效果。
