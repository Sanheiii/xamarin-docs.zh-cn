---
title: Xamarin.Forms XAML 基础知识
description: 本指南介绍了如何开始对移动设备进行跨平台 XAML。 XAML 允许开发人员 Xamarin.Forms 使用标记而不是代码来定义应用程序中的用户界面。
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3d2620014c9554712c746b98de3b9a0eeaa20864
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562751"
---
# <a name="no-locxamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基础知识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

XAML)  (的可扩展应用程序标记语言是一种基于 XML 的语言，由 Microsoft 创建，作为编程代码的替代方法，用于实例化和初始化对象，以及在父子层次结构中组织这些对象。 XAML 已改编到 .NET framework 中的几种技术，但它在定义 Windows Presentation Foundation (WPF) 、Silverlight、Windows 运行时和通用 Windows 平台 UWP (中的用户界面的布局时找到了其最大的实用程序。

XAML 允许开发人员 Xamarin.Forms 使用标记而不是代码来定义应用程序中的用户界面。 程序中从不需要 XAML Xamarin.Forms ，但通常情况下，它比等效的代码更简洁、更直观，并且可能 toolable。 XAML 非常适合用于常见 MVVM (ViewModel) 应用程序体系结构： XAML 定义通过基于 XAML 的数据绑定链接到 ViewModel 代码的视图。

在 XAML 文件中， Xamarin.Forms 开发人员可以使用所有 Xamarin.Forms 视图、布局和页面以及自定义类来定义用户界面。 XAML 文件可以编译或嵌入可执行文件中。 无论采用哪种方式，都将在生成时分析 XAML 信息以定位命名对象，并再次在运行时对对象进行实例化和初始化，并在这些对象和编程代码之间建立链接。

XAML 与等效代码相比具有多个优点：

- XAML 通常比等效代码更简洁、更易读。
- XML 中的固有父子层次结构允许 XAML 以更好的视觉清晰度模拟用户界面对象的父子层次结构。
- XAML 可以轻松地由程序员编写，还可以 toolable 并由视觉对象设计工具生成。

还有一些缺点，主要与标记语言固有的限制相关：

- XAML 不能包含代码。 所有事件处理程序都必须在代码文件中定义。
- XAML 不能包含重复处理的循环。 但是， (多个 Xamarin.Forms 可视化对象（最值得注意  [`ListView`](xref:Xamarin.Forms.ListView) 的是）可以根据其集合中的对象生成多个子对象 `ItemsSource` 。 ) 
- XAML 不能包含条件处理 (但是，数据绑定可以引用基于代码的绑定转换器，该转换器有效地允许一些条件处理。 ) 
- XAML 通常不能实例化未定义无参数构造函数的类。  (不过，有时这种限制会有一种方法。 ) 
- XAML 通常不能调用方法。 再次 (，有时可能会克服此限制。 ) 

尚未提供用于在应用程序中生成 XAML 的可视化设计器 Xamarin.Forms 。 所有 XAML 都必须手动编写，但有一个 [Xaml 预览](~/xamarin-forms/xaml/xaml-previewer/index.md)器。 XAML 的新用户可能希望经常生成并运行应用程序，尤其是在任何可能不正确的情况下。 即使是在 XAML 中有大量经验的开发人员也知道试验是有益的。

XAML 本质上是 XML，但 XAML 具有一些独特的语法功能。 其中最重要的是：

- 属性元素
- 附加属性
- 标记扩展

这些功能 *不* 是 XML 扩展。 XAML 是完全合法的 XML。 但这些 XAML 语法功能以独特方式使用 XML。 下面的文章中对它们进行了详细介绍，最后介绍了如何使用 XAML 实现 MVVM。

## <a name="requirements"></a>要求

本文假定你熟悉 Xamarin.Forms 。 本文还假定你熟悉 XML，包括了解 XML 命名空间声明的使用以及术语 *元素*、 *标记*和 *特性*。

熟悉 Xamarin.Forms 和 XML 后，开始阅读 [第1部分。与 XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [创建移动应用书籍](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 示例](/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]