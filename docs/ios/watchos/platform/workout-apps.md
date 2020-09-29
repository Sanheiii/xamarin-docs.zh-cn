---
title: Xamarin 中的 watchOS 健身应用
description: 本文介绍 Apple 在 watchOS 3 中对健身应用的增强功能，以及如何在 Xamarin 中实现这些功能。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 826d8c982e9976cb4147c0ed77a55a4b1d18f879
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432403"
---
# <a name="watchos-workout-apps-in-xamarin"></a>Xamarin 中的 watchOS 健身应用

_本文介绍 Apple 在 watchOS 3 中对健身应用的增强功能，以及如何在 Xamarin 中实现这些功能。_

WatchOS 3 中的新功能，健身相关应用可以在后台运行 Apple Watch 并获得 HealthKit 数据的访问权限。 基于其父 iOS 10 的应用还可以在无需用户干预的情况下启动基于 watchOS 3 的应用。

将详细介绍以下主题：

## <a name="about-workout-apps"></a>关于健身应用

健康和健身应用的用户可能是高度专用的，花费几个小时的时间向其运行状况和适用性目标。 因此，他们希望能够提供响应、易于使用的应用程序，以便准确地收集和显示数据，并与 Apple Health 无缝集成。

设计良好的健康或健身应用可帮助用户将其活动图与适合目标联系在一起。 通过使用 Apple Watch，健身和健身应用可以即时访问心率、热量刻录和活动检测。

