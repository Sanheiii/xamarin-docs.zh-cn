---
title: Xamarin.Android 环境
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: da0e3775f400c965ee59a762884e638e3379c8df
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454853"
---
# <a name="xamarinandroid-environment"></a>Xamarin.Android 环境

## <a name="execution-environment"></a>执行环境

执行环境** 是一系列影响程序执行的环境变量和 Android 系统属性。 Android 系统属性可通过 `adb shell setprop` 命令设置，而环境变量可通过设置 `debug.mono.env` 系统属性进行设置：

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

Android 系统属性是针对目标设备上的所有进程而设置的。

从 Xamarin.Android 4.6 开始，可能会在每个应用的基础上设置或重写系统属性和环境变量，方法是将环境文件** 添加到项目。 环境文件是 Unix 格式的纯文本文件，附带 [`AndroidEnvironment` 的生成操作****](~/android/deploy-test/building-apps/build-process.md)。
环境文件包含键=值** 格式的行。
注释是以 `#` 开头的行。 空行将被忽略。

如果密钥** 以大写字母开头，密钥** 将被视为一个环境变量，且 setenv****(3) 用于在进程启动过程中将环境变量设置为指定值**。

如果密钥** 以小写字母开头，则密钥** 会被视为 Android 系统属性，且值** 为默认值**：首先从 Android 系统属性存储查找控制 Xamarin.Android 执行行为的 Android 系统属性，如果未指定任何值，则使用环境文件中指定的值。 这是为了允许 `adb shell setprop` 用于重写来自环境文件的值，以进行诊断。

## <a name="xamarinandroid-environment-variables"></a>Xamarin.Android 环境变量

Xamarin.Android 支持 `XA_HTTP_CLIENT_HANDLER_TYPE` 变量，可通过 `adb shell setprop debug.mono.env` 或 `$(AndroidEnvironment)` 生成操作进行设置。

### `XA_HTTP_CLIENT_HANDLER_TYPE`

程序集限定类型必须继承自 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1)，并从[`HttpClient()`默认构造函数](/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor)进行构造。

在 Xamarin.Android 6.1 中，默认情况下不会设置此环境变量，且会使用 [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1)。

或者，可以指定值 `Xamarin.Android.Net.AndroidClientHandler` 使用 [`java.net.URLConnection`](xref:Java.Net.URLConnection)
进行网络访问，该操作可允许使用 TLS 1.2（在获得 Android 支持的情况下）**。

已在 Xamarin.Android 6.1 中添加。

## <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系统属性

Xamarin.Android 支持以下系统属性，可通过 `adb shell setprop` 或 `$(AndroidEnvironment)` 生成操作进行设置。

- `debug.mono.debug`
- `debug.mono.env`
- `debug.mono.gc`
- `debug.mono.log`
- `debug.mono.max_grefc`
- `debug.mono.profile`
- `debug.mono.runtime_args`
- `debug.mono.trace`
- `debug.mono.wref`
- `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

`debug.mono.debug` 系统属性的值是一个整数。 若为 `1`，则表现为就好像进程是以 `mono --debug` 开始的。
这通常会在堆栈跟踪中显示文件和行信息等内容，而无需应用通过调试器启动。

### `debug.mono.env`

包含环境变量以 `|` 分隔的列表。

### `debug.mono.gc`

`debug.mono.debug` 系统属性的值是一个整数。
若为 `1`，则应记录 GC 信息。

这相当于使 `debug.mono.log` 系统属性包含 `gc`。

### `debug.mono.log`

控制 Xamarin.Android 将记录到 `adb logcat` 的其他信息。
它是一个以逗号分隔的字符串 (`,`)，包含以下值之一：

- `all`：打印所有** 消息。 这不是一个好主意，因为它包含 `lref` 消息。
- `assembly`：打印 `.apk` 和程序集分析消息。
- `gc`：打印与 GC 相关的消息。
- `gref`：打印 JNI 全局引用消息。
- `lref`：打印 JNI 本地引用消息。
  > [!NOTE]
  > 这将是真正的垃圾邮件**`adb logcat`。
  > 在 Xamarin.Android 5.1 中，它还会创建 `.__override__/lrefs.txt` 文件，从中获取 gigantic**。
  > 请避免。
- `timing`：打印某些方法计时信息。 这还将创建文件 `.__override__/methods.txt` 和 `.__override__/counters.txt`。

### `debug.mono.max_grefc`

`debug.mono.max_grefc` 系统属性的值是一个整数。
其值会重写** 默认检测到的目标设备的最大 GREF 计数。

*请注意：* 这只能与 `adb shell setprop
debug.mono.max_grefc` 一起使用，因为当此值在 environment.txt 文件中时将不可用 **。**

### `debug.mono.profile`

`debug.mono.profile` 系统属性将启用探查器。
它等效于 `mono --profile` 选项，并使用与之相同的值。 （请参阅 [mono****(1)](http://docs.go-mono.com/?link=man%3amono(1)) 手册页了解详细信息。）

### `debug.mono.runtime_args`

`debug.mono.runtime_args` 系统属性包含应通过 mono**** 分析的其他选项。

### `debug.mono.trace`

`debug.mono.trace` 系统属性将启用跟踪。
它等效于 `mono --trace` 选项，并使用与之相同的值。 （请参阅 [mono****(1)](http://docs.go-mono.com/?link=man%3amono(1)) 手册页了解详细信息。）

一般情况下，* 不使用 *。 使用跟踪将发送垃圾邮件 `adb logcat` 输出，严重减慢程序行为，并更改程序行为（直至并包括添加其他错误情况）。

但是有些时候**，它允许执行某些进一步研究...

### `debug.mono.wref`

`debug.mono.wref` 系统属性可重写默认检测到的 JNI 弱引用机制。 有两个支持的值：

- `jni`：使用 JNI 弱引用，由 `JNIEnv::NewWeakGlobalRef()` 创建并由 `JNIEnv::DeleteWeakGlobalREf()` 销毁。
- `java`：使用引用 `java.lang.WeakReference` 实例的 JNI 全局引用。

默认情况下使用 `java`，通过 API 7 和 API-19 (Kit Katt) 启用，同时启用 ART。 （API 8 添加了 `jni` 引用，而 ART 中断了** `jni` 引用。）

此系统属性可用于测试和进行某些形式的调查。
一般情况下**，不应更改此属性。

### <a name="xa_http_client_handler_type"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

在 Xamarin.Android 6.1 中首次引入，此环境变量声明将由 `HttpClient` 使用的默认 `HttpMessageHandler` 实现。 默认情况下不设置此变量，Xamarin.Android 将使用 `HttpClientHandler`。

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> 基础 Android 设备必须支持 TLS 1.2。
Android 5.0 及更高版本支持 TLS 1.2

## <a name="example"></a>示例

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.

## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```

## <a name="related-links"></a>相关链接

- [传输层安全性](~/cross-platform/app-fundamentals/transport-layer-security.md)