---
title: 处理旋转
description: 本主题介绍如何处理 Xamarin 中的设备方向更改。 其中介绍了如何使用 Android 资源系统自动为特定设备方向加载资源，以及如何以编程方式处理方向更改。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 944452160ed023f47fc2c648f092032c245350fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455661"
---
# <a name="handling-rotation"></a>处理旋转

_本主题介绍如何处理 Xamarin 中的设备方向更改。其中介绍了如何使用 Android 资源系统自动为特定设备方向加载资源，以及如何以编程方式处理方向更改。_

## <a name="overview"></a>概述

因为移动设备易于旋转，所以内置旋转是移动操作系统中的一项标准功能。 Android 提供了一个用于处理应用程序内的旋转的复杂框架，无论用户界面是在 XML 中以声明方式创建还是以编程方式在代码中创建。 当自动处理旋转设备上的声明性布局更改时，应用程序可以从与 Android 资源系统紧密集成中获益。 对于编程布局，必须手动处理更改。 这样，就可以更好地控制运行时，但会给开发人员提供更多的工作。 应用程序还可以选择退出活动重启并进行手动控制方向更改。

本指南将探讨以下方向主题：

- **声明性布局旋转** &ndash; 如何使用 Android 资源系统构建方向感知应用程序，包括如何为特定方向加载布局和绘图。

- **编程布局旋转** &ndash; 如何以编程方式添加控件，以及如何手动处理方向更改。

## <a name="handling-rotation-declaratively-with-layouts"></a>用布局以声明方式处理旋转

通过在遵循命名约定的文件夹中包含文件，Android 会在方向更改时自动加载相应的文件。
这包括对以下内容的支持：

- *布局资源* &ndash; 指定为每个方向放大的布局文件。

- *绘制资源* &ndash; 指定为每个方向加载的绘图。

### <a name="layout-resources"></a>布局资源

默认情况下， **资源/布局** 文件夹中包含的 Android XML (main.axml) 文件用于活动的呈现视图。 如果没有专门为横向提供的其他布局资源，则此文件夹的资源用于纵向和横向方向。 考虑由默认的项目模板创建的项目结构：

