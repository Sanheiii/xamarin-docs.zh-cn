---
title: 文本在 Xamarin.Forms
description: Xamarin.Forms 具有三个用于处理文本的主要视图，本文介绍如何使用它们在应用程序中输入和显示文本 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1ad1d4fa9d5ce006373382e85398edac59a82a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557590"
---
# <a name="text-in-no-locxamarinforms"></a>文本在 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_使用 Xamarin.Forms 输入或显示文本。_

Xamarin.Forms 有三个主要视图用于处理文本：

- **[标签](#label)** &mdash; 用于呈现单行或多行文本。 可以在同一行中显示具有多个格式设置选项的文本。
- **[条目](#entry)** &mdash; 用于输入只是一行的文本。 条目具有密码模式。
- **[编辑器](#editor)** &mdash; 用于输入可能需要多行的文本。

可以使用内置或自定义 [样式](#styles) 更改文本外观，某些控件支持自定义 [字体](#fonts)。

## <a name="label"></a>[Label](label.md)

`Label`视图用于显示文本。 它可以显示多行文本或一行文本。 `Label` 可以使用内联中使用的多个格式设置选项呈现文本。 当标签视图无法容纳在一行上时，它可以换行或截断文本。

![标签示例](images/label.png)

有关更多详细信息，请参阅 [标签](label.md) 文章。

有关自定义标签中使用的字体的信息，请参阅 [字体](fonts.md)。

## <a name="entry"></a>[项](entry.md)

`Entry` 用于接受单行文本输入。 `Entry` 提供对颜色和字体的控制。 `Entry` 具有密码模式，并且可以在输入文本之前显示占位符文本。

![条目示例](images/entry.png)

有关详细[信息，请参阅文章。](entry.md)

请注意，与不同 `Label` ， `Entry` 不能有自定义字体设置。

## <a name="editor"></a>[编辑器](editor.md)

`Editor` 用于接受多行文本输入。 `Editor` 提供对颜色和字体的控制。

![编辑器示例](images/editor.png)

有关详细信息，请参阅 [编辑器](editor.md) 文章。

## <a name="fonts"></a>[字体](fonts.md)

许多控件都支持在每个平台上使用内置字体的不同字体设置，或应用中包含的自定义字体。 有关更多详细信息，请参阅 [字体](fonts.md) 一文。

## <a name="styles"></a>[样式](styles.md)

请参阅使用 [样式](~/xamarin-forms/user-interface/styles/index.md) 了解如何设置应用于多个控件的字体、 [颜色](~/xamarin-forms/user-interface/colors.md)和其他显示属性。

## <a name="related-links"></a>相关链接

- [文本 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)