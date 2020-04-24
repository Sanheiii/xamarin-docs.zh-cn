---
title: Xamarin 中的图像
description: 可以通过 Xamarin 在平台之间共享映像，这些映像可以专门为每个平台加载，也可以下载以便显示。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: dc7a678e106cc4065eab1ae9023bdc4875a93f9b
ms.sourcegitcommit: 1fb87ff74560d4d7c89f80018cc010c07646461c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139065"
---
# <a name="images-in-xamarinforms"></a>Xamarin 中的图像

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_可以通过 Xamarin 在平台之间共享映像，这些映像可以专门为每个平台加载，也可以下载以便显示。_

映像是应用程序导航、可用性和品牌的重要组成部分。 Xamarin。窗体应用程序需要能够跨所有平台共享图像，但可能会在每个平台上显示不同的图像。

图标和初始屏幕也需要特定于平台的图像;需要在每个平台上配置这些设置。

## <a name="display-images"></a>显示图像

Xamarin 使用[`Image`](xref:Xamarin.Forms.Image)视图在页面上显示图像。 它有两个重要属性：

- [`Source`](xref:Xamarin.Forms.Image.Source)- [`ImageSource`](xref:Xamarin.Forms.ImageSource)实例，文件、Uri 或资源，用于设置要显示的图像。
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect)-如何在它所显示的边界内调整图像大小（是伸展、裁剪还是黑边）。

[`ImageSource`](xref:Xamarin.Forms.ImageSource)对于每种类型的映像源，都可以使用静态方法获取实例：

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))-需要可在每个平台上解析的文件名或文件路径。
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))-需要 Uri 对象，例如。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)-需要资源标识符到应用程序或 .NET Standard 库项目中嵌入的图像文件，其中包含**生成操作： EmbeddedResource**。
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))-需要提供图像数据的流。

[`Aspect`](xref:Xamarin.Forms.Image.Aspect)属性确定如何缩放图像以适合显示区域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)-将图像拉伸到完全完全填充显示区域。 这可能会导致图像扭曲。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-剪辑图像，使其在保留该方位（即无扭曲）的情况下填充显示区域。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Letterboxes 图像（如有必要），以便将整个图像适应显示区域，并根据图像的宽度或高度增加空白区域。

