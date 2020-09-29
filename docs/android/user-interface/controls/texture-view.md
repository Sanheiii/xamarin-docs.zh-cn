---
title: Xamarin TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 3085131e251a787965c91216106becb6c954da85
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457700"
---
# <a name="xamarinandroid-textureview"></a>Xamarin TextureView

`TextureView`类是一个视图，它使用硬件加速2d 呈现来启用视频或 OpenGL 内容流。 例如，以下屏幕截图显示了 `TextureView` 从设备相机显示实时源：

[![设备相机中的实时图像的示例屏幕截图](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

与 `SurfaceView` 类（也可用于显示 OpenGL 或视频内容）不同，TextureView 不会呈现在单独的窗口中。
因此，可以 `TextureView` 像任何其他视图一样支持视图转换。 例如，旋转可以通过设置其 `TextureView` `Rotation` 属性（通过设置其属性等）来实现 `Alpha` 。

因此，通过， `TextureView` 我们现在可以执行一些操作，例如显示照相机中的实时流并对其进行转换，如以下代码所示：

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

上面的代码 `TextureView` 在活动的方法中创建一个实例 `OnCreate` ，并将该活动设置为 `TextureView` 的 `SurfaceTextureListener` 。 若为 `SurfaceTextureListener` ，则活动实现 `TextureView.ISurfaceTextureListener` 接口。 `OnSurfaceTextAvailable`当 `SurfaceTexture` 已准备好使用时，系统将调用方法。 在此方法中，我们将采用传入的， `SurfaceTexture` 并将其设置为相机的预览纹理。 然后，可以根据 `Rotation` 上述示例中所示，随意执行基于视图的常规操作，如设置和 `Alpha` 。 在设备上运行的生成的应用程序如下所示：

[![在设备上运行的应用的示例，显示图像](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用 `TextureView` ，必须启用硬件加速，默认情况下，它将在 API 级别14下启用。 此外，由于本示例使用了照相机，因此 `android.permission.CAMERA` `android.hardware.camera` 必须在 **AndroidManifest.xml**中设置权限和功能。

## <a name="related-links"></a>相关链接

- [TextureViewDemo (示例) ](/samples/xamarin/monodroid-samples/textureviewdemo)/) 