---
title: 是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 336fb2ae1f1619994b11b630a6cb6726f17d0758
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851517"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？

若要从 Windows 虚拟机连接到 Mac 上运行的 Android Emulator，请按照以下步骤操作：

> [!NOTE]
> 建议使用不包含 Google Play 商店的 Android Emulator。

1. 在 Mac 上启动模拟器。

2. 在 Mac 上终止 `adb` 服务器：

    ```bash
    adb kill-server
    ```

3. 请注意，模拟器在侦听环回网络接口上的 2 个 TCP 端口：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇数端口用于连接到 `adb`。 另请参阅 [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4. _选项 1_：使用 `nc` 将从外部端口 5555（或你喜欢的其他任何端口）收到的入站 TCP 数据包转发到环回接口上的奇数端口（在此示例中为 127.0.0.1 5555），并以另一种方式将出站数据包转发回来：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要 `nc` 命令仍继续在终端窗口中运行，数据包就会按预期转发。 在使用完模拟器后，可以在终端窗口中键入“Control-C”，以退出 `nc` 命令。

    （选项 1 通常比选项 2 简单，特别是在“系统首选项”>“安全和隐私”>“防火墙”开关打开时。）

    _选项 2_：使用 `pfctl` 将 TCP 数据包从[共享网络](https://kb.parallels.com/en/4948)接口上的端口 `5555`（或你喜欢的其他任何端口）重定向到环回接口上的奇数端口（在此示例中为 `127.0.0.1:5555`）：

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令使用 `pf packet filter` 系统服务来设置端口转发。 换行符非常重要。 请务必原封不动地复制粘贴它们。 如果使用的是 Parallels，还需要调整 vmnet8 中的接口名称。 `vmnet8` 是 VMWare Fusion 中“共享网络”模式的特殊 NAT 设备的名称。 Parallels 中的相应网络接口可能是 [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 从 Windows 计算机连接到模拟器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    将“ip-address-of-the-mac”替换为 Mac 的 IP 地址（例如，`ifconfig vmnet8 | grep 'inet '` 列出的 IP 地址）。 如果需要，请在第 4 步中将 `5555` 替换为你喜欢的其他任何端口。 （注意：获取对 `adb` 的命令行访问权限的一种方法是，通过 Visual Studio 中的[“工具”>“Android”>“Android Adb 命令提示符”](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。）

### <a name="alternate-technique-using-ssh"></a>使用 `ssh` 的替代技术

如果已在 Mac 上启用“远程登录”，可以使用 `ssh` 端口转发来连接到模拟器。

1. 在 Windows 上安装 SSH 客户端。 一种选择是安装 [Git for Windows](https://git-for-windows.github.io/)。 然后，`ssh` 命令会在 Git Bash 命令提示符中可用。

2. 按照上面的第 1-3 步操作，以启动模拟器，在 Mac 上终止 `adb` 服务器，并标识模拟器端口。

3. 在 Windows 上运行 `ssh`，以在 Windows 上的本地端口（在此示例中为 `localhost:15555`）和 Mac 环回接口上的奇数模拟器端口（在此示例中为 `127.0.0.1:5555`）之间设置双向端口转发：

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    将 `mac-username` 替换为 `whoami` 列出的 Mac 用户名。 将 `ip-address-of-the-mac` 替换为 Mac 的 IP 地址。

4. 使用 Windows 上的本地端口连接到模拟器：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    （注意：获取对 `adb` 的命令行访问权限的一种简单方法是，通过 [Visual Studio 中的“工具”>“Android”>“Android Adb 命令提示符”](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。）

小警告：如果对本地端口使用端口 `5555`，`adb` 会认为模拟器在 Windows 上本地运行。 这在 Visual Studio 中不会导致任何问题发生，但在 Visual Studio for Mac 中，这会导致应用在启动后立即退出。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>尚不支持使用 `adb -H` 的替代技术

理论上，另一种方法是，使用 `adb` 的内置功能连接到远程计算机上运行的 `adb` 服务器（请参阅示例 [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)）。
不过，暂无法使用 Xamarin.Android IDE 扩展来配置相应选项。

## <a name="contact-information"></a>联系信息

本文档讨论了自 2016 年 3 月起至今的当前行为。 由于本文档中所述技术不属于 Xamarin 稳定测试套件，因此将来可能会出故障。

如果你注意到技术不再有效，或注意到本文档中有其他任何错误，请随时加入以下论坛线程的讨论：[http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
谢谢！
