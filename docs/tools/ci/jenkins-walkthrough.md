---
title: 结合使用 Jenkins 和 Xamarin
description: 本文档介绍了如何使用 Jenkins 与 Xamarin 应用程序进行持续集成。 它讨论了如何安装、配置和使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0ce1d4d0b74330b623b6d933e385222a71a38ec4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458155"
---
# <a name="using-jenkins-with-xamarin"></a>结合使用 Jenkins 和 Xamarin

_本指南说明如何将 Jenkins 设置为持续集成服务器，并自动编译使用 Xamarin 创建的移动应用程序。它介绍了如何在 OS X 上安装 Jenkins，如何对其进行配置，以及如何设置在将更改提交到源代码管理系统时编译 Xamarin 和 Xamarin Android 应用程序的作业。_

[与 Xamarin 持续集成的简介](~/tools/ci/intro-to-ci.md) 引入了持续集成作为一种有用的软件开发方案，该方案可提供中断或不兼容代码的早期警告。 CI 使开发人员能够在出现问题时解决问题和问题，并使软件处于适当的部署状态。 本演练介绍如何将这两个文档中的内容一起使用。

本指南演示如何在运行 OS X 的专用计算机上安装 Jenkins，并将其配置为在计算机启动时自动运行。 安装 Jenkins 后，我们将安装其他插件以支持 MS Build。 Jenkins 支持现成的 Git。 如果 TFS 正在用于源代码管理，则还必须安装其他插件和命令行实用工具。

配置 Jenkins 并安装任何所需插件后，我们将创建一个或多个作业以编译 Xamarin 和 Xamarin iOS 项目。 作业是执行一些工作所需的步骤和元数据的集合。 作业通常包括以下内容：

- **源代码管理 (SCM) ** –这是 Jenkins 配置文件中的元数据条目，其中包含有关如何连接到源代码管理和要检索的文件的信息。
- **触发器** –触发器用于基于特定操作启动作业，例如当开发人员将更改提交到源代码存储库时。
- **生成说明** -这是一个插件或脚本，它将编译源代码并生成可以安装在移动设备上的二进制文件。
- **可选的生成操作** -这可能包括运行单元测试、对代码执行静态分析、签名代码或启动其他作业来执行其他与生成相关的工作。
- **通知** -作业可以发送有关生成状态的某种通知。
- **安全性** –尽管是可选的，但强烈建议同时启用 Jenkins 安全功能。

本指南将指导如何设置涵盖每个点的 Jenkins 服务器。 最后，我们应该非常了解如何设置和配置 Jenkins，以便为 Xamarin mobile 项目创建 IPA 和 APK。

## <a name="requirements"></a>要求

