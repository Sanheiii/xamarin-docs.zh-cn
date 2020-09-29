---
title: Xamarin 中的 CallKit
description: 本文介绍了 Apple 在 iOS 10 中发布的新 CallKit API，以及如何在 Xamarin iOS 应用程序中实现它。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 0dd7736d37f6db17ad794258d9dff852b2745053
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431847"
---
# <a name="callkit-in-xamarinios"></a>Xamarin 中的 CallKit

IOS 10 中的新 CallKit API 提供了一种方法，让 VOIP 应用与 iPhone UI 集成，并为最终用户提供熟悉的界面和体验。 使用此 API，用户可以从 iOS 设备的锁屏界面查看并与 VOIP 呼叫交互，并使用 Phone 应用的 **"收藏夹" 和 "** **最近** " 视图管理联系人。

## <a name="about-callkit"></a>关于 CallKit

根据 Apple，CallKit 是一个新的框架，它将 IP (VOIP) 应用提升为第三方语音，以将其提升到 iOS 10 上的第一方体验。 CallKit API 允许 VOIP 应用与 iPhone UI 集成，并为最终用户提供熟悉的界面和体验。 与内置的手机应用程序一样，用户可以从 iOS 设备的锁屏界面查看并与 VOIP 呼叫交互，并使用 Phone 应用的 **"收藏夹** " 和 " **最近** " 视图管理联系人。

此外，CallKit API 提供了创建应用扩展的功能，可以将电话号码与名称 (调用方 ID) ，或者告诉系统应阻止某个数字 (调用阻止) 。

### <a name="the-existing-voip-app-experience"></a>现有的 VOIP 应用体验

在讨论新的 CallKit API 及其功能之前，请查看使用 iOS 9 (的第三方 VOIP 应用的当前用户体验，并使用名为 MonkeyCall 的虚构 VOIP 应用) 更少的用户体验。 MonkeyCall 是一个简单的应用，允许用户使用现有的 iOS Api 发送和接收 VOIP 呼叫。

目前，如果用户正在 MonkeyCall 上接收传入呼叫，并且其 iPhone 处于锁定状态，则锁定屏幕上收到的通知与任何其他类型的 (通知（例如，) 中的通知）不区分开来。

如果用户想要接听呼叫，他们必须滑动 MonkeyCall 通知以打开应用程序，并输入其密码 (或用户 Touch ID) 在电话可以接受呼叫并开始对话之前，对其进行解锁。

如果电话未锁定，则体验同样繁琐。 同样，传入的 MonkeyCall 调用将显示为从屏幕顶部滑入的标准通知横幅。 由于通知是暂时的，因此用户可以轻松地将其丢失，迫使用户打开通知中心，找到要回答的特定通知，然后手动调用或查找并启动 MonkeyCall 应用。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 应用体验

通过在 MonkeyCall 应用程序中实现新的 CallKit Api，可以在 iOS 10 中大大提高用户使用传入 VOIP 呼叫的经验。 如果用户的电话被锁定，请在接收 VOIP 呼叫的用户的示例中进行。 通过实现 CallKit，调用将显示在 iPhone 的锁定屏幕上，就像从内置手机应用接收呼叫时一样，它具有全屏、本机 UI 和标准的 "轻扫-应答" 功能。

同样，如果在接收到 MonkeyCall VOIP 呼叫时，iPhone 处于解锁状态，则将显示内置手机应用的相同全屏、本机 UI 和标准的 "轻扫到" 和 "点击到拒绝" 功能，并且 MonkeyCall 可以选择播放自定义铃声。

CallKit 为 MonkeyCall 提供附加功能，允许其 VOIP 调用与其他类型的调用交互，在内置的最近和收藏夹列表中显示，以使用内置的 "不干扰" 和 "阻止" 功能，从 Siri 启动 MonkeyCall 调用，并为用户提供向联系人应用中的人员分配 MonkeyCall 呼叫的能力。

以下部分将详细介绍 CallKit 体系结构、传入和传出调用流和 CallKit API。

