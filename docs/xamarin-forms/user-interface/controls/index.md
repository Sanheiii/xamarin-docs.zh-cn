---
title: 控件引用
description: 用于构造应用程序的所有用户界面元素的说明 Xamarin.Forms 。 本文列出了构成应用程序的用户界面的控件组 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 60c58ee17f68a12e3e51170f143adc9dc204e275
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562764"
---
# <a name="controls-reference"></a>控件引用

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

应用程序的用户界面 Xamarin.Forms 是构造的对象，这些对象映射到每个目标平台的本机控件。 这使得适用于 iOS、Android 和通用 Windows 平台的特定于平台的应用程序使用 Xamarin.Forms [.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md)中包含的代码。

用于创建应用程序的用户界面的四个主要控件组 Xamarin.Forms 如下所示：

- [**Pages**](pages.md)
- [**布局**](layouts.md)
- [**视图**](views.md)
- [**单元**](cells.md)

Xamarin.Forms页面通常占据整个屏幕。 页面通常包含一个布局，其中包含视图和其他布局。 单元是用于与和连接的专用 [`TableView`](xref:Xamarin.Forms.TableView) 组件 [`ListView`](xref:Xamarin.Forms.ListView) 。 类图显示了通常用于在中生成用户界面的类型的层次结构， Xamarin.Forms 可在[ Xamarin.Forms Controls 类层次结构](~/xamarin-forms/internals/class-hierarchy.md)找到。

在有关 [**页面**](pages.md)、 [**布局**](layouts.md)、 [**视图**](views.md)和 [**单元格**](cells.md)的四篇文章中，介绍了每种类型的控件，并提供了指向其 API 文档的链接、说明其用法的文章 (如果存在) ，以及一个或多个示例程序 (如果存在) 。 每种类型的控件还附带一个屏幕截图，其中显示了在 iOS 和 Android 设备上运行的 [**FormsGallery**](/samples/xamarin/xamarin-forms-samples/formsgallery) 示例页面。 在每个屏幕截图下面都链接到 c # 页的源代码、等效的 XAML 页，以及在适当) XAML 页的 c # 代码隐藏文件时 (。

> [!NOTE]
> 页面、布局和视图派生自 `VisualElement` 类。 `VisualElement`类提供各种可用于派生类的属性、方法和事件。 有关详细信息，请参阅 [VisualElement 属性、方法和事件](common-properties.md)。

除了提供的控件外 Xamarin.Forms ，第三方控件也可用。 有关详细信息，请参阅 [第三方控件](thirdparty.md)。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Controls 类层次结构](~/xamarin-forms/internals/class-hierarchy.md)
- [API 文档](/dotnet/api/xamarin.forms?view=xamarin-forms)