可以从[本地文件](#local-images)、[嵌入资源](#embedded-images)、[下载](#download-images)或从流中加载图像。 此外，通过在[`Image`](xref:Xamarin.Forms.Image) `FontImageSource`对象中指定字体图标数据，可通过视图显示字体图标。 有关详细信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)指南中的[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="local-images"></a>本地映像

可以将图像文件添加到每个应用程序项目中，并从 Xamarin. Forms 共享代码进行引用。 当图像特定于平台时，例如在不同平台上使用不同分辨率或稍微不同的设计时，需要这种分发图像的方法。

若要在所有应用中使用单个图像，*必须在每个平台上使用相同的文件名*，并且它应是有效的 Android 资源名称（即，只允许使用小写字母、数字、下划线和句点）。

- **ios** -从 ios 9 开始管理和支持映像的首选方法是使用**资产目录映像集，该映像集**应包含支持各种设备所需的所有版本映像和应用程序的缩放比例。 有关详细信息，请参阅[将图像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- 带有生成操作的 "**资源/** 图形目录" 中的**Android**位置图像 **： AndroidResource**。 还可以提供图像的高 DPI 和低 DPI 版本（在适当命名的**资源**子目录中，如**ldpi**、 **hdpi**和可**绘制-xhdpi**）。
- **通用 Windows 平台（UWP）** -默认情况下，应将图像放在应用程序的根目录中，**生成操作： Content**。 或者，可以将图像置于不同的目录中，然后使用特定于平台的来指定映像。 有关详细信息，请参阅[Windows 上的默认图像目录](~/xamarin-forms/platform/windows/default-image-directory.md)。

> [!IMPORTANT]
> 在 iOS 9 之前，映像通常放置在包含生成操作的**Resources**文件夹中 **： BundleResource**。 但是，Apple 不推荐使用此方法来处理 iOS 应用中的图像。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵循这些文件命名和放置规则，可在所有平台上加载和显示映像：

```xaml
<Image Source="waterfront.jpg" />
```

等效的 c # 代码如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

以下屏幕截图显示了在每个平台上显示本地映像的结果：

[![显示本地映像的示例应用程序](images-images/local-sml.png)](images-images/local.png#lightbox)

为了获得更大`Device.RuntimePlatform`的灵活性，此属性可用于为某些或所有平台选择不同的图像文件或路径，如以下代码示例所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要在所有平台上使用相同的映像文件名，该名称必须在所有平台上都有效。 Android 绘图具有命名限制–只允许使用小写字母、数字、下划线和句点–对于跨平台兼容性，还必须在所有其他平台上遵循这一点。 示例 filename**海滨**遵循规则，但无效文件名的示例包括 "水源 front .png"、"海滨"、"water-front" 和 "wåterfront"。

### <a name="native-resolutions-retina-and-high-dpi"></a>本机分辨率（retina 和高 DPI）

iOS、Android 和 UWP 包括支持不同的映像分辨率，其中操作系统会根据设备的功能在运行时选择适当的映像。 Xamarin 使用本机平台的 Api 来加载本地映像，因此，如果文件已正确命名并位于项目中，则它会自动支持替代解决方案。

自 iOS 9 以后管理映像的首选方法是将图像拖动到相应资产目录图像集所需的每个解析。 有关详细信息，请参阅[将图像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前，可能会在**资源**文件夹中放置图像的 retina 版本，在文件扩展名之前，可以将图像的**@2x**两**@3x**个版本和文件名上的后缀（例如 **myimage@2x.png**). 但是，Apple 不推荐使用此方法来处理 iOS 应用中的图像。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 备用分辨率图像应放置在 Android 项目的[特殊命名目录](https://developer.android.com/guide/practices/screens_support.html)中，如以下屏幕截图所示：

[![Android 多分辨率图像位置](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

UWP 图像文件名[可以在文件扩展名之前`.scale-xxx`带有后缀](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中`xxx`是应用于资产的缩放百分比，例如**myimage. scale-200**。 然后，在不带缩放修饰符的情况下，可以在代码或 XAML 中引用图像，例如仅**myimage**。 平台将基于显示器的当前 DPI 选择最接近的适当资产规模。

### <a name="additional-controls-that-display-images"></a>显示图像的其他控件

某些控件具有显示图像的属性，例如：

- [`Button`](xref:Xamarin.Forms.Button)具有一个[`ImageSource`](xref:Xamarin.Forms.Button.ImageSource)属性，该属性可设置为要在上显示的`Button`位图图像。 有关详细信息，请参阅将[位图与按钮一起使用](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。
- [`ImageButton`](xref:Xamarin.Forms.Button)具有可[`Source`](xref:Xamarin.Forms.ImageButton.Source)设置为要在中显示的图像的属性`ImageButton`。 有关详细信息，请参阅[设置图像源](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source)。
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)具有一个[`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)属性，该属性可设置为从文件、嵌入资源、URI 或流加载的图像。
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)具有一个[`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource)属性，该属性可设置为从文件、嵌入资源、URI 或流中检索的图像。
- [`Page`](xref:Xamarin.Forms.Page). 派生自`Page`的任何页类型都[`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource)具有[`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource)和属性，可以为其分配文件、嵌入资源、URI 或流。 在某些情况下（例如当显示[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)时）， [`ContentPage`](xref:Xamarin.Forms.ContentPage)将显示图标（如果平台支持）。

  > [!IMPORTANT]
  > 在 iOS 上， [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource)不能从资产目录图像集中的映像填充属性。 而是从文件、嵌入资源`Page.IconImageSource` 、URI 或流加载属性的图标图像。

## <a name="embedded-images"></a>嵌入图像

嵌入图像还随应用程序一起提供（例如本地映像），但在每个应用程序的文件结构中不会有图像的副本，而是将该图像文件作为资源嵌入到程序集中。 当在每个平台上使用相同的图像时，建议使用这种分发图像的方法，尤其适合创建组件时，因为图像与代码捆绑在一起。

若要在项目中嵌入图像，请右键单击 "添加新项"，然后选择要添加的映像。 默认情况下，该映像将包含**生成操作： None**;这需要设置为**生成操作： EmbeddedResource**。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![将生成操作设置为嵌入资源](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

可以在文件的 "**属性**" 窗口中查看和更改**生成操作**。

在此示例中，资源 ID 为**WorkingWithImages**。
IDE 通过使用文件名连接此项目的**默认命名空间**（在每个值之间使用句点（.））来生成此默认值。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

还可以在文件的**Properties** pad 中查看和更改**生成操作**。
此面板显示用于在代码中引用资源的**资源 ID** 。 在下面的屏幕截图中，**资源 ID**为**WorkingWithImages**。
IDE 通过使用文件名连接此项目的**默认命名空间**（在每个值之间使用句点（.））来生成此默认值。
可以在**Properties** pad 中编辑此 ID，但对于这些示例，将使用值**WorkingWithImages** 。

[![嵌入的资源属性板](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

如果将嵌入的图像放入项目中的文件夹，则在资源 ID 中，文件夹名称还由句点（.）分隔。 将**海滩 .jpg**图像移到名为**MyImages**的文件夹中会生成**WorkingWithImages**的资源 ID。

加载嵌入图像的代码只是将**资源 ID**传递到[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)方法，如下所示：

```csharp
var embeddedImage = new Image {
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg",
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> 若要支持在通用 Windows 平台上以发布模式显示嵌入的图像，则需要使用`ImageSource.FromResource`的重载来指定要在其中搜索图像的源程序集。

当前没有资源标识符的隐式转换。 相反，你必须使用[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)或`new ResourceImageSource()`来加载嵌入的图像。

以下屏幕截图显示了在每个平台上显示嵌入图像的结果：

[![显示嵌入图像的示例应用程序](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

由于不存在从`string`到`ResourceImageSource`的内置类型转换器，因此 XAML 无法以本机方式加载这些类型的图像。 可以改为使用 XAML 中指定的**资源 ID**来编写简单的自定义 XAML 标记扩展以加载图像：

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> 若要支持在通用 Windows 平台上以发布模式显示嵌入的图像，则需要使用`ImageSource.FromResource`的重载来指定要在其中搜索图像的源程序集。

若要使用此扩展，请`xmlns`使用项目的正确命名空间和程序集值将自定义添加到 XAML。 然后，可以使用以下语法设置图像源： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 示例如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>排查嵌入图像问题

#### <a name="debug-code"></a>调试代码

由于有时很难理解无法加载特定图像资源，因此可以暂时将以下调试代码添加到应用程序，以帮助确认正确配置资源。 它会将在给定程序集中嵌入的所有已知资源输出到**控制台**，以帮助调试资源加载问题。

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>嵌入到其他项目中的图像

默认情况下， `ImageSource.FromResource`该方法仅查找与调用`ImageSource.FromResource`方法的代码位于同一程序集中的图像。 使用上面的调试代码，你可以通过将`typeof()`语句更改为每个程序集中的`Type`已知来确定哪些程序集包含特定资源。

但是，可以将搜索嵌入图像的源程序集指定为`ImageSource.FromResource`方法的参数：

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>下载映像

可以自动下载图像以进行显示，如下面的 XAML 所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

等效的 c # 代码如下所示：

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

[`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法需要一个`Uri`对象，并返回一个从中[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)读取的`Uri`新。

还存在对 URI 字符串的隐式转换，因此下面的示例也适用：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

以下屏幕截图显示了在每个平台上显示远程映像的结果：

[![显示已下载映像的示例应用程序](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>已下载映像缓存

还[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)支持缓存下载的映像，这些映像通过以下属性进行配置：

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled)-是否启用缓存（`true`默认值）。
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)-一个`TimeSpan` ，它定义将在本地存储图像的时间长度。

默认情况下，缓存处于启用状态，并将在本地存储该图像24小时。 若要为特定映像禁用缓存，请按如下所示实例化映像源：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

若要设置特定的缓存时间段（例如5天），请按如下所示实例化映像源：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

利用内置缓存，可以非常轻松地支持图像滚动列表等方案，在这些方案中，你可以在每个单元格中设置（或绑定）一个图像，并让内置缓存在单元格滚动回 "查看" 时，处理重新加载该图像。

## <a name="animated-gifs"></a>动画 Gif

Xamarin 支持显示小型动画 Gif。 这是通过将[`Image.Source`](xref:Xamarin.Forms.Image.Source)属性设置为动画 gif 文件来完成的：

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> 虽然 Xamarin 中的动画 GIF 支持能够下载文件，但它不支持缓存或流式处理动画 Gif。

默认情况下，加载动画 GIF 后，不会播放它。 这是因为， `IsAnimationPlaying`用于控制动画 GIF 播放或停止的属性的默认值为`false`。 此属性的类型`bool`为，它是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它可以是数据绑定的目标和样式。

因此，在加载动态 GIF 后，将不会播放它，直到`IsAnimationPlaying`属性设置为`true`。 然后，可以通过将`IsAnimationPlaying`属性设置为来`false`停止播放。 请注意，在显示非 GIF 图像源时，此属性不起作用。

> [!NOTE]
> 在 Android 上，动态 GIF 支持要求你的应用程序使用快速呈现器，如果你已选择使用旧呈现器，则不能正常工作。
> 在 UWP 上，动态 GIF 支持要求最小版本的 Windows 10 周年更新（版本1607）。

## <a name="icons-and-splash-screens"></a>图标和初始屏幕

与[`Image`](xref:Xamarin.Forms.Image)视图无关时，应用程序图标和初始屏幕也是 Xamarin. Forms 项目中的图像的重要使用。

为 Xamarin 设置图标和初始屏幕。 Forms 应用在每个应用程序项目中完成。 这意味着为 iOS、Android 和 UWP 生成正确大小的图像。 应根据每个平台的要求来命名和查找这些映像。

## <a name="icons"></a>图标

有关创建这些应用程序资源的详细信息，请参阅[适用于磁贴和图标资产](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)的[iOS 使用图像](~/ios/app-fundamentals/images-icons/index.md)、 [Google 插图](https://developer.android.com/design/style/iconography.html)和 UWP 指导原则。

此外，通过在[`Image`](xref:Xamarin.Forms.Image) `FontImageSource`对象中指定字体图标数据，可通过视图显示字体图标。 有关详细信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)指南中的[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="splash-screens"></a>初始屏幕

只有 iOS 和 UWP 应用程序需要初始屏幕（也称为启动屏幕或默认图像）。

请参阅 Windows 开发人员中心上的 IOS 文档，[使用图像](~/ios/app-fundamentals/images-icons/index.md)和[初始屏幕](/windows/uwp/launch-resume/splash-screens/)。

## <a name="related-links"></a>相关链接

- [WorkingWithImages （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS 使用映像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 插图](https://developer.android.com/design/style/iconography.html)
- [磁贴和图标资源指南](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
