---
title: IOS 11 中的 SiriKit 更新
description: 本文档介绍如何使用 iOS 11 中的 SiriKit。 特别是，它会检查如何使用任务和注释，以及如何为应用程序提供备用名称。
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/07/2017
ms.openlocfilehash: 7bc102069d673b9459c863282b0423952c8fa59d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437320"
---
# <a name="sirikit-updates-in-ios-11"></a>IOS 11 中的 SiriKit 更新

IOS 10 中引入了 SiriKit，其中包含多个服务域 (包括 workouts、请求预订和) 进行调用。 有关 SiriKit 的概念以及如何在应用程序中实现 SiriKit，请参阅 [SiriKit 部分](~/ios/platform/sirikit/index.md) 。

![Siri 任务列表演示](sirikit-images/sirikit.png)

IOS 11 中的 SiriKit 添加了以下新的和更新的目的域：

- [**列表和备注**](#listsnotes) –新增！ 为应用提供一个 API，用于处理任务和说明。
- **Visual 代码** -New！ Siri 可以显示 QR 码来共享联系信息或参与支付交易。
- **支付** –为支付交互增加了搜索和传输意向。
- "向**预约预订**"-添加了 "取消" 和 "反馈"。

其他新功能包括：

- [**备用应用名称**](#alternativenames) –提供可帮助客户通过提供备用名称/发音来帮助客户确定 Siri 目标的别名。
- **启动 Workouts** –提供在后台启动健身的能力。

下面介绍其中的一些功能。 有关其他信息，请参阅 [Apple 的 SiriKit 文档](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes"></a>

## <a name="lists-and-notes"></a>列表和注释

新的列表和便笺域提供一个 API，用于应用程序通过 Siri 语音请求来处理任务和说明。

**任务**

- 具有 "标题" 和 "完成" 状态。
- 选择性地包含截止时间和位置。

**备注**

- 具有 "标题" 和 "内容" 字段。

任务和说明可以组织成组。 本部分的其余部分介绍如何使用 [TasksNotes SiriKit 示例](/samples/xamarin/ios-samples/ios11-sirikitsample)实现此带有 SiriKit 的新域。

### <a name="how-to-process-a-sirikit-request"></a>如何处理 SiriKit 请求

按照以下步骤处理 SiriKit 请求：

1. **解决** –验证参数并请求用户 (的详细信息（如果需要）) 。
2. **确认** –最终验证和验证是否可以处理该请求。
3. **Handle** –执行操作 (更新数据或) 执行网络操作。

前两个步骤是可选的 (，尽管鼓励) ，最后一步是必需的。
[SiriKit 部分](~/ios/platform/sirikit/index.md)提供了更详细的说明。

### <a name="resolve-and-confirm-methods"></a>解析和确认方法

通过这些可选方法，你的代码可以执行验证、选择默认值或请求用户的其他信息。

例如，对于 `IINCreateTaskListIntent` 接口，所需的方法为 `HandleCreateTaskList` 。 有四种可选方法可以更好地控制 Siri 交互：

- `ResolveTitle` –验证标题，将默认标题设置 (如果适当) ，或通知数据不是必需的。
- `ResolveTaskTitles` –验证用户所说的任务列表。
- `ResolveGroupName` –验证组名称、选择默认组或通知数据不是必需的。
- `ConfirmCreateTaskList` –验证你的代码是否可以执行所请求的操作，但不会执行该操作 (仅 `Handle*` 应) 方法修改数据。

### <a name="handle-the-intent"></a>处理意向

在列表和 notes 域中有六个意向，三个用于任务，三个用于注释。
处理这些方法必须实现的方法如下：

- 对于任务：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 对于备注：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每个方法都传递了特定意向类型，该类型包含 Siri 从用户的 (请求中分析的所有信息，并可能在 `Resolve*` 和方法) 中进行了更新 `Confirm*` 。。
您的应用程序必须分析提供的数据，然后执行某些操作来存储或处理数据，并返回一个 Siri，并向用户显示结果。

### <a name="response-codes"></a>响应代码

必需的 `Handle*` 和可选的 `Confirm*` 方法通过对其传递到完成处理程序的对象设置值来指示响应代码。 响应来自 `INCreateTaskListIntentResponseCode` 枚举：

- `Ready` –在确认阶段 (为 ie 时返回。从 `Confirm*` 方法，而不是从 `Handle*` 方法) 。
- `InProgress` –用于长时间运行的任务， (如网络/服务器操作) 。
- `Success` –使用成功操作的详细信息 (仅从 `Handle*`) 方法进行响应。
- `Failure` -表示发生了错误，无法完成操作。
- `RequiringAppLaunch` –无法通过意向进行处理，但可能会在应用程序中进行操作。
- `Unspecified` –不要使用：向用户显示错误消息。

在 Apple 的 [SiriKit 列表和说明文档](https://developer.apple.com/documentation/sirikit/lists_and_notes)中了解有关这些方法和响应的详细信息。

### <a name="implementing-lists-and-notes"></a>实现列表和注释

[TasksNotes SiriKit 示例](/samples/xamarin/ios-samples/ios11-sirikitsample)是使用以下步骤创建的，以将 SiriKit 支持添加到空白 iOS 应用。

首先，若要添加 SiriKit 支持，请对 iOS 应用执行以下步骤：

1. **Info.plist**中的**SiriKit** 。
2. 将 **隐私– Siri 使用描述** 密钥添加到 **info.plist**，并将消息添加到客户。
3. `INPreferences.RequestSiriAuthorization`在应用中调用方法，以提示用户允许 Siri 交互。
4. 将 SiriKit 添加到开发人员门户中的应用 ID，然后重新创建预配配置文件以包括新的权利。

然后，将新的扩展项目添加到应用以处理 Siri 请求：

1. 右键单击解决方案，然后选择 " **添加" > "添加新项目 ...**"。
2. 选择 **iOS > 扩展 > 意向扩展** 模板。
3. 将添加两个新项目：意向和 IntentUI。 自定义 UI 是可选的，因此，该示例仅在 **意图** 项目中包括代码。

扩展项目是将处理所有 SiriKit 请求的位置。 作为单独的扩展，它不会自动与主应用通信，这通常是通过使用应用组实现共享文件存储来解决的。

#### <a name="configure-the-intenthandler"></a>配置 IntentHandler

`IntentHandler`类是 Siri 请求的入口点–每个意向都传递给 `GetHandler` 方法，该方法返回可处理请求的对象。

下面的代码演示了一个简单的实现：

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

类必须从继承 `INExtension` ，并且此示例将处理列表和说明，它还实现 `IINNotebookDomainHandling` 。

> [!NOTE]
>
> - .NET 中有一种约定，其中的接口以大写作为前缀 `I` ，Xamarin 在从 IOS SDK 绑定协议时要遵守此约定。
> - Xamarin 还保留来自 iOS 的类型名称，并且 Apple 使用类型名称中的前两个字符来反映类型所属的框架。
> - 对于 `Intents` 框架，类型以 `IN*` (例如为前缀。 `INExtension`) ，但这些 _不_ 是接口。
> - 它还遵循 (成为 c # 中的接口的协议 ) 最终会出现两个 `I` ，如 `IINAddTasksIntentHandling` 。

#### <a name="handling-intents"></a>处理意向

每个意向 (添加任务、设置任务属性等) 在类似于下面所示的方法中实现。 方法应执行三个主要功能：

1. **处理意向** – Siri 分析的数据在 `intent` 特定于意向类型的对象中可用。 您的应用程序可能已使用可选方法验证了该数据 `Resolve*` 。
2. **验证和更新数据存储** –使用应用组将数据保存到 filesystem (，以便主 iOS 应用还可以) 或通过网络请求访问它。
3. **提供响应** –使用 `completion` 处理程序将响应发送回 Siri，以便对用户进行读取/显示：

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

请注意， `null` 作为第二个参数传递到响应–这是用户活动参数，如果未提供，则将使用默认值。
只要你的 iOS 应用通过 info.plist 中的密钥支持，你就可以设置自定义活动类型 `NSUserActivityTypes` 。 **Info.plist** 然后，你可以在打开应用时处理此情况，并执行特定操作 (例如打开相关视图控制器，以及从 Siri 操作) 加载数据。

该示例还 hardcodes 了 `Success` 结果，但在实际情况下，应添加正确的错误报告。

### <a name="test-phrases"></a>测试短语

以下测试短语应在示例应用中运行：

- "在 TasksNotes 中使用苹果、bananas 和 pears 创建杂货列表
- "Add task WWDC in TasksNotes"
- "将 task WWDC 添加到 TasksNotes 中的训练列表"
- "在 TasksNotes 中标记参加 WWDC
- "在 TasksNotes 时提醒我购买 iphone"
- "将 iPhone 购买为在 TasksNotes 中完成"
- "提醒我在家中早晨8点"

![创建新列表示例](sirikit-images/createtasklist-sml.png) ![将任务设置为完整示例](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 模拟器支持对 Siri (进行测试，这与早期版本) 不同。
>
> 如果在实际设备上进行测试，请不要忘记配置应用 ID 和预配配置文件以提供 SiriKit 支持。

<a name="alternativenames"></a>

## <a name="alternative-names"></a>备用名称

这一新的 iOS 11 功能意味着您可以为应用程序配置备用名称，以帮助用户在 Siri 中正确地触发它。 将以下项添加到 iOS 应用项目的 **info.plist** 文件中：

![显示替代应用名称键和值的 info.plist](sirikit-images/alternative-names.png)

设置备用应用程序名称后，以下短语也适用于实际命名为 **TasksNotes**) 的示例应用 (：

- "在 _MonkeyNotes_中使用苹果、bananas 和 pears 创建杂货列表
- "Add task WWDC in _MonkeyTodo_"

## <a name="troubleshooting"></a>疑难解答

运行示例或将 SiriKit 添加到你自己的应用程序时可能遇到的一些错误：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_引发了目标-C 异常。 名称： NSInternalInconsistencyException 原因：从应用中使用类 <INPreferences： 0x60400082ff00> 需要权限 siri。是否在 Xcode 项目中启用了 Siri 功能？_

- **Info.plist**中的 SiriKit 为勾选。
- **Info.plist** 在项目选项中配置， **> 生成 > iOS 捆绑签名**。

  [![显示正确设置了权利的项目选项](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- 用于设备部署的 () 应用 ID 已启用 SiriKit 并已下载预配配置文件。

## <a name="related-links"></a>相关链接

- [SiriKit (Apple) ](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 示例](/samples/xamarin/ios-samples/ios11-sirikitsample)
- [SiriKit 中的新增功能 (WWDC)  (视频) ](https://developer.apple.com/videos/play/wwdc2017/214/)