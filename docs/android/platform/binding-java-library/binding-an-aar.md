---
title: 绑定 .AAR
description: 本演练将提供从 Android .AAR 文件创建 Xamarin.Android Java 绑定库的分步说明。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 489408400a7a900bf867a4303188cdc927020f7f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454463"
---
# <a name="binding-an-aar"></a>绑定 .AAR

> [!IMPORTANT]
> 我们当前正在调查 Xamarin 平台上的自定义绑定使用情况。 请参与[此调查](https://www.surveymonkey.com/r/KKBHNLT)，告诉我们将来应该进行哪些开发工作。

_本演练将提供从 Android .AAR 文件创建 Xamarin.Android Java 绑定库的分步说明。_

## <a name="overview"></a>概述

Android 存档 (.AAR) 文件是 Android 库的文件格式。
.AAR 文件是 .ZIP 存档，其中包含以下内容：

- 已编译的 Java 代码
- 资源 ID
- 资源
- 元数据（例如，活动声明、权限）

本指南将逐步介绍为单个 .AAR 文件创建绑定库的基础知识。 有关 Java 库绑定的常规概述（包含基本代码示例），请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。

> [!IMPORTANT]
> 绑定项目只能包含一个 .AAR 文件。 如果 .AAR 依赖于其他 .AAR，则这些依赖项应包含在其自己的绑定项目中，然后进行引用。 请参阅 [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。

## <a name="walkthrough"></a>演练

我们将为 Android Studio 中创建的示例 Android 存档文件创建一个绑定库，即 [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)。 该 .AAR 包含一个具有静态方法的 `TextCounter` 类，这些方法会对字符串中的元音和辅音进行计数。 另外，textanalyzer.aar 包含一个图像资源以帮助显示计数结果。

我们将使用以下步骤从 .AAR 文件创建绑定库：

1. 创建一个新的 Java 绑定库项目。

2. 将一个 .AAR 文件添加到项目中。 绑定项目只能包含一个 .AAR。

3. 为 .AAR 文件设置适当的生成操作。

4. 选择 .AAR 支持的目标框架。

5. 生成绑定库。

创建绑定库后，我们将开发一个小型的 Android 应用，该应用会提示用户输入文本字符串，调用 .AAR 方法来分析文本，检索 .AAR 中的图像，并显示结果以及图像。

该示例应用将访问 textanalyzer.aar 的 `TextCounter` 类：

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

此外，此示例应用还将检索并显示打包在 textanalyzer.aar 中的图像资源：

[![Xamarin monkey 图像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

该图像资源位于 textanalyzer.aar 中的 res/drawable/monkey.png。

### <a name="creating-the-bindings-library"></a>创建绑定库

在开始以下步骤之前，请下载示例 [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 存档文件：

1. 从 Android 绑定库模板开始，创建一个新的绑定库项目。 可以使用 Visual Studio for Mac 或 Visual Studio（下面的屏幕截图显示 Visual Studio，但 Visual Studio for Mac 非常相似）。 将该解决方案命名为 AarBinding：

    [![创建 AarBindings 项目](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. 该模板包括一个 Jars 文件夹，可以在其中将 .AAR 添加到“绑定库”项目中。 右键单击 Jars 文件夹，并选择“添加”>“现有项” ：

    [![添加现有项](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. 导航到之前下载的 textanalyzer.aar 文件，选中它，然后单击“添加”：

    [![添加 textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. 验证 textanalyzer.aar 文件是否已成功添加到项目中：

    [![已添加 textanalyzer.aar 文件](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. 将 textanalyzer.aar 的生成操作设置为 `LibraryProjectZip`。 在 Visual Studio for Mac 中，右键单击“textanalyzer.aar”以设置生成操作。 在 Visual Studio 中，可以在“属性”窗格中设置生成操作：

    [![将 textanalyzer.aar 生成操作设置为 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. 打开项目属性以配置目标框架。 如果 .AAR 使用任何 Android API，请将目标框架设置为 .AAR 预计的 API 级别。 （有关目标框架设置和 Android API 级别的更多概要信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。）

    设置绑定库的目标 API 级别。 在此示例中，可以随意使用最新的平台 API 级别（API 级别 23），因为 textanalyzer 不依赖于 Android API：

    [![将目标级别设置为 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. 生成绑定库。 绑定库项目应成功构建并在以下位置生成输出 .DLL：**AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>使用绑定库

要在 Xamarin.Android 应用中使用此 .DLL，必须首先添加对绑定库的引用。 请使用以下步骤：

1. 我们将在与绑定库相同的“解决方案”中创建此应用，以简化此演练。 （使用绑定库的应用也可以驻留在其他“解决方案”中。）创建一个新的 Xamarin.Android 应用：右键单击“解决方案”，然后选择“添加新项目”。 将新项目命名为 BindingTest：

    [![创建新的 BindingTest 项目](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. 右键单击 BindingTest 项目的“引用”节点并选择“添加引用...”  ：

    [![单击“添加引用”](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. 选择之前创建的 AarBinding 项目，然后单击“确定”：

    [![查看 AAR 绑定项目](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. 打开 BindingTest 项目的“引用”节点，以验证是否存在 AarBinding 引用：

    [![AarBinding 在“引用”下列出](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

如果想查看绑定库项目的内容，可以双击引用以在“对象浏览器”中打开它。 可以查看 `Com.Xamarin.Textcounter` 命名空间的映射内容（从 Java `com.xamarin.textanalyzezr` 包映射），还可以查看 `TextCounter` 类的成员：

[![查看“对象浏览器”](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上面的屏幕截图突出显示了示例应用将调用的两个 `TextAnalyzer` 方法：`NumConsonants`（包装基础 Java `numConsonants` 方法）和 `NumVowels`（包装基础 Java `numVowels` 方法）。

### <a name="accessing-aar-types"></a>访问 .AAR 类型

在向指向绑定库的应用添加引用之后，可以像访问 C# 类型一样访问 .AAR 中的 Java 类型（得益于 C# 包装器）。 C# 应用代码可以调用 `TextAnalyzer` 方法，如以下示例所示：

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上面的示例中，我们在 `TextCounter` 类中调用静态方法。 但是，还可以实例化类和调用实例方法。 例如，如果 .AAR 包装名为 `Employee` 且具有实例方法 `buildFullName` 的类，则可以实例化 `MyClass` 并使用它，如下所示：

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

以下步骤将代码添加到应用，以便应用提示用户输入文本，并使用 `TextCounter` 分析文本，然后显示结果。

用以下 XML 替换 BindingTest 布局 (Main.axml)。 此布局具有用于文本输入的 `EditText` 和两个用于启动元音和辅音计数的按钮：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

将 MainActivity.cs 的内容替换为以下代码。 如本例所示，按钮事件处理程序调用驻留在 .AAR 中的包装好的 `TextCounter` 方法，并使用 toast 显示结果。 注意，绑定库的命名空间的 `using` 语句（在本例中为 `Com.Xamarin.Textcounter`）：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

编译并运行 BindingTest 项目。 该应用将启动并在左侧显示屏幕截图（`EditText` 用一些文本进行初始化，但你可以点击它以对其进行更改）。 点击“COUNT VOWELS”时，toast 会显示元音的数量，如右图所示：

[![运行 BindingTest 的屏幕截图](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

尝试点击“COUNT CONSONANTS”按钮。 另外，可以修改文本行并再次点击这些按钮以测试不同的元音和辅音计数。

### <a name="accessing-aar-resources"></a>访问 .AAR 资源

Xamarin 工具将 .AAR 中的 R 数据合并到应用的 Resource 类中。 因此，可以从布局（和从代码隐藏）访问 .AAR 资源，其访问方式与访问项目“资源”路径中的资源的方式相同。

若要访问图像资源，请将包装在 .AAR 中的图像命名为 Resource.Drawable。 例如，可以使用 `@drawable/image` 来引用 .AAR 文件中的 image.png：

```xml
<ImageView android:src="@drawable/image" ... />
```

还可以访问 .AAR 中的资源布局。 为此，请将打包在 .AAR 中的布局命名为 Resource.Layout。 例如：

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

textanalyzer.aar 示例包含一个位于 res/drawable/monkey.png 的图像文件。 让我们访问此图像资源并在示例应用中使用它：

编辑 BindingTest 布局 (Main.axml)，并将 `ImageView` 添加到 `LinearLayout` 容器的末尾。 此 `ImageView` 显示在 \@drawable/monkey处找到的图像；该图像将从 textanalyzer.aar 的资源部分进行加载：

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

编译并运行 BindingTest 项目。 该应用将启动并在左侧显示屏幕截图 &ndash; 在点击“COUNT CONSONANTS”时，将显示结果，如右图所示：

[![BindingTest 显示辅音计数](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

祝贺你！ 你已成功绑定 Java 库 .AAR！

## <a name="summary"></a>总结

在本演练中，我们为 .AAR 文件创建了一个绑定库，将绑定库添加到最小的测试应用中，然后运行该应用以验证我们的 C# 代码是否可以调用驻留在 .AAR 文件中的 Java 代码。
此外，还扩展了该应用以访问和显示驻留在 .AAR 文件中的图像资源。

## <a name="related-links"></a>相关链接

- [生成 Java 绑定库（视频）](https://university.xamarin.com/classes#10090)
- [绑定 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [AarBinding（示例）](/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573 - 一个项目无法绑定多个 .aar 文件](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)