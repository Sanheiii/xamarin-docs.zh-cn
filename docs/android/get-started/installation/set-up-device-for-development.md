---
title: 设置设备进行开发
description: 本文介绍了如何设置 Android 设备并将其连接到计算机，使设备可用于运行和调试 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 0b0bfc650ffa271a7616d7c6e6a436fafa2664c8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932595"
---
# <a name="set-up-device-for-development"></a>设置设备进行开发

本文介绍了如何设置 Android 设备并将其连接到计算机，以便可以将该设备用于运行和调试 Xamarin.Android 应用程序  。

在 Android 仿真器上进行测试后，需要查看并测试 Android 设备上运行的应用。 需要启用调试，并将设备连接到计算机。

上述每个步骤将在以下部分中详细介绍。

## <a name="enable-debugging-on-the-device"></a>在设备上启用调试

为了测试 Android 应用程序，需要为一台设备启用调试功能。 4\.2 及更高版本的 Android 上默认隐藏开发人员选项，启用这些选项的方式可能因 Android 版本而异。

### <a name="android-90"></a>Android 9.0+

针对 Android 9.0 及更高版本，请按照以下步骤启用调试：

1. 转到“设置”  屏幕。
2. 选择“关于电话”  。
3. 点击“生成号”  7次，直到显示“你现在已经是开发人员了！” 选项为止。

### <a name="android-80-and-android-81"></a>Android 8.0 和 Android 8.1

1. 转到“设置”  屏幕。
2. 选择“系统”  。
3. 选择“关于电话”
4. 点击“生成号”  7次，直到显示“你现在已经是开发人员了！” 选项为止。

### <a name="android-71-and-lower"></a>Android 7.1 及更低版本

1. 转到“设置”  屏幕。
2. 选择“关于电话”  。
3. 点击“生成号”  7次，直到显示“你现在已经是开发人员了！” 选项为止。