[![健康和健身应用示例](workout-apps-images/workout01.png)](workout-apps-images/workout01.png#lightbox)

WatchOS 3 中的新增功能， _运行后台_ 的应用程序可以在后台运行 Apple Watch 并获得 HealthKit 数据的访问权限。

本文档将介绍后台运行功能，涵盖健身应用生命周期，并显示健身应用如何对 Apple Watch 上的用户 _活动环_ 做出贡献。

## <a name="about-workout-sessions"></a>关于健身会话

每个健身应用程序的核心都_Workout Session_是 (`HKWorkoutSession`) 用户可以启动和停止的健身会话。 健身会话 API 易于实现，并为健身应用提供了若干优点，例如：

- 基于活动类型的运动和热量刻录检测。
- 用户活动环的自动贡献。
- 在会话中，无论用户何时唤醒设备，都将自动显示应用程序 (通过提高手腕或与 Apple Watch) 进行交互。

## <a name="about-background-running"></a>关于后台运行

如前所述，使用 watchOS 3 时，可以将健身应用设置为在后台运行。 使用后台运行的应用程序可以在后台运行时，处理来自 Apple Watch 传感器的数据。 例如，应用程序可以继续监视用户的心率，即使它不再显示在屏幕上也是如此。

后台运行还提供了在活动健身会话期间随时向用户显示实时反馈的功能，如发送 haptic 警报以通知用户当前进度。

此外，通过后台运行，应用程序可以快速更新其用户界面，使用户能够快速浏览 Apple Watch 的数据。

为了保持 Apple Watch 上的高性能，使用后台运行的监视应用程序应限制用于节省电池的后台工作的量。 如果应用在后台使用过多 CPU，则 watchOS 会将其挂起。

### <a name="enabling-background-running"></a>正在启用后台运行

若要启用后台运行，请执行以下操作：

1. 在 **解决方案资源管理器**中，双击监视扩展的 "配套 iPhone" 应用的 `Info.plist` 文件以将其打开以进行编辑。
2. 切换到 **源** 视图： 

    [![源视图](workout-apps-images/plist01.png)](workout-apps-images/plist01.png#lightbox)
3. 添加一个名为的新项 `WKBackgroundModes` ，并将 **类型** 设置为 `Array` ： 

    [![添加名为 WKBackgroundModes 的新项](workout-apps-images/plist02.png)](workout-apps-images/plist02.png#lightbox)
4. 将新项添加到 **类型** 为的数组 `String` ，并将值添加到 `workout-processing` ： 

    [![将新项添加到数组，其中包含字符串的类型和一个健身处理的值](workout-apps-images/plist03.png)](workout-apps-images/plist03.png#lightbox)
5. 保存对文件所做的更改。

## <a name="starting-a-workout-session"></a>启动健身会话

启动健身会话有三个主要步骤：

[![启动健身会话的三个主要步骤](workout-apps-images/workout02.png)](workout-apps-images/workout02.png#lightbox)

1. 应用必须请求授权才能访问 HealthKit 中的数据。
2. 为正在启动的健身类型创建健身配置对象。
3. 使用新创建的健身配置创建并启动健身会话。

### <a name="requesting-authorization"></a>请求授权

在应用程序可以访问用户的 HealthKit 数据之前，它必须请求和接收来自用户的授权。 根据健身应用的性质，它可能会产生以下类型的请求：

- 授权写入数据：
  - Workouts
- 授权读取数据：
  - 已刻录能量
  - 距离
  - 心率  

在应用可以请求授权之前，需要将它配置为访问 HealthKit。

执行以下操作：

1. 在“解决方案资源管理器”**** 中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
2. 滚动到底部并选中 " **启用 HealthKit**"： 

    [![选中 "启用 HealthKit"](workout-apps-images/auth01.png)](workout-apps-images/auth01.png#lightbox)
3. 保存对文件所做的更改。
4. 按照 [显式应用 id 和预配配置文件](~/ios/platform/healthkit.md) 中的说明进行操作，并将 [应用程序 Id 和预配配置文件与](~/ios/platform/healthkit.md) HealthKit 文章 [简介](~/ios/platform/healthkit.md) 的 Xamarin 应用程序部分关联，以正确预配应用程序。
5. 最后，使用[编程运行状况工具包](~/ios/platform/healthkit.md)中的说明，并向[HealthKit 文章简介](~/ios/platform/healthkit.md)的 User 节请求[权限](~/ios/platform/healthkit.md)，请求授权访问用户的 HealthKit 数据存储。

### <a name="setting-the-workout-configuration"></a>设置健身配置

健身会话是使用健身配置对象创建的 (`HKWorkoutConfiguration`) 指定健身类型 (如 `HKWorkoutActivityType.Running`) 和健身位置 (例如 `HKWorkoutSessionLocationType.Outdoor`) ：

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>创建健身会话委托 

若要处理在健身会话过程中可能发生的事件，应用程序需要创建健身会话委托实例。 向项目中添加一个新类，并将其作为类的基础 `HKWorkoutSessionDelegate` 。 对于户外运行的示例，该示例可能如下所示：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }
    #endregion
  }
}
```

此类创建多个事件，这些事件将在 () 时引发， `DidChangeToState` 并且如果健身会话 () 失败，则会引发这些事件 `DidFail` 。 

### <a name="creating-a-workout-session"></a>创建健身会话

使用上面创建的健身配置和健身会话委托创建新的健身会话，并根据用户的默认 HealthKit 存储区进行启动：

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
  // Create a workout configuration
  var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
  };

  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (configuration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

如果应用启动此健身会话，并且用户切换回其观看面，则会在人脸上方显示一个小绿色的 "正在运行" 图标：

[![正面显示的一个小绿色运行的人图标](workout-apps-images/workout03.png)](workout-apps-images/workout03.png#lightbox)

如果用户点击此图标，则会将其返回到应用。

## <a name="data-collection-and-control"></a>数据收集和控制

配置并启动健身会话后，应用将需要收集有关会话的数据 (例如用户的心率) 并控制会话的状态：

[![数据收集和控制关系图](workout-apps-images/workout04.png)](workout-apps-images/workout04.png#lightbox)

1. **观察示例** -应用需要检索 HealthKit 中的信息，这些信息将处理并显示给用户。
2. **观察事件** -应用将需要响应 HealthKit 或应用的 UI 生成的事件， (例如暂停健身) 的用户。
3. **进入运行状态** -会话已启动且当前正在运行。
4. **进入暂停状态** -用户已暂停当前健身会话，可以在以后重新启动。 用户可以在单个健身会话中多次切换运行状态和暂停状态。
5. **结束健身会话** -用户可以在任何时间点结束测验会话，或者，如果它是按流量计费的 (，例如两英里运行) ，它可能会过期并自行结束。

最后一步是将健身会话的结果保存到用户的 HealthKit 数据存储中。

### <a name="observing-healthkit-samples"></a>观察 HealthKit 示例

应用将需要为其感兴趣的每个 HealthKit 数据点打开一个 _定位点对象查询_ ，如心率或已刻录的活动能量。 对于观察到的每个数据点，将需要创建更新处理程序，以便在将新数据发送到应用时捕获它们。

根据这些数据点，应用程序可以累积合计 (例如总运行距离) 并根据需要更新其用户界面。 此外，应用程序可以在用户达到特定目标或成就（例如完成下一英里的运行）时通知用户。

请看下面的示例代码：

```csharp
private void ObserveHealthKitSamples ()
{
  // Get the starting date of the required samples
  var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

  // Get data from the local device
  var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
  var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

  // Assemble compound predicate
  var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

  // Get ActiveEnergyBurned
  var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
    // Valid?
    if (error == null) {
      // Yes, process all returned samples
      foreach (HKSample sample in addedObjects) {
        var quantitySample = sample as HKQuantitySample;
        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
      }
      
      // Update User Interface
      ...
    }
  });

  // Start Query
  HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                        
}
```

它会创建一个谓词，用于设置要使用方法获取数据的开始日期 `GetPredicateForSamples` 。 它会创建一组设备以从使用方法拉取 HealthKit 信息 `GetPredicateForObjectsFromDevices` ，在本例中，本地 Apple Watch 仅 (`HKDevice.LocalDevice`) 。 使用方法将两个谓词合并为 () 的复合谓词 `NSCompoundPredicate` `CreateAndPredicate` 。

将 `HKAnchoredObjectQuery` 为所需的数据点创建一个新的 (在本例中， `HKQuantityTypeIdentifier.ActiveEnergyBurned` 对于活动的能源烧制数据点) ，不会对)  (返回的数据量施加限制， `HKSampleQuery.NoLimit` 并定义更新处理程序来处理从 HealthKit 返回到应用程序的数据。 

每当将新数据传递到给定数据点的应用时，都将调用更新处理程序。 如果未返回任何错误，应用程序可以安全地读取数据，进行任何所需的计算，并根据需要更新其 UI。

该代码将循环使用 `HKSample` 数组中返回 () 的所有示例 `addedObjects` ，并将其转换为数量示例 (`HKQuantitySample`) 。 然后，它获取样本的双精度值作为 joule (`HKUnit.Joule`) 并将其累积到用于健身的活动能源的运行总计，并更新用户界面。

### <a name="achieved-goal-notification"></a>实现目标通知

如上所述，当用户实现健身应用中的目标时 (例如完成运行) 的第一英里时，它可以通过 Taptic Engine 向用户发送 haptic 反馈。 此时，该应用程序还应更新它的 UI，因为用户可能会提高手腕，以查看提示反馈的事件。

若要播放 haptic 的反馈，请使用以下代码：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>观察事件

事件是应用程序可用于在用户健身期间突出显示某些点的时间戳。 某些事件将由应用程序直接创建并保存到健身中，某些事件将由 HealthKit 自动创建。

若要观察由 HealthKit 创建的事件，应用程序将重写 `DidGenerateEvent` 的方法 `HKWorkoutSessionDelegate` ：

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Save HealthKit generated event
  WorkoutEvents.Add (@event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Lap:
    break;
  case HKWorkoutEventType.Marker:
    break;
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

Apple 在 watchOS 3 中添加了以下新的事件类型：

- `HKWorkoutEventType.Lap` -适用于将测验分成相等距离部分的事件。 例如，用于在运行时标记一个重叠。
- `HKWorkoutEventType.Marker` -适用于健身中感兴趣的任意点。 例如，到达户外运行的路由上的特定点。

这些新类型可由应用程序创建，并存储在健身中，供以后用于创建关系图和统计信息。

若要创建标记事件，请执行以下操作：

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
  // Create and save marker event
  var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
  WorkoutEvents.Add (markerEvent);

  // Notify user
  NotifyUserOfReachedMileGoal (++MilesRun);
}
```

此代码 () 创建标记事件的新实例 `HKWorkoutEvent` ，并将其保存到 (的专用事件集合，稍后将其写入到测验会话) 并通过 haptics 通知事件用户。

### <a name="pausing-and-resuming-workouts"></a>暂停和恢复 Workouts

在测验期中，用户可以暂时暂停健身，稍后再恢复。 例如，他们可能会暂停室内运行以执行重要调用，并在调用完成后继续运行。

应用的 UI 应提供一种方法，通过调用 HealthKit) 来暂停和恢复健身 (，以便 Apple Watch 可以在用户挂起其活动时保留电源和数据空间。 此外，该应用应忽略在健身会话处于暂停状态时可能收到的任何新数据点。

HealthKit 将通过生成暂停和恢复事件来响应暂停和恢复调用。 当健身会话暂停时，不会通过 HealthKit 将新的事件或数据发送到该应用，直到该会话恢复。

使用以下代码暂停和恢复健身会话：

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
  // Pause the current workout
  HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
  // Pause the current workout
  HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

可以通过重写的方法来处理将从 HealthKit 生成的暂停和恢复事件 `DidGenerateEvent` `HKWorkoutSessionDelegate` ：

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);

  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