理想的生成服务器是专门用于构建和测试应用程序的唯一目的的独立计算机。 专用计算机可确保其他角色（如 web 服务器) ）可能需要的项目不会对生成进行污染 (。 例如，如果生成服务器也充当 web 服务器，则 web 服务器可能需要某些公用库的冲突版本。 由于此冲突，web 服务器可能无法正常工作，或者 Jenkins 可能会创建部署到用户时不工作的生成。

Xamarin 移动应用生成服务器的设置与开发人员工作站非常类似。 它有一个用户帐户，将在该帐户中安装 Jenkins、Visual Studio for Mac 和 Xamarin 和 Xamarin。 所有代码签名证书、预配配置文件和密钥存储也必须安装。 *通常，生成服务器的用户帐户与开发人员帐户不同-请确保在用生成服务器用户帐户登录时安装和配置所有软件、密钥和证书。*

下图演示了典型的 Jenkins 生成服务器上的所有这些元素：

[![此关系图说明典型 Jenkins 生成服务器上的所有这些元素](jenkins-walkthrough-images/image1.png)](jenkins-walkthrough-images/image1.png#lightbox)

iOS 应用程序只能在运行 macOS 的计算机上生成和签名。 Mac 迷你是合理的低成本选项，但能够运行 OS X 10.10 (Yosemite) 或更高版本的计算机就已足够。

如果 TFS 正在用于源代码管理，则需要安装 [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/)。 Team Explorer Everywhere 提供对 macOS 中终端的 TFS 的跨平台访问。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安装 Jenkins

使用 Jenkins 的第一个任务是安装该任务。 有三种方法可以在 OS X 上运行 Jenkins：

- 作为后台运行的后台程序。
- 在 servlet 容器中，例如 Tomcat、Jetty 或 JBoss。
- 作为在用户帐户下运行的普通进程。

大多数传统的持续集成应用程序都在后台运行，作为 (OS X 或 nix 上的守护程序 \*) 或 Windows 上的服务 () 。 这适用于无需 GUI 交互的情况，以及可以轻松执行生成环境设置的情况。 移动应用还需要密钥库和签名证书，当 Jenkins 作为守护程序运行时，访问时可能会有问题。 由于这些问题，本文档重点介绍第三种方案，即在生成服务器上的用户帐户下运行 Jenkins。

Jenkins 是安装 Jenkins 的一种简便方法。 这是简化 Jenkins 服务器的启动和停止的 AppleScript 包装器。 Jenkins 在停靠中使用图标作为应用运行，而不是在 bash shell 中运行，如以下屏幕截图所示：

[![Jenkins 在停靠中使用图标作为应用运行，而不是在 bash shell 中运行，如以下屏幕截图所示](jenkins-walkthrough-images/image2.png)](jenkins-walkthrough-images/image2.png#lightbox)

启动或停止 Jenkins 非常简单，只是启动或停止 Jenkins。

若要安装 Jenkins，请从项目的下载页下载最新版本，如以下屏幕截图所示：

[![应用，从 "项目下载" 页下载最新版本，如以下屏幕截图所示](jenkins-walkthrough-images/image3.png)](jenkins-walkthrough-images/image3.png#lightbox)

将 zip 文件提取到 `/Applications` 生成服务器上的文件夹，并像任何其他 OS X 应用程序一样启动它。
首次启动 Jenkins 时，它将显示一个对话框，通知你它将下载 Jenkins：

[![应用，它将显示一个对话框，通知你将下载 Jenkins](jenkins-walkthrough-images/image4.png)](jenkins-walkthrough-images/image4.png#lightbox)

Jenkins 完成下载后，将显示另一个对话框，询问你是否要自定义 Jenkins 启动，如以下屏幕截图所示：

[![应用已完成下载，它将显示另一个对话框，询问你是否要自定义 Jenkins 启动，如以下屏幕截图所示](jenkins-walkthrough-images/image5.png)](jenkins-walkthrough-images/image5.png#lightbox)

自定义 Jenkins 是可选的，无需在每次启动应用时执行– Jenkins 的默认设置适用于大多数情况。

如果需要自定义 Jenkins，请单击 " **更改默认值** " 按钮。 这会显示两个连续的对话框：一个对话框要求提供 Java 命令行参数，另一个对话框要求提供 Jenkins 命令行参数。 以下两个屏幕截图显示了这两个对话框：

[![此屏幕截图显示了对话框](jenkins-walkthrough-images/image6.png)](jenkins-walkthrough-images/image6.png#lightbox)

[![此屏幕截图显示了对话框](jenkins-walkthrough-images/image7.png)](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 运行后，你可能想要将其设置为登录项，以便在每次用户登录到计算机时启动它。 为此，可以右键单击停靠中的 Jenkins 图标，然后选择 " **选项 ..." > 在登录时打开**，如以下屏幕截图所示：

[![若要执行此操作，请右键单击 Dock 中的 Jenkins 图标，然后选择 "登录时 OptionsOpen"，如以下屏幕截图所示](jenkins-walkthrough-images/image8.png)](jenkins-walkthrough-images/image8.png#lightbox)

这将导致 Jenkins 在用户每次登录时自动启动，但不会在计算机启动时自动启动。 可以指定 OS X 将用于在启动时自动登录的用户帐户。 打开 " **系统首选项**"，然后选择 " **用户 & 组** " 图标，如以下屏幕截图所示：

[![打开 "系统首选项"，然后选择 "用户组" 图标，如以下屏幕截图所示](jenkins-walkthrough-images/image9.png)](jenkins-walkthrough-images/image9.png#lightbox)

单击 " **登录选项** " 按钮，然后选择 OS X 将用于在启动时登录的帐户。

此时，已安装 Jenkins。 但是，如果我们想要生成 Xamarin 移动应用程序，则需要安装一些插件。

### <a name="installing-plugins"></a>安装插件

Jenkins 安装程序完成后，它将开始 Jenkins 并启动 web 浏览器 http://localhost:8080 ，如以下屏幕截图所示：

[![8080，如以下屏幕截图所示](jenkins-walkthrough-images/image10.png)](jenkins-walkthrough-images/image10.png#lightbox)

在此页上，从左上角的菜单中选择 " **Jenkins > 管理 Jenkins > 管理插件** "，如以下屏幕截图所示：

[![从此页中，从左上角的菜单中选择 "Jenkins" "管理插件"。](jenkins-walkthrough-images/image11.png)](jenkins-walkthrough-images/image11.png#lightbox)

此时将显示 " **Jenkins 插件管理器** " 页。 如果单击 "可用" 选项卡，则会看到一个列表，其中列出了可下载和安装的超过600个插件。 下面的屏幕截图中介绍了这一点：

[![如果单击 "可用" 选项卡，你会看到一个列表，其中列出了可下载和安装的超过600个插件](jenkins-walkthrough-images/image12.png)](jenkins-walkthrough-images/image12.png#lightbox)

滚动所有600的插件来找出少量的插件可能会很繁琐，而且容易出错。 Jenkins 在界面的右上角提供筛选器搜索字段。 使用此筛选器字段进行搜索可简化查找和安装以下一项或所有插件：

- **Jenkins MSBuild 插件** –此插件可让你 ( .sln) 和项目 ( .csproj) 构建 Visual Studio 和 Visual Studio for Mac 解决方案。
- **环境注入器插件** -这是一个可选但有用的插件，它可以在作业和生成级别设置环境变量。 它还为变量提供额外的保护，如用于对应用程序进行编码的密码。 它有时缩写为  *EnvInject 插件* 。
- **Team Foundation Server 插件** –这是一个可选的插件，仅当你使用 Team Foundation Server 或 Team Foundation service 进行源代码控制时才需要此插件。

Jenkins 支持 Git，无需任何额外的插件。

安装所有插件后，需要重新启动 Jenkins 并为每个插件配置全局设置。 可以通过从左上角选择 "Jenkins" > " **管理 Jenkins" > 配置系统** "来查找插件的全局设置，如以下屏幕截图中所示：

[![可以通过从左上角选择 Jenkins/Manage Jenkins/Configure System 来查找插件的全局设置](jenkins-walkthrough-images/image13.png)](jenkins-walkthrough-images/image13.png#lightbox)

选择此菜单选项时，将转到 " **配置系统 [Jenkins]** " 页。 此页面包含用于配置 Jenkins 本身并设置某些全局插件值的部分。  下面的屏幕截图演示了此页的一个示例：

[![此屏幕截图演示了此页面的示例](jenkins-walkthrough-images/image14.png)](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>配置 MSBuild 插件

必须将 MSBuild 插件配置为使用 **/Library/Frameworks/Mono.framework/Commands/xbuild** 来编译 Visual Studio for Mac 解决方案和项目文件。 向下滚动 **配置系统 [Jenkins]** 页，直到出现 " **添加 MSBuild** " 按钮，如以下屏幕截图中所示：

 [![在 "配置 System Jenkins" 页中向下滚动，直到出现 "添加 MSBuild" 按钮](jenkins-walkthrough-images/image15.png)](jenkins-walkthrough-images/image15.png#lightbox)

单击此按钮，并在显示的窗体上填写 " **MSBuild** " 字段的**名称**和**路径**。 **Msbuild**安装的名称应该有意义，而**msbuild 路径**应是指向的路径 `xbuild` ，通常为 **/Library/Frameworks/Mono.framework/Commands/xbuild**。 通过单击页面底部的 "保存" 或 "应用" 按钮保存更改后，Jenkins 将能够用于 `xbuild` 编译解决方案。

#### <a name="configuring-the-tfs-plugin"></a>配置 TFS 插件

如果要将 TFS 用于源代码管理，则本部分是必需的。

为了使 macOS 工作站与 TFS 服务器进行交互，必须在工作站上安装 [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) 。 Team Explorer Everywhere 是 Microsoft 提供的一套工具，其中包括用于访问 TFS 的跨平台命令行客户端。 可以从 Microsoft 下载 Team Explorer Everywhere，并通过三个步骤进行安装：

1. 将存档文件解压缩到用户帐户可以访问的目录。 例如，你可以将文件解压缩到 **~/tee**。
2. 将 shell 或系统路径配置为包含一个文件夹，该文件夹包含上述第一步中解压缩的文件。 例如，

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. 若要确认已安装 Team Explorer Everywhere，请打开终端会话，然后执行 `tf` 命令。 如果正确配置了 tf，你会在终端会话中看到以下输出：

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

安装 TFS 的命令行客户端后，必须将 Jenkins 配置为具有 `tf` 命令行客户端的完整路径。 在 " **配置系统 [Jenkins]** " 页中向下滚动，直到找到 "Team Foundation Server" 部分，如以下屏幕截图所示：

[![在 "配置 System Jenkins" 页中向下滚动，直到找到 "Team Foundation Server" 部分](jenkins-walkthrough-images/image17.png)](jenkins-walkthrough-images/image17.png#lightbox)

输入命令的完整路径 `tf` ，然后单击 " **保存** " 按钮。

### <a name="configure-jenkins-security"></a>配置 Jenkins 安全性

首次安装时，Jenkins 已禁用安全功能，因此任何用户都可以匿名设置和运行任意类型的作业。 本部分介绍如何使用 Jenkins 用户数据库配置安全性，以配置身份验证和授权。

可以通过选择 **Jenkins > 管理 Jenkins > 配置全局安全性**来找到安全设置，如以下屏幕截图所示：

[![可以通过选择 Jenkins/管理 Jenkins/配置全局安全性来找到安全设置](jenkins-walkthrough-images/image18.png)](jenkins-walkthrough-images/image18.png#lightbox)

在 " **配置全局安全性** " 页上，选中 " **启用安全性** " 复选框和 " **访问控制** " 窗体，如下所示：

[![在 "配置全局安全性" 页上，选中 "启用安全性" 复选框，应显示 "访问控制" 窗体，此屏幕截图类似于此屏幕截图](jenkins-walkthrough-images/image19.png)](jenkins-walkthrough-images/image19.png#lightbox)

在 "**安全领域" 部分**切换 " **Jenkins 的用户数据库"** 单选按钮，并确保还选中 "**允许用户注册**"，如以下屏幕截图中所示：

[![在 "安全领域" 部分切换 "Jenkins 用户数据库" 单选按钮，并确保还选中 "允许用户注册"](jenkins-walkthrough-images/image20.png)](jenkins-walkthrough-images/image20.png#lightbox)

最后，重新启动 Jenkins 并创建一个新帐户。 创建的第一个帐户是根帐户，此帐户将自动提升为管理员。 向后导航到 " **配置全局安全性** " 页，然后选中 " **基于矩阵的安全** " 单选按钮。 应授予根帐户完全访问权限，并且应为匿名帐户授予只读访问权限，如以下屏幕截图所示：

[![应为根帐户授予完全访问权限，并且应为匿名帐户授予只读访问权限](jenkins-walkthrough-images/image21.png)](jenkins-walkthrough-images/image21.png#lightbox)

保存这些设置并重新启动 Jenkins 后，安全将打开。

#### <a name="disabling-security"></a>禁用安全

如果忘记了密码或 Jenkins 的锁定，可以通过执行以下步骤来禁用安全性：

1. 停止 Jenkins。 如果你使用的是 Jenkins，则可以通过右键单击 Dock 中的 "Jenkins" 图标，然后从弹出菜单中选择 "退出" 来执行此操作：

    ![停靠中的应用图标，然后从弹出的菜单中选择 "退出"](jenkins-walkthrough-images/image19.png)

2. 在文本编辑器中打开文件 **~/.jenkins/config.xml** 。
3. 将元素的值 `<usesecurity></usesecurity>` 从更改 `true` 为 `false` 。
4. `<authorizationstrategy></authorizationstrategy>` `<securityrealm></securityrealm>` 从文件中删除和元素。
5. 重新启动 Jenkins。

## <a name="setting-up-a-job"></a>设置作业

在顶级，Jenkins 将构建软件所需的各种任务组织到一个 *作业*中。 作业还具有与之关联的元数据，提供有关生成的信息，例如如何获取源代码、生成的运行频率、生成所需的任何特殊变量以及如何在生成失败时通知开发人员。

可以通过在右上角的菜单中选择 " **Jenkins" > "新建作业** " 来创建作业，如以下屏幕截图所示：

![作业是通过在右上角菜单中选择 "Jenkins 新建作业" 来创建的](jenkins-walkthrough-images/image22.png)

这将显示 " **新建作业 [Jenkins]** " 页。 输入作业的名称，然后选择 " **生成自由格式的软件项目** " 单选按钮。 以下屏幕截图显示了一个示例：

![输入作业的名称，然后选择 "生成自由格式的软件项目" 单选按钮](jenkins-walkthrough-images/image23.png)

单击 **"确定"** 按钮将显示该作业的配置页。 这应类似于以下屏幕截图：

![这应类似于此屏幕截图](jenkins-walkthrough-images/image24.png)

Jenkins 在硬盘上的目录中组织作业，路径为： **~/.jenkins/jobs/[作业名称]**

此文件夹包含特定于作业的所有文件和项目，如日志、配置文件和需要编译的源代码。

初始作业创建完成后，必须将其配置为以下一项或多项：

- 必须指定源代码管理系统。
- 必须将一个或多个 *生成操作* 添加到项目。 这些是生成应用程序所需的步骤或任务。
- 必须为该作业分配一个 *生成触发器* –一组说明，通知 Jenkins 检索代码和生成最终项目的频率。

### <a name="configuring-source-code-control"></a>配置源代码管理

Jenkins 的第一个任务是检索源代码管理系统中的源代码。 Jenkins 支持目前可用的许多常用源代码管理系统。 本部分介绍了两个热门系统： Git 和 Team Foundation Server。 以下各节将更详细地讨论每个源代码管理系统。

#### <a name="using-git-for-source-code-control"></a>使用 Git 进行源代码管理

如果使用 TFS 进行源代码管理，请 [跳过](#using-tfs-for-source-code-management) 此部分，并使用 TFS 转到下一节。

Jenkins 支持现成的 Git –无需额外的插件。 若要使用 Git，请单击 **git** 单选按钮并输入 git 存储库的 URL，如以下屏幕截图所示：

![若要使用 Git，请单击 Git 单选按钮并输入 Git 存储库的 URL](jenkins-walkthrough-images/image25.png)

保存更改后，Git 配置完成。

#### <a name="using-tfs-for-source-code-management"></a>使用 TFS 进行源代码管理

本部分仅适用于 TFS 用户。

单击 " **Team Foundation Server** " 单选按钮，应显示 "TFS 配置" 部分，类似于以下屏幕截图所示：

![单击 "Team Foundation Server" 单选按钮，"TFS 配置" 部分应会出现](jenkins-walkthrough-images/image26.png)

提供 TFS 的必要信息。 以下屏幕截图显示了已完成表单的示例：

![此屏幕截图显示了已完成的窗体示例](jenkins-walkthrough-images/image27.png)

#### <a name="testing-the-source-code-control-configuration"></a>测试源代码管理配置

配置适当的源代码管理后，单击 " **保存** " 以保存所做的更改。 这会将你返回到作业的主页，这将与以下屏幕截图类似：

![这会将你返回到该作业的主页，这将与此屏幕截图类似](jenkins-walkthrough-images/image28.png)

验证源代码管理正确配置的最简单方法是手动触发生成，即使未指定任何生成操作也是如此。 若要手动启动生成，作业的 "主页 **" 链接位于** 左侧的菜单中，如下面的屏幕截图中所示：

![若要手动启动生成，作业的主页在左侧的菜单中有一个 "立即生成" 链接](jenkins-walkthrough-images/image29.png)

启动生成后，"生成历史记录" 对话框将显示一个闪烁的蓝色圆圈、一个进度栏、生成号和生成的开始时间，类似于以下屏幕截图：

![启动生成后，"生成历史记录" 对话框将显示一个闪烁的蓝色圆圈、一个进度栏、生成号和生成开始的时间。](jenkins-walkthrough-images/image30.png)

如果作业成功，将显示一个蓝色圆圈。 如果作业失败，将显示一个红色圆圈。

为了帮助解决在生成过程中可能出现的问题，Jenkins 将捕获作业的所有控制台输出。 若要查看控制台输出，请单击 **生成历史记录**中的作业，然后单击左侧菜单中的 " **控制台输出** " 链接。 以下屏幕截图显示了 **控制台输出** 链接，并显示了成功作业的一些输出：

![此屏幕截图显示了控制台输出链接，以及成功作业的一些输出](jenkins-walkthrough-images/image31.png)

#### <a name="location-of-build-artifacts"></a>生成项目的位置

Jenkins 会将整个源代码检索到一个名为 " *工作区*" 的特殊文件夹中。 可在以下位置的文件夹中找到此目录：

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

工作区的路径将存储在一个名为的环境变量中 `$WORKSPACE` 。

通过导航到作业的登陆页面，然后单击左侧菜单中的 " **工作区** " 链接，可以浏览 Jenkins 中的工作区文件夹。 以下屏幕截图显示了名为 **HelloWorld**的作业的工作区示例：

![此屏幕截图显示了名为 HelloWorld 的作业的工作区示例](jenkins-walkthrough-images/image32.png)

### <a name="build-triggers"></a>生成触发器

可以通过几种不同的策略来启动 Jenkins 中的生成，这些策略称为 " *生成触发器*"。 生成触发器有助于 Jenkins 决定何时启动作业并生成项目。 还有两个常见的生成触发器：

- **定期生成** –此触发器会导致 Jenkins 按指定的时间间隔（例如每两小时或每周午夜）启动作业。 无论源代码存储库中是否有任何更改，都将开始生成。
- **轮询 SCM** –此触发器将定期轮询源代码管理。 如果任何更改已提交到源代码存储库，Jenkins 将启动新的生成。

轮询 SCM 是一个流行的触发器，因为当开发人员提交导致生成中断的更改时，它可以提供快速反馈。 这对于通知团队，某些最近提交的代码会导致问题，并使开发人员能够解决问题，而这些更改仍需要全新进行。

定期生成通常用于创建可以分发给测试人员的应用程序版本。 例如，可以安排在星期五晚上计划定期生成，以便 QA 团队的成员可以测试上一周的工作。

### <a name="compiling-a-xamarinios-applications"></a>编译 Xamarin iOS 应用程序
可以使用或在命令行编译 Xamarin iOS 项目 `xbuild` `msbuild` 。 Shell 命令将在运行 Jenkins 的用户帐户的上下文中执行。 重要的是，用户帐户有权访问预配配置文件，以便可以正确打包应用程序进行分发。 可以将此 shell 命令添加到作业配置页。

向下滚动到 " **生成** " 部分。 单击 " **添加生成步骤** " 按钮，然后选择 " **执行 shell**"，如以下屏幕截图所示：

![单击 "添加生成步骤" 按钮，然后选择 "执行 shell"](jenkins-walkthrough-images/image33.png)

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>生成 Xamarin Android 项目

生成 Xamarin Android 项目在概念上非常类似于生成 Xamarin iOS 项目。 若要从 Xamarin Android 项目创建 APK，必须将 Jenkins 配置为执行以下两个步骤：

- 使用 MSBuild 插件编译项目
- 使用有效的 release 密钥，对 APK 进行签名和压缩。

接下来的两个部分将更详细地介绍这两个步骤。

### <a name="creating-the-apk"></a>创建 APK

单击 " **添加生成步骤** " 按钮，然后选择 " **使用 MSBuild 生成 Visual Studio 项目或解决方案**"，如下面的屏幕截图所示：

![创建 APK 单击 "添加生成步骤" 按钮，然后选择 "使用 MSBuild 生成 Visual Studio 项目或解决方案"](jenkins-walkthrough-images/image36.png)

将生成步骤添加到项目后，请填写显示的窗体字段。 以下屏幕截图是已完成表单的一个示例：

![将生成步骤添加到项目后，请填写显示的窗体字段](jenkins-walkthrough-images/image37.png)

此生成步骤将 `xbuild` 在 **$WORKSPACE** 文件夹中执行。 MSBuild 生成文件设置为 **Xamarin. .csproj** 文件。 **命令行参数**指定目标**PackageForAndroid**的发布版本。 此步骤的产品将是以下位置的 APK：

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

以下屏幕截图显示了此 APK 的示例：

![此屏幕截图显示了此 APK 的示例](jenkins-walkthrough-images/image38.png)

此 APK 尚未准备好进行部署，因为它尚未使用专用密钥存储进行签名，因此必须进行 zip 对齐。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>对 APK 进行签名和归档

对 APK 进行签名和归档在技术上是两个单独的任务，这些任务由 Android SDK 两个单独的命令行工具执行。 但是，在一个生成操作中执行这些操作非常方便。 有关签名和归档 APK 的详细信息，请参阅用于准备 Android 应用程序进行发布的 Xamarin 文档。

这两个命令都需要命令行参数，这些参数可能与项目不同。 此外，其中的某些命令行参数是在运行生成时不应显示在控制台输出中的密码。 我们会在环境变量中存储某些命令行参数。 下表描述了签名和/或 zip 对齐所需的环境变量：

|环境变量|描述|
|--- |--- |
|KEYSTORE_FILE|这是用于对 APK 进行签名的密钥存储的路径|
|KEYSTORE_ALIAS|用于对 APK 进行签名的密钥存储中的密钥。|
|INPUT_APK|由创建的 APK `xbuild` 。|
|SIGNED_APK|由生成的已签名 APK `jarsigner` 。|
|FINAL_APK|这是由生成的 zip 对齐 APK `zipalign` 。|
|STORE_PASS|这是用于访问唱歌文件的密钥存储的内容的密码。|

如 "要求" 部分所述，可以在生成过程中使用 EnvInject 插件设置这些环境变量。 应根据注入环境变量添加新的生成步骤，如以下屏幕截图所示：

![应根据注入环境变量添加新的生成步骤](jenkins-walkthrough-images/image39.png)

在将显示的 " **属性" 内容** 窗体字段中，按以下格式添加环境变量（每行一个）：

```
ENVIRONMENT_VARIABLE_NAME = value
```

以下屏幕截图显示了对 APK 进行签名所需的环境变量：

![此屏幕快照显示了对 APK 进行签名所需的环境变量](jenkins-walkthrough-images/image40.png)

请注意，APK 文件的某些环境变量是在环境变量上构建的 `WORKSPACE` 。

最终环境变量是用于访问密钥存储的内容的密码： `STORE_PASS` 。 密码是应在日志文件中隐藏或省略的敏感值。 可以将 EnvInject 插件配置为保护这些值，使其不显示在日志中。

紧跟在作业配置的 " **生成** " 部分之前的 " **生成环境** " 部分。 切换 " **插入密码** " 复选框时，将显示一些窗体字段。 这些窗体字段用于捕获环境变量的名称和值。 下面的屏幕截图是添加 `STORE_PASS` 环境变量的示例：

![此屏幕截图是添加 STOREPASS 环境变量的示例](jenkins-walkthrough-images/image41.png)

初始化环境变量后，下一步是添加用于签名的生成步骤，并使 APK 对齐。 紧跟在要插入环境变量的生成步骤之后，将执行和的另一个 **执行 shell** 命令 `jarsigner` 生成 `zipalign` 。 每个命令将占用一行，如以下代码片段所示：

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

以下屏幕截图显示了一个示例，说明如何在 `jarsigner` `zipalign` 步骤中输入和命令：

![此屏幕截图显示了如何将 jarsigner 和 zipalign 命令输入到步骤中的示例](jenkins-walkthrough-images/image42.png)

完成所有生成操作后，最好触发手动生成来验证所有内容是否正常工作。 如果生成失败，应查看 **控制台输出** ，了解导致生成失败的原因的信息。

### <a name="submitting-tests-to-test-cloud"></a>将测试提交到 Test Cloud

可以使用 shell 命令将自动测试提交到 Test Cloud。 有关在 Xamarin Test Cloud 中设置测试运行的详细信息，请参阅 [准备 Xamarin Android 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) 和 [准备 xamarin iOS 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)。

## <a name="summary"></a>总结

在本指南中，我们在 macOS 上引入了 Jenkins 作为生成服务器，并将其配置为编译和准备 Xamarin 移动应用程序以供发布。 我们在 macOS 计算机上安装了 Jenkins 以及多个插件来支持生成过程。 我们创建并配置了一个作业，该作业将从 TFS 或 Git 拉取代码，然后将该代码编译为发布就绪应用程序。 我们还探讨了两种不同的方法来安排作业的运行时间。

## <a name="related-links"></a>相关链接

- [持续集成](~/tools/ci/index.md)
- [App Center Test](/appcenter/test-cloud/)