[![Android 9.0 上的“开发人员选项”屏幕](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>验证是否已启用 USB 调试

在设备上启用开发人员模式后，需要确保在设备上启用了 USB 调试。 具体验证方法也取决于 Android 版本。

### <a name="android-90"></a>Android 9.0+

导航到“设置”>“系统”>“高级”>“开发人员选项”  ，并启用“USB 调试”  。

### <a name="android-80-and-android-81"></a>Android 8.0 和 Android 8.1

导航到“设置”>“系统”>“开发人员选项”  ，并启用“USB 调试”  。

### <a name="android-71-and-lower"></a>Android 7.1 及更低版本

导航到“设置”>“开发人员选项”  ，并启用“USB 调试”  。

“开发人员选项”  选项卡可用后，请在“设置”>“系统”  下将其打开，以显示开发人员设置：

[![Android 9.0 上的“开发人员选项”屏幕](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

从此处可启用开发人员选项，例如 USB 调试和保持唤醒状态模式。

## <a name="connect-the-device-to-the-computer"></a>将设备连接到计算机

最后一步是将设备连接到计算机。 最简单且可靠的方式是通过 USB 来连接。

如果之前未用该计算机调试过，操作时会收到一个提示，询问设备是否要信任该计算机。 还可以选中“始终允许连接此计算机”  ，从而避免每次连接设备时都出现此提示。

![Google USB](set-up-device-for-development-images/trust-computer-for-usb-debugging.png)

## <a name="alternate-connection-via-wifi"></a>其他连接方式：通过 Wifi

可以通过 WiFi 将 Android 设备连接到计算机，无需使用 USB 线。 此方法需耗费更多工作量，但在设备距离计算机太远而不能持续通过电缆保持接通状态时很有用。 

### <a name="connecting-over-wifi"></a>通过 WiFi 连接

默认情况下，[Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) 配置为通过 USB 与 Android 设备进行通信。 可将其重新配置为使用 TCP/IP，而不是使用 USB。 为此，设备和计算机必须处于同一 WiFi 网络上。 若要通过 WiFi 设置调试环境，请从命令行执行以下步骤：

1. 确定 Android 设备的 IP 地址。 查找 IP 地址的一种方法是在“设置”>“网络和 Internet”>“Wi-Fi”下查看，然后点击设备所连接的 WiFi 网络，接着点击“高级”。 此时会打开一个下拉列表，显示网络连接的相关信息，类似下面的屏幕截图中所示：

    [![IP 地址](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    某些版本的 Android 上不会列出 IP 地址，但可在“设置”>“关于手机”>“状态”下找到 IP 地址。

2. 通过 USB 将 Android 设备连接到计算机。

3. 接下来，重启 ADB，以便可在端口 5555 上使用 TCP。 在命令提示符处，键入以下命令：

    ```command
    adb tcpip 5555
    ```

    发出此命令后，计算机不能侦听通过 USB 连接的设备。

4. 断开将设备连接到计算机的 USB 线连接。

5. 配置 ADB，使其可在上面步骤 1 中指定的端口上连接 Android 设备：

    ```command
    adb connect 192.168.1.28:5555
    ```

    此命令完成后，Android 设备即可通过 WiFi 连接到计算机。

    通过 WiFi 完成调试后，可通过以下命令将 ADB 重置回 USB 模式：
    
    ```command
    adb usb
    ```
    
    可请求 ADB 列出连接到计算机的设备。 无论设备通过何种方式连接，可在命令提示符发出以下命令，查看连接的设备：
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>疑难解答

在某些情况下，可能会发现设备无法连接到计算机。 如果出现这种情况，可能需要验证是否已安装 USB 驱动程序。

## <a name="install-usb-drivers"></a>安装 USB 驱动程序

对于 macOS，无需此步骤；只需通过 USB 线将设备连接到 Mac。

可能需要先安装一些额外的驱动程序，Windows 计算机才能识别通过 USB 连接的 Android 设备。

> [!NOTE]
> 这些是设置 Google Nexus 设备的步骤，将作为参考提供。 适用于特定设备的步骤可能有所不同，但遵循的模式是类似的。 如果遇到问题，请在 Internet 上搜索你的设备。

在 **[Android SDK install path]\tools** 目录中，运行 **android.bat** 应用程序。 默认情况下，Xamarin.Android 安装程序会将 Android SDK 放置在 Windows 计算机上的以下位置中：

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>下载 USB 驱动程序

Google Nexus 设备（Galaxy Nexus 除外）需要 Google USB 驱动程序。 Galaxy Nexus 的驱动程序[由 Samsung 分发](https://www.samsung.com/us/support/downloads/)。
所有其他 Android 设备应使用[来自其各自制造商的 USB 驱动程序](https://developer.android.com/tools/extras/oem-usb.html#Drivers)。

通过启动 Android SDK 管理器并展开“附加程序”文件夹，安装 **Google USB 驱动程序**包，如下面的屏幕截图所示：

![选择了 Google USB 驱动程序](set-up-device-for-development-images/google-usb-driver.png)

选中“Google USB 驱动程序”框，然后单击“应用更改”按钮。
驱动程序文件将下载到以下位置：

`[Android SDK install path]\extras\google\usb\_driver`

Xamarin.Android 安装的默认路径为：

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>安装 USB 驱动程序

USB 驱动程序下载完成后，请将其安装。
在 Windows 7 上安装驱动程序：

1. 通过 USB 线，将设备连接到计算机。

2. 在桌面或 Windows 资源管理器中右键单击“计算机”，然后选择“管理”。

3. 在左窗格中，选择“设备”。

4. 在右窗格中，找到并展开“其它设备”。

5. 右键单击设备名，并选择“更新驱动程序软件”。
    这将启动硬件更新向导。

6. 选择“浏览计算机以查找驱动程序软件”，然后单击“下一步” 。

7. 单击“浏览”，找到 USB 驱动程序文件夹（Google USB 驱动程序位于 [Android SDK install path]\extras\google\usb_driver ）。

8. 单击“下一步”安装驱动程序。

## <a name="summary"></a>总结

本文介绍如何通过在设备上启用调试，配置用于开发的 Android 设备。 还介绍了如何使用 USB 或 WiFi 将设备连接到计算机。

## <a name="related-links"></a>相关链接

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [使用硬件设备](https://developer.android.com/tools/device.html)
- [Samsung 驱动程序下载](https://www.samsung.com/us/support/downloads/)
- [OEM USB 驱动程序](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Google USB 驱动程序](https://developer.android.com/sdk/win-usb.html)
- [XDA 开发人员：已解决 Windows 8 - ADB/快速启动驱动程序问题](https://forum.xda-developers.com/showthread.php?t=1583801)
