---
title: 了解 Android 多屏显示：快速入门
description: 本指南分为两部分，介绍了扩展 Phoneword 应用程序以处理第二个屏幕的情况。 与此同时，介绍了基础的 Android 应用程序构建基块，便于更深入探讨 Android 体系结构。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 91583ff57c7336f6dabf7e83301ebfde7274ddbd
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454775"
---
# <a name="hello-android-multiscreen-quickstart"></a>了解 Android 多屏显示：快速入门

_本指南分为两部分，介绍了扩展 Phoneword 应用程序以处理第二个屏幕的情况。与此同时，介绍了基础的 Android 应用程序构建基块，便于更深入探讨 Android 体系结构。_

在本指南的演练部分中，将向 [Phoneword](/samples/xamarin/monodroid-samples/phoneword) 应用程序添加第二个屏幕，用于跟踪使用此应用转换的号码的相关历史记录。 [最终的应用程序](/samples/xamarin/monodroid-samples/phonewordmultiscreen)将具有第二个屏幕，可显示“已转换”的号码，如右侧屏幕截图所示：

[![示例应用屏幕截图](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

附随的[深入了解](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)将回顾构建的内容，并讨论体系结构、导航和此过程中遇到的其他 Android 新概念。

## <a name="requirements"></a>要求

由于本指南紧接[了解 Android](~/android/get-started/hello-android/index.md) 中的内容，因此需要完成[了解 Android 快速入门](~/android/get-started/hello-android/hello-android-quickstart.md)。
如果想要直接跳到以下演练，可下载完整版的 [Phoneword](/samples/xamarin/monodroid-samples/phoneword)（参见“了解 Android 快速入门”），然后使用该版本进行演练。

## <a name="walkthrough"></a>演练

在本演练中，将向 Phoneword 应用程序添加一个“转换历史记录”屏幕   。

::: zone pivot="windows"

首先在 Visual Studio 中打开 Phoneword 应用程序，然后从“解决方案资源管理器”中编辑 Main.axml 文件    。

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

### <a name="updating-the-layout"></a>更新布局

从“工具箱”中将“按钮”拖动到 Design Surface 上，然后将其置于“TranslatedPhoneWord”TextView 下方。    在“属性面板”窗格中，将按钮“ID”更改为 `@+id/TranslationHistoryButton` 

[![拖动新按钮](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

将按钮的 **Text** 属性设为 `@string/translationHistory`。 Android 设计器将按字面意思解读此属性，但用户需要做些更改，使按钮的文本正确显示：

[![设置转换历史记录按钮文本](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

在**解决方案资源管理器**的“资源”  文件夹下展开“值”  节点，然后双击字符串资源文件 **Strings.xml**：

[![打开 Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

向 **Strings.xml** 文件添加 `translationHistory` 字符串名称和值，然后保存该文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

“转换历史记录”按钮文本应会更新以反映新的字符串值  ：

[![按钮反映新的字符串值](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

在 Design Surface 上选中“转换历史记录”按钮后，在“属性面板”窗格中查找 `enabled` 设置，然后将其值设为 `false` 以禁用此按钮   。 这将导致按钮在设计图面上颜色变暗：

[![禁用转换历史记录按钮](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>创建第二个活动

再创建一个“活动”以支持第二个屏幕。 在“解决方案资源管理器”  中，右键单击“Phoneword”  项目，然后选择“添加”>“新建项…” ：

[![添加新文件](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

在“添加新项”对话框中，选择“Visual C#”>“活动”，然后将活动文件命名为 TranslationHistoryActivity.cs    。

将 TranslationHistoryActivity.cs 中的模板代码替换为以下代码  ：

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

在此类中，将按编程方式创建和填充 `ListActivity`，因此无需新建此活动的布局文件。 有关更详细的信息，请参阅[了解 Android 多屏显示详述](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)。

### <a name="adding-a-list"></a>添加列表

此应用会收集电话号码（用户已在第一个屏幕上转换的），然后传递给第二个屏幕。 电话号码以字符串列表的形式存储。 若要支持列表（和稍后使用的“意向”），请将以下 `using` 指令添加到 MainActivity.cs 顶部  ：

```csharp
using System.Collections.Generic;
using Android.Content;
```

然后请创建可使用电话号码填充的空白列表。
`MainActivity` 类将如下所示：

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

在 `MainActivity` 类中，添加以下代码以注册“转换历史记录”按钮（将此行放在 `translateButton` 声明后）  ：

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

将以下代码添加到 `OnCreate` 方法的末尾，以关联“转换历史记录”  按钮：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

更新“转换”按钮将电话号码添加到 `phoneNumbers` 列表  。 用于 `translateButton` 的 `Click` 处理程序应与以下代码类似：

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

保存并生成应用程序，确保没有错误。

### <a name="running-the-app"></a>运行应用程序

向仿真器或设备部署应用程序。 下面的屏幕截图描述了正在运行的 **Phoneword** 应用程序:

[![示例屏幕快照](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

首先在 Visual Studio for Mac 中打开 Phoneword 项目，然后从“Solution Pad”中编辑 Main.axml 文件。   

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

### <a name="updating-the-layout"></a>更新布局

从“工具箱”中将“按钮”拖动到 Design Surface 上，然后将其置于“TranslatedPhoneWord”TextView 下方。    在“Properties Pad”中，将按钮“ID”更改为 `@+id/TranslationHistoryButton`  ：

[![拖动新按钮](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

将按钮的 **Text** 属性设为 `@string/translationHistory`。 Android 设计器将按字面意思解读此属性，但用户需要做些更改，使按钮的文本正确显示：

[![设置转换历史记录按钮文本](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

在“Solution Pad”中的“Resources”文件夹下展开“values”节点，然后双击字符串资源文件 Strings.xml     ：

[![打开的字符串](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

向 **Strings.xml** 文件添加 `translationHistory` 字符串名称和值，然后保存该文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

“转换历史记录”按钮文本应会更新以反映新的字符串值  ：

[![按钮反映新的字符串值](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)

在 Design Surface 上选中“转换历史记录”按钮后，在“Properties Pad”中打开“行为”选项卡，然后双击“已启用”复选框以禁用此按钮     。 这将导致按钮在设计图面上颜色变暗：

[![禁用转换历史记录按钮](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>创建第二个活动

再创建一个“活动”以支持第二个屏幕。 在**解决方案面板**中，单击 **Phoneword** 项目旁的灰色齿轮图标，然后选择“添加”>“新文件...”  ：

在“新文件”对话框中，选择“Android”>“活动”，将活动命名为 `TranslationHistoryActivity`，然后单击“添加”    。

将 `TranslationHistoryActivity` 中的模板代码替换为以下代码：

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

在此类中，将按编程方式创建和填充 `ListActivity`，因此无需新建该活动的布局文件。 有关更详细的信息，请参阅[了解 Android 多屏显示详述](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)。

### <a name="adding-a-list"></a>添加列表

此应用会收集电话号码（用户已在第一个屏幕上转换的），然后传递给第二个屏幕。 电话号码以字符串列表的形式存储。 若要支持列表（和稍后使用的“意向”），请将以下 `using` 指令添加到 MainActivity.cs 顶部  ：

```csharp
using System.Collections.Generic;
using Android.Content;
```

然后请创建可使用电话号码填充的空白列表。 `MainActivity` 类将如下所示：

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

在 `MainActivity` 类中，添加以下代码以注册“转换历史记录”按钮（将此行放在 `TranslationHistoryButton` 声明后）  ：

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

将以下代码添加到 `OnCreate` 方法的末尾，以关联“转换历史记录”  按钮：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

更新“转换”按钮将电话号码添加到 `phoneNumbers` 列表  。 用于 `TranslateHistoryButton` 的 `Click` 处理程序应与以下代码类似：

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>运行应用程序

向仿真器或设备部署应用程序。 下面的屏幕截图描述了正在运行的 **Phoneword** 应用程序:

[![示例屏幕快照](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

恭喜，你完成了第一个多屏 Xamarin.Android 应用程序！ 现在可仔细分析刚才所学的工具和技能 &ndash; 接下来介绍[了解 Android 多屏显示详述](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)。

## <a name="related-links"></a>相关链接

- [Xamarin 应用图标和启动屏幕 (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword（示例）](/samples/xamarin/monodroid-samples/phoneword)
- [PhonewordMultiscreen（示例）](/samples/xamarin/monodroid-samples/phonewordmultiscreen)