## <a name="the-callkit-architecture"></a>CallKit 体系结构

在 iOS 10 中，Apple 在所有系统服务中采用了 CallKit，以便在 CarPlay 上进行调用，例如，通过 CallKit 对系统 UI 知道。 在下面给出的示例中，由于 MonkeyCall 采用了 CallKit，因此系统会以与这些内置系统服务相同的方式来识别系统，并获得所有相同的功能：

[![CallKit 服务堆栈](callkit-images/callkit01.png)](callkit-images/callkit01.png#lightbox)

请详细了解上图中的 MonkeyCall 应用。 该应用包含其所有代码，以与其自己的网络通信，并包含其自己的用户界面。 它链接在 CallKit 中以与系统通信：

[![MonkeyCall 应用程序体系结构](callkit-images/callkit02.png)](callkit-images/callkit02.png#lightbox)

应用使用的 CallKit 中有两个主要接口：

- `CXProvider` -这允许 MonkeyCall 应用向系统通知可能发生的任何带外通知系统。
- `CXCallController` -允许 MonkeyCall 应用向系统通知本地用户操作。

### <a name="the-cxprovider"></a>CXProvider

如上所述， `CXProvider` 允许应用向系统通知可能发生的任何带外通知系统。 这些是由于本地用户操作而不会发生的通知，但由于传入呼叫等外部事件而发生。

应用应为以下各项使用 `CXProvider` ：

- 报告对系统的传入调用。
- 报告传出调用已连接到系统。
- 报告远程用户正在调用系统。

当应用程序想要与系统通信时，它会使用 `CXCallUpdate` 类，并且当系统需要与应用程序进行通信时，它将使用 `CXAction` 类：

[![通过 CXProvider 与系统进行通信](callkit-images/callkit03.png)](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`允许应用向系统通知本地用户操作，如启动 VOIP 呼叫的用户。 通过实现 `CXCallController` 应用程序，可以使用系统中的其他类型的调用来相互作用。 例如，如果已有正在进行的活动电话服务呼叫， `CXCallController` 则可允许 VOIP 应用将该呼叫置于保持状态并启动或应答 VOIP 呼叫。

应用应为以下各项使用 `CXCallController` ：

- 当用户已启动对系统的传出调用时报告。
- 当用户回答对系统的传入调用时报告。
- 当用户结束对系统的调用时报告。

当应用程序想要向系统传达本地用户操作时，它将使用 `CXTransaction` 类：

[![使用 CXCallController 向系统报告](callkit-images/callkit04.png)](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>实现 CallKit

以下部分将介绍如何在 Xamarin iOS VOIP 应用中实现 CallKit。 例如，本文档将使用虚构的 MonkeyCall VOIP 应用中的代码。 此处提供的代码代表多个支持类，以下各节将详细介绍 CallKit 特定部分。

### <a name="the-activecall-class"></a>ActiveCall 类

`ActiveCall`MonkeyCall 应用使用类来保存有关当前活动的 VOIP 呼叫的所有信息，如下所示：

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` 保存几个属性，这些属性定义调用的状态和两个可以在调用状态发生更改时引发的事件。 由于这只是一个示例，因此可以使用三种方法来模拟开始、应答和结束调用。

### <a name="the-startcallrequest-class"></a>StartCallRequest 类

`StartCallRequest`静态类提供了几种在使用传出调用时将使用的帮助器方法：

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

`CallHandleFromURL`和 `CallHandleFromActivity` 类在 AppDelegate 中用于获取在传出调用中调用的人员的联系句柄。 有关详细信息，请参阅下面的 [处理传出呼叫](#handling-outgoing-calls) 部分。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 类

`ActiveCallManager`类处理 MonkeyCall 应用中的所有打开的调用。

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

同样，由于这只是一个模拟，因此 `ActiveCallManager` 只维护对象的集合， `ActiveCall` 并且有一个例程用于按其属性查找给定的调用 `UUID` 。 它还包括启动、结束和更改传出调用的保持状态的方法。 有关详细信息，请参阅下面的 [处理传出呼叫](#handling-outgoing-calls) 部分。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 类

如上所述， `CXProvider` 提供应用与系统之间的双向通信，用于带外通知。 开发人员需要提供一个自定义 `CXProviderDelegate` ，并将其附加到，以便 `CXProvider` 应用程序处理带外 CallKit 事件。 MonkeyCall 使用以下 `CXProviderDelegate` 内容：

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

创建此委托的实例后，它将被传递 `ActiveCallManager` 到它将用于处理任何调用活动。 接下来，它定义将响应的 () 的句柄类型 `CXHandleType` `CXProvider` ：

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

并且，它将获取正在进行调用时将应用于应用图标的模板映像：

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

这些值捆绑到 `CXProviderConfiguration` 将用于配置的 `CXProvider` ：

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

然后，委托将使用这些配置创建一个新的 `CXProvider` ，并将其自身附加到其中：

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

当使用 CallKit 时，应用程序将不再创建和处理自己的音频会话，而是需要配置和使用系统为其创建和处理的音频会话。

如果这是实际应用，则 `DidActivateAudioSession` 使用该方法通过系统提供的预配置来启动调用 `AVAudioSession` ：

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它还会使用 `DidDeactivateAudioSession` 方法来完成与系统提供的音频会话的连接：

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

后续部分将详细介绍代码的其余部分。

### <a name="the-appdelegate-class"></a>AppDelegate 类

MonkeyCall 使用 AppDelegate 来保存 `ActiveCallManager` 将在整个应用程序中使用的和的实例 `CXProviderDelegate` ：

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

`OpenUrl` `ContinueUserActivity` 当应用程序正在处理传出调用时，使用和重写方法。 有关详细信息，请参阅下面的 [处理传出呼叫](#handling-outgoing-calls) 部分。

## <a name="handling-incoming-calls"></a>处理传入调用

在典型的传入调用工作流（例如）中，传入的 VOIP 调用可以通过几种状态和过程：

- 通知用户 (和系统) 存在传入呼叫。
- 当用户想要接听呼叫，并向其他用户初始化呼叫时接收通知。
- 当用户想要结束当前呼叫时，通知系统和通信网络。

以下部分将详细介绍应用如何使用 CallKit 来处理传入调用工作流，并使用 MonkeyCall VOIP 应用作为示例。

### <a name="informing-user-of-incoming-call"></a>通知用户传入呼叫

当远程用户启动了与本地用户的 VOIP 对话时，将发生以下情况：

[![远程用户已开始 VOIP 对话](callkit-images/callkit05.png)](callkit-images/callkit05.png#lightbox)

1. 此应用从其通信网络获取一个传入 VOIP 调用的通知。
2. 应用使用将 `CXProvider` 发送 `CXCallUpdate` 到系统，向系统通知调用。
3. 系统使用 CallKit 发布对系统 UI、系统服务和任何其他 VOIP 应用的调用。

例如，在中 `CXProviderDelegate` ：

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

此代码将创建一个新的 `CXCallUpdate` 实例，并向其附加一个用于标识调用方的句柄。 接下来，它使用 `ReportNewIncomingCall` 类的方法 `CXProvider` 向系统通知调用。 如果成功，则调用将添加到活动调用的应用集合中（如果不是这样），则需要向用户报告错误。

### <a name="user-answering-incoming-call"></a>用户应答传入呼叫

如果用户想要应答传入的 VOIP 呼叫，则会发生以下情况：

[![用户应答传入的 VOIP 呼叫](callkit-images/callkit06.png)](callkit-images/callkit06.png#lightbox)

1. 系统 UI 通知系统用户要应答 VOIP 呼叫。
2. 系统 `CXAnswerCallAction` 会向应用程序发送 `CXProvider` 通知。
3. 应用通知其通信网络用户正在应答呼叫，并且 VOIP 呼叫照常继续。

例如，在中 `CXProviderDelegate` ：

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此代码首先搜索其活动调用列表中的给定调用。 如果找不到该调用，则系统将通知系统并退出方法。 如果找到此方法，则 `AnswerCall` `ActiveCall` 调用类的方法启动调用，如果该方法成功或失败，系统将为其信息。

### <a name="user-ending-incoming-call"></a>用户正在结束传入呼叫

如果用户希望在应用的 UI 中终止调用，则会发生以下情况：

[![用户在应用的 UI 中终止调用](callkit-images/callkit07.png)](callkit-images/callkit07.png#lightbox)

1. 应用程序创建将 `CXEndCallAction` 捆绑到 `CXTransaction` 发送到系统的，以通知其调用正在结束。
2. 系统验证 End Call 意向，并 `CXEndCallAction` 通过将返回到应用程序 `CXProvider` 。
3. 然后，应用程序通知其通信网络呼叫正在结束。

例如，在中 `CXProviderDelegate` ：

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此代码首先搜索其活动调用列表中的给定调用。 如果找不到该调用，则系统将通知系统并退出方法。 如果找到此方法，则 `EndCall` `ActiveCall` 调用类的方法以结束调用，如果成功，则系统为信息。 如果成功，则从活动调用的集合中删除该调用。

## <a name="managing-multiple-calls"></a>管理多个调用

大多数 VOIP 应用可以一次处理多个调用。 例如，如果当前有活动的 VOIP 呼叫，并且应用收到了新的传入呼叫通知，则用户可以在第一次调用时暂停或挂断以回答第二台呼叫。

在上述情况下，系统会将发送 `CXTransaction` 到应用，该应用将包含多个操作的列表 (例如 `CXEndCallAction` 和 `CXAnswerCallAction`) 。 所有这些操作都需要单独完成，以便系统可以相应地更新 UI。

## <a name="handling-outgoing-calls"></a>处理传出调用

例如，如果用户在电话应用程序中从最近列表中点击某个条目 () 例如，来自属于该应用的调用，则系统会将其发送到 _开始呼叫意向_ ：

[![接收开始呼叫意向](callkit-images/callkit08.png)](callkit-images/callkit08.png#lightbox)

1. 此应用将基于从系统收到的开始呼叫意向创建 " _启动调用" 操作_ 。
2. 应用程序将使用 `CXCallController` 从系统请求启动调用操作。
3. 如果系统接受操作，它将通过委托返回到应用 `XCProvider` 。
4. 应用程序通过其通信网络启动传出呼叫。

有关意向的详细信息，请参阅我们的 [意图和意向 UI 扩展](~/ios/platform/sirikit/understanding-sirikit.md) 文档。

### <a name="the-outgoing-call-lifecycle"></a>传出呼叫生命周期

使用 CallKit 和传出呼叫时，应用需要通知系统以下生命周期事件：

1. **正在启动** -通知系统传出的调用即将开始。
2. **已启动** -通知系统已开始传出呼叫。
3. **连接** -通知系统传出呼叫正在连接。
4. **已连接** -通知传出呼叫已连接，并且双方都可以立即交谈。

例如，以下代码将启动传出呼叫：

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

它将创建一个 `CXHandle` ，并使用它来配置将 `CXStartCallAction` 绑定到 `CXTransaction` 使用类的方法发送到系统的 `RequestTransaction` `CXCallController` 。 通过调用 `RequestTransaction` 方法，系统可以在新调用开始之前，将任何现有调用置于现有状态，无论源 (Phone 应用 FaceTime、VOIP 等 ) 。

启动传出 VOIP 呼叫的请求可以来自多个不同的源，例如 Siri、Contact 应用中 (的联系人卡中的条目) 或手机应用) 中的最近列表 (的条目。 在这些情况下，将在中向应用发送开始呼叫意向 `NSUserActivity` ，AppDelegate 将需要对其进行处理：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

下面 `CallHandleFromActivity` 是帮助器类的方法，用于 `StartCallRequest` 获取正在被调用的人员的句柄 (参见上面) [的 StartCallRequest 类](#the-startcallrequest-class) 。

`PerformStartCallAction` [ProviderDelegate 类](#the-providerdelegate-class)的方法用于最终启动实际的传出呼叫并通知系统其生命周期：

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

它创建类的实例， `ActiveCall` (保存正在进行的调用的相关信息) 并使用正在调用的人员进行填充。 `StartingConnectionChanged`和 `ConnectedChanged` 事件用于监视和报告传出呼叫生命周期。 调用已启动，系统已通知你已完成操作。

### <a name="ending-an-outgoing-call"></a>结束传出呼叫

当用户使用传出呼叫结束并希望结束时，可以使用以下代码：

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

如果使用 `CXEndCallAction` 调用的 UUID 创建，则使用类的方法将其绑定到 `CXTransaction` 发送到系统的 `RequestTransaction` `CXCallController` 。

## <a name="additional-callkit-details"></a>其他 CallKit 详细信息

本部分将介绍开发人员在使用 CallKit 时需要考虑的一些附加详细信息，如：

- 提供程序配置
- 操作错误
- 系统限制
- VOIP 音频

### <a name="provider-configuration"></a>提供程序配置

当使用 CallKit 时，提供程序配置允许 iOS 10 VOIP 应用自定义在本机 (调用 UI 内部) 的用户体验。

应用可以进行以下类型的自定义：

- 显示本地化的名称。
- 启用视频呼叫支持。
- 通过显示其自己的模板图像图标，自定义拨入 UI 中的按钮。 与自定义按钮的用户交互将直接发送到要处理的应用。

### <a name="action-errors"></a>操作错误

使用 CallKit 的 iOS 10 VOIP 应用需要处理正常失败的操作，并使用户始终获得操作状态通知。

请考虑以下示例：

1. 应用已接收到开始呼叫操作，并已开始使用其通信网络初始化新的 VOIP 呼叫。
2. 由于网络通信功能受限或不存在，此连接将失败。
3. 应用程序 *必须* 将 **失败** 消息发送回 "开始呼叫" 操作 (`Action.Fail()`) ，以通知系统发生故障。
4. 这允许系统通知用户呼叫的状态。 例如，显示呼叫失败 UI。

此外，iOS 10 VOIP 应用需要响应 _超时错误_ ，在给定的时间内无法处理预期的操作时可能会发生此错误。 CallKit 提供的每个操作类型都具有与之关联的最大超时值。 这些超时值可确保用户请求的任何 CallKit 操作都是以响应方式进行处理的，因此也保持操作系统的流畅性和响应能力。

提供程序委托 (了几种方法 `CXProviderDelegate`) ，应重写该方法以合理处理此超时情况。

### <a name="system-restrictions"></a>系统限制

根据运行 iOS 10 VOIP 应用的 iOS 设备的当前状态，可能会强制实施某些系统限制。

例如，在以下情况下，系统可能会限制传入的 VOIP 呼叫：

1. 调用的人员位于用户的 "阻止的呼叫方" 列表中。
2. 用户的 iOS 设备处于 "请勿打扰" 模式。

如果 VOIP 调用受到任何这些情况的限制，请使用以下代码对其进行处理：

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>VOIP 音频

CallKit 提供了几个优点用于处理在实时 VOIP 呼叫期间 iOS 10 VOIP 应用需要的音频资源。 最大的好处之一是应用的音频会话在 iOS 10 中运行时具有提升的优先级。 此优先级与内置的手机和 FaceTime 应用相同，并且此增强的优先级级别将阻止其他正在运行的应用中断 VOIP 应用的音频会话。

此外，CallKit 还可以访问其他音频路由提示，这些提示可以提高性能，并根据用户首选项和设备状态将 VOIP 音频智能地路由到特定的输出设备。 例如，基于连接的设备（如蓝牙耳机）、实时 CarPlay 连接或辅助功能设置。

在使用 CallKit 的典型 VOIP 呼叫的生命周期内，应用将需要配置 CallKit 将提供的音频流。 请看下面的示例：

[![开始调用操作序列](callkit-images/callkit09.png)](callkit-images/callkit09.png#lightbox)

1. 应用会接收开始呼叫操作，以应答传入呼叫。
2. 在应用程序完成此操作之前，它将为其提供需要的配置 `AVAudioSession` 。
3. 应用程序将通知系统操作已完成。
4. 在调用连接之前，CallKit 提供高优先级的 `AVAudioSession` 匹配应用程序请求的配置。 将通过其的方法通知应用 `DidActivateAudioSession` `CXProviderDelegate` 。

## <a name="working-with-call-directory-extensions"></a>使用调用目录扩展

当使用 CallKit 时， _呼叫目录扩展_ 提供了一种方法，可在 iOS 设备上的联系人应用中添加被阻止的电话号码，并识别特定于给定 VOIP 应用的数字。

### <a name="implementing-a-call-directory-extension"></a>实现调用目录扩展

若要在 Xamarin iOS 应用中实现调用目录扩展，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开应用的解决方案。
2. 在**解决方案资源管理器**中右键单击解决方案名称，然后选择 "**添加**" "  >  **添加新项目**"。
3. 选择 " **iOS**  >  **扩展**  >  " "**调用目录扩展**"，然后单击 "**下一步**" 按钮：

    [![创建新的调用目录扩展](callkit-images/calldir01.png)](callkit-images/calldir01.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 " **下一步** " 按钮：

    [![输入扩展的名称](callkit-images/calldir02.png)](callkit-images/calldir02.png#lightbox)
5. 如果需要，请调整 **项目名称** 和/或 **解决方案名称** ，并单击 " **创建** " 按钮：

    [![创建项目](callkit-images/calldir03.png)](callkit-images/calldir03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开应用的解决方案。
2. 在**解决方案资源管理器**中右键单击解决方案名称，然后选择 "**添加**" "  >  **添加新项目**"。
3. 选择 " **iOS**  >  **扩展**  >  " "**调用目录扩展**"，然后单击 "**下一步**" 按钮：

    [![创建新的调用目录扩展](callkit-images/calldir01w.png)](callkit-images/calldir01.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 **"确定"** 按钮

-----

这会将 `CallDirectoryHandler.cs` 类添加到项目中，如下所示：

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

`BeginRequest`需要修改调用目录处理程序中的方法，以提供所需的功能。 在上述示例中，它会尝试在 VOIP 应用的 "联系人" 数据库中设置已阻止的和可用的编号列表。 如果任何一个请求由于任何原因而失败，请创建一个 `NSError` 来描述失败并向其传递 `CancelRequest` 类的方法 `CXCallDirectoryExtensionContext` 。

若要设置被阻止的数字，请使用 `AddBlockingEntry` 类的方法 `CXCallDirectoryExtensionContext` 。 提供给方法的数字 _必须_ 按数值升序排列。 为获得最佳性能和内存使用量（如果有多个电话号码），请考虑仅在给定时间加载数字子集，并使用 autorelease pool (s) 释放在每个加载的编号批次中分配的对象。

若要通知与 VOIP 应用已知联系号码的联系应用，请使用类的 `AddIdentificationEntry` 方法， `CXCallDirectoryExtensionContext` 并同时提供数字和标识标签。 同样，提供给方法的数字 _必须_ 按数值升序排列。 为获得最佳性能和内存使用量（如果有多个电话号码），请考虑仅在给定时间加载数字子集，并使用 autorelease pool (s) 释放在每个加载的编号批次中分配的对象。

## <a name="summary"></a>总结

本文介绍了 Apple 在 iOS 10 中发布的新 CallKit API，以及如何在 Xamarin iOS 应用程序中实现它。 它已展示了 CallKit 如何允许应用集成到 iOS 系统，以及如何通过内置 (应用（例如电话) ）提供功能奇偶校验，以及如何通过 Siri 交互和通过联系人应用来提高应用程序在各个位置（如锁定和主屏幕）的可见性。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)