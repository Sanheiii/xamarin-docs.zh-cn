---
title: Xamarin.Forms 数据绑定
description: 数据绑定将两个对象的属性链接起来，如此，对某一属性的更改将自动反映在另一个属性中。 数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构必不可少的一部分。
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 88b2bd5a0fe6720a4e14e6327715690bfd74a185
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556797"
---
# <a name="no-locxamarinforms-data-binding"></a>Xamarin.Forms 数据绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

数据绑定将两个对象的属性链接起来，如此，对某一属性的更改将自动反映在另一个属性中。__ 数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构必不可少的一部分。

## <a name="the-data-linking-problem"></a>数据链接问题

Xamarin.Forms 应用程序由一个或多个页面组成，每个页面通常包含多个名为“视图”的用户界面对象**。 该程序的一项首要任务是确保这些视图保持同步，并跟踪它们所表示的各种值或选项。 通常，视图表示来自基础数据源的值，用户可对这些视图进行处理以更改该数据。 当视图发生更改时，基础数据必会反映该更改；同样，当基础数据发生更改时，该更改必将反映在视图中。

要成功执行此作业，程序必须知晓这些视图或基础数据的更改。 常用的解决方案是定义一个事件，在发生更改时发出信号。 随后将安装事件处理程序，就这些更改进行通知。 它通过将数据从一个对象传输到另一个对象来响应。 但是，如果存在多个视图，就必须使用多个事件处理程序并将涉及大量代码。

## <a name="the-data-binding-solution"></a>数据绑定解决方案

数据绑定自动执行此作业，无需使用事件处理程序。 可以通过代码或 XAML 实现数据绑定，但更常采用 XAML，这有助于缩小代码隐藏文件的大小。 通过使用声明性代码或标记替换事件处理程序中的过程代码，可简化应用程序，使其清晰明了。

数据绑定涉及两个对象，其中一个几乎总是从 `View` 派生的元素，并构成页面可视界面的一部分。 另一个对象则是：

- 派生自另一个 `View`，且通常位于同一页面。
- 或是代码文件中的对象。

在演示程序中（如 [**DataBindingDemos**](/samples/xamarin/xamarin-forms-samples/databindingdemos) 示例中的程序），为了清楚简便起见，通常会显示两个 `View` 派生对象之间的数据绑定。 但是，相同的原则也可应用于 `View` 和其他对象之间的数据绑定。 使用模型-视图-视图模型 (MVVM) 架构生成应用程序时，具有基础数据的类通常称为 viewmodel。

以下系列文章探讨了数据绑定：

## <a name="basic-bindings"></a>[基本绑定](basic-bindings.md)

了解数据绑定目标和源之间的区别，并查看以代码和 XAML 创建的简单数据绑定。

## <a name="binding-mode"></a>[绑定模式](binding-mode.md)

了解绑定模式如何控制两个对象之间的数据流。

## <a name="string-formatting"></a>[字符串格式设置](string-formatting.md)

使用数据绑定格式化对象并将其显示为字符串。

## <a name="binding-path"></a>[绑定路径](binding-path.md)

深入了解数据绑定的 `Path` 属性以访问子属性和集合成员。

## <a name="binding-value-converters"></a>[绑定值转换器](converters.md)

使用绑定值转换器来更改数据绑定中的值。

## <a name="relative-bindings"></a>[相对绑定](relative-bindings.md)

使用相对绑定可设置相对于绑定目标位置的绑定源。

## <a name="binding-fallbacks"></a>[绑定回退](binding-fallbacks.md)

通过定义绑定过程失败时要使用的回退值，使数据绑定更加可靠。

## <a name="multi-bindings"></a>[多绑定](multibinding.md)

将 [`Binding`](xref:Xamarin.Forms.Binding) 对象集合附加到单个绑定目标属性。

## <a name="the-command-interface"></a>[命令界面](commanding.md)

使用数据绑定实现 `Command` 属性。

## <a name="compiled-bindings"></a>[编译的绑定](compiled-bindings.md)

使用已编译的绑定来提高数据绑定性能。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书籍中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)