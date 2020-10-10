---
title:“实现视图”说明：本文说明如何为 Xamarin.Forms 自定义控件创建自定义呈现器，用于显示设备摄像头的预览视频流。
ms.prod: xamarin ms.assetid:915E25E7-4A6B-4F34-B7B4-07D5F4B240F2 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date:2018/10/05 no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

---
# <a name="implementing-a-view"></a>实现视图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_Xamarin.Forms 自定义用户界面控件应派生自视图类，该类用于在屏幕上放置布局和控件。本文演示如何为 Xamarin.Forms 自定义控件创建自定义呈现器，用于显示设备摄像头的预览视频流。_

每个 Xamarin.Forms 视图都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 当 iOS 中的 Xamarin.Forms 应用程序呈现 [`View`](xref:Xamarin.Forms.View) 时，将实例化 `ViewRenderer` 类，而该操作又会实例化本机 `UIView` 控件。 在 Android 平台上，`ViewRenderer` 类实例化本机 `View` 控件。 在通用 Windows 平台 (UWP) 上，`ViewRenderer` 类实例化本机 `FrameworkElement` 控件。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`View`](xref:Xamarin.Forms.View) 和实现它的相应本机控件之间的关系：

![视图类及其实现本机类之间的关系](view-images/view-classes.png)

通过在每个平台上为 [`View`](xref:Xamarin.Forms.View) 创建自定义呈现器，可以使用呈现过程来实现特定于平台的自定义。 执行此操作的过程如下：

