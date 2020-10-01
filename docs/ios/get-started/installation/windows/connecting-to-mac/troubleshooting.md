---
title: Xamarin.iOS 生成主机连接疑难解答
description: 本指南针对使用新连接管理器时可能会遇到的问题（包括连接和 SSH 问题）提供疑难解答步骤。
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 56c4e9c5b0ca44500ca3930a63b300b6bd3fb33f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436598"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Xamarin.iOS 生成主机连接疑难解答

本指南针对使用新连接管理器时可能会遇到的问题（包括连接和 SSH 问题）提供疑难解答步骤。

## <a name="log-file-location"></a>日志文件位置

- **Mac** – `~/Library/Logs/Xamarin-[MAJOR.MINOR]`
- **Windows** – `%LOCALAPPDATA%\Xamarin\Logs`

可以通过在 Visual Studio 中浏览到“帮助”&gt;“Xamarin”&gt;“压缩日志”来查找日志文件。

## <a name="wheres-the-xamarin-build-host-app"></a>Xamarin 生成主机应用在什么位置？

不再需要较旧版本 Xamarin.iOS 中 Xamarin 生成主机。 Visual Studio 现在会自动通过远程登录部署代理并在后台运行它。 没有任何其他应用程序在 Mac 或 Windows 计算机上运行。

## <a name="troubleshooting-remote-login"></a>远程登录疑难解答

