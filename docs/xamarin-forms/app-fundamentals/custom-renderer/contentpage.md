---
title: 自定义 ContentPage
description: ContentPage 是一个可视元素，它显示单个视图并占据大部分屏幕区域。 本文演示了如何为 ContentPage 页面创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d9749c110019f2cf711c1df56196d3296223641
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557967"
---
# <a name="customizing-a-contentpage"></a>自定义 ContentPage

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)

ContentPage 是一个可视元素，它显示单个视图并占据大部分屏幕区域。本文演示了如何为 ContentPage 页面创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。

每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 当 Xamarin.Forms 应用程序呈现 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 时，将在 iOS 中实例化 `PageRenderer` 类，而该操作又会实例化本机 `UIViewController` 控件。 在 Android 平台上，`PageRenderer` 类实例化 `ViewGroup` 控件。 在通用 Windows 平台 (UWP) 上，`PageRenderer` 类实例化 `FrameworkElement` 控件。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 和实现它的相应本机控件之间的关系：

![ContentPage 类和实现本机控件之间的关系](contentpage-images/contentpage-classes.png)

通过在每个平台上为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 创建自定义呈现器，可以利用呈现过程来实现特定于平台的自定义。 执行此操作的过程如下：

1. [创建](#creating-the-xamarinforms-page) Xamarin.Forms 页面。
1. [使用](#consuming-the-xamarinforms-page) Xamarin.Forms 中的页面。
1. 在每个平台上为页面[创建](#creating-the-page-renderer-on-each-platform)自定义呈现器。

现在将依次介绍每个项，以实现 `CameraPage`，提供实时摄像头源和捕获照片的功能。

## <a name="creating-the-no-locxamarinforms-page"></a>创建 Xamarin.Forms 页面

可向共享的 Xamarin.Forms 项目添加未更改的 [`ContentPage`](xref:Xamarin.Forms.ContentPage)，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同样，[`ContentPage`](xref:Xamarin.Forms.ContentPage) 的代码隐藏文件也应保持未更改，如下面的代码示例中所示：

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

以下代码示例显示如何在 C# 中创建页面：

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

`CameraPage` 的实例将用于在每个平台上显示实时摄像头源。 控件的自定义内容将在自定义呈现器中进行设置，因此 `CameraPage` 类中无需任何其他实现。

## <a name="consuming-the-no-locxamarinforms-page"></a>使用 Xamarin.Forms 页面

空白的 `CameraPage` 必须由 Xamarin.Forms 应用程序显示。 单击 `MainPage` 实例上的按钮时将发生此情况，这将反过来执行 `OnTakePhotoButtonClicked` 方法，如下面的代码示例中所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此代码只是简单地导航到 `CameraPage`，自定义呈现器将在该页面自定义每个平台上的页面外观。

## <a name="creating-the-page-renderer-on-each-platform"></a>在每个平台上创建页面呈现器

创建自定义呈现器类的过程如下所示：

1. 创建 `PageRenderer` 类的子类。
1. 替代 `OnElementChanged` 方法，以呈现导航页并编写自定义该页面的逻辑。 创建相应的 Xamarin.Forms 控件时将调用 `OnElementChanged` 方法。
1. 向页面呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 页。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 可选择在每个平台项目中提供页面呈现器。 如果未注册页面呈现器，则将使用默认的页面呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![CameraPage 自定义呈现器项目的职责](contentpage-images/solution-structure.png)

`CameraPage` 实例由平台特定的 `CameraPageRenderer` 类呈现，它们均派生自该平台的 `PageRenderer` 类。 这会导致使用实时摄像头源呈现 `CameraPage` 实例，如下面的屏幕截图中所示：

![每个平台上的 CameraPage](contentpage-images/screenshots.png)

`PageRenderer` 类会公开 `OnElementChanged` 方法，此方法会在创建 Xamarin.Forms 页面时被调用以呈现相应的本机控件。 此方法采用 `ElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素 。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `CameraPage` 实例的引用。

`CameraPageRenderer` 类中 `OnElementChanged` 方法的替代版本是执行本机页面自定义的位置。 可通过 `Element` 属性获取对正在呈现的 Xamarin.Forms 页面实例的引用。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 页面的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各节讨论针对每个平台的 `CameraPageRenderer` 自定义呈现器的实现。

### <a name="creating-the-page-renderer-on-ios"></a>在 iOS 上创建页面呈现器

以下代码示例展示了适用于 iOS 平台的页面呈现器：

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

调用基类的 `OnElementChanged` 方法可对 iOS `UIViewController` 控件实例化。 实时摄像头流仅在呈现器尚未附加到现有 Xamarin.Forms 元素的前提下呈现，并且假设存在自定义呈现器正在呈现的页面实例。

然后，将通过使用 `AVCapture` API 的一系列方法自定义页面，以提供来自摄像头的实时流和捕获照片的功能。

### <a name="creating-the-page-renderer-on-android"></a>创建适用于 Android 的页面呈现器

以下代码示例展示了适用于 Android 平台的页面呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

调用基类的 `OnElementChanged` 方法可对 Android `ViewGroup` 控件实例化，该控件是一组视图。 实时摄像头流仅在呈现器尚未附加到现有 Xamarin.Forms 元素的前提下呈现，并且假设存在自定义呈现器正在呈现的页面实例。

然后，将通过调用使用 `Camera` API 的一系列方法自定义页面，以在调用 `AddView` 方法之前提供来自摄像头的实时流和捕获照片的功能，以向 `ViewGroup` 添加实时摄像头流 UI。 请注意，在 Android 上，还有必要替代 `OnLayout` 方法以对视图执行测量和布局操作。 有关详细信息，请参阅 [ContentPage 呈现器示例](/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)。

### <a name="creating-the-page-renderer-on-uwp"></a>在 UWP 上创建页面呈现器

以下代码示例展示了适用于 UWP 的页面呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

调用基类的 `OnElementChanged` 方法可对 `FrameworkElement` 控件实例化，页面将在其上呈现。 实时摄像头流仅在呈现器尚未附加到现有 Xamarin.Forms 元素的前提下呈现，并且假设存在自定义呈现器正在呈现的页面实例。 然后，将通过调用使用 `MediaCapture` API 的一系列方法自定义页面，以在自定义页面添加到 `Children` 集合用于显示之前提供来自摄像头的实时流和捕获照片的功能。

在 UWP 上实现派生自 `PageRenderer` 的自定义呈现器时，还应呈现 `ArrangeOverride` 方法，以排列页面控件，因为基础呈现器不知道如何处理它们。 否则，将生成空白页。 因此，在此示例中，`ArrangeOverride` 方法将对 `Page` 实例调用 `Arrange` 方法。

> [!NOTE]
> 停止和释放 UWP 应用程序中提供对摄像头的访问权限的对象至关重要。 如果不这样做，可能会影响尝试访问设备的摄像头的其他应用程序。 有关详细信息，请参阅[显示摄像头预览](/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>总结

本文演示了如何为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 页面创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。 `ContentPage` 是一个可视元素，它显示单个视图并占据大部分屏幕区域。

## <a name="related-links"></a>相关链接

- [CustomRendererContentPage（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)