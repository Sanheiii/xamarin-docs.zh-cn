---
title: 发布应用程序
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 0b44cbf754e8d1be66b0f3bf09badb78852978eb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454632"
---
# <a name="publishing-an-application"></a>发布应用程序

创建优秀的应用程序后，用户自然希望进行使用。 本部分介绍公开分发使用 Xamarin.Android 创建的应用程序所涉及的步骤，其中销售渠道包括电子邮件、专用 Web 服务器、Google Play 或 Android 版 Amazon 应用商店。

## <a name="overview"></a>概述

Xamarin.Android 应用程序开发的最终步骤是发布应用程序。 发布是编译 Xamarin.Android 应用程序，使其可供用户安装在设备上的过程，其中包括 2 个基本任务：

- **做好发布准备** &ndash; 创建可部署到支持 Android 的设备的应用程序的一个发布版本（有关发布准备的详细信息，请参阅[做好应用程序发布准备](~/android/deploy-test/release-prep/index.md)）。

- **分发** &ndash; 通过一个或多个不同的分发渠道提供应用程序的发行版本。

下图说明了发布 Xamarin.Android 应用程序涉及的步骤：

[![生成和部署流程图](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

如上图所示，所有分发方式的准备过程都是相同的。 可通过以下几种方式将 Android 应用程序发布给用户：

- **通过网站 ** &ndash; Xamarin.Android 应用程序可通过在网站下载获得，用户可在网站中单击链接安装应用程序。
- **通过电子邮件 ** &ndash; 用户可以从电子邮件安装 Xamarin.Android 应用程序。 在 Android 设备上打开附件时会安装应用程序。
- **通过应用商店 ** &ndash; 有多个应用市场可用于分发，例如 [Google Play](https://play.google.com/) 或 [Android 版 Amazon 应用商店](https://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011)。

发布应用程序最常见的方法是通过知名的应用商店，因为它可以提供最广的市场范围和达到最佳的分发控制。 但是，通过应用市场发布应用程序需要进行其他工作。

Xamarin.Android 应用程序可同时通过多个渠道分发。 例如，应用程序可在 Google Play 和 Android 版 Amazon 应用商店上发布，也可从 Web 服务器下载。

其他两种分发方法（下载或电子邮件）非常适用于受控数量的用户，例如仅面向少数或特定用户的企业环境或应用程序。
服务器和电子邮件分发也是比较简单的发布模型，发布应用程序所需的准备较少。

通过 Amazon 移动应用分发程序，移动应用开发人员可在 Amazon 上分发和销售应用程序。 用户可使用 Amazon 应用商店应用程序在 Android 设备上发现和购买应用。 Android 设备上运行的 Amazon 应用商店的屏幕截图如下所示：

Google Play 无疑是最全面和最受欢迎的 Android 应用程序应用市场。 在 Google Play 中，用户可以仅通过单击设备或计算机上的图标便可发现、下载、评价和购买应用程序。 Google Play 还提供工具以帮助分析销售和市场趋势和控制可能下载应用程序的设备和用户。 Android 设备上运行的 Google Play 的屏幕截图如下所示：

[![Google Play 屏幕快照](images/google-play-app.png)](images/google-play-app.png#lightbox)

这部分演示如何将应用程序与相应的宣传材料上传到 Google Play 等商店。 对 APK 扩展文件进行了解释，从概念上概述了其内容和工作原理。 还对 Google 授权服务进行了说明。 最后，介绍了其他的分发方式，包括使用 HTTP Web 服务器、简单的电子邮件分发以及适用于 Android 的 Amazon 应用商店。

## <a name="related-links"></a>相关链接

- [HelloWorldPublishing（示例）](/samples/xamarin/monodroid-samples/helloworldpublishing)
- [生成过程](~/android/deploy-test/building-apps/build-process.md)
- [链接](~/android/deploy-test/linker.md)
- [获取 Google Maps API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [通过 Visual Studio 应用中心部署](/appcenter/distribution/stores/googleplay)
- [应用程序签名](https://source.android.com/security/apksigning/)
- [发布到 Google Play](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Google 应用程序授权](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [移动应用分发门户](https://developer.amazon.com/welcome.html)
- [Amazon 移动应用分发常见问题解答](https://developer.amazon.com/help/faq.html)