> [!IMPORTANT]
> 这些疑难解答步骤主要针对在新系统上进行初始设置的过程中发生的问题。  如果以前在特定环境中已成功使用连接，然后连接突然或间歇性停止工作，则可以（在大多数情况下）直接跳过以检查以下任一情况看是否有帮助： 
>
> - 按如下所述[由于现有生成主机进程而导致的错误](#errors)，终止剩余进程。 
> - 按如下所述[清除中转站、IDB、生成和设计器代理](#clearing)清除代理，然后按如下所述[无法连接到 MacBuildHost。请重试。](#tryagain)使用有线 Internet 连接并通过 IP 地址直接连接。  
> 如果这些选项都不能解决问题，请按照[步骤 9](#stepnine) 中的说明提交新的 bug 报告。

1. 检查是否在 Mac 上安装了兼容 Xamarin.iOS 版本。 若要使用 Visual Studio 2017 执行此操作，请确保你位于 Visual Studio for Mac 中稳定的分发通道上。 在 Visual Studio 2015 及更早版本中，请确保在两个 IDE 中都处于相同分发通道上。
    - 在 Visual Studio for Mac 中，转到“Visual Studio for Mac”>“检查更新...”，以查看或更改“更新通道”。
    - 在 Visual Studio 2015 及更低版本中，依次转到“工具”>“选项”>“Xamarin”>“其他”下，检查分发通道。

2. 确保在 Mac 上启用“远程登录”。 为“仅这些用户”设置访问权限，并确保 Mac 用户包含在列表或组中：

    [![为“仅这些用户”设置访问权限](troubleshooting-images/troubleshooting-image1.png)](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. 检查防火墙是否允许通过端口 22（用于 SSH 的默认端口）进行传入连接：

    [![检查防火墙是否允许通过端口 22 的传入连接](troubleshooting-images/troubleshooting-image2.png)](troubleshooting-images/troubleshooting-image2.png#lightbox)

    如果禁用了“自动允许已签名的软件接收传入连接”，则 OS X 会在配对过程中提供一个对话框，询问是否允许 `mono-sgen` 或 `mono-sgen32` 接收传入连接。 请务必在该对话框中单击“允许”：

    [![在此对话框上单击“允许”](troubleshooting-images/troubleshooting-image4a.png)](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. 确认已登录到该 Mac 上的用户帐户，并且具有活动 GUI 会话。

5. 确保使用_用户名_ 而不是_全名_ 连接到 Mac。 这样可避免针对包含带重音字符的全名的已知限制。

    可以通过在 **Terminal.app** 中运行 `whoami` 命令来查找_用户名_。

    例如，在下面的屏幕截图中，帐户名称是“amyb”，而不是“Amy Burns”：

    [![从终端应用获取帐户名称](troubleshooting-images/troubleshooting-image5a.png)](troubleshooting-images/troubleshooting-image5a.png#lightbox)

6. 检查用于 Mac 的 IP 地址是否正确。 可以在 Mac 上依次转到“系统偏好设置”>“共享”>“远程登录”下，查找 IP 地址。

    [![“系统首选项”应用中的 IP 地址](troubleshooting-images/troubleshooting-image17.png)](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. 确认了 Mac 的 IP 地址之后，尝试在 Windows 上的 `cmd.exe` 中对该地址执行 `ping` 操作：

    ```
    ping 10.1.8.95
    ```
    
    如果 ping 失败，则不可从 Windows 计算机_路由_ 到 Mac。 该问题需要在 2 台计算机之间的局域网配置级别上进行解决。 确保两台计算机位于同一本地网络。

8. 接下来，测试来自 OpenSSH 的 `ssh` 客户端是否可以从 Windows 成功连接到 Mac。 安装此程序的一种方法是安装[适用于 Windows 的 Git](https://git-for-windows.github.io/)。 然后可以启动 **Git Bash** 命令提示，并尝试使用用户名和 IP 地址对 Mac 执行 `ssh` 操作：

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine"></a>

9. 如果**步骤 8 成功**，则可以尝试通过连接运行简单命令（如 `ls`）：

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    这应列出 Mac 上的主目录的内容。 如果 `ls` 命令可正常运行，但 Visual Studio 连接仍失败，则可以查看有关特定于 Xamarin 的复杂问题的“[已知问题和限制](#knownissues)”部分。 如果找不到所需问题，请在 Visual Studio 中转到“帮助”>“发送反馈”>“报告问题”，提交新的 Bug 报告并附加[检查详细日志文件](#verboselogs)下所述的日志。

10. 如果**步骤 8 失败**，则可以在 Mac 上的终端中运行以下命令，以查看 SSH 服务器是否接受_任何_ 连接：

    ```bash
    ssh localhost
    ```
    
11. 如果步骤 8 失败，但**步骤 10 成功**，则问题很可能是由于网络配置而无法从 Windows 访问 Mac 生成主机上的端口 22。 可能配置问题包括：

    - OS X 防火墙设置禁止连接。 请务必仔细检查步骤 3。

        有时 OS X 防火墙的每应用配置也可能会在无效状态下结束，在这种情况下，显示在“系统首选项”中的设置不反映实际行为。 删除配置文件 ( **/Library/Preferences/com.apple.alf.plist**) 并重新启动计算机可以帮助还原默认行为。 删除该文件的一种方法是在查找工具中的“转到”&gt;“转到文件夹”下进入 **/Library/Preferences**，然后将 **com.apple.alf.plist** 文件移动到回收站。

    - Mac 与 Windows 计算机之间的路由器之一的防火墙设置阻止连接。

    - Windows 本身禁止与远程端口 22 之间的出站连接。 这是不寻常的。 可以将 Windows 防火墙配置为禁止出站连接，但默认设置是允许所有出站连接。

    - Mac 生成主机通过 `pfctl` 规则禁止从所有外部主机访问端口 22。 这是不可能的，除非你知道已在过去配置了 `pfctl`。

12. 如果步骤 8 失败并且**步骤 10 失败**，则问题可能是 Mac 上的 SSH 服务器进程未在运行或未配置为允许当前用户登录。 在这种情况下，请务必在调查任何更复杂的可能性之前，仔细检查步骤 2 中的远程登录设置。

<a name="knownissues"></a>

### <a name="known-issues-and-limitations"></a>已知问题和限制

> [!NOTE]
> 仅当你已使用 OpenSSH SSH 客户端，通过你的 Mac 用户名和密码成功连接到 Mac 生成主机（如上面的步骤 8 和 9 所述）时，此部分才适用。

#### <a name="invalid-credentials-please-try-again"></a>“凭据无效。 请重试。”

已知原因：

- **限制** – 如果名称包含带重音字符，则在尝试使用帐户_全名_ 登录到生成主机时可能会出现此错误。 这是 Xamarin 用于 SSH 连接的 [SSH.NET 库](https://sshnet.codeplex.com/)的一个限制。 **解决方法**：请参阅上面的步骤 5。

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>“无法使用 SSH 密钥进行身份验证。 请首先尝试使用凭据登录”

已知原因：

- **SSH 安全限制** – 此消息通常表示 Mac 上 **$HOME/.ssh/authorized\_keys** 完全限定路径中的文件或目录之一对_其他_ 或_组_ 成员启用了写入权限。 **常用修复方法**：在 Mac 上的终端命令提示中运行 `chmod og-w "$HOME"`。 有关导致该问题的特定文件或目录的详细信息，请在终端中运行 `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"`，然后从桌面打开 **sshd.log** 文件并查找“拒绝身份验证: 所有权或模式错误”。

#### <a name="trying-to-connect-never-completes"></a>一直停留在“正在尝试连接...”

- **Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** – 如果在“系统首选项”&gt;“用户”&amp;“组”中，Mac 用户的“高级选项”上下文菜单内的“登录 shell”设置为 **/bin/bash** 以外的值，则 Xamarin 4.1 上可能出现此问题。 （自 Xamarin 4.2 起，此方案转而会导致“无法连接”错误消息。）**解决方法**：将“登录 shell”更改回原始默认值 **/bin/bash**。

<a name="tryagain"></a>

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>“无法连接到 MacBuildHost.local。 请重试。”

报告的原因：

- **Bug** - 一些用户在尝试使用 Active Directory 或其他目录服务域用户帐户登录到生成主机时，遇到了此错误消息，并且在日志文件中有更详细的错误”为用户配置 SSH 时发生意外错误...会话操作已超时”。 **临时解决方法：** 改为使用本地用户帐户登录到生成主机。

- **Bug** – 一些用户在尝试通过双击连接对话框中的 Mac 名称来连接到生成主机时遇到了此错误。 **可能的解决方法**：使用 IP 地址[手动添加 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac)。

- **Bug [#35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)** – 一些用户在 Mac 生成主机与 Windows 之间使用无线网络连接时遇到了此错误。 **可能的解决方法**：将两台计算机移动到有线网络连接。

- **Bug [#36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)** – 在 Xamarin 4.0 中，只要 Mac 上的 **$HOME/.bashrc** 文件包含错误，此消息便会出现。 （从 Xamarin 4.1 开始， **.bashrc** 文件中的错误将不再影响连接过程。）**解决办法**：将 .bashrc 文件移动到备份位置（或是在知道不再需要它时删除它）。

- Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264) - 如果在“系统偏好设置”>“用户和组”中，Mac 用户的“高级选项”上下文菜单中的“登录 shell”设置为除“/bin/bash”以外的值，可能会出现此错误。 **解决方法**：将“登录 shell”更改回原始默认值 **/bin/bash**。

- **限制** – 如果 Mac 生成主机连接到无法访问 Internet 的路由器（或如果 Mac 使用的 DNS 服务器在系统要求进行 Windows 计算机的反向 DNS 查找时超时），则可能会出现此错误。 Visual Studio 需要大约 30 秒来检索 SSH 指纹，最终未能连接。

    **可能的解决方法**：将“UseDNS no”添加到 sshd\_config 文件。 请务必在进行更改之前查看此 SSH 设置。 请参阅示例 [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](https://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option)。

    以下步骤描述更改该设置的一种方法。 你需要在 Mac 上登录到管理员帐户以完成这些步骤。

    1. 通过在终端命令提示中运行 `ls /etc/ssh/sshd_config` 和 `ls /etc/sshd_config` 来确认 **sshd\_config** 文件的位置。 对于所有其余步骤，请务必使用_不_ 返回“No such file or directory”的位置。

        [![在终端中运行 `ls /etc/ssh/sshd_config` 和 `ls /etc/sshd_config`](troubleshooting-images/troubleshooting-image18.png)](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. 在终端中运行 `cp /etc/ssh/sshd_config "$HOME/Desktop/"` 以将文件复制到桌面。

    3. 在文本编辑器中从桌面打开文件。 例如，可以在终端中运行 `open -a TextEdit "$HOME/Desktop/sshd_config"`。

    4. 在文件末尾添加以下行：

        ```
        UseDNS no
        ```

    5. 删除显示 `UseDNS yes` 的所有行以确保新设置生效。

    6. 保存该文件。

    7. 在终端中运行 `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` 以将编辑过的文件复制回原位。 在出现提示时输入密码。

    8. 在“系统首选项”&gt;“共享”&gt;“远程登录”下禁用并重新启用“远程登录”以重新启动 SSH 服务器。

<a name="clearing"></a>

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>在 Mac 上清除代理、IDB、生成和设计器代理

如果日志文件对于任何 Mac 代理在“安装”、“上传”或“启动”步骤期间显示问题，则可以尝试删除 **XMA** 缓存文件夹以强制 Visual Studio 重新上传它们。

1. 在 Mac 上的终端中运行以下命令：

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. 在按住 Control 的同时单击 **XMA** 文件夹并选择“移动到回收站”：

    [![将 XMA 文件夹移至“回收站”](troubleshooting-images/troubleshooting-image8.png)](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. Windows 上有一个缓存，可帮助进行清除。 在 Windows 上以管理员身份打开命令提示符：

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>警告消息

此部分讨论可能会出现在“输出”窗口和日志中，不过通常可以忽略的几个消息。

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>“在安装的 Xamarin.iOS … 与本地 Xamarin.iOS 之间存在不匹配”

只要已确认 Mac 和 Windows 都更新到相同 Xamarin 分发通道，便可以忽略此警告。

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>“未能执行 'ls /usr/bin/mono': ExitStatus=1"

只要 Mac 在运行 OS X 10.11 (El Capitan) 或更高版本，便可以忽略此消息。 此消息在 OS X 10.11 上不是什么问题，因为 Xamarin 还会检查 **/usr/local/bin/mono**，这是 `mono` 在 OS X 10.11 上的正确预期位置。

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>“Bonjour 服务 'MacBuildHost' 未使用其 IP 地址进行响应。”

除非注意到连接对话框不显示 Mac 生成主机的 IP 地址，否则可以忽略此消息。 如果在该对话框中_缺少_ IP 地址，则仍可以[手动添加 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac)。

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>“来自 10.1.8.95 的用户无效”和“input\_userauth\_request: 无效用户 a [预身份验证]”

如果你查看 **sshd.log** 文件，则可能会注意到此消息。 这些消息是正常连接过程的一部分。 它们的出现原因是 Xamarin 在检索 _SSH 指纹_ 时会暂时使用用户名 **a**。

### <a name="output-window-and-log-files"></a>“输出”窗口和日志文件

如果 Visual Studio 在连接到生成主机时遇到错误，则可以在 2 个位置检查其他消息：“输出”窗口和日志文件。

#### <a name="output-window"></a>输出窗口

“输出”窗口是最佳开始位置。 它显示有关主要连接步骤和错误的消息。 在“输出”窗口中查看 Xamarin 消息：

1. 从菜单中选择“视图”>“输出”或单击“输出”选项卡。
2. 单击“显示输出来源”下拉菜单。
3. 选择“Xamarin”。

[![在“输出”选项卡中选择“Xamarin”](troubleshooting-images/troubleshooting-image11.png)](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>日志文件

如果“输出”窗口未包含足够信息来诊断问题，则日志文件是要查看的下一个位置。 日志文件包含不会出现在“输出”窗口中的其他诊断消息。 查看日志文件：

1. 启动 Visual Studio。

    > [!IMPORTANT]
    > 请注意，**s**.svclog 默认为不启用。 若要进行访问，需要启动具有[版本日志](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs)指南中所述的详细日志的 Visual Studio。 有关详细信息，请参阅博客文章：[Troubleshooting Extensions with the Activity Log](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/)（使用活动日志对扩展进行故障排除）。

2. 尝试连接到生成主机。

3. Visual Studio 遇到连接错误之后，会从“帮助”>“Xamarin”>“压缩日志”收集日志：

    [![从“帮助”>“Xamarin”>“压缩日志”收集日志](troubleshooting-images/troubleshooting-image12.png)](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. 打开 .zip 文件时，你会看到类似于以下示例的文件的列表。 对于连接错误，最重要的文件是 **\*Ide.log** 和 **\*Ide.svclog** 文件。 这两个文件以两种略有不同的格式包含相同消息。 **.svclog** 是 XML，在要浏览消息时非常有用。 **.log** 是纯文本，在你要使用命令行工具筛选消息时非常有用。

    若要浏览所有消息，请选择并打开 **.svclog** 文件：

    [![选择 svclog 文件](troubleshooting-images/troubleshooting-image13.png)](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. **.svclog** 文件会在 **Microsoft Service Trace Viewer** 中打开。 可以按线程浏览消息以查看相关消息组。 若要按线程进行浏览，请首先选择“图表”选项卡，单击“布局模式”下拉菜单，然后选择“线程”：

    [![单击“布局模式”下拉菜单并选择“线程”](troubleshooting-images/troubleshooting-image14.png)](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs"></a>

#### <a name="verbose-log-files"></a>详细日志文件

如果常规日志文件仍未提供足够信息来诊断问题，则要尝试的最后一种方法是启用详细日志记录。 详细日志在 bug 报告中也是首选的。

1. 退出 Visual Studio。

2. 启动[**开发人员命令提示**](/dotnet/framework/tools/developer-command-prompt-for-vs)。

3. 在命令提示中运行以下命令，以启动具有详细日志记录的 Visual Studio：

    ```bash
    devenv /log
    ```

4. 尝试从 Visual Studio 连接到生成主机。

5. 在 Visual Studio 遇到连接错误后，依次转到“帮助”>“Xamarin”>“压缩日志”，收集日志。

6. 通过在 Mac 上的终端中运行以下命令，从 SSH 服务器将所有最近的日志消息复制到桌面上的文件中：

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

如果这些详细日志文件未提供足够线索来直接解决该问题，请[提交新 bug 报告](https://bugzilla.xamarin.com/newbug)并附加来自步骤 5 的 .zip 文件和来自步骤 6 的 .log 文件。

## <a name="troubleshooting-automatic-mac-provisioning"></a>自动预配 Mac 疑难解答

### <a name="ide-log-files"></a>IDE 日志文件

如果在使用[自动预配 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) 的过程中遇到任何问题，请查看存储在 %LOCALAPPDATA%\Xamarin\Logs\15.0 中的 Visual Studio 2017 IDE 日志。

## <a name="troubleshooting-build-and-deployment-errors"></a>生成和部署错误疑难解答

此部分介绍在 Visual Studio 成功连接到生成主机之后可能发生的一些问题。

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>“无法使用用户 'macuser' 连接到地址 '192.168.1.2:22'”

已知原因：

- **Xamarin 4.1 安全功能** – 如果在使用 Xamarin 4.1 或更高版本之后降级到 Xamarin 4.0，则_会_ 发生此错误。 在这种情况下，会伴随错误出现附加警告“私钥已加密，但密码为空”。 这是由于 Xamarin 4.1 中的新安全功能而_有意_ 进行的更改。 **推荐的修复方法**：从 %LOCALAPPDATA%\Xamarin\MonoTouch 中删除 id\_rsa 和 id\_rsa.pub，然后重新连接到 Mac 生成主机。

- **SSH 安全限制** – 当伴随此消息出现附加警告“无法使用现有 ssh 密钥对用户进行身份验证”时，它通常表示 Mac 上 **$HOME/.ssh/authorized\_keys** 完全限定路径中的文件或目录之一对_其他_ 或_组_ 成员启用了写入权限。 **常用修复方法**：在 Mac 上的终端命令提示中运行 `chmod og-w "$HOME"`。 有关导致该问题的特定文件或目录的详细信息，请在终端中运行 `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"`，然后从桌面打开 **sshd.log** 文件并查找“拒绝身份验证: 所有权或模式错误”。

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>无法从网络共享位置加载解决方案

仅当解决方案位于本地 Windows 文件系统或映射驱动器上时，它们才会进行编译。

保存在网络共享中的解决方案可能会引发错误，或完全拒绝进行编译。 Visual Studio 中使用的所有 .sln 文件都应保存到本地 Windows 文件系统中。

此问题会引发以下错误：

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

相关 bug：[#36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>缺少设置配置文件或“未能创建 the a fat 库”错误

在 Mac 上启动 Xcode，并确保你的 Apple 开发人员帐户已登录并且你的 iOS 开发配置文件已下载：

[![确保登录 Apple 开发者帐户并且下载 iOS 开发配置文件](troubleshooting-images/troubleshooting-image7.png)](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>“向一个无法连接的网络尝试了一个套接字操作”

报告的原因：

- **增强功能 [#36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)** – 当 Visual Studio 使用 IPv6 地址连接到生成主机时，此错误可能会阻止成功生成。 （生成主机连接尚不支持 IPv6 地址。）

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>Xamarin.iOS Visual Studio 插件在 beta/alpha 通道重新安装之后未能加载

相关 bug [#40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781)。

当 Visual Studio 未能刷新 MEF 组件缓存时，可能会发生此问题。 如果是这种情况，安装此 Visual Studio 扩展可能会有所帮助：[https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)

这会清除 Visual Studio MEF 组件缓存以修复与缓存损坏有关的问题。

<a name="errors"></a>

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>由于 Mac 上的现有生成主机进程而导致的错误

来自以前生成主机连接的进程有时可能会干扰当前活动连接的行为。 若要检查是否存在任何现有进程，请关闭 Visual Studio，然后在 Mac 上的终端中运行以下命令：

```bash
ps -A | grep mono
```

[![在 Mac 上的终端中运行命令](troubleshooting-images/troubleshooting-image10.png)](troubleshooting-images/troubleshooting-image10.png#lightbox)

若要终止现有过程，请使用以下命令：

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>清除 Mac 生成缓存

如果对生成问题进行疑难解答，并且要确保行为与存储在 Mac 上的任何临时生成文件不相关，则可以删除生成缓存文件夹。

1. 在 Mac 上的终端中运行以下命令：

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. 在按住 Control 的同时单击 **mtbs** 文件夹并选择“移动到回收站”：

    [![将 mtbs 文件夹移至“回收站”](troubleshooting-images/troubleshooting-image9.png)](troubleshooting-images/troubleshooting-image9.png#lightbox)

## <a name="related-links"></a>相关链接

- [与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Xamarin Mac 生成代理视频](https://www.youtube.com/watch?v=MBAPBtxkjFQ)