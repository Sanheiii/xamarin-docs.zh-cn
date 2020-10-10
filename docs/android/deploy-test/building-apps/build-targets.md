---
title: 生成目标
description: 本文档将列出 Xamarin.Android 生成过程支持的所有目标。
ms.prod: xamarin
ms.assetid: 17DE89FF-F316-4620-B865-EF6E0963A29C
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/17/2020
ms.openlocfilehash: 4482e6c4bfe2a6952d59d896b7c79ca82432b42b
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247214"
---
# <a name="build-targets"></a>生成目标

以下的生成目标是为 Xamarin.Android 项目定义的。

## <a name="build"></a>生成

在项目和所有依赖项中生成源代码。

此目标不会创建 Android 包（`.apk` 文件）。
若要创建 Android 包，请使用 [SignAndroidPackage](#signandroidpackage) 目标，或在进行生成时将 [`$(AndroidBuildApplicationPackage)](~/android/deploy-test/building-apps/build-properties.md#androidbuildapplicationpackage) 属性设置为 True：

```shell
msbuild /p:AndroidBuildApplicationPackage=True App.sln
```

## <a name="buildandstartaotprofiling"></a>BuildAndStartAotProfiling

使用嵌入的 AOT 探查器生成应用，将探查器 TCP 端口设置为 [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport)，并启动默认活动。

默认的 TCP 端口为 `9999`。

在 Xamarin.Android 10.2 中新增。

## <a name="clean"></a>clean

删除由生成过程生成的所有文件。

## <a name="finishaotprofiling"></a>FinishAotProfiling

必须在 [BuildAndStartAotProfiling](#buildandstartaotprofiling) 目标之后调用。

通过 TCP 端口 [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport) 从设备或模拟器收集 AOT 探查器数据，
并将其写入 [`$(AndroidAotCustomProfilePath)`](~/android/deploy-test/building-apps/build-properties.md#androidaotcustomprofilepath)。

端口和自定义配置文件的默认值分别为 `9999` 和 `custom.aprof`。

若要将其他选项传递到 `aprofutil`，则从 [`$(AProfUtilExtraOptions)`](~/android/deploy-test/building-apps/build-properties.md#aprofutilextraoptions) 属性中设置它们
属性中找到的值。

这等效于：

```shell
aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
```

在 Xamarin.Android 10.2 中新增。

## <a name="install"></a>安装

[创建 Android 包、进行签名](#signandroidpackage)，并将其安装到默认设备或虚拟设备中。

[`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) 属性指定 Android 包可能要安装到或从中删除的 Android 目标设备。

```bash
# Install package onto emulator via -e
# Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
```

## <a name="signandroidpackage"></a>SignAndroidPackage

创建 Android 包 (`.apk`) 文件并进行签名。

用于 `/p:Configuration=Release`，生成自包含的“发行”包。

## <a name="startandroidactivity"></a>StartAndroidActivity

在设备或正在运行的模拟器上启动默认活动。

若要启动其他活动，请将 [`$(AndroidLaunchActivity)`](~/android/deploy-test/building-apps/build-properties.md#androidlaunchactivity)
属性设置为相应的活动名称。

这等效于：

```shell
adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)
```

在 Xamarin.Android 10.2 中新增。

## <a name="stopandroidpackage"></a>StopAndroidPackage

在设备或正在运行的模拟器上完全停止应用程序包。

这等效于：

```shell
adb shell am force-stop @PACKAGE_NAME@
```

在 Xamarin.Android 10.2 中新增。

## <a name="uninstall"></a>卸载

从默认设备或虚拟设备中卸载 Android 包。

[`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) 属性指定 Android 包可能要安装到或从中删除的 Android 目标设备。

## <a name="updateandroidresources"></a>UpdateAndroidResources

更新 `Resource.designer.cs` 文件。

将新的资源添加到项目中时，这个目标通常由 IDE 调用。
