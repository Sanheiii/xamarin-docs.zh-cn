---
title: 将 Xamarin.iOS 应用发布到 App Store
description: 本文档介绍了如何配置、生成和发布 Xamarin.iOS 应用程序，以在 App Store 中进行分发。
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 1f556e7b63cabcb2a0909927df4970d367338694
ms.sourcegitcommit: 2d41e287ccf937efa24813151e371298b2b4a582
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102279"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>将 Xamarin.iOS 应用发布到 App Store

若要将应用发布至 [App Store](https://www.apple.com/ios/app-store/)，应用开发人员必须先将该应用及其屏幕截图、说明、图标和其他信息提交给 Apple 审核。 Apple 会在批准应用后将其加入 App Store，用户可以直接使用 iOS 设备在其中购买并安装应用。

本指南介绍向 App Store 提供应用以及将应用发给 Apple 审核所要遵循的步骤。 具体而言，描述了以下内容：

> [!div class="checklist"]
>
> - 遵循 App Store 审核指南
> - 设置应用 ID 和权利
> - 提供 App Store 图标和应用图标
> - 设置 App Store 预配配置文件
> - 更新“发布”生成配置 
> - 在 iTunes Connect 中配置应用
> - 生成应用并将其提交至 Apple

> [!IMPORTANT]
> Apple [已表明](https://developer.apple.com/ios/submit/)，从 2019 年 3 月开始，提交到 App Store 的所有应用和更新必须是使用 iOS 12.1 SDK 或更高版本构建的，包含在 Xcode 10.1 或更高版本中。
> 应用还应支持 iPhone XS 和 12.9 英寸 iPad Pro 屏幕大小。

## <a name="app-store-guidelines"></a>App Store 指南

在将应用提交以发布在 App Store 之前，请确保应用符合 Apple 的 [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)中所定义的标准。
在将应用提交至 App Store 时，Apple 会审核应用以确保它满足这些要求。 如果不满足，Apple 会拒绝该应用，需要处理好提出的问题并重新提交应用。
因此，在开发过程中最好尽早熟悉这些准则。

提交应用时需要注意几点：

1. 确保应用的描述与其功能匹配。
2. 测试应用不会在正常使用时崩溃。 这包括在它支持的每台 iOS 设备上的使用情况。

另请参阅 Apple 提供的 [App Store 相关资源](https://developer.apple.com/app-store/resources/)。

## <a name="set-up-an-app-id-and-entitlements"></a>设置应用 ID 和权利

每个 iOS 应用都有唯一的应用 ID，此 ID 有一组相关联的应用程序服务，也就是“权利”  。 “权利”允许应用执行一系列的操作，例如接收推送通知、访问 iOS 功能（例如 HealthKit）等等。

若要创建应用 ID 并选择任何需要的权利，请访问 [Apple 开发人员门户](https://developer.apple.com/account/)并执行以下步骤：

1. 登录到 [Apple 开发人员门户](https://developer.apple.com/account/)。 如果还没有 Apple ID，请先[创建 Apple ID](https://appleid.apple.com/account?appId=632&returnUrl=https%3A%2F%2Fdeveloper.apple.com%2Faccount%2F#!&page=create)。
2. 依次转到“证书、ID 和配置文件”部分以及“标识符”部分 。
3. 单击页面顶部标识符标题旁边的 + 按钮。
4. 选择“应用 ID”，然后单击“继续” 。 
5. 选择“应用”，然后单击“继续” 。
6. 为新应用程序输入“描述”和“捆绑 ID”，然后选择 Xamarin.iOS 应用程序要求的任何“功能”  。 [使用 Xamarin.iOS 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中进一步描述了应用服务。 选择完后，单击“继续”。
7. 单击“注册”按钮完成创建新应用 ID 的过程。

定义应用 ID 时，除了选择和配置所需的应用程序服务，还必须通过编辑“Info.plist”和“Entitlements.plist”文件在 Xamarin.iOS 项目中配置权利********。 有关详细信息，请参阅[使用 Xamarin.iOS 中的权利](~/ios/deploy-test/provisioning/entitlements.md)指南，其中介绍了如何创建 Entitlements.plist 文件以及它所包含的各种权利设置的含义****。

## <a name="include-an-app-store-icon"></a>包括 App Store 图标

在将应用提交至 Apple 时，请确保包含一个带有 App Store 图标的资产目录。 若要了解如何做到这一点，请参阅 [Xamarin.iOS 中的 App Store 图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

## <a name="set-the-apps-icons-and-launch-screens"></a>设置应用图标和启动屏幕

为了让 Apple 能在 App Store 上提供 iOS 应用，该应用必须在所有能运行的 iOS 设备上具备合适的图标和启动屏幕。 若要详细了解如何设置应用图标和启动屏幕，请阅读以下指南：

- [Xamarin.iOS 中的应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 应用的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>创建并安装 App Store 预配配置文件

iOS 使用预配配置文件** 来控制特定应用程序生成的部署方式。 这些文件包含有关用于应用签名的证书、应用 ID 和应用安装位置的信息。 对于开发和临时分发，预配配置文件还包括允许部署应用的设备列表。 但是如果是 App Store 分发，则只包含证书和应用 ID 信息，因为公开分发的唯一机制是 App Store。

若要创建和安装 App Store 预配配置文件，请执行以下步骤：

1. 登录到 [Apple 开发人员门户](https://developer.apple.com/account/)。
2. 依次转到“证书、ID 和配置文件”部分以及“配置文件”部分 。
3. 单击 + 按钮，选择“iOS 应用开发”和 App Store，然后单击“继续”   。
4. 从列表中选择应用的“应用 ID”然后单击“继续”********。
5. 选择签名证书，然后单击“继续”****。
6. 选择要包含在此配置文件中的设备，然后单击“继续”。
7. 输入一个预配配置文件名称，然后单击“生成”以生成配置文件 。
8. 使用 Xamarin 的 [Apple 帐户管理](~/cross-platform/macios/apple-account-management.md)工具将新建的预配配置文件下载到 Mac。 如果是在 Mac 设备上，还可以直接从 Apple 开发人员门户下载预配配置文件并双击它以进行安装。

有关详细说明，请参阅[创建分发配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。

## <a name="update-the-release-build-configuration"></a>更新“发布”生成配置

新的 Xamarin.iOS 项目会自动设置“调试”和“发布”_生成配置_ 。 若要正确地配置“发布”生成，请执行以下步骤****：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Solution Pad 中，打开位于 iOS 项目内部的 Info.plist 文件 。 
2. 请确保位于“应用程序”视图。 这可以通过单击底部导航栏中的选项来设置。
3. 选择“手动预配”作为签名选项。 保存并关闭该文件。
4. 在 Solution Pad 中右键单击“项目名称”，选择“选项”并导航至“iOS 生成”选项卡****************。
5. 将“配置”设为“发布”，并将“平台”设为“iPhone”****************。
6. 若要使用特定 iOS SDK 进行生成，请从“SDK 版本”列表中进行选择****。 如果不需要，则让此值保持为“默认”****。
7. 通过链接，可去除未使用的代码，从而降低应用程序的整体大小。
   * 在大多数情况下，“链接器行为”应设为默认值“仅链接框架 SDK” 。
   * 使用“不链接”选项可能会导致 Apple 因以下原因而拒绝应用：Xamarin 中存在需要使用“仅链接 Framework SDK”选项进行链接的非公共 iOS API 
   * 应谨慎使用“全部链接”，因为它将从项目的所有程序集中去除代码，包括第三方库。 它会去除第三方库可能仅通过反射（链接器无法检测到）使用的代码，因为它执行静态代码分析来确定正在使用的库代码。 应谨慎使用“全部链接”，因为可能需要手动保留某些类和/或方法来避免由于缺少代码而导致运行时失败。
   * 有关详细信息，请参阅[链接 Xamarin.iOS 应用](~/ios/deploy-test/linker.md)指南。
8. 对于 iOS 11，请选择一个支持 ARM64 的设备体系结构****。 若要深入了解适用于 64 位 iOS 设备的生成，请参阅 [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档中的“启用 Xamarin.iOS 应用的 64 位生成”部分****。
9. 可使用 LLVM 编译器生成更小更快的代码****。 但是此选项会增加编译时间。
10. 选中“优化 PNG 图像”以进一步降低应用程序的大小****。
11. _不_应启用调试，因为这将使生成增加不必要的大小。
12. 根据应用程序的需要，可能需要调整使用的**垃圾回收**的类型以及**国际化**的设置。
    
    按上文所述设置选项后，你的生成设置应类似于：

    ![iOS 生成设置](publishing-to-the-app-store-images/build-m157.png "iOS 生成设置")

    另请参阅 [iOS 生成机制](~/ios/deploy-test/ios-build-mechanics.md)指南，其中进一步介绍了生成设置。

13. 导航至“iOS 捆绑签名”选项卡。如果此处的选项不可编辑，请确保在 Info.plist 文件中选择了“手动预配” 。
14. 请确保将“配置”设置为“发布”，并将“平台”设置为“iPhone”****************。
15. 将“签名标识”设置为“分发(自动)”********。
16. 对于“预配配置文件”，请选择[上面创建的](#create-and-install-an-app-store-provisioning-profile) App Store 预配配置文件****。

    现在，项目的捆绑签名选项应类似如下：

    ![iOS 捆绑签名](publishing-to-the-app-store-images/bundleSigning-m157.png "iOS 捆绑签名")

17. 单击“确定”，保存对项目属性所做的更改****。

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

1. 请确保 Visual Studio 2019 已[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在“解决方案资源管理器”**** 中，右键单击“项目名称”****，选择“属性”****。
3. 导航到“iOS 生成”选项卡并将“配置”设置为“发布”，将“平台”设置为“iPhone”********************。
4. 若要使用特定 iOS SDK 进行生成，请从“SDK 版本”列表中进行选择。 如果不需要，则让此值保持为“默认”****。
5. 通过链接，可去除未使用的代码，从而降低应用程序的整体大小。
   * 在大多数情况下，“链接器行为”应设为默认值“仅链接框架 SDK” 。
   * 使用“不链接”选项可能会导致 Apple 因以下原因而拒绝应用：Xamarin 中存在需要使用“仅链接 Framework SDK”选项进行链接的非公共 iOS API 
   * 应谨慎使用“全部链接”，因为它将从项目的所有程序集中去除代码，包括第三方库。 它会去除第三方库可能仅通过反射（链接器无法检测到）使用的代码，因为它执行静态代码分析来确定正在使用的库代码。 应谨慎使用“全部链接”，因为可能需要手动保留某些类和/或方法来避免由于缺少代码而导致运行时失败。
   * 有关详细信息，请参阅[链接 Xamarin.iOS 应用](~/ios/deploy-test/linker.md)指南。
6. 对于 iOS 11，请选择一个支持 ARM64 的设备体系结构****。 若要深入了解适用于 64 位 iOS 设备的生成，请参阅 [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档中的“启用 Xamarin.iOS 应用的 64 位生成”部分****。
7. 可使用 LLVM 编译器生成更小更快的代码****。 但是此选项会增加编译时间。
8. 选中“优化 PNG 图像”以进一步降低应用程序的大小****。
9. _不_应启用调试，因为这将使生成增加不必要的大小。
10. 根据应用程序的需要，可能需要调整使用的**垃圾回收**的类型以及**国际化**的设置。

    设置好上述选项后，生成设置应类似如下：

    ![iOS 生成设置](publishing-to-the-app-store-images/build-w157.png "iOS 生成设置")

    另请参阅 [iOS 生成机制](~/ios/deploy-test/ios-build-mechanics.md)指南，其中进一步介绍了生成设置。

11. 导航至“iOS 捆绑签名”选项卡。请确保将“配置”设置为“发布”，将“平台”设置为“iPhone”，并选择“手动预配”。
12. 将“签名标识”设置为“分发(自动)”********。
13. 对于“预配配置文件”，请选择[上面创建的](#create-and-install-an-app-store-provisioning-profile) App Store 预配配置文件****。

    现在，项目的捆绑签名选项应类似如下：

    ![iOS 捆绑签名设置](publishing-to-the-app-store-images/bundleSigning-w157.png "iOS 捆绑签名设置")

14. 保存生成配置并将其关闭。

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

1. 请确保 Visual Studio 2017 已[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在“解决方案资源管理器”**** 中，右键单击“项目名称”****，选择“属性”****。
3. 导航到“iOS 生成”选项卡并将“配置”设置为“发布”，将“平台”设置为“iPhone”********************。
4. 若要使用特定 iOS SDK 进行生成，请从“SDK 版本”列表中进行选择。 如果不需要，则让此值保持为“默认”****。
5. 通过链接，可去除未使用的代码，从而降低应用程序的整体大小。
   * 在大多数情况下，“链接器行为”应设为默认值“仅链接框架 SDK” 。
   * 使用“不链接”选项可能会导致 Apple 因以下原因而拒绝应用：Xamarin 中存在需要使用“仅链接 Framework SDK”选项进行链接的非公共 iOS API 
   * 应谨慎使用“全部链接”，因为它将从项目的所有程序集中去除代码，包括第三方库。 它会去除第三方库可能仅通过反射（链接器无法检测到）使用的代码，因为它执行静态代码分析来确定正在使用的库代码。 应谨慎使用“链接全部”，因为可能需要手动保留某些类和/或方法等内容来避免由于缺少代码而导致运行时失败。
   * 有关详细信息，请参阅[链接 Xamarin.iOS 应用](~/ios/deploy-test/linker.md)指南。
6. 对于 iOS 11，请选择一个支持 ARM64 的设备体系结构****。 若要深入了解适用于 64 位 iOS 设备的生成，请参阅 [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档中的“启用 Xamarin.iOS 应用的 64 位生成”部分****。
7. 可使用 LLVM 编译器生成更小更快的代码****。 但是此选项会增加编译时间。
8. 选中“优化 PNG 图像”以进一步降低应用程序的大小****。
9. _不_应启用调试，因为这将使生成增加不必要的大小。
10. 根据应用程序的需要，还可调整所用的“垃圾回收”的类型和“国际化”设置********。

    设置好上述选项后，生成设置应类似如下：

    ![iOS 生成设置](publishing-to-the-app-store-images/build-w157.png "iOS 生成设置")

    另请参阅 [iOS 生成机制](~/ios/deploy-test/ios-build-mechanics.md)指南，其中进一步介绍了生成设置。

11. 导航至“iOS 捆绑签名”选项卡。请确保将“配置”设置为“发布”，将“平台”设置为“iPhone”，并选择“手动预配”。
12. 将“签名标识”设置为“分发(自动)”********。
13. 对于“预配配置文件”，请选择[上面创建的](#create-and-install-an-app-store-provisioning-profile) App Store 预配配置文件****。

    现在，项目的捆绑签名选项应类似如下：

    ![iOS 捆绑签名设置](publishing-to-the-app-store-images/bundleSigning-w157.png "iOS 捆绑签名设置")

14. 导航到“iOS IPA 选项”选项卡****。
15. 请确保将“配置”设置为“发布”，并将“平台”设置为“iPhone”****************。
16. 勾选“生成 iTunes 包存档 (IPA)”复选框****。 此设置将让每个“发布”生成（因为这是所选配置）生成一个 .ipa 文件****。 可将此文件提交到 Apple 以发布在 App Store 上。

    > [!NOTE]
    > 对于 App Store 发布，“iTunes 元数据”和“iTunes 图稿”不是必需的********。 有关详细信息，请参阅 [Xamarin.iOS 应用中的 iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)以及 [iTunes 图稿](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork)。

17. 若要指定一个与 Xamarin.iOS 项目名称不同的 .ipa 文件名，请在“包名称”字段中输入这个名称****。

    ![iOS 捆绑签名设置](publishing-to-the-app-store-images/ipaOptions-w157.png "iOS 捆绑签名设置")

18. 保存生成配置并将其关闭。

-----

## <a name="configure-your-app-in-itunes-connect"></a>在 iTunes Connect 中配置应用

[iTunes Connect](https://itunesconnect.apple.com) 是一款基于 Web 的工具套件，用于在 App Store 上管理 iOS 应用程序。 必须在 iTunes Connect 中对 Xamarin.iOS 应用程序进行恰当的配置，然后才能提交到 Apple 进行审核并在 App Store 上发布。

若要了解如何做到这一点，请阅读[在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南。

## <a name="build-and-submit-your-app"></a>生成并提交应用

如果已正确配置生成设置，且 iTunes Connect 正在等待提交，则可生成应用并将其提交至 Apple。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，选择“发布”生成配置和一个作为生成目标的设备（不是模拟器）****。

    ![生成配置和平台选择](publishing-to-the-app-store-images/chooseConfig-m157.png "生成配置和平台选择")

2. 在“生成”菜单中，选择“存档以供发布” 。
3. 存档创建完成后，将显示“存档”视图。 单击“签名并分发...”以打开发布向导****。

    ![“存档”视图中“签名和分发”按钮位置的屏幕截图。](publishing-to-the-app-store-images/archives-mac.png "“存档”视图中“签名和分发”按钮位置的屏幕截图。")

    > [!NOTE]
    > 默认情况下，“存档”视图仅显示打开的解决方案的存档****。 若要查看拥有存档的所有解决方案，请勾选“显示所有存档”复选框****。 最好保留旧的存档，以便在必要时将它们所包含的调试信息用于符号化崩溃报告。

4. 选择“App Store”分发通道****。 单击 **“下一步”** 。

5. 选择“上传”作为目标****。 单击 **“下一步”** 。

6. 在“预配配置文件”窗口中选择签名标识、应用和预配配置文件****。 单击 **“下一步”** 。

    ![“预配配置文件”向导页面的屏幕截图，其中显示有效的签名标识、应用和预配配置文件选择。](publishing-to-the-app-store-images/provProfileSelect-mac.png "“预配配置文件”向导页面的屏幕截图，其中已选中有效的签名标识、应用和预配配置文件。")

7. 在“App Store Connect 信息”窗口中，从菜单中选择 Apple ID 用户名，然后输入[应用特定的密码](https://support.apple.com/ht204397)****。 单击 **“下一步”** 。

    ![“App Store Connect”信息向导页面的屏幕截图，其中显示了所选的 Apple ID 用户名。](publishing-to-the-app-store-images/connectInfo-mac.png "“App Store Connect”信息向导页面的屏幕截图，其中显示了所选的 Apple ID 用户名。")

8. 验证包的详细信息，然后单击“发布”。 选择一个位置来保存 .ipa 文件后，向导会将应用上传到 App Store Connect。

    > [!NOTE]
    > Apple 可能会使用 .ipa 文件中包含的 iTunesMetadata.plist 拒绝应用，导致如下所示的错误****：
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > 若要了解如何处理此错误，请参阅 [Xamarin 论坛中的这篇帖子](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)。

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

> [!NOTE]
> Visual Studio 2019 版本 16.3 及更高版本支持发布到 App Store。

1. 请确保 Visual Studio 2019 已[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 从“解决方案配置”下拉列表中选择“发布”，并从“解决方案平台”下拉列表中选择“iPhone”****************。

    ![Visual Studio 工具栏的屏幕快照，其中显示“解决方案配置”设置为“发布”，“解决方案平台”设置为“iPhone”，“目标”设置为“设备”。](publishing-to-the-app-store-images/chooseConfig-w157.png "Visual Studio 工具栏的屏幕快照，其中显示“解决方案配置”设置为“发布”，“解决方案平台”设置为“iPhone”，“目标”设置为“设备”。")

3. 从“生成”菜单中选定“存档...” 。这会打开“存档管理器”并开始创建存档。

4. 创建存档后，请单击“分发...”以打开发布向导****。

    ![“存档管理器”视图中“分发”按钮位置的屏幕截图。](publishing-to-the-app-store-images/archives-win.png "“存档管理器”视图中“分发”按钮位置的屏幕截图。")

5. 选择“App Store”分发通道****。

6. 选择签名标识和预配配置文件。 单击“上传到商店”****。

    ![“发布”向导的屏幕截图，其中显示有效的签名标识和预配配置文件选择。](publishing-to-the-app-store-images/provProfileSelect-win.png "“发布”向导的屏幕截图，其中显示有效的签名标识和预配配置文件选择。")

7. 输入你的 Apple ID 和[特定于应用的密码](https://support.apple.com/ht204397)。 单击“确定”开始将应用上传到 App Store Connect****。

    ![输入 Apple ID 和应用专用密码的弹出窗口的屏幕截图。](publishing-to-the-app-store-images/connectInfo-win.png "输入 Apple ID 和应用专用密码的弹出窗口的屏幕截图。")

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

> [!NOTE]
> Visual Studio 2017 不支持 Visual Studio for Mac 和 Visual Studio 2019 中的完整发布工作流。
>
> 下面是适用于 Xcode 10 的步骤。
>
> 你仍可以按照以下步骤生成 .IPA 文件，但为了使用 Xcode 11 部署到 App Store（iOS 13 支持有此要求），应[使用 Visual Studio for Mac](?tabs=macos#build-and-submit-your-app)。

1. 请确保 Visual Studio 2017 已[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 从 Visual Studio 2017“解决方案配置”下拉列表中选择“发布”，并从“解决方案平台”下拉列表中选择“iPhone”****************。

    ![生成配置和平台选择](publishing-to-the-app-store-images/chooseConfig-w157.png "生成配置和平台选择")

3. 生成项目。 这将创建一份 .ipa 文件。

    > [!NOTE]
    > 本文档的[更新发布生成配置](#update-the-release-build-configuration)部分配置了应用的生成设置以为每个“发布”生成创建 .ipa 文件****。

4. 若要在 Windows 计算机上找到该 .ipa 文件，请在 Visual Studio 2019 或 Visual Studio 2017 解决方案资源管理器中右键单击 Xamarin.iOS 项目名称，并选择“在文件资源管理器中打开文件夹”********。 然后在刚打开的 Windows 文件资源管理器中导航至 bin/iPhone/Release 子目录********。 该文件应位于此子目录中，除非[自定义了 .ipa 文件输出位置](#customize-the-ipa-location)。
5. 若要在 Mac 生成主机上查看 .ipa 文件，请在 Visual Studio 2019 或 Visual Studio 2017 解决方案资源管理器（在 Windows 上）中右键单击 Xamarin.iOS 项目名称，并选择“在生成服务器上显示 IPA 文件”********。 这将在 Mac 生成主机上打开一个已选中 .ipa 文件的“查找器”窗口****。

    > [!TIP]
    >
    > 仅当使用 Xcode 10 并为 iOS 12 和更早版本生成时，以下步骤才有效。
    >
    > 为了使用 Xcode 11 部署到 App Store（针对 iOS 13），应[使用 Visual Studio for Mac](?tabs=macos#build-and-submit-your-app) 生成和上传应用。 “应用程序加载器”不适用于 Xcode 11****。

6. 在 Mac 生成主机上，打开“应用程序加载器”****。 在 Xcode 中，选择“Xcode”>“打开开发人员工具”>“应用程序加载器”****。

    > [!NOTE]
    > 若要详细了解该工具，请参阅[关于应用程序加载器的 Apple 文档](https://help.apple.com/itc/apploader/#/apdS673accdb)。

7. 登录至应用程序加载器（注意，必须为 Apple ID [创建一个特定于应用的密码](https://support.apple.com/ht204397)）。
8. 选择“提交应用”****，单击“选择”**** 按钮：

    ![选择“提交应用”](publishing-to-the-app-store-images/publishvs01.png "选择“提交应用”")

9. 选择前面创建的.ipa 文件，然后单击“确定”****。
10. 应用加载程序将验证文件：

    ![“验证”屏幕](publishing-to-the-app-store-images/publishvs02.png "“验证”屏幕")

11. 单击“下一步”**** 按钮，应用程序将针对 App Store 进行验证：

    ![对 App Store 进行验证](publishing-to-the-app-store-images/publishvs03.png "对 App Store 进行验证")

12. 单击“发送”**** 按钮，将应用程序发送到 Apple 以供审核。
13. 文件成功上传后，应用程序加载程序将通知你。

    > [!NOTE]
    > Apple 可能会使用 .ipa 文件中包含的 iTunesMetadata.plist 拒绝应用，导致如下所示的错误****：
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > 若要了解如何处理此错误，请参阅 [Xamarin 论坛中的这篇帖子](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)。

-----

## <a name="itunes-connect-status"></a>iTunes Connect 状态

若要查看应用提交的状态，请登录至 iTunes Connect 并选择应用。 初始状态应该为“等待审核”，不过在被处理时可能会暂时地显示为“上传已接收”********。

![正在等待审阅](publishing-to-the-app-store-images/image21.png "正在等待审阅")

## <a name="tips-and-tricks"></a>提示和技巧

### <a name="customize-the-ipa-location"></a>自定义 .ipa 位置

可以通过 MSBuild 属性 `IpaPackageDir` 来自定义 .ipa 文件输出位置****。 如果将 `IpaPackageDir` 设为自定义位置，则 .ipa 文件将放入该位置，而不是默认时间戳子目录中。 创建依赖特定目录路径才能正常工作的自动生成，如用于持续集成 (CI) 生成的生成，此操作会很有帮助。

要使用新属性，有以下几种可能的方法。 例如，若要将 .ipa 文件输出到旧的默认目录（正如在 Xamarin.iOS 9.6 和更低版本中那样），可以使用以下方法之一将 `IpaPackageDir` 属性设为 `$(OutputPath)`。 两种方法都与所有 Unified API Xamarin.iOS 生成兼容，包括 IDE 生成，以及使用 msbuild 或 mdtool 的命令行生成********：

- 第一个选项是，在 **MSBuild** 文件的 `<PropertyGroup>` 元素内，设置 `IpaPackageDir` 属性。 例如，可将以下 `<PropertyGroup>` 添加到 iOS 应用项目 .csproj 文件底部（`</Project>` 结束标记前）：

    ```xml
    <PropertyGroup>
      <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- 更好的方法是将 `<IpaPackageDir>` 元素添加到现有 `<PropertyGroup>` 的底部，对应用于生成 .ipa 文件的配置。 此方法更好，因为会在 iOS IPA 选项项目属性页上使用计划设置针对将来的兼容性准备项目。 如果当前使用 `Release|iPhone` 配置生成 .ipa 文件，完整的已更新属性组可能类似下面这样：

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone'">
       <Optimize>true</Optimize>
       <OutputPath>bin\iPhone\Release</OutputPath>
       <ErrorReport>prompt</ErrorReport>
       <WarningLevel>4</WarningLevel>
       <ConsolePause>false</ConsolePause>
       <CodesignKey>iPhone Developer</CodesignKey>
       <MtouchUseSGen>true</MtouchUseSGen>
       <MtouchUseRefCounting>true</MtouchUseRefCounting>
       <MtouchFloat32>true</MtouchFloat32>
       <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
       <MtouchLink>SdkOnly</MtouchLink>
       <MtouchArch>ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

另一种用于 msbuild 命令行生成的方法是添加 `/p:` 命令行参数来设置 `IpaPackageDir` 属性****。 请注意，在这种情况下，**msbuild** 不会展开传入到命令行的 `$()` 表达式，因此不能使用 `$(OutputPath)` 语法。 必须改为提供完整路径名称。

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或 Mac 上以下示例：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

创建分发生成并将其存档后，现可将应用程序提交到 iTunes Connect。

## <a name="summary"></a>总结

本文介绍了如何配置、生成并提交 iOS 应用以发布在 App Store 上。

## <a name="related-links"></a>相关链接

- [Apple 开发人员门户 (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [App Store 审核指南 (Apple)](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [常见的应用拒绝 (Apple)](https://developer.apple.com/app-store/review/rejections/)
- [使用 Xamarin.iOS 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)
- [在 Xamarin.iOS 中使用权利](~/ios/deploy-test/provisioning/entitlements.md)
- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Xamarin.iOS 中的应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 应用的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)
