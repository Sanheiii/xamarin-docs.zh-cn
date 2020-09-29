---
title: 在远程进程中运行 Android 服务
description: 通常，Android 应用程序中的所有组件都将在同一进程中运行。 Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。 本指南将介绍如何使用 Xamarin 创建和使用 Android 远程服务。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 14db376fbb40575cc3b11c5b23f0af6d32370503
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455323"
---
# <a name="running-android-services-in-remote-processes"></a>在远程进程中运行 Android 服务

_通常，Android 应用程序中的所有组件都将在同一进程中运行。Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。本指南将介绍如何使用 Xamarin 创建和使用 Android 远程服务。_

## <a name="out-of-process-services-overview"></a>进程外服务概述

当应用程序启动时，Android 会创建要在其中运行应用程序的进程。 通常，应用程序将在此过程中运行的所有组件。 Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。 这些类型的服务称为 _远程服务_ 或 _进程外服务_。 这些服务的代码将包含在与主应用程序相同的 APK 中;但是，当服务启动时，Android 将只为该服务创建一个新的进程。 相反，在与应用程序的其余部分相同的进程中运行的服务有时称为 " _本地服务_"。

通常，应用程序无需实现远程服务。 在许多情况下，本地服务足以 (和理想的应用程序要求) 。 一个进程外的内存空间必须由 Android 管理。 尽管这会对整个应用程序带来更多的开销，但在某些情况下，在其自身的进程中运行服务可能会有好处：

1. **共享功能** &ndash; 某些应用程序开发人员可以在所有应用程序之间共享多个应用和功能。 将运行在其自身进程中的 Android 服务中的该功能打包可简化应用程序维护。 还可以将该服务打包到其自己的独立 APK 中，并将其与应用程序的其余部分分开部署。
2. **改进用户体验** &ndash; 进程外服务可以通过两种方式来改善应用程序的用户体验。 第一种处理内存管理的方式。 如果垃圾回收 (GC) 循环发生，则 Android 将暂停进程中的所有活动，直到 GC 完成。 用户可能会认为此暂停为 "断断续续" 或 "jank"。 当服务在其自己的进程中运行时，它是暂停的服务进程，而不是应用程序进程。 当应用程序进程 (，因此，此暂停将不太明显，因此不会暂停用户界面) 。

    其次，如果进程的内存要求过大，则 Android 可能会终止该进程以释放设备的资源。 如果某个服务的内存占用量较大，并且它在与 UI 相同的进程中运行，则当 Android 强制回收这些资源时，UI 将关闭，并强制用户启动该应用程序。 但是，如果服务在其自己的进程中运行时由 Android 关闭，则 UI 进程将不受影响。 UI 可以绑定 (和重启) 服务，对用户透明，恢复正常工作。

3. **提高应用程序性能** &ndash; UI 进程可能会因服务进程而终止或关闭。 通过将较长的启动任务移动到进程外服务，UI 的启动时间可能会提高， (假设服务进程在) 启动 UI 的时间之间保持活动状态。

