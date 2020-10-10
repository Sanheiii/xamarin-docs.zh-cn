---
title: 将设计时数据与 XAML 预览器结合使用
description: 本文介绍如何使用设计时数据来显示 XAML 预览器中的数据密集型布局，而不运行您的应用程序。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4221cd313ca8fbc3f3f9f886e3bd3f774c7472f7
ms.sourcegitcommit: 27f33b0a83b9dc871ad9fb207a1c9960ef197d9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91897942"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>将设计时数据与 XAML 预览器结合使用

_某些布局难以直观显示，无需数据。使用这些提示可充分利用 XAML 预览器中的数据密集型页面。_

> [!NOTE]
> 如果使用 Windows Presentation Foundation (WPF) 或 UWP，请参阅 [将设计时数据与桌面应用程序的 XAML 设计器配合使用](/visualstudio/xaml-tools/xaml-designtime-data)

## <a name="design-time-data-basics"></a>设计时数据基础

设计时数据是您设置的虚假数据，以使您的控件更易于在 XAML 预览程序中可视化。 若要开始，请将以下代码行添加到 XAML 页面的标头：

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

添加命名空间后，可以将 `d:` 其置于任何属性或控件的前面，以便在 XAML 预览器中显示它。 `d:`运行时未显示的元素。

例如，可以将文本添加到通常具有绑定数据的标签。

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![使用标签中的文本设计时间数据](xaml-previewer-images/designtimedata-label-sm.png "使用文本标记设计时间数据")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

在此示例中，如果没有 `d:Text` ，XAML 预览程序将不会显示标签。 而是显示“Name!”， 其中，标签在运行时将包含实际数据。

可以将 `d:` 与控件的任何特性一起使用， Xamarin.Forms 如颜色、字号和间距。 甚至可以将其添加到控件本身：

```xaml
<d:Button Text="Design Time Button" />
```

[![使用按钮控件设计时间数据](xaml-previewer-images/designtimedata-controls-sm.png "使用按钮控件设计时间数据")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此示例中，按钮仅在设计时显示。 使用此方法可为 [XAML 预览器不支持的自定义控件](render-custom-controls.md)放置中的占位符。

## <a name="preview-images-at-design-time"></a>在设计时预览图像

您可以为绑定到页面或动态加载的图像设置设计时间源。 在 Android 项目中，将你想要在 XAML 预览器中显示的图像添加到 **> 可绘制** 文件夹的资源。 在 iOS 项目中，将图像添加到 **Resources** 文件夹。 然后，你可以在设计时在 XAML 预览器中显示该图像：

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![用图像设计时间数据](xaml-previewer-images/designtimedata-image-sm.png "用配置映像设计时间数据")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的设计时数据

Listview 是在移动应用中显示数据的一种常用方式。 但是，如果没有实际数据，就难以直观显示。 若要将设计时数据与它们一起使用，必须创建一个用作 System.windows.controls.itemscontrol.itemssource 的设计时数组。 XAML 预览器会在设计时显示 ListView 中该数组的内容。

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![使用 ListView 设计时间数据](xaml-previewer-images/designtimedata-itemssource-sm.png "使用 ListView 设计时间数据")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

此示例将在 XAML 预览器中显示三个 TextCells 的 ListView。 您可以更改 `x:String` 为项目中现有的数据模型。

你也可以创建一个数据对象数组。 例如， `Monkey` 可以将数据对象的公共属性构建为设计时数据：

```csharp
namespace Monkeys.Models
{
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
    }
}
```

若要在 XAML 中使用类，则需要在根节点中导入命名空间：

```xaml
xmlns:models="clr-namespace:Monkeys.Models"
```

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type models:Monkey}">
                <models:Monkey Name="Baboon" Location="Africa and Asia"/>
                <models:Monkey Name="Capuchin Monkey" Location="Central and South America"/>
                <models:Monkey Name="Blue Monkey" Location="Central and East Africa"/>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="models:Monkey">
                <TextCell Text="{Binding Name}"
                          Detail="{Binding Location}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

此处的优点是可以绑定到计划使用的实际模型。

## <a name="alternative-hardcode-a-static-viewmodel"></a>替代方法：硬编码静态 ViewModel

如果不想将设计时数据添加到各个控件，可以设置模拟数据存储以绑定到页面。 请参阅 James Montemagno 的 [博客文章添加设计时数据](https://montemagno.com/xamarin-forms-design-time-data-tips-best-practices/) ，了解如何绑定到 XAML 中的静态 ViewModel。

## <a name="troubleshooting"></a>疑难解答

### <a name="requirements"></a>要求

设计时数据需要的最低版本为 Xamarin.Forms 3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 在我的设计时数据下显示波浪线

这是一个已知问题，将在即将发布的 Visual Studio 版本中得到解决。 项目仍将生成，但不会发生错误。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 预览器已停止工作

尝试关闭并重新打开 XAML 文件，清理并重新生成项目。
