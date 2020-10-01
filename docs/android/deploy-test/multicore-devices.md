---
title: 多核设备和 Xamarin.Android
description: Android 可以在几种不同的计算机体系结构上运行。 本文档讨论可为一个 Xamarin.Android 应用程序部署的不同 CPU 体系结构。 本文还将介绍如何打包 Android 应用程序以支持不同的 CPU 体系结构。 将介绍应用程序二进制接口 (ABI)，并且将提供有关在 Xamarin.Android 应用程序中使用哪些 ABI 的指导。
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 85efadaa88e8923f363bd92c0426d536b6ebd331
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454866"
---
# <a name="multi-core-devices--xamarinandroid"></a>多核设备和 Xamarin.Android

_Android 可以在几种不同的计算机体系结构上运行。本文档讨论可为一个 Xamarin.Android 应用程序部署的不同 CPU 体系结构。本文还将介绍如何打包 Android 应用程序以支持不同的 CPU 体系结构。将介绍应用程序二进制接口 (ABI)，并且将提供有关在 Xamarin.Android 应用程序中使用哪些 ABI 的指导。_

## <a name="overview"></a>概述

Android 允许创建“胖二进制文件”，这是一个 `.apk` 文件，其中包含将支持多种不同的 CPU 体系结构的计算机代码。 这是通过将每段计算机代码与应用程序二进制接口  相关联来实现的。 ABI 用于控制哪些计算机代码将在给定的硬件设备上运行。 例如，对于在 x86 设备上运行的 Android 应用程序，编译应用程序时需要包含 x86 ABI 支持。

具体而言，每个 Android 应用程序将至少支持一个嵌入式应用程序二进制接口  (EABI)。 EABI 是特定于嵌入式软件程序的约定。 典型的 EABI 将描述如下内容：

- CPU 指令集。

- 内存的字节排序方式在运行时存储和加载。

- 对象文件和程序库的二进制格式，以及这些文件和库中允许或支持的内容类型。

- 用于在应用程序代码和系统之间传递数据的各种约定（例如：调用函数时如何使用寄存器和/或堆栈、对齐约束等）

- 枚举类型、结构、字段和数组的对齐和大小约束。

- 运行时可用于计算机代码的函数符号列表，通常来自一组专门选择的库。

### <a name="armeabi-and-thread-safety"></a>armeabi 和线程安全性

应用程序二进制接口将在下面详细讨论，但务必要记住 Xamarin.Android 使用的 `armeabi` 运行时不具线程安全性  。 如果将具有 `armeabi` 支持的应用程序部署到 `armeabi-v7a` 设备，则将会发生许多奇怪且无法解释的异常情况。

由于 Android 4.0.0、4.0.1、4.0.2 和 4.0.3 中存在 bug，即使存在 `armeabi-v7a` 目录且设备为 `armeabi-v7a` 设备，也会从 `armeabi` 目录中选取本机库。

> [!NOTE]
> Xamarin.Android 将确保 `.so` 以正确的顺序添加到 APK。 此 bug 对 Xamarin.Android 的用户来说应该不是问题。

### <a name="abi-descriptions"></a>ABI 说明

Android 支持的每个 ABI 均由唯一名称标识。

#### <a name="armeabi"></a>armeabi

这是基于 ARM 的至少支持 ARMv5TE 指令集的 CPU 的 EABI 名称。 Android 遵循小字节序 ARM GNU/Linux ABI。 此 ABI 不支持硬件辅助的浮点计算。 所有 FP 操作都由来自编译器的 `libgcc.a` 静态库的软件帮助程序函数执行。 `armeabi` 不支持 SMP 设备。

> [!IMPORTANT]
> Xamarin.Android 的 `armeabi` 代码不具线程安全性，不应在多 CPU `armeabi-v7a` 设备上使用（如下所述）。 在单核 `armeabi-v7a` 设备上使用 `armeabi` 代码是安全的。

#### <a name="armeabi-v7a"></a>armeabi-v7a

这是另一种基于 ARM 的 CPU 指令集，可扩展上述 `armeabi` EABI。 `armeabi-v7a` EABI 支持硬件浮点操作和多个 CPU (SMP) 设备。 使用 `armeabi-v7a` EABI 的应用程序与使用 `armeabi` 的应用程序相比，可以获得极大的性能改进。

> [!NOTE]
> `armeabi-v7a` 计算机代码将不会在 ARMv5 设备上运行。

#### <a name="arm64-v8a"></a>arm64-v8a

这是基于 ARMv8 CPU 体系结构的 64 位指令集。 此体系结构用于 *Nexus 9*。
Xamarin.Android 5.1 引入了对此体系结构的支持（有关详细信息，请参阅 [64 位运行时支持](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support)）。

