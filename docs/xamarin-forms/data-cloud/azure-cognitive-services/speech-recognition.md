---
title: 使用语音服务 API 的语音识别
description: 本文介绍如何使用 Azure Speech Service API 将语音转录到应用程序中的文本 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e5888e77948bef0fbb60a4e0068cbedf2d2a50e7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555380"
---
# <a name="speech-recognition-using-azure-speech-service"></a>使用 Azure Speech Service 进行语音识别

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

Azure Speech Service 是一种基于云的 API，它提供以下功能：

- **语音到文本** 转录音频文件或流到文本。
- **文本到语音** 转换将输入文本转换为用户喜欢的合成语音。
- **语音翻译** 为语音到文本和语音到语音转换启用了实时、多语言翻译。
- **语音助手** 可为应用程序创建类似于用户的对话接口。

本文介绍如何 Xamarin.Forms 使用 Azure 语音服务在示例应用程序中实现语音到文本。 以下屏幕截图显示了 iOS 和 Android 上的示例应用程序：

[![IOS 和 Android 上的示例应用程序的屏幕截图](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "IOS 和 Android 上的示例应用程序的屏幕截图")

## <a name="create-an-azure-speech-service-resource"></a>创建 Azure 语音服务资源

Azure Speech Service 是 Azure 认知服务的一部分，可为图像识别、语音识别和翻译等任务和必应搜索提供基于云的 Api。 有关详细信息，请参阅 [什么是 Azure 认知服务？](/azure/cognitive-services/welcome)。

示例项目需要在 Azure 门户中创建 Azure 认知服务资源。 可以为单个服务（如语音服务）或多服务资源创建认知服务资源。 创建语音服务资源的步骤如下所示：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 创建多服务或单一服务资源。
1. 获取资源的 API 密钥和区域信息。
1. 更新示例 **Constants.cs** 文件。

有关创建资源的循序渐进指南，请参阅 [创建认知服务资源](/azure/cognitive-services/cognitive-services-apis-create-account)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。 拥有帐户后，可以在免费层创建单个服务资源来试用服务。

## <a name="configure-your-app-with-the-speech-service"></a>通过语音服务配置应用

创建认知服务资源后，可使用 Azure 资源中的区域和 API 密钥更新 **Constants.cs** 文件：

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>安装 NuGet 语音服务包

示例应用程序使用 **cognitiveservices account** NuGet 包来连接到 Azure 语音服务。 在共享项目和每个平台项目中安装此 NuGet 包。

## <a name="create-an-imicrophoneservice-interface"></a>创建 IMicrophoneService 接口

每个平台都需要有权访问麦克风。 示例项目 `IMicrophoneService` 在共享项目中提供了一个接口，并使用 Xamarin.Forms `DependencyService` 来获取接口的平台实现。

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>创建页面布局

示例项目定义了 **MainPage** 文件中的基本页面布局。 键布局元素是一个 `Button` ，它启动脚本过程、 `Label` 包含转录文本的，以及一个要在脚本 `ActivityIndicator` 进行过程中显示的：

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>实现语音服务

**MainPage.xaml.cs**代码隐藏文件包含用于从 Azure Speech Service 发送音频和接收转录文本的所有逻辑。

`MainPage`构造函数从获取接口的实例 `IMicrophoneService` `DependencyService` ：

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

`TranscribeClicked`当点击实例时，将调用方法 `transcribeButton` ：

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

`TranscribeClicked` 方法执行以下操作：

1. 检查应用程序是否有权访问麦克风，如果不是，则提前退出。
1. 创建类的实例（ `SpeechRecognizer` 如果它尚不存在）。
1. 如果正在进行，则停止运行。
1. 插入时间戳，如果未在进行，则启动连续脚本。
1. 通知应用程序基于新应用程序状态更新其外观。

类方法的其余部分 `MainPage` 是用于显示应用程序状态的帮助程序：

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

`UpdateTranscription`方法将提供的写入名为的 `newText` `string` `Label` 元素 `transcribedText` 。 它强制在 UI 线程上进行此更新，以便在不引发异常的情况下从任何上下文中进行调用。 将 `InsertDateTimeRecord` 当前日期和时间写入 `transcribedText` 实例，以标记新脚本的开头。 最后， `UpdateDisplayState` 方法更新 `Button` 和 `ActivityIndicator` 元素以反映脚本是否正在进行。

## <a name="create-platform-microphone-services"></a>创建平台麦克风服务

应用程序必须具有用于收集语音数据的麦克风访问权限。 `IMicrophoneService`接口必须在 `DependencyService` 每个平台上实现并注册，才能让应用程序正常工作。

### <a name="android"></a>Android

示例项目定义了一个 `IMicrophoneService` 名为的 Android 实现 `AndroidMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

`AndroidMicrophoneService`具有以下功能：

1. `Dependency`特性向注册该类 `DependencyService` 。
1. `GetPermissionAsync`方法根据 Android SDK 版本检查是否需要权限，并在 `RequestMicPermissions` 尚未授予权限的情况下调用。
1. `RequestMicPermissions`方法使用 `Snackbar` 类从用户请求权限（如果需要基本原理），否则它会直接请求音频录制权限。
1. `OnRequestPermissionResult` `bool` 当用户对权限请求作出响应后，将使用结果调用方法。

`MainActivity`自定义类，以便 `AndroidMicrophoneService` 在完成权限请求时更新实例：

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

`MainActivity`类定义了一个名为的静态引用 `Instance` ， `AndroidMicrophoneService` 对象在请求权限时需要该引用。 `OnRequestPermissionsResult` `AndroidMicrophoneService` 当用户批准或拒绝了权限请求时，它将重写方法以更新对象。

最后，Android 应用程序必须包括在 **AndroidManifest.xml** 文件中录制音频的权限：

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

示例项目定义了一个 `IMicrophoneService` 名为的 iOS 实现 `iOSMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

`iOSMicrophoneService`具有以下功能：

1. `Dependency`特性向注册该类 `DependencyService` 。
1. `GetPermissionAsync`方法调用 `RequestMicPermissions` 来请求设备用户的权限。
1. `RequestMicPermissions`方法使用共享 `AVAudioSession` 实例来请求录制权限。
1. `OnRequestPermissionResult`方法 `TaskCompletionSource` 用提供的值更新实例 `bool` 。

最后，iOS 应用 **信息。 info.plist** 必须包含一条消息，告知用户应用请求访问麦克风的原因。 编辑 info.plist 文件，以在元素中包含以下标记 `<dict>` ：

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>UWP

示例项目定义了一个 `IMicrophoneService` 名为的 UWP 实现 `UWPMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

`UWPMicrophoneService`具有以下功能：

1. `Dependency`特性向注册该类 `DependencyService` 。
1. 此 `GetPermissionAsync` 方法尝试初始化 `MediaCapture` 实例。 如果此操作失败，则会启动用户请求以启用麦克风。
1. 此 `OnRequestPermissionResult` 方法存在以满足接口，但不是 UWP 实现所必需的。

最后，UWP **Package. appxmanifest.xml** 必须指定应用程序使用麦克风。 双击 appxmanifest.xml 文件，并在 Visual Studio 2019 的 "**功能**" 选项卡上选择 "**麦克风**" 选项：

[![Visual Studio 2019 中的清单屏幕截图](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Visual Studio 2019 中的清单屏幕截图")

## <a name="test-the-application"></a>测试应用程序

运行应用，然后单击 " **转录** " 按钮。 应用应请求麦克风访问并开始脚本过程。 `ActivityIndicator`将动画显示，并显示脚本处于活动状态。 在说话时，应用程序会将音频数据流式传输到 Azure 语音服务资源，该资源将以转录文本响应。 转录文本将在收到时显示在 `Label` 元素中。

> [!NOTE]
> Android 仿真程序无法加载和初始化语音服务库。 对于 Android 平台，建议使用物理设备进行测试。

## <a name="related-links"></a>相关链接

- [Azure 语音服务示例](/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Azure 语音服务概述](/azure/cognitive-services/speech-service/overview)
- [创建认知服务资源](/azure/cognitive-services/cognitive-services-apis-create-account)
- [快速入门：识别来自麦克风的语音](/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)