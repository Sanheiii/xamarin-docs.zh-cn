---
title: 使用 TestFlight 来分发 Xamarin.iOS 应用
description: TestFlight 现在归 Apple 所有，是进行 Xamarin.iOS 应用的 Beta 测试的主要方法。 本文将指导你完成 TestFlight 过程的所有步骤，范围包括从上传应用到使用 iTunes Connect。
ms.prod: xamarin
ms.assetid: BA880768-2BC8-41E4-B57E-A56F8EED4690
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 35adaf4a5cf9ce103cb36320692ad8307b9d1354
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564116"
---
# <a name="using-testflight-to-distribute-xamarinios-apps"></a>使用 TestFlight 来分发 Xamarin.iOS 应用

_TestFlight 现在归 Apple 所有，是进行 Xamarin.iOS 应用的 Beta 测试的主要方法。本文将指导你完成 TestFlight 过程的所有步骤，范围包括从上传应用到使用 iTunes Connect。_

Beta 测试是软件开发周期中不可分割的一部分，有许多跨平台应用程序服务/产品可简化此过程（如 [HockeyApp](https://hockeyapp.net/features/)、[Applause](https://www.applause.com/mobile-app-testing)），Google Play 当前也有面向 Android 应用的本地应用 Beta 测试。 本文档重点介绍 Apple 的 TestFlight。

TestFlight 是 Apple 向 iOS 应用推出的 Beta 测试服务，仅可通过 [iTunes Connect](https://itunesconnect.apple.com/) 访问。 它当前可用于 iOS 8.0 应用及更高版本。 TestFlight 允许对内部和外部用户进行 beta 测试，并且由于外部用户的 Beta 版应用审核，可在发布到 App Store 时的最终审核中大幅简化该过程。

以前，二进制文件在 Visual Studio for Mac 中生成并上传到 TestFlightApp 网站以分发给测试员。 新进程有了大量改进，使用户可在 App Store 中拥有经过充分测试的优质应用。 例如：

- 外部测试所需的 Beta 版应用审核可增加最终 App Store 审核的成功几率，因为两者都需要遵守 Apple 相关指南。
- 上传之前，应用需要在 iTunes Connect 中进行注册。 这可确保配置文件、名称和证书之间完全匹配。
- TestFlight 应用现是一款真正的 iOS 应用，因此运行速度更快。
- Beta 测试完成后，可快速高效地移动应用进行审核；只需单击一次按钮。

## <a name="requirements"></a>要求

只有 iOS 8.0 或更高版本的应用才可通过 TestFlight 进行测试。

所有测试员均至少必须在 iOS 8 设备上测试应用。 但是，最佳做法表明应在所有 iOS 版本上测试应用

## <a name="provisioning"></a>预配

为了使用 TestFlight 测试你的生成，需要使用新的 beta 权利创建 App Store 分发配置文件  。 此权利允许通过 TestFlight 进行 beta 测试，并且所有**新的** App Store 分发配置文件均自动包含此权利。 可按照[创建分发配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)指南中的分步说明生成新的配置文件。

[在 Xcode 中验证内部版本](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)时，可确认分发配置文件中是否包含 Beta 权利，如下所示：

[![将应用提交到 Apple](testflight-images/validate-build.png)](testflight-images/validate-build.png#lightbox)

## <a name="testflight-workflow"></a>TestFlight 工作流

以下工作流介绍了开始使用 TestFlight 进行应用的 Beta 测试时所需的步骤：

1. 对于新应用，创建一个 [iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)。
2. 将应用程序[存档和发布](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)到 iTunes Connect。
3. 管理 Beta 测试：
    - 添加元数据。
    - 添加内部用户：
      - 最多 100 位用户，每位用户最多可以在 30 个设备上进行测试。
    - 添加外部用户：
      - 最多 10000 位用户。
      - 需要 Beta 测试审核，这需要遵守 Apple 相关指南。
4. 接收来自用户的反馈，采取相应操作，然后返回到步骤 2。

## <a name="create-an-itunes-connect-record"></a>创建 iTunes Connect 记录

1. 使用 Apple 开发人员凭据登录到 [iTunes Connect 门户](https://itunesconnect.apple.com/)。
2. 选择“我的应用”：

    [![选择“我的应用”](testflight-images/my-apps.png)](testflight-images/my-apps.png#lightbox)

3. 在“我的应用”屏幕上，单击屏幕左上角的“+”按钮以添加新的应用。 若拥有 Mac 和 iOS 开发人员帐户，则系统将提示你在此处选择新的应用类型。

将显示“新 iOS 应用”提交窗口，其中需包含与应用的 Info.plist 中完全相同的信息

若要深入了解如何创建新 iTunes Connect 记录，请参阅[创建 iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南。

### <a name="completing-the-new-ios-app-submission-form"></a>完成“新 iOS 应用提交”窗体

该窗体需反映与应用的 Info.plist 文件中完全相同的信息，如下所示：

[![应用的 Info.plist](testflight-images/infoplist.png)](testflight-images/infoplist.png#lightbox)
[![iTunes Connect 上的窗体](testflight-images/newiosapp.png)](testflight-images/newiosapp.png#lightbox)

- **名称** - 设置应用程序包时使用的描述性名称。 它必须与 `Info.plist` 中的“应用程序名称”条目完全匹配。
- **主要语言** - 应用中使用的基本语言。 这通常是你所说的语言。
- **程序包 ID** - 一个下拉菜单，其列出在开发人员帐户上创建的所有应用 ID。
  - **应用程序包 ID 后缀** - 如果已选择通配符应用程序包 ID（即以 * 结尾，如上面的示例所示），则会看到其他框，提示输入应用程序包 ID 后缀。 在此示例中，“应用程序包 ID”是“`mobi.chkn.*`”，“后缀”是“PageView”。 这些一起构成了 `Info.plist` 中的应用程序包标识符。
- **版本** - 正在上传的应用的版本号。 这由开发人员选择。
- **SKU** - SKU 是应用的唯一 ID，不向用户显示。 可按类似方式将其视为产品 ID。 在以上示例中，我选择了日期和该日期的版本号。

## <a name="upload-your-app"></a>上传应用

创建 iTunes Connect 记录后，即可上传新的版本。 请记住，版本必须具有新的 Beta 权利。

首先，在 IDE 中生成[最终可分发文件](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)，然后通过“应用程序加载程序”或 Xcode 中的存档功能[将应用提交到 Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

### <a name="create-an-archive"></a>创建存档

 若要在 Visual Studio for Mac 中生成二进制文件，将需要使用存档功能。 右键单击项目，然后选择“存档以供发布”，如下所示：

 [![选择“存档以供发布”](testflight-images/new-archive.png)](testflight-images/new-archive.png#lightbox)

 有关详细信息，请参阅[生成可分发文件](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南。

### <a name="sign-and-distribute-your-app"></a>签名和分发应用

 创建存档将自动打开“存档视图”，显示已存档的所有项目（按解决方案分组）。 若要对应用进行签名并准备好进行分发，请选择“签名和分发...”，如下所示：

[![创建存档将自动打开存档视图](testflight-images/archive-view.png)](testflight-images/archive-view.png#lightbox)

 这将打开发布向导。 选择“App Store”分发渠道以创建一个包，然后打开“应用程序加载程序”。 在“配置文件”屏幕上，选择你的签名标识和配置文件，或使用其他标识重新签名。 验证包的详细信息，然后单击“发布”保存 `.ipa`

[![选择签名标识和配置文件，或使用其他标识重新签名](testflight-images/group.png)](testflight-images/group.png#lightbox)

 有关这些步骤的详细信息，请参阅[将应用提交到 Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) 部分。

### <a name="submitting-your-build"></a>提交内部版本
 发布向导将打开“应用程序加载程序”程序，便于所有人将版本上传到 iTunes Connect。 选择“提交应用”选项，然后上传上面创建的 `.ipa` 文件。 应用程序加载程序将验证你的版本并将其上传到 iTunes Connect。

 有关这些步骤的详细信息，请参阅[将应用提交到 Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) 部分。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="building-your-final-distributable"></a>生成最终可分发文件
 由于 Visual Studio 的 Xamarin 插件不支持存档 Xamarin.iOS 应用以发布到 App Store，因此可采用两种方式通过 Visual Studio 发布 iOS 应用程序。 这些是：

1. 上传通过 Build Adhoc IPA 命令创建的 IPA。
1. 上传压缩的 `.app` 程序包。

 有关这两种方法的说明，请参阅[生成可分发文件](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南。

### <a name="submitting-your-build"></a>提交内部版本
 若要将应用提交到 Apple，必须移动到生成主机，然后使用“应用程序加载器”程序（可作为 Xcode 的一部分进行安装）。 有关访问应用程序加载器的详细信息，请参阅 Apple 的[访问应用程序加载器](https://help.apple.com/itc/apploader/#/apdATD1E927-D1E1A1303-D1E927A1126)指南。

打开后，选择“提交应用”选项，然后上传上面创建的 zip 或 `.ipa` 文件。 应用程序加载程序将验证你的版本并将其上传到 iTunes Connect。

 有关这些步骤的详细信息，请参阅[将应用提交到 Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) 部分。

-----

[发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南更详细地介绍了所有上述步骤，请参阅此指南以更加深入地了解 App Store 提交过程。

返回 iTunes Connect 的“我的应用”部分后，会发现你的应用程序已成功上传。 此时即可执行一些 Beta 测试！

## <a name="manage-beta-testing"></a>管理 Beta 测试

### <a name="add-metadata"></a>添加元数据

若要开始使用 TestFlight，请浏览到应用的“预发行”选项卡。 会显示 3 个选项卡，内附内部版本、内部测试员和外部测试员的列表，如下所示：

[![“生成”、“内部测试员”和“外部测试员”选项卡](testflight-images/app-uploaded.png)](testflight-images/app-uploaded.png#lightbox)

若要将元数据添加到应用，请单击内部版本号，然后单击 TestFlight：

[![添加元数据](testflight-images/metadata.png)](testflight-images/metadata.png#lightbox)

在“测试信息”下，可为测试员提供有关应用的重要信息，例如：

- 要测试的内容
- 应用说明。
- 营销网址 URL - 这将提供当前添加的应用的相关信息。
- 隐私策略 URL - 提供贵公司隐私策略相关信息的 URL。
- 反馈电子邮件。

请注意，此元数据对内部测试员而言**不是**必需的，但对外部测试员**是**必需的。

<a name="beta-testing"></a>

### <a name="enable-beta-testing"></a>启用 Beta 测试

准备好开始测试应用后，打开适合你的版本的“TestFlight Beta 版测试”开关：

[![打开 TestFlight Beta 测试开关](testflight-images/turn-on-testing.png)](testflight-images/turn-on-testing.png#lightbox)

从打开 TestFlight Beta 开关的日期起，每个内部版本可活动 **60 天**。 “测试信息”页上显示了每个版本的剩余天数：

[![“测试信息”页面](testflight-images/daysleft.png)](testflight-images/daysleft.png#lightbox)

可随时关闭测试。

### <a name="internal-testers"></a>内部测试员

内部测试员是你的开发团队的成员，其已分配有 iTunes Connect 中的以下某一角色：

- **管理员** - 管理员负责在 iTunes Connect 中添加和管理新用户。
- **律师** - 团队代理是将分配有法律角色的唯一管理用户。 它允许代理签署法律合同。
- **技术员** - 技术用户可更改有关应用的大多数属性。 例如，编辑应用信息、上传二进制文件和发送应用进行审核。

每个内部版本最多可以与 100 个成员共享，其中的每位成员最多可在 30 个设备上进行测试。

若要添加测试员，请浏览到 iTunes Connect 主屏幕上的“用户和角色”：

[![iTunes Connect 主屏幕上的用户和角色](testflight-images/users-and-roles.png)](testflight-images/users-and-roles.png#lightbox)

现有 iTunes Connect 用户将在列表中显示。 若要选择他们，请单击其姓名、打开“内部测试员”开关，然后单击“保存”：

[![打开“内部测试员”开关](testflight-images/internal-tester.png)](testflight-images/internal-tester.png#lightbox)

若要添加列表上未显示的用户，请选择“用户”旁边的“+”按钮，然后提供名字、姓氏和电子邮件地址以创建帐户。 用户将需要确认其电子邮件才可激活该帐户：

[![添加用户](testflight-images/add-new-user.png)](testflight-images/add-new-user.png#lightbox)

如果返回到“我的应用”>“预发行”>“内部测试员”，现在将看到已添加用于进行 TestFlight 内部 Beta 测试的用户：

[![针对 TestFlight 内部 beta 测试添加的用户列表](testflight-images/select-users.png)](testflight-images/select-users.png#lightbox)

可选择其姓名，然后单击“邀请”按钮邀请这些测试员。 将向其发送一封电子邮件，邀请其测试你的应用。

可在“内部测试员”页的状态列中查看其邀请状态：

[![邀请状态](testflight-images/status-added.png)](testflight-images/status-added.png#lightbox)

### <a name="external-testers"></a>外部测试员

在邀请外部测试员对你的应用进行 Beta 测试之前，需要经过 Beta 版应用审核，因此需遵守 [ 审核指南](https://developer.apple.com/app-store/review/guidelines/)。

若要提交应用进行审核，请单击内部版本旁边的“提交进行 Beta 版应用审核”文本，如下图所示：

[![提交以供 Beta 版本应用评审](testflight-images/beta-app-review.png)](testflight-images/beta-app-review.png#lightbox)

为使应用通过审核，必须在“TestFlight Beta 版信息”页上输入必需的全部元数据。

现可开始准备邀请并可通过“外部测试员”选项卡添加最多 10000 个外部测试员，方法是输入其电子邮件、名字和姓氏，如下面的屏幕截图所示。 所输入的电子邮件不需要是其 Apple ID；这只是他们将接收邀请的电子邮件。

[![邀请测试员](testflight-images/add-external.png)](testflight-images/add-external.png#lightbox)

如果有大量外部测试员，可使用“导入文件”链接导入一个每行采用以下格式的 `CSV` 文件：

```
first name, last name, email address
```

还可将外部测试员添加为不同的组，帮助保持测试员有序。

输入外部测试员的详细信息后，单击“添加”并确认你拥有邀请他们的用户许可：

[![确认有接受邀请的用户](testflight-images/confirm-consent.png)](testflight-images/confirm-consent.png#lightbox)

仅在成功通过 Beta 应用审核后，才可向外部测试员发送邀请。 此时，内部版本页上“外部”下的文本将更改为“发送邀请”。 单击此处，将邀请发送给所有已添加的测试员。

如果你的应用遭拒，则需要修复“解决中心”中显示的问题，然后重新提交更新后的整个二进制文件进行审核。

## <a name="as-a-beta-tester"></a>作为 Beta 测试员

邀请测试员后，他们将立即收到一封类似于以下屏幕截图中的电子邮件：

[![邀请电子邮件示例](testflight-images/tester-email.png)](testflight-images/tester-email.png#lightbox)

在其单击“在 TestFlight 中打开”按钮后，你的应用随即会在 TestFlight 应用程序中打开；如果尚未下载它，则将直接转到 App Store 并允许其下载。

你的应用在 TestFlight 中打开后，将显示要测试的内容的详细信息，并提示测试员将你的应用程序安装到其 iOS 8.0（或更高版本）设备上：

[![TestFlight 将显示测试内容的详细信息](testflight-images/install-app.png)](testflight-images/install-app.png#lightbox)

在设备主屏幕上，应用程序名前会放置一个橙色圆点，表示测试内部版本。

测试员可通过 TestFlight 应用提供反馈，你将在元数据中提供的电子邮件地址中收到此信息。

### <a name="beta-testing-complete"></a>Beta 测试完成

Beta 测试完成后，即可提交应用进行 Apple 的 App Store 审核。 单击“提交进行审核”按钮，即可在 iTunes Connect 中直接完成该过程，如下所示：

[![单击“提交以供评审”按钮](testflight-images/submit-for-review.png)](testflight-images/submit-for-review.png#lightbox)

## <a name="summary"></a>总结

本文介绍了如何通过 iTunes Connect 使用 Apple 的 TestFlight Beta 版测试。 其中阐释了如何将新的内部版本上传到 iTunes Connect，并介绍如何邀请内部和外部 Beta 版测试员来使用我们的应用。

## <a name="related-links"></a>相关链接

- [创建 iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [为 App Store 分发预配应用](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#provisioning)
- [使用 Apple TestFlight Beta](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/BetaTestingTheApp.html#//apple_ref/doc/uid/TP40011225-CH35-SW2)
