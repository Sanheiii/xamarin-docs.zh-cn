---
title: Xamarin.Forms 数据模板
description: 数据模板用于指定受支持控件上的数据外观，并且通常会绑定到要显示的数据。
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 459b918869bd3e2b5c1927d3fbcded6035226a58
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562582"
---
# <a name="no-locxamarinforms-data-templates"></a>Xamarin.Forms 数据模板

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

数据模板用于指定受支持控件上的数据外观，并且通常会绑定到要显示的数据。

## <a name="introduction"></a>[介绍](introduction.md)

Xamarin.Forms 数据模板让你可以在支持的控件上定义数据表示形式。 本文介绍了数据模板，并且分析了它们必不可少的原因。

## <a name="creating-a-datatemplate"></a>[创建数据模板](creating.md)

可通过内联方式创建数据模板，也可在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中或通过自定义类型或适当的 Xamarin.Forms 单元类型进行创建。 如果不需要在其他地方重复使用数据模板，则应使用内联模板。 或者，可将数据模板定义为自定义类型或控件级别、页面级别或应用程序级别资源，从而重复使用数据模板。

## <a name="creating-a-datatemplateselector"></a>[创建 DataTemplateSelector](selector.md)

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 可用于在运行时根据数据绑定属性的值来选择 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 如此可将多个 `DataTemplate` 实例应用于同一类型的对象，以自定义特定对象的外观。 本文演示如何创建和使用 `DataTemplateSelector`。

## <a name="related-links"></a>相关链接

- [数据模板（示例）](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)