### <a name="motion-events"></a>运动事件

另一种是 watchOS 3 的新手，它是暂停 (`HKWorkoutEventType.MotionPaused`) 和恢复 (`HKWorkoutEventType.MotionResumed`) 事件的动作。 当用户启动和停止移动时，HealthKit 会在运行的健身期间自动引发这些事件。

当应用收到运动暂停事件时，它应停止收集数据，直到用户恢复动作并收到动作恢复事件为止。 应用不应暂停健身会话来响应运动暂停事件。

> [!IMPORTANT]
> 暂停动作和运动恢复事件仅支持 RunningWorkout 活动类型 (`HKWorkoutActivityType.Running`) 。

同样，可以通过重写的方法来处理这些事件 `DidGenerateEvent` `HKWorkoutSessionDelegate` ：

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  }
}

```

## <a name="ending-and-saving-the-workout-session"></a>结束并保存健身会话

当用户完成其健身后，应用将需要结束当前健身会话并将其保存到 HealthKit 数据库中。 保存到 HealthKit 的 Workouts 将自动显示在 "健身活动" 列表中。

新到 iOS 10，还包括用户 iPhone 上的 "健身活动列表" 列表。 即使 Apple Watch 不在附近，也会在手机上显示健身。

包含能源样品的 Workouts 会更新用户在活动应用程序中的移动环，因此第三方应用程序现在可为用户提供每日移动目标。

若要结束并保存健身会话，需执行以下步骤：

[![结束并保存健身会话示意图](workout-apps-images/workout05.png)](workout-apps-images/workout05.png#lightbox)

1. 首先，应用将需要结束测验会话。
2. 健身会话保存到 HealthKit。
3. 向保存的健身会话添加任何示例 (例如，能源烧制或距离) 。

### <a name="ending-the-session"></a>结束会话

若要结束健身会话，请调用 `EndWorkoutSession` `HKHealthStore` 传入的方法 `HKWorkoutSession` ：

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
  // End the current workout session
  HealthStore.EndWorkoutSession (WorkoutSession);
}
```

