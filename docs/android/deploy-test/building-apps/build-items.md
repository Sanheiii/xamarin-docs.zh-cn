---
title: 生成项
description: 本文档将列出 Xamarin.Android 生成过程支持的所有项组。
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/23/2020
ms.openlocfilehash: 90efe2533f971180124d044ec39ddcf1591b9d36
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455035"
---
# <a name="build-items"></a>生成项

“生成项”控制生成 Xamarin.Android 应用程序或库项目的方式。

## <a name="androidasset"></a>AndroidAsset

支持 [Android 资产](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles)，即将包含在 Java Android 项目的 `assets` 文件夹中的文件。

## <a name="androidaarlibrary"></a>AndroidAarLibrary

`AndroidAarLibrary` 生成操作应用于直接引用 `.aar` 文件。 Xamarin 组件最常使用此生成操作。 也就是说，要添加对 `.aar` 文件的引用，它们是 Google Play 和其他服务正常运行所必需。

包含此生成操作的文件的处理方式类似于库项目中嵌入的资源。 `.aar` 会被提取到中间目录。 然后，任何资产、资源和 `.jar` 文件都会被添加到相应项组中。

## <a name="androidaotprofile"></a>AndroidAotProfile

用于提供 AOT 配置文件，以与配置文件指导的 AOT 一起使用。

## <a name="androidboundlayout"></a>AndroidBoundLayout

指示 `AndroidGenerateLayoutBindings` 属性设置为 `false` 时，系统会为布局文件生成代码隐藏。 在所有其他情况下，它与上面所述的 `AndroidResource` 相同。 此操作仅适用于布局文件****：

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

## <a name="androidenvironment"></a>AndroidEnvironment

生成操作为 `AndroidEnvironment` 的文件用于[在过程启动期间初始化环境变量和系统属性](~/android/deploy-test/environment.md)。
`AndroidEnvironment` 生成操作可能会应用于多个文件，并且它们将以特定顺序进行评估（因此，不要在多个文件中指定相同的环境变量或系统属性）。

## <a name="androidfragmenttype"></a>AndroidFragmentType

指定生成布局绑定代码时，要用于所有 `<fragment>` 布局元素的默认完全限定的类型。 该属性默认为标准的 Android `Android.App.Fragment` 类型。

## <a name="androidjavalibrary"></a>AndroidJavaLibrary

生成操作为 `AndroidJavaLibrary` 的文件是 Java 归档（`.jar` 文件），它将包含在最终的 Android 程序包中。

## <a name="androidjavasource"></a>AndroidJavaSource

生成操作为 `AndroidJavaSource` 的文件是 Java 源代码，将包含在最终的 Android 程序包中。

## <a name="androidlintconfig"></a>AndroidLintConfig

应将“AndroidLintConfig”生成操作与 [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled) 生成属性结合使用
属性中找到的值。 系统将使用此生成操作的文件合并起来并传递给 Android `lint` 工具。 它们应当是包含要启用/禁用哪些测试的信息的 XML 文件。

有关详细信息，请参阅 [lint 文档](https://developer.android.com/studio/write/lint)。

## <a name="androidnativelibrary"></a>AndroidNativeLibrary

通过将生成操作设置为 `AndroidNativeLibrary`，将[本机库](~/android/platform/native-libraries.md)添加到版本中。

请注意，由于 Android 支持多个应用程序二进制接口 (ABI)，因此生成系统必须知道本机库是为哪个 ABI 生成的。 可以通过两种方法完成：

1. 路径“探查”。
2. 使用 `Abi` 项目属性。

通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果将 `lib/armeabi-v7a/libfoo.so` 添加到版本中，则 ABI 将被“探查”为 `armeabi-v7a`。

### <a name="item-attribute-name"></a>项属性名称

**Abi ** &ndash; 指定本机库的 ABI。

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

## <a name="androidresource"></a>AndroidResource

生成操作为 AndroidResource** 的所有文件都将在生成过程中编译为 Android 资源，并可通过 `$(AndroidResgenFile)` 访问。

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

更高级的用户可能希望在不同的配置中使用不同的资源，但使用相同的有效路径。 为此，可以使用多个资源目录并使具有相同相对路径的文件放在这些不同的目录中，以及使用 MSBuild 条件来有条件地在不同配置中包括不同文件。 例如：

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName ** &ndash; 显式指定资源路径。 允许使用 &ldquo;aliasing&rdquo; 文件，以便它们可用作多个不同的资源名称。

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

## <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

生成操作 `AndroidResourceAnalysisConfig` 将某个文件标记为 Xamarin Android Designer 布局诊断工具的严重性级别配置文件。 目前这仅已用于布局编辑器，未用于生成消息。

有关详细信息，请参阅 [Android 资源分析文档](../../user-interface/android-designer/diagnostics.md)。

在 Xamarin.Android 10.2 中新增。

## <a name="content"></a>Content

不支持正常的 `Content` 生成操作（因为我们还未想出如何在没有成本可能昂贵的首次运行步骤的情况下支持它）。

从 Xamarin.Android 5.1 开始，尝试使用 `@(Content)` 生成操作将导致 `XA0101` 警告。

## <a name="linkdescription"></a>LinkDescription

生成操作为 LinkDescription** 的文件用于[控制链接器行为](~/cross-platform/deploy-test/linker.md)。

## <a name="proguardconfiguration"></a>ProguardConfiguration

生成操作为 ProguardConfiguration** 的文件包含用于控制 `proguard` 行为的选项。 有关此生成操作的更多信息，请参阅 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

这些文件将被忽略，除非 [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
MSBuild 属性为 `True`。