---
title: 分析 Android 应用
description: 本指南介绍如何使用探查器工具来检查 Android 应用的性能和内存使用情况。
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 96c9e750238bce35d5c8c12dd2ceba6be7ab8fcc
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454814"
---
# <a name="profiling-android-apps"></a>分析 Android 应用

在将应用部署到应用商店之前，必须先识别并修复任何性能瓶颈、过度占用内存或网络资源利用效率低下的问题。 可以使用两个探查器工具来实现此目的：

- Xamarin Profiler 
- Android Studio 中的 Android Profiler

本指南介绍了 Xamarin Profiler，并提供了有关如何开始使用 Android Profiler 的详细信息。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 是独立的应用程序，与 Visual Studio 和 Visual Studio for Mac 集成，用于从 IDE 中分析 Xamarin 应用。 有关使用 Xamarin Profiler 的详细信息，请参阅 [Xamarin Profiler](~/tools/profiler/index.md)。

> [!NOTE]
> 必须订阅 [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) 才能解锁 Windows 版 Visual Studio Enterprise 或 Visual Studio for Mac 中的 Xamarin Profiler 功能。

## <a name="android-studio-profiler"></a>Android Studio Profiler

Android Studio 3.0 及更高版本含有 Android Profiler 工具。 可以使用 Android Profiler 来衡量使用 Visual Studio 生成的 Xamarin Android 应用的性能 &ndash; 而无需 Visual Studio Enterprise 许可证。 但是，与 Xamarin Profiler 不同，Android Profiler 没有与 Visual Studio 集成，并且只能用于分析已提前生成并导入 Android Profiler 的 Android 应用程序包 (APK)。

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>在 Android Profiler 中启动 Xamarin Android 应用

下列步骤介绍如何在 Android Studio 的 Android Profiler 工具中启动 Xamarin Android 应用程序。 在下面的示例屏幕截图中，Xamarin 窗体 [XamagonXuzzle](/samples/xamarin/mobile-samples/liveplayer-xamagonxuzzlelp/) 应用是使用 Android Profiler 生成和分析的：

1. 在 Android 项目生成选项中，禁用“使用共享运行时”  。 这可确保 Android 应用程序包 (APK) 的生成不依赖于共享开发时间 Mono 运行时。

    ![禁用“使用共享运行时”](profiling-images/vswin/01-turn-off-shared-runtime.png)

2. 生成用于“调试”的应用，并将其部署到物理设备或仿真器中  。 这可生成 APK 的已签名“调试”版本  。
    对于 XamagonXuzzle 示例，生成的 APK 名为 com.companyname.XamagonXuzzle Signed.apk   。

3. 打开项目文件夹，然后导航到 bin/Debug  。 在此文件夹中，找到 Signed.apk 版本的应用并将其复制到易于访问的位置（例如桌面）  。 在下面的屏幕截图中，已找到 APK com.companyname.XamagonXuzzle Signed.apk，并已将其复制到桌面  ：

    [![调试已签名 APK 文件的位置](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4. 启动 Android Studio，然后选择“分析或调试 APK”  ：

    ![从 Android Studio 启动屏幕启动探查器](profiling-images/vswin/03-android-studio.png)

5. 在“选择 APK 文件”对话框中，导航到先前生成和复制的 APK  。 选择 APK，然后单击“确定”  ： 
    
    ![在“选择 APK 文件”对话框中选择 APK](profiling-images/vswin/04-select-apk-dialog.png)

6. Android Studio 将加载 APK 并反汇编 classes.dex  ：

    ![设置 APK](profiling-images/vswin/05-setting-up-the-apk.png)

7. 加载 APK 后，Android Studio 显示以下 APK 项目屏幕。 右键单击左侧树视图中的应用名称，然后选择“打开模块设置”  ：

    [![“打开模块设置”菜单项的位置](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8. 导航到“项目设置”>“模块”，选择应用的 -Signed 节点，然后单击“&lt;无 SDK&gt;”    ：

    [![导航到 SDK 设置](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9. 在“模块 SDK”下拉菜单中，选择用于生成应用的 Android SDK 级别（在此示例中，使用 API 级别 26 来生成 XamagonXuzzle）   ：

    [![设置项目 SDK 级别](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    单击“应用”和“确定”保存此设置   。

10. 从工具栏图标启动探查器：

    [![探查器工具栏图标的位置](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. 选择运行/分析应用的部署目标，然后单击“确定”  。 部署目标可以是物理设备，也可以是在仿真器中运行的虚拟设备。 此示例使用的是 Nexus 5X 设备：

    ![选择部署目标](profiling-images/vswin/10-select-deployment-target.png)

12. 探查器启动后，需要几秒钟时间才能连接到部署设备和应用进程。 安装 APK 时，Android Profiler 将报告“无已连接设备”和“无可调试进程”   。

    [![探查器安装 APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. 几秒钟后，Android Profiler 完成 APK 安装并启动 APK，报告正在分析的设备名称和应用进程名称（在此示例中，分别是 LGE Nexus 5X 和 com.companyname.XamagonXuzzle）   ：

    [![启动后的 Profiler 窗口](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. 识别设备和可调试进程后，Android Profiler 开始分析应用：

    [![为正在运行的应用显示的 Profiler](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. 如果点击 XamagonXuzzle 上的“随机化”按钮（这会导致其移动和随机化磁贴），可看到 CPU 使用率在应用的随机化间隔期间增加   ：

    [![点击“随机化”按钮时的 CPU 使用率](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)

### <a name="using-the-android-profiler"></a>使用 Android Profiler

[Android Studio 文档](https://developer.android.com/studio/profile/android-profiler.html)中记录了有关使用 Android Profiler 的详细信息。
Xamarin Android 开发人员可能对以下主题感兴趣：

- [CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler.html) &ndash; 说明如何实时检查应用的 CPU 使用情况和线程活动。

- [内存 Profiler](https://developer.android.com/studio/profile/memory-profiler.html) &ndash; 显示应用内存使用情况的实时图，并包括记录内存分配以进行分析的按钮。

- [网络 Profiler](https://developer.android.com/studio/profile/network-profiler.html) &ndash; 显示应用发送和接收的数据的实时网络活动。