---
title: KitKat 功能
description: Android 4.4 (KitKat) 为用户和开发人员提供了许多功能。 本指南重点介绍其中一些功能，并提供代码示例和实现详细信息，以帮助你充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 4616d5eb9dde29faaf1e2fefe5e3e9917fdc078e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456712"
---
# <a name="kitkat-features"></a>KitKat 功能

Android 4.4 (KitKat) 为用户和开发人员提供了许多功能。本指南重点介绍其中一些功能，并提供代码示例和实现详细信息，以帮助你充分利用 KitKat。 

## <a name="overview"></a>概述

Android 4.4（API 级别 19）也称为“KitKat”，发布于 2013 年底。 KitKat 提供了各种新功能和改进，包括：

- [用户体验](#user_experience) &ndash; 使用转换框架的简单动画、半透明状态和导航栏以及全屏沉浸式模式，有助于为用户带来更好的体验。

- [用户内容](#user_content) &ndash; 使用存储访问框架简化了用户文件管理；通过改进的打印 API 可以更轻松地打印图片、网站和其他内容。

- [硬件](#hardware) &ndash; 使用 NFC 中基于主机的卡模拟将任何应用转换为 NFC 卡；使用 `SensorManager` 运行低能耗传感器。

- [开发人员工具](#developer_tools) &ndash; 与 Android Debug Bridge 客户端配合使用的截屏应用程序，可作为 Android SDK 的一部分提供。

本指南提供有关将现有 Xamarin Android 应用程序迁移到 KitKat 的指南，并概述了适用于 Xamarin.Android 开发人员的 KitKat。

## <a name="requirements"></a>要求

要使用 KitKat 开发 Xamarin.Android 应用程序，需要通过 Android SDK 管理器安装 Xamarin.Android 4.11.0 或更高版本以及 Android 4.4（API 级别 19），如以下屏幕截图所示  ：

[![在 Android SDK 管理器中选择 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat"></a>

## <a name="migrating-your-app-to-kitkat"></a>将应用迁移到 KitKat

本部分提供了一些第一响应项，可帮助将现有应用程序转换为 Android 4.4。

### <a name="check-system-version"></a>检查系统版本

如果应用程序需要与早期 Android 版本兼容，请确保在系统版本检查中包装特定于 KitKat 的任何代码，如下面的代码示例所示：

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>报警批处理

Android 使用警报服务在指定的时间在后台唤醒应用。 KitKat 通过对警报进行批处理来更进一步地执行此步骤，从而节省电量。 这意味着，KitKat 倾向于将注册为在同一时间间隔内唤醒的多个应用程序分为一组并同时唤醒它们，而不是在确切的时间唤醒每个应用。
要告知 Android 在指定的时间间隔内唤醒应用，请对 [`AlarmManager`](xref:Android.App.AlarmManager) 调用 `SetWindow`，并传入应用唤醒之前可以等待的最短和最长时间（以毫秒为单位），以及唤醒时执行的操作。
下面的代码提供了一个应用程序示例，该应用程序需要在设置时间窗口后的半小时到一小时之间唤醒：

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

要在确切的时间继续唤醒应用，请使用 `SetExact`，并传入应唤醒应用的确切时间以及执行的操作：

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再允许设置确切的重复警报。 使用 [`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*) 
并需要确切的警报才能工作的应用程序现在需要手动触发每个警报。

### <a name="external-storage"></a>外部存储

外部存储现在划分为两种类型，即应用程序独有的存储以及多个应用程序共享的数据。 在外部存储上读取和写入应用的特定位置不需要特殊权限。 与共享存储上的数据交互现在需要 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 权限。 这两种类型可以分类为：

- 如果是通过对 `Context` 调用方法来获取文件或目录路径（例如 [`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*) 
  或 [`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)），
  - 你的应用不需要额外的权限。

- 如果是通过访问属性或对 `Environment` 调用方法来获取文件或目录路径（例如 [`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory) 
  或 [`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)），
  你的应用需要 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 权限。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` 暗示 `READ_EXTERNAL_STORAGE` 权限，因此你只需设置一个权限。

### <a name="sms-consolidation"></a>短信合并

KitKat 将所有短信内容聚合到用户选择的一个默认应用程序，从而简化用户的消息传递。 开发人员负责将应用设置为可选择作为默认的消息传递应用程序，并在未选择该应用程序的情况下，负责使应用程序在代码和实际操作中正常工作。 有关将短信应用转换为 KitKat 的详细信息，请参阅 Google 的[让短信应用准备好使用 KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) 指南。

### <a name="webview-apps"></a>WebView 应用

[WebView](xref:Android.Webkit.WebView)在 KitKat 中进行了改进。 最大的变化是增加了将内容加载到 `WebView` 的安全性。 虽然面向早期 API 版本的大多数应用程序应该都可以正常工作，但强烈建议测试使用 `WebView` 类的应用程序。 有关受影响的 WebView API 的详细信息，请参阅 Android [迁移到 Android 4.4 中的 WebView](https://developer.android.com/guide/webapps/migrating.html) 文档。

<a name="user_experience"></a>

## <a name="user-experience"></a>用户体验

KitKat 附带了几个新的 API 来增强用户体验，其中包括用于处理属性动画的新转换框架和用于设置主题的半透明 UI 选项。 下面介绍了这些更改。

### <a name="transition-framework"></a>转换框架

转换框架使动画更易于实现。 通过 KitKat，可使用一行代码执行简单的属性动画，也可使用“场景”自定义转换  。

#### <a name="simple-property-animation"></a>简单的属性动画

新的 Android 转换库简化了属性动画的代码。 通过框架，可以使用最少的代码执行简单的动画。 例如，以下代码示例使用 [`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*) 
来对显示和隐藏 `TextView` 进行动画处理：

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

上面的示例使用转换框架在更改属性值之间创建自动的默认转换。 由于动画是通过一行代码进行处理的，因此可以通过将 `BeginDelayedTransition` 调用包装在系统版本检查中，轻松使其与早期 Android 版本兼容。 有关详细信息，请参阅[将应用迁移到 KitKat](#Migrating_Your_App_to_KitKat) 部分。

下面的屏幕截图展示了动画之前的应用：

[![动画开始之前的应用屏幕截图](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下面的屏幕截图展示了动画之后的应用：

[![动画完成之后的应用屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下一部分将介绍如何使用“场景”更好地控制转换。

#### <a name="android-scenes"></a>Android 场景

[场景](xref:Android.Transitions.Scene)是转换框架的一部分，旨在使开发人员可以更好地控制动画。 场景在 UI 中创建一个动态区域：用户需为容器中的 XML 内容指定一个容器和多个版本或“场景”，Android 会执行其余工作以对场景之间的转换进行动画处理。 通过 Android 场景，可以使用最小的工作量在开发端生成复杂的动画。

承载动态内容的静态 UI 元素称为“容器”或“场景基”   。 下面的示例使用 Android Designer 创建名为 `container` 的 `RelativeLayout`：

[![使用 Android Designer 创建 RelativeLayout 容器](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

示例布局还在 `container` 下方定义了一个名为 `sceneButton` 的按钮。 此按钮将触发转换。

容器内的动态内容需要两个新的 Android 布局。 这些布局仅指定容器内的代码  。
下面的示例代码定义了两个布局，一个名为 Scene1，其中包含分别为“Kit”和“Katt”的两个文本字段，另一个名为 Scene2，其中包含反转的相同文本字段   。 XML 如下所示:

 **Scene1.axml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

上面的示例使用 `merge` 使视图代码更短，并简化视图层次结构。 可以在[此处](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)阅读有关 `merge` 布局的详细信息。

通过调用 [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*) 并传入容器对象、场景布局文件的资源 ID 和当前的 `Context` 创建场景，如以下代码示例所示：

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

单击按钮可在两个场景之间切换，Android 会以默认转换值进行动画处理：

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下面的屏幕截图展示了动画之前的场景：

[![动画开始之前的应用屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下面的屏幕截图展示了动画之后的场景：

[![动画完成之后的应用屏幕截图](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Android 转换库中有一个[已知 bug](https://code.google.com/p/android/issues/detail?id=62450)，当用户第二次浏览“活动”时，该 bug 会导致使用 `GetSceneForLayout` 创建的场景中断。 [此处](http://www.doubleencore.com/2013/11/new-transitions-framework/)介绍了 java 临时解决方法。

##### <a name="custom-transitions-in-scenes"></a>场景中的自定义转换

可以在 `Resources` 下 `transition` 目录中的 xml 资源文件中定义自定义转换，如以下屏幕截图所示：

[![资源/转换目录下 transition.xml 文件的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下面的代码示例定义一个转换，该转换将在 5 秒内进行动画处理，并使用 [overshoot 内插程序](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html)：

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

使用 [TransitionInflater](xref:Android.Transitions.TransitionInflater) 在“活动”中创建转换，如以下代码所示：

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

然后将新的转换添加到开始动画的 `Go` 调用：

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

使用 KitKat，可以通过可选的半透明状态和导航栏更好地控制应用的主题设置。 可以在用于定义 Android 主题的同一 XML 文件中更改系统 UI 元素的半透明度。 KitKat 引入了以下属性：

- `windowTranslucentStatus` - 如果设置为 true，则顶部状态栏为半透明。

- `windowTranslucentNavigation` - 如果设置为 true，则底部导航栏为半透明。

- `fitsSystemWindows` - 默认情况下，将顶部或底部栏设置为半透明可在透明 UI 元素下移动内容。 将此属性设置为 `true` 是防止内容与半透明系统 UI 元素重叠的一种简单方法。

下面的代码定义了一个具有半透明状态和导航栏的主题：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

下面的屏幕截图展示了上述具有半透明状态和导航栏的主题：

[![具有半透明状态和导航栏的应用的示例屏幕截图](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content"></a>

## <a name="user-content"></a>用户内容

### <a name="storage-access-framework"></a>存储访问框架

存储访问框架 (SAF) 是用户与存储的内容（例如图像、视频和文档）进行交互的一种新方法。 KitKat 将打开一个新的 UI，使用户能够在一个聚合位置访问其数据，而不是向用户显示一个对话框来选择用于处理内容的应用程序。 选择内容后，用户将返回到请求内容的应用程序，并且应用体验会照常继续。

此更改需要开发人员执行两个操作：首先，需要将从提供程序请求内容的应用更新为使用新方法来请求内容。 其次，需要将把数据写入 `ContentProvider` 的应用程序修改为使用新框架。 这两种方案都依赖于新的 [`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat 中，与 `ContentProviders` 的交互是通过 `DocumentsProvider` 类抽象化的。 这意味着，只要可通过 `DocumentsProvider` API 访问数据，SAF 就不会关心数据的物理位置。 本地提供程序、云服务和外部存储设备均使用相同的接口，并以相同的方式进行处理，从而为用户和开发人员提供一个与用户内容进行交互的位置。

本部分介绍如何使用存储访问框架加载和保存内容。

#### <a name="request-content-from-a-provider"></a>从提供程序请求内容

我们可以告诉 KitKat，我们想要通过使用 SAF UI 和 `ActionOpenDocument` 意向来选取内容，这表示我们想要连接到设备可用的所有内容提供程序。 你可以通过指定 `CategoryOpenable` 来向此意向添加一些筛选，这意味着只会返回可打开的内容（即可访问的可用内容）。 KitKat 还允许使用 `MimeType` 来筛选内容。 例如，以下代码通过指定图像 `MimeType` 来筛选图像结果：

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

调用 `StartActivityForResult` 会启动 SAF UI，用户可以通过浏览该 UI 来选择图像：

[![使用存储访问框架来浏览图像的应用的示例屏幕截图](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

用户选择图像后，`OnActivityResult` 返回所选文件的 `Android.Net.Uri`。 下面的代码示例显示用户的图像选择：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>将内容写入提供程序

除了从 SAF UI 加载内容外，KitKat 还允许将内容保存到实现 `DocumentProvider` API 的任何 `ContentProvider`。 使用 `Intent` 和 `ActionCreateDocument` 保存内容：

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上面的代码示例加载 SAF UI，以便用户更改文件名和选择承载新文件的目录：

[![用户在“下载”目录中将文件名更改为 NewDoc 的屏幕截图](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

当用户按“保存”时，`OnActivityResult` 会传递新创建文件的 `Android.Net.Uri`，可通过 `data.Data` 进行访问  。 此 uri 可用于将数据流式传输到新文件中：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

请注意，[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*) 
返回 `System.IO.Stream`，因此可以使用 .NET 编写整个流式传输过程。

有关使用存储访问框架加载、创建和编辑内容的详细信息，请参阅 [Android 的存储访问框架文档](https://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>打印

通过引入[打印服务](xref:Android.PrintServices)和 `PrintManager`，在 KitKat 中简化了内容的打印。 KitKat 还是第一个使用 [Google 云打印应用程序](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)充分利用 [Google 的云打印服务 API](https://developers.google.com/cloud-print/) 的 API 版本。
当 KitKat 附带的大多数设备首次连接到 WiFi 时，会自动下载 Google 云打印应用和 [HP 打印服务插件](https://play.google.com/store/apps/details?id=com.hp.android.printservice)。 用户可以通过导航到“设置”>“系统”>“打印”来检查其设备的打印设置  ：

[![“打印设置”屏幕的示例屏幕截图](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> 尽管打印 API 默认设置为使用 Google 云打印，但 Android 仍允许开发人员使用新的 API 准备打印内容，并将其发送给其他应用程序以处理打印。

#### <a name="printing-html-content"></a>打印 HTML 内容

KitKat 会自动使用 `WebView.CreatePrintDocumentAdapter` 为 Web 视图创建 [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)。 打印 Web 内容是 [`WebViewClient`](xref:Android.Webkit.WebViewClient)（等待 HTML 内容加载并让“活动”知道在选项菜单中提供打印选项）与“活动”（等待用户选择“打印”选项并对 `PrintManager` 调用 `Print`）之间的协调工作。 本部分介绍打印屏幕上 HTML 内容所需的基本设置。

请注意，加载和打印 Web 内容需要 Internet 权限：

[![在应用选项中设置 Internet 权限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>打印菜单项

“打印”选项通常显示在“活动”的[选项菜单](https://developer.android.com/guide/topics/ui/menus.html#options-menu)中。
选项菜单允许用户对“活动”执行操作。 它位于屏幕的右上角，如下所示：

[![屏幕右上角显示的“打印”菜单项的示例屏幕截图](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

可以在“资源”下的“菜单”目录中定义其他菜单项   。 下面的代码定义了一个名为[打印](xref:Android.Print.PrintManager)的示例菜单项：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

与“活动”中的选项菜单的交互通过 `OnCreateOptionsMenu` 和 `OnOptionsItemSelected` 方法进行。
`OnCreateOptionsMenu` 是从“菜单”资源目录添加新菜单项（例如“打印”选项）的位置  。
`OnOptionsItemSelected` 侦听用户是否从菜单中选择“打印”选项，然后开始打印：

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

上面的代码还定义了一个名为 `dataLoaded` 的变量来跟踪 HTML 内容的状态。 加载所有内容后，`WebViewClient` 会将此变量设置为 true，以便“活动”知道将“打印”菜单项添加到选项菜单。

##### <a name="webviewclient"></a>WebViewClient

`WebViewClient` 的工作是确保 `WebView` 中的数据完全加载，然后在菜单中显示“打印”选项，它与 `OnPageFinished` 方法相同。 `OnPageFinished` 侦听 Web 内容是否加载完成，并通知“活动”通过 `InvalidateOptionsMenu` 重新创建其选项菜单：

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` 还将 `dataLoaded` 值设置为 `true`，因此 `OnCreateOptionsMenu` 可以通过“打印”选项就地重新创建菜单。

##### <a name="printmanager"></a>PrintManager

下面的代码示例将打印 `WebView` 的内容：

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` 将以下内容作为参数：打印作业的名称（在本示例中为“MyWebPage”）、从内容生成打印文档的 [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) 
以及 [`PrintAttributes`](xref:Android.Print.PrintAttributes)
（在上面的示例中为 `null`）。 你可以指定 `PrintAttributes` 来帮助在打印页面上布局内容，尽管默认属性应该可以处理大多数情况。

调用 `Print` 会加载打印 UI，其中列出了打印作业的选项。 UI 为用户提供了将 HTML 内容打印或保存为 PDF 的选项，如以下屏幕截图所示：

[![显示“打印”菜单的 PrintHtmlActivity 的屏幕截图](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![显示“另存为 PDF”菜单的 PrintHtmlActivity 的屏幕截图](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware"></a>

## <a name="hardware"></a>硬件

KitKat 添加了多个 API 来容纳新的设备功能。 其中最值得注意的是基于主机的卡模拟和新的 `SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 中基于主机的卡模拟

基于主机的卡模拟 (HCE) 允许应用程序像 NFC 卡或 NFC 读卡器那样运行，而无需依赖于运营商的专有安全元件。 设置 HCE 之前，请使用 `PackageManager.HasSystemFeature` 确保 HCE 在设备上可用：

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 要求将 HCE 功能和 `Nfc` 权限都注册到应用程序的 `AndroidManifest.xml`：

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在应用选项中设置 NFC 权限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

要工作，HCE 必须能够在后台运行，即使未运行使用 HCE 的应用程序，也必须在用户进行 NFC 交易时启动。 可以通过将 HCE 代码编写为 `Service` 来实现此目的。 HCE 服务实现 `HostApduService` 接口，该接口实现以下方法：

- ProcessCommandApdu - 应用程序协议数据单元 (APDU) 是在 NFC 读卡器和 HCE 服务之间发送的内容  。 此方法使用读卡器中的 ADPU，并返回响应中的数据单位。

- OnDeactivated - 当 HCE 服务不再与 NFC 读卡器通信时，将停用此 `HostAdpuService` 。

还需要在应用程序清单中注册 HCE 服务，并使用适当的权限、意向筛选器和元数据对其进行修饰。 下面的代码是使用 `Service` 属性注册到 Android 清单的 `HostApduService` 的示例（有关属性的详细信息，请参阅 Xamarin 的[使用 Android 清单](~/android/platform/android-manifest.md)指南）：

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

上述服务为 NFC 读卡器提供了一种与应用程序交互的方式，但 NFC 读卡器仍无法知道此服务是否正在模拟需要扫描的 NFC 卡。 为了帮助 NFC 读卡器识别该服务，我们可以为该服务分配一个唯一的“应用程序 ID (AID)”  。 我们在注册了 `MetaData` 属性的 xml 资源文件中指定 AID 以及有关 HCE 服务的其他元数据（请参阅上面的代码示例）。 此资源文件指定一个或多个 AID 筛选器（以十六进制格式表示的唯一标识符字符串，对应于一个或多个 NFC 读卡器设备的 AID）：

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

除 AID 筛选器外，xml 资源文件还提供面向用户的 HCE 服务描述，指定 AID 组（付款应用程序与“其他”），对于付款应用程序，则向用户显示 260x96 dp 横幅。

上述设置提供模拟 NFC 卡的应用程序的基本构建基块。 NFC 本身需要执行更多步骤并进行进一步的测试才能配置。 有关基于主机的卡模拟的详细信息，请参阅 [Android 文档门户](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
有关将 NFC 与 Xamarin 配合使用的详细信息，请查看 [Xamarin NFC 示例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>Sensors

KitKat 可以通过 [`SensorManager`](xref:Android.Hardware.SensorManager) 访问设备传感器。
`SensorManager` 允许 OS 分批将传感器信息传送给应用程序，从而延长电池寿命。

KitKat 还附带了两种新的传感器类型，用于跟踪用户步骤。 这些传感器基于加速计，其中包括：

- StepDetector - 当用户执行一个步骤时，将通知/唤醒应用，并且检测器会为其提供执行该步骤的时间值  。

- StepCounter - 跟踪用户自注册传感器以来执行的步骤数，直到下一次重新启动设备   。

下面的屏幕截图展示了步骤计数器的实际运行：

[![显示步骤计数器的 SensorsActivity 应用的屏幕截图](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

你可以通过调用 `GetSystemService(SensorService)` 并将结果转换为 `SensorManager` 来创建 `SensorManager`。 要使用步骤计数器，请对 `SensorManager` 调用 `GetDefaultSensor`。 你可以注册传感器，并借助 [`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener) 接口侦听步骤计数的变化，
如下面的代码示例所示：

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

如果在应用程序处于前台时步骤计数更新，则调用 `OnSensorChanged`。 如果应用程序进入后台，或者设备处于睡眠模式，则不会调用 `OnSensorChanged`，但是在调用 `UnregisterListener` 之前，将继续对步骤进行计数。

请记住，在所有注册传感器的应用程序中，步骤计数都是累积的  。 这意味着，即使你卸载然后重新安装应用程序，并在应用程序启动时将 `count` 变量初始化为 0，传感器报告的值仍为注册传感器时执行的步骤总数，无论是你的应用程序还是其他应用程序都一样。 可以通过对 `SensorManager` 调用 `UnregisterListener` 来防止应用程序添加到步骤计数器，如以下代码所示：

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新启动设备会将步骤计数重置为 0。 你的应用将需要额外的代码，以确保它报告准确的应用程序计数，而不受使用传感器的其他应用程序或设备状态所影响。

> [!NOTE]
> 尽管 KitKat 附带了用于进行步骤检测和计数的 API，但并不是所有手机都配备了传感器。 你可以通过运行 `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);` 来检查传感器是否可用，也可检查以确保 `GetDefaultSensor` 的返回值不是 `null`。

<a name="developer_tools"></a>

## <a name="developer-tools"></a>开发人员工具

### <a name="screen-recording"></a>屏幕录制

KitKat 包括新的屏幕录制功能，以便开发人员能够记录应用程序的实际操作。 屏幕录制可通过 [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) 客户端提供，可作为 Android SDK 的一部分进行下载。

要录制屏幕，请连接设备，然后找到 Android SDK 安装项，导航到 platform-tools 目录，并运行 adb 客户端   ：

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令将以默认的 4Mbps 分辨率记录默认 3 分钟的视频。 要编辑时长，请添加 --time-limit 标志  。
要更改分辨率，请添加 --bit-rate 标志  。 以下命令将以 8Mbps 记录一分钟的视频：

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

可以在设备上找到你的视频。录制完成后，它将显示在你的库中。

## <a name="other-kitkat-additions"></a>其他 KitKat 新增功能

除上述更改外，KitKat 还允许：

- 使用全屏 - KitKat 引入了新的[沉浸式模式](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))来浏览内容、玩游戏，以及运行可从全屏体验中获益的其他应用程序  。

- 自定义通知 - 通过 [`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService) 获取有关系统通知的其他详细信息 
  。 这使你能够以不同的方式在应用中显示信息。

- 对可绘制资源进行镜像处理 - 可绘制资源具有新的 [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)  
  属性，该属性告知系统为需要左右翻转的图像创建镜像版本。

- 暂停动画 - 暂停和继续通过 [`Animator`](xref:Android.Animation.Animator) 类创建的 
  类的新实例。

- 读取动态更改的文本 - 使用新的 [`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)  
  属性将用新文本动态更新的 UI 部分表示为“活动区域”，以便在辅助功能模式下自动读取新文本。

- 增强音频体验 - 通过 [`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer) 使曲目声音更响亮  ，
  通过 [`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms) 类查找音频流的峰值和 RMS，
  并从[音频时间戳](xref:Android.Media.AudioTimestamp)获取信息以帮助实现音频视频同步。

- 在自定义间隔同步 ContentResolver - KitKat 为执行同步请求的时间增加了一些可变性  。 通过调用 `ContentResolver.RequestSync` 并传入 `SyncRequest`，在自定义时间或间隔同步 `ContentResolver`。

- 区分控制器 - 在 KitKat 中，为控制器分配了唯一的整数标识符，这些标识符可通过设备的 `ControllerNumber` 属性进行访问  。 这样就可以更轻松地在游戏中区分玩家。

- 远程控制 - 通过硬件和软件端的一些更改，KitKat 允许使用 `ConsumerIrService` 将配备 IR 发送器的设备转换为远程控制，并使用新的 [`RemoteController`](xref:Android.Media.RemoteController)  API 与外围设备进行
  访问。

有关上述 API 更改的详细信息，请参阅 Google [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html) 概述。

## <a name="summary"></a>总结

本文介绍了 Android 4.4（API 级别 19）中提供的一些新 API，并介绍了将应用程序转换为 KitKat 的最佳做法。 还概述了影响用户体验的 API 更改，包括转换框架和新的主题设置选项   。 接下来，介绍了存储访问框架和 `DocumentsProvider` 类，以及新的打印 API   。 然后，探讨了 NFC 中基于主机的卡模拟，以及如何使用低能耗传感器，包括两个用于跟踪用户步骤的新传感器   。 最后，演示了如何使用屏幕录制来捕获应用程序的实时演示，并提供了 KitKat API 更改和新增内容的详细列表  。

## <a name="related-links"></a>相关链接

- [KitKat 示例](/samples/xamarin/monodroid-samples/kitkat)
- [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)