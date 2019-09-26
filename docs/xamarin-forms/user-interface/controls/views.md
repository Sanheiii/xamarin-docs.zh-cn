---
title: Xamarin.Forms 视图
description: Xamarin.Forms 视图是跨平台移动用户界面的构建基块。 本文列出了在 Xamarin.Forms 中包含的视图。
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 0094fbc73e88dc4e84d8bf415db30c17f955ddcc
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69976607"
---
# <a name="xamarinforms-views"></a>Xamarin.Forms 视图

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Forms 视图是跨平台移动用户界面的构建基块。_

视图是用户界面对象，如标签、 按钮和滑块，通常称为分别*控件*或*小组件*其他图形的编程环境中。 支持所有派生自 Xamarin.Forms 的视图[ `View` ](xref:Xamarin.Forms.View)类。 它们可以划分为几个类别：

## <a name="views-for-presentation"></a>演示文稿的视图

### <a name="label"></a>Label

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) 显示单行文本字符串或多行文本，使用常量或变量格式设置块。 设置[ `Text` ](xref:Xamarin.Forms.Label.Text)属性设置为常量的格式设置，或一组字符串[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)属性设置为[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)变量的对象格式设置。<br /><br />[API 文档](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![标签示例](views-images/Label.png "标签示例")](views-images/Label-Large.png#lightbox "标签示例")<br /> [此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>图像

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) 显示位图。 可以通过作为资源嵌入常见的项目或平台项目中或使用.NET 创建的 Web 下载位图`Stream`对象。<br /><br />[API 文档](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![图像示例](views-images/Image.png "图像示例")](views-images/Image-Large.png#lightbox "图像示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>字数

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) 显示实心矩形通过着色[ `Color` ](xref:Xamarin.Forms.BoxView.Color)属性。 `BoxView` 有 40 x 40 个的默认大小请求。 其他大小，将分配[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)并[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)属性。<br /><br />[API 文档](xref:Xamarin.Forms.BoxView) / [指南](~/xamarin-forms/user-interface/boxview.md) / [示例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)， [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)， [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)， [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)， [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)，和[6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![BoxView 示例](views-images/BoxView.png "BoxView 示例")](views-images/BoxView-Large.png#lightbox "BoxView 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) 显示网页或 HTML 内容，根据是否[ `Source` ](xref:Xamarin.Forms.WebView.Source)属性设置为[ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource)或者[ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource)对象。<br /><br />[API 文档](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) / [示例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Web 视图示例](views-images/WebView.png "Web 视图示例")](views-images/WebView-Large.png#lightbox "Web 视图示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) 将 OpenGL 图形显示的 iOS 和 Android 项目中。 没有适用于通用 Windows 平台支持。 IOS 和 Android 项目需要引用**OpenTK 1.0**程序集或**OpenTK**版本 1.0.0.0 的程序集。 `OpenGLView` 更轻松地在共享项目; 中使用如果使用.NET Standard 库中，然后一个依赖关系服务还需要 （如示例代码中所示）。<br /><br />这是内置于 xamarin 中的唯一图形功能，但 xamarin 应用程序还可以使用[`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)、或[`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md)呈现图形。<br /><br />[API 文档](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![OpenGLView 示例](views-images/OpenGLView.png "OpenGLView 示例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>映射

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) 显示了一个映射。 **Xamarin.Forms.Maps**必须安装 Nuget 包。 Android 和通用 Windows 平台需要映射授权密钥。<br /><br />[API 文档](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![地图示例](views-images/Map.png "地图示例")](views-images/Map-Large.png#lightbox "地图示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>启动命令的视图

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) 是一个矩形对象，显示的文本，并会激发[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)事件已按下时。<br /><br />[API 文档](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![按钮示例](views-images/Button.png "按钮示例")](views-images/Button-Large.png#lightbox "按钮示例")<br /> [此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` 将矩形对象图中，该显示，并且这会触发`Clicked`事件已按下时。<br /><br /> [指南](~/xamarin-forms/user-interface/imagebutton.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![ImageButton 示例](views-images/ImageButton.png "ImageButton 示例")](views-images/ImageButton-Large.png#lightbox "ImageButton 示例")<br /> [此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) 显示一个区域供用户键入文本字符串，和一个按钮 （或键盘键），用于通知应用程序执行的搜索。 [ `Text` ](xref:Xamarin.Forms.SearchBar.Text)属性提供对文本、 访问和[ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)事件指示按下按钮。<br /><br />[API 文档](xref:Xamarin.Forms.SearchBar) / [指南](~/xamarin-forms/user-interface/searchbar.md) / [示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SearchBar) | [![SearchBar 示例](views-images/SearchBar.png "SearchBar 示例")](views-images/SearchBar-Large.png#lightbox "SearchBar 示例")<br /> [此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>对于设置值的视图

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`允许用户使用可以选中或空的按钮类型选择布尔值。 属性是的状态`CheckBox`， `CheckedChanged`在状态更改时激发事件。 `IsChecked`<br /><br />API 文档/[指南](~/xamarin-forms/user-interface/checkbox.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![复选框示例](views-images/CheckBox.png "复选框示例")](views-images/CheckBox-Large.png#lightbox "复选框示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) |
|     |     |

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) 允许用户选择`double`连续范围与指定值[ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum)并[ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum)属性。<br /><br />[API 文档](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![滑块示例](views-images/Slider.png "滑块示例")](views-images/Slider-Large.png#lightbox "滑块示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>分档器

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) 允许用户选择`double`通过一系列的增量值使用指定的值[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)， [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)，并且[ `Increment` ](xref:Xamarin.Forms.Stepper.Increment)属性。<br /><br />[API 文档](xref:Xamarin.Forms.Stepper)  / [指南](~/xamarin-forms/user-interface/stepper.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![分档器示例](views-images/Stepper.png "分档器示例")](views-images/Stepper-Large.png#lightbox "分档器示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>开关

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) 采用打开/关闭开关以允许用户选择一个布尔值的形式。 [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled)属性为 state 的开关，和[ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled)状态更改时触发事件。<br /><br />[API 文档](xref:Xamarin.Forms.Switch) / [指南](~/xamarin-forms/user-interface/switch.md) / [示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos) | [![Switch 示例](views-images/Switch.png "Switch 示例")](views-images/Switch-Large.png#lightbox "Switch 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) 允许用户与平台日期选取器选择一个日期。 设置与允许的日期范围[ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate)并[ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate)属性。 [ `Date` ](xref:Xamarin.Forms.DatePicker.Date)属性是所选的日期，和[ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected)该属性发生更改时触发事件。<br /><br />[API 文档](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![DatePicker 示例](views-images/DatePicker.png "DatePicker 示例")](views-images/DatePicker-Large.png#lightbox "DatePicker 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) 允许用户与平台时间选取器选择的时间。 [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)属性是所选的时间。 应用程序可以监视变化`Time`通过安装的处理程序的属性[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件。<br /><br />[API 文档](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![TimePicker 示例](views-images/TimePicker.png "TimePicker 示例")](views-images/TimePicker-Large.png#lightbox "TimePicker 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>编辑文本的视图

这两个类派生自[ `InputView` ](xref:Xamarin.Forms.InputView)类，该类定义[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)属性。

<a name="entry" />

### <a name="entry"></a>条目

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) 允许用户输入和编辑单个文本行。 该文本是可用作[ `Text` ](xref:Xamarin.Forms.Entry.Text)属性，和[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)并[ `Completed` ](xref:Xamarin.Forms.Entry.Completed)的事件触发时的文本更改或用户通过点击 enter 键，向发出完成信号。<br /><br />使用[ `Editor` ](#editor)用于输入和编辑多行文本。<br /><br />[API 文档](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![条目示例](views-images/Entry.png "条目示例")](views-images/Entry-Large.png#lightbox "条目示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>编辑器

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) 允许用户输入和编辑多行文本。 该文本是可用作[ `Text` ](xref:Xamarin.Forms.Editor.Text)属性，和[ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged)并[ `Completed` ](xref:Xamarin.Forms.Editor.Completed)的事件触发时的文本更改或用户完成向发出信号。<br /><br />使用[ `Entry` ](#entry)输入和编辑单个文本行的视图。<br /><br />[API 文档](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![条目示例](views-images/Editor.png "编辑器示例")](views-images/Editor-Large.png#lightbox "编辑器示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>若要指示活动的视图

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 使用动画来显示该应用程序所从事的耗时较长的活动而不授予任何可能的进度。 [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning)属性控制动画。<br /><br />如果已知的活动的进度，使用[ `ProgressBar` ](#progressbar)相反。<br /><br />[API 文档](xref:Xamarin.Forms.ActivityIndicator) / [指南](~/xamarin-forms/user-interface/activityindicator.md) / [示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos) | [![ActivityIndicator 示例](views-images/ActivityIndicator.png "ActivityIndicator 示例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 使用动画来显示，该应用程序时通过耗时较长的活动的进展情况。 设置[ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress)属性设置为值介于 0 和 1，表示进度。<br /><br />如果不知道该活动的进度，使用[ `ActivityIndicator` ](#activityindicator)相反。<br /><br />[API 文档](xref:Xamarin.Forms.ProgressBar) / [指南](~/xamarin-forms/user-interface/progressbar.md) / [示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos) | [![ProgressBar 示例](views-images/ProgressBar.png "ProgressBar 示例")](views-images/ProgressBar-Large.png#lightbox "ProgressBar 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>显示集合的视图

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)使用不同的布局规范显示可滚动的数据项列表。 它旨在提供更灵活、更高的性能替代方法[`ListView`](xref:Xamarin.Forms.ListView)。 将属性设置为对象的集合，并`ItemTemplate`将属性设置为描述如何[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)设置这些项的格式的对象。 `ItemsSource` 该事件指示已进行了选择，这可`SelectedItem`作为属性。 `SelectionChanged`<br /><br />[指南](~/xamarin-forms/user-interface/collectionview/index.md) / [示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/) | [![CollectionView 示例](views-images/CollectionView.png "CollectionView 示例")](views-images/CollectionView-Large.png#lightbox "CollectionView 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) 派生自[ `ItemsView` ](xref:Xamarin.Forms.ItemsView`1) ，并显示可选择数据项的可滚动列表。 设置[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)属性设置为一系列对象，并设置[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述如何将项目对象要设置格式。 [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件发出信号，已做出选择，这是可用作[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性。<br /><br />[API 文档](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![ListView 示例](views-images/ListView.png "ListView 示例")](views-images/ListView-Large.png#lightbox "ListView 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>选取器

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) 显示选定的项，从列表中的文本字符串，并允许您选择该项，点击该视图时。 设置[ `Items` ](xref:Xamarin.Forms.Picker.Items)属性设置为一组字符串，则[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性设置为对象的集合。 [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)当选中某个项时触发事件。<br /><br />`Picker`仅选择此项时显示的项的列表。 使用[ `ListView` ](#listView)或[ `TableView` ](#tableView)有关页保留的可滚动列表。<br /><br />[API 文档](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![选取器示例](views-images/Picker.png "选取器示例")](views-images/Picker-Large.png#lightbox "选取器示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) 显示类型的行的列表[ `Cell` ](xref:Xamarin.Forms.Cell)可选标头和小标题。 设置[ `Root` ](xref:Xamarin.Forms.TableView.Root)类型的对象的属性[ `TableRoot` ](xref:Xamarin.Forms.TableRoot)，并添加[ `TableSection` ](xref:Xamarin.Forms.TableSection)对象的`TableRoot`。 每个`TableSection`是一系列`Cell`对象。<br /><br />[API 文档](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![TableView 示例](views-images/TableView.png "TableView 示例")](views-images/TableView-Large.png#lightbox "TableView 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
