---
title: 初始屏幕
description: Android 应用程序需要一些时间才能启动，特别是在设备上首次启动应用程序时。 初始屏幕可能会显示用户的启动进度或指示品牌。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/02/2019
ms.openlocfilehash: 2b0b68be5cd36d908c7e152137b14b41a7a0b947
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458168"
---
# <a name="splash-screen"></a>初始屏幕

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)

_Android 应用程序需要一些时间才能启动，特别是在设备上首次启动应用程序时。初始屏幕可能会显示用户的启动进度或指示品牌。_

## <a name="overview"></a>概述

Android 应用程序需要一些时间才能启动，特别是在第一次在设备上运行应用程序时 (有时这称为 _冷启动_) 。 初始屏幕可能会显示用户的启动进度，也可能显示品牌信息来识别和升级应用程序。

本指南讨论了在 Android 应用程序中实现初始屏幕的一项技术。 其中包括以下步骤：

1. 创建初始屏幕的 "绘制资源"。

2. 定义将显示可绘制资源的新主题。

3. 向应用程序添加一个新活动，该活动将用作上一步骤中创建的主题定义的初始屏幕。

[![示例 Xamarin 徽标初始屏幕，后面跟有应用屏幕](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>要求

本指南假定应用程序面向 Android API 级别21或更高版本。 该应用程序还必须具有添加到项目中的 **xamarin** . **AppCompat 和 v7.** NuGet 包。

本指南中的所有代码和 XML 都可在本指南的 [SplashScreen](/samples/xamarin/monodroid-samples/splashscreen) 示例项目中找到。

## <a name="implementing-a-splash-screen"></a>实现初始屏幕

呈现和显示初始屏幕的最快捷方法是创建自定义主题，并将其应用于显示初始屏幕的活动。 呈现活动时，它将加载主题，并将主题) 引用的可绘制资源 (应用到活动的背景。 此方法避免了创建布局文件的需要。

初始屏幕实现为一个活动，该活动显示品牌可绘制、执行任何初始化并启动所有任务。 应用引导后，初始屏幕活动将启动主活动，并从应用程序后端堆栈中移除自身。

### <a name="creating-a-drawable-for-the-splash-screen"></a>创建初始屏幕的 "绘制"

初始屏幕将在初始屏幕活动的背景中显示 XML 可绘制的。 需要使用位图图像 (如用于显示图像的 PNG 或 JPG) 。

示例应用程序定义了一个名为 **splash_screen.xml**的 "绘制"。 此可绘制方式使用 [层列表](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) 在应用程序中居中显示初始屏幕图像，如以下 xml 所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash_logo"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

这会使 `layer-list` 初始映像在资源指定的背景色上居中 `@color/splash_background` 。 示例应用程序在 **资源/值/colors.xml** 文件中定义此颜色：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  ...
  <color name="splash_background">#FFFFFF</color>
</resources>
```

有关对象的详细信息， `Drawable` 请参阅 [Android 用于在 Android 上进行绘制的文档](https://developer.android.com/reference/android/graphics/drawable/Drawable)。

### <a name="implementing-a-theme"></a>实现主题

若要为初始屏幕活动创建自定义主题，请编辑 (或添加) 文件 **值/styles.xml** 并 `style` 为初始屏幕创建新元素。 下面显示了一个示例 **值/style.xml** 文件，其中包含一个 `style` 名为 **MyTheme**的示例：

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>  
    <item name="android:windowFullscreen">true</item>  
    <item name="android:windowContentOverlay">@null</item>  
    <item name="android:windowActionBar">true</item>  
  </style>
</resources>
```

**MyTheme** 是非常 spartan 的 &ndash; ，它声明了窗口背景，从窗口中显式删除了标题栏，并声明它为全屏。 如果要创建在活动增加第一个布局之前模拟应用 UI 的初始屏幕，可以使用 `windowContentOverlay` 而不是 `windowBackground` 在样式定义中使用。 在这种情况下，您还必须修改 **splash_screen.xml** 可绘制的，以便它显示 UI 的仿真。

### <a name="create-a-splash-activity"></a>创建初始活动

现在，我们需要一个用于启动 Android 的新活动，该活动具有初始映像并执行任何启动任务。 下面的代码是完整的初始屏幕实现的示例：

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` 显式使用在上一部分中创建的主题，替代应用程序的默认主题。
不需要在中加载布局， `OnCreate` 因为主题将可绘制的作为背景声明。

必须设置 `NoHistory=true` 属性，以便从后端堆栈中删除活动。 若要阻止 "后退" 按钮取消启动进程，还可以重写， `OnBackPressed` 使其不执行任何操作：

```csharp
public override void OnBackPressed() { }
```

启动工作在中以异步方式执行 `OnResume` 。 这是必需的，以便启动工作不会减慢启动屏幕或延迟启动屏幕的外观。 工作完成后， `SplashActivity` 将启动， `MainActivity` 并且用户可能开始与应用进行交互。

此新的 `SplashActivity` 设置为应用程序的启动程序活动，方法是将 `MainLauncher` 属性设置为 `true` 。 由于 `SplashActivity` 现在是启动器活动，因此必须编辑 `MainActivity.cs` 并 `MainLauncher` 从以下内容中删除属性 `MainActivity` ：

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>横向模式

在前面的步骤中实现的初始屏幕将在纵向模式和横向模式下正确显示。 但是，在某些情况下，需要为纵向模式和横向模式使用单独的初始屏幕 (例如，如果初始映像为全屏) 。

若要为横向模式添加初始屏幕，请使用以下步骤：

1. 在 " **资源/可绘制** 的文件夹" 中，添加要使用的初始屏幕映像的横向版本。 在此示例中， **splash_logo_land.png** 是上述示例中使用的徽标的横向版本 (使用白色字母而不是蓝色的) 。

2. 在 " **资源"/** "可绘制文件夹" 文件夹中，创建 `layer-list` 前面定义的可绘制的横向版本 (例如 **splash_screen_land.xml**) 。 在此文件中，将位图路径设置为初始屏幕图像的横向版本。 在下面的示例中， **splash_screen_land.xml** 使用 **splash_logo_land.png**：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3. 如果 **资源/值** 尚不存在，则创建它们。

4. 将文件 **colors.xml** 和 **style.xml** 添加到 **值-土地** (可以从现有  **值/colors.xml** 和 **值/style.xml** 文件) 复制和修改这些文件。

5. 修改 **值-土地/style.xml** 以便它使用的可绘制的横向版本 `windowBackground` 。 在此示例中，使用 **splash_screen_land.xml** ：

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6. 修改 **值-"土地/colors.xml** " 以配置要用于初始屏幕的横向版本的颜色。 在此示例中，对于横向模式，初始背景色更改为蓝色：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7. 重新生成并运行应用。 在仍显示初始屏幕时，将设备旋转到横向模式。 初始屏幕更改为横向版本：

    [![初始屏幕旋转到横向模式](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

请注意，使用横向模式初始屏幕并不总是提供无缝体验。 默认情况下，Android 在纵向模式下启动应用并将其转换为横向模式，即使设备已处于横向模式下也是如此。 因此，如果在设备处于横向模式时启动该应用，则设备会短暂显示纵向初始屏幕，然后在屏幕上将旋转从纵向变为横向初始屏幕。 遗憾的是，即使在 `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` 初始活动的标志中指定了，也会发生这种最初的纵向到横向转换。 解决此限制的最佳方式是创建一个在纵向和横向模式下均正确呈现的单一初始屏幕图像。

## <a name="summary"></a>总结

本指南讨论了在 Xamarin Android 应用程序中实现初始屏幕的一种方法：即，将自定义主题应用于启动活动。

## <a name="related-links"></a>相关链接

- [SplashScreen (示例) ](/samples/xamarin/monodroid-samples/splashscreen)
- [层列表的绘制](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [材料设计模式-启动屏幕](https://material.io/design/communication/launch-screen.html#usage)