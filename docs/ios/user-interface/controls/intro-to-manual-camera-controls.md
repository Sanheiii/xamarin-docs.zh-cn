---
title: 在 Xamarin 中手动照相机控件
description: 本文档介绍如何将 iOS AVFoundation 框架与 Xamarin 一起用于启用手动照相机控制。 手动相机控制允许用户控制焦点、白平衡和曝光设置。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 04c33b59e2a1bab7fb401ffdc49d120bcfd679d0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436825"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin 中手动照相机控件

手动相机控制（由 `AVFoundation Framework` iOS 8 中的提供）允许移动应用程序完全控制 ios 设备的照相机。 这一精细的控制级别可用于创建专业级相机应用程序，并通过在拍摄静止图像或视频的同时调整照相机的参数来提供艺术家组合。

这些控件还可用于开发科学或工业应用程序，在此类应用程序中，结果不太适合图像的正确性或优点，并且更多地强调了如何突出显示所采用的图像的某个功能或元素。

## <a name="avfoundation-capture-objects"></a>AVFoundation 捕获对象

无论是在 iOS 设备上使用摄像机拍摄视频还是静止图像，用于捕获这些映像的进程在很大程度上都是相同的。 使用默认的自动照相机控件的应用程序或使用新的手动照相机控件的应用程序是如此：

 [![AVFoundation 捕获对象概述](intro-to-manual-camera-controls-images/image1.png)](intro-to-manual-camera-controls-images/image1.png#lightbox)

输入通过将从 `AVCaptureDeviceInput` 传入 `AVCaptureSession` `AVCaptureConnection` 。 结果可能输出为静止图像或视频流。 整个过程由控制 `AVCaptureDevice` 。

## <a name="manual-controls-provided"></a>提供的手动控制

使用 iOS 8 提供的新 Api，应用程序可以控制以下相机功能：

- **手动焦点** –通过允许最终用户直接控制焦点，应用程序可以更好地控制所采用的图像。
- **手动曝光** –通过提供对曝光的手动控制，应用程序可以为用户提供更大的自由度，并使用户能够实现风格的外观。
- **手动白平衡** –用白色余额调整图像中的颜色，通常是为了使其看起来真实。 不同光源的颜色温度不同，用于捕获图像的照相机设置会进行调整，以弥补这些差异。 同样，通过允许用户控制白平衡，用户可以进行自动调整。

iOS 8 提供对现有 iOS Api 的扩展和增强功能，以提供对映像捕获过程的精细控制。

## <a name="bracketed-capture"></a>带括号捕获

带括号的捕获基于上面提供的手动相机控件中的设置，并允许应用程序以各种不同的方式捕获一段时间。

简单地说，用括号捕获是指使用各种设置从图片到图片的静止图像。

## <a name="requirements"></a>要求

若要完成本文中所述的步骤，需要满足以下要求：

- 需要在开发人员的计算机上安装和配置**Xcode 7 + 和 ios 8 或更高版本**的 Xcode 7 和 ios 8 或更高版本的 api。
- **Visual Studio for Mac** –应在用户设备上安装和配置 Visual Studio for Mac 的最新版本。
- **ios 8 设备** -运行最新版本 ios 8 的 ios 设备。 无法在 iOS 模拟器中测试相机功能。

## <a name="general-av-capture-setup"></a>常规 AV 捕获设置

在 iOS 设备上录制视频时，某些一般的安装代码始终是必需的。 本部分介绍了从 iOS 设备相机记录视频所需的最小设置，并将该视频实时显示在中 `UIImageView` 。

### <a name="output-sample-buffer-delegate"></a>输出示例缓冲区委托

首先需要的是用于监视示例输出缓冲区的委托，并 `UIImageView` 在应用程序 UI 中将从缓冲区中采集的图像显示为。

以下例程将监视示例缓冲区并更新 UI：

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

在此例程就绪 `AppDelegate` 后，可以修改以打开 AV 捕获会话来记录实时视频源。

### <a name="creating-an-av-capture-session"></a>创建 AV 捕获会话

AV 捕获会话用于控制来自 iOS 设备照相机的实时视频记录，并且需要将视频导入到 iOS 应用程序中。 由于示例 `ManualCameraControl` 应用程序在多个不同位置使用捕获会话，因此它将在中进行配置， `AppDelegate` 并可用于整个应用程序。

执行以下操作来修改应用程序的 `AppDelegate` ，并添加必要的代码：

1. 双击 `AppDelegate.cs` "解决方案资源管理器中的文件以将其打开以进行编辑。
1. 在文件的开头添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. 将以下私有变量和计算属性添加到 `AppDelegate` 类：

    ```csharp
    #region Private Variables
    private NSError Error;
    #endregion

    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```

1. 重写完成的方法，并将其更改为：

    ```csharp
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;

        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }

        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }

        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);

        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();

        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }

        // Attach input to session
        Session.AddInput (Input);

        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;

        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);

        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```

1. 保存对文件所做的更改。

完成此代码后，可以轻松实现手动照相机控制来进行试验和测试。

## <a name="manual-focus"></a>手动定位

通过允许最终用户直接控制焦点，应用程序可以为所拍摄的图像提供更多的艺术控件。

例如，专业摄影师可以将图像的聚焦柔化，以实现 [Bokeh 效果](https://en.wikipedia.org/wiki/Bokeh)：

[![Bokeh 效果](intro-to-manual-camera-controls-images/image2.png)](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，创建一个 [焦点效果](http://www.mediacollege.com/video/camera/focus/pull.html)，如：

[![焦点拉取效果](intro-to-manual-camera-controls-images/image3.png)](intro-to-manual-camera-controls-images/image3.png#lightbox)

对于科学家或医疗应用程序的作者，应用程序可能需要以编程方式移动镜头来实现试验。 无论采用哪种方式，新的 API 都允许最终用户或应用程序在拍摄图像时控制焦点。

### <a name="how-focus-works"></a>焦点的工作方式

在讨论如何在 IOS 8 应用程序中控制焦点的详细信息之前。 让我们快速了解一下如何在 iOS 设备中工作：

[![IOS 设备中的焦点如何工作](intro-to-manual-camera-controls-images/image4.png)](intro-to-manual-camera-controls-images/image4.png#lightbox)

浅在 iOS 设备上进入相机镜头，并专注于图像传感器。 从传感器控制镜头的距离，其中焦点 (图像将显示在尖) 的区域，与传感器的关系。 该镜头远离传感器，距离对象看起来尖，接近的对象看似尖。

在 iOS 设备中，镜头通过磁体和弹簧更接近或更接近传感器。 因此，不可能精确定位镜头，因为它会因设备而异，并且可能会受参数（如设备的方向或设备的使用时间和弹簧）影响。

### <a name="important-focus-terms"></a>重要的重点术语

在处理焦点时，开发人员应熟悉几个术语：

- **字段深度** –最接近和最远的聚焦对象之间的距离。
- **宏** -这是 "焦点" 频谱的近距离，是镜头可聚焦的最接近的距离。
- **无穷** –这是最远的重心，是镜头可聚焦的距离。
- **Hyperfocal 距离** –这是在焦点范围内的点，其中的最远对象刚好处于焦点的最末尾。 换句话说，这是最大化字段深度的焦点位置。
- **镜头位置** –这就是控制以上所有术语的内容。 这是镜头距传感器的距离，进而成为焦点控制器。

考虑到这些术语和知识，可以在 iOS 8 应用程序中成功实现新的手动焦点控制。

### <a name="existing-focus-controls"></a>现有焦点控件

iOS 7 及更早版本，通过属性将现有的焦点控件提供 `FocusMode` 为：

- `AVCaptureFocusModeLocked` –焦点锁定在单个焦点点上。
- `AVCaptureFocusModeAutoFocus` –照相机会将镜头扫描到所有焦点上，直到它找到清晰的焦点，然后停留在该处。
- `AVCaptureFocusModeContinuousAutoFocus` –当 refocuses 检测到超出焦点状态时，照相机就会出现问题。

现有的控件还通过属性提供了一种可设置的相关点 `FocusPointOfInterest` ，以便用户可以点击以将焦点放在特定区域。 应用程序还可以通过监视属性来跟踪镜头运动 `IsAdjustingFocus` 。

此外，范围限制由属性提供， `AutoFocusRangeRestriction` 如下所示：

- `AVCaptureAutoFocusRangeRestrictionNear` –将 autofocus 限制为附近深度。 用于扫描 QR 码或条形码等情况。
- `AVCaptureAutoFocusRangeRestrictionFar` –限制 autofocus 到远处的深度。 在视图 (的 "视图" 字段中，这种情况非常有用，例如，) 的窗口框架。

最后有 `SmoothAutoFocus` 一个属性，该属性减慢了自动焦点算法，并以较小的增量进行操作，以避免在录制视频时移动项目。

### <a name="new-focus-controls-in-ios-8"></a>IOS 8 中的新焦点控件

除了 iOS 7 及更高版本提供的功能之外，现在还可以使用以下功能在 iOS 8 中控制焦点：

- 锁定焦点时完全手动控制镜头位置。
- 任何焦点模式下的镜头位置的键-值观察。

为了实现上述功能， `AVCaptureDevice` 类已修改为包含一个只读属性，该 `LensPosition` 属性用于获取相机镜头的当前位置。

若要手动控制镜头位置，捕获设备必须处于锁定的焦点模式。 示例：

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`捕获设备的方法用于调整照相机镜头的位置。 在更改生效时，可以提供可选的回调例程来获取通知。 示例：

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

如上面的代码所示，必须锁定捕获设备进行配置，然后才能进行更改。 有效的镜头位置值介于0.0 和1.0 之间。

### <a name="manual-focus-example"></a>手动焦点示例

使用常规 AV 捕获安装代码后， `UIViewController` 可将添加到应用程序的情节提要，并按如下所示进行配置：

[![UIViewController 可以添加到应用程序情节提要，并按如下所示进行配置](intro-to-manual-camera-controls-images/image5.png)](intro-to-manual-camera-controls-images/image5.png#lightbox)

视图包含以下主要元素：

- `UIImageView`将显示视频源的。
- `UISegmentedControl`将焦点模式从自动更改为锁定状态的。
- `UISlider`将显示并更新当前镜头位置的。

执行以下操作以连接视图控制器以进行手动焦点控制：

1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 添加以下计算属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 重写 `ViewDidLoad` 方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. 重写 `ViewDidAppear` 方法并添加以下内容以在视图加载时开始记录：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 当照相机处于 Auto 模式时，滑块将在照相机调整焦点时自动移动：

    [![当照相机调整此示例应用中的焦点时，滑块将自动移动](intro-to-manual-camera-controls-images/image6.png)](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 点击锁定段，并拖动 "位置" 滑块以手动调整镜头位置：

    [![手动调整镜头位置](intro-to-manual-camera-controls-images/image7.png)](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止应用程序。

上面的代码演示了如何在照相机处于自动模式时监视镜头位置，或使用滑块控制处于锁定模式的镜头位置。

## <a name="manual-exposure"></a>手动公开

"公开" 是指相对于源亮度的图像亮度，由感应器的点击率、传感器 (ISO 映射) 的程度确定的程度。 通过提供对公开的手动控制，应用程序可以为最终用户提供更多自由，并允许他们实现风格化的外观。

使用手动公开控制，用户可以从不切实际明亮到暗色和随和：

[![示例显示了从不切实际明亮到暗色和随和的曝光](intro-to-manual-camera-controls-images/image8.png)](intro-to-manual-camera-controls-images/image8.png#lightbox)

同样，这可以使用科学应用程序的编程控件或应用程序用户界面提供的手动控件来自动完成。 无论采用哪种方式，新的 iOS 8 公开 Api 都提供对相机曝光设置的精细控制。

### <a name="how-exposure-works"></a>曝光如何工作

在讨论 IOS 8 应用程序中的公开公开的详细信息之前。 让我们简单了解一下曝光的工作方式：

[![曝光如何工作](intro-to-manual-camera-controls-images/image9.png)](intro-to-manual-camera-controls-images/image9.png#lightbox)

共同控制其公开的三个基本元素是：

- **快门速度** -这是打开快门以使相机传感器亮起的时间长度。 快门打开的时间越短，就越少，图像越 (降低运动模糊) 。 快门打开的时间越长，会有更多的光线，并产生更多的运动模糊。
- **ISO 映射** –这是从胶卷照片借用的一项术语，表示电影中的化学用品的灵敏度。 电影中的低 ISO 值具有更少的颜色和更精细的颜色重现;数字传感器上的低 ISO 值具有更少的传感器噪音，但亮度更少。 ISO 值越高，图像越快，但传感器噪音越多。 数字传感器上的 "ISO" 是 [电子增益](https://en.wikipedia.org/wiki/Gain)的度量，而不是物理功能。
- **镜头口径** –这是镜头打开的大小。 在所有 iOS 设备上，镜头口径都是固定的，因此，仅有两个可用于调整曝光度的值为快门速度和 ISO。

### <a name="how-continuous-auto-exposure-works"></a>连续自动曝光的工作方式

在了解手动曝光的工作方式之前，最好了解 iOS 设备中的持续自动曝光工作方式。

[![IOS 设备中连续自动曝光的工作方式](intro-to-manual-camera-controls-images/image10.png)](intro-to-manual-camera-controls-images/image10.png#lightbox)

第一种是自动曝光块，它可以计算理想的曝光度，并持续送入计数统计信息。它使用此信息来计算 ISO 和快门速度的最佳组合，以使场景良好发亮。 此循环称为自动曝光循环。

### <a name="how-locked-exposure-works"></a>锁定的曝光如何工作

接下来，让我们看看在 iOS 设备中锁定的曝光如何工作。

[![IOS 设备中锁定的曝光如何工作](intro-to-manual-camera-controls-images/image11.png)](intro-to-manual-camera-controls-images/image11.png#lightbox)

同样，您具有自动曝光块，尝试计算最佳 Io 和持续时间值。 但是，在此模式下，自动曝光块与计量统计信息引擎断开连接。

### <a name="existing-exposure-controls"></a>现有公开控件

iOS 7 及更高版本，可通过属性提供以下现有公开控制 `ExposureMode` ：

- `AVCaptureExposureModeLocked` –对场景进行一次采样，并在场景中使用这些值。
- `AVCaptureExposureModeContinuousAutoExposure` –对场景进行连续采样，以确保其正常亮起。

`ExposurePointOfInterest`可用于点击以通过选择要在其上公开的目标对象来公开场景，应用程序可以监视 `AdjustingExposure` 属性以查看何时调整曝光。

### <a name="new-exposure-controls-in-ios-8"></a>IOS 8 中的新公开控制

除了 iOS 7 及更高版本提供的功能之外，现在还可以使用以下功能来控制 iOS 8 中的公开：

- 完全手动自定义公开。
- 获取、设置和键-值观察 IOS 和快门速度 (持续时间) 。

为了实现上述功能，已添加了新 `AVCaptureExposureModeCustom` 模式。 如果中的照相机是自定义模式，则可以使用以下代码调整曝光持续时间和 ISO：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在 "自动" 和 "锁定" 模式下，应用程序可以使用以下代码调整自动曝光例程的偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小值和最大值设置范围取决于运行应用程序的设备，因此永远不应进行硬编码。 而是使用以下属性来获取最小值和最大值范围：

- `CaptureDevice.MinExposureTargetBias`
- `CaptureDevice.MaxExposureTargetBias`
- `CaptureDevice.ActiveFormat.MinISO`
- `CaptureDevice.ActiveFormat.MaxISO`
- `CaptureDevice.ActiveFormat.MinExposureDuration`
- `CaptureDevice.ActiveFormat.MaxExposureDuration`

如上面的代码所示，必须锁定捕获设备进行配置，然后才能进行更改。

### <a name="manual-exposure-example"></a>手动曝光示例

使用常规 AV 捕获安装代码后， `UIViewController` 可将添加到应用程序的情节提要，并按如下所示进行配置：

[![UIViewController 可以添加到应用程序情节提要，并按如下所示进行配置](intro-to-manual-camera-controls-images/image12.png)](intro-to-manual-camera-controls-images/image12.png#lightbox)

视图包含以下主要元素：

- `UIImageView`将显示视频源的。
- `UISegmentedControl`将焦点模式从自动更改为锁定状态的。
- `UISlider`将显示和更新偏移、持续时间、ISO 和偏差的四个控件。

执行以下操作以连接视图控制器以进行手动公开控制：

1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```

1. 添加以下计算属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 重写 `ViewDidLoad` 方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;

        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;

        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });

            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });

            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });

            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        ISO.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        Bias.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. 重写 `ViewDidAppear` 方法并添加以下内容以在视图加载时开始记录：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 当照相机处于 Auto 模式时，滑块会在相机调整曝光时自动移动：

    [![当照相机调整曝光度时，滑块会自动移动](intro-to-manual-camera-controls-images/image13.png)](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 点击锁定段并拖动偏向滑块，手动调整自动曝光度的偏差：

    [![手动调整自动曝光的偏差](intro-to-manual-camera-controls-images/image14.png)](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 点击自定义段，并拖动 "持续时间" 和 "ISO" 滑块以手动控制曝光：

    [![拖动 "持续时间" 和 "ISO" 滑块以手动控制曝光](intro-to-manual-camera-controls-images/image15.png)](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止应用程序。

上面的代码演示了如何在照相机处于自动模式时监视曝光设置，以及如何使用滑块来控制处于锁定或自定义模式时的曝光度。

## <a name="manual-white-balance"></a>人工白平衡

白平衡控制允许用户调整图像中的 colosr 余额，使其看起来更逼真。 不同光源的颜色温度不同，必须调整用于捕获图像的照相机设置，以弥补这些差异。 同样，通过允许用户控制白平衡，他们可以进行专业调整，使自动例程无法实现艺术效果。

[![显示人工白余额调整的示例图像](intro-to-manual-camera-controls-images/image16.png)](intro-to-manual-camera-controls-images/image16.png#lightbox)

例如，日光具有 blueish 铸造，而 tungsten incandescent 灯的色调为黄色-橙色。  (令困惑，"冷" 颜色的颜色温度高于 "热" 颜色。 颜色温度是一种物理度量，而不是一种感知。 ) 

对于颜色温度之间的差异，用户想法非常不错，但这是相机无法完成的工作。 相机的工作方式是提升相对色谱的颜色，以调整颜色差异。

新的 iOS 8 公开 API 允许应用程序控制进程，并提供对照相机白平衡设置的精细控制。

### <a name="how-white-balance-works"></a>白平衡的工作原理

在讨论 IOS 8 应用程序中对白平衡的详细信息之前。 让我们简单了解一下白平衡的工作方式：

在学习颜色感知的 [同时，CIE 1931 RGB 颜色空间和 CIE 1931 XYZ color space](https://en.wikipedia.org/wiki/CIE_1931_color_space) 是第一个数学定义的颜色空间。 它们是由国际佣金在 1931 (CIE) 上创建的。

[![CIE 1931 RGB 颜色空间和 CIE 1931 XYZ 颜色空间](intro-to-manual-camera-controls-images/image17.png)](intro-to-manual-camera-controls-images/image17.png#lightbox)

上面的图表显示了人们眼中看到的所有颜色，从深蓝色到鲜绿色到红色。 关系图上的任何点都可以用 X 和 Y 值绘制，如上面的关系图中所示。

如图中所示，有 X 和 Y 值可绘制在图形上，这些值将在人机愿景范围之外，因此，照相机无法重现这些颜色。

上面的图表中的较小曲线称为 [Planckian 出自](https://en.wikipedia.org/wiki/Planckian_locus)，它表示色温) 的色温温度 (，蓝色两侧 (温度) ，红色两侧的数字越小， (温度) 。 这些方法对典型照明情况非常有用。

在混合照明条件下，白平衡调整需要与 Planckian 出自进行偏离，才能进行所需的更改。 在这些情况下，需要将调整移动到 CIE 刻度的绿色或红色/品红端。

iOS 设备通过提高相对颜色增益来补偿颜色转换。 例如，如果场景的蓝色太多，则会提升红色增益以进行补偿。 这些增益值是针对特定设备校准的，因此它们依赖于设备。

### <a name="existing-white-balance-controls"></a>现有的白平衡控件

iOS 7 和更高版本通过属性提供了以下现有的白平衡控件 `WhiteBalanceMode` ：

- `AVCapture WhiteBalance ModeLocked` –对场景进行一次采样，并在场景中使用这些值。
- `AVCapture WhiteBalance ModeContinuousAutoExposure` –对场景进行连续采样，以确保其平衡。

应用程序可以监视 `AdjustingWhiteBalance` 属性，以查看调整曝光的时间。

### <a name="new-white-balance-controls-in-ios-8"></a>IOS 8 中的新白平衡控制

除了 iOS 7 及更高版本提供的功能之外，现在还可以使用以下功能在 iOS 8 中控制白平衡：

- 完全手动控制设备 RGB 增益。
- 获取、设置和键-值观察设备 RGB 增益。
- 使用灰色卡支持白平衡。
- 与设备无关颜色空间之间的转换例程。

为了实现上述功能，已 `AVCaptureWhiteBalanceGain` 添加了以下成员的结构：

- `RedGain`
- `GreenGain`
- `BlueGain`

最大白余额收益目前为四 (4) ，可以从 `MaxWhiteBalanceGain` 属性准备。 因此，合法范围从一个 (1) 到 `MaxWhiteBalanceGain` 当前 (4 个) 。

`DeviceWhiteBalanceGains`属性可用于观察当前的值。 `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`当照相机处于锁定的白平衡模式时，使用调整余额提升。

#### <a name="conversion-routines"></a>转换例程

转换例程已添加到 iOS 8，可帮助转换为与设备无关的颜色空间。 若要实现转换例程，已 `AVCaptureWhiteBalanceChromaticityValues` 添加了以下成员的结构：

- `X` -值介于0和1之间。
- `Y` -值介于0和1之间。

`AVCaptureWhiteBalanceTemperatureAndTintValues`还添加了一个结构，其中包含以下成员：

- `Temperature` -为开氏度的浮点值。
- `Tint` -从0到150的偏移量，正值为绿色，正值方向为负值，在洋红色方向为负值。

使用 `CaptureDevice.GetTemperatureAndTintValues` 和方法在 `CaptureDevice.GetDeviceWhiteBalanceGains` 温度和淡色之间进行转换，CHROMATICITY 和 RGB 增益颜色空间。

> [!NOTE]
> 转换例程越精确，要转换的值越接近于 Planckian 出自。

#### <a name="gray-card-support"></a>灰色卡支持

Apple 使用术语 "灰色" 来指代 iOS 8 中内置的灰色卡支持。 它使用户能够专注于至少涵盖帧中心50% 的物理灰色卡，并使用它来调整白平衡。 灰色卡的用途是实现显示为中性的白色。

在应用程序中，可以通过提示用户将物理灰色卡置于照相机的前面，监视 `GrayWorldDeviceWhiteBalanceGains` 属性并等待直到值向下滚动，来实现此目的。

然后，应用程序 `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` 使用属性中的值来应用更改，从而锁定方法的白平衡收益 `GrayWorldDeviceWhiteBalanceGains` 。

必须先锁定捕获设备的配置，然后才能进行更改。

### <a name="manual-white-balance-example"></a>手册白平衡示例

使用常规 AV 捕获安装代码后， `UIViewController` 可将添加到应用程序的情节提要，并按如下所示进行配置：

[![UIViewController 可以添加到应用程序情节提要，并按如下所示进行配置](intro-to-manual-camera-controls-images/image18.png)](intro-to-manual-camera-controls-images/image18.png#lightbox)

视图包含以下主要元素：

- `UIImageView`将显示视频源的。
- `UISegmentedControl`将焦点模式从自动更改为锁定状态的。
- `UISlider`将显示和更新温度和色调的两个控件。
- `UIButton`用于对灰色 (灰色世界) 空间取样并使用这些值设置白平衡的。

执行以下操作以将视图控制器连接到手动白平衡控件：

1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 添加以下计算属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 添加以下私有方法以设置新的白平衡温度和淡色：

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }

    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```

1. 重写 `ViewDidLoad` 方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ```

1. 重写 `ViewDidAppear` 方法并添加以下内容以在视图加载时开始记录：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 保存对代码所做的更改并运行应用程序。
1. 当照相机处于 Auto 模式时，滑块将自动移动，因为照相机调整了白平衡：

    [![当照相机调整白平衡时，滑块会自动移动](intro-to-manual-camera-controls-images/image19.png)](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 点击锁定段，并拖动 Temp 和淡色滑块以手动调整白平衡：

    [![拖动 Temp 和淡色滑块以手动调整白色余额](intro-to-manual-camera-controls-images/image20.png)](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 在仍选中锁定段的情况下，将物理灰色卡置于照相机的前面，然后点击灰色卡按钮，以将白平衡调整到灰色世界：

    [![点击灰色卡按钮，将白平衡调整到灰色世界](intro-to-manual-camera-controls-images/image21.png)](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止应用程序。

上面的代码演示了如何在相机处于自动模式时监视白色平衡设置，或使用滑杆控制处于锁定模式下的白平衡。

## <a name="bracketed-capture"></a>带括号捕获

带括号的捕获基于上面提供的手动相机控件中的设置，并允许应用程序以各种不同的方式捕获一段时间。

简单地说，用括号捕获是指使用各种设置从图片到图片的静止图像。

[![括号捕获的工作原理](intro-to-manual-camera-controls-images/image22.png)](intro-to-manual-camera-controls-images/image22.png#lightbox)

使用 iOS 8 中带括号的捕获，应用程序可以预设一系列手动相机控件，发出单个命令，并使当前场景为每个手动预设返回一系列图像。

### <a name="bracketed-capture-basics"></a>带括号的捕获基础知识

同样，括在括号中的捕获是指使用各种设置从图片到图片的静止图像。 可用的括起来捕获的类型包括：

- **自动曝光支架** –所有映像都具有不同的偏移量。
- **手动曝光支架** -其中所有图像的快门速度都不同 (持续时间) 和 ISO 量。
- **简单的突发括号** -一系列快速的静止图像。

### <a name="new-bracketed-capture-controls-in-ios-8"></a>IOS 8 中的新括号内捕获控件

所有括起来的捕获命令都是在类中实现的 `AVCaptureStillImageOutput` 。 使用 `CaptureStillImageBracket` 方法可通过给定的设置数组获取一系列图像。

实现了两个新类来处理设置：

- `AVCaptureAutoExposureBracketedStillImageSettings` –它有一个属性，  `ExposureTargetBias` 用于为自动曝光支架设置偏置。
- `AVCaptureManual`  `ExposureBracketedStillImageSettings` –它有两个属性：  `ExposureDuration` 和  `ISO` ，用于为手动曝光支架设置快门速度和 ISO。

### <a name="bracketed-capture-controls-dos-and-donts"></a>用括号括起来的捕获控件

#### <a name="dos"></a>应做事项

以下列表列出了在 iOS 8 中使用括号内捕获控件时应执行的操作：

- 通过调用方法为最坏大小写捕获情况准备应用  `PrepareToCaptureStillImageBracket` 。
- 假设示例缓冲区来自同一共享池。
- 若要释放前面的准备调用分配的内存，请再次调用  `PrepareToCaptureStillImageBracket` 并向其发送一个对象的数组。

#### <a name="donts"></a>禁止事项

以下列表列出了在 iOS 8 中使用括号内捕获控件时不应执行的操作：

- 不要在单个捕获中混合使用括号内的捕获设置类型。
- 不要  `MaxBracketedCaptureStillImageCount` 在单个捕获中请求多于个图像。

### <a name="bracketed-capture-details"></a>括起来的捕获详细信息

在 iOS 8 中使用带括号的捕获时，应考虑以下详细信息：

- 带括号的设置暂时替代  `AVCaptureDevice` 设置。
- 忽略闪光和静止图像稳定设置。
- 所有图像必须使用相同的输出格式 (jpeg、png 等 ) 
- 视频预览可能会丢弃帧。
- 与 iOS 8 兼容的所有设备均支持带括号捕获。

考虑到此信息，让我们看一下在 iOS 8 中使用带括号的捕获的示例。

### <a name="bracket-capture-example"></a>方括号捕获示例

使用常规 AV 捕获安装代码后， `UIViewController` 可将添加到应用程序的情节提要，并按如下所示进行配置：

[![UIViewController 可以添加到应用程序情节提要，并按如下所示进行配置](intro-to-manual-camera-controls-images/image23.png)](intro-to-manual-camera-controls-images/image23.png#lightbox)

视图包含以下主要元素：

- `UIImageView`将显示视频源的。
- 三个  `UIImageViews` 将显示捕获结果的。
- `UIScrollView`用于容纳视频源和结果视图的。
- ，  `UIButton` 用于采用某些预设设置进行带括号的捕获。

执行以下操作以将视图控制器连接到带外捕获：

1. 添加以下 using 语句：

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```

1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```

1. 添加以下计算属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```

1. 添加以下私有方法以生成所需的输出图像视图：

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {

        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));

        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");

        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);

        // Add to scrolling view
        ScrollView.AddSubview (imageView);

        // Return new image view
        return imageView;
    }
    #endregion
    ```

1. 重写 `ViewDidLoad` 方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);

        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));

        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };

        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;

            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });

            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);

                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```

1. 重写 `ViewDidAppear` 方法并添加以下代码：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
        }
    }

    ```

1. 保存对代码所做的更改并运行应用程序。
1. 将场景画为一段，然后点击 "捕获括号" 按钮：

    [![将场景画为一段](intro-to-manual-camera-controls-images/image24.png)](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 向左轻扫可查看按括号捕获拍摄的三个图像：

    [![向左轻扫可查看按括号捕获拍摄的三个图像](intro-to-manual-camera-controls-images/image25.png)](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止应用程序。

上面的代码演示了如何在 iOS 8 中配置和采用自动曝光包含的捕获。

## <a name="summary"></a>总结

在本文中，我们介绍了 iOS 8 提供的新手动摄像头控制，并介绍了其功能及其工作原理的基础知识。 我们提供了手动重点、手动曝光和人工白平衡的示例。 最后，我们提供了一个示例，其中使用前面讨论过的手动照相机控件

## <a name="related-links"></a>相关链接

- [ManualCameraControls (示例) ](/samples/xamarin/ios-samples/manualcameracontrols)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)