在许多方面，绑定到在另一个进程中运行的服务与 [绑定到本地服务](~/android/app-fundamentals/services/creating-a-service/bound-services.md)的操作相同。 `BindService`如果需要) 服务，则客户端将调用以绑定 (和 start。 `Android.OS.IServiceConnection`将创建一个对象，用于管理客户端和服务之间的连接。 如果客户端成功绑定到服务，则 Android 将通过返回一个对象，该对象 `IServiceConnection` 可用于对服务调用方法。 然后，客户端使用此对象与服务进行交互。 若要查看，请执行以下步骤以绑定到服务：

- **创建意向** &ndash; 必须使用显式目的才能绑定到服务。
- **实现并实例化 `IServiceConnection` 对象** &ndash;`IServiceConnection`对象充当客户端和服务之间的中介。  它负责监视客户端与服务器之间的连接。
- **调用 `BindService` 方法** &ndash; 调用 `BindService` 会将在前面的步骤中创建的意向和服务连接调度到 Android，这将负责启动该服务并建立客户端与服务之间的通信。

跨进程边界的需求会带来额外的复杂性：与服务器的通信是单向的 (客户端) ，客户端不能直接对服务类调用方法。 请记住，当服务运行与客户端相同的进程时，Android 会提供一个 `IBinder` 允许双向通信的对象。 如果服务在其自己的进程中运行，则不会出现这种情况。 客户端使用类的帮助与远程服务进行通信 `Android.OS.Messenger` 。

当客户端请求与远程服务绑定时，Android 将调用 `Service.OnBind` 生命周期方法，该方法将返回封装的内部 `IBinder` 对象 `Messenger` 。 `Messenger`是 Android SDK 提供的特殊实现的精简包装器 `IBinder` 。 `Messenger`负责处理两个不同进程之间的通信。 开发人员不关心了有关序列化消息、跨进程边界封送消息，然后在客户端上反序列化消息的详细信息。 此工作由 `Messenger` 对象处理。 此图显示了当客户端启动到进程外服务的绑定时所涉及的客户端 Android 组件：

![显示客户端绑定到服务的步骤和组件的关系图](out-of-process-services-images/ipc-01.png "显示客户端绑定到服务的步骤和组件的关系图。")

`Service`远程进程中的类将经历本地进程中的绑定服务将经历的相同生命周期回调，并且所涉及的许多 api 是相同的。 `Service.OnCreate` 用于初始化 `Handler` 并注入到 `Messenger` 对象中。 同样， `OnBind` 将重写，但该服务将返回，而不是返回 `IBinder` 对象 `Messenger` 。  此图说明了当客户端绑定到服务时，服务会发生什么情况：

![显示服务通过远程客户端绑定到的步骤和组件的关系图](out-of-process-services-images/ipc-02.png "此图显示了服务通过远程客户端绑定到时所经历的步骤和组件。")

当 `Message` 服务收到时，它将在的实例中处理 `Android.OS.Handler` 。 服务将实现其自己 `Handler` 的子类，该子类必须重写 `HandleMessage` 方法。 此方法由调用 `Messenger` ，并 `Message` 以参数的形式接收。 `Handler`将检查 `Message` 元数据并使用该信息对服务调用方法。

当客户端创建 `Message` 对象并使用方法将其调度到服务时，将发生单向通信 `Messenger.Send` 。 `Messenger.Send` 会将 `Message` 序列化的数据从中序列化到 Android，这会将消息路由到进程边界和服务。  `Messenger`由服务托管的会 `Message` 从传入数据创建一个对象。 此 `Message` 项放置在队列中，其中的消息一次提交到 `Handler` 。 `Handler`将检查中包含的元数据 `Message` ，并在上调用相应的方法 `Service` 。 下图说明了这些各个概念的操作：

![显示如何在进程之间传递消息的关系图](out-of-process-services-images/ipc-03.png "显示进程之间消息传递方式的关系图。")

本指南将讨论实现进程外服务的详细信息。 本文将讨论如何实现一个应在其自己的进程中运行的服务，以及客户端如何使用框架与该服务进行通信 `Messenger` 。 它还将简要讨论双向通信：客户端将消息发送到服务，并将消息发送回客户端。 由于可以在不同的应用程序之间共享服务，本指南还将讨论一种使用 Android 权限限制客户端对服务的访问的方法。

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-具有独立进程和自定义应用程序类的服务无法正确地](https://github.com/xamarin/xamarin-android/issues/1950) 报告在设置为时，不会正确启动 Xamarin 服务 `IsolatedProcess` `true` 。 本指南提供了参考。 Xamarin Android 应用程序仍应能够与用 Java 编写的进程外服务进行通信。

## <a name="requirements"></a>要求

本指南假定你熟悉如何创建服务。

尽管可以对面向较早 Android Api 的应用程序使用隐式意向，但本指南将专门介绍显式意图的使用。 面向 Android 5.0 (API 级别 21) 或更高版本的应用必须使用显式意图才能与服务绑定;本指南将演示这项技术。

## <a name="create-a-service-that-runs-in-a-separate-process"></a>创建在单独的进程中运行的服务

如上所述，服务在其自己的进程中运行的事实意味着涉及到一些不同的 Api。 下面是使用远程服务进行绑定和使用的步骤：  

- **创建 `Service` 子类** &ndash; 将类型划分为子类 `Service` 并实现绑定服务的生命周期方法。 还必须设置元数据，以通知 Android 服务要在自己的进程中运行。
- **实现一个 `Handler` **&ndash; `Handler` 负责分析客户端请求，提取从客户端传递的任何参数，并对服务调用适当的方法。
- **实例 `Messenger` 化**如上所述 &ndash; ，每个都 `Service` 必须维护类的实例， `Messenger` 该实例将客户端请求路由到 `Handler` 在上一步中创建的。

打算在其自身进程中运行的服务从根本上讲，仍是一项绑定服务。 服务类将扩展基类 `Service` ，并通过 `ServiceAttribute` 包含 android 需要在 android 清单中捆绑的元数据进行修饰。 首先是，的以下属性 `ServiceAttribute` 对于进程外服务非常重要：

1. `Exported`&ndash;必须将此属性设置为 `true` 以允许其他应用程序与该服务交互。 此属性的默认值为 `false`。
2. `Process`&ndash;必须设置此属性。 它用于指定服务将在其中运行的进程的名称。
3. `IsolatedProcess`&ndash;此属性将实现额外的安全性，告诉 Android 在隔离沙盒中运行该服务，并且具有与系统其余部分交互的最小权限。 请参阅 [Bugzilla 51940-具有独立进程的服务和自定义应用程序类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission`可以 &ndash; 通过指定客户端必须 (并被授予) 的权限来控制对服务的客户端访问。

若要运行服务自己的进程， `Process` 则上的属性 `ServiceAttribute` 必须设置为服务的名称。 若要与外部应用程序交互， `Exported` 应将属性设置为 `true` 。 如果 `Exported` 为 `false` ，则只有同一 APK 中的客户端 (（即同一个应用程序) 并在同一进程中运行）才能与服务交互。

服务将运行的进程类型取决于属性的值 `Process` 。 Android 标识三种不同类型的进程：

- **专用流程** &ndash; 专用进程是指仅适用于启动它的应用程序。 若要将进程标识为专用，其名称必须以 **：** (分号) 开头。 上面的代码片段中描述的服务和屏幕截图是一个专用进程。 以下代码片段是一个示例 `ServiceAttribute` ：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **全局进程** &ndash; 设备上运行的所有应用程序都可以访问在全局进程中运行的服务。 全局进程必须是以小写字符开头的完全限定类名。
     (除非采取措施来保护服务，否则其他应用程序可能会与之进行绑定和交互。 在本指南的后面部分将讨论如何保护服务不受未经授权的使用。 ) 

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- **独立进程** &ndash; 独立进程是在其自己的沙盒中运行的进程，该进程与系统的其余部分隔离，并且没有自己的特殊权限。 若要在隔离的进程中运行服务， `IsolatedProcess` 的属性将 `ServiceAttribute` 设置为， `true` 如以下代码片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 请参阅 [Bugzilla 51940-具有独立进程的服务和自定义应用程序类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

隔离服务是保护应用程序和设备不受信任代码的一种简单方法。 例如，应用可以从网站下载并执行脚本。 在这种情况下，在隔离的进程中执行此操作可针对危及 Android 设备的不受信任代码提供额外的安全层。

> [!IMPORTANT]
> 一旦导出了某个服务，该服务的名称将不会更改。 更改服务的名称可能会破坏正在使用该服务的其他应用程序。

若要查看 `Process` 属性具有的效果，请参阅以下屏幕截图，显示在其自己的专用进程中运行的服务：

![显示在专用进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-04.png "显示在专用进程中运行的服务的屏幕截图。")

下面的屏幕截图显示了 `Process="com.xamarin.xample.messengerservice.timestampservice_process"` 和在全局进程中运行的服务：

![全局进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-05.png "全局进程中运行的服务的屏幕截图。")

设置后 `ServiceAttribute` ，服务需要实现 `Handler` 。

### <a name="implementing-a-handler"></a>实现处理程序

若要处理客户端请求，服务必须实现 `Handler` ，并重写 `HandleMessage` 方法。 此方法采用一个实例， `Message` 该实例封装来自客户端的方法调用，并将调用转换为服务将执行的某个操作或任务。 `Message`对象公开一个名为的属性，该属性 `What` 是一个整数值，这是在客户端和服务之间共享的，并与服务为客户端执行的某些任务相关。

下面的示例应用程序中的代码片段演示了一个示例 `HandleMessage` 。 在此示例中，客户端可以请求服务的两个操作：

- 第一个操作是 _Hello，World_ 消息，客户端已向服务发送了简单消息。
- 第二个操作将对服务调用方法并检索字符串，在这种情况下，该字符串是一条消息，该消息返回服务启动的时间和运行时间：

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client has sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

还可以在中为服务打包参数 `Message` 。 本指南稍后将对此进行讨论。 下一个要考虑的主题是创建 `Messenger` 对象以处理传入的 `Message` 。

### <a name="instantiating-the-messenger"></a>实例化 Messenger

如前文所述，反序列化 `Message` 对象和调用 `Handler.HandleMessage` 是对象的责任 `Messenger` 。 `Messenger`类还提供 `IBinder` 客户端将用于向服务发送消息的对象。  

当服务启动时，它将实例化 `Messenger` 并插入 `Handler` 。 在服务的方法上执行此初始化的好地方 `OnCreate` 。 此代码片段是初始化自己的服务的一个示例 `Handler` `Messenger` ：

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此时，最后一步是用于 `Service` 重写的 `OnBind` 。

### <a name="implementing-serviceonbind"></a>实现 OnBind

所有绑定服务（无论是否在其自己的进程中运行）都必须实现 `OnBind` 方法。 此方法的返回值是一个对象，客户端可以使用它来与服务进行交互。 对象的确切内容取决于该服务是本地服务还是远程服务。 当本地服务将返回自定义 `IBinder` 实现时，远程服务将返回 `IBinder` 封装但在 `Messenger` 上一部分中创建的的：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

完成这三个步骤后，即可将远程服务视为已完成。

## <a name="consuming-the-service"></a>使用服务

所有客户端都必须实现某些代码才能绑定和使用远程服务。 从概念上讲，从客户端的角度来看，绑定到本地服务或远程服务之间的差别很小。 客户端调用 `BindService` 方法，并通过显式意图标识服务，并使用 `IServiceConnection` 来帮助管理客户端和服务之间的连接。

此代码片段举例说明如何创建绑定到远程服务的 **显式意图** 。 目的必须标识包含服务的包和服务的名称。 设置此信息的一种方法是使用 `Android.Content.ComponentName` 对象并将其提供给目的。 此代码段是一个示例：  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

绑定服务时，将 `IServiceConnection.OnServiceConnected` 调用方法并 `IBinder` 向客户端提供。 但是，客户端不会直接使用 `IBinder` 。 相反，它将实例化 `Messenger` 对象 `IBinder` 。 这是 `Messenger` 客户端将用于与远程服务进行交互的。

下面是一个非常基本的 `IServiceConnection` 实现示例，演示了客户端如何处理与服务的连接和断开连接。 请注意，该 `OnServiceConnected` 方法接收和 `IBinder` ，并且客户端将 `Messenger` 从创建 `IBinder` ：

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null;
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

创建服务连接和意向之后，客户端可以调用 `BindService` 并启动绑定过程：

```csharp
var serviceConnection = new TimestampServiceConnection(this);
BindService(serviceToStart, serviceConnection, Bind.AutoCreate);
```

当客户端成功绑定到服务并且 `Messenger` 可用时，客户端可以发送 `Messages` 到服务。

## <a name="sending-messages-to-the-service"></a>将消息发送到服务

如果客户端已连接并且具有 `Messenger` 对象，则可以通过分派对象来与服务进行通信 `Message` `Messenger` 。 此通信是单向的，客户端发送消息，但没有从服务到客户端的返回消息。 在这方面， `Message` 是一种防火机制。

创建对象的首选方法 `Message` 是使用 [`Message.Obtain`](xref:Android.OS.Message) 工厂方法。 此方法将 `Message` 从 Android 维护的全局池中提取对象。 `Message.Obtain` 还具有一些重载方法，这些方法允许 `Message` 使用服务所需的值和参数对对象进行初始化。  `Message`实例化后，它将通过调用调度到服务 `Messenger.Send` 。 此代码片段是创建和调度 `Message` 到服务进程的一个示例：

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

方法有多种不同的形式 `Message.Obtain` 。 前面的示例使用 [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain) 。 因为这是对进程外服务的异步请求，服务不会有响应，因此将 `Handler` 设置为 `null` 。 第二个参数 `Int32 what` 将存储在 `.What` 对象的属性中 `Message` 。 `.What`服务进程中的代码使用该属性来调用服务上的方法。

`Message`类还公开了两个可用于接收方的其他属性： `Arg1` 和 `Arg2` 。 这两个属性是整数值，可能在客户端和服务之间有一些特别同意的值。 例如， `Arg1` 可能会持有客户 ID，并 `Arg2` 可能包含该客户的采购订单号。 [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain)创建时，可用于设置这两个属性 `Message` 。 填充这两个值的另一种方法是，在 `.Arg` `.Arg2` `Message` 对象创建后直接在对象上设置和属性。

### <a name="passing-additional-values-to-the-service"></a>向服务传递其他值

可以通过使用将更复杂的数据传递给服务 `Bundle` 。 在这种情况下， `Bundle` `Message` 通过在发送之前设置[ `.Data` 属性](xref:Android.OS.Message.Data)属性，可以将额外的值置于中并与一起发送。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> 通常， `Message` 不应具有大于1mb 的有效负载。 大小限制可能因 Android 版本而异，并基于供应商对其实现 Android 开源项目的实现所需的任何专有更改， (AOSP) 与设备捆绑在一起。

## <a name="returning-values-from-the-service"></a>从服务返回值

此点已讨论的消息体系结构是单向的，客户端将向服务发送一条消息。 如果服务需要将值返回到客户端，则会反向讨论到此点的所有内容。 服务必须创建 `Message` 、打包任何返回值，并 `Message` 通过将通过发送 `Messenger` 到客户端。 但是，服务不会创建自己的， `Messenger` 而是依赖于客户端实例化并将 a 打包 `Messenger` 为初始请求的一部分。 服务将 `Send` 使用此客户端提供的消息 `Messenger` 。  

双向通信的事件序列如下：

1. 客户端绑定到服务。 当服务和客户端连接时， `IServiceConnection` 由客户端维护的将具有对 `Messenger` 用于将发送到服务的对象的引用 `Message` 。 为避免混淆，这称为 _服务信使_。
2. 客户端实例化 `Handler` (称为 _客户端处理程序_) 并使用它来初始化自己 `Messenger` 的 (_客户端 Messenger_) 。 请注意，服务信使和客户端 Messenger 是两个不同的对象，它们以两个不同的方向处理流量。 服务 Messenger 处理从客户端到服务的消息，而客户端 Messenger 将处理从服务到客户端的消息。
3. 客户端创建一个 `Message` 对象，并 `ReplyTo` 使用客户端 Messenger 设置该属性。 然后，使用服务信使将消息发送到服务。
4. 服务从客户端接收消息，并执行所请求的工作。
5. 当服务将响应发送到客户端时，它将使用 `Message.Obtain` 创建一个新的 `Message` 对象。
6. 若要将此消息发送到客户端，该服务将从客户端消息的属性中提取客户端 Messenger，并将其 `.ReplyTo` `.Send` 返回给 `Message` 客户端。
7. 当客户端收到响应时，它拥有自己 `Handler` 的，它会通过检查 (属性来处理该响应， `Message` 并在必要时 `.What` 提取) 包含的任何参数（如有必要） `Message` 。

此示例代码演示了客户端如何实例化 `Message` 和包 `Messenger` ，服务应将其用于响应：

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

此服务必须对其自身进行一些更改， `Handler` 以便提取 `Messenger` 并使用它将答复发送到客户端。 此代码片段举例说明了服务 `Handler` 将如何创建 `Message` 并将其发送回客户端：  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

请注意，在上面的代码示例中， `Messenger` 客户端创建的实例与服务*not*接收的对象不同。 这两个不同的 `Messenger` 对象在两个单独的进程中运行，表示信道。

## <a name="securing-the-service-with-android-permissions"></a>通过 Android 权限保护服务

在全局进程中运行的服务可由该 Android 设备上运行的所有应用程序访问。 在某些情况下，这种开放性和可用性是不需要的，而且需要保护服务以防未经授权的客户端的访问。 限制对远程服务的访问的一种方法是使用 Android 权限。

权限可通过 `Permission` 修饰子类的的属性来标识 `ServiceAttribute` `Service` 。 这将命名在绑定到服务时必须授予客户端的权限。 如果客户端没有相应的权限，则 `Java.Lang.SecurityException` 当客户端尝试绑定到服务时，Android 将引发。

Android 提供四种不同的权限级别：

- **正常** &ndash; 这是默认权限级别。 它用于标识可由 Android 自动向请求它的客户端授予的低风险权限。 用户不必显式授予这些权限，但可以在应用设置中查看权限。
- **签名** &ndash; 这是一种特殊的权限类别，由 Android 自动授予使用同一证书签名的应用程序。 此权限旨在使应用程序开发人员能够轻松地在应用程序之间共享组件或数据，而无需麻烦用户进行持续审批。
- **signatureOrSystem** &ndash; 这非常类似于上面所述的 **签名** 权限。 除了自动授予由同一证书签名的应用之外，还会将此权限授予已签名证书的应用，该证书用于对随 Android 系统映像安装的应用进行签名。 此权限通常仅由 Android ROM 开发人员用于允许其应用程序使用第三方应用。 这种情况通常不是指一般的公开分发的应用程序。
- **危险** &ndash; 危险权限是指那些可能会给用户带来问题的权限。 出于此原因，必须由用户显式批准 **危险** 权限。

由于 `signature` 和 `normal` 权限是在安装时由 Android 自动授予的，因此在包含客户端的 APK **之前** 安装 APK 托管服务是非常重要的。 如果首先安装客户端，则 Android 不会授予权限。 在这种情况下，需要卸载客户端 APK，安装服务 APK，然后重新安装客户端 APK。

使用 Android 权限保护服务的方法有两种：

1. **实现签名级别安全性** &ndash; 签名级别安全是指将权限自动授予那些使用同一密钥进行签名的应用程序，该密钥用于对保存该服务的 APK 进行签名。 这是开发人员保护其服务的一种简单方法，使其能够从他们自己的应用程序中进行访问。 通过将 `Permission` 的属性设置为来声明签名级别权限 `ServiceAttribute` `signature` ：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **创建自定义权限** &ndash; 服务的开发人员可以为该服务创建自定义权限。 这最适用于开发人员想要与其他开发人员的应用程序共享其服务的情况。 自定义权限需要更多精力才能实现并将在下面介绍。

下一节将介绍创建自定义权限的简化示例 `normal` 。 有关 Android 权限的详细信息，请参阅 Google 的文档，以了解 [& 安全性的最佳做法](https://developer.android.com/training/articles/security-tips.html)。 有关 Android 权限的详细信息，请参阅应用程序清单的 Android 文档的 " [权限" 部分](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) ，了解有关 android 权限的详细信息。

> [!NOTE]
> 通常，Google 会阻止 [使用自定义权限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) ，因为它们可能会给用户造成混淆。

### <a name="creating-a-custom-permission"></a>创建自定义权限

若要使用自定义权限，则在客户端显式请求该权限时，此服务将声明该权限。

若要在服务 APK 中创建权限，请将 `permission` 元素添加到 `manifest` **AndroidManifest.xml**中的元素。 此权限必须 `name` `protectionLevel` 设置、和 `label` 属性。 `name`特性必须设置为唯一标识该权限的字符串。 该名称将显示在 " **Android 设置**" 的 "**应用信息**" 视图中 (如下一部分 ") 所示。

`protectionLevel`特性必须设置为上面所述的四个字符串值之一。  `label`和 `description` 必须引用字符串资源，并用于向用户提供用户友好名称和说明。

此代码片段是 `permission` 在包含服务的 APK 的 **AndroidManifest.xml** 中声明自定义属性的示例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

然后，客户端 APK 的 **AndroidManifest.xml** 必须显式请求此新权限。 这是通过将属性添加 `users-permission` 到 **AndroidManifest.xml**来完成的：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>查看向应用程序授予的权限

若要查看应用程序的权限，请打开 "Android 设置" 应用，然后选择 " **应用**"。 在列表中找到并选择该应用程序。 在 " **应用信息** " 屏幕上，点击 " **权限** "，这将显示授予应用的所有权限：

[![来自 Android 设备的屏幕截图，显示了如何查找授予应用程序的权限](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>总结

本指南是有关如何在远程进程中运行 Android 服务的高级讨论。 介绍了本地和远程服务之间的差异，以及远程服务对 Android 应用程序的稳定性和性能有所帮助的一些原因。 在说明如何实现远程服务以及客户端如何与服务进行通信后，指南会提供一种方法，用于从仅授权客户端限制对服务的访问。

## <a name="related-links"></a>相关链接

- [Handler](xref:Android.OS.Handler)
- [消息](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [导出的属性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [具有隔离进程和自定义应用程序类的服务无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [进程和线程](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 清单-权限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全提示](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)