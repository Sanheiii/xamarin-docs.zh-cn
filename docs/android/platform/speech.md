---
title: Android 语音
description: 本文介绍了使用功能强大的 Android.Speech 命名空间的基本知识。 从一开始，Android 就能够识别语音并将其作为文本输出。 这是一个相对简单的过程。 不过，对于文本转语音，此过程更为复杂，因为它不仅需要考虑语音引擎，还必须考虑到文本转语音 (TTS) 系统中提供和安装的语言。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: ce94179fe0ae7f3df1f2fa5ead20ec09fd6f8f5e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457908"
---
# <a name="android-speech"></a>Android 语音

_本文介绍了使用功能强大的 Android.Speech 命名空间的基本知识。从一开始，Android 就能够识别语音并将其作为文本输出。这是一个相对简单的过程。不过，对于文本转语音，此过程更为复杂，因为它不仅需要考虑语音引擎，还必须考虑到文本转语音 (TTS) 系统中提供和安装的语言。_

## <a name="speech-overview"></a>语音概述

随着设备对自然通信需求的不断增长，拥有一个“理解”人类语言并能清楚地表达所输入的内容（语音转文本和文本转语音）的系统是移动开发中一个日益增长的领域。 在许多情况下，将文本转换为语音的功能是一个非常有用的工具（反之亦然），可将其整合到 Android 应用程序中。

例如，由于开车时对使用手机的限制，用户需要一种无需手动操作其设备的方式。 过多不同的 Android 外形规格（如 Android Wear）以及越来越多的人使用 Android 设备（如平板电脑和记事本），人们更加关注出色的 TTS 应用程序。

Google 就 Android.Speech 命名空间向开发人员提供了一组丰富的 API，以涵盖使设备具备“语音识别”功能的大多数实例（如为盲人设计的软件）。  命名空间包含允许通过 `Android.Speech.Tts` 将文本转换为语音的工具，对用于执行转换的引擎的控制，以及允许将语音转换为文本的多个 `RecognizerIntent`。

虽然这些工具是用来理解语音的，但根据所使用的硬件存在一些限制。 设备不太可能用每一种可用的语言成功解释所有语音内容。

## <a name="requirements"></a>要求

除了设备要有麦克风和扬声器外，本指南没有其他特殊要求。

Android 设备解释语音的核心是使用具有相应 `OnActivityResult` 的 `Intent`。
但重要的是要认识到语音并不会被理解，而是被解释为文本。 这种区别很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>理解与解释的区别

理解的简单定义是，你能够通过语气和上下文来确定话语的实际含义。 解释则意味着汲取字词然后以另一种形式将其输出。

请考虑以下日常对话中会用到的简单示例：

<kbd>嗨，你好吗？</kbd>

没有音调变化（强调特定字词或字词的一部分），这是一个简单的问题。 然而，如果这句话采用慢速语调，听者会发现问候方不太开心，可能需要来点安慰，或者问候方身体不适。 如果强调的是“吗”，问候方通常对回答更感兴趣。

如果没有相当强大的音频处理功能来利用音调变化，也没有一定程度的人工智能 (AI) 来理解上下文，则软件甚至无法开始理解话语内容，为此，一部简单的手机可以采取的最佳做法就是将语音转换为文本。

## <a name="setting-up"></a>设置

在使用语音系统之前，最好先检查下设备是否有麦克风。 尝试在没有安装麦克风的 Kindle 或 Google 记事本上运行应用程序将毫无意义。

下面的代码示例演示如何查询麦克风是否可用，如果不可能，则创建警报。 如果此时没有可用的麦克风，可以退出活动或禁用录制语音功能。

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>创建意向

语音系统的意向是使用一种称为 `RecognizerIntent` 的特殊类型的意向。 此意向控制大量参数，包括在录制结束之前在静默状态下要等待多长时间、需要识别和输出的任何其他语言，以及作为指令的方式在 `Intent` 的模式对话框中包含的任何文本。 在此代码段中，`VOICE` 是用于在 `OnActivityResult`中进行识别的 `readonly int`。

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>语音转换

将在 `Intent` 中传递从语音解释的文本，该文本在活动完成时返回并通过 `GetStringArrayListExtra(RecognizerIntent.ExtraResults)` 访问。 这将返回一个 `IList<string>`，根据调用方意向中请求的语言数量（在 `RecognizerIntent.ExtraMaxResults` 中指定），可以使用和显示其中的索引。 不过，对于任何列表，都需要进行检查以确保存在要显示的数据。

侦听 `StartActivityForResult` 的返回值时，必须提供 `OnActivityResult` 方法。

在下面的示例中，`textBox` 是用于输出所指示内容的 `TextBox`。 它同样可以用来将文本传递给某种形式的解释器，然后应用程序可以从那里比较文本，并将其分支到应用程序的其他部分。

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>文本转语音

