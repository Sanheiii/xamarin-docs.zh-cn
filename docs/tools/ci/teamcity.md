---
title: 将团队城市与 Xamarin 配合使用
description: 本指南将讨论使用 TeamCity 编译移动应用程序并将其提交到 App Center 测试所涉及的步骤。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: a7118cc3ce9c0a5c4254bb5921800fe38acd85a0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458220"
---
# <a name="using-team-city-with-xamarin"></a>将团队城市与 Xamarin 配合使用

_本指南将讨论使用 TeamCity 编译移动应用程序并将其提交到 App Center 测试所涉及的步骤。_

如 [持续集成指南简介](~/tools/ci/intro-to-ci.md) 中所述，持续集成 (CI) 在开发高质量移动应用程序时非常有用。 对于持续集成服务器软件，有许多可行的选项;本指南将重点介绍 JetBrains 中的 [TeamCity](https://www.jetbrains.com/teamcity/) 。

TeamCity 安装有多个不同的排列。 以下列表描述了其中一些排列：

- **Windows 服务** –在此方案中，当 Windows 作为 windows 服务启动时，TeamCity 启动。 它必须与 Mac 生成主机配对，才能编译任何 iOS 应用程序。

- **在 OS X 上启动守护** 程序-从概念上讲，这类似于在上一步中描述的 Windows 服务运行。 默认情况下，生成将在根帐户下运行。

- **OS X 上的用户帐户** –可以在每次用户登录时启动的用户帐户下运行 TeamCity。

在前面的方案中，在 OS X 的用户帐户下运行 TeamCity 是最简单且最容易设置的。

设置 TeamCity 涉及多个步骤：

- **安装 TeamCity** –本指南未介绍如何安装 TeamCity。 本指南假定 TeamCity 已安装，并且在用户帐户下运行。 有关 [安装 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) 的说明，请参阅 JetBrains 的 [TeamCity 8 文档](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) 。

- **准备生成服务器** –此步骤涉及安装生成移动应用程序所需的软件、工具和证书，并准备好它们以进行分发。

- **创建生成脚本** –此步骤不是必需的，但生成脚本是用于以无人参与方式构建应用程序的有用帮助。 使用生成脚本可帮助解决可能出现的生成问题，并提供一致的、可重复的方式来创建要分发的二进制文件，即使不会进行持续集成。

- **创建 TeamCity 项目** –在完成上述三个步骤之后，必须创建一个 TeamCity 项目，该项目将包含检索源代码、编译项目和将测试提交到 App Center 测试所需的所有元数据。

## <a name="requirements"></a>要求

需要 [App Center 测试](/appcenter/test-cloud/) 的经验。

需要熟悉 TeamCity 8.1。 TeamCity 的安装超出了本文档的范围。 假设 TeamCity 安装在 OS X Mavericks 上，并且在普通用户帐户下运行，而不是在根帐户下运行。

生成服务器应该是运行 OS X 的独立计算机，专用于持续集成。 理想情况下，生成服务器不负责任何其他角色，如数据库服务器、web 服务器或开发人员工作站。

> [!IMPORTANT]
> 本指南不包括 Xamarin 的 "无外设" 安装。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>准备生成服务器

配置生成服务器的一个关键步骤是安装所有必需的工具、软件和证书以构建移动应用程序。 生成服务器可以编译移动解决方案并运行所有测试，这一点很重要。 若要最大程度地减少配置问题，应在托管 TeamCity 的同一用户帐户中安装软件和工具。 以下列表详细说明了所需内容：

1. **Visual Studio for Mac** –其中包括 Xamarin 和 xamarin。
2. **登录到 Xamarin 组件存储** –此步骤是可选的，仅当应用程序使用 Xamarin 组件存储中的组件时才是必需的。 此时主动登录到组件存储会阻止 TeamCity 生成尝试编译应用程序时出现的任何问题。
3. **Xcode** –对 iOS 应用程序进行编译和签名需要 Xcode。
4. **Xcode 命令行工具** –在 [更新 Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki) 指南的 "安装" 部分的步骤1中进行了介绍。
5. **签名身份 & 预配配置文件** –通过 XCode 导入证书和预配配置文件。 有关更多详细信息，请参阅 [导出签名标识和预配配置文件](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) 的 Apple 指南。
6. **Android 密钥库** –将所需的 Android 密钥库复制到 TeamCity 用户有权访问的目录， `~/Documents/keystores/MyAndroidApp1` 即。
7. **Calabash** -如果你的应用程序具有使用 Calabash 编写的测试，则这是一个可选步骤。 有关详细信息，请参阅在 [OS X Mavericks 指南中安装 Calabash](https://github.com/calabash/calabash-ios/wiki) 和 [更新 Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki) 指南。

下图演示了这些组件的所有组件：

![此图说明了所有这些组件](teamcity-images/image1.png)

安装所有软件后，登录到该用户帐户，并确认所有软件都已正确安装并正常工作。 这应涉及到编译解决方案并提交应用程序以 App Center 测试。 这可以通过运行生成脚本来简化，如下一节中所述。

## <a name="create-a-build-script"></a>创建生成脚本

尽管 TeamCity 可以处理编译和提交移动应用程序的各个方面，才能自行 App Center 测试;建议创建生成脚本。 生成脚本具有以下优势：

1. **文档** –生成脚本是一种用于构建软件的方式的文档。 这消除了与部署应用程序相关的一些 "神奇"，并允许开发人员将精力集中在功能上。
1. **可重复性** –一个生成脚本可确保每次编译和部署应用程序时都以相同方式发生，而不管工作是谁或什么。 这种可重复的一致性可消除由于错误执行的生成或人为错误而可能出现的任何问题或错误。
1. **版本控制** -可在源代码管理系统中包含生成脚本。 这意味着，如果发现错误或错误，可以跟踪、监视和更正对生成脚本所做的更改。
1. **准备环境** –生成脚本可包含用于安装任何所需的第三方依赖项的逻辑。 这将确保用适当的组件生成应用程序。

生成脚本可以像 Windows) 上的 PowerShell 文件 (一样简单，也可以在 OS X) 上 (bash 脚本。 创建生成脚本时，有几种脚本语言选项可供选择：

- [**Rake**](https://github.com/jimweirich/rake) –这是一种特定于域的语言 (DSL) 用于根据 Ruby 生成项目。 Rake 的优势在于普及和丰富的库生态系统。

- [**psake**](https://github.com/psake/psake) –这是用于构建软件的 Windows PowerShell 库

- [**虚设**](https://fsharp.github.io/FAKE/) –这是一个基于 F # 的 DSL，如有必要，可以使用现有的 .net 库。

使用哪种脚本语言取决于您的首选项和要求。

> [!NOTE]
> 可以使用基于 XML 的生成系统（如 MSBuild 或 NAnt），但这缺乏专用于构建软件的 DSL 的表现力和可维护性。

### <a name="parameterizing-the-build-script"></a>参数化生成脚本

构建和测试软件的过程需要保密的信息。 创建 APK 可能需要用于密钥存储的密码和/或密钥存储中的密钥别名。 同样，App Center 测试需要一个对开发人员唯一的 [API 密钥](/appcenter/api-docs/) 。 在生成脚本中，这些类型的值不应进行硬编码。 相反，它们应作为变量传递到生成脚本。

不太敏感是一些值，例如 iOS 设备 ID 或 Android 设备 ID，用于标识 App Center 应用于测试运行的设备。 这不是需要保护的值，但它们可能会因生成而发生更改。

将这些类型的变量存储在生成脚本之外还可以更轻松地与开发人员一起共享组织内的生成脚本。 开发人员可以使用与生成服务器完全相同的脚本，但可以使用自己的密钥库和 [API 密钥](/appcenter/api-docs/)。

可以通过两种方法存储这些敏感值：

- **配置文件** -若要保护 [API 密钥](/appcenter/api-docs/) ，此值不应签入到版本控制中。 可以为每台计算机创建该文件。 从此文件中读取值的方式取决于所使用的脚本语言。

- **环境变量** –可以在每台计算机上轻松设置这些变量，并且这些变量独立于基础脚本语言。

每种选择都有各自的优点和缺点。 TeamCity 适用于环境变量，因此本指南将在创建生成脚本时建议使用此方法。

### <a name="build-steps"></a>生成步骤

生成脚本必须执行以下步骤：

- **编译应用程序** –这包括通过正确的预配配置文件对应用程序进行签名。

- 将**应用程序提交到 Xamarin Test Cloud** –这包括签名和 ZIP 将 APK 与适当的密钥存储进行对齐。

下面将更详细地介绍这两个步骤。

#### <a name="compiling-a-xamarinios-application"></a>编译 Xamarin iOS 应用程序

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>编译 Xamarin Android 应用程序

若要编译 Android 应用程序，请在 Windows 上使用 **xbuild** (或 **msbuild**) ：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
编译 Android 应用程序 **xbuild** 使用项目，而 iOS 应用程序 **xbuild** 使用该解决方案。

#### <a name="submitting-xamarinuitests-to-app-center"></a>正在将 Uitest 提交到 App Center

Uitest 使用 [APP CENTER CLI](https://github.com/microsoft/appcenter-cli)进行提交，如以下代码片段所示：

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

当测试运行时，测试结果将以名为 **report.xml**的 NUNIT 样式 XML 文件的形式返回。 TeamCity 会在生成日志中显示信息。

有关如何将 Uitest 提交到 App Center 的详细信息，请参阅 [准备 Xamarin Android 应用](/appcenter/test-cloud/uitest/preparing-for-upload-android) 或 [准备 xamarin iOS 应用](/appcenter/test-cloud/uitest/preparing-for-upload-ios)。

#### <a name="submitting-calabash-tests-to-app-center"></a>将 Calabash 测试提交到 App Center

Calabash 测试是使用 [APP CENTER CLI](https://github.com/microsoft/appcenter-cli)提交的，如以下代码片段所示：

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

若要提交 Android 应用程序以 App Center 测试，必须先使用 calabash-Android 重新生成 APK 测试服务器：

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

有关提交 Calabash 测试的详细信息，请参阅 [将 Calabash 测试提交到 Test Cloud](https://github.com/calabash/calabash-ios/wiki)的 Xamarin 指南。

## <a name="creating-a-teamcity-project"></a>创建 TeamCity 项目

安装 TeamCity 并 Visual Studio for Mac 可以生成项目后，便可以在 TeamCity 中创建项目以生成项目并将其提交到 App Center。

1. 通过 web 浏览器登录到 TeamCity 开始。 导航到根项目：

    ![导航到根项目](teamcity-images/image2.png "导航到根项目") 在根项目下，创建一个新的子项目：

    ![导航到根项目下面的根项目，创建一个新的子项目](teamcity-images/image3.png "导航到根项目下面的根项目，创建一个新的子项目")
2. 创建子项目后，添加新的生成配置：

    ![创建子项目后，添加新的生成配置](teamcity-images/image5.png "创建子项目后，添加新的生成配置")
3. 将 VCS 项目附加到生成配置。 此操作通过 "版本控制" 设置屏幕完成：

    ![此操作通过 "版本控制" 设置屏幕来完成](teamcity-images/image6.png "此操作通过 "版本控制" 设置屏幕来完成")

    如果没有创建 VCS 项目，可以从下面显示的 "新的 VCS 根" 页面创建一个项目：

    ![如果没有创建 VCS 项目，可以从新的 VCS 根页面创建一个](teamcity-images/image7.png "如果没有创建 VCS 项目，可以选择从新的 VCS 根页面创建一个")

    附加 VCS 根后，TeamCity 将签出该项目并尝试自动检测生成步骤。 如果熟悉 TeamCity，则可以选择检测到的生成步骤之一。 现在可以放心地忽略检测到的生成步骤。

4. 接下来，配置生成触发器。 这会在满足特定条件时（例如，当用户将代码提交到存储库时）对生成进行排队。 以下屏幕截图显示了如何添加生成触发器：

    ![此屏幕截图显示了如何添加生成触发器](teamcity-images/image8.png "此屏幕截图显示了如何添加生成触发器") 可以在以下屏幕截图中查看配置生成触发器的示例：

    ![可在此屏幕截图中查看配置生成触发器的示例](teamcity-images/image9.png "可在此屏幕截图中查看配置生成触发器的示例")

5. 上一部分，参数化生成脚本，并建议将某些值存储为环境变量。 可以通过 "参数" 屏幕将这些变量添加到生成配置中。 如以下屏幕截图所示，为 App Center [API 密钥](/appcenter/api-docs/)、IOS 设备 Id 和 ANDROID 设备 id 添加变量：

    ![为 App Center 测试 API 密钥、iOS 设备 ID 和 Android 设备 ID 添加变量](teamcity-images/image11.png "为 Test Cloud API 密钥、iOS 设备 ID 和 Android 设备 ID 添加变量")

6. 最后一步是添加一个生成步骤，该步骤将调用生成脚本来编译应用程序，并将应用程序排入队列，以便 App Center 测试。 以下屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例：

    ![此屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例](teamcity-images/image12.png "此屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例")

7. 此时，生成配置已完成。 最好触发生成以确认已正确配置该项目。 实现此目的的一种好方法是提交对存储库的少量不重要的更改。 TeamCity 应检测到提交并启动生成。

8. 完成生成后，检查生成日志，并查看生成中是否存在需要注意的问题或警告。

## <a name="summary"></a>总结

本指南介绍了如何使用 TeamCity 生成 Xamarin 移动应用程序，然后将其提交到 App Center 测试。 我们讨论了如何创建生成脚本来自动执行生成过程。 生成脚本负责编译应用程序、提交到 App Center 测试并等待结果。

然后，我们介绍了如何在 TeamCity 中创建一个项目，该项目将在每次开发人员提交代码时对生成进行排队，并调用生成脚本。

## <a name="related-links"></a>相关链接

- [正在准备 Xamarin Android 应用](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [正在准备 Xamarin iOS 应用](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [安装和配置 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)