这会将设备传感器重置为其正常模式。 当 HealthKit 结束健身后，它将接收对的方法的回调 `DidChangeToState` `HKWorkoutSessionDelegate` ：

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
  // Take action based on the change in state
  switch (toState) {
  ...
  case HKWorkoutSessionState.Ended:
    StopObservingHealthKitSamples ();
    RaiseEnded ();
    break;
  }

}
```

### <a name="saving-the-session"></a>正在保存会话

应用结束测验会话后，需要创建健身 (`HKWorkout`) 并将其与) 到 HealthKit 数据存储 (的事件一起保存 (`HKHealthStore` ：

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
  // Build required workout quantities 
  var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
  var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

  // Create any required metadata
  var metadata = new NSMutableDictionary ();
  metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

  // Create workout
  var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                  WorkoutSession.StartDate, 
                                  NSDate.Now, 
                                  WorkoutEvents.ToArray (), 
                                  energyBurned, 
                                  distance, 
                                  metadata);

  // Save to HealthKit
  HealthStore.SaveObject (workout, (successful, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (successful) {

      }
    } else {
      // Report error
    }
  });

}
```

此代码会创建所需的能源量和健身距离作为对象的距离 `HKQuantity` 。 将创建定义健身的元数据的字典，并指定健身的位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