文本转语音并不完全是语音转文本的反向，它依赖于两个关键组件：在设备上安装的文本转语音引擎，以及安装的语言。

大多数情况下，Android 设备会安装默认的 Google TTS 服务和至少一种语言。 这是在设备首次设置时确定的，并将基于设备当时所在的位置（例如，在德国设置的电话将安装德语，而在美国设置的电话将安装美国英语）。

### <a name="step-1---instantiating-texttospeech"></a>步骤 1 - 实例化 TextToSpeech

`TextToSpeech` 最多可使用 3 个参数，前两个参数是必需的，第三个参数是可选的（`AppContext`、`IOnInitListener`、`engine`）。 侦听器用于绑定到服务并测试故障，而引擎是任意数量的 Android 文本转语音可用引擎。 至少，设备将拥有 Google 自己的引擎。

### <a name="step-2---finding-the-languages-available"></a>步骤 2 - 查找可用语言

`Java.Util.Locale` 类包含一个名为 `GetAvailableLocales()` 的有效方法。 然后，可以根据安装的语言对语音引擎支持的语言列表进行测试。

生成“理解的”语言列表很简单。 始终会有一种默认语言（用户首次设置设备时设置的语言），因此在此示例中，`List<string>` 的第一个参数为“Default”，列表的其余部分将根据 `textToSpeech.IsLanguageAvailable(locale)` 的结果进行填充。

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

此代码将调用 [TextToSpeech.IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*)，以测试给定区域设置的语言包是否已存在于设备上。
此方法返回 [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult)，该值指示所传递的区域设置的语言是否可用。 如果 `LanguageAvailableResult` 指示语言为 `NotSupported`，则表明该语言没有可用的语音包（甚至是下载）。 如果 `LanguageAvailableResult` 设置为 `MissingData`，则可以按照下面步骤 4 中的说明下载新语言包。

### <a name="step-3---setting-the-speed-and-pitch"></a>步骤 3 - 设置速度和音高

Android 允许用户通过更改 `SpeechRate` 和 `Pitch`（语音的速度和音调）来更改语音声音。 范围从 0 到 1，对于这两个要素，“正常”语音为 1。

### <a name="step-4---testing-and-loading-new-languages"></a>步骤 4 - 测试和加载新语言

下载新语言是通过使用 `Intent` 来执行的。 此意向的结果将导致调用 [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) 方法。 与语音转文本示例（使用 [RecognizerIntent](xref:Android.Speech.RecognizerIntent) 作为 `Intent` 的 `PutExtra` 参数）不同，测试和加载 `Intent` 基于 `Action`：

- [TextToSpeech.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; 从平台 `TextToSpeech` 引擎启动一个活动，验证设备上的语言资源是否正确安装及其可用性。

- [TextToSpeech.Engine.ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; 启动一个活动，该活动提示用户下载所需的语言。

下面的代码示例演示如何使用这些操作来测试语言资源并下载一种新语言：

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` 测试语言资源的可用性。 此测试完成时将调用 `OnActivityResult`。 如果需要下载语言资源，`OnActivityResult` 会触发 `TextToSpeech.Engine.ActionInstallTtsData` 操作，以启动一个活动来允许用户下载所需的语言。 请注意，此 `OnActivityResult` 实现不检查 `Result` 代码，因为在此简化示例中，已确定需要下载语言包。

`TextToSpeech.Engine.ActionInstallTtsData` 操作将向用户显示“Google TTS 语音数据”活动，以选择要下载的语言：

![Google TTS 语音数据活动](speech-images/01-google-tts-voice-data.png)

例如，用户可能会选择法语并单击下载图标以下载法语语音数据：

![下载法语语言的示例](speech-images/02-selecting-french.png)

下载完成后，会自动安装此数据。

### <a name="step-5---the-ioninitlistener"></a>步骤 5 - IOnInitListener

要使活动能够将文本转换为语音，必须实现接口方法 `OnInit`（这是为实例化 `TextToSpeech` 类指定的第二个参数）。 这将初始化侦听器并测试结果。

侦听器应至少测试 `OperationResult.Success` 和 `OperationResult.Failure`。
下面的示例演示了这一点：

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>总结

在本指南中，我们介绍了文本转语音和语音转文本的基本知识，以及如何在自己的应用程序中包含这些内容的可能方法。 虽然它们并没有涵盖所有特殊情况，但你现在应基本了解如何解释语音、如何安装新语言以及如何增加应用的包容性。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms DependencyService](/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [文本转语音（示例）](/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [语音转文本（示例）](/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Android.Speech 命名空间](xref:Android.Speech)
- [Android.Speech.Tts 命名空间](xref:Android.Speech.Tts)