1. [创建](#creating-the-custom-control) Xamarin.Forms 自定义控件。
1. [使用](#consuming-the-custom-control) Xamarin.Forms 中的自定义控件。
1. 在每个平台上为控件[创建](#creating-the-custom-renderer-on-each-platform)自定义呈现器。

现在将依次讨论每个项目，以实现 `CameraPreview` 呈现器，该呈现器显示设备摄像头的预览视频流。 点击视频流将停止和启动视频流。

## <a name="creating-the-custom-control"></a>创建自定义控件

通过子类化 [`View`](xref:Xamarin.Forms.View) 类（如下面的代码示例中所示），可以创建自定义控件：

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

`CameraPreview` 自定义控件在 .NET Standard 库项目中创建，该控件定义控件的 API。 自定义控件公开 `Camera` 属性，该属性用于控制应该从设备的前置摄像头还是后置摄像头显示视频流。 如果在创建控件时没有为 `Camera` 属性指定值，则默认为指定后置摄像头。

## <a name="consuming-the-custom-control"></a>使用自定义控件

通过在自定义控件元素上声明 `CameraPreview` 自定义控件位置的命名空间并使用命名空间前缀，可以在 .NET Standard 库项目的 XAML 中引用该自定义控件。 以下代码示例展示了 XAML 页可以如何使用 `CameraPreview` 自定义控件：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`local` 命名空间前缀可以命名为任何内容。 但是，`clr-namespace` 和 `assembly` 值必须与自定义控件的详细信息相匹配。 声明命名空间后，前缀用于引用自定义控件。

以下代码示例展示了 C# 页可以如何使用 `CameraPreview` 自定义控件：

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

`CameraPreview` 自定义控件的实例将用于显示设备摄像头的预览视频流。 除了选择为 `Camera` 属性指定值以外，还将在自定义呈现器中执行控件的自定义。

现在可以向每个应用程序项目添加自定义呈现器，以便创建特定于平台的摄像头预览控件。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建呈现自定义控件的 `ViewRenderer<T1,T2>` 类的子类。 第一个类型参数应该是使用呈现器的自定义控件，在本例中为 `CameraPreview`。 第二个类型参数应该是实现自定义控件的本机控件。
1. 替代呈现自定义控件的 `OnElementChanged` 方法，并编写逻辑以自定义控件。 创建相应的 Xamarin.Forms 控件时将调用此方法。
1. 向自定义呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 自定义控件。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，都可选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。 但是，呈现[视图](xref:Xamarin.Forms.View)元素时，每个平台项目中都需要自定义呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![CameraPreview 自定义呈现器项目的职责](view-images/solution-structure.png)

`CameraPreview` 自定义控件由特定于平台的呈现器类呈现，这些类全都派生自各平台的 `ViewRenderer` 类。 这导致每个 `CameraPreview` 自定义控件都使用特定于平台的控件呈现，如以下屏幕截图所示：

![每个平台上的 CameraPreview](view-images/screenshots.png)

`ViewRenderer` 类公开 `OnElementChanged` 方法，创建 Xamarin.Forms 自定义控件时调用此方法以呈现对应的本机控件。 此方法采用 `ElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素 。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `CameraPreview` 实例的引用。

在每个特定于平台的呈现器类中，`OnElementChanged` 方法的替代版本是执行本机控件实例化和自定义的位置。 `SetNativeControl` 方法应该用于实例化本机控件，此方法还会将控件引用分配给 `Control` 属性。 此外，可以通过 `Element` 属性获取正在呈现的 Xamarin.Forms 控件的引用。

在某些情况下，可以多次调用 `OnElementChanged` 方法。 因此，为了防止内存泄漏，在实例化新的本机控件时务必要格外小心。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。 此外，仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应创建、配置该控件并订阅事件处理程序。 同样，仅当呈现器所附加到的元素更改时，才应取消订阅任何订阅的事件处理程序。 采用此方法将有助于创建不会遭受内存泄漏的高性能自定义呈现器。

> [!IMPORTANT]
> 仅当 `e.NewElement` 不是 `null` 时，才应调用 `SetNativeControl` 方法。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各节讨论每个平台特定的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

以下代码示例展示了适用于 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                if (Control == null) {
                  uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                  SetNativeControl (uiCameraPreview);
                }
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

如果 `Control` 属性为 `null`，则调用 `SetNativeControl` 方法以实例化新的 `UICameraPreview` 控件并将其引用分配给 `Control` 属性。 `UICameraPreview` 控件是特定于平台的自定义控件，它使用 `AVCapture` API 提供摄像头的预览视频流。 该控件公开由 `OnCameraPreviewTapped` 方法处理的 `Tapped` 事件，以便在点击视频预览时停止和启动视频预览。 自定义呈现器附加到新的 Xamarin.Forms 元素时会订阅 `Tapped` 事件，并且仅当呈现器所附加到的元素更改时才会取消订阅。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

以下代码示例展示了适用于 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  cameraPreview = new CameraPreview(Context);
                  SetNativeControl(cameraPreview);
                }
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

如果 `Control` 属性为 `null`，则调用 `SetNativeControl` 方法以实例化新的 `CameraPreview` 控件并将其引用分配给 `Control` 属性。 `CameraPreview` 控件是特定于平台的自定义控件，它使用 `Camera` API 提供摄像头的预览视频流。 如果自定义呈现器附加到新的 Xamarin.Forms 元素，则之后会配置 `CameraPreview` 控件。 此配置涉及创建新的本机 `Camera` 对象以访问特定硬件摄像头，以及注册事件处理程序以处理 `Click` 事件。 此处理程序转而将在点击视频预览时停止和启动视频预览。 如果呈现器所附加到的 Xamarin.Forms 元素更改，则会取消订阅 `Click` 事件。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

以下代码示例展示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  ...
                  _captureElement = new CaptureElement();
                  _captureElement.Stretch = Stretch.UniformToFill;

                  SetupCamera();
                  SetNativeControl(_captureElement);
                }
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

如果 `Control` 属性为 `null`，则实例化新的 `CaptureElement` 并调用 `SetupCamera` 方法，该方法使用 `MediaCapture` API 提供摄像头的预览视频流。 之后调用 `SetNativeControl` 方法以将 `CaptureElement` 实例的引用分配给 `Control` 属性。 `CaptureElement` 控件公开由 `OnCameraPreviewTapped` 方法处理的 `Tapped` 事件，以便在点击视频预览时停止和启动视频预览。 自定义呈现器附加到新的 Xamarin.Forms 元素时会订阅 `Tapped` 事件，并且仅当呈现器所附加到的元素更改时才会取消订阅。

> [!NOTE]
> 停止和释放 UWP 应用程序中提供对摄像头的访问权限的对象至关重要。 如果不这样做，可能会影响尝试访问设备的摄像头的其他应用程序。 有关详细信息，请参阅[显示摄像头预览](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>总结

本文演示了如何为 Xamarin.Forms 自定义控件创建自定义呈现器，用于显示设备摄像头的预览视频流。 Xamarin.Forms 自定义用户界面控件应派生自 [`View`](xref:Xamarin.Forms.View) 类，该类用于在屏幕上放置布局和控件。

## <a name="related-links"></a>相关链接

- [CustomRendererView（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-view)