将创建一个新的 `HKWorkout` 对象，其 `HKWorkoutActivityType` `HKWorkoutSession` 起始日期和结束日期、开始日期和结束日期、 (从上述部分) 累积的事件列表、已刻录的能源和总距离和元数据字典。 此对象保存到运行状况存储，并处理任何错误。  

### <a name="adding-samples"></a>添加示例

当应用程序将一组示例保存到某一健身中时，HealthKit 会在示例和健身自身之间生成一个连接，以便应用可以在以后针对与给定健身关联的所有样本查询 HealthKit。 使用此信息，应用程序可以从健身数据生成图形，并将其绘制到健身时间线。

为了使应用程序参与活动应用的移动环，它必须包含已保存健身的能源样品。 此外，距离和能量的总和必须与应用与已保存健身关联的任何样本的总和匹配。

若要向保存的健身添加示例，请执行以下操作：

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...

private void SaveWorkoutSamples (HKWorkout workout)
{
  // Add samples to saved workout
  HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (success) {

      }
    } else {
      // Report error
    }
  });
}
```

或者，应用程序可以计算并创建较小的样本子集或一 (兆位样本，以跨越测验) 的整个范围，并将其与保存的健身相关联。

## <a name="workouts-and-ios-10"></a>Workouts 和 iOS 10

每个 watchOS 3 健身应用都有一个基于 iOS 10 的父子应用，而新到 iOS 10，则可以使用此 iOS 应用启动一个健身，将 Apple Watch 置于健身模式 (无需用户) 干预的情况，并在后台运行模式下运行 watchOS 应用 (参阅有关更多) 详细信息，请参阅上面[运行的后台](#about-background-running)

当 watchOS 应用程序正在运行时，它可以使用 WatchConnectivity 进行消息传递，并与父 iOS 应用通信。

请看一下此过程的工作原理：

[![iPhone 和 Apple Watch 通信关系图](workout-apps-images/workout06.png)](workout-apps-images/workout06.png#lightbox)

1. IPhone 应用会创建一个 `HKWorkoutConfiguration` 对象，并设置健身类型和位置。
2. `HKWorkoutConfiguration`对象将发送到 Apple Watch 版本的应用，如果尚未运行，则系统会启动该对象。
3. 使用在健身配置中传递的，watchOS 3 应用 () 启动新的健身会话 `HKWorkoutSession` 。

> [!IMPORTANT]
> 为了使父 iPhone 应用开始 Apple Watch 的健身，watchOS 3 应用程序必须启用后台功能。 有关更多详细信息，请参阅 [启用上面运行的后台](#enabling-background-running) 。

此过程与在 watchOS 3 应用中直接启动健身会话的过程非常相似。 在 iPhone 上，使用以下代码：

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
  // Can the app communicate with the watchOS version of the app?
  if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
      ActivityType = HKWorkoutActivityType.Running,
      LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Start watch app
    HealthStore.StartWatchApp (configuration, (success, error) => {
      // Handle any errors
      if (error == null) {
        // Was the save successful
        if (success) {
          ...
        }
      } else {
        // Report error
        ...
      }
    });
  }
}
```

此代码可确保安装 watchOS 版本的应用，并且 iPhone 版本可以首先连接到它：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

然后，它会 `HKWorkoutConfiguration` 照常创建，并使用的 `StartWatchApp` 方法将 `HKHealthStore` 其发送到 Apple Watch 并启动应用和健身会话。

在监视 OS 应用上，使用以下代码 `WKExtensionDelegate` ：

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

它使用 `HKWorkoutConfiguration` 并创建一个新的 `HKWorkoutSession` ，并附加自定义的实例 `HKWorkoutSessionDelegate` 。 将对用户的 HealthKit Health Store 启动健身会话。

## <a name="bringing-all-the-pieces-together"></a>将所有部分组合在一起

