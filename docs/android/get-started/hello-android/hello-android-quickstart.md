---
title: Hello, Android：快速入门
description: 在由两部分构成的本指南中，你将生成第一个 Xamarin.Android 应用程序（使用 Visual Studio 或 Visual Studio for Mac），并了解使用 Xamarin 进行 Android 应用程序开发的基础知识。 在此过程中，会向你介绍生成和部署 Xamarin.Android 应用程序所需的工具、概念和步骤。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: da074d5df9033e4a86c4b38ca76d7cb1373ab205
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455204"
---
# <a name="hello-android-quickstart"></a>Hello, Android：快速入门

在这个两部分的指南中，你将使用 Visual Studio 生成第一个 Xamarin.Android 应用程序，并了解使用 Xamarin 进行 Android 应用程序开发的基础知识  。

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)

你将创建一个应用程序，它将字母数字电话号码（由用户输入）转换为数字电话号码，然后向用户显示此数字电话号码。 最终应用程序如下所示：

[![完成时应用的屏幕截图](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

::: zone pivot="windows"

## <a name="windows-requirements"></a>Windows 要求

若要按照本演练进行操作，你需要以下内容：

- Windows 10。

- Visual Studio 2019 或 Visual Studio 2017（版本 15.8 或更高版本）：Community、Professional 或 Enterprise。

::: zone-end
::: zone pivot="macos"

## <a name="macos-requirements"></a>macOS 要求

若要按照本演练进行操作，你需要以下内容：

- Visual Studio for Mac 的最新版本。

- 运行 macOS HIgh Sierra (10.13) 或更高版本的 Mac。

::: zone-end

本演练假设最新版本的 Xamarin.Android 已在你选择的平台上安装并运行。 有关安装 Xamarin.Android 的指南，请参阅 [Xamarin.Android 安装](~/android/get-started/installation/index.md)指南。

## <a name="configuring-emulators"></a>配置仿真器

如果使用的是 Android 仿真器，建议将仿真器配置为使用硬件加速。 [通过硬件加速提高仿真器性能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中提供了有关配置硬件加速的说明。

## <a name="create-the-project"></a>创建项目

::: zone pivot="windows"

启动 Visual Studio。 单击“文件”>“新建”>“项目”以创建新项目  。

在“新建项目”  对话框中，单击“Android 应用”  模板。
将新项目命名为 `Phoneword`，然后单击“确定”  ：

[![新项目为 Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w158-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w158.png#lightbox)

在“新 Android 应用”  对话框中，依次单击“空白应用”  和“确定”  ，以新建项目：

[![选择“空白应用”模板](hello-android-quickstart-images/vs/02-blank-app-w158-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w158.png#lightbox)

## <a name="create-a-layout"></a>创建布局

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

创建新项目之后，在“解决方案资源管理器”  中展开 **Resources** 文件夹，然后展开 **layout** 文件夹。
双击“activity_main.axml”  ，以在 Android Designer 中打开它。 这是应用屏幕的布局文件：

[![打开活动 axml 文件](hello-android-quickstart-images/vs/03-open-layout-w158-sml.png)](hello-android-quickstart-images/vs/03-open-layout-w158.png#lightbox)

> [!TIP]
> 较新版本的 Visual Studio 包含的应用模板略有不同。
>
> 1. 布局位于 content_main.axml 中，而不是 activity_main.axml 中   。
> 2. 默认布局是 `RelativeLayout`。 要执行本页面上的其他步骤，应将 `<RelativeLayout>` 标记更改为 `<LinearLayout>`，并将其他属性 `android:orientation="vertical"` 添加到 `LinearLayout` 开始标记。

在“工具箱”  （左侧区域）的搜索字段中输入 `text`，并将一个“文本(大)”  小组件拖动至 Design Surface 上（中央区域）：

[![添加大文本小组件](hello-android-quickstart-images/vs/04-large-text-w158-sml.png)](hello-android-quickstart-images/vs/04-large-text-w158.png#lightbox)

在 Design Surface 上选中“文本(大)”控件的情况下，可使用“属性”窗格将“文本(大)”小组件的 `Text` 属性更改为 `Enter a Phoneword:`   ：

[![设置大文本属性](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158.png#lightbox)

将一个“纯文本”  小组件从“工具箱”  拖动到设计图面上，并将它放置在“文本(大)”  小组件下。 直到将鼠标指针移动到布局中可接受小组件的位置，才会放置小组件。 在下面的屏幕截图中，直到鼠标指针移动到前一个 `TextView` 的下方（如右图所示），才能放置小组件（如左图所示）：

[![鼠标指示可放置小组件的位置](hello-android-quickstart-images/vs/06a-cant-drop-w158-sml.png)](hello-android-quickstart-images/vs/06a-cant-drop-w158.png#lightbox)

正确放置“纯文本”（`EditText` 小组件）后，它将如以下屏幕截图所示  ：

[![添加纯文本小组件](hello-android-quickstart-images/vs/06b-plain-text-w158-sml.png)](hello-android-quickstart-images/vs/06b-plain-text-w158.png#lightbox)

在 Design Surface 上选中“纯文本”  小组件的情况下，可使用“属性”  窗格将“纯文本”  小部件的 `Id` 属性更改为 `@+id/PhoneNumberText`，并将 `Text` 属性更改为 `1-855-XAMARIN`：

[![设置纯文本属性](hello-android-quickstart-images/vs/07-add-properties-w158-sml.png)](hello-android-quickstart-images/vs/07-add-properties-w158.png#lightbox)

将一个“按钮”  从“工具箱”  拖动到设计图面上，并将它放置在“纯文本”  小组件下方：

[![将“Translate”按钮拖至 Design Surface](hello-android-quickstart-images/vs/08-drag-button-w158-sml.png)](hello-android-quickstart-images/vs/08-drag-button-w158.png#lightbox)

在 Design Surface 上选中“按钮”后，使用“属性”窗格将其 `Text` 属性更改为 `Translate`，将其 `Id` 属性更改为 `@+id/TranslateButton`  ：

[![设置“Translate”按钮属性](hello-android-quickstart-images/vs/09-translate-button-w158-sml.png)](hello-android-quickstart-images/vs/09-translate-button-w158.png#lightbox)

将一个“TextView”从“工具箱”拖动到 Design Surface 上，并将其置于“按钮”小组件下方    。 将“TextView”的 `Text` 属性更改为空字符串，并将其 `Id` 属性设置为 `@+id/TranslatedPhoneword` ：

[![在文本视图上设置属性。](hello-android-quickstart-images/vs/10-textview-properties-w158-sml.png)](hello-android-quickstart-images/vs/10-textview-properties-w158.png#lightbox)

通过按 **CTRL+S** 来保存工作。

## <a name="write-some-code"></a>编写代码

下一步是添加一些代码，以将电话号码从字母数字转换为数字。 通过在“解决方案资源管理器”  窗格中右键单击“Phoneword”  项目，然后选择“添加”>“新建项…”  以向项目添加新文件，如下所示：

[![添加新项](hello-android-quickstart-images/vs/12-add-new-item-w158-sml.png)](hello-android-quickstart-images/vs/12-add-new-item-w158.png#lightbox)

在“添加新项”  对话框中，选择“Visual C#”>“代码”>“代码文件”  ，然后将新代码文件命名为“PhoneTranslator.cs”  ：

[![添加 PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-w158-sml.png)](hello-android-quickstart-images/vs/14-add-class-w158.png#lightbox)

这将创建新的空 C# 类。 在此文件中插入以下代码：

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
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
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

通过单击“文件”>“保存”  （或按 **CTRL+S**）来保存对 **PhoneTranslator.cs** 文件进行的更改，然后关闭该文件。

## <a name="wire-up-the-user-interface"></a>关联用户界面

接下来，通过将支持代码插入到 `MainActivity` 类中来添加代码以关联用户界面。 首先关联“Translate”  按钮。 在 `MainActivity` 类中找到 `OnCreate` 方法。 接下来，在 `OnCreate` 内的 `base.OnCreate(savedInstanceState)` 和 `SetContentView(Resource.Layout.activity_main)` 调用下添加该按钮代码。 首先，修改模板代码，使 `OnCreate` 方法与以下内容相似：

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;

namespace Phoneword
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            // New code will go here
        }
    }
}
```

获取对通过 Android 设计器在布局文件中创建的控件的引用。 在 `OnCreate` 方法中将以下代码添加到 `SetContentView` 调用后面：

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneword);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

添加对用户按“Translate”  按钮进行响应的代码。
将以下代码添加到 `OnCreate` 方法（在上一步中添加的行后）：

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

通过选择“文件”>“全部保存”  （或按 **CTRL-SHIFT-S**）来保存工作，然后通过选择“生成”>“重新生成解决方案”  （或按 **CTRL-SHIFT-B**）来生成应用程序。 

如果发生错误，则完成前面的步骤并更正任何错误，直到应用程序成功生成。 如果收到生成错误（如“资源在当前上下文中不存在”  ），请验证 **MainActivity.cs** 中的命名空间名称是否与项目名称 (`Phoneword`) 匹配，然后完全重新生成解决方案。 如果仍收到生成错误，请验证已安装最新 Visual Studio 更新。

## <a name="set-the-app-name"></a>设置应用名称

你现在应具有可正常工作的应用程序 &ndash; 该设置应用名称了。 展开“值”文件夹（在“资源”文件夹中）并打开文件“strings.xml”    。 将应用名称字符串更改为 `Phone Word`，如下所示：

```xml
<resources>
    <string name="app_name">Phone Word</string>
    <string name="action_settings">Settings</string>
</resources>
```

## <a name="run-the-app"></a>运行应用

通过在 Android 设备或仿真器上运行应用程序来测试该应用程序。
点击“转换”按钮，将“1-855-XAMARIN”转换为电话号码   ：

[![正在运行的应用的屏幕截图](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

若要在 Android 设备上运行应用，请参阅如何[设置设备以进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

::: zone-end
::: zone pivot="macos"

从“应用程序”文件夹或从 Spotlight 启动 Visual Studio for Mac   。

单击“新建项目...”以创建新项目  。

在“为新项目选择模板”  对话框中，单击“Android”>“应用”  ，然后选择“Android 应用”  模板。 单击“下一步”  。

[![选择“Android 应用”模板](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

在“配置 Android 应用”对话框中，将新应用命名为 `Phoneword`，然后单击“下一步”   。

[![配置 Android 应用](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

在“配置新的 Android 应用”对话框中，将“解决方案”和“项目”名称保留设置为 `Phoneword`，然后单击“创建”  以创建项目  。

## <a name="create-a-layout"></a>创建布局

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

创建新项目之后，在“解决方案”  板中展开 **Resources** 文件夹，然后展开 **layout** 文件夹。
双击 **Main.axml** 以在 Android 设计器中打开它。 这是在 Android Designer 中进行查看时屏幕的布局文件：

[![打开 Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

选择设计图面上的“Hello World, Click Me!”  **按钮**，然后按 **Delete** 键以删除它。 

在“工具箱”  （右侧区域）中，向搜索字段中输入 `text` 并将一个“文本(大)”  小部件拖动到设计图面上（中央区域）：

[![添加大文本小组件](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

在设计图面上选择了“文本(大)”  小部件的情况下，可以使用“属性”  板将“文本(大)”  小部件的 `Text` 属性更改为 `Enter a Phoneword:`，如下所示：

[![设置大文本小组件属性](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

接下来，将一个“纯文本”  小组件从“工具箱”  拖动到设计图面上，并将它放置在“文本(大)”  小组件下。 请注意，可以使用搜索字段帮助按名称查找小组件：

[![添加纯文本小组件](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

在设计图面上选择了“纯文本”  小部件的情况下，可以使用“属性”  板将“纯文本”  小部件的 `Id` 属性更改为 `@+id/PhoneNumberText`，并将 `Text` 属性更改为 `1-855-XAMARIN`：

[![设置纯文本小组件属性](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

将一个“按钮”  从“工具箱”  拖动到设计图面上，并将它放置在“纯文本”  小组件下方：

[![添加一个按钮](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

在设计图面上选择了“按钮”  的情况下，可以使用“属性”  板将“按钮”  的 `Id` 属性更改为 `@+id/TranslateButton`，并将 `Text` 属性更改为 `Translate`：

[![配置为“Translate”按钮](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

将一个“TextView”从“工具箱”拖动到 Design Surface 上，并将其置于“按钮”小组件下方    。 选中“TextView”后，将“TextView”的 `id` 属性设置为 `@+id/TranslatedPhoneWord`，并将 `text` 更改为一个空字符串   ：

[![在文本视图上设置属性。](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

通过按 **&#8984; + S** 来保存工作。

## <a name="write-some-code"></a>编写代码

现在添加一些代码，以将电话号码从字母数字转换为数字。 通过在“解决方案”  板中单击“Phoneword”  项目旁的齿轮图标，然后选择“添加”>“新建文件…”  ，向项目添加新文件：

[![向项目添加一个新文件](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为 PhoneTranslator，然后单击“新建”     。 这会为我们创建新的空 C# 类。

在新类中删除所有模板代码，并将其替换为以下代码：

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
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
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

通过选择“文件”>“保存”  （或按 **&#8984; + S**）来保存对 **PhoneTranslator.cs** 文件进行的更改，然后关闭该文件。 通过重新生成解决方案来确保没有编译时错误。

## <a name="wire-up-the-user-interface"></a>关联用户界面

下一步是通过向 `MainActivity` 类中添加支持代码来添加代码以关联用户界面。
在“Solution Pad”中双击“MainActivity.cs”中以打开它   。

首先将事件处理程序添加到“转换”按钮  。 在 `MainActivity` 类中找到 `OnCreate` 方法。 在 `OnCreate` 中的 `base.OnCreate(bundle)` 和 `SetContentView (Resource.Layout.Main)` 调用下添加按钮代码。 删除全部现有按钮处理代码（即引用 `Resource.Id.myButton` 并为其创建单击处理程序的代码），让 `OnCreate` 方法如下所示：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

接下来，需要引用使用 Android 设计器在布局文件中创建的控件。 将以下代码添加到 `OnCreate` 方法中（在 `SetContentView` 调用后面）：

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

通过向 `OnCreate` 方法添加以下代码（在最后一步中添加的行后面），来添加对用户按“Translate”  按钮进行响应的代码：

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

通过选择“生成”>“全部生成”  （或按 **&#8984; + B**），来保存工作并生成应用程序。 如果应用程序进行了编译，则会在 Visual Studio for Mac 的顶部收到成功消息：

如果发生错误，则完成前面的步骤并更正任何错误，直到应用程序成功生成。 如果收到生成错误（如“资源在当前上下文中不存在”  ），请验证 **MainActivity.cs** 中的命名空间名称是否与项目名称 (`Phoneword`) 匹配，然后完全重新生成解决方案。 如果仍收到生成错误，请验证是否已安装最新的 Xamarin.Android 和 Visual Studio for Mac 更新。

## <a name="set-the-label-and-app-icon"></a>设置标签和应用图标

你现在具有可正常工作的应用程序，可以添加完成收尾工作了！ 首先编辑 `MainActivity` 的 `Label`。
`Label` 是 Android 屏幕顶部显示的内容，用于让用户知道他们在应用程序中所处的位置。 在 `MainActivity` 类顶部，将 `Label` 更改为 `Phone Word`，如下所示：

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

现在可以设置应用程序图标了。 默认情况下，Visual Studio for Mac 将为项目提供默认图标。 从解决方案中删除这些文件，然后使用不同的图标替换它们。 在“Solution Pad”中展开“Resources”文件夹   。 请注意，有 5 个前缀为“mipmap-”的文件夹，每个文件夹都包含一个“Icon.png”文件   ：

[![mipmap- 文件夹和 Icon.png 文件](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

需要从项目中删除每个图标文件。 右键单击每个“Icon.png”文件，然后从上下文菜单中选择“删除”   ：

[![删除默认的 Icon.png](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

单击对话框中的“删除”按钮  。

接着，下载并解压缩 [Xamarin 应用图标集](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)。 此 zip 文件包含应用程序的图标。 尽管每个图标看上去都相同，但它们具有不同的分辨率，使它们能在具有不同屏幕密度的不同设备上正确呈现。  必须将此文件集复制到 Xamarin.Android 项目中。 在 Visual Studio for Mac 的“Solution Pad”中，右键单击 mipmap-hdpi 文件夹，然后选择“添加”>“添加文件”    ：

[![添加文件](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

从选择对话框中，导航到已解压缩的 Xamarin AdApp 图标目录并打开 mipmap-hdpi 文件夹  。 选择“Icon.png”，然后单击“打开”   。

在“将文件添加到文件夹”  对话框中，选择“将文件复制到目录中”  ，然后单击“确定”  ：

[![将文件复制到目录对话框](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

为每个 mipmap- 文件夹重复执行这些步骤，直到 mipmap- Xamarin 应用图标文件夹的内容复制到其在 Phoneword 项目中对应的 mipmap- 文件夹为止     。

在所有图标都复制到 Xamarin.Android 项目中后，在“Solution Pad”中右键单击项目，打开“项目选项”对话框   。 选择“生成”>“Android 应用程序”，然后从“应用程序图标”组合框中选择 `@mipmap/icon`  ：

[![设置项目图标](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

## <a name="run-the-app"></a>运行应用

最后，通过在 Android 设备或仿真器上运行应用程序并转换 Phoneword 以测试此应用程序：

[![完成时应用的屏幕截图](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

若要在 Android 设备上运行应用，请参阅如何[设置设备以进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

::: zone-end

祝贺你完成第一个 Xamarin.Android 应用程序！
现在可以仔细分析刚刚学习的工具和技能。 接下来是 [Hello，Android 深入了解](~/android/get-started/hello-android/hello-android-deepdive.md)。

## <a name="related-links"></a>相关链接

- [Xamarin Android 应用图标 (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword（示例）](/samples/xamarin/monodroid-samples/phoneword)