[![默认项目模板结构](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此项目将在**资源/布局**文件夹中创建一个**main.axml**文件。 调用活动的 `OnCreate` 方法时，它会增加 main.axml 中定义的视图 **，** 该视图将声明一个按钮，如下图所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

如果设备旋转到横向方向，则 `OnCreate` 会再次调用该活动的方法，并且将对 **main.axml** 文件进行膨胀，如以下屏幕截图所示：

[![同一屏幕，但在横向方向上](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)

#### <a name="orientation-specific-layouts"></a>特定于方向的布局

除了布局文件夹 (默认为纵向，还可以通过包含名为) 的文件夹显式命名 *布局-端口* `layout-land` ，应用程序可以在没有任何代码更改的情况下，定义它所需的视图。

假设 **main.axml** 文件包含以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

如果将包含附加的 **main.axml** 文件的名为 layout 的文件夹添加到项目中，则在横向的情况下，因为这样做布局现在会导致 Android 加载新添加的 **main.axml。** 为了简单起见，请考虑包含以下代码 (的 **main.axml** 文件的横向版本，此 XML 类似于默认的纵向版本代码，但使用) 中的不同字符串 `TextView` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

运行此代码并将设备从纵向旋转到横向演示了新的 XML 加载，如下所示：

[![纵向和横向屏幕快照打印纵向模式](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)

### <a name="drawable-resources"></a>绘制资源

旋转时，Android 将可绘制资源视为布局资源。 在这种情况下，系统将分别从 **资源/** 每个 "绘图" 和 " **资源"-土地** 文件夹中获取。

例如，假设项目在 **资源/可绘制** 文件夹中包含一个名为 Monkey.png 的图像，其中从 XML 引用的该图像如下所示 `ImageView` ：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

接下来，我们会假设**资源/可绘制-土地**下包含不同版本的**Monkey.png** 。 与布局文件类似，当设备旋转时，为给定方向进行的可绘制更改，如下所示：

[![纵向模式和横向模式下显示的 Monkey.png 的不同版本](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)

## <a name="handling-rotation-programmatically"></a>以编程方式处理旋转

有时我们在代码中定义布局。 出现这种情况的原因有多种，包括技术限制、开发人员首选项等。以编程方式添加控件时，应用程序必须手动考虑设备方向，当我们使用 XML 资源时，它会自动处理。

### <a name="adding-controls-in-code"></a>在代码中添加控件

若要以编程方式添加控件，应用程序需要执行以下步骤：

- 创建布局。
- 设置布局参数。
- 创建控件。
- 设置控件布局参数。
- 将控件添加到布局。
- 将布局设置为内容视图。

例如，假设有一个用户界面，其中包含一个 `TextView` 添加到的控件 `RelativeLayout` ，如下面的代码所示。

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码创建类的实例 `RelativeLayout` 并设置其 `LayoutParameters` 属性。 `LayoutParams`类是 Android 方法，它封装了控件如何以一种可重用的方式放置。 创建布局的实例后，可以创建控件并将其添加到其中。 控件也具有 `LayoutParameters` ，如 `TextView` 本示例中的。 创建后 `TextView` ，将其添加到， `RelativeLayout` 并将设置 `RelativeLayout` 为内容视图会导致应用程序显示 `TextView` 如下所示的：

[![显示在纵向和横向模式下的递增计数器按钮](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)

### <a name="detecting-orientation-in-code"></a>在代码中检测方向

如果在调用时，应用程序尝试为每个方向加载不同的用户界面 `OnCreate` (这将在每次设备旋转) 时出现，它必须检测方向，然后加载所需的用户界面代码。 Android 具有一个名为的类 `WindowManager` ，可用于通过属性确定当前设备旋转 `WindowManager.DefaultDisplay.Rotation` ，如下所示：

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码将设置 `TextView` 为从屏幕左上角定位到100像素，当旋转到横向时，将自动动态显示为新的布局，如下所示：

[![跨纵向模式和横向模式保留视图状态](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)

### <a name="preventing-activity-restart"></a>阻止活动重新启动

除了处理中的所有内容外 `OnCreate` ，应用程序还可以通过在中设置来防止在方向更改时重新启动活动，如下所示 `ConfigurationChanges` `ActivityAttribute` ：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```

现在设备旋转后，不会重新启动活动。 为了在此情况下手动处理方向更改，活动可以重写 `OnConfigurationChanged` 方法并从传入的对象确定方向 `Configuration` ，如以下活动的新实现所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

此时，将 `TextView's` 为横向和纵向初始化布局参数。 类变量与本身一起保存参数， `TextView` 因为在方向更改时不会重新创建活动。 代码仍在中使用 `surfaceOrientartion` `OnCreate` 来设置的初始布局 `TextView` 。 然后， `OnConfigurationChanged` 处理所有后续的布局更改。

当我们运行应用程序时，Android 会在设备轮换发生时加载用户界面更改，且不会重新启动该活动。

## <a name="preventing-activity-restart-for-declarative-layouts"></a>阻止声明性布局的活动重启

如果在 XML 中定义布局，还可以阻止设备旋转引起的活动重启。 例如，如果出于性能原因，我们想要防止活动重启 (，则可以使用这种方法) ，但不需要为不同方向加载新的资源。

为此，我们将遵循与编程布局一起使用的相同过程。 只需在中进行设置 `ConfigurationChanges` `ActivityAttribute` ，就像前面所述 `CodeLayoutActivity` 。 需要为方向更改运行的任何代码都可以在方法中再次实现 `OnConfigurationChanged` 。

## <a name="maintaining-state-during-orientation-changes"></a>在方向更改期间保持状态

无论是以声明方式还是以编程方式处理旋转，所有 Android 应用程序都应该实现相同的方法，以便在设备方向发生变化时管理状态。 管理状态很重要，因为在对 Android 设备进行旋转时系统会重新启动正在运行的活动。 Android 实现此目的是为了方便地加载替代资源，如布局和绘图，这些资源是专门为特定方向设计的。 当它重新启动时，活动将丢失它可能已存储在局部类变量中的任何暂时性状态。 因此，如果活动是状态相关的，则必须在应用程序级别保持其状态。 应用程序需要处理保存和还原要跨方向更改保留的任何应用程序状态。

有关保存 Android 状态的详细信息，请参阅 [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md) 指南。

## <a name="summary"></a>总结

本文介绍如何使用 Android 内置功能处理旋转。 首先，它介绍了如何使用 Android 资源系统创建面向定向的应用程序。 然后，它介绍了如何在代码中添加控件，以及如何手动处理方向更改。

## <a name="related-links"></a>相关链接

- [旋转演示 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [处理运行时更改](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速更改屏幕方向](https://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)