使用本文档中提供的所有信息，基于 watchOS 3 的健身应用及其基于 iOS 10 的父子应用可能包括以下部分：

1. **iOS 10 `ViewController.cs` **-处理监视连接会话的开始和 Apple Watch 上的健身。
2. **watchOS 3 `ExtensionDelegate.cs` **-处理健身应用的 watchOS 3 版本。
3. **watchOS 3 `OutdoorRunDelegate.cs` **- `HKWorkoutSessionDelegate` 用于处理健身事件的自定义。

> [!IMPORTANT]
> 以下各节中所示的代码仅包括实现在 watchOS 3 中提供给健身应用程序的新的增强功能所需的部分。 所有支持代码和显示和更新 UI 的代码都不包括在内，但可以通过以下其他 watchOS 文档轻松创建。<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

工作 `ViewController.cs` 应用的父 iOS 10 版本中的文件将包括以下代码：

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
  public partial class ViewController : UIViewController
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
    public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
    #endregion

    #region Constructors
    protected ViewController (IntPtr handle) : base (handle)
    {
      // Note: this .ctor should not contain any initialization logic.
    }
    #endregion

    #region Private Methods
    private void InitializeWatchConnectivity ()
    {
      // Is Watch Connectivity supported?
      if (!WCSession.IsSupported) {
        // No, abort
        return;
      }

      // Is the session already active?
      if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
        // No, start session
        ConnectivitySession.ActivateSession ();
      }
    }

    private void StartOutdoorRun ()
    {
      // Can the app communicate with the watchOS version of the app?
      if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
          ActivityType = HKWorkoutActivityType.Running,
          LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
          // Handle any errors
          if (error == null) {
            // Was the save successful
            if (success) {
              ...
            }
          } else {
            // Report error
            ...
          }
        });
      }
    }
    #endregion

    #region Override Methods
    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Start Watch Connectivity
      InitializeWatchConnectivity ();
    }
    #endregion
  }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

`ExtensionDelegate.cs`WatchOS 3 版本的健身应用中的文件将包括以下代码：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
    public OutdoorRunDelegate RunDelegate { get; set; }
    #endregion

    #region Constructors
    public ExtensionDelegate ()
    {
      
    }
    #endregion

    #region Private Methods
    private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
    {
      // Create workout session
      // Start workout session
      NSError error = null;
      var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

      // Successful?
      if (error != null) {
        // Report error to user and return
        return;
      }

      // Create workout session delegate and wire-up events
      RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

      RunDelegate.Failed += () => {
        // Handle the session failing
        ...
      };

      RunDelegate.Paused += () => {
        // Handle the session being paused
        ...
      };

      RunDelegate.Running += () => {
        // Handle the session running
        ...
      };

      RunDelegate.Ended += () => {
        // Handle the session ending
        ...
      };
      
      RunDelegate.ReachedMileGoal += (miles) => {
        // Handle the reaching a session goal
        ...
      };

      RunDelegate.HealthKitSamplesUpdated += () => {
        // Update UI as required
        ...
      };

      // Start session
      HealthStore.StartWorkoutSession (workoutSession);
    }

    private void StartOutdoorRun ()
    {
      // Create a workout configuration
      var workoutConfiguration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
      };

      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion

    #region Override Methods
    public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
    {
      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion
  }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

