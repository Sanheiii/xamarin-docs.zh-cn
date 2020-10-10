---
title: 生成过程
description: 本文档将提供对 Xamarin 生成过程的概述。
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/11/2020
ms.openlocfilehash: d89f686be99dc8ae8d1aada12dcbe94d857424d7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454957"
---
# <a name="build-process"></a>生成过程

Xamarin.Android 生成过程负责将所有内容集合在一起：[生成 `Resource.designer.cs`](~/android/internals/api-design.md)，支持 [`@(AndroidAsset)`](~/android/deploy-test/building-apps/build-items.md#androidasset)、[`@(AndroidResource)`](~/android/deploy-test/building-apps/build-items.md#androidresource) 和其他[生成操作](~/android/deploy-test/building-apps/build-items.md)，生成 [Android 可调用的包装器](~/android/platform/java-integration/android-callable-wrappers.md)，以及生成 `.apk` 以在 Android 设备上执行。

## <a name="application-packages"></a>应用程序包

在广义上说，Xamarin.Android 生成系统可以生成两种类型的 Android 应用程序包（`.apk` 文件）：

-  发行版本，完全是自包含的，不需要额外的程序包来执行。 这些是提供给应用商店的程序包。

-  调试版本则不是。

并非巧合的是，这些版本与生成程序包的 MSBuild `Configuration` 相匹配。

## <a name="shared-runtime"></a>共享运行时

 “共享运行时”是一对额外的 Android 程序包，它们提供基类库（`mscorlib.dll` 等）和 Android 绑定库（`Mono.Android.dll` 等）。 调试版本依赖共享运行时替代在 Android 应用程序包中包含基类库和绑定程序集，从而使调试程序包更小。

可以在调试版本中禁用共享运行时，具体方法是将 [`$(AndroidUseSharedRuntime)`](~/android/deploy-test/building-apps/build-properties.md#androidusesharedruntime)
属性设置为 `False`。

<a name="Fast_Deployment"></a>

## <a name="fast-deployment"></a>快速部署

 “快速部署”与共享运行时协同工作，进一步缩小 Android 应用程序包的大小。 可以通过不在程序包内捆绑应用的程序集来完成此操作。 而是通过 `adb push` 将它们复制到目标上。 此进程加快了生成/部署/调试周期，因为如果  只更改了程序集，则不会重新安装该程序包。 相反，只有更新的程序集才会重新同步到目标设备。

已知快速部署在阻止 `adb` 同步到目录 `/data/data/@PACKAGE_NAME@/files/.__override__` 的设备上会失败。

快速部署在默认情况下处于启用状态，可以通过将 `$(EmbedAssembliesIntoApk)` 属性设置为 `True` 在调试版本中禁用。

## <a name="msbuild-projects"></a>MSBuild 项目

Xamarin.Android 生成过程基于 MSBuild，它也是 Visual Studio for Mac 和 Visual Studio 使用的项目文件格式。
通常，用户不需要手工编辑 MSBuild 文件 &ndash; IDE 将创建功能齐全的项目并使用所做的全部更改进行更新，根据需要自动调用生成目标。

高级用户可能希望执行 IDE 的 GUI 不支持的操作，因此，可通过直接编辑项目文件来自定义生成过程。
本页仅记录 Xamarin.Android 特定的功能和自定义 &ndash; 可以使用正常的 MSBuild 项目、属性和目标执行更多的操作。

<a name="Build_Targets"></a>

## <a name="binding-projects"></a>绑定项目

以下 MSBuild 属性与[绑定项目](~/android/platform/binding-java-library/index.md)一起使用：

- [`$(AndroidClassParser)`](~/android/deploy-test/building-apps/build-properties.md#androidclassparser)
- [`$(AndroidCodegenTarget)`](~/android/deploy-test/building-apps/build-properties.md#androidcodegentarget)

## <a name="resourcedesignercs-generation"></a>`Resource.designer.cs` 生成

以下 MSBuild 属性用于控制 `Resource.designer.cs` 文件的生成：

- [`$(AndroidAapt2CompileExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2compileextraargs)
- [`$(AndroidAapt2LinkExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2linkextraargs)
- [`$(AndroidExplicitCrunch)`](~/android/deploy-test/building-apps/build-properties.md#androidexplicitcrunch)
- [`$(AndroidR8IgnoreWarnings)`](~/android/deploy-test/building-apps/build-properties.md#androidr8ignorewarnings)
- [`$(AndroidResgenExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenextraargs)
- [`$(AndroidResgenFile)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenfile)
- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2)
- [`$(MonoAndroidResourcePrefix)`](~/android/deploy-test/building-apps/build-properties.md#monoandroidresourceprefix)

## <a name="signing-properties"></a>签名属性

签名属性控制应用程序包的签名方式，以便将其安装到 Android 设备上。 为了更快地生成迭代，Xamarin.Android 任务不会在生成过程中为包签名，因为签名很慢。 相反，它们在安装之前或导出过程中由 IDE 或** 安装生成目标进行签名（如有必要）。 调用 SignAndroidPackage** 目标将生成一个在输出目录中带有 `-Signed.apk` 后缀的包。

默认情况下，如果需要，签名目标会生成一个新的调试签名密钥。 如果你希望使用特定的密钥，例如在生成服务器上，则使用以下 MSBuild 属性：

- [`$(AndroidDebugKeyAlgorithm)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyalgorithm)
- [`$(AndroidDebugKeyValidity)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyvalidity)
- [`$(AndroidDebugStoreType)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugstoretype)
- [`$(AndroidKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidkeystore)
- [`$(AndroidSigningKeyAlias)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeyalias)
- [`$(AndroidSigningKeyPass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeypass)
- [`$(AndroidSigningKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeystore)
- [`$(AndroidSigningStorePass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningstorepass)
- [`$(JarsignerTimestampAuthorityCertificateAlias)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthoritycertificatealias)
- [`$(JarsignerTimestampAuthorityUrl)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthorityurl)

### <a name="keytool-option-mapping"></a>`keytool` 选项映射

请考虑以下 `keytool` 调用：

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

要使用上面生成的密钥存储，请使用属性组：

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

## <a name="build-extension-points"></a>生成扩展点

Xamarin.Android 为希望连接到我们的生成过程的用户公开一些公共扩展点。 为了使用其中一个扩展点，你需要将你的自定义目标添加到 `PropertyGroup` 中的相应 MSBuild 属性。 例如：

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

扩展点包括：

- [`$(AfterGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#aftergenerateandroidmanifest)
- [`$(BeforeGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#beforegenerateandroidmanifest)

有关扩展生成过程的注意事项：如果编写不正确，则生成扩展会影响生成性能，尤其是在每个生成上运行时。 强烈建议先阅读 MSBuild [文档](/visualstudio/msbuild/msbuild)，再实现此类扩展。

## <a name="target-definitions"></a>目标定义

生成过程的 Xamarin.Android 特定部分在 `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets` 中定义，但生成该程序集时也需要使用正常的语言特定目标，如 Microsoft.CSharp.targets**。

导入任何语言目标之前，必须设置以下生成属性：

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

通过导入 Xamarin.Android.CSharp.targets，可在 C# 中包含所有这些目标和属性**：

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

该文件可轻松适应于其他语言。