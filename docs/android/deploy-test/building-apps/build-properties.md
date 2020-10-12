---
title: 生成属性
description: 本文档将列出 Xamarin.Android 生成过程支持的所有属性。
ms.prod: xamarin
ms.assetid: FC0DBC08-EBCB-4D2D-AB3F-76B54E635C22
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/21/2020
ms.openlocfilehash: aeb0cca9ead1a0f0a3f5b1dec88b2470289cd589
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454931"
---
# <a name="build-properties"></a>生成属性

MSBuild 属性控制[目标](~/android/deploy-test/building-apps/build-targets.md)的行为。
它们是在项目文件中指定的，例如 [MSBuild PropertyGroup](/visualstudio/msbuild/propertygroup-element-msbuild) 中的 MyApp.csproj。

## <a name="adbtarget"></a>AdbTarget

`$(AdbTarget)` 属性指定 Android 包可能要安装到或从中删除的 Android 目标设备。
此属性的值与 [`adb` 目标设备选项](https://developer.android.com/tools/help/adb.html#issuingcommands)相同。

## <a name="aftergenerateandroidmanifest"></a>AfterGenerateAndroidManifest

此属性中列出的 MSBuild 目标将在内部 `_GenerateJavaStubs` 目标（及在 `AndroidManifest.xml` 中生成 `$(IntermediateOutputPath)` 的位置）后直接运行。 如果想要对生成的 `AndroidManifest.xml` 文件进行任何修改，则可以使用此扩展点完成。

Added in Xamarin.Android 9.4。

## <a name="androidaapt2compileextraargs"></a>AndroidAapt2CompileExtraArgs

指定处理 Android 资产和资源时传递给 aapt2 compile 命令的附加命令行选项。

在 Xamarin.Android 9.1 中新增。

## <a name="androidaapt2linkextraargs"></a>AndroidAapt2LinkExtraArgs

指定处理 Android 资产和资源时传递给 aapt2 link 命令的附加命令行选项。

在 Xamarin.Android 9.1 中新增。

## <a name="androidaotcustomprofilepath"></a>AndroidAotCustomProfilePath

`aprofutil` 应创建的用于保存探查器数据的文件。

## <a name="androidaotprofiles"></a>AndroidAotProfiles

允许开发人员通过命令行添加 AOT 配置文件的字符串属性。 它是以分号或逗号分隔的绝对路径列表。
在 Xamarin.Android 10.1 中新增。

## <a name="androidaotprofilerport"></a>AndroidAotProfilerPort

获取分析数据时 `aprofutil` 应连接到的端口。

## <a name="androidapkdigestalgorithm"></a>AndroidApkDigestAlgorithm

此字符串值指定将用于 `jarsigner -digestalg` 的摘要算法。

默认值为 `SHA-256`。 在 Xamarin.Android 10.0 以及更早的版本中，默认值为 `SHA1`。

Added in Xamarin.Android 9.4。

## <a name="androidapksigneradditionalarguments"></a>AndroidApkSignerAdditionalArguments

此字符串属性允许开发人员向 `apksigner` 工具提供其他参数。

在 Xamarin.Android 8.2 中新增。

## <a name="androidapksigningalgorithm"></a>AndroidApkSigningAlgorithm

此字符串值指定将用于 `jarsigner -sigalg` 的签名算法。

默认值为 `SHA256withRSA`。 在 Xamarin.Android 10.0 以及更早的版本中，默认值为 `md5withRSA`。

在 Xamarin.Android 8.2 中新增。

## <a name="androidapplication"></a>AndroidApplication

此布尔值指示项目是用于 Android 应用程序 (`True`) 还是用于 Android 库项目（`False` 或不存在）。

在 Android 包中，可能只存在一个具有 `<AndroidApplication>True</AndroidApplication>` 的项目。 （遗憾的是，这点尚未得到验证，这可能会导致与 Android 资源有关的微妙和奇怪的错误。）

## <a name="androidapplicationjavaclass"></a>AndroidApplicationJavaClass

完整 Java 类名称，用于在类继承自 [Android.App.Application](xref:Android.App.Application) 时替代 `android.app.Application`。

该属性通常由其他属性设置，例如 `$(AndroidEnableMultiDex)` MSBuild 属性  。

已在 Xamarin.Android 6.1 中添加。

## <a name="androidbinutilspath"></a>AndroidBinUtilsPath

包含 Android [binutils][binutils]（如本机链接器 `ld` 和本机汇编程序 `as`）的目录路径。 这些工具是 Android NDK 的组成部分，Xamarin.Android 安装中也包含它们。

默认值为 `$(MonoAndroidBinDirectory)\ndk\`。

Added in Xamarin.Android 10.0。

[binutils]: https://android.googlesource.com/toolchain/binutils/

## <a name="androidboundexceptiontype"></a>AndroidBoundExceptionType

字符串值，当 Xamarin.Android 提供的类型根据 Java 类型实现 .NET 类型或接口时（例如 `Android.Runtime.InputStreamInvoker` 和 `System.IO.Stream`，或 `Android.Runtime.JavaDictionary` 和 `System.Collections.IDictionary`），此值会指定如何传播异常。

- `Java`：原样传播原始 Java 异常。

  这表示，如果 `InputStreamInvoker` 未正确实现 `System.IO.Stream` API，可能会从 `Stream.Read()` 引发 `Java.IO.IOException`，而不是从 `System.IO.IOException` 引发。

  在 Xamarin.Android 11.1 之前的所有版本中，异常传播行为都是如此。

  这是 Xamarin.Android 11.1 中的默认值。

- `System`：捕获原始 Java 异常类型，并将其包装到适当的 .NET 异常类型中。

  这表示，如果 `InputStreamInvoker` 正确实现 `System.IO.Stream`，`Stream.Read()` 将不会引发  `Java.IO.IOException` 实例。  （它可能会改为引发 `System.IO.IOException`，并将 `Java.IO.IOException` 作为 `Exception.InnerException` 值。）

  这将成为 .NET 6.0 中的默认值。

在 Xamarin.Android 10.2 中新增。

## <a name="androidbuildapplicationpackage"></a>AndroidBuildApplicationPackage

此布尔值指示是否创建包 (.apk) 并为其签名。 将此值设置为 `True` 相当于使用 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#install)
生成目标。

在 Xamarin.Android 7.1 之后添加了对该属性的支持。

该属性默认为 `False`。

## <a name="androidbundleconfigurationfile"></a>AndroidBundleConfigurationFile

指定一个文件名，以在生成 Android 应用程序捆绑包时用作 `bundletool` 的[配置文件][bundle-config-format]。 此文件从某些方面控制捆绑包如何生成 APK，例如从哪些维度拆分捆绑包来生成 APK。 请注意，Xamarin.Android 自动配置其中的部分设置，包括不要压缩的文件扩展名列表。

仅当 [`$(AndroidPackageFormat)`](#androidpackageformat) 设置为 `aab` 时，此属性才是相关的。

在 Xamarin.Android 10.3 中新增。

[bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

## <a name="androidclassparser"></a>AndroidClassParser

此字符串属性用于控制如何分析 `.jar` 文件。 可能的值包括：

- **** class-parse：使用 `class-parse.exe` 直接解析 Java 字节码，无需 JVM 的帮助。 此值处于试验阶段。

- **** jar2xml：使用 `jar2xml.jar` 以使用 Java 反射从 `.jar` 文件中提取类型和成员。

`class-parse` 优于 `jar2xml` 的优势是：

- `class-parse` 能够从包含调试符号的 Java 字节码（用 `javac -g` 编译的字节码）提取参数名称。

- `class-parse` 不会“跳过”从无法解析的类型继承或者包含无法解析类型成员的类。

“实验”。 已在 Xamarin.Android 6.0 中添加。

默认值为 `jar2xml`。

`jar2xml` 支持已过时，.NET 6 中将删除 `jar2xml` 支持。

## <a name="androidcodegentarget"></a>AndroidCodegenTarget

此字符串属性控制代码生成目标 ABI。
可能的值包括：

- XamarinAndroid：自 Mono for Android 1.0 开始使用存在的 JNI 绑定 API。 使用 Xamarin.Android 5.0 或更高版本生成的绑定程序集只能在 Xamarin.Android 5.0 或更高版本（API/ABI 附加程序）上运行，但源与先前的产品版本兼容**。

- **** XAJavaInterop1：使用 Java.Interop 进行 JNI 调用。 只能通过 Xamarin.Android 6.1 或更高版本构建和执行使用 `XAJavaInterop1` 的绑定程序集。 Xamarin.Android 6.1 和更高版本会将 `Mono.Android.dll` 与此值绑定。

`XAJavaInterop1` 的好处包括：

- 程序集较小。

- 只要继承层次结构中的所有其他绑定类型均使用 `XAJavaInterop1` 或更高版本构建，即可使用 `base` 方法调用的 `jmethodID` 缓存。

- 用于托管子类的 Java Callable Wrapper 构造函数的 `jmethodID` 缓存。

默认值为 `XAJavaInterop1`。

## <a name="androidcreatepackageperabi"></a>AndroidCreatePackagePerAbi

此布尔属性决定是否应按照 [`$(AndroidSupportedAbis)`](#androidsupportedabis) 中指定的每个 ABI 创建一组文件，而不是将对所有 ABI 的支持包含在单个 `.apk` 中。

另请参阅[生成 ABI 特定的 APK](~/android/deploy-test/building-apps/abi-specific-apks.md) 指南。

## <a name="androiddebugkeyalgorithm"></a>AndroidDebugKeyAlgorithm

指定要用于 `debug.keystore` 的默认算法。 默认为 `RSA`。

## <a name="androiddebugkeyvalidity"></a>AndroidDebugKeyValidity

指定要用于 `debug.keystore` 的默认有效期。 默认值为 `10950`、`30 * 365` 或 `30 years`。

## <a name="androiddebugstoretype"></a>AndroidDebugStoreType

指定用于 `debug.keystore` 的密钥存储文件格式。 默认为 `pkcs12`。

在 Xamarin.Android 10.2 中新增。

## <a name="androiddextool"></a>AndroidDexTool

枚举样式的属性，有效值为 `dx` 或 `d8`。 指示在 Xamarin.Android 生成过程中使用的 Android [dex][dex] 编译器。
当前默认为 `dx`。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

[dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidenabledesugar"></a>AndroidEnableDesugar

此布尔属性确定是否启用了 `desugar`。 Android 当前不支持所有 Java 8 功能；默认工具链通过对 `javac` 编译器的输出执行称为 `desugar` 的字节码转换，实现新的语言功能。 如果使用 `AndroidDexTool=dx`，默认为 `False`；如果使用 [`$(AndroidDexTool)`](#androiddextool)=`d8`，默认为 `True`。

## <a name="androidenablegoogleplaystorechecks"></a>AndroidEnableGooglePlayStoreChecks

此布尔属性允许开发人员禁用以下 Google Play 商店检查：XA1004、XA1005 和 XA1006。 对于目标不是 Google Play 商店并且不想运行这些检查的开发人员来说，这非常有用。

Added in Xamarin.Android 9.4。

## <a name="androidenablemultidex"></a>AndroidEnableMultiDex

此布尔属性确定是否将在最终的 `.apk` 中使用 multi-dex 支持。

Xamarin.Android 5.1 中增加了对该属性的支持。

该属性默认为 `False`。

## <a name="androidenablepreloadassemblies"></a>AndroidEnablePreloadAssemblies

此布尔属性控制在进程启动时是否加载应用程序包内绑定的所有托管程序集。

如果设置为 `True`，在进程启动时，将在调用任何应用程序代码前加载应用程序包内绑定的所有程序集。
这与 Xamarin.Android 在 Xamarin.Android 9.2 之前的版本中执行的操作一致。

如果设置为 `False`，将仅根据需要加载程序集。
这样可以加快应用程序的启动速度，它更符合桌面 .NET 语义。  若要查看节省的时间，请将 `debug.mono.log` 系统属性设置为包含 `timing`，然后在 `Finished loading assemblies: preloaded` 中查找 `adb logcat` 消息。

如果使用依存关系注入的应用程序或库不需要应用程序捆绑包内的所有程序集，但它们反而要求 `AppDomain.CurrentDomain.GetAssemblies()` 返回所有的程序集，在这种情况下它们可能要求此属性为 `True` 。

默认情况下，此值设置为 `True`。

在 Xamarin.Android 9.2 中新增。

## <a name="androidenableprofiledaot"></a>AndroidEnableProfiledAot

此布尔属性确定是否在预先编译时使用 AOT 配置文件。

配置文件在 [`@(AndroidAotProfile)`](~/android/deploy-test/building-apps/build-items.md#androidaotprofile)
项组中列出。 此 ItemGroup 包含默认配置文件。 通过删除现有的配置文件并添加你自己的 AOT 配置文件可以进行替代。

在 Xamarin.Android 9.4 中增加了对此属性的支持。

该属性默认为 `False`。

## <a name="androidenablesgenconcurrent"></a>AndroidEnableSGenConcurrent

此布尔属性确定是否使用 Mono 的[并发垃圾收集器](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen)。

在 Xamarin.Android 7.2 中增加了对该属性的支持。

该属性默认为 `False`。

## <a name="androiderroroncustomjavaobject"></a>AndroidErrorOnCustomJavaObject

此布尔属性确定类型能否实现 `Android.Runtime.IJavaObject`
，而无需同时继承自 `Java.Lang.Object` 或 `Java.Lang.Throwable`：

```csharp
class BadType : IJavaObject {
    public IntPtr Handle {
        get {return IntPtr.Zero;}
    }

    public void Dispose()
    {
    }
}
```

若为 True，这些类型生成 XA4212 错误；否则，生成 XA4212 警告。

Xamarin.Android 8.1 现已开始支持此属性。

该属性默认为 `True`。

## <a name="androidexplicitcrunch"></a>AndroidExplicitCrunch

Xamarin.Android 11.0 中不再支持。

## <a name="androidextraaotoptions"></a>AndroidExtraAotOptions

字符串属性，对将 [`$(AndroidEnableProfiledAot)`](#androidenableprofiledaot) 或 [`$(AotAssemblies)`](#aotassemblies) 设置为 `true` 的项目执行 `Aot` 任务时，可通过它将其他选项传递到 Mono 编译器。
调用 Mono 交叉编译器时，此属性的字符串值会添加到响应文件。

通常情况下，此属性应保留为空，但在某些特殊场景中，它可以提供有用的灵活性。

请注意，此属性不同于相关的 `$(AndroidAotAdditionalArguments)` 属性。 后面的属性将逗号分隔的参数置于 Mono 编译器的 `--aot` 选项。 相反，`$(AndroidExtraAotOptions)` 将完整独立的逗号分隔的选项（如 `--verbose` 或 `--debug`）传递到此编译器。

在 Xamarin.Android 10.2 中新增。

## <a name="androidfastdeploymenttype"></a>AndroidFastDeploymentType

`:`（冒号）分隔的值列表，[`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) MSBuild 属性为 `False` 时可用于控制部署到目标设备上的[快速部署目录](~/android/deploy-test/building-apps/build-process.md#Fast_Deployment)的类型。 如果资源是快速部署的，则  不会嵌入到生成的 `.apk` 中，这样做可以加快部署时间。 （部署的速度越快，`.apk` 需要重建的频率越低，安装过程可能会更快。）有效值包括：

- `Assemblies`：部署应用程序集。

- `Dexes`：部署 `.dex` 文件、Android 资源和 Android 资产。 **此值仅  可以在运行 Android 4.4 或更高版本 (API-19) 的设备上使用。**

默认值为 `Assemblies`。

 “实验”。 已在 Xamarin.Android 6.1 中添加。

## <a name="androidgeneratejnimarshalmethods"></a>AndroidGenerateJniMarshalMethods

此布尔属性允许在生成过程中生成 JNI 封送方法。 大大减少了在绑定帮助程序代码中对 `System.Reflection` 的使用。

默认设置为 False。 如果开发者希望使用新的 JNI 封送方法功能，他们可以进行如下设置：

```xml
<AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
```

在 `.csproj` 中进行设置。 或者通过如下设置，在命令行上提供该属性：

```shell
/p:AndroidGenerateJniMarshalMethods=True
```

 “实验”。 在 Xamarin.Android 9.2 中新增。
默认值为 False。

## <a name="androidgeneratejnimarshalmethodsadditionalarguments"></a>AndroidGenerateJniMarshalMethodsAdditionalArguments

此字符串属性可用于向 `jnimarshalmethod-gen.exe` 调用添加额外的参数。  对调试非常有用，以便可使用 `-v`、`-d` 或 `--keeptemp` 等选项。

默认值为空字符串。 可以在 `.csproj` 文件中或命令行上设置。 例如：

```xml
<AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
```

或：

```shell
/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
```

在 Xamarin.Android 9.2 中新增。

## <a name="androidgeneratelayoutbindings"></a>AndroidGenerateLayoutBindings

如果设置为 `true`，则允许生成[布局代码隐藏](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md)，如果设置为 `false`，则完全禁止。 默认值为 `false`。

## <a name="androidhttpclienthandlertype"></a>AndroidHttpClientHandlerType

控制 `System.Net.Http.HttpClient` 默认构造函数使用的默认 `System.Net.Http.HttpMessageHandler` 实现。 值是 `HttpMessageHandler` 子类的程序集限定类型名称，适用于 [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype#System_Type_GetType_System_String_)。
此属性最常见的值为：

- `Xamarin.Android.Net.AndroidClientHandler`：使用 Android Java API 执行网络请求。 这样，如果基础 Android 版本支持 TLS 1.2，就可以访问 TLS 1.2 URL。 只有 Android 5.0 及更高版本通过 Java 可靠提供 TLS 1.2 支持。

  这对应于 Visual Studio 属性页中的“Android”选项，以及 Visual Studio for Mac 属性页中的“AndroidClientHandler”选项   。

  当 Visual Studio 中“最低 Android 版本”配置为“Android 5.0 (Lollipop)”或更高版本，或者当 Visual Studio for Mac 中“目标平台”设置为“最新和最高版本”时，新建项目向导为新项目选择此选项     。

- 取消设置/空字符串：这等效于 `System.Net.Http.HttpClientHandler, System.Net.Http`

  这对应于 Visual Studio 属性页中的“默认”选项  。

  当 Visual Studio 中“最低 Android 版本”配置为“Android 4.4.87”或更低版本，或者当 Visual Studio for Mac 中“目标平台”设置为“新式开发”或“最大兼容性”时，新建项目向导为新项目选择此选项      。

- `System.Net.Http.HttpClientHandler, System.Net.Http`：使用托管 `HttpMessageHandler`。

  这对应于 Visual Studio 属性页中的“托管”选项  。

> [!NOTE]
> 如果需要在低于 Android 5.0 的版本上具备 TLS 1.2 支持，或者 TLS 1.2 支持需要与 `System.Net.WebClient` 及相关 API 一起使用，则应使用 [`$(AndroidTlsProvider)`](#androidtlsprovider)。

> [!NOTE]
> 通过设置 [`XA_HTTP_CLIENT_HANDLER_TYPE` 环境变量](~/android/deploy-test/environment.md)可使用对此属性的支持。
> 在生成操作为 [`@(AndroidEnvironment)`](~/android/deploy-test/building-apps/build-items.md#androidenvironment) 的文件中，发现的 `$XA_HTTP_CLIENT_HANDLER_TYPE` 值
> 的优先级更高。

已在 Xamarin.Android 6.1 中添加。

## <a name="androidkeystore"></a>AndroidKeyStore

此布尔值指示是否应使用自定义签名信息。 默认值是 `False`，这意味着将使用默认的调试签名密钥来对包进行签名。

## <a name="androidlaunchactivity"></a>AndroidLaunchActivity

要启动的 Android 活动。

## <a name="androidlinkmode"></a>AndroidLinkMode

指定应对 Android 包中包含的程序集执行的[链接](~/android/deploy-test/linker.md)的类型。 仅在 Android 应用程序项目中使用。 默认值是 SdkOnly  。 有效值为：

- “无”  ：将不尝试任何链接。

- SdkOnly  ：将仅在基类库上（不在用户的程序集上）执行链接。

- “完整”  ：将在基类库和用户程序集上执行链接。

  > [!NOTE]
  > 使用“完整”这一 `AndroidLinkMode` 值常会导致应用损坏，尤其是使用放射时  。 除非你  真正知道在做什么，否则请避免。

```xml
<AndroidLinkMode>SdkOnly</AndroidLinkMode>
```

## <a name="androidlinkskip"></a>AndroidLinkSkip

指定不应链接的程序集名称的列表，以分号分隔 (`;`)，且没有文件扩展名。 仅在 Android 应用程序项目中使用。

```xml
<AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
```

## <a name="androidlinktool"></a>AndroidLinkTool

枚举样式的属性，有效值为 `proguard` 或 `r8`。 指示用于 Java 代码的代码压缩器。 当前默认为空字符串；如果 `$(AndroidEnableProguard)` 是 `True`，则为 `proguard`。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidlintenabled"></a>AndroidLintEnabled

此布尔属性使开发人员能够在打包过程中运行 Android `lint` 工具。

`$(AndroidLintEnabled)` 为 True 时，使用以下属性：

- [`$(AndroidLintEnabledIssues)`](#androidlintenabledissues):
- [`$(AndroidLintDisabledIssues)`](#androidlintdisabledissues):
- [`$(AndroidLintCheckIssues)`](#androidlintcheckissues):

也可以使用以下生成操作：

- [`@(AndroidLintConfig)`](~/android/deploy-test/building-apps/build-items.md#androidlintconfig):

请参阅 [Lint 帮助](https://developer.android.com/studio/write/lint)，了解有关 Android `lint` 工具的详细信息。

## <a name="androidlintenabledissues"></a>AndroidLintEnabledIssues

只有 [`$(AndroidLintEnabled)`](#androidlintenabled) 为 True 时，才使用此属性。

要启用的 lint 问题的列表（以逗号分隔）。

## <a name="androidlintdisabledissues"></a>AndroidLintDisabledIssues

只有 [`$(AndroidLintEnabled)`](#androidlintenabled) 为 True 时，才使用此属性。

要禁用的 lint 问题的列表（以逗号分隔）。

## <a name="androidlintcheckissues"></a>AndroidLintCheckIssues

只有 [`$(AndroidLintEnabled)`](#androidlintenabled) 为 True 时，才使用此属性。

要检查的 lint 问题的列表（以逗号分隔）。

请注意：只检查这些问题。

## <a name="androidmanagedsymbols"></a>AndroidManagedSymbols

此布尔属性控制是否生成序列点，以便可以从 `Release` 堆栈跟踪中提取文件名和行号信息。

已在 Xamarin.Android 6.1 中添加。

## <a name="androidmanifest"></a>AndroidManifest

指定用于应用 [`AndroidManifest.xml`](~/android/platform/android-manifest.md) 的模板的文件名。
在生成期间，将合并任何其他必要的值以生成实际的 `AndroidManifest.xml`。
`$(AndroidManifest)` 必须在 `/manifest/@package` 属性中包含程序包名称。

## <a name="androidmanifestmerger"></a>AndroidManifestMerger

指定用于合并 AndroidManifest.xml 文件的实现。 这是一个枚举样式的属性，其中 `legacy` 选择原始 C# 实现，`manifestmerger.jar` 选择 Google 的 Java 实现。

默认值当前为 `legacy`。 在未来版本中，此值将更改为 `manifestmerger.jar`，以使行为与 Android Studio 一致。

Google 的合并器启用了 `xmlns:tools="http://schemas.android.com/tools"` 支持，如 [Android 文档][manifest-merger]中所述。

在 Xamarin.Android 10.2 中引入

[manifest-merger]: https://developer.android.com/studio/build/manifest-merge

## <a name="androidmanifestplaceholders"></a>AndroidManifestPlaceholders

AndroidManifest.xml 的键值替换对列表，采用分号分隔，其中每个对的格式为 `key=value`。

例如，`assemblyName=$(AssemblyName)` 的属性值定义一个 `${assemblyName}` 占位符，随后该占位符可以出现在 AndroidManifest.xml 中：

```xml
<application android:label="${assemblyName}"
```

这提供了一种将生成过程中的变量插入 AndroidManifest.xml 文件的方法。

## <a name="androidmultidexclasslistextraargs"></a>AndroidMultiDexClassListExtraArgs

此字符串属性使开发人员能够在生成 `multidex.keep` 文件时，向 `com.android.multidex.MainDexListBuilder` 传递额外的参数。

具体事例：是否在 `dx` 编译期间发生以下错误。

```text
com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
```

如果发生此错误，可以向 `.csproj` 添加以下内容。

```xml
<DxExtraArguments>--force-jumbo </DxExtraArguments>
<AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
```

这样，`dx` 步骤才能成功。

在 Xamarin.Android 8.3 中新增。

## <a name="androidpackageformat"></a>AndroidPackageFormat

枚举样式的属性，有效值为 `apk` 或 `aab`。 该属性指示你希望将 Android 应用程序打包为 [APK 文件][apk]还是 [Android 应用程序包][bundle]。 应用程序包是一种新的格式，适用于要在 Google Play 上提交的 `Release` 版本。 该值当前默认为 `apk`。

当 `$(AndroidPackageFormat)` 设置为 `aab` 时，系统将设置 Android 应用程序包所必需的其他 MSBuild 属性：

- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2) 为 `True`。
- [`$(AndroidUseApkSigner)`](#androiduseapksigner) 为 `False`。
- [`$(AndroidCreatePackagePerAbi)`](#androidcreatepackageperabi) 为 `False`。

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

## <a name="androidpackagenamingpolicy"></a>AndroidPackageNamingPolicy

枚举样式的属性，用于指定生成的 Java 源代码的 Java 包名称。

在 Xamarin.Android 10.2 和更高版本中，仅支持值 `LowercaseCrc64`。

在 Xamarin.Android 10.1 中，过渡值 `LowercaseMD5` 曾可用于重新切换到原始 Java 包名称样式，Xamarin.Android 10.0 和更早版本中也是如此。 Xamarin.Android 10.2 删除了此选项，以便更好地与执行 FIPS 合规性的生成环境兼容。

在 Xamarin.Android 10.1 中新增。

## <a name="androidr8ignorewarnings"></a>AndroidR8IgnoreWarnings

指定 `r8` 的 `-ignorewarnings` proguard 规则。 这允许 `r8` 继续进行 dex 编译（即使遇到特定的警告）。 默认设置为 `True`但可以设置为 `False`，以便强制执行更加严格的行为。 有关详细信息，请参阅 [ProGuard 手册](https://www.guardsquare.com/products/proguard/manual/usage)。

在 Xamarin.Android 10.3 中新增。

## <a name="androidr8jarpath"></a>AndroidR8JarPath

指向 `r8.jar` 的路径，可与 r8 dex 编译器和压缩器结合使用。 默认为 Xamarin.Android 安装中的路径。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

## <a name="androidresgenextraargs"></a>AndroidResgenExtraArgs

指定处理 Android 资产和资源时传递给 aapt 命令的附加命令行选项。

## <a name="androidresgenfile"></a>AndroidResgenFile

指定要生成的资源文件的名称。 默认模板将其设置为 `Resource.designer.cs`。

## <a name="androidsdkbuildtoolsversion"></a>AndroidSdkBuildToolsVersion

Android SDK 生成工具包提供 aapt 和 zipalign 工具等。 可以同时安装多个不同版本的生成工具包。 若要选择用于打包的生成工具包，请检查是否有“首选”生成工具版本。如果有，请使用它；如果没有**“首选”版本，请使用版本最高的已安装生成工具包。

`$(AndroidSdkBuildToolsVersion)` MSBuild 属性包含首选的生成工具版本。 如果（例如）已知上一 aapt 版本可用，而此时最新的 aapt 发生崩溃，则 Xamarin.Android 生成系统会在 `Xamarin.Android.Common.targets` 中提供默认值，并且可在项目文件中替代该默认值，选择备用的生成工具版本。

## <a name="androidsigningkeyalias"></a>AndroidSigningKeyAlias

指定密钥存储中密钥的别名。 这是创建密钥存储时使用的 keytool -alias**** 值。

## <a name="androidsigningkeypass"></a>AndroidSigningKeyPass

指定密钥存储文件中密钥的密码。 这是在 `keytool` 要求“输入 $(AndroidSigningKeyAlias) 的密匙密码”**** 时输入的值。

在 Xamarin.Android 10.0 和更早的版本中，此属性仅支持纯文本密码。

在 Xamarin.Android 10.1 和更高版本中，此属性还支持 `env:` 和 `file:` 前缀，它们可用于指定包含密码的环境变量或文件。 通过这些选项，可以防止密码显示在生成日志中。

例如，使用名称为 AndroidSigningPassword 的环境变量：

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
</PropertyGroup>
```

使用位于 `C:\Users\user1\AndroidSigningPassword.txt` 的文件：

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
</PropertyGroup>
```

> [!NOTE]
> [`$(AndroidPackageFormat)`](#androidpackageformat) 设置为 `aab` 时，不支持 `env:` 前缀。

## <a name="androidsigningkeystore"></a>AndroidSigningKeyStore

指定由 `keytool` 创建的密钥存储文件的文件名。 这对应于提供给 keytool -keystore**** 选项的值。

## <a name="androidsigningstorepass"></a>AndroidSigningStorePass

指定 [`$(AndroidSigningKeyStore)`](#androidsigningkeystore) 的密码。
这是在创建密钥存储文件并要求“输入密钥存储密码:”**** 时为 `keytool` 提供的值。

在 Xamarin.Android 10.0 和更早的版本中，此属性仅支持纯文本密码。

在 Xamarin.Android 10.1 和更高版本中，此属性还支持 `env:` 和 `file:` 前缀，它们可用于指定包含密码的环境变量或文件。 通过这些选项，可以防止密码显示在生成日志中。

例如，使用名称为 AndroidSigningPassword 的环境变量：

```xml
<PropertyGroup>
    <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
</PropertyGroup>
```

使用位于 `C:\Users\user1\AndroidSigningPassword.txt` 的文件：

```xml
<PropertyGroup>
    <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
</PropertyGroup>
```

> [!NOTE]
> [`$(AndroidPackageFormat)`](#androidpackageformat) 设置为 `aab` 时，不支持 `env:` 前缀。

## <a name="androidsupportedabis"></a>AndroidSupportedAbis

此字符串属性包含应加入 `.apk` 中的分号 (`;`) 分隔的 ABI 列表。

支持的值包括：

- `armeabi-v7a`
- `x86`
- `arm64-v8a`：需要 Xamarin.Android 5.1 及更高版本。
- `x86_64`：需要 Xamarin.Android 5.1 及更高版本。

## <a name="androidtlsprovider"></a>AndroidTlsProvider

此字符串值指定应用程序中应使用哪个 TLS 提供程序。 可能的值包括：

- 取消设置/空字符串：在 Xamarin.Android 7.3 和更高版本中，这等效于 `btls`。

  在 Xamarin.Android 7.1 中，这等效于 `legacy`。

  这对应于 Visual Studio 属性页中的“默认”设置。

- `btls`：使用 [Boring SSL](https://boringssl.googlesource.com/boringssl) 与 [HttpWebRequest](xref:System.Net.HttpWebRequest) 进行 TLS 通信。

  这样，可以对所有 Android 版本使用 TLS 1.2。

  这对应于 Visual Studio 属性页中的“Native TLS 1.2+”设置。

- `legacy`：在 Xamarin.Android 10.1 和更早的版本中，对于网络交互使用之前托管的 SSL 实现。 这** 不支持 TLS 1.2。

  这对应于 Visual Studio 属性页中的“托管 TLS 1.0”设置。

  在 Xamarin.Android 10.2 和更高版本中，将忽略此值并使用 `btls` 设置。

- `default`：该值不太可能用于 Xamarin.Android 项目。 建议改用的值为空列表，它对应于 Visual Studio 属性页中的“默认”设置****。

  Visual Studio 属性页中不提供 `default` 值。

  这当前等效于 `legacy`。

已在 Xamarin.Android 7.1 中添加。

## <a name="androiduseaapt2"></a>AndroidUseAapt2

此布尔属性使开发者能够控制 `aapt2` 打包工具的使用。
默认情况下，这将为 False，并且 Xamarin.Android 将使用 `aapt`。
如果开发者要使用新的 `aapt2` 功能，则执行以下操作：

```xml
<AndroidUseAapt2>True</AndroidUseAapt2>
```

在 `.csproj` 中进行添加。 或者在命令行上提供该属性：

```shell
/p:AndroidUseAapt2=True
```

在 Xamarin.Android 8.3 中新增。

## <a name="androiduseapksigner"></a>AndroidUseApkSigner

此布尔属性使开发人员能够使用 `apksigner` 工具，而不是 `jarsigner`。

在 Xamarin.Android 8.2 中新增。

## <a name="androidusedefaultaotprofile"></a>AndroidUseDefaultAotProfile

允许开发人员禁止使用默认 AOT 配置文件的布尔属性。

若要禁止使用默认 AOT 配置文件，则将此属性设置为 `false`。

在 Xamarin.Android 10.1 中新增。

## <a name="androiduselegacyversioncode"></a>AndroidUseLegacyVersionCode

此布尔属性允许开发人员将 versionCode 计算还原到先前的 Xamarin.Android 8.2 旧行为。 这只能适用于在 Google Play 商店中已发布应用程序的开发人员。 强烈建议使用新 [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) 属性。

在 Xamarin.Android 8.2 中新增。

## <a name="androidusemanageddesigntimeresourcegenerator"></a>AndroidUseManagedDesignTimeResourceGenerator

此布尔属性将设计时生成切换为使用受管理资源分析程序，而不是 `aapt`。

在 Xamarin.Android 8.1 中新增。

## <a name="androidusesharedruntime"></a>AndroidUseSharedRuntime

此布尔属性用于确定是否需要“共享运行时包”才能在目标设备上运行应用程序。 依靠共享运行时包以允许应用程序包更小，加快包创建和部署过程，从而加快生成/部署/调试周转周期。

对于调试版本，该属性应为 `True`，对于发行项目应为 `False`。

## <a name="androidversioncodepattern"></a>AndroidVersionCodePattern

该字符串属性允许开发人员在清单中自定义 `versionCode`。
有关决定 `versionCode` 的信息，请参阅[为 APK 创建版本代码](~/android/deploy-test/building-apps/abi-specific-apks.md)。

例如，如果 `abi`是 `armeabi`，清单中的 `versionCode` 为 `123`，则当 `$(AndroidCreatePackagePerAbi)` 为 True 时，`{abi}{versionCode}` 将生成 `1123` 的 versionCode，否则将生成值 123。
如果 `abi` 是 `x86_64`，则清单中的 `versionCode` 是 `44`。 当 `$(AndroidCreatePackagePerAbi)` 为 True 时，这将生成 `544`，否则会生成值 `44`。

如果我们包含左填充格式字符串 `{abi}{versionCode:0000}`，则会生成 `50044`，因为我们用 `0` 在左边填充 `versionCode`。 此外，也可以使用十进制填充（例如 `{abi}{versionCode:D4}`），
该操作与前一个示例的效果相同。

由于值必须是整数，因此只支持 0 和 Dx 填充格式字符串。

预定义的键项

- **abi**  &ndash; 插入应用的目标 abi
  - 2 &ndash; `armeabi-v7a`
  - 3 &ndash; `x86`
  - 4 &ndash; `arm64-v8a`
  - 5 &ndash; `x86_64`

- minSDK &ndash; 如果没有定义，则插入 `AndroidManifest.xml` 或 `11` 中支持的最小 SDK 值。

- versionCode &ndash; 直接使用 `Properties\AndroidManifest.xml` 中的版本代码。

你可以使用（下文中定义的）`$(AndroidVersionCodeProperties)` 属性定义自定义项。

默认情况下，值设置为 `{abi}{versionCode:D6}`。 如果开发人员要保留旧行为，可将 `$(AndroidUseLegacyVersionCode)` 属性设置为 `true`，从而替代默认值

已在 Xamarin.Android 7.2 中添加。

## <a name="androidversioncodeproperties"></a>AndroidVersionCodeProperties

此字符串属性允许开发人员定义要与 [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) 配合使用的自定义项。
它们采用 `key=value` 对的形式。 `value` 中的所有项都应是整数值。 例如：`screen=23;target=$(_AndroidApiLevel)`。 正如你所看到的，你可以使用字符串中现有或自定义的 MSBuild 属性。

已在 Xamarin.Android 7.2 中添加。

## <a name="aotassemblies"></a>AotAssemblies

此布尔属性确定程序集是否会被预编译为本机代码并包含在 `.apk` 中。

Xamarin.Android 5.1 中增加了对该属性的支持。

该属性默认为 `False`。

## <a name="aprofutilextraoptions"></a>AProfUtilExtraOptions

要传递给 `aprofutil` 的其他选项。

## <a name="beforegenerateandroidmanifest"></a>BeforeGenerateAndroidManifest

此属性中列出的 MSBuild 目标将在 `_GenerateJavaStubs` 之前直接运行。

Added in Xamarin.Android 9.4。

## <a name="configuration"></a>Configuration

指定要使用的生成配置，例如“调试”或“发行”。 配置属性用于确定其他属性（确定目标行为）的默认值。 其他配置可能会在 IDE 中创建。

默认情况下，`Debug` 配置将导致 [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
和 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
目标创建更小的 Android 程序包，这需要提供其他文件和包进行操作。

默认 `Release` 配置会导致 [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
和 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
目标创建独立的 Android 包，无需安装其他任何包或文件即可使用此包。

## <a name="debugsymbols"></a>DebugSymbols

确定 Android 程序包是否为可调试并具有 [`$(DebugType)`](#debugtype) 属性的布尔值。
可调试包含有调试符号，将 [`//application/@android:debuggable` 属性](https://developer.android.com/guide/topics/manifest/application-element#debug)设置为 `true`，并自动添加 [`INTERNET`](https://developer.android.com/reference/android/Manifest.permission#INTERNET)
权限，以便调试器可以附加到该过程。 如果 `DebugSymbols` 是 `True`，  并且 `DebugType` 是空字符串或 `Full`，则应用程序是可调试的。

## <a name="debugtype"></a>DebugType

指定要生成的[调试符号的类型](/visualstudio/msbuild/csc-task)作为版本的一部分，它还会影响应用程序是否可调试。 可能的值包括：

- “完整”  ：生成完整符号。 如果 [`DebugSymbols`](#debugsymbols)
  MSBuild 属性也为 `True`，则应用程序包是可调试的。

- PdbOnly  ：生成“PDB”符号。 应用程序包将不可调试。

如果 `DebugType` 未设置或为空字符串，则 `DebugSymbols` 属性控制应用程序是否可调试。

## <a name="embedassembliesintoapk"></a>EmbedAssembliesIntoApk

此布尔属性确定应用的程序集是否应嵌入到应用程序包中。

对于发行版本，该属性应为 `True`，对于调试版本应为 `False`。 如果“快速部署”不支持目标设备，则调试版本中** 可能必须为 `True`。

该属性为 `False` 时，[`$(AndroidFastDeploymentType)`](#androidfastdeploymenttype)
MSBuild 属性还会控制嵌入到 `.apk` 中的内容，这会影响部署和重新生成时间。

## <a name="enablellvm"></a>EnableLLVM

此布尔属性确定在将程序集预编译为本机代码时是否使用 LLVM。

必须安装 Android NDK 才能生成启用了此属性的项目。

Xamarin.Android 5.1 中增加了对该属性的支持。

该属性默认为 `False`。

除非 [`$(AotAssemblies)`](#aotassemblies) MSBuild 属性为 `True`，否则该属性将被忽略。

## <a name="enableproguard"></a>EnableProguard

此布尔属性确定是否在打包过程中运行 [proguard](https://developer.android.com/tools/help/proguard.html) 以链接 Java 代码。

Xamarin.Android 5.1 中增加了对该属性的支持。

该属性默认为 `False`。

如果为 `True`，则 [@(ProguardConfiguration)](~/android/deploy-test/building-apps/build-items.md#proguardconfiguration) 文件将用于控制 `proguard` 的执行。

## <a name="javamaximumheapsize"></a>JavaMaximumHeapSize

指定在打包过程中构建 `.dex` 文件时使用的 java
`-Xmx` 参数值的值。 如果未指定，则 `-Xmx` 选项向 java 提供值 `1G`****。 我们发现与其他平台相比，Windows 常常要求这样设置。

如果 [`_CompileDex` 目标引发 `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)，则指定该属性是必需的。

通过如下更改自定义值：

```xml
<JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
```

## <a name="javaoptions"></a>JavaOptions

指定在生成 `.dex` 文件时传递给 java 的附加命令行选项。

## <a name="jarsignertimestampauthoritycertificatealias"></a>JarsignerTimestampAuthorityCertificateAlias

此属性允许指定时间戳颁发机构密钥存储中的别名。
有关详细信息，请参阅 Java [签名时间戳支持](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html)文档。

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
</PropertyGroup>
```

## <a name="jarsignertimestampauthorityurl"></a>JarsignerTimestampAuthorityUrl

此属性允许指定时间戳颁发机构服务的 URL。 这可确保 `.apk` 签名包含时间戳。
有关详细信息，请参阅 Java [签名时间戳支持](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html)文档。

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
</PropertyGroup>
```

## <a name="linkerdumpdependencies"></a>LinkerDumpDependencies

此布尔属性允许生成链接器依赖项文件。 可将此文件用作 [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md) 工具的输入。

默认值为 False。

## <a name="mandroidi18n"></a>MandroidI18n

指定应用程序附带的国际化支持，例如排序规则和排序表。 该值是以下一个或多个不区分大小写值的以逗号或分号分隔的列表：

- **** 无：不包含其他编码。

- **** 全部：包含所有可用的编码。

- **** CJK：包括中文、日语和朝鲜语编码，例如*日语(EUC)* \[enc-jp, CP51932\]、*日语(Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\]、*日语(JIS)* \[CP50220\]、*简体中文(GB2312)* \[gb2312, CP936\]、*朝鲜语(UHC)* \[ks\_c\_5601-1987, CP949\]、*朝鲜语(EUC)* \[euc-kr, CP51949\]、*繁体中文(Big5)* \[big5, CP950\] 以及*简体中文 (GB18030)* \[GB18030, CP54936\]。

- **** 中东：包括中东编码，例如*土耳其语(Windows)* \[iso-8859-9, CP1254\]、*希伯来语(Windows)* \[windows-1255, CP1255\]、*阿拉伯语(Windows)* \[windows-1256, CP1256\]、*阿拉伯语(ISO)* \[iso-8859-6, CP28596\]、*希伯来语(ISO)* \[iso-8859-8, CP28598\]、*拉丁语 5 (ISO)* \[iso-8859-9, CP28599\] 以及*希伯来语(Iso 备用)* \[iso-8859-8, CP38598\]。

- **** 其他：包括其他编码，例如*西里尔文(Windows)* \[CP1251\]、*波罗的语(Windows)* \[iso-8859-4, CP1257\]、*越南语(Windows)* \[CP1258\]、*西里尔文(KOI8-R)* \[koi8-r, CP1251\]、*乌克兰语(KOI8-U)* \[koi8-u, CP1251\]、*波罗的语(ISO)* \[iso-8859-4, CP1257\]、*西里尔文(ISO)* \[iso-8859-5, CP1251\]、*ISCII Davenagari* \[x-iscii-de, CP57002\]、*ISCII 孟加拉语* \[x-iscii-be, CP57003\]、*ISCII 泰米尔语* \[x-iscii-ta, CP57004\]、*ISCII 泰卢固语* \[x-iscii-te, CP57005\]、*ISCII 阿萨姆语* \[x-iscii-as, CP57006\]、*ISCII 奥里亚语* \[x-iscii-or, CP57007\]、*ISCII 卡纳达语* \[x-iscii-ka, CP57008\]、*ISCII 马拉雅拉姆语* \[x-iscii-ma, CP57009\]、*ISCII 古吉拉特语* \[x-iscii-gu, CP57010\]、*ISCII 旁遮普文* \[x-iscii-pa, CP57011\] 以及*泰语(Windows)* \[CP874\]。

- **** 少数：包括少数编码，例如 *IBM EBCDIC(土耳其语)* \[CP1026\]、*IBM EBCDIC(开放系统拉丁语 1)* \[CP1047\]、*IBM EBCDIC(美国-加拿大与欧洲)* \[CP1140\]、*IBM EBCDIC(德国与欧洲)* \[CP1141\]、*IBM EBCDIC(丹麦/挪威与欧洲)* \[CP1142\]、*IBM EBCDIC(芬兰/瑞典与欧洲)* \[CP1143\]、*IBM EBCDIC(意大利与欧洲)* \[CP1144\]、*IBM EBCDIC(拉丁美洲/西班牙与欧洲)* \[CP1145\]、*IBM EBCDIC(英国与欧洲)* \[CP1146\]、*IBM EBCDIC(法国与欧洲)* \[CP1147\]、*IBM EBCDIC(国际与欧洲)* \[CP1148\]、*IBM EBCDIC(冰岛与欧洲)* \[CP1149\]、*IBM EBCDIC(德国)* \[CP20273\]、*IBM EBCDIC(丹麦/挪威)* \[CP20277\]、*IBM EBCDIC(芬兰/瑞典)* \[CP20278\]、*IBM EBCDIC(意大利)* \[CP20280\]、*IBM EBCDIC(拉丁美洲/西班牙)* \[CP20284\]、*IBM EBCDIC(英国)* \[CP20285\]、*IBM EBCDIC(扩展式日语片假名)* \[CP20290\]、*IBM EBCDIC(法国)* \[CP20297\]、*IBM EBCDIC(阿拉伯语)* \[CP20420\]、*IBM EBCDIC(希伯来语)* \[CP20424\]、*IBM EBCDIC(冰岛语)* \[CP20871\]、*IBM EBCDIC (西里尔文 - 塞尔维亚文，保加利亚语)* \[CP21025\]、*IBM EBCDIC(美国-加拿大)* \[CP37\]、*IBM EBCDIC(国际)* \[CP500\]、*阿拉伯语(ASMO 708)* \[CP708\]、*中欧语言(DOS)* \[CP852\]*、西里尔文(DOS)* \[CP855\]、*土耳其语(DOS)* \[CP857\]、*西欧(DOS 与欧洲)* \[CP858\]、*希伯来语(DOS)* \[CP862\]、*阿拉伯语(DOS)* \[CP864\]、*俄语(DOS)* \[CP866\]、*希腊语(DOS)* \[CP869\]、*IBM EBCDIC(拉丁语 2)* \[CP870\] 以及 *IBM EBCDIC(希腊语)* \[CP875\]。

- **** 西部：包括西部编码，例如*西欧(Mac)* \[macintosh, CP10000\]、*冰岛语(Mac)* \[x-mac-icelandic, CP10079\]、*中欧(Windows)* \[iso-8859-2, CP1250\]、*西欧(Windows)* \[iso-8859-1, CP1252\]、*希腊语(Windows)* \[iso-8859-7, CP1253\]、*中欧(ISO)* \[iso-8859-2, CP28592\]、*拉丁语 3 (ISO)* \[iso-8859-3, CP28593\]、*希腊语(ISO)* \[iso-8859-7, CP28597\]、*拉丁语 9 (ISO)* \[iso-8859-15, CP28605\]、*OEM 美国* \[CP437\]、*西欧(DOS)* \[CP850\]、*葡萄牙语(DOS)* \[CP860\]、*冰岛语(DOS)* \[CP861\]、*加拿大法语(DOS)* \[CP863\] 以及*北欧文(DOS)* \[CP865\]。

```xml
<MandroidI18n>West</MandroidI18n>
```

## <a name="monoandroidresourceprefix"></a>MonoAndroidResourcePrefix

通过 `AndroidResource` 生成操作指定从文件名开头删除的“路径前缀”。 这是为了允许更改资源所在的位置。

默认值为 `Resources`。 将此项更改为 `res` 以获得 Java 项目结构。

## <a name="monosymbolarchive"></a>MonoSymbolArchive

此布尔属性控制是否创建 `.mSYM` 项目供以后与 `mono-symbolicate` 一起使用，以从版本堆栈跟踪中提取&ldquo;真实&rdquo;文件名和行号信息。

对于已启用调试符号的&ldquo;发行&rdquo;应用，默认情况下为 True：[`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) 为 True，[`$(DebugSymbols)`](~/android/deploy-test/building-apps/build-properties.md#debugsymbols)
为 True，且 [`$(Optimize)`](/visualstudio/msbuild/common-msbuild-project-properties)
为 True。

已在 Xamarin.Android 7.1 中添加。