`OutdoorRunDelegate.cs`WatchOS 3 版本的健身应用中的文件将包括以下代码：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Private Variables
    private HKAnchoredObjectQuery QueryActiveEnergyBurned;
    #endregion

    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    public float MilesRun { get; set; }
    public double ActiveEnergyBurned { get; set;}
    public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
    public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;

    }
    #endregion

    #region Private Methods
    private void ObserveHealthKitSamples ()
    {
      // Get the starting date of the required samples
      var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

      // Get data from the local device
      var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
      var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

      // Assemble compound predicate
      var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

      // Get ActiveEnergyBurned
      QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
          // Yes, process all returned samples
          foreach (HKSample sample in addedObjects) {
            // Accumulate totals
            var quantitySample = sample as HKQuantitySample;
            ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

            // Save samples
            WorkoutSamples.Add (sample);
          }

          // Inform caller
          RaiseHealthKitSamplesUpdated ();
        }
      });

      // Start Query
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                            
    }

    private void StopObservingHealthKitSamples ()
    {
      // Stop query
      HealthStore.StopQuery (QueryActiveEnergyBurned);
    }

    private void ResumeObservingHealthkitSamples ()
    {
      // Resume current queries 
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
    }

    private void NotifyUserOfReachedMileGoal (float miles)
    {
      // Play haptic feedback
      WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

      // Raise event
      RaiseReachedMileGoal (miles);
    }

    private void SaveWorkoutSession ()
    {
      // Build required workout quantities
      var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
      var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

      // Create any required metadata
      var metadata = new NSMutableDictionary ();
      metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

      // Create workout
      var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                      WorkoutSession.StartDate, 
                                      NSDate.Now, 
                                      WorkoutEvents.ToArray (), 
                                      energyBurned, 
                                      distance, 
                                      metadata);

      // Save to HealthKit
      HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (successful) {
            // Add samples to workout
            SaveWorkoutSamples (workout);
          }
        } else {
          // Report error
          ...
        }
      });

    }

    private void SaveWorkoutSamples (HKWorkout workout)
    {
      // Add samples to saved workout
      HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (success) {
            ...
          }
        } else {
          // Report error
          ...
        }
      });
    }
    #endregion

    #region Public Methods
    public void PauseWorkout ()
    {
      // Pause the current workout
      HealthStore.PauseWorkoutSession (WorkoutSession);
    }

    public void ResumeWorkout ()
    {
      // Pause the current workout
      HealthStore.ResumeWorkoutSession (WorkoutSession);
    }

    public void ReachedNextMile ()
    {
      // Create and save marker event
      var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
      WorkoutEvents.Add (markerEvent);

      // Notify user
      NotifyUserOfReachedMileGoal (++MilesRun);
    }

    public void EndOutdoorRun ()
    {
      // End the current workout session
      HealthStore.EndWorkoutSession (WorkoutSession);
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        StopObservingHealthKitSamples ();
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        if (fromState == HKWorkoutSessionState.Paused) {
          ResumeObservingHealthkitSamples ();
        } else {
          ObserveHealthKitSamples ();
        }
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        SaveWorkoutSession ();
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);

      // Save HealthKit generated event
      WorkoutEvents.Add (@event);

      // Take action based on the type of event
      switch (@event.Type) {
      case HKWorkoutEventType.Lap:
        ...
        break;
      case HKWorkoutEventType.Marker:
        ...
        break;
      case HKWorkoutEventType.MotionPaused:
        ...
        break;
      case HKWorkoutEventType.MotionResumed:
        ...
        break;
      case HKWorkoutEventType.Pause:
        ...
        break;
      case HKWorkoutEventType.Resume:
        ...
        break;
      }
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();
    public delegate void OutdoorRunMileGoalDelegate (float miles);

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }

    public event OutdoorRunMileGoalDelegate ReachedMileGoal;
    internal void RaiseReachedMileGoal (float miles)
    {
      if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
    }

    public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
    internal void RaiseHealthKitSamplesUpdated ()
    {
      if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
    }
    #endregion
  }
}
```

## <a name="best-practices"></a>最佳实践

Apple 建议在 watchOS 3 和 iOS 10 中设计和实现健身应用时采用以下最佳做法：

- 确保 watchOS 3 健身应用仍可正常运行，即使无法连接到 iPhone 和应用的 iOS 10 版本也是如此。
- 当 GPS 不可用时，请使用 HealthKit 距离，因为它能够生成没有 GPS 的距离示例。
- 允许用户从 Apple Watch 或 iPhone 开始测验。
- 允许应用显示来自其他 (源的 workouts，例如，其他第三方应用) 其历史数据视图中。
- 确保应用不会在历史数据中显示已删除的 workouts。

## <a name="summary"></a>总结

本文介绍了 Apple 在 watchOS 3 中对健身应用的增强功能，以及如何在 Xamarin 中实现这些功能。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [HealthKit 简介](~/ios/platform/healthkit.md)