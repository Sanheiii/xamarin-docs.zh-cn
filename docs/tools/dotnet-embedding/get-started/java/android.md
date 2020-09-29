---
title: Android 入门
description: 本文档介绍如何开始使用 Android 的 .NET 嵌入。 本文介绍了如何安装 .NET 嵌入、创建 Android 库项目、在 Android Studio 项目中使用生成的输出和其他注意事项。
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 813fb08835f30d0fc24f0c04e1f26b1f4e7f83e0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456544"
---
# <a name="getting-started-with-android"></a>Android 入门

除了 [Java](~/tools/dotnet-embedding/get-started/java/index.md) 入门指南中的要求外，还需要：

- [Xamarin 7.5](https://visualstudio.microsoft.com/xamarin/) 或更高版本
- [Android Studio](https://developer.android.com/studio/index.html) 1.X 与 Java 1。8

作为概述，我们将：

- 创建 c # Android 库项目
- 通过 NuGet 安装 .NET 嵌入
- 在 Android 库程序集上运行 .NET 嵌入
- 在 Android Studio 的 Java 项目中使用生成的 AAR 文件

## <a name="create-an-android-library-project"></a>创建 Android 库项目

打开 Visual Studio for Windows 或 Mac，创建新的 Android 类库项目，将其命名为 " **hello-从-csharp**"，并将其保存到 **~/Projects/hello-from-csharp** 或 **%USERPROFILE%\Projects\hello-from-csharp**。

添加一个名为 " **HelloActivity.cs**" 的新 android 活动，后跟 " **资源/布局"/"main.axml**" 中的 android 布局。

向你的布局添加新的 `TextView` ，并将文本更改为愉快的内容。

布局源应类似于：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

在活动中，请确保正在 `SetContentView` 通过新的布局调用：

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> 别忘了 `[Register]` 属性。 有关详细信息，请参阅 [限制](#current-limitations-on-android)。

生成项目。 生成的程序集将保存在中 `bin/Debug/hello-from-csharp.dll` 。

## <a name="installing-net-embedding-from-nuget"></a>从 NuGet 安装 .NET 嵌入

按照以下 [说明](~/tools/dotnet-embedding/get-started/install/install.md) 安装和配置项目的 .net 嵌入。

应配置的命令调用如下所示：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>在 Android Studio 项目中使用生成的输出

1. 打开 Android Studio，然后创建一个包含 **空活动**的新项目。
2. 右键单击 **应用** 模块，并选择 " **新建" > 模块**。
3. 选择 " **导入"。JAR/。AAR 包**。
4. 使用目录浏览器查找 **~/projects/hello-from-csharp/output/hello_from_csharp aar** ，然后单击 " **完成**"。

![将 AAR 导入 Android Studio](android-images/androidstudioimport.png)

这会将 AAR 文件复制到名为 **hello_from_csharp**的新模块中。

![Android Studio 项目](android-images/androidstudioproject.png)

若要从 **应用**中使用新模块，请右键单击并选择 " **打开模块设置**"。 在 " **依赖项** " 选项卡上，添加新的 **模块依赖项** ，然后选择 " **hello_from_csharp**"。

![Android Studio 依赖关系](android-images/androidstudiodependencies.png)

在活动中，添加一个新 `onResume` 方法，然后使用以下代码启动 c # 活动：

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>程序集压缩 (*重要*) 

还需要对 Android Studio 项目中的 .NET 嵌入进行进一步更改。

打开应用的 **gradle** 文件并添加以下更改：

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin 目前直接从 APK 加载 .NET 程序集，但它需要不压缩程序集。

如果没有此设置，则在启动时应用程序将崩溃，并将类似于下面的内容打印到控制台：

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>运行应用

启动应用时：

![在模拟器中运行的 c # 示例](android-images/hello-from-csharp-android.png)

请注意此处发生了什么：

- 我们有一个 c # 类， `HelloActivity` 它是子类
- 我们有 Android 资源文件
- 在 Android Studio 中，我们使用了它们

若要使此示例正常工作，请在最终 APK 中设置以下所有内容：

- 在应用程序启动时配置 Xamarin。
- **资产/程序集中**包含的 .net 程序集
- 对 c # 活动**AndroidManifest.xml**修改等。
- .NET 库中的 Android 资源和资产
- 任何子类的[Android 可调用包装](~/android/platform/java-integration/android-callable-wrappers.md)器 `Java.Lang.Object`

如果你正在寻找其他演练，请查看以下视频，其中演示了如何将 Charles Petzold 的 [FingerPaint 演示](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) 嵌入到 Android Studio 项目中：

[![适用于 Android 的 Embeddinator-4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 Java 1。8

在撰写本文时，最好的方法是使用此处 Android Studio 3.0 ([下载](https://developer.android.com/studio/index.html)) 。

若要在应用模块的 **gradle** 文件中启用 Java 1.8，请执行以下操作：

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

有关更多详细信息，还可以查看 [Android Studio 测试项目](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) 。 

如果要使用 Android Studio 2.3. x 稳定，则必须启用不推荐使用的插座工具链：

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Android 的当前限制

现在，如果子类化 `Java.Lang.Object` ，Xamarin 将 (Android 可调用) 包装器生成 Java 存根，而不是 .Net 嵌入。 因此，你必须遵循将 c # 导出为 Xamarin 的相同规则。 例如：

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

- `[Register]` 需要映射到所需的 Java 包名称
- `[Export]` 使方法对 Java 可见是必需的

我们可以 `ViewSubclass` 在 Java 中使用，如下所示：

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

阅读有关 [Java 与 Xamarin 集成的](~/android/platform/java-integration/index.md)详细信息。

## <a name="multiple-assemblies"></a>多个程序集

嵌入单个程序集非常简单;但是，您可能会有多个 c # 程序集。 很多时候，你会依赖于 NuGet 包，如 Android 支持库或 Google Play Services，从而进一步使工作复杂化。

这会导致出现问题，因为 .NET 嵌入需要将许多类型的文件包含到最终 AAR 中，例如：

- Android 资产
- Android 资源
- Android 本机库
- Android java 源

您很可能不想将这些文件包含在 Android 支持库中或 Google Play Services 到 AAR 中，而是使用 Android Studio 中 Google 的官方版本。

下面是建议的方法：

- 通过 .NET 嵌入你拥有的任何程序集 (具有) 的源，并希望从 Java 调用
- 通过 .NET 将需要 Android 资产、本机库或资源的任何程序集嵌入到
- 添加 Java 依赖项，例如 Android 支持库或 Android Studio 中的 Google Play Services

因此，你的命令可能是：

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

你应从 NuGet 中排除任何内容，除非你发现它包含 Android 资产、资源等，你将需要在 Android Studio 项目中执行此操作。 你还可以省略不需要从 Java 调用的依赖项，链接器 _应_ 包括你的库中所需的部分。

若要添加 Android Studio 中所需的任何 Java 依赖项， **gradle** 文件可能如下所示：

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>其他阅读材料

- [Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
- [Android 初步研究](~/tools/dotnet-embedding/android/index.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [参与开源项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [错误代码和描述](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>相关链接

- [Android)  (天气示例 ](https://github.com/jamesmontemagno/embeddinator-weather)