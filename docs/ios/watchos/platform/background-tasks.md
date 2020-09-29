---
title: Xamarin 中的 watchOS 后台任务
description: 本文档介绍如何在 Xamarin 中通过 watchOS 使用后台任务，并查看后台任务类型，使用资源、实现后台任务、计划、最佳实践等。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/13/2017
ms.openlocfilehash: 98f7f7b23fc437dee28f6797c3401bae472fa672
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433357"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Xamarin 中的 watchOS 后台任务

使用 watchOS 3，手表应用可通过以下三种主要方式将其信息保持在最新状态：

- 使用几个新的后台任务之一。
- 在手表面上有一个复杂的问题， (将) 提供额外的时间。
- 让用户将应用固定到新的停靠 (在其中保存在内存中，并经常更新) 。

## <a name="keeping-an-app-up-to-date"></a>使应用保持最新状态

在讨论开发人员可以让 watchOS 应用程序的数据和用户界面保持最新和更新的所有方法之前，此部分将查看一组典型的使用模式，以及用户在当天的 iPhone 与其 Apple Watch 之间的移动方式，这取决于当天的时间和当前正在 (执行的活动（如驱动) ）。

执行以下示例：

[![用户当天的 iPhone 及其 Apple Watch 之间的移动方式](background-tasks-images/update00.png)](background-tasks-images/update00.png#lightbox)

1. 在早上，在等待咖啡时，用户会在其 iPhone 上浏览当前新闻几分钟。
2. 在离开咖啡店之前，他们会快速查看天气，并看看其观看面上的难点。
3. 就餐之前，他们使用 iPhone 上的 Maps 应用来查找附近的餐馆，并预订保留以满足客户端的需要。
4. 旅行给餐馆后，他们会收到有关其 Apple Watch 的通知，并通过快速浏览了解其午餐约会是否正在延迟。
5. 在晚上，它们使用 iPhone 上的 Maps 应用来检查流量。
6. 在家里，它们会在其 Apple Watch 上收到 iMessage 通知，要求他们选取一些牛奶，并使用 "快速答复" 功能将响应发送到 "正常"。

由于 "速览" (小于三秒) 用户希望使用 Apple Watch 应用程序的方式，因此，应用程序在向用户显示所需的信息和更新其 UI 之前，通常没有足够的时间。

使用新的 Api Apple 已包含在 watchOS 3 中，应用可以计划 _后台刷新_ ，并在用户请求之前准备好所需的信息。 采用上述天气的示例：

[![天气的示例](background-tasks-images/update01.png)](background-tasks-images/update01.png#lightbox)

1. 应用计划在特定时间唤醒系统。
2. 此应用将获取生成更新时所需的信息。
3. 应用程序重新生成其用户界面，以反映新的数据。
4. 当用户深入了解应用程序时，它具有最新信息，用户无需等待更新。

如上所示，watchOS 系统使用一个或多个任务唤醒应用程序，该应用程序具有非常有限的池可用：

[![WatchOS 系统使用一个或多个任务唤醒应用](background-tasks-images/update02.png)](background-tasks-images/update02.png#lightbox)

Apple 建议充分利用此 (任务，因为它是应用程序的有限资源，因为它是应用程序) 的有限资源，直到应用程序完成自行更新过程。

系统通过调用委托的新方法来提供这些任务 `HandleBackgroundTasks` `WKExtensionDelegate` 。 例如：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Constructors
    public ExtensionDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request here
      ...
    }
    #endregion
  }
}
```

当应用完成了给定任务后，它会将其标记为 "已完成"，从而将其返回给系统：

[![任务通过将其标记为已完成来返回到系统](background-tasks-images/update03.png)](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks"></a>

## <a name="new-background-tasks"></a>新后台任务

watchOS 3 引入了多个后台任务，应用可以使用这些任务来更新其信息，确保在打开应用之前，该用户具有用户需要的内容，例如：

- **后台应用刷新** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) 任务允许应用在后台更新其状态。 通常，这会包括其他任务，例如，使用 [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)从 internet 下载新内容。
- **后台快照刷新** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 任务允许应用程序在系统拍摄用于填充停靠的快照之前更新其内容和 UI。
- **后台监视连接** -从配对的 iPhone 接收背景数据时，会为应用启动 [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) 任务。
- **后台 URL 会话** -在后台传输需要授权或 (成功或) 时，为应用启动 [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) 任务。

以下各节将详细介绍这些任务。

<a name="WKApplicationRefreshBackgroundTask"></a>

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是一种通用任务，可计划在将来的某个日期使应用唤醒：

[![未来日期的 WKApplicationRefreshBackgroundTask 唤醒](background-tasks-images/update04.png)](background-tasks-images/update04.png#lightbox)

在任务的运行时，应用可以执行任何类型的本地处理，例如更新复杂的时间线或使用获取一些必需的数据 `NSUrlSession` 。

<a name="WKURLSessionRefreshBackgroundTask"></a>

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

`WKURLSessionRefreshBackgroundTask`当数据完成下载并准备好由应用程序处理时，系统会发送。

[![数据下载完成后的 WKURLSessionRefreshBackgroundTask](background-tasks-images/update05.png)](background-tasks-images/update05.png#lightbox)

在后台下载数据时，应用程序不会处于运行状态。 相反，应用程序会计划数据请求，然后将其挂起，系统将处理数据下载，仅在下载完成时 reawakening 应用。

<a name="WKSnapshotRefreshBackgroundTask"></a>

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3 中，Apple 添加了插接，用户可在其中固定喜爱的应用并对其进行快速访问。 当用户按下 Apple Watch 上的侧按钮时，将显示固定应用程序快照的库。 用户可以向左或向右轻扫以查找所需的应用程序，然后点击应用程序以启动它，并将快照替换为正在运行的应用程序的接口。

[![将快照替换为正在运行的应用接口](background-tasks-images/update06.png)](background-tasks-images/update06.png#lightbox)

系统定期通过发送 `WKSnapshotRefreshBackgroundTask`) 并使用这些快照填充停靠来定期拍摄应用 UI (快照。 watchOS 使应用有机会在拍摄此快照之前更新其内容和 UI。

快照在 watchOS 3 中非常重要，因为它们充当应用程序的预览和启动映像。 如果用户在停靠中显示某个应用程序，它会扩展到全屏显示，进入前台并开始运行，因此，必须保持快照最新：

[![如果用户在停靠中的某个应用上进行了结算，它将扩展到全屏](background-tasks-images/update07.png)](background-tasks-images/update07.png#lightbox)

同样，系统将发出 a， `WKSnapshotRefreshBackgroundTask` 使应用可以通过在拍摄快照之前更新数据和 UI) 来准备 (：

[![在拍摄快照之前，应用可以通过更新数据和 UI 做好准备](background-tasks-images/update08.png)](background-tasks-images/update08.png#lightbox)

应用标记为 `WKSnapshotRefreshBackgroundTask` 已完成时，系统将自动拍摄应用 UI 的快照。

> [!IMPORTANT]
> 务必在 `WKSnapshotRefreshBackgroundTask` 应用收到新数据并更新其用户界面之后始终计划，否则，用户将看不到修改后的信息。

此外，当用户从应用程序收到通知并点击该通知以使应用程序进入前台时，快照必须是最新的，因为它也充当启动屏幕：

[![用户从应用程序收到通知，并点击该通知以使应用程序进入前台](background-tasks-images/update09.png)](background-tasks-images/update09.png#lightbox)

如果已超过一小时，因为用户已与 watchOS 应用交互，它将能够返回到其默认状态。 默认状态对于不同的应用程序可能有不同的含义，根据应用程序的设计，它可能根本没有默认状态。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask"></a>

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

在 watchOS 3 中，Apple 通过新的与后台刷新 API 集成了监视连接 `WKWatchConnectivityRefreshBackgroundTask` 。 使用这项新功能，iPhone 应用可以将最新的数据传递到其手表应用对应项，同时在后台运行 watchOS 应用程序：

[![IPhone 应用可以将新数据传递到其监视应用对应项，同时在后台运行 watchOS 应用](background-tasks-images/update10.png)](background-tasks-images/update10.png#lightbox)

启动复杂的推送、应用上下文、从 iPhone 应用发送文件或更新用户信息时，会在后台唤醒 Apple Watch 应用。

当通过唤醒监视应用时， `WKWatchConnectivityRefreshBackgroundTask` 将需要使用标准 API 方法接收 iPhone 应用中的数据。

[![WKWatchConnectivityRefreshBackgroundTask 数据流](background-tasks-images/update11.png)](background-tasks-images/update11.png#lightbox)

1. 确保会话已激活。
2. 监视新 `HasContentPending` 属性只要值为 `true` ，应用仍有要处理的数据。 与之前一样，应用程序应一直保存到任务，直到处理完所有数据为止。
3. 如果没有更多的数据要处理 (`HasContentPending = false`) ，请将任务标记为已完成以将其返回给系统。 如果无法执行此操作，将耗尽应用的分配后台运行时，从而导致崩溃报告。

<a name="The-Background-API-Lifecycle"></a>

## <a name="the-background-api-lifecycle"></a>后台 API 生命周期

将所有新的后台任务 API 放在一起，一组典型的交互如下所示：

[![后台 API 生命周期](background-tasks-images/update12.png)](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 应用计划一个后台任务，使其在将来的某个时间点而唤醒于。
2. 此应用唤醒系统并发送任务。
3. 应用程序将处理任务以完成所需的任何工作。
4. 作为处理任务的结果，应用程序可能需要计划更多的后台任务，以在将来完成更多的工作，如使用下载更多内容 `NSUrlSession` 。
5. 应用程序将任务标记为已完成，并将其返回给系统。

<a name="Using-Resources-Responsibly"></a>

## <a name="using-resources-responsibly"></a>使用资源的责任

WatchOS 应用程序在此生态系统中的工作方式很重要，因为它限制了对系统共享资源的消耗。

请查看以下方案：

[![WatchOS 应用限制了系统共享资源的消耗](background-tasks-images/update13.png)](background-tasks-images/update13.png#lightbox)

1. 用户在下午1:00 启动 watchOS 应用。
2. 应用计划一个任务在下午2:00 的一小时内唤醒和下载新内容。
3. 在下午1:50，用户可以重新打开应用程序，使其能够随时更新其数据和 UI。
4. 应用应重新计划任务，使其在晚于2:50 时运行一小时，而不是让任务在10分钟后再次唤醒应用。

虽然每个应用程序都不同，但 Apple 建议查找使用模式（如上面所示的模式），以帮助节省系统资源。

<a name="Implementing-Background-Tasks"></a>

## <a name="implementing-background-tasks"></a>实现后台任务

例如，本文档将使用向用户报告足球评分的虚假 MonkeySoccer 运动应用。

请看下面的典型使用方案：

[![典型使用方案](background-tasks-images/update14.png)](background-tasks-images/update14.png#lightbox)

用户的喜爱的足球团队正在播放从 7:00 PM 到 9:00 PM 的大匹配项，因此该应用程序应定期检查该分数，并决定30分钟的更新间隔。

1. 用户将打开该应用程序，并在30分钟后计划一个用于后台更新的任务。 后台 API 只允许在给定时间运行一种类型的后台任务。
2. 应用会接收该任务并更新其数据和 UI，并在30分钟后计划另一后台任务。 开发人员必须记住计划另一后台任务，否则应用程序将不会重新唤醒以获取更多更新，这一点很重要。
3. 同样，应用程序将接收该任务并更新其数据，更新其 UI，并在30分钟后计划另一后台任务。
4. 同一过程再次重复。
5. 接收到最后一个后台任务，应用再次更新其数据和 UI。 由于这是最终评分，因此不会计划新的后台刷新。

<a name="Scheduling-for-Background-Update"></a>

## <a name="scheduling-for-background-update"></a>计划后台更新

在上述方案中，MonkeySoccer 应用可以使用以下代码来计划后台更新：

```csharp
private void ScheduleNextBackgroundUpdate ()
{
  // Create a fire date 30 minutes into the future
  var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

  // Create
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
  userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

`NSDate`当应用程序想要唤醒并创建一个 `NSMutableDictionary` 来保存所请求任务的详细信息时，它将在将来创建30分钟。 的 `ScheduleBackgroundRefresh` 方法 `SharedExtension` 用于请求计划任务。

如果系统无法计划请求的任务，系统将返回 `NSError` 。

<a name="Processing-the-Update"></a>

## <a name="processing-the-update"></a>处理更新

接下来，请仔细查看5分钟窗口，其中显示了更新评分所需的步骤：

[![显示更新评分所需步骤的5分钟窗口](background-tasks-images/update15.png)](background-tasks-images/update15.png#lightbox)

1. 下午7:30:02，此应用程序会被系统唤醒，并提供更新后台任务。 第一种优先级是从服务器获取最新分数。 请参阅下面 [的计划 NSUrlSession](#Scheduling-a-NSUrlSession) 。
2. 在7:30:05，应用完成了原始任务，系统会将应用置于睡眠状态，并继续在后台下载请求的数据。
3. 当系统完成下载时，它将创建一个新任务来唤醒应用程序，以便它可以处理下载的信息。 请参阅 [处理后台任务](#Handling-Background-Tasks) 和 [处理下面的下载](#Handling-the-Download-Completing) 。
4. 应用保存更新后的信息并将任务标记为已完成。 开发人员目前可能会尝试更新应用程序的用户界面，但 Apple 建议计划快照任务来处理该过程。 请参阅下面 [的计划快照更新](#Scheduling-a-Snapshot-Update) 。
5. 应用会接收快照任务、更新其用户界面并将任务标记为已完成。 请参阅下面 [的处理快照更新](#Handling-a-Snapshot-Update) 。

<a name="Scheduling-a-NSUrlSession"></a>

## <a name="scheduling-a-nsurlsession"></a>计划 NSUrlSession

可以使用以下代码来计划下载最新分数：

```csharp
private void ScheduleURLUpdateSession ()
{
  // Create new configuration
  var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

  // Create new session
  var backgroundSession = NSUrlSession.FromConfiguration (configuration);

  // Create and start download task
  var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
  downloadTask.Resume ();
}
```

它配置并创建新的 `NSUrlSession` ，然后使用该会话通过方法创建新的下载任务 `CreateDownloadTask` 。 它调用 `Resume` 下载任务的方法来启动会话。

<a name="Handling-Background-Tasks"></a>

## <a name="handling-background-tasks"></a>处理后台任务

通过重写的 `HandleBackgroundTasks` 方法 `WKExtensionDelegate` ，应用程序可以处理传入的后台任务：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
    #endregion

    ...

    #region Public Methods
    public void CompleteTask (WKRefreshBackgroundTask task)
    {
      // Mark the task completed and remove from the collection
      task.SetTaskCompleted ();
      PendingTasks.Remove (task);
    }
    #endregion

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request
      foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Is this a background session task?
        var urlTask = task as WKUrlSessionRefreshBackgroundTask;
        if (urlTask != null) {
          // Create new configuration
          var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

          // Create new session
          var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

          // Keep track of all pending tasks
          PendingTasks.Add (task);
        } else {
          // Ensure that all tasks are completed
          task.SetTaskCompleted ();
        }
      }
    }
    #endregion

    ...
  }
}
```

`HandleBackgroundTasks`方法会在) 搜索的情况中，遍历系统向其发送了应用的所有任务 (`backgroundTasks` `WKUrlSessionRefreshBackgroundTask` 。 如果找到一个，则会重新加入会话并附加 `NSUrlSessionDownloadDelegate` 以处理下载完成 (请参阅 [处理以下下载完成](#Handling-the-Download-Completing)) ：

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

它会在任务完成后，通过将其添加到集合来保留任务的句柄：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有发送到应用的任务都需要完成，对于当前未处理的任何任务，请将其标记为 "已完成"：

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing"></a>

## <a name="handling-the-download-completing"></a>正在处理下载完成

MonkeySoccer 应用使用以下 `NSUrlSessionDownloadDelegate` 委托来处理完成的下载并处理请求的数据：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
  {
    #region Computed Properties
    public ExtensionDelegate WatchExtensionDelegate { get; set; }

    public WKRefreshBackgroundTask Task { get; set; }
    #endregion

    #region Constructors
    public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
    {
      // Initialize
      this.WatchExtensionDelegate = extensionDelegate;
      this.Task = task;
    }
    #endregion

    #region Override Methods
    public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
    {
      // Handle the downloaded data
      ...

      // Mark the task completed
      WatchExtensionDelegate.CompleteTask (Task);

    }
    #endregion
  }
}
```

在初始化时，它会保留生成它的和的句柄 `ExtensionDelegate` `WKRefreshBackgroundTask` 。 它重写 `DidFinishDownloading` 方法以处理完成的下载。 然后，使用的 `CompleteTask` 方法 `ExtensionDelegate` 通知任务已完成，并将其从挂起任务的集合中删除。 请参阅 [处理上述后台任务](#Handling-Background-Tasks) 。

<a name="Scheduling-a-Snapshot-Update"></a>

## <a name="scheduling-a-snapshot-update"></a>计划快照更新

下面的代码可用于计划快照任务，以使用最新分数更新 UI：

```csharp
private void ScheduleSnapshotUpdate ()
{
  // Create a fire date of now
  var fireDate = NSDate.FromTimeIntervalSinceNow (0);

  // Create user info dictionary
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
  userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

与 `ScheduleURLUpdateSession` 上面的方法类似，它会 `NSDate` 为应用程序要唤醒时创建新的，并创建 `NSMutableDictionary` 来保存所请求任务的详细信息。 的 `ScheduleSnapshotRefresh` 方法 `SharedExtension` 用于请求计划任务。

如果系统无法计划请求的任务，系统将返回 `NSError` 。

<a name="Handling-a-Snapshot-Update"></a>

## <a name="handling-a-snapshot-update"></a>处理快照更新

若要处理快照任务， `HandleBackgroundTasks` 方法 (参阅 [处理上述后台任务](#Handling-Background-Tasks)) 修改为如下所示：

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
  // Handle background request
  foreach (WKRefreshBackgroundTask task in backgroundTasks) {
    // Take action based on task type
    if (task is WKUrlSessionRefreshBackgroundTask) {
      var urlTask = task as WKUrlSessionRefreshBackgroundTask;

      // Create new configuration
      var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

      // Create new session
      var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

      // Keep track of all pending tasks
      PendingTasks.Add (task);
    } else if (task is WKSnapshotRefreshBackgroundTask) {
      var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

      // Update UI
      ...

      // Create a expiration date 30 minutes into the future
      var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

      // Create user info dictionary
      var userInfo = new NSMutableDictionary ();
      userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
      userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

      // Mark task complete
      snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
    } else {
      // Ensure that all tasks are completed
      task.SetTaskCompleted ();
    }
  }
}
```

方法测试正在处理的任务的类型。 如果是， `WKSnapshotRefreshBackgroundTask` 它可以访问任务：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

方法更新用户界面，然后创建一个 `NSDate` ，以告知系统快照将过时。 它 `NSMutableDictionary` 使用用户信息创建一个用于描述新快照的，并使用此信息标记已完成的快照任务：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它还会告诉快照任务，应用未返回到第一个参数) 中 (默认状态。 没有默认状态概念的应用应始终将此属性设置为 `true` 。

<a name="Working-Efficiently"></a>

## <a name="working-efficiently"></a>有效地工作

如前面的示例中所示，MonkeySoccer 应用程序用来更新其评分，通过高效地工作并使用新的 watchOS 3 后台任务，该应用只需15秒的活动状态：

[![此应用只会处于活动状态的时间超过15秒](background-tasks-images/update16.png)](background-tasks-images/update16.png#lightbox)

这会降低应用对可用 Apple Watch 资源和电池寿命的影响，还可让应用更好地与在手表上运行的其他应用程序一起工作。

<a name="How-Scheduling-Works"></a>

## <a name="how-scheduling-works"></a>计划的工作方式

当 watchOS 3 应用处于前台时，它将始终计划运行，并可执行所需的任何类型的处理（如更新数据或重绘其 UI）。 当应用移到后台时，它通常被系统挂起，所有运行时操作都将暂停。

当应用处于后台时，系统可能会将其设定为目标，以快速运行特定的任务。 因此，在 watchOS 2 中，系统可能会暂时唤醒后台应用程序以执行操作，例如处理长时间通知或更新应用程序的问题。 在 watchOS 3 中，有几种新方法可在后台运行应用。

当应用处于后台时，系统对其施加了几个限制：

- 只有几秒钟时间才能完成任何给定的任务。 系统不仅要考虑所经过的时间量，还需要考虑应用程序使用多少 CPU 能力来派生此限制。
- 超出其限制的任何应用都将终止，并出现以下错误代码：
  - **CPU** -0xc51bad01
  - **时间** -0xc51bad02
- 系统会根据要求应用执行的后台任务类型来施加不同的限制。 例如，在 `WKApplicationRefreshBackgroundTask` `WKURLSessionRefreshBackgroundTask` 其他类型的后台任务上，将为和任务提供稍长的运行时。

<a name="Complications-and-App-Updates"></a>

### <a name="complications-and-app-updates"></a>复杂和应用更新

除了 Apple 添加到 watchOS 3 的新后台任务外，watchOS 应用的复杂程度还会影响应用接收后台更新的方式和时间。

复杂的是提供有用信息的小视觉元素。 根据所选的监视面，用户可以自定义一个或多个会在 watchOS 3 中的监视应用程序中提供的一个或多个复杂的监视面。

如果用户在其手表面上包含某个应用的复杂问题，则会为应用提供以下更新的优点：

- 它会导致系统使应用程序处于随时可用状态，在该状态下，它会尝试在后台启动应用程序，将其保存在内存中，并提供额外的更新时间。
- 最复杂的一天保证至少50推送更新。

开发人员应始终努力为其应用程序带来引人注目的复杂性，以使用户能够根据上面列出的原因将其添加到其手表中。

在 watchOS 2 中，这是应用在后台收到运行时的主要方式。 在 watchOS 3 中，仍将确保一项复杂的应用程序每小时接收多个更新，但它可以使用 `WKExtensions` 请求更多运行时来更新其复杂性。

请查看以下用于更新连接的 iPhone 应用中的难点的代码：

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

  // Get session and the number of remaining transfers
  var session = WCSession.DefaultSession;
  var transfers = session.RemainingComplicationUserInfoTransfers;

  // Create user info dictionary
  var iconattrs = new Dictionary<NSString, NSObject>
    {
      {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
      {new NSString ("reason"), new NSString ("UpdateScore")}
    };

  var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

  // Take action based on the number of transfers left
  if (transfers < 1) {
    // No transfers left, either attempt to send or inform
    // user of situation.
    ...
  } else if (transfers < 11) {
    // Running low on transfers, only send on important updates
    // else conserve for a significant change.
    ...
  } else {
    // Send data
    session.TransferCurrentComplicationUserInfo (userInfo);
  }
}
```

它使用 `RemainingComplicationUserInfoTransfers` 的属性 `WCSession` 来查看应用在一天中剩余的高优先级传输数，然后根据该数字采取措施。 如果应用程序的传输时间不足，则它可以在发送次要更新时保持不变，并且仅在发生重大更改时发送信息。

<a name="Scheduling-and-Dock"></a>

### <a name="scheduling-and-the-dock"></a>计划和停靠

在 watchOS 3 中，Apple 添加了插接，用户可在其中固定喜爱的应用并对其进行快速访问。 当用户按下 Apple Watch 上的侧按钮时，将显示固定应用程序快照的库。 用户可以向左或向右轻扫以查找所需的应用程序，然后点击应用程序以启动它，并将快照替换为正在运行的应用程序的接口。

[![停靠](background-tasks-images/dock01.png)](background-tasks-images/dock01.png#lightbox)

系统会定期拍摄应用 UI 的快照，并使用这些快照来填充文档。watchOS 使应用有机会在拍摄此快照之前更新其内容和 UI。

已固定到停靠的应用可能需要以下各项：

- 它们最少每小时更新一次。 这包括应用刷新任务和快照任务。
- 更新预算在停靠中的所有应用之间分布。 因此，用户所固定的应用越少，每个应用接收的潜在更新就越多。
- 该应用程序将保留在内存中，以便在从插接中选择应用程序时，应用程序将会快速恢复。

用户运行的最后一个应用将被视为 _最近使用_ 的应用，并且将占据停靠中的最后一个槽。 用户可以选择将其永久固定到停靠。 最近使用的将视为用户已固定到停靠的任何其他最喜爱的应用。

> [!IMPORTANT]
> 只有添加到主屏幕的应用才会获得定期计划。 若要接收定期计划和后台更新， _必须_ 将应用添加到停靠。

如本文档前面所述，快照在 watchOS 3 中非常重要，因为它们充当应用程序的预览和启动映像。 如果用户在停靠中显示某个应用程序，它会扩展到全屏显示，进入前台并开始运行，因此，必须保持快照最新状态。

有时，系统可能会确定它需要应用 UI 的新快照。 在这种情况下，快照请求不会对应用的运行时预算计数。 以下操作将触发系统快照请求：

- 较复杂的时间线更新。
- 用户与应用程序的通知交互。
- 从前台切换到后台状态。
- 在后台状态一小时后，应用可以恢复为默认状态。
- 首次启动 watchOS 时。

<a name="Best-Practices"></a>

## <a name="best-practices"></a>最佳实践

Apple 建议在使用后台任务时采用以下最佳做法：

- 在应用需要更新时按计划进行。 每次应用运行时，它应该重新评估其未来需求，并根据需要调整此计划。
- 如果系统发送后台刷新任务，且应用程序不需要更新，请推迟工作，直到实际需要更新。
- 考虑可用于应用的所有运行时机会：
  - 停靠和前台激活。
  - 通知。
  - 更新。
  - 后台刷新。
- 用于 `ScheduleBackgroundRefresh` 常规用途后台运行时，例如：
  - 轮询系统以获取信息。
  - 计划将来 `NSURLSessions` 请求背景数据。
  - 已知时间转换。
  - 触发复杂的更新。

<a name="Snapshot-Best-Practices"></a>

## <a name="snapshot-best-practices"></a>快照最佳实践

使用快照更新时，Apple 提出以下建议：

- 仅在需要时（例如，在有重大内容更改时）使快照无效。
- 避免高频率快照失效。 例如，计时器应用不应每秒更新一次快照，只应在计时器结束时执行。

<a name="App-Data-Flow"></a>

## <a name="app-data-flow"></a>应用数据流

Apple 建议使用以下项来处理数据流：

[![应用数据流关系图](background-tasks-images/update17.png)](background-tasks-images/update17.png#lightbox)

外部事件 (如监视连接) 唤醒应用。 这会强制应用程序更新 (表示应用程序当前状态) 的数据模型。 由于数据模型更改，应用将需要更新其复杂性，请求新的快照，可能会启动一个后台 `NSURLSession` 来请求更多的数据，并计划进一步的后台刷新。

<a name="The-App-Lifecycle"></a>

## <a name="the-app-lifecycle"></a>应用生命周期

由于可以将收藏的应用固定到一起，因此 Apple 认为用户将在更多的应用之间移动，更常见的是，它们都是 watchOS 2。 因此，应用应准备好处理此更改，并快速在前台和后台状态之间移动。

Apple 提供以下建议：

- 请确保应用程序在进入前台激活时尽快完成所有后台任务。
- 确保在通过调用进入背景之前完成所有前景工作 `NSProcessInfo.PerformExpiringActivity` 。
- 在 watchOS 模拟器中测试应用时，不会强制执行任何任务预算，因此应用可以尽可能多地刷新，以正确地测试功能。
- 请始终在实际 Apple Watch 硬件上进行测试，以确保应用在发布到 iTunes Connect 之前不会在其预算之外运行。
- Apple 建议在测试和调试时在充电器上保留 Apple Watch。
- 请确保完全测试了冷启动和恢复应用。
- 验证所有应用任务是否已完成。
- 更改停靠中固定的应用数量，以测试最佳和最差情况。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了 Apple 对 watchOS 的增强功能，以及如何使用它们来使手表应用保持最新状态。 首先，它涵盖了 Apple 添加到 watchOS 3 中的所有新后台任务。 然后，它介绍了后台 API 生命周期，以及如何实现 Xamarin watchOS 应用程序中的后台任务。 最后，它介绍了计划的工作方式，并提供了一些最佳实践。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)