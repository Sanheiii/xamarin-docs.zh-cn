---
title: Xamarin.Forms 中的字符串和图像本地化
description: 可使用 .NET 资源文件实现 Xamarin.Forms 应用本地化。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 727bac49c5bff522125a2f21a9742e428b81c924
ms.sourcegitcommit: bb18e6b3b5d592374b17932efa1e85c3b5fbad57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950737"
---
# <a name="no-locxamarinforms-string-and-image-localization"></a>Xamarin.Forms 字符串和图像本地化

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

本地化是调整应用程序来满足目标市场的特定语言或区域性要求的过程。 为实现本地化，应用程序中的文本和图像可能需要翻译成多种语言。 本地化后的应用程序会根据移动设备的区域性设置自动显示翻译后的文本：

![iOS 和 Android 上的本地化应用程序的屏幕截图](text-images/localizationdemo-screenshots.png)

.NET Framework 包含一个使用 [Resx 资源文件](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)进行应用程序本地化的内置机制。 资源文件将文本和其他内容存储为名称/值对，让应用程序能在内容中检索所提供的键。 资源文件可使本地化的内容与应用程序代码分开。

要使用资源文件进行 Xamarin.Forms 应用程序的本地化，需要执行以下步骤：

1. 创建包含已翻译文本的 [Resx 文件](#create-resx-files)。
1. 在共享项目中[指定默认区域性](#specify-the-default-culture)。
1. [在 Xamarin.Forms 中进行文本本地化](#localize-text-in-xamarinforms)。
1. 根据每个平台的区域性设置[本地化图像](#localize-images)。
1. 在每个平台上[本地化应用程序名称](#localize-the-application-name)。
1. 在每个平台上[测试本地化](#test-localization)。

## <a name="create-resx-files"></a>创建 Resx 文件

资源文件是扩展名为 .resx 的 XML 文件，它们在生成过程中会编译为二进制资源 (.resources) 文件。 Visual Studio 2019 生成一个类，该类提供用于检索资源的 API。 本地化后的应用程序通常包含一个默认资源文件，其中带有应用程序中使用的所有字符串，还包含用于每种受支持语言的资源文件。 示例应用程序在共享项目中有一个 Resx 文件夹，其中包含多个资源文件和一个名为 AppResources.resx 的默认资源文件 。

资源文件包含每个项的下列信息：

- 名称指定用于在代码中访问文本的键。
- 值指定翻译后的文本。
- 注释是包含其他信息的可选字段。

::: zone pivot="windows"

资源文件通过 Visual Studio 2019 中的“添加新项”对话框进行添加：

![在 Visual Studio 2019 中添加新资源](text-images/pc-add-resource-file.png)

添加文件后，可为每个文本资源添加行：

![在 .resx 文件中指定默认文本资源](text-images/pc-default-strings.png)

“访问修饰符”下拉设置确定了 Visual Studio 如何生成用于访问资源的类。 将“访问修饰符”设置为“公共”或“内部”会生成具有指定可访问性级别的类 。 如果将“访问修饰符”设置为“不生成代码”，则不会生成类文件。 默认资源文件应配置为生成类文件，这样即可生成一个扩展名为 .designer.cs 且要添加到项目中的文件。

创建默认资源文件后，可为应用程序支持的每个区域性创建其他文件。 每个其他资源文件都应在文件名中包含翻译区域性，并且应将“访问修饰符”设置为“不生成代码” 。

运行时期间，应用程序会按指定顺序尝试解析资源请求。 例如，如果设备区域性是 en-US，应用程序将按以下顺序查找资源文件：

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx（默认）

以下屏幕截图显示了名为 AppResources.es.cs 的西班牙语翻译文件：

![在 .resx 文件中指定默认西班牙语文本资源](text-images/pc-spanish-strings.png)

翻译文件使用默认文件中指定的“名称”值，但会在“值”列中包含西班牙语语言字符串 。 此外，“访问修饰符”设置为“不生成代码” 。

::: zone-end
::: zone pivot="macos"

资源文件通过 Visual Studio 2019 for Mac 中的“添加新文件”对话框进行添加：

![在 Visual Studio 2019 for Mac 中添加新资源](text-images/mac-add-resource-file.png)

创建默认资源文件后，可通过在资源文件的 `root` 元素中创建 `data` 元素来添加文本：

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

可通过在资源文件选项中设置“自定义工具”属性来创建 .designer.cs 类文件 ：

![资源文件的属性中指定的自定义工具](text-images/mac-resx-properties.png)

将“自定义工具”设置为 PublicResXFileCodeGenerator 将生成具有 `public` 访问权限的类 。 将“自定义工具”设置为 InternalResXFileCodeGenerator 将生成具有 `internal` 访问权限的类 。 “自定义工具”值为空则不生成类。 生成的类名称将与资源文件名匹配。 例如，AppResources.resx 文件将在名为 AppResources.designer.cs 的文件中创建 `AppResources` 类 。

可为每个支持的区域性创建其他资源文件。 每个语言文件都应在文件名中包含翻译区域性，因此要翻译成 es-MX 的文件应命名为 AppResources.es-MX.resx 。

运行时期间，应用程序会按指定顺序尝试解析资源请求。 例如，如果设备区域性是 en-US，应用程序将按以下顺序查找资源文件：

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx（默认）

语言翻译文件的“名称”值应与默认文件中指定的相同。 以下 XML 显示了名为 AppResources.es.resx 的西班牙语翻译文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>指定默认区域性

要使资源文件可正常使用，应用程序必须指定 `NeutralResourcesLanguage`。 在共享项目中，应自定义 AssemblyInfo.cs 文件以指定默认区域性。 以下代码演示如何在 AssemblyInfo.cs 文件中将 `NeutralResourcesLanguage` 设置为 en-US ：

```csharp
using System.Resources;

// The resources from the neutral language .resx file are stored directly
// within the library assembly. For that reason, changing en-US to a different
// language in this line will not by itself change the language shown in the
// app. See the discussion of UltimateResourceFallbackLocation in the
// documentation for additional information:
// https://docs.microsoft.com/dotnet/api/system.resources.neutralresourceslanguageattribute
[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> 如果未指定 `NeutralResourcesLanguage` 属性，`ResourceManager` 类会对缺少特定资源文件的任何区域性返回 `null` 值。 指定默认区域性后，`ResourceManager` 会对不支持的区域性返回默认 Resx 文件中的结果。 因此，建议始终指定 `NeutralResourcesLanguage` 以便对不支持的区域性显示文本。

创建默认资源文件并在 AssemblyInfo.cs 文件中指定默认区域性后，应用程序可在运行时期间检索已本地化的字符串。

要详细了解资源文件，请参阅 [Create resource files for .NET apps](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)（为 .NET 应用创建资源文件）。

## <a name="specify-supported-languages-on-ios"></a>指定 iOS 上支持的语言

在 iOS 上，必须为项目声明 Info.plist 文件中支持的所有语言。 在 Info.plist 文件中，使用源视图为 `CFBundleLocalizations` 键设置数组，并提供与 Resx 文件相对应的值 。 此外，请确保通过 `CFBundleDevelopmentRegion` 键设置所需的语言：

![显示本地化部分的 Info.plist 编辑器的屏幕截图](text-images/info-plist.png)

或者，在 XML 编辑器中打开 Info.plist 文件并添加以下内容：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

> [!NOTE]
> Apple 处理葡萄牙语的方式与你所想的略有不同。 有关详细信息，请参阅 developer.apple.com 上的[添加语言](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2)。

有关详细信息，请参阅[在 Info.plist 中指定默认语言和支持的语言](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist)。

## <a name="localize-text-in-no-locxamarinforms"></a>在 Xamarin.Forms 中进行文本本地化

文本通过在 Xamarin.Forms 中使用生成的 `AppResources` 类进行本地化。 此类根据默认的资源文件名称进行命名。 由于示例项目资源文件名为 AppResources.cs，因此 Visual Studio 会生成名为 `AppResources` 的匹配类。 对资源文件中的每一行，静态属性在 `AppResources` 类中生成。 以下静态属性在示例应用程序的 `AppResources` 类中生成：

- AddButton
- NotesLabel
- NotesPlaceholder

将这些值作为 [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) 属性访问即可在 XAML 中显示本地化文本：

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

还可在代码中检索本地化文本：

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };

    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };

    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };

    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

`AppResources` 类中的属性使用 `System.Globalization.CultureInfo.CurrentUICulture` 的当前值确定要从中检索值的区域性资源文件。

## <a name="localize-images"></a>本地化图像

除存储文本以外，Resx 文件不仅能够存储文本，还可以存储图像和二进制数据。 但是，移动设备的屏幕大小和密度各异，而每个移动平台都有显示受到密度约束的图像的功能。 因此，应使用平台图像本地化功能，而不是将图像存储到资源文件。

### <a name="localize-images-on-android"></a>在 Android 上本地化图像

在 Android 上，使用“资源”目录中的文件夹命名约定来存储已本地化的可绘制资源（图像）。 文件夹命名为 drawable，并附上目标语言的后缀。 例如，西班牙语文件夹命名为 drawable-es。

如果需要 4 个字母的区域设置代码，Android 需要在短划线后附加 r。 例如，墨西哥区域设置 (es-MX) 文件夹应命名为 drawable-es-rMX。 每个区域设置文件夹中的图像文件名应相同：

![Android 项目中的本地化图像](text-images/pc-android-images.png)

有关详细信息，请参阅 [Android 本地化](~/android/app-fundamentals/localization.md)。

### <a name="localize-images-on-ios"></a>在 iOS 上本地化图像

在 iOS 上，使用“资源”目录中的文件夹命名约定来存储本地化图像。 默认文件夹命名为 Base.lproj。 特定语言的文件夹以语言或区域设置名称命名并后接 .lproj。 例如，西班牙语文件夹命名为 es.lproj。

四字母本地代码的工作方式与两字母语言代码的类似。 例如，墨西哥区域设置 (es-MX) 文件夹应命名为 es-MX.lproj。 每个区域设置文件夹中的图像文件名应相同：

![iOS 项目中的本地化图像](text-images/pc-ios-images.png)

> [!NOTE]
> iOS 支持创建本地化资产目录，不支持使用 .lproj 文件夹结构。 但是，这些必须在 Xcode 中进行创建和管理。

有关详细信息，请参阅 [iOS 本地化](~/ios/app-fundamentals/localization/index.md)。

### <a name="localize-images-on-uwp"></a>在 UWP 上本地化图像

在 UWP 上，使用“资产/图像”目录中的文件夹命名约定来存储本地化图像。 文件夹按语言或区域设置进行命名。 例如，西班牙语文件夹以 es 命名，墨西哥区域设置文件夹应以 es-MX 命名 。 每个区域设置文件夹中的图像文件名应相同：

![UWP 项目中的本地化图像](text-images/pc-uwp-images.png)

有关详细信息，请参阅 [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="consume-localized-images"></a>使用本地化图像

由于每个平台均使用唯一的文件结构来存储图像，因此 XAML 使用 `OnPlatform` 类根据当前平台设置 `ImageSource` 属性：

```xaml
<Image>
    <Image.Source>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="flag.png" />
            <On Platform="UWP" Value="Assets/Images/flag.png" />
        </OnPlatform>
    </Image.Source>
</Image>
```

> [!NOTE]
> `OnPlatform` 标记扩展提供了一种更简洁的方式来指定特定于平台的值。 有关详细信息，请参见 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

可根据代码中的 `Device.RuntimePlatform` 属性设置图像源：

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>对应用程序名称进行本地化

应用程序名称是按平台指定的，不使用 Resx 资源文件。 若要在 Android 上对应用程序名称进行本地化，请参阅[在 Android 上对应用名称进行本地化](~/android/app-fundamentals/localization.md#stringsxml-file-format)。 若要在 iOS 上对应用程序名称进行本地化，请参阅[在 iOS 上对应用名称进行本地化](~/ios/app-fundamentals/localization/index.md#app-name)。 若要在 UWP 上对应用程序名称进行本地化，请参阅[对 UWP 程序包清单中的字符串进行本地化](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest)。

## <a name="test-localization"></a>对本地化进行测试

对本地化进行测试的最佳做法是更改设备语言。 可在代码中设置 `System.Globalization.CultureInfo.CurrentUICulture` 的值，但各平台上的设置行为并不相同，因此不建议这样测试。

在 iOS 的设置应用中，可专门设置每个应用的语言，而无需更改设备语言。

在 Android 上，应用程序启动时会检测并缓存语言设置。 如果更改语言，则可能需要退出并重启应用程序才能看到所做更改。

## <a name="related-links"></a>相关链接

- [本地化示例项目](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Create resource files for .NET apps](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)（为 .NET 应用创建资源文件）
- [跨平台本地化](~/cross-platform/app-fundamentals/localization.md)
- [使用 CultureInfo 类 (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Android 本地化](~/android/app-fundamentals/localization.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
- [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)
- [使用特定区域性的资源 (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
