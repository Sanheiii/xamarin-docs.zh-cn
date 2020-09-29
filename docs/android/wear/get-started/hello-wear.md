---
title: 你好，穿戴设备
description: 创建第一个 Android 应用并在磨损模拟器或设备上运行它。 本演练提供了有关创建小型 Android 损耗项目的分步说明，该项目用于处理按钮单击并在磨损设备上显示单击计数器。 它介绍了如何使用通过蓝牙连接到 Android 手机的磨损模拟器或磨损设备来调试应用程序。 它还提供了一组适用于 Android 的调试提示。
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/10/2018
ms.openlocfilehash: 8b640e867e3f7adb6960ecda557ac1e410ec5799
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456928"
---
# <a name="hello-wear"></a>你好，穿戴设备

_创建第一个 Android 应用并在磨损模拟器或设备上运行它。本演练提供了有关创建小型 Android 损耗项目的分步说明，该项目用于处理按钮单击并在磨损设备上显示单击计数器。它介绍了如何使用通过蓝牙连接到 Android 手机的磨损模拟器或磨损设备来调试应用程序。它还提供了一组适用于 Android 的调试提示。_

![要在本教程中完成的应用程序的屏幕截图](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>你的第一个应用程序

按照以下步骤创建你的第一个 Xamarin 应用：

### <a name="1-create-a-new-android-project"></a>1. 创建新的 Android 项目

创建新的 **Android 应用程序**：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在 "新建项目" 对话框中创建新的 Android 应用程序](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在 "新建解决方案" 对话框中创建新的 Android 应用程序](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----

此模板自动包括 **Xamarin Android 可穿戴库** NuGet (和依赖项) 以便您可以访问特定于特定的小组件。 如果看不到磨损模板，请查看 [安装和设置](~/android/wear/get-started/installation.md) 指南，确认是否已安装受支持的 Android SDK。 

### <a name="2-choose-the-correct-target-framework"></a>2. 选择正确的 **目标框架**

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

确保将 **最小 android 目标** 设置为 **Android 5.0 (棒糖形) ** 或更高版本： 

[![在 Visual Studio 中将目标框架设置为 Android 5。0](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

确保将目标框架设置为 **Android 5.0 (棒糖形) ** 或更高版本：

[![将目标框架设置为 Visual Studio for Mac 中的 Android 5。0](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

有关设置目标框架的详细信息，请参阅 [了解 ANDROID API 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="3-edit-the-mainaxml-layout"></a>3. 编辑 **main.axml** 布局

配置布局以包含示例的 `TextView` 和 `Button` ： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. 编辑 **MainActivity.cs** 源

添加代码以递增计数器，并在每次单击按钮时显示它： 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. 设置模拟器或设备

下一步是设置模拟器或设备以部署和运行应用。 如果你尚不熟悉部署和运行 Xamarin Android 应用程序的一般过程，请参阅 [Android 快速入门](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果你没有 Android 磨损设备，如 Android 磨损 Smartwatch，则可以在模拟器上运行该应用程序。 有关在模拟器上调试应用的详细信息，请参阅 [在模拟器上调试 Android 损耗](~/android/wear/deploy-test/debug-on-emulator.md)。

如果有 Android 磨损设备，如 Android 磨损 Smartwatch，则可以在设备上运行应用，而不是使用模拟器。 有关在磨损设备上进行调试的详细信息，请参阅 [在磨损设备上调试](~/android/wear/deploy-test/debug-on-device.md)。

### <a name="6-run-the-android-wear-app"></a>6. 运行 Android 应用程序

Android 磨损设备应该出现在设备下拉菜单中。 在开始调试之前，请务必选择正确的 Android 磨损设备或 AVD。 选择设备后，单击 "播放" 按钮，将应用部署到模拟器或设备。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在 Visual Studio 设备菜单中选择磨损 AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在 Visual Studio for Mac 设备菜单中选择磨损 AVD](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

首先，您可能会看到一 **分钟** (或其他一些插播式屏幕) ： 

![监视模拟器只显示一分钟 .。。](hello-wear-images/please-wait.png)

如果使用的是监视模拟器，则可能需要一段时间来启动应用。 使用蓝牙时，部署应用所需的时间比通过 USB 更多。  (例如，将此应用程序部署到连接到第5部手机的 "LG G" 观看大约需要5分钟。 ) 

应用成功部署后，磨损设备的屏幕应显示如下所示的屏幕：

[![磨损应用的初始屏幕](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

点击 "**单击我！** " 按钮，查看每次点击的计数增量：

[![单击3次后磨损应用的屏幕截图](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)

## <a name="next-steps"></a>后续步骤

查看包含带手机应用程序的 Android 应用程序的 [磨损示例](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) 。

准备好分发应用时，请参阅 [使用打包](~/android/wear/deploy-test/packaging.md)。

## <a name="related-links"></a>相关链接

- [单击 "我的应用 (示例) ](/samples/xamarin/monodroid-samples/wear-weartest)