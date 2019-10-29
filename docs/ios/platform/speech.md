---
title: Xamarin 中的语音识别
description: 本文介绍了新的语音 API，并演示了如何在 Xamarin iOS 应用程序中实现此功能，以支持连续语音识别和转录语音（从实时或录制的音频流）到文本。
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 015be44f60dbf8cd2d70badd7c9edaf4e5a1d2a4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031456"
---
# <a name="speech-recognition-in-xamarinios"></a>Xamarin 中的语音识别

_本文介绍了新的语音 API，并演示了如何在 Xamarin iOS 应用程序中实现此功能，以支持连续语音识别和转录语音（从实时或录制的音频流）到文本。_

Apple 在 iOS 10 中的新增功能发布了语音识别 API，该 API 允许 iOS 应用支持连续语音识别和转录语音（从实时或录制的音频流）到文本。

根据 Apple，语音识别 API 具有以下功能和优势：

- 高度准确
- 图稿状态
- 易于使用
- 快速
- 支持多种语言
- 尊重用户隐私

## <a name="how-speech-recognition-works"></a>语音识别的工作方式

语音识别在 iOS 应用程序中实现，方法是获取实时或预先录制的音频（采用 API 支持的任何一种语言），并将其传递给语音识别器，这会返回明文形式的字词。

