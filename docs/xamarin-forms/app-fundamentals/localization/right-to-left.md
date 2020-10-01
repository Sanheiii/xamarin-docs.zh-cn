---
title: 从右到左本地化
description: 从右到左本地化为 Xamarin.Forms 应用程序添加了对从右到左流方向的支持。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fe27c00752723ac34d614c510f1305b197933715
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558162"
---
# <a name="right-to-left-localization"></a>从右到左本地化

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

从右到左本地化为 Xamarin.Forms 应用程序添加了对从右到左流方向的支持。

> [!NOTE]
> 从右到左本地化需要使用 iOS 9 或更高版本，或者 Android 版 API 17 或更高版本。

流方向是指页面 UI 元素的浏览方向。 某些语言（例如阿拉伯语和希伯来语）需要按从右到左流动方向对 UI 元素进行布局。 这也可以通过设置 [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性来实现。 此属性获取或设置 UI 元素在控制其布局的任何父元素中的流动方向，并且此属性应设置为一个 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 枚举值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在一个元素上将 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性设置为 [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 通常会将对齐方式设置为向右，阅读顺序设置为从右到左，控件布局设置为从右到左流动：

[![阿拉伯语的 TodoItemPage，采用从右到左的流动方向](rtl-images/TodoItemPage-Arabic.png "阿拉伯语的 TodoItemPage，采用从右到左的流动方向")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "阿拉伯语的 TodoItemPage，采用从右到左的流动方向")

> [!TIP]
> 在初始布局上，应该只设置 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。 运行时更改此值会导致影响性能的成本高昂的布局过程。

无父级的元素的默认 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性值是 [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)，有父级的元素的默认 `FlowDirection` 为 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此，元素从可视化树中的其父级处继承 `FlowDirection` 属性值，并且任何元素都可以替代该元素从其父级处获取的值。

> [!TIP]
> 本地化采用从右到左语言的应用时，设置页或根布局上的 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。 这导致页或根布局包含的所有元素对流方向作出相应响应。

## <a name="respecting-device-flow-direction"></a>遵循设备流方向

遵循基于所选语言和区域的设备流动方向是开发人员的选择，不会自动发生。 可以通过将页或根布局上的 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性设置为 `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) 值来实现：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

页或根布局的所有子元素会默认继承 [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) 值。

## <a name="platform-setup"></a>平台设置

启用从右到左的区域设置需要特定平台设置。

### <a name="ios"></a>iOS

所需的从右向左的区域设置应作为支持语言添加到 Info.plist 中的 `CFBundleLocalizations` 键的数组项。 下面的示例显示阿拉伯语已添加到 `CFBundleLocalizations` 键的数组：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Info.plist 支持的语言](rtl-images/ios-locales.png "Info.plist 支持的语言")

有关详细信息，请参阅[ iOS 中的本地化基础知识](../../../ios/app-fundamentals/localization/index.md#localization-basics-in-ios)。

然后可以通过将设备/模拟器上的语言和区域更改为 Info.plist 中指定的从右向左的区域设置，以测试从右到左的本地化。

> [!WARNING]
> 请注意，在 iOS 上将语言和区域更改为从右向左的区域设置时，如果未包括区域设置所需资源，任何 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 视图都将引发异常。 例如，测试具有 `DatePicker` 的阿拉伯语应用时，请确保选中“iOS 生成”窗格的“国际化”部分中的“中东”  。

### <a name="android"></a>Android

应更新应用的“AndroidManifest.xml”文件，使 `<uses-sdk>` 节点将 `android:minSdkVersion` 属性设置为 17，`<application>` 节点将 `android:supportsRtl` 属性设置为 `true`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

通过将设备/模拟器更改为使用从右到左的语言，或者通过启用“设置”>“开发人员选项”中的“强制执行 RTL 布局方向”，可以测试从右向左的本地化 。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

应在“Package.appxmanifest”文件的 `<Resources>` 节点中指定所需语言资源。 下面的示例显示阿拉伯语已添加到 `<Resources>` 节点中：

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

此外，UWP 要求在 .NET Standard 库中显式定义应用的默认区域性。 这可以通过将 `AssemblyInfo.cs` 或另一个类中的 `NeutralResourcesLanguage` 属性设置为默认区域性来实现：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

然后可以通过将设备上的语言和区域更改为适当的从右向左的区域设置，测试从右到左的本地化。

## <a name="limitations"></a>限制

Xamarin.Forms 从右到左本地化当前有许多限制：

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按钮位置、工具栏项的位置和转换动画都是由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 轻扫方向不翻转。
- [`Image`](xref:Xamarin.Forms.Image) 可视内容不翻转。
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) 方向由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- [`WebView`](xref:Xamarin.Forms.WebView) 内容不遵从 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。
- 需要添加 `TextDirection` 属性以控制文本对齐方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 方向由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文本对齐方式由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- 不逆转 [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 笔势和对齐方式。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 方向由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 放置由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文本对齐方式由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。
- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 子级不继承 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文本对齐方式由设备区域设置控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性控制。

## <a name="force-right-to-left-layout"></a>强制使用从右到左的布局

通过修改相应的平台项目，可以强制 Xamarin.iOS 和 Xamarin.Android 应用程序始终使用从右到左的布局，而不考虑设备设置。

### <a name="ios"></a>iOS

通过修改 AppDelegate 类，可以强制 Xamarin iOS 应用程序始终使用从右到左的布局，如下所示：

1. 将 `IntPtr_objc_msgSend` 函数声明为 `AppDelegate` 类中的第一行：

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. 从 `FinshedLaunching` 方法返回之前，从 `FinishedLaunching` 方法调用 `IntPtr_objc_msgSend` 函数：

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

如果应用程序始终需要从右到左的布局，则此方法非常有用，而且无需设置 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。

有关 `IntrPtr_objc_msgSend` 方法的详细信息，请参阅 [Xamarin.iOS 中的 Objective-C 选择器](~/ios/internals/objective-c-selectors.md)。

### <a name="android"></a>Android

要强制 Xamarin Android 应用程序始终使用从右到左的布局，可修改 MainActivity 类，使其包含以下行：

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

如果应用程序始终需要从右到左的布局，则此方法非常有用，而且无需设置 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Xamarin.University 的从右至左的语言支持

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 从右到左支持视频**

## <a name="related-links"></a>相关链接

- [TodoLocalizedRTL 示例应用](/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)