---
title: 演练：绑定 Android Kotlin 库
description: 本文提供了为现有 Kotlin 库 BubblePicker 创建 Xamarin.Android 绑定的实践演练。 它介绍了一些主题，如编译 Kotlin 库、将其绑定和在 Xamarin.Android 应用程序中使用绑定。
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: d75ec48bf9736297c31e0bb5af5a71de4332c66b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454268"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>演练：绑定 Android Kotlin 库

> [!IMPORTANT]
> 我们当前正在调查 Xamarin 平台上的自定义绑定使用情况。 请参与[此调查](https://www.surveymonkey.com/r/KKBHNLT)，告诉我们将来应该进行哪些开发工作。

借助 Xamarin，移动开发人员能够通过 Visual Studio 和 C# 创建跨平台的本机移动应用。 可以使用现成的 Android 平台 SDK 组件，但在许多情况下，你还会希望使用为该平台编写的第三方 SDK，Xamarin 允许你通过绑定来实现此目的。 若要将第三方 Android 框架合并到 Xamarin.Android 应用程序，需要先为其创建一个 Xamarin.Android 绑定，然后才能在应用程序中使用它。

Android 平台及其本机语言和工具不断发展，其中包括最近引入的 Kotlin 语言，最终会设置为替换 Java。 有许多第三方 SDK，它们已从 Java 迁移到 Kotlin，并且为我们带来了新的挑战。 尽管 Kotlin 绑定过程与 Java 相似，但它需要额外的步骤和配置设置才能成功生成并作为 Xamarin.Android 应用程序的一部分运行。  

本文档的目的是概述对此方案进行寻址的高级方法，并通过一个简单的示例提供了详细分步指南。

## <a name="background"></a>背景

Kotlin 于 2016 年 2 月发布，并于 2017 年作为标准 Java 编译器的替代项定位到 Android Studio。 后来在 2019 年，Google 宣布 Kotlin 编程语言将成为 Android 应用开发人员的首选语言。 高级绑定方法类似于[常规 Java 库的绑定过程](../binding-java-library/index.md)，只需几个重要的 Kotlin 特定步骤即可完成。

## <a name="prerequisites"></a>先决条件

若要完成本演练，你将需要：

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>生成本机库

第一步是使用 Android Studio 生成本机 Kotlin 库。 库通常由第三方开发人员提供，或在 [Google 的 Maven 存储库](https://maven.google.com/web/index.html)和其他远程存储库中提供。 例如，在本教程中，将创建 Bubble Picker Kotlin 库绑定：

![GitHub BubblePicker 演示](walkthrough-images/github-bubblepicker-demo.png)

1. 从 GitHub 下载该库的[源代码](https://github.com/igalata/Bubble-Picker/archive/develop.zip)，并将其解压到本地文件夹 Bubble-Picker。

1. 启动 Android Studio 并选择“打开现有 Android Studio项目”菜单选项，选择 Bubble-Picker 本地文件夹：

    ![Android Studio 开放项目](walkthrough-images/android-studio-open-project.png)

1. 验证 Android Studio 是否是最新的，包括 Gradle。 可以在 Android Studio v3.5.3、Gradle v5.4.1 上成功生成源代码。 有关如何将 Gradle 更新到最新 Gradle 版本的说明，请参阅[此处](https://gradle.org/install/)。

1. 验证是否安装了所需的 Android SDK。 源代码需要 Android SDK v25。 打开“工具”>“SDK 管理器”菜单选项以安装 SDK 组件。

1. 更新和同步位于项目文件夹根目录的主 build.gradle 配置文件：

    - 将 Kotlin 版本设置为 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - 注册默认的 Google Maven 存储库，这样就可以解决支持库依赖项：

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - 配置文件更新后，它将不再同步，Gradle 会显示“立即同步”按钮，按下它，等待同步过程完成：

        ![立即同步 Android Studio Gradle](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Gradle 的依赖项缓存可能已损坏，这有时会在网络连接超时后发生。 重新下载依赖项和同步项目（需要网络）。

        > [!TIP]
        > Gradle 生成过程（守护程序）的状态可能为损坏。 停止所有 Gradle 守护程序可以解决此问题。 停止 Gradle 生成过程（需要重新启动）。 对于损坏的 Gradle 进程，还可以尝试关闭 IDE，然后终止所有 Java 进程。

        > [!TIP]
        > 项目可能使用的是第三方插件，该插件与项目中的其他插件或项目请求的 Gradle 版本不兼容。

1. 打开右侧的 Gradle 菜单，导航到“bubblepicker”>“任务”菜单，通过双击来执行“生成”任务，并等待生成过程完成：

    ![Android Studio Gradle 执行任务](walkthrough-images/android-studio-gradle-execute-task.png)

1. 打开根文件夹文件浏览器并导航到“生成”文件夹：“Bubble-Picker”->“bubblepicker”->“生成”->“输出”->“aar”，将“bubblepicker-release.aar”文件另存为“bubblepicker-v1.0.aar”，稍后将在绑定过程中使用此文件：

    ![Android Studio AAR 输出](walkthrough-images/android-studio-aar-output.png)

AAR 文件是 Android 存档，其中包含 Android 使用此 SDK 运行应用程序所需的已编译 Kotlin 源代码和资产。  

## <a name="prepare-metadata"></a>准备元数据

第二步是准备由 Xamarin.Android 使用的元数据转换文件，以生成相应的 C# 类。 Xamarin.Android 绑定项目将发现给定 Android 存档中的所有本机类和成员，而后使用相应的元数据生成 XML 文件。 然后将手动创建的元数据转换文件将应用于以前生成的基线，以创建用于生成 C# 代码的最终 XML 定义文件。

元数据使用 [XPath](https://www.w3.org/TR/xpath/)  语法，由绑定生成器用来影响绑定程序集的创建。 [Java 绑定元数据](../binding-java-library/customizing-bindings/java-bindings-metadata.md)一文提供了有关转换的详细信息，可应用这些信息：

1. 创建一个空的 Metadata.xml 文件：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. 定义 xml 转换：

- 本机 Kotlin 库有两个你不希望公开给 C# 的依赖项，定义两个转换来完全忽略它们。 重要说明：不会从生成的二进制文件中去除本机成员，只有 C# 类不会生成。 [Java 反编译程序](http://java-decompiler.github.io/)可用于识别依赖项。 运行该工具并打开前面创建的 AAR 文件，结果将显示 Android 存档结构，反映所有依赖项、值、资源、清单和类：  

    ![Java 反编译程序依赖项](walkthrough-images/java-decompiler-dependencies.png)

    使用 XPath 指令定义用于跳过处理这些包的转换：

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- 本机 `BubblePicker` 类有 `getBackgroundColor` 和 `setBackgroundColor` 两个方法，以下转换会将其更改为 C# `BackgroundColor` 属性：

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- 无符号类型 `UInt, UShort, ULong, UByte` 不需要特殊处理。 对于这些类型，Kotlin 会自动更改方法名称和参数类型，这会在生成的代码中反映出来：

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    此代码编译为以下 Java 字节代码：

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    此外，相关类型（如 `UIntArray, UShortArray, ULongArray, UByteArray`）还受 Kotlin 的影响。 方法名称将更改为包含附加后缀，并将参数更改为同一类型的带符号版本元素数组。 在下面的示例中，类型 `UIntArray` 的参数会自动转换为 `int[]`，且方法名称从 `fooUIntArrayMethod` 更改为 `fooUIntArrayMethod--ajY-9A`。 后者是通过 Xamarin.Android 工具发现的，并生成有效的方法名称：

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    此代码编译为以下 Java 字节代码：

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    若要为其指定一个有意义的名称，可将以下元数据添加到 Metadata.xml，这会将该名称重新更新为 Kotlin 代码中最初定义的名称：

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    在 BubblePicker 示例中，没有使用无符号类型的成员，因此不需要进行额外的更改。

- 具有泛型参数的 Kotlin 成员在默认情况下转换为 Java.`Lang.Object` 类型 的参数。 例如，Kotlin 方法有一个通用参数 \<T>：

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    生成 Xamarin.Android 后，该方法将按如下方式公开给 C#：

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Xamarin.Android 绑定不支持 Java 和 Xamarin Kotlin 泛型，因此会创建一个通用 C# 方法来访问通用 API。 作为一种解决方法，你可以创建一个包装器 Kotlin 库，并以强类型方式公开所需的 API，而无需使用泛型。 另外，还可以通过强类型 API 以相同的方式在 C# 端创建帮助程序来解决此问题。

    > [!TIP]
    > 通过转换元数据，可以将任何更改应用到生成的绑定。 [绑定 Java 库](../binding-java-library/index.md)一文详细说明了如何生成和处理元数据。

## <a name="build-a-binding-library"></a>生成绑定库

下一步是使用 Visual Studio 绑定模板创建 Xamarin.Android 绑定项目，添加所需的元数据和本机引用，然后生成项目以生成可使用的库：

1. 打开 Visual Studio for Mac 并创建新的 Xamarin.Android 绑定库项目，为其指定一个名称，在本例中为“testBubblePicker.Binding”并完成向导。 Xamarin.Android 绑定模板位于以下路径： **“Android”>“库”>“绑定库”** ：

    ![Visual Studio 创建绑定](walkthrough-images/visual-studio-create-binding.png)

    在“转换”文件夹中有三个主要转换文件：

    - **Metadata.xml** – 允许对最终 API 进行更改，如更改生成绑定的命名空间。
    - **EnumFields.xml** – 包含 Java int 常量和 C# 枚举之间的映射。
    - **EnumMethods.xml** – 允许更改方法参数，并将返回类型从 Java int 常数更改为 C# 枚举。

    使“EnumFields”和“EnumMethods”文件保持为空，并更新“Metadata.xml”来定义转换。

1. 将现有的“Transformations/Metadata.xml”文件替换为在上一步创建的“Metadata.xml”文件。 在“属性”窗口中，验证“生成操作”文件是否设置为“TransformationFile”：

    ![Visual Studio 元数据](walkthrough-images/visual-studio-metadata.png)

1. 将在步骤 1 中生成的“bubblepicker-v1.0.aar”文件作为本机引用添加到绑定项目。 若要添加本机库引用，请打开查找器，然后导航到带有 Android 存档的文件夹。 将存档拖放到“解决方案资源管理器”的 Jars 文件夹中。 或者，可以使用 Jars 文件夹上的“添加”上下文菜单选项，然后选择“现有文件...”。 出于本演练的目的，请选择将文件复制到目录。 请确保验证“生成操作”是否设置为“LibraryProjectZip”：

    ![Visual Studio 本机引用](walkthrough-images/visual-studio-native-reference.png)

1. 添加对 [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) 包的引用。 此包是 Kotlin 标准库的绑定。 如果不使用此包，则绑定仅在 Kotlin 库不使用任何 Kotlin 特定类型时才起作用，否则，所有这些成员都不会公开给 C#，并且尝试使用绑定的任何应用都将在运行时崩溃。

    > [!TIP]
    > 由于 Xamarin.Android 的限制，绑定工具只能为每个绑定项目添加一个 Android 存档 (AAR)。 如果需要包含多个 AAR 文件，则需要多个 Xamarin.Android 项目，每个 AAR 对应一个项目。 如果本演练是这种情况，则必须为每个存档重复执行此步骤之前的四项操作。 另一种选择是，可以将多个 Android 存档手动合并为单个存档，这样就可以使用单个 Xamarin.Android 绑定项目。

1. 最后一个操作是生成库，并确保不会产生任何编译错误。 如果出现编译错误，可以使用 Metadata.xml 文件来解决和处理这些错误，该文件是之前通过添加 xml 转换元数据创建的，它将添加、删除或重命名库成员。

## <a name="consume-the-binding-library"></a>使用绑定库

最后一步是在 Xamarin.Android 应用程序中使用 Xamarin.Android 绑定库。 创建新的 Xamarin.Android 项目，添加对绑定库的引用并呈现 Bubble Picker UI：

1. 创建 Xamarin.Android 项目。 使用“Android”>“应用”>“Android 应用”作为起点，并在“目标平台”选项中选择“最新和最大平台”，以避免兼容性问题。 以下所有步骤均面向此项目：

    ![Visual Studio 创建应用](walkthrough-images/visual-studio-create-app.png)

1. 添加对绑定项目的项目引用，或添加 DLL 前面创建的引用：

    ![Visual Studio Add Binding Reference.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. 添加对先前添加到 Xamarin.Android 绑定项目的 [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) 包的引用。 它添加了对需要在运行时中处理的任何 Kotlin 特定类型的支持。  如果没有此包，可以编译应用，但将在运行时崩溃：

    ![Visual Studio Add StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. 将 `BubblePicker` 控件添加到 `MainActivity` 的 Android 布局。 打开 testBubblePicker/Resources/layout/content_main.xml 文件，并将 BubblePicker 控件节点附加为根 RelativeLayout 控件的最后一个元素：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. 更新应用程序的源代码，并将初始化逻辑添加到 `MainActivity`，这会激活 Bubble Picker SDK：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter` 和 `BubblePickerListener` 是从头开始创建的两个类，用于处理气泡数据和控件交互：

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. 运行应用程序，应呈现 Bubble Picker UI：

    ![BubblePicker 演示](walkthrough-images/bubble-picker-demo.png)

    该示例需要附加代码来呈现元素样式并处理交互，但已成功创建并激活了 `BubblePicker` 控件。

祝贺你！ 你已成功创建了 Xamarin.Android 应用和使用 Kotlin 库的绑定库。  

现在，应具有通过 Xamarin.Android 绑定库使用本机 Kotlin 库的基本 Xamarin.Android 应用程序。 本演练特意使用一个基本示例来更好地强调所引入的主要概念。 在实际场景中，可能需要公开更多的 API，并向它们应用元数据转换。

## <a name="related-links"></a>相关链接

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安装](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 库](https://github.com/igalata/Bubble-Picker)
- [绑定 Java 库](../binding-java-library/index.md)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 绑定元数据](../binding-java-library/customizing-bindings/java-bindings-metadata.md)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [示例项目存储库](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)