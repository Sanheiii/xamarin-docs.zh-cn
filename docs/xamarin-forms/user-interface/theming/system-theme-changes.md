---
title: 响应应用程序中的系统主题更改 Xamarin.Forms
description: Xamarin.Forms 应用程序可以使用 OnAppTheme 类型和 DynamicResource 标记扩展对操作系统主题更改做出响应。
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 258061042fc8ab80b8bbdd9a0a9a965d87a1e064
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557564"
---
# <a name="respond-to-system-theme-changes-in-no-locxamarinforms-applications"></a>响应应用程序中的系统主题更改 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

设备通常包括浅色和深色主题，它们分别引用可在操作系统级别设置的各种外观首选项。 应用程序应遵守这些系统主题，并在系统主题发生更改时立即做出响应。

根据设备配置的不同原因，系统主题可能会发生变化。 这包括用户显式更改的系统主题，它因一天中的某个时间而变化，并因环境因素（如低亮度）而变化。

Xamarin.Forms 应用程序可以通过使用带有 `AppThemeBinding` 标记扩展的资源和 `SetAppThemeColor` 和扩展方法来响应系统主题更改 `SetOnAppTheme<T>`  。

Xamarin.Forms为了响应系统主题更改，必须满足以下要求：

- Xamarin.Forms 4.6.0.967 或更高版本。
- iOS 13 或更高版本。
- Android 10 (API 29) 或更高版本。
- UWP 版本14393或更高版本。

以下屏幕截图显示了主题页面，适用于 iOS 和 Android 上的轻型和深色系统主题：

[![IOS 和 Android](system-theme-changes-images/main-page-both-themes.png "主题应用的主页")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "主题应用的主页") 
 上主题应用的主页的屏幕截图[ ![IOS 和 Android 上的主题应用的详细信息页的屏幕截图](system-theme-changes-images/detail-page-both-themes.png "主题应用的详细信息页")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "主题应用的详细信息页")

## <a name="define-and-consume-theme-resources"></a>定义和使用主题资源

用于浅和深色主题的资源可与 `AppThemeBinding` 标记扩展一起使用，以及 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 扩展方法。 利用这些方法，将根据当前系统主题的值自动应用资源。 此外，如果在应用程序运行时系统主题发生更改，则会自动更新使用这些资源的对象。

### <a name="appthemebinding-markup-extension"></a>AppThemeBinding 标记扩展

`AppThemeBinding`标记扩展使你能够根据当前系统主题使用图像或颜色等资源：

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label) 当设备使用光源主题时，第一个的文本颜色设置为绿色; 当设备使用其深主题时，将设置为红色。 同样， [`Image`](xref:Xamarin.Forms.Image) 根据当前系统主题显示不同的图像文件。

此外，在中定义的资源 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可与 `StaticResource` 标记扩展一起使用：

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

在此示例中，的背景色 [`Grid`](xref:Xamarin.Forms.Grid) 和样式根据 [`Button`](xref:Xamarin.Forms.Button) 设备使用的是背景色还是深色主题而变化。

有关标记扩展的详细信息 `AppThemeBinding` ，请参阅 [AppThemeBinding 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)。

### <a name="extension-methods"></a>扩展方法

Xamarin.Forms 包含 `SetAppThemeColor` 和 `SetOnAppTheme<T>` 扩展方法，这些方法使 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 对象能够响应系统主题更改。

`SetAppThemeColor`方法启用 [`Color`](xref:Xamarin.Forms.Color) 要根据当前系统主题在目标属性上设置的对象：

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label) 当设备使用光源主题时，的文本颜色设置为绿色; 当设备使用其深色主题时，将设置为红色。

`SetOnAppTheme<T>`方法启用 `T` 将基于当前系统主题在目标属性上设置的类型的对象：

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

在此示例中， [`Image`](xref:Xamarin.Forms.Image) `lightlogo.png` 当设备使用其轻型主题时，以及 `darklogo.png` 当设备使用其深主题时，会显示。

## <a name="detect-the-current-system-theme"></a>检测当前系统主题

可以通过获取属性的值来检测当前系统主题 `Application.RequestedTheme` ：

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

`RequestedTheme`属性返回 `OSAppTheme` 枚举成员。 `OSAppTheme` 枚举定义下列成员：

- `Unspecified`，它指示设备使用未指定的主题。
- `Light`，它指示设备使用的是光源主题。
- `Dark`，它指示设备使用的是深色主题。

## <a name="set-the-current-user-theme"></a>设置当前用户主题

应用程序使用的主题可以通过属性进行设置，该 `Application.UserAppTheme` 属性的类型为 `OSAppTheme` ，而不考虑当前运行的是哪个系统主题：

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

在此示例中，应用程序设置为使用为系统暗色模式定义的主题，而不管当前操作的是哪个主题。

> [!NOTE]
> 将 `UserAppTheme` 属性设置为， `OSAppTheme.Unspecified` 默认为操作系统主题。

## <a name="react-to-theme-changes"></a>主题更改的响应

设备上的系统主题可能会因多种原因而发生变化，这取决于设备的配置方式。 Xamarin.Forms 当系统主题通过处理事件进行更改时，可以通知应用 `Application.RequestedThemeChanged` ：

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

`AppThemeChangedEventArgs`事件附带的对象 `RequestedThemeChanged` 具有名为的单个属性 `RequestedTheme` ，类型为 `OSAppTheme` 。 可以检查此属性以检测请求的系统主题。

> [!IMPORTANT]
> 若要响应 Android 上的主题更改，你必须 `ConfigChanges.UiMode` 在类的属性中包含该标志 `Activity` `MainActivity` 。

## <a name="related-links"></a>相关链接

- [SystemThemes (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [AppThemeBinding 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)