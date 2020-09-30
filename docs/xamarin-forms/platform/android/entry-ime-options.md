---
title: Android 上的条目输入法编辑器选项
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，它为输入的软键盘设置输入法编辑器选项。
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 308b0aa56afdbdbd0ac1e918eefd72b1edb4f338
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563817"
---
# <a name="entry-input-method-editor-options-on-android"></a>Android 上的条目输入法编辑器选项

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定用于设置的输入法编辑器 (用于的软键盘) 选项 [`Entry`](xref:Xamarin.Forms.Entry) 。 这包括设置软键盘右下角的 "用户操作" 按钮，以及与的交互 `Entry` 。 它通过将 [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) 附加属性设置为枚举的值，在 XAML 中使用 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `Entry.SetImeOptions` ] (x： Xamarin.Forms 。PlatformConfiguration Xamarin.Forms . SetImeOptions (。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry}， Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. ImeFlags) # A3 方法（位于 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空间中）用于为软键盘设置输入方法操作选项 [`Entry`](xref:Xamarin.Forms.Entry) ， [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 枚举提供以下值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) –指示无需特定操作键，并且基础控件将自行生成。 此为 `Next` 或 `Done` 。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) -指示将不会提供操作密钥。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) –指示操作密钥将执行 "执行" 操作，使用户进入其所键入文本的目标。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) –指示操作键执行 "搜索" 操作，使用户能够搜索其所键入的文本。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) –指示操作密钥将执行 "发送" 操作，并将文本传递到其目标。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) –指示操作键将执行 "下一步" 操作，并将用户带到将接受文本的下一个字段。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) –指示操作键将执行 "完成" 操作，并关闭软键盘。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) –指示操作键将执行 "上一步" 操作，并将用户带到将接受文本的上一个字段。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) –用于选择操作选项的掩码。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) –指示拼写检查器不会从用户学习，也不会根据用户以前键入的内容来建议更正。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) –指示 UI 不应转为全屏。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) -指示不会为提取的文本显示任何 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) -指示不会为自定义操作显示任何 UI。

结果是，将指定的 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 值应用于的软键盘 [`Entry`](xref:Xamarin.Forms.Entry) ，这将设置输入法编辑器选项：

[![条目输入法编辑器特定于平台](entry-ime-options-images/entry-imeoptions.png "条目输入法编辑器特定于平台")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "条目输入法编辑器特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)