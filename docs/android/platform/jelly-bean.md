---
title: Jelly Bean 功能
description: 本文档将简要概述 Android 4.1 中引入的面向开发人员的新功能。 这些功能包括：增强型通知、共享大型文件的 Android Beam 的更新、多媒体更新、对等网络发现、动画和新权限。
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 55969993a4cb3755f5a26e681ae21bf993307a0a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456881"
---
# <a name="jelly-bean-features"></a>Jelly Bean 功能

_本文档将简要概述 Android 4.1 中引入的面向开发人员的新功能。这些功能包括：增强型通知、共享大型文件的 Android Beam 的更新、多媒体更新、对等网络发现、动画和新权限。_

## <a name="overview"></a>概述

Android 4.1（API 级别 16），也称为“Jelly Bean”，于 2012 年 7 月 9 日发布。 本文将简要介绍 Android 4.1 中面向使用 Xamarin.Android 的开发人员的一些新功能。 引入的一些新功能包括用于启动活动的动画的增强功能、相机的新声音以及改进的应用程序堆栈导航支持。 现在可以通过意向进行剪切和粘贴了。

通过隔离不稳定内容提供程序上的依赖项，Android 应用程序的稳定性得到了改善。 也可以隔离服务，以便只能由启动它们的活动访问。

添加了对使用 Bonjour、UPnP 或基于多播 DNS 服务的网络服务发现的支持。 现在可以实现包括格式化的文本、操作按钮和大图像的更丰富的通知。

最后，Android 4.1 添加了几个新权限。

## <a name="requirements"></a>要求

若要使用 Jelly Bean 开发 Xamarin.Android 应用程序，需要通过 Android SDK 管理器安装 Xamarin.Android 4.2.6 或更高版本以及 Android 4.1（API 级别 16），如以下屏幕截图所示：

