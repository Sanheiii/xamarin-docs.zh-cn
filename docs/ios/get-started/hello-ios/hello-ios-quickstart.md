---
title: Hello，iOS - 快速入门
description: 本演练演示如何生成名为 Hello，iOS 的简单 Xamarin.iOS 应用程序。 在此过程中，它介绍了生成 Xamarin.iOS 应用程序必须了解的基本工具和概念。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: bde05cfeecaeba389224bbbdb7ae01fcb7bcb56c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434106"
---
# <a name="hello-ios--quickstart"></a>Hello，iOS - 快速入门

本指南介绍如何创建一个应用程序，它将用户输入的字母数字电话号码转换为数字电话号码，然后呼叫该号码。 最终应用程序如下所示：

 [![Hello.iOS 快速入门应用](hello-ios-quickstart-images/image1.png)](hello-ios-quickstart-images/image1.png#lightbox)

## <a name="requirements"></a>要求

使用 Xamarin 进行 iOS 开发需要：

- 运行 macOS High Sierra (10.13) 或更高版本的 Mac。
- 从 [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 安装的最新版本 Xcode 和 iOS SDK。

::: zone pivot="macos"

Xamarin.iOS 适用于以下设置：

- 符合以上规范的最新版本 Visual Studio for Mac。

[Xamarin.iOS Mac 安装指南](~/ios/get-started/installation/mac.md)提供了分步安装说明

::: zone-end
::: zone pivot="windows"

Xamarin.iOS 适用于以下设置：

- Windows 10 上的最新版 Visual Studio 2019 或 Visual Studio 2017 Community、Visual Studio 2017 Professional 或 Visual Studio 2017 Enterprise，与符合以上规范的 Mac 生成主机相配合。

[Xamarin.iOS Windows 安装指南](~/ios/get-started/installation/windows/index.md)提供了分步安装说明。

::: zone-end

开始之前，请下载 [Xamarin 应用图标集](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)。

::: zone pivot="macos"

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练

本演练介绍如何创建一个名为 Phoneword 的应用程序，它将字母数字电话号码转换为数字电话号码。

1. 从“应用程序”文件夹或 Spotlight 启动 Visual Studio for Mac ：

    ![“启动”屏幕](hello-ios-quickstart-images/image2new.png)

    在“启动屏幕”中，单击“新项目...”以创建新的 Xamarin.iOS 解决方案：

    ![iOS 解决方案](hello-ios-quickstart-images/image3new.png)

2. 从“新建解决方案”对话框中，选择“iOS”>“应用”>“单视图应用程序”模板，确保选择了 C#。 单击“下一步”  ：

    ![选择“单一视图应用程序”](hello-ios-quickstart-images/image4new.png)

3. 配置应用。 将其命名为 `Phoneword_iOS`，并将其他项保留为默认值。 单击“下一步”：

    ![输入应用名称](hello-ios-quickstart-images/image5new.png)

4. 将项目和解决方案名称保留为原样。 在此处选择项目的位置，或将它保留为默认值：

    ![选择项目的位置](hello-ios-quickstart-images/image6new.png)

5. 单击“创建”制定解决方案 。

6. 通过在“解决方案板”中双击 **Main.storyboard** 文件来打开它。 这提供了一种直观创建 UI 的方法：

    ![iOS 设计器](hello-ios-quickstart-images/image7new.png)

    注意，默认情况下会启用“大小类”。 请参阅[统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南以了解有关它们的详细信息。

7. 在“Toolbox Pad”中，向搜索栏键入“标签”并将一个“标签”拖动到设计图面上（中央区域）：

    ![将“标签”拖动到中心区域的设计图面上](hello-ios-quickstart-images/image8new.png)

    > [!NOTE]
    > 可以通过导航到“视图”>“面板”，随时打开“Properties Pad”或“工具箱”。

8. 抓取拖动控件的图柄（控件周围的圆圈）并使标签更宽：

    ![增加标签宽度](hello-ios-quickstart-images/image9.png)

9. 在设计图面上选择了“标签”的情况下，使用“属性板”将“标签”的“文本”属性更改为“Enter a Phoneword:”

    ![将标签设为“输入 Phoneword”](hello-ios-quickstart-images/image10.png)

10. 在工具箱内搜索“文本字段”，将一个“文本字段”从“工具箱”拖动到设计图面上，并将它放置在“标签”下方。 调整宽度，直到“文本字段”的宽度与“标签”相同：

    ![将“文本字段”宽度设置为与“标签”相同](hello-ios-quickstart-images/image12new.png)

11. 在设计图面上选择了“文本字段”的情况下，在“Properties Pad”的“标识”部分中将“文本字段”的“名称”属性更改为 `PhoneNumberText`，并将“文本”属性更改为“1-855-XAMARIN”：

    ![将“标题”属性更改为“1-855-XAMARIN”](hello-ios-quickstart-images/image13new.png)

12. 将一个“按钮”从“工具箱”拖动到设计图面上，并将它放置在“文本字段”下方。 调整宽度，以便“按钮”与“文本字段”和“标签”一样宽：

    ![调整宽度以便“按钮”的宽度与“文本字段”和“标签”相同](hello-ios-quickstart-images/image14new.png)

13. 在设计图面上选择了“按钮”的情况下，在“属性板”的“标识”部分中将“名称”属性更改为 `TranslateButton`。 将“标题”属性更改为“Translate”：

    ![将“标题”属性更改为“Translate”](hello-ios-quickstart-images/image15new.png)

14. 重复上面的两个步骤，将一个“按钮”从“工具箱”拖动到设计图面上，并将它放置第一个“按钮”下方。 调整宽度，以便该“按钮”与第一个“按钮”一样宽：

    ![调整宽度，使该按钮宽度与第一个按钮相同](hello-ios-quickstart-images/image16new.png)

15. 在设计图面上选择了第二个“按钮”的情况下，在“属性板”的“标识”部分中将“名称”属性更改为 `CallButton`。 将“标题”属性更改为“Call”：

    ![将“标题”属性更改为“Call”](hello-ios-quickstart-images/image17new.png)

    通过导航到“文件”>“保存”或通过按 **⌘ + s** 来保存更改。

16. 需要将一些逻辑添加到应用，以便将电话号码为字母数字转换为数字。 右键单击“Solution Pad”中的“Phoneword_iOS”，再依次选择“添加”>“新文件…”或按“⌘ + n”，向项目添加新文件：

    ![向项目添加一个新文件](hello-ios-quickstart-images/image18.png)

17. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 `PhoneTranslator`：

    ![选择 Empty 类，将新的文件命名为“PhoneTranslator”](hello-ios-quickstart-images/image19.png)

18. 这会为我们创建新的空 C# 类。 删除所有模板代码并替换为以下代码：

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword_iOS
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    保存 **PhoneTranslator.cs** 文件并关闭它。

19. 添加代码以关联用户界面。 若要执行此操作，请在“解决方案板”中双击 **ViewController.cs**以打开它：

    ![添加代码，关联用户界面](hello-ios-quickstart-images/image20new.png)

20. 首先关联 `TranslateButton`。 在 **ViewController** 类中，找到 `ViewDidLoad` 方法并在 `base.ViewDidLoad()` 调用下方添加以下代码：

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(
            PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call ", UIControlState.Normal);
            CallButton.Enabled = false;
        } else {
            CallButton.SetTitle ("Call " + translatedNumber,
                UIControlState.Normal);
            CallButton.Enabled = true;
        }
    };
    ```

    如果文件的命名空间不同，则包括 `using Phoneword_iOS;`。

21. 添加代码以响应按第二个按钮的用户（名为 `CallButton`）。 将以下代码置于 `TranslateButton` 的代码下方，并将 `using Foundation;` 添加到文件顶部：

    ```csharp
        CallButton.TouchUpInside += (object sender, EventArgs e) => {
            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl ("tel:" + translatedNumber);

            // ...otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl (url)) {
                var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                PresentViewController (alert, true, null);
            }
        };
    ```

22. 保存更改，然后通过选择“生成”>“全部生成”或按 **⌘ + B** 来生成应用程序。如果应用程序进行了编译，则成功消息会出现在 IDE 顶部：

    ![成功消息将出现在 IDE 顶部](hello-ios-quickstart-images/image21.png)

    如果发生错误，则完成前面的步骤并更正任何错误，直到应用程序成功生成。

23. 最后，在 **iOS 模拟器**中测试应用程序。 在 IDE 左上角，从第一个下拉菜单中选择“调试”，并从第二个下拉菜单中选择“iPhone XR iOS 12.0”（或其他可用的模拟器），然后按“启动”（类似于“播放”按钮的三角形按钮）  ：

    ![选择模拟器，然后按“启动”](hello-ios-quickstart-images/image27.png)

    > [!NOTE]
    > 目前，Apple 可能要求拥有开发证书或签名标识才能为设备或模拟器生成代码。 请按照[设备预配指南](~/ios/get-started/installation/device-provisioning/manual-provisioning.md)中的步骤执行此设置。

24. 这会在 iOS 模拟器中启动应用程序：

    ![应用程序在 iOS 模拟器中运行](hello-ios-quickstart-images/image28.png)

    iOS 模拟器不支持电话呼叫；相反，在尝试拨打电话时，将会看到警报对话框：

    ![尝试拨打电话时的警报对话框](hello-ios-quickstart-images/image29.png)

::: zone-end
::: zone pivot="windows"

## <a name="visual-studio-walkthrough"></a>Visual Studio 演练

本演练介绍如何创建一个名为 Phoneword 的应用程序，它将字母数字电话号码转换为数字电话号码。

> [!NOTE]
> 本演练在 Windows 10 虚拟机上使用 Visual Studio Enterprise 2017。 你的设置可以与此不同，只要满足以上要求即可，但是请注意，一些屏幕截图可能因你的设置而异。

> [!NOTE]
> 继续此演练前，必须已从 Visual Studio 连接到 Mac。 这是因为 Xamarin.iOS 依赖于 Apple 的工具生成并启动 iOS 设计器和应用程序。 若要获取设置，请执行[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南中的步骤。

1. 从“开始”菜单启动 Visual Studio：

    ![“启动”屏幕](hello-ios-quickstart-images/image001-.png)

    通过选择“文件”>“新建”>“项目...”>“Visual C#”>“iPhone 和 iPad”>“iOS 应用(Xamarin)”，创建新的 Xamarin.iOS 解决方案：

    ![选择“iOS 应用 (Xamarin)”项目类型](hello-ios-quickstart-images/image002.w157.png "选择“iOS 应用 (Xamarin)”项目类型")

    在接下来出现的对话框中，选择“单视图应用”模板，然后按“确定”创建项目 ：

    ![选择“单一视图”项目模版](hello-ios-quickstart-images/image002-2.w157.png "选择“单一视图”项目模版")

1. 确认工具栏中的 Xamarin Mac 代理图标为绿色。

    ![确认工具栏中的 Xamarin Mac Agent 图标是否为绿色](hello-ios-quickstart-images/vs-image4.png)

    如果不是，则意味着没有连接到 Mac 生成主机，请按照[配置指南](~/ios/get-started/installation/windows/connecting-to-mac/index.md)中的步骤进行连接。

1. 通过在“解决方案资源管理器”中双击 iOS 设计器中的 **Main.storyboard** 文件来打开它：

    ![iOS 设计器](hello-ios-quickstart-images/vs-image7.png)

1. 打开“工具箱”选项卡，向搜索栏中输入“标签”并将一个“标签”拖动到设计图面上（中央区域）：

    ![将“标签”拖动到中心区域的设计图面上](hello-ios-quickstart-images/vs-image8.png)

1. 接下来，抓取“拖动控件”的图柄，并拉宽标签：

    ![增加标签宽度](hello-ios-quickstart-images/vs-image9.png)

1. 在设计图面上选择了“标签”的情况下，使用“属性窗口”将“标签”的“文本”属性更改为“Enter a Phoneword:”

    ![将“标签”的“文本”属性更改为“Enter a Phoneword”](hello-ios-quickstart-images/vs-image10.png)

    > [!NOTE]
    > 可以通过导航到“视图”菜单，随时打开“属性”或“工具箱”。

1. 在工具箱内搜索“文本字段”，将一个“文本字段”从“工具箱”拖动到设计图面上，并将它放置在“标签”下方。 调整宽度，直到“文本字段”的宽度与“标签”相同：

    ![调整宽度以便“文本字段”的宽度与“标签”相同](hello-ios-quickstart-images/vs-image12.png)

1. 在设计图面上选择了“文本字段”的情况下，在“属性”的“标识”部分中将“文本字段”的“名称”属性更改为 `PhoneNumberText`，并将“文本”属性更改为“1-855-XAMARIN”：

    ![将“文本”属性更改为“1-855-XAMARIN”](hello-ios-quickstart-images/vs-image13.png)

1. 将一个“按钮”从“工具箱”拖动到设计图面上，并将它放置在“文本字段”下方。 调整宽度，以便“按钮”与“文本字段”和“标签”一样宽：

    ![调整宽度以便“按钮”的宽度与“文本字段”和“标签”相同](hello-ios-quickstart-images/vs-image14.png)

1. 在设计图面上选择了“按钮”的情况下，在“属性”的“标识”部分中将“名称”属性更改为 `TranslateButton`。 将“标题”属性更改为“Translate”：

    ![将“标题”属性更改为“Translate”](hello-ios-quickstart-images/vs-image15.png)

1. 重复前面的两个步骤，将一个“按钮”从“工具箱”拖动到设计图面上，并将它放置第一个“按钮”下方。 调整宽度，以便该“按钮”与第一个“按钮”一样宽：

    ![调整宽度，使该按钮宽度与第一个按钮相同](hello-ios-quickstart-images/vs-image16.png)

1. 在设计图面上选择了第二个“按钮”的情况下，在“属性”的“标识”部分中将“名称”属性更改为 `CallButton`。 将“标题”属性更改为“Call”：

    ![将“标题”属性更改为“Call”](hello-ios-quickstart-images/vs-image17.png)

    通过导航到“文件”>“保存”或通过按 **Ctrl + s** 来保存更改。

1. 添加一些代码，以将电话号码从字母数字转换为数字。 若要执行此操作，请首先通过在“解决方案资源管理器”中右键单击“Phoneword”项目，然后选择“添加”>“新建项…”或按 **Ctrl + Shift + A**，向项目添加新文件：

    ![添加一些代码以将电话号码从字母数字转换为数字](hello-ios-quickstart-images/vs-image18.png)

1. 在“添加新项”对话框（右键单击项目，选择“添加”>“新建项...”）中，选择“Apple”>“类”，然后将新文件命名为 `PhoneTranslator` ：

    ![添加名为“PhoneTranslator”的新类](hello-ios-quickstart-images/vs-image19.w157.png)

    > [!IMPORTANT]
    > 请确保选择在图标中包含 C# 的“类”模板。 否则，可能无法引用此新类。

1. 这会创建新 C# 类。 删除所有模板代码并替换为以下代码：

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    保存 **PhoneTranslator.cs** 文件并关闭它。

1. 在“解决方案资源管理器”中双击 **ViewController.cs** 以打开它，以便可以添加逻辑以处于与按钮进行的交互：

    ![添加逻辑以处理与按钮之间的交互](hello-ios-quickstart-images/vs-image20.png)

1. 首先关联 `TranslateButton`。 在 **ViewController** 类中，找到 `ViewDidLoad` 方法。 在 `ViewDidLoad` 中的 `base.ViewDidLoad()` 调用下添加以下按钮代码：

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {

        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call", UIControlState.Normal);
            CallButton.Enabled = false;
            }
        else {
            CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
            CallButton.Enabled = true;
            }
    };
    ```

    如果文件的命名空间不同，则包括 `using Phoneword;`。

1. 添加代码以响应按第二个按钮的用户（名为 `CallButton`）。 将以下代码置于 `TranslateButton` 的代码下方，并将 `using Foundation;` 添加到文件顶部：

    ```csharp
    CallButton.TouchUpInside += (object sender, EventArgs e) => {
        var url = new NSUrl ("tel:" + translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app,
            // otherwise show an alert dialog

        if (!UIApplication.SharedApplication.OpenUrl (url)) {
                        var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                        alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        PresentViewController (alert, true, null);
                    }
    };
    ```

1. 保存更改，然后通过选择“生成”>“生成解决方案”或按 **Ctrl + Shift + B** 来生成应用程序。如果应用程序进行了编译，则成功消息会出现在 IDE 底部：

    ![成功消息将出现在 IDE 底部](hello-ios-quickstart-images/vs-image21.png)

    如果发生错误，则完成前面的步骤并更正任何错误，直到应用程序成功生成。

1. 最后，在“远程 iOS 模拟器”中测试应用程序。 在 IDE 工具栏中，从下拉菜单中选择“调试”和“iPhone 8 Plus iOS x.x”，然后按“启动”（类似于“播放”按钮的绿色三角形）：

    ![按“启动”](hello-ios-quickstart-images/vs-image27.png)

1. 这会在 iOS 模拟器中启动应用程序：

    ![应用程序在 iOS 模拟器中运行](hello-ios-quickstart-images/vs-image28.png)

    iOS 模拟器不支持电话呼叫；相反，在尝试拨打电话时，将会看到警报对话框：

    ![尝试拨打电话时会显示警报对话框](hello-ios-quickstart-images/vs-image29.png)

::: zone-end

祝贺你完成第一个 Xamarin.iOS 应用程序！

现在，可以剖析 [Hello，iOS 深入了解](~/ios/get-started/hello-ios/hello-ios-deepdive.md)的本指南中显示的工具和技能。

## <a name="related-links"></a>相关链接

- [Xamarin 应用图标和启动图像（示例）](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hello，iOS（示例）](/samples/xamarin/ios-samples/hello-ios)
- [iOS 人机界面指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 预配门户](https://developer.apple.com/ios/manage/overview/index.action)