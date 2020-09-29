---
title: 为 Android 磨损1.0 创建手表面
description: 本指南介绍如何实现适用于 Android 磨损1.0 的自定义监视人脸服务。 提供了一种循序渐进的说明，用于构建一个去除的数字观看人脸服务，后跟更多代码以创建模拟样式的手表面。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 79dcab73c379cecb5108a88cc8bbb2eab33af05c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457102"
---
# <a name="creating-a-watch-face"></a>创建表盘

_本指南介绍如何实现适用于 Android 磨损1.0 的自定义监视人脸服务。提供了一种循序渐进的说明，用于构建一个去除的数字观看人脸服务，后跟更多代码以创建模拟样式的手表面。_

## <a name="overview"></a>概述

在本演练中，将创建一个基本的 "监视人脸" 服务，以说明创建自定义 Android 磨损1.0 监视面的基本要素。
初始监视人脸服务显示一个简单的数字手表，其中显示当前时间（以小时和分钟为单位）：

[![数字观看面](creating-a-watchface-images/01-initial-face.png "初始数字监视面部的示例屏幕截图")](creating-a-watchface-images/01-initial-face.png#lightbox)

开发并测试了此数字手表面后，添加了更多代码，以将其升级到更复杂的模拟观看面：

[![模拟观看面](creating-a-watchface-images/02-example-watchface.png "最终模拟观看面的示例屏幕截图")](creating-a-watchface-images/02-example-watchface.png#lightbox)

观看人脸服务作为磨损1.0 应用的一部分进行捆绑和安装。 在下面的示例中， `MainActivity` 只包含来自 "磨损 1.0" 应用程序模板的代码，以便可以将手表人脸服务打包并部署到智能手表作为应用的一部分。 实际上，此应用程序将纯粹作为一个车辆，用于将手表人服务加载到 (或模拟器) 1.0 中，以便进行调试和测试。

## <a name="requirements"></a>要求

若要实现 watch 面部服务，需要满足以下要求：

- 在磨损设备或模拟器上，Android 5.0 (API 级别 21) 或更高版本。

- [Xamarin Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Wear)必须添加到 xamarin 项目。

尽管 Android 5.0 是用于实现监视人脸服务的最低 API 级别，但建议使用 Android 5.1 或更高版本。 运行 Android 5.1 (API 22) 或更高版本的 android 磨损设备允许磨损应用在设备处于低功率 *环境* 模式时控制屏幕上显示的内容。 当设备离开低功率 *环境* 模式时，它处于 *交互* 模式。 有关这些模式的详细信息，请参阅 [使应用保持可见](https://developer.android.com/training/wearables/apps/always-on.html)。

## <a name="start-an-app-project"></a>启动应用项目

创建名为 **WatchFace** (的新 Android 磨损1.0 项目。有关创建新的 Xamarin android 项目的详细信息，请参阅 [Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md)) ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[!["新建项目" 对话框](creating-a-watchface-images/03-wear-project-vs-sml.png "在 "新建项目" 对话框中选择 "应用"")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[!["新建项目" 对话框](creating-a-watchface-images/03-wear-project-xs-sml.png "在 "新建项目" 对话框中选择 "应用"")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

将包名称设置为 `com.xamarin.watchface` ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![包名称设置](creating-a-watchface-images/04-package-name-vs.png "将包名称设置为 watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![包名称设置](creating-a-watchface-images/04-package-name-xs.png "将包名称设置为 watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

此外，向下滚动并启用 **INTERNET** 并 **WAKE_LOCK** 权限：

[![所需的权限](creating-a-watchface-images/05-required-permissions-vs.png "启用 INTERNET 和 WAKE_LOCK 权限")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

将最低 Android 版本设置为 **android 5.1 (API 级别 22) **。
同时，启用 **Internet** 和 **WakeLock** 权限：

[![所需的权限](creating-a-watchface-images/05-required-permissions-xs.png "启用 Internet 和 WakeLock 权限")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下来，下载 [preview.png](creating-a-watchface-images/preview.png) &ndash; 此演练稍后将添加到 **绘图** 文件夹。

## <a name="add-the-xamarinandroid-wear-package"></a>添加 Xamarin 文件包

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中启动 NuGet 包管理器 (，右键单击**解决方案资源管理器**中的 "**引用**"，然后选择 "**管理 NuGet 包 ...**) "。将项目更新为最新稳定版本的**Xamarin**：

[![NuGet 包管理器添加](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "添加 Xamarin 包")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下来，如果安装了 **v13** ，请将其卸载：

[![NuGet 包管理器删除](creating-a-watchface-images/07-uninstall-v13-sml.png "删除 v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

启动 NuGet 包管理器 (在 Visual Studio for Mac 中，右键单击 "解决方案"**窗格**中的 "**包**"，然后选择 "**添加包 ...**) "。将项目更新为最新稳定版本的**Xamarin**：

[![NuGet 包管理器添加](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "添加 Xamarin 包")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

在磨损设备或模拟器上构建并运行应用 (有关如何执行此操作的详细信息，请参阅 [入门](~/android/wear/get-started/index.md) 指南) 。 你应在磨损设备上看到以下应用屏幕：

[![应用屏幕截图](creating-a-watchface-images/08-app-screen.png "磨损设备上的应用屏幕")](creating-a-watchface-images/08-app-screen.png#lightbox)

此时，基本应用程序不具有手表人脸功能，因为它尚未提供监视人脸服务实现。 此服务将添加到下一步。

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 损耗通过类实现了观察面 `CanvasWatchFaceService` 。 `CanvasWatchFaceService` 派生自 `WatchFaceService` ，它本身派生自，如下 `WallpaperService` 图所示：

[![继承关系图](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 继承关系图")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包括嵌套的 `CanvasWatchFaceService.Engine` ; 它将实例化一个 `CanvasWatchFaceService.Engine` 对象，该对象执行绘制观看面的实际工作。 `CanvasWatchFaceService.Engine` 派生自， `WallpaperService.Engine` 如上面的关系图所示。

此关系图中未显示的是用于 `Canvas` `CanvasWatchFaceService` 绘制手表面的， &ndash; `Canvas` 如下所述通过方法传入 `OnDraw` 。

在以下部分中，将通过执行以下步骤来创建自定义监视人脸服务：

1. 定义 `MyWatchFaceService` 派生自的名为的类  `CanvasWatchFaceService` 。

2. 在中 `MyWatchFaceService` ，创建一个  `MyWatchFaceEngine` 从派生的名为的嵌套类  `CanvasWatchFaceService.Engine` 。

3. 在中 `MyWatchFaceService` ，实现一个 `CreateEngine` 实例化 `MyWatchFaceEngine` 并返回该方法的方法。

4. 在中 `MyWatchFaceEngine` ，实现 `OnCreate` 方法以创建 watch 面部样式并执行任何其他初始化任务。

5. 实现的 `OnDraw` 方法 `MyWatchFaceEngine` 。 只要需要重绘手表面，就会调用此方法， (即) *无效* 。 `OnDraw` 用于绘制 (和重绘的方法) 观看面部元素，例如小时、分钟和秒。

6. 实现的 `OnTimeTick` 方法 `MyWatchFaceEngine` 。
    `OnTimeTick` 在环境和交互模式下，至少每分钟调用一次 () 或日期/时间发生更改。

有关的详细信息 `CanvasWatchFaceService` ，请参阅 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 文档。
同样， [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) 说明了观察面的实际实现。

### <a name="add-the-canvaswatchfaceservice"></a>添加 CanvasWatchFaceService

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中添加一个名为**MyWatchFaceService.cs** (的新文件，右键单击**解决方案资源管理器**中的 " **WatchFace** "，单击 "**添加 > 新项 ...**"，然后选择 "**类**) "。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中添加一个名为 **MyWatchFaceService.cs** (的新文件，右键单击 " **WatchFace** " 项目，单击 " **添加 > 新文件 ...**"，然后选择 " **空类**) "。

-----

将此文件的内容替换为以下代码：

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` 从) 派生的 (`CanvasWatchFaceService` 是手表面的 "主程序"。 `MyWatchFaceService` 仅实现一个方法， `OnCreateEngine` 此方法实例化并返回 `MyWatchFaceEngine` (`MyWatchFaceEngine` 派生自) 的对象 `CanvasWatchFaceService.Engine` 。 实例化 `MyWatchFaceEngine` 对象必须以形式返回 `WallpaperService.Engine` 。 将封装 `MyWatchFaceService` 对象传递到构造函数中。

`MyWatchFaceEngine` 是实际的监视面实现， &ndash; 它包含用于绘制观察面的代码。 它还处理系统事件，例如屏幕更改 (环境/交互模式、屏幕关闭等 ) 。

### <a name="implement-the-engine-oncreate-method"></a>实现 Engine OnCreate 方法

`OnCreate`方法初始化手表面。 将以下字段添加到 `MyWatchFaceEngine` ：

```csharp
Paint hoursPaint;
```

此 `Paint` 对象将用于绘制监视面上的当前时间。 接下来，将以下方法添加到 `MyWatchFaceEngine` ：

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` 启动后不久就会调用 `MyWatchFaceEngine` 。 它设置 `WatchFaceStyle` (来控制磨损设备如何与用户交互) 并实例化 `Paint` 将用于显示时间的对象。

对的调用 `SetWatchFaceStyle` 会执行以下操作：

1. 将 *速览模式* 设置为 `PeekModeShort` ，这将导致通知在显示器上显示为较小的 "速览" 卡。

2. 将背景可见性设置为 `Interruptive` ，这使得仅在显示会造成中断通知时才会显示速览卡的背景。

3. 禁止在手表面上绘制默认系统 UI 时间，以便自定义监视人可以显示时间。

有关这些和其他手表样式选项的详细信息，请参阅 Android [WatchFaceStyle](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 文档。

`SetWatchFaceStyle`完成后， `OnCreate` (实例化 `Paint` 对象 `hoursPaint`) 并将其颜色设置为白色，将其文本大小设置为48像素， (必须以像素为) 单位指定[TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) 。

### <a name="implement-the-engine-ondraw-method"></a>实现引擎 OnDraw 方法

此 `OnDraw` 方法可能是最重要的 `CanvasWatchFaceService.Engine` 方法 &ndash; ，它是实际绘制观看人脸元素（例如数字和时钟面部指针）的方法。
在下面的示例中，它在手表面上绘制一个时间字符串。
将以下方法添加到 `MyWatchFaceEngine`：

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

当 Android 调用时 `OnDraw` ，它会传入一个 `Canvas` 实例和可在其中绘制面部的边界。 在上面的代码示例中， `DateTime` 用于以小时和分钟为单位计算当前时间， (以12小时格式) 。 使用方法在画布上绘制生成的时间字符串 `Canvas.DrawText` 。 该字符串将从左边缘向下显示70像素，从上边缘向下移动80像素。

有关方法的详细信息 `OnDraw` ，请参阅 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) API 文档。

### <a name="implement-the-engine-ontimetick-method"></a>实现 Engine OnTimeTick 方法

Android 会定期调用 `OnTimeTick` 方法以更新监视面显示的时间。 在环境和交互模式) ，或者日期/时间或时区发生更改时，至少每分钟调用一次 (。 将以下方法添加到 `MyWatchFaceEngine`：

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

此实现 `OnTimeTick` 只需调用 `Invalidate` 。 `Invalidate`方法计划 `OnDraw` 重绘手表面。

有关方法的详细信息 `OnTimeTick` ，请参阅 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 文档。

## <a name="register-the-canvaswatchfaceservice"></a>注册 CanvasWatchFaceService

`MyWatchFaceService` 必须在关联磨损应用的 **AndroidManifest.xml** 中注册。 为此，请将以下 XML 添加到 `<application>` 部分：

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

此 XML 执行以下操作：

1. 设置 `android.permission.BIND_WALLPAPER` 权限。 此权限提供了在设备上更改系统墙纸的 "监视人脸服务" 权限。 请注意，必须在 `<service>` 部分中而不是在外部部分设置此权限 `<application>` 。

2. 定义 `watch_face` 资源。 此资源是一个简短的 XML 文件，用于声明 `wallpaper` 资源 (将在下一节) 中创建此文件。

3. 声明一个名为的可绘制图像 `preview` ，将由 "监视选取器选择" 屏幕显示。

4. 包含一个 `intent-filter` ，它使 Android 知道  `MyWatchFaceService` 将显示一个手表面。

完成基本示例的代码 `WatchFace` 。 下一步是添加必要的资源。

## <a name="add-resource-files"></a>添加资源文件

必须先添加 **watch_face** 资源和预览图像，然后才能运行该监视服务。 首先，在 **Resources/xml/watch_face.xml** 中创建一个新的 XML 文件，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

将此文件的生成操作设置为 **AndroidResource**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![生成操作](creating-a-watchface-images/10-android-resource-vs.png "将生成操作设置为 AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![生成操作](creating-a-watchface-images/10-android-resource-xs.png "将生成操作设置为 AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此资源文件定义 `wallpaper` 将用于监视人脸的简单元素。

如果尚未这样做，请下载 [preview.png](creating-a-watchface-images/preview.png)。
请在 **资源/绘制/preview.png**上安装它。 请确保将此文件添加到 `WatchFace` 项目。 此预览图像将在磨损设备上的手表面部选取器中显示给用户。 若要为自己的 "手表面" 创建预览图像，可以在运行时拍摄手表面的屏幕截图。  (有关从磨损设备获取屏幕截图的详细信息，请参阅 [拍摄屏幕截图](~/android/wear/deploy-test/debug-on-device.md#screenshots)) 。

## <a name="try-it"></a>试试看！

构建应用并将其部署到磨损设备。 应该会看到 "应用程序" 屏幕显示为 "之前"。 执行以下操作以启用新的手表脸：

1. 向右轻扫，直到看到 "监视" 屏幕的背景。

2. 在屏幕背景上的任意位置按住鼠标并保持两秒钟。

3. 从左到右滑动以浏览各种手表面。

4. 选择右侧)  (显示的 **Xamarin 示例** 监视面：

    [![Watchface 选取器](creating-a-watchface-images/11-watchface-picker.png "轻扫以定位 Xamarin 示例监视面")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. 点击 **Xamarin 示例** 监视人脸将其选中。

这会更改磨损设备的手表面，以使用迄今为止实现的自定义监视人脸服务：

[![数字观看面](creating-a-watchface-images/12-digital-watchface.png "在磨损设备上运行的自定义数字观看")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

这是一个相对较为粗糙的手表，因为应用程序实现非常简单 (例如，它不包括手表面背景，也不会调用 `Paint` 反别名方法来改善外观) 。
不过，它确实实现了创建自定义监视人脸所需的基本功能。

在下一部分中，此监视面将升级到更复杂的实现。

## <a name="upgrading-the-watch-face"></a>升级手表面

本演练的其余部分 `MyWatchFaceService` 将升级以显示模拟样式的监视面，并将其扩展以支持更多功能。 将添加以下功能来创建升级的监视面：

1. 指示具有模拟小时、分钟和秒的时间。

2. 对可见性变化做出反应。

3. 响应环境模式和交互模式之间的更改。

4. 读取基础磨损设备的属性。

5. 自动更新发生时区更改的时间。

在实现下面的代码更改之前，请下载 [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)，将其解压缩，然后将解压后的 .png 文件移动到 **资源/可绘制** (覆盖以前的 **preview.png**) 。 向项目中添加新的 .png 文件 `WatchFace` 。

### <a name="update-engine-features"></a>更新引擎功能

下一步是将 **MyWatchFaceService.cs** 升级到绘制模拟监视面并支持新功能的实现。 将 **MyWatchFaceService.cs** 的内容替换为 [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) 中的手表面部代码的模拟版本 (你可以将此源剪切并粘贴到现有的 **MyWatchFaceService.cs**) 中。

此版本的 **MyWatchFaceService.cs** 将更多代码添加到现有方法，并包括其他重写的方法来添加更多功能。 以下各节提供了有关源代码的指导教程。

#### <a name="oncreate"></a>OnCreate

已更新的 **OnCreate** 方法会像以前一样配置 watch 面部样式，但它还包括一些附加步骤：

1. 将背景图像设置为**资源 hdpi/xamarin_background.png**中驻留的**xamarin_background**资源。

2. 初始化 `Paint` 对象以绘制小时、分钟和秒。

3. 初始化一个 `Paint` 对象，用于绘制观察表面边缘的小时刻度。

4. 创建调用 `Invalidate` (重绘) 方法的计时器，以便每秒重绘第二次。 请注意，此计时器是必需的，因为 `OnTimeTick` `Invalidate` 每分钟只调用一次。

此示例仅包含一个 **xamarin_background.png** 图像;但是，你可能希望为自定义监视面将支持的每个屏幕密度创建不同的背景图像。

#### <a name="ondraw"></a>OnDraw

已更新的 **OnDraw** 方法使用以下步骤绘制模拟样式的监视面：

1. 获取当前时间，现在该时间已在对象中进行维护 `time` 。

2. 确定绘图图面的边界及其中心。

3. 绘制背景，在绘制背景时缩放以适合设备。

4. 在时钟面周围绘制十二个 *刻度* (对应于时钟面上的小时) 。

5. 计算每个观看局的角度、旋转和长度。

6. 在手表面上绘制每个手。 请注意，如果手表处于环境模式，则不会绘制第二个。

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

调用此方法以通知 `MyWatchFaceEngine` 有关磨损设备的属性的 (例如，低位环境模式和烧入保护) 。 在中 `MyWatchFaceEngine` ，此方法仅检查低位环境模式下 (的低位环境模式，屏幕为每种颜色) 支持较少的位数。

有关此方法的详细信息，请参阅 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 文档。

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

当磨损设备进入或退出环境模式时，将调用此方法。 在 `MyWatchFaceEngine` 实现中，如果监视面处于环境模式，则会禁用抗锯齿。

有关此方法的详细信息，请参阅 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 文档。

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

只要手表变为可见或隐藏，就会调用此方法。 在中 `MyWatchFaceEngine` ，此方法将根据可见性状态向下面的) 中 (所述的时区接收器注册/取消注册。

有关此方法的详细信息，请参阅 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 文档。

### <a name="time-zone-feature"></a>时区功能

新的 **MyWatchFaceService.cs** 还包括在时区发生更改时更新当前时间的功能， (例如，当跨时区) 传播时。 接近 **MyWatchFaceService.cs**的末尾，定义了一个时区更改， `BroadcastReceiver` 用于处理时区更改意向对象：

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

`RegisterTimezoneReceiver` `UnregisterTimezoneReceiver` 方法由方法调用和方法 `OnVisibilityChanged` 。
`UnregisterTimezoneReceiver` 当 watch 面的可见性状态更改为隐藏时调用。 当监视面再次可见时， `RegisterTimezoneReceiver` 将调用 (参见) 的 `OnVisibilityChanged` 方法。

Engine `RegisterTimezoneReceiver` 方法为此时区接收方的事件声明处理程序 `Receive` ; 此处理程序在 `time` 每次超过时区时用新时间更新对象：

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

为时区接收方创建并注册了一个意向筛选器：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver`方法将注销时区接收方：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>运行改进的手表面

重新生成应用并将其部署到磨损设备。 像以前一样从 "手表人脸" 选取器中选择手表面。 监视选取器中的预览显示在左侧，而新的手表面显示在右侧：

[![模拟观看面](creating-a-watchface-images/13-analog-watchface.png "改善了选择器和设备上的模拟脸")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在此屏幕截图中，第二个局每秒移动一次。 在磨损设备上运行此代码时，第二个局会在手表进入环境模式时消失。

## <a name="summary"></a>总结

在本演练中，已实现并测试了自定义 Android 磨损 1.0 watchface。 `CanvasWatchFaceService`引入了和 `CanvasWatchFaceService.Engine` 类，并实现了引擎类的基本方法来创建一个简单的数字监视面。 此实现已通过更多的功能进行了更新，可创建模拟监视面，还实现了其他方法来处理可见性、环境模式和设备属性差异方面的更改。 最后，实现了时区广播接收器，以便监视自动更新时区的时间。

## <a name="related-links"></a>相关链接

- [创建手表面](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 示例](/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService 引擎](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)