[![在 Android SDK 管理器中选择 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>新增功能

### <a name="animations"></a>动画

可以通过使用 `ActivityOptions` 类使用缩放动画或自定义动画来启动活动。 提供了以下新方法来支持这些动画：

- `MakeScaleUpAnimation` – 此方法将创建一个动画，用于在屏幕上从起始位置和大小纵向扩展活动窗口。
- `MakeThumbnailScaleUpAnimation` – 此方法将创建一个动画，在屏幕的指定位置从缩略图进行纵向扩展。
- `MakeCustomAnimation` – 此方法将从应用程序中的资源创建动画。 其中一个动画在活动打开时使用，另一个动画在活动停止时使用。

新的 `TimeAnimator` 类提供一个 `TimeAnimator.ITimeListener` 接口，它会在每次动画中的帧发生变化时通知应用程序。 例如，请考虑以下 `TimeAnimator.ITimeListener` 实现：

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

现在，若要使用类，将创建一个 `TimeAnimator` 实例，并设置侦听器：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

当 `TimeAnimator` 实例正在运行时，它将调用 `ITimeAnimator.ITimeListener`，这将记录动画生成器的运行时间以及自上次调用方法以来它运行的时间。

### <a name="application-stack-navigation"></a>应用程序堆栈导航

Android 4.1 改进了 Android 3.0 中引入的应用程序堆栈导航。 通过指定 `ActivityAttribute` 的 `ParentName` 属性，当用户在操作栏上按[向上按钮](https://developer.android.com/design/patterns/navigation.html#up-vs-back)时，Android 会打开相应的父活动 - Android 将实例化 `ParentName` 属性指定的活动。 这使应用程序可以保留执行给定任务的活动的层次结构。

对于大多数应用程序来说，在活动中设置 `ParentName` 已经为 Android 提供了充足的信息，使其提供正确的行为来浏览应用程序堆栈；Android 将通过为每个父活动创建一系列意向来合成必要的后台堆栈。 但是，这是一个人工应用程序堆栈，因此每个合成活动都不会像自然活动那样具有保存状态。 若要为合成父活动提供保存状态，活动可以重写 `OnPrepareNavigationUpTaskStack` 方法。 此方法接收一个 `TaskStackBuilder` 实例，该实例将拥有一个意向对象集合，Android 将使用该集合创建后台堆栈。 活动可以修改这些意向，以便在创建合成活动时，它将收到正确的状态信息。

对于更复杂的场景，可以使用 Activity 类的新方法来处理向上导航的行为并构造后台堆栈：

- `OnNavigateUp` – 通过重写此方法，可以在按“向上”  按钮时执行自定义操作。
- `NavigateUpTo` – 调用此方法将导致应用程序从当前活动导航到给定的意向指定的活动。
- `ParentActivityIntent` –用于获取将启动当前活动的父活动的意向。
- `ShouldUpRecreateTask` – 此方法用于查询是否必须创建合成后台堆栈才能向上导航到父活动。 如果必须创建合成堆栈，则返回 `true`。 
- `FinishAffinity` –调用此方法将在当前任务中完成当前活动及其下所有具有相同任务关联性的活动。
- `OnCreateNavigateUpTaskStack` – 当需要完全控制如何创建合成堆栈时，将重写此方法。

### <a name="camera"></a>照相机

有一个新接口 `Camera.IAutoFocusMoveCallback`，该接口可用于检测自动对焦何时开始或停止移动。 可在以下代码片段中查看此新接口示例：

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

新类 `MediaActionSound` 提供了一组 API，用于为各种媒体操作生成声音。 相机可能会进行多种操作，这些操作由枚举 `Android.Media.MediaActionSoundType` 定义：

- `MediaActionSoundType.FocusComplete` – 对焦完成时播放的声音。
- `MediaActionSoundType.ShutterClick` – 拍摄静止图像时，将播放此声音。
- `MediaActionSoundType.StartVideoRecording` – 此声音用于指示开始录制视频。
- `MediaActionSoundType.StopVideoRecording` – 将播放此声音以指示视频录制结束。

下面的代码片段演示如何使用 `MediaActionSound` 类：

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```

### <a name="connectivity"></a>连接性

#### <a name="android-beam"></a>Android 无线发送

Android Beam 是一种基于 NFC 的技术，它允许两个 Android 设备相互通信。 Android 4.1 为传输大型文件提供了更好的支持。 使用新方法 `NfcAdapter.SetBeamPushUris()` 时，Android 将在备用传输机制（如蓝牙）之间切换，以实现快速传输。

#### <a name="network-services-discovery"></a>网络服务发现

Android 4.1 包含新的 API，用于基于多播 DNS 的服务发现。
这允许应用程序检测其他设备（如打印机、相机和媒体设备）并通过 Wi-Fi 与之连接。 这些新 API 位于 `Android.Net.Nsd` 包中。

若要创建可供其他服务使用的服务，可使用 `NsdServiceInfo` 类创建将定义服务属性的对象。 然后将此对象连同 `NsdManager.ResolveListener` 实现一起提供给 `NsdManager.RegisterService()`。 `NsdManager.ResolveListener` 实现用于通知注册成功并注销服务。

若要发现网络上的服务，请将 `Nsd.DiscoveryListener` 实现传递到 `NsdManager.discoverServices()`。

#### <a name="network-usage"></a>网络使用情况

`ConnectivityManager.IsActiveNetworkMetered` 是一种新方法，允许设备检查是否连接到按流量计费的网络。 此方法可用于帮助管理数据使用情况，具体做法是准确地告知用户数据操作可能会产生昂贵的费用。

#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服务发现

Android 4.0 中引入了 `WifiP2pManager` 类来支持 zeroconf  。 Zeroconf（零配置网络）是一组技术，使设备（计算机、打印机、电话）能够自动连接到网络，由人工网络操作员或特殊配置服务器进行干预。

在 Jelly Bean 中，`WifiP2pManager` 可以使用 Bonjour  或 Upnp  发现附近的设备。 Bonjour 是 Apple 的 zeroconf 实现。 Upnp 是一组同样支持 zeroconf 的网络协议。 将以下方法添加到 `WiFiP2pManager` 以支持 Wi-Fi 服务发现：

- `AddLocalService()` –此方法用于通过 Wi-Fi 声明应用程序即服务，供对等设备发现。
- `AddServiceRequest(`) – 此方法用于向框架发送服务发现请求。 它用于初始化 Wi-Fi 服务发现。
- `SetDnsSdResponseListeners()` – 此方法用于注册回调，以便在接收来自 Bonjour 的发现请求的响应时调用。
- `SetUpnpServiceResponseListener()` – 此方法用于注册回调，以便在接收对发现请求 Upnp 的响应时调用。

### <a name="content-providers"></a>内容提供商

`ContentResolver` 类收到了一个新方法 `AcquireUnstableContentProvider`。 此方法允许应用程序获取“不稳定”内容提供程序。 通常情况下，当应用程序获取内容提供程序，该内容提供程序崩溃时，应用程序也会崩溃。 使用此方法调用时，即使内容提供程序崩溃，应用程序也不会崩溃。 相反，会从内容提供程序的调用中引发 `Android.OS.DeadObjectionException`，以通知应用程序内容提供程序已消失。 与其他应用程序的内容提供程序交互时，“不稳定”内容提供程序会很有用，因为来自其他应用程序的错误代码不太可能影响到其他应用程序。

### <a name="copy-and-paste-with-intents"></a>通过意向复制和粘贴

`Intent` 类现在可以通过 `Intent.ClipData` 属性与 `ClipData` 对象关联。 此方法允许通过意向从剪贴板中传输额外的数据。 `ClipData` 实例可以包含一个或多个 `ClipData.Item`。 `ClipData.Item` 是以下类型的项：

- **Text** – 这是任何文本字符串，无论是 HTML 还是任何格式受内置 Android 样式范围支持的字符串。
- **意向** – 任意 `Intent` 对象。
- **Uri** – 可以是任何 URI，例如 HTTP 书签或内容提供程序的 URI。

### <a name="isolated-services"></a>独立服务

独立服务是在其自身的特殊进程下运行的服务，其本身不具备权限。 与该服务的唯一通信是在启动服务并通过服务 API 绑定到它时。 可以通过在 `ServiceAttribute` 中设置修饰服务类的 `IsolatedProcess="true"` 属性，将服务声明为独立服务。

### <a name="media"></a>媒体

新的 `Android.Media.MediaCodec` 类向低级别媒体编解码器提供一个 API。 应用程序可以查询系统以确定设备上可用的低级别编解码器。

添加了新的 `Android.Media.Audiofx.AudioEffect` 子类，以支持捕获音频上的其他音频预处理：

- `Android.Media.Audiofx.AcousticEchoCanceler` – 此类用于预处理音频，以从捕获的音频信号中删除远程方发出的信号。 例如，从语音通信应用程序中删除回声。
- `Android.Media.Audiofx.AutomaticGainControl` – 此类用于通过增加或降低输入信号来规范化捕获的信号，因此输出信号为常量。
- `Android.Media.Audiofx.NoiseSuppressor` – 此类将删除捕获信号中的背景噪音。

并非所有设备都支持这些效果。 应由应用程序调用 `AudioEffect.IsAvailable` 方法，以查看运行应用程序的设备是否支持所涉及的音频效果。

`MediaPlayer` 类现支持通过 `SetNextMediaPlayer()` 方法进行无缝播放。 此新方法指定当前媒体播放器完成播放时要启动的下一个 MediaPlayer。

以下新类提供了标准机制和 UI，可用于选择媒体的播放位置：

- `MediaRouter` – 此类允许应用程序控制从设备到外部扬声器或其他设备的媒体通道路由。
- `MediaRouterActionProvider` 和 `MediaRouteButton` – 这些类可帮助提供用于选择和播放媒体的一致 UI。

### <a name="notifications"></a>通知

Android 4.1 使应用程序更灵活，并控制显示通知。 现在，应用程序可以向用户显示更出彩的大型通知。 新方法 `NotificationBuilder.SetStyle()` 允许在通知上设置三种新样式之一：

- `Notification.BigPictureStyle` – 这是一个帮助程序类，它将生成包含图像的通知。 下图显示了包含大图像的通知示例：

 [![BigPictureStyle 通知示例屏幕截图](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle` – 这是一个帮助程序类，它将生成将包含多行文本（如电子邮件）的通知。 可以在以下屏幕截图中查看此新通知样式示例：

 [![BigTextStyle 通知示例屏幕截图](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle` – 这是一个帮助程序类，它将生成包含字符串列表的通知（如电子邮件中的代码段），如以下屏幕截图所示：

 [![Notification.InboxStyle 通知示例屏幕截图](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

通知使用普通样式或更大的样式时，可以在通知消息的底部添加最多两个操作按钮。
下面的屏幕截图显示了这一示例，其中操作按钮显示在通知底部：

 [![通知消息下显示的操作按钮示例屏幕截图](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification` 类收到了允许开发人员为通知指定五个优先级别之一的新常量。 可以使用 `Priority` 属性在通知上设置优先级。

### <a name="permissions"></a>权限

已添加以下新权限：

- `READ_EXTERNAL_STORAGE` - 应用程序需要对外部存储的只读访问权限。 默认情况下，所有应用程序都具有读取访问权限，但 Android 的未来版本将要求应用程序显式请求读取访问权限。
- `READ_USER_DICTIONARY` - 允许对用户字典进行读取访问。
- `READ_CALL_LOG` - 允许应用程序通过读取呼叫日志来获取有关传入和传出呼叫的信息。
- `WRITE_CALL_LOG` - 允许应用程序在电话上写入呼叫日志。
- `WRITE_USER_DICTIONARY` - 允许应用程序写入用户字典。

需要注意一项重要变更 `READ_EXTERNAL_STORAGE` – 此权限目前由 Android 自动授予。 未来版本的 Android 将要求应用程序在授予此权限之前请求权限。

## <a name="summary"></a>总结

本文介绍了一些可在 Android 4.1（API 级别 16）中使用的新 API。 它重点介绍了一些动画变更以及活动启用时动画效果的变化，并引入了新的 API，以便对使用 Bonjour 或 UPnP 等协议的其他设备实现网络发现。 还着重强调了对 API 的其他更改，如通过意向剪切和粘贴数据、可以使用独立服务或“不稳定”内容提供程序。

本文接着介绍了通知更新，并讨论了一些已在 Android 4.1 中引入的新权限

## <a name="related-links"></a>相关链接

- [时间动画示例（示例）](/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 API](https://developer.android.com/about/versions/android-4.1.html)
- [任务和后台堆栈](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [用返回和向上箭头导航](https://developer.android.com/design/patterns/navigation.html)