#### <a name="x86"></a>x86

这是支持通常名为 x86  或 IA-32  的指令集的 CPU 的 ABI 名称。 此 ABI 对应于 Pentium Pro 指令集的指令，包括 MMX、SSE、SSE2 和 SSE3 指令集。 不包括任何其他可选的 IA-32 指令集扩展，例如：

- MOVBE 指令。
- 补充 SSE3 扩展 (SSSE3)。
- SSE4 的任何变体。

> [!NOTE]
> 虽然 Google TV 在 x86 上运行，但不受 Android 的 NDK 支持。

#### <a name="x86_64"></a>x86_64

这是支持 64 位 x86 指令集（也称为 x64  或 AMD64  ）的 CPU 的 ABI 名称。 Xamarin.Android 5.1 引入了对此体系结构的支持（有关详细信息，请参阅 [64 位运行时支持](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support)）。

#### <a name="apk-file-format"></a>APK 文件格式

Android 应用程序包是包含 Android 应用程序所需的所有代码、资产、资源和证书的文件格式。 它是一个 `.zip` 文件，但使用 `.apk` 文件扩展名。 展开后，Xamarin.Android 创建的 `.apk` 的内容可以在下面的屏幕截图中看到：

[![.apk 的内容](multicore-devices-images/00.png)](multicore-devices-images/00.png#lightbox)

`.apk` 文件内容的快速说明：

- AndroidManifest.xml  &ndash; 这是 `AndroidManifest.xml` 文件，采用二进制 XML 格式。

- classes.dex  &ndash; 这包含应用程序代码，编译为 Android 运行时 VM 使用的 `dex` 文件格式。

- resources.arsc  &ndash; 此文件包含应用程序的所有预编译资源。

- lib  &ndash; 此目录包含每个 ABI 的编译代码。 它将包含上一节中所述的每个 ABI 的一个子文件夹。 在上面的屏幕截图中，所涉及的 `.apk` 都具有 `armeabi-v7a` 和 `x86` 的本机库。

- META-INF  &ndash; 此目录（如果存在）用于存储签名信息、包和扩展配置数据。

- res  &ndash; 此目录包含未编译为 `resources.arsc` 的资源。

> [!NOTE]
> 文件 `libmonodroid.so` 是所有 Xamarin.Android 应用程序所需的本机库。

#### <a name="android-device-abi-support"></a>Android 设备 ABI 支持

每个 Android 设备支持在最多两个 ABI 中执行本机代码：

- “主”ABI  &ndash; 这对应于系统映像中使用的计算机代码。

- “辅助”ABI  &ndash; 这是也受系统映像支持的可选 ABI。

例如，典型的 ARMv5TE 设备将仅具有主 ABI `armeabi`，而 ARMv7 设备将指定主 ABI `armeabi-v7a` 和辅助 ABI `armeabi`。 典型的 x86 设备将仅指定主 ABI `x86`。

### <a name="android-native-library-installation"></a>Android 本机库安装

在包安装时，`.apk` 中的本机库被提取到应用的本机库目录中，通常为 `/data/data/<package-name>/lib`，此后称为 `$APP/lib`。

Android 的本机库安装行为在 Android 版本之间会发生显著变化。

#### <a name="installing-native-libraries-pre-android-40"></a>安装本机库：Android 4.0 之前

4\.0 Ice Cream Sandwich 之前的 Android 将仅从 `.apk` 内的单个 ABI  中提取本机库。 此年份的 Android 应用将首先尝试为主 ABI 提取所有本机库，如果不存在此类库，则 Android 将为辅助 ABI 提取所有本机库。 没有执行任何“合并”。

例如，请考虑在 `armeabi-v7a` 设备上安装应用程序的情况。 同时支持 `armeabi` 和 `armeabi-v7a` 的 `.apk,` 具有以下 ABI `lib` 目录以及其中的文件：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

安装完成后，本机库目录将包含：

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

换而言之，没有安装 `libone.so`。 这将导致出现问题，因为对于在运行时要加载的应用程序 `libone.so` 不存在。 此行为是意外发生，已被记录为 bug 并重新归类为“[按预期方式工作](https://code.google.com/p/android/issues/detail?id=9089)”。

因此，针对 4.0 之前的 Android 版本，有必要为应用程序将支持的每个  ABI 提供所有  本机库，即 `.apk` 应包含：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```

#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>安装本机库：Android 4.0 &ndash; Android 4.0.3

Android 4.0 Ice Cream Sandwich 更改了提取逻辑。 它将枚举所有本机库，查看是否已经提取文件的基本名称，并且如果满足以下两个条件，则将提取该库：

- 它还没有被提取。

- 本机库的 ABI 匹配目标的主或辅助 ABI。

满足这些条件即允许“合并”行为；也就是说，如果我们有一个具有以下内容的 `.apk`：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

安装完成后，本机库目录将包含：

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

遗憾的是，此行为依赖于顺序，如以下文档所述 - [问题 24321：armeabi 和 armeabi-v7a 同时包含在 apk 中时，Galaxy Nexus 4.0.2 使用 armeabi 本机代码](https://code.google.com/p/android/issues/detail?id=25321)。

本机库“按顺序”（例如，通过解压缩列出的顺序）进行处理，并提取*第一个匹配项*。 由于 `.apk` 包含 `libtwo.so` 的 `armeabi` 和 `armeabi-v7a` 版本，并且首先列出 `armeabi`，它是提取的 `armeabi` 版本，*不是* `armeabi-v7a` 版本：

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

此外，即使同时指定了 `armeabi` 和 `armeabi-v7a` ABI（如下面的*声明支持的 ABI* 部分所述），Xamarin.Android 将在 .
`csproj`：

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

因此，首先可找到 `.apk` 中的 `armeabi` `libmonodroid.so`，并且会提取 `armeabi` `libmonodroid.so`，即使 `armeabi-v7a` `libmonodroid.so` 存在并针对目标进行了优化。 这也会导致隐蔽的运行时错误，因为 `armeabi` 不具 SMP 安全性。

##### <a name="installing-native-libraries-android-404-and-later"></a>安装本机库：Android 4.0.4 及更高版本

Android 4.0.4 更改了提取逻辑：它将枚举所有本机库，读取文件的基本名称，然后提取主 ABI 版本（如果存在）或辅助 ABI（如果存在）。 这允许“合并”行为；也就是说，如果我们有一个具有以下内容的 `.apk`：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

安装完成后，本机库目录将包含：

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android 和 ABI

Xamarin.Android 支持以下 64 位  体系结构：

- `arm64-v8a`
- `x86_64`

> [!NOTE]
> 自 2018 年 8 月起新应用需要面向 API 级别 26，自 2019 年 8 月起，除 32 位版本之外，应用还[需要提供 64 位版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

Xamarin.Android 支持以下 32 位体系结构：

- `armeabi` ^
- `armeabi-v7a`
- `x86`

> [!NOTE]
> ^  从 [Xamarin.Android 9.2](/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture) 开始，不再支持 `armeabi`。

Xamarin.Android 当前不支持 `mips`。

### <a name="declaring-supported-abis"></a>声明受支持的 ABI

默认情况下，对于**发行**版本，Xamarin.Android 默认为 `armeabi-v7a`，对于**调试**版本，则为 `armeabi-v7a` 和 `x86`。 通过 Xamarin.Android 项目的项目选项可以设置对不同 ABI 的支持。 在 Visual Studio 中，可在“高级”  选项卡下的项目“属性”  的“Android 选项”  页中进行此设置，如以下屏幕截图所示：

![Android 选项高级属性](multicore-devices-images/vs-abi-selections.png)

在 Visual Studio for Mac 中，可在“高级”  选项卡下的“项目选项”  的“Android 版本”  页上选择支持的体系结构，如以下屏幕截图所示：

[![Android 版本支持的 ABI](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png#lightbox)

在某些可能需要声明额外的 ABI 支持的情况，例如以下情况：

- 将应用程序部署到 `x86` 设备。

- 将应用程序部署到 `armeabi-v7a` 设备以确保线程安全性。

## <a name="summary"></a>总结

本文档讨论可运行 Android 应用程序的不同 CPU 体系结构。 它介绍了应用程序二进制接口以及 Android 如何使用它来支持不同的 CPU 体系结构。
然后接着讨论如何在 Xamarin.Android 应用程序中指定 ABI 支持，并强调了在仅适用于 `armeabi` 的 `armeabi-v7a` 设备上使用 Xamarin.Android 应用程序时出现的问题。

## <a name="related-links"></a>相关链接

- [Android NDK](https://developer.android.com/tools/sdk/ndk/index.html)
- [问题 9089：Nexus One - 如果 armeabi-v7a 中至少有一个库，则不会加载来自 armeabi 的任何本机库](https://code.google.com/p/android/issues/detail?id=9089)
- [问题 24321：armeabi 和 armeabi-v7a 同时包含在 apk 中时，Galaxy Nexus 4.0.2 使用 armeabi 本机代码](https://code.google.com/p/android/issues/detail?id=25321)