[![](speech-images/speech01.png "How Speech Recognition Works")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>键盘听写

当大多数用户在 iOS 设备上考虑语音识别时，他们会想到内置的 Siri 语音助手，该助手是在 iOS 5 中与 iPhone 4S 一起发布的键盘听写。

支持 TextKit 的任何 interface 元素（例如 `UITextField` 或 `UITextArea`）都支持键盘听写，并由用户在 iOS 虚拟键盘中单击 "听写" 按钮（直接位于空格键左侧）激活。

Apple 已经发布了以下键盘听写统计信息（从2011开始收集）：

- 键盘听写已广泛使用，因为它已在 iOS 5 中发布。
- 大约65000个应用每天使用该应用。
- 大约在第三方应用中完成了所有 iOS 听写。

键盘听写非常易于使用，因为它不需要开发人员在应用程序的 UI 设计中使用 TextKit interface 元素。 键盘听写还利用了不需要应用程序的任何特殊权限请求，然后才能使用。

使用新语音识别 Api 的应用需要用户授予特殊权限，因为语音识别需要在 Apple 服务器上传输和临时存储数据。 有关详细信息，请参阅[安全和隐私增强](~/ios/app-fundamentals/security-privacy.md)文档。

虽然键盘听写很容易实现，但它也有几个限制和缺点：

- 它需要使用文本输入字段和键盘显示。
- 它仅适用于实时音频输入，应用无法控制音频录制过程。
- 它不能控制用于解释用户语音的语言。
- 应用程序无法知道听写按钮是否可供用户使用。
- 应用无法自定义音频录制过程。
- 它提供一组非常浅的结果，这些结果缺少诸如时间和信心等信息。

### <a name="speech-recognition-api"></a>语音识别 API

Apple 在 iOS 10 中的新增功能，它提供了语音识别 API，使 iOS 应用程序能够更有效地实现语音识别。 此 API 与 Apple 用于同时为 Siri 和键盘听写提供强大功能的 API，并且能够提供具有艺术准确度状态的快速脚本。

语音识别 API 提供的结果以透明方式自定义到各个用户，而应用程序无需收集或访问任何私有用户数据。

语音识别 API 在用户讲话时以近乎实时的方式向调用应用程序提供结果，并且提供有关翻译结果的详细信息，而不仅仅是文本。 这些方法包括：

- 用户所说的内容的多个解释。
- 单个翻译的置信度。
- 计时信息。

如上所述，转换音频可以通过实时源，也可以从预录制的源以及 iOS 10 支持的任何一种50语言和方言提供。

语音识别 API 可用于运行 iOS 10 的任何 iOS 设备上，并且在大多数情况下，需要实时 internet 连接，因为在 Apple 的服务器上进行大量的翻译。 也就是说，一些较新的 iOS 设备支持特定语言的 always on、设备上翻译。

Apple 提供了一个可用性 API 来确定当前是否可以翻译给定的语言。 应用应使用此 API，而不是直接测试 internet 连接本身。

正如上面的键盘听写部分所述，语音识别需要通过 internet 在 Apple 服务器上传输和临时存储数据，因此，应用程序_必须_通过包括`NSSpeechRecognitionUsageDescription` 项在其 `Info.plist` 文件中，并调用 `SFSpeechRecognizer.RequestAuthorization` 方法。 

基于用于语音识别的音频源，可能需要对应用的 `Info.plist` 文件进行其他更改。 有关详细信息，请参阅[安全和隐私增强](~/ios/app-fundamentals/security-privacy.md)文档。

## <a name="adopting-speech-recognition-in-an-app"></a>在应用中采用语音识别

开发人员必须执行四个主要步骤，才能在 iOS 应用中采用语音识别：

- 使用 `NSSpeechRecognitionUsageDescription` 密钥在应用的 `Info.plist` 文件中提供使用说明。 例如，照相机应用可能包含以下说明， _"这只允许您通过说" 奶酪 "一词来拍摄照片。_
- 通过调用 `SFSpeechRecognizer.RequestAuthorization` 方法来提供一个说明（在上面的 `NSSpeechRecognitionUsageDescription` 关键字中提供），以使应用程序希望语音识别在对话框中访问用户并允许他们接受或拒绝。
- 创建语音识别请求：
  - 对于磁盘上预先录制的音频，请使用 `SFSpeechURLRecognitionRequest` 类。
  - 对于实时音频（或内存中的音频），请使用 `SFSPeechAudioBufferRecognitionRequest` 类。
- 将语音识别请求传递到语音识别器（`SFSpeechRecognizer`）开始识别。 应用可以选择性地按住返回的 `SFSpeechRecognitionTask` 来监视和跟踪识别结果。

下面将详细介绍这些步骤。

### <a name="providing-a-usage-description"></a>提供使用说明

若要在 `Info.plist` 文件中提供所需的 `NSSpeechRecognitionUsageDescription` 项，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击 `Info.plist` 文件，将其打开以进行编辑。
2. 切换到**源**视图： 

    [![](speech-images/speech02.png "The Source view")](speech-images/speech02.png#lightbox)
3. 单击 "**添加新条目**"，为 "**属性**" 输入 `NSSpeechRecognitionUsageDescription`，`String` "**类型**" 和 "**用法说明**" 作为**值**。 例如: 

    [![](speech-images/speech03.png "Adding NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. 如果应用将处理实时音频操作，还需要麦克风使用说明。 单击 "**添加新条目**"，为 "**属性**" 输入 `NSMicrophoneUsageDescription`，`String` "**类型**" 和 "**用法说明**" 作为**值**。 例如: 

    [![](speech-images/speech04.png "Adding NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
5. 保存对该文件所做的更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击 `Info.plist` 文件，将其打开以进行编辑。
2. 单击 "**添加新条目**"，为 "**属性**" 输入 `NSSpeechRecognitionUsageDescription`，`String` "**类型**" 和 "**用法说明**" 作为**值**。 例如: 

    [![](speech-images/speech03w.png "Adding NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
3. 如果应用将处理实时音频操作，还需要麦克风使用说明。 单击 "**添加新条目**"，为 "**属性**" 输入 `NSMicrophoneUsageDescription`，`String` "**类型**" 和 "**用法说明**" 作为**值**。 例如: 

    [![](speech-images/speech04w.png "Adding NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. 保存对该文件所做的更改。

-----

> [!IMPORTANT]
> 如果未能提供上述任一 `Info.plist` 密钥（`NSSpeechRecognitionUsageDescription` 或 `NSMicrophoneUsageDescription`），则在尝试访问语音识别或实时音频的麦克风时，可能会导致应用失败且不发出警告。

### <a name="requesting-authorization"></a>请求授权

若要请求允许应用访问语音识别的必需用户授权，请编辑主视图控制器类并添加以下代码：

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

`SFSpeechRecognizer` 类的 `RequestAuthorization` 方法将请求用户提供的权限，以使用开发人员在 `Info.plist` 文件的 `NSSpeechRecognitionUsageDescription` 键中提供的原因来访问语音识别。

`SFSpeechRecognizerAuthorizationStatus` 结果返回到 `RequestAuthorization` 方法的回调例程，该例程可用于根据用户的权限执行操作。 

> [!IMPORTANT]
> Apple 建议等待，直到用户在应用中启动了要求语音识别的操作，然后才能请求该权限。

### <a name="recognizing-pre-recorded-speech"></a>识别预先录制的语音

如果应用想要从预录制的 WAV 或 MP3 文件识别语音，则可以使用以下代码：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

首先详细查看此代码，它会尝试创建语音识别器（`SFSpeechRecognizer`）。 如果语音识别不支持默认语言，则将返回 `null` 并且函数将退出。

如果语音识别器可用于默认语言，则应用将检查其当前是否可用于使用 `Available` 属性进行识别。 例如，如果设备没有活动的 internet 连接，则识别可能不可用。

`SFSpeechUrlRecognitionRequest` 是从 iOS 设备上的预录制文件的 `NSUrl` 位置创建的，它将移交给语音识别器以使用回调例程进行处理。

调用回调时，如果 `NSError` 没有 `null` 则表明必须处理错误。 由于语音识别是以增量方式完成的，因此可能会多次调用回调例程，以便测试 `SFSpeechRecognitionResult.Final` 属性，以确定转换是否完整以及是否编写最佳版本的翻译（`BestTranscription`）。

### <a name="recognizing-live-speech"></a>识别实时语音

如果应用想要识别实时语音，该过程与识别预先录制的语音的过程非常相似。 例如:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

查看此代码的详细信息，它会创建几个专用变量来处理识别过程：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它使用 AV 基础来记录将传递到 `SFSpeechAudioBufferRecognitionRequest` 以处理识别请求的音频：

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

如果无法启动记录，应用会尝试开始记录，并处理任何错误：

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

识别任务将启动，并将句柄保存到识别任务（`SFSpeechRecognitionTask`）：

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

回调的使用方式类似于在预先录制的语音中使用的方式。

如果用户 stoped 录制，则会通知音频引擎和语音识别请求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果用户取消识别，则会通知音频引擎和识别任务：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

如果用户取消转换以释放内存和设备的处理器，则必须调用 `RecognitionTask.Cancel`。

> [!IMPORTANT]
> 如果无法提供 `NSSpeechRecognitionUsageDescription` 或 `NSMicrophoneUsageDescription` `Info.plist` 的密钥，则在尝试访问语音识别或实时音频的麦克风（`var node = AudioEngine.InputNode;`）时，可能会导致应用失败且不发出警告。 有关详细信息，请参阅上面的 "**提供使用说明**" 部分。

## <a name="speech-recognition-limits"></a>语音识别限制

在 iOS 应用中使用语音识别时，Apple 会施加以下限制：

- 语音识别对于所有应用都是免费的，但其用法并不是无限的：
  - 单个 iOS 设备的识别数量有限，每天可执行。
  - 每日请求将以全局方式限制应用。
- 应用必须准备好处理语音识别网络连接和使用速率限制失败。
- 在用户的 iOS 设备上，语音识别的电量消耗和高网络流量可能会产生较高的成本，因此 Apple 将严格的音频持续时间限制为大约一分钟的语音最大值。

如果应用经常达到其速率限制，则 Apple 会要求开发人员与他们联系。

## <a name="privacy-and-usability-considerations"></a>隐私和可用性注意事项

在 iOS 应用中包括语音识别时，Apple 提供以下建议，使其透明，并尊重用户的隐私：

- 录制用户的语音时，一定要清楚地指示记录是在应用的用户界面中进行的。 例如，应用可能会播放一条 "录音" 声并显示录制指示器。
- 不要将语音识别用于敏感用户信息，例如密码、运行状况数据或财务信息。
- 在操作_之前_显示识别结果。 这不仅提供有关应用程序的反馈，而且允许用户在发生识别错误时进行处理。

## <a name="summary"></a>总结

本文介绍了新的语音 API，并演示了如何在 Xamarin iOS 应用程序中实现此功能，以支持连续语音识别和转录语音（从实时或录制的音频流）到文本。 

## <a name="related-links"></a>相关链接

- [SpeakToMe （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-speaktome)
