---
title: iOS 6 中对 StoreKit 的更改
description: iOS 6 引入了对应用商店工具包 API 的两项更改：在应用中显示 iTunes (和 App Store/Ibooks store 色情) 产品的功能，以及 Apple 将托管可下载文件的新的应用内购买选项。 本文档介绍如何通过 Xamarin 实现这些功能。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 85e6be722b0d2ddbd2c63955bd19b2907e062156
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433015"
---
# <a name="changes-to-storekit-in-ios-6"></a>iOS 6 中对 StoreKit 的更改

_iOS 6 引入了对应用商店工具包 API 的两项更改：在应用中显示 iTunes (和 App Store/Ibooks store 色情) 产品的功能，以及 Apple 将托管可下载文件的新的应用内购买选项。本文档介绍如何通过 Xamarin 实现这些功能。_

IOS6 中存储工具包的主要变化是这两项新功能：

- **应用内内容显示 & 购买** –用户可购买和下载应用、音乐、书籍和其他 iTunes 内容，而无需离开应用。 你还可以链接到你自己的应用程序来促销购买或只是鼓励审核和评级。
- **应用内购买托管内容** – Apple 将存储和传递与应用内购买产品关联的内容，这无需单独的服务器来承载文件，自动支持后台下载并可编写更少的代码。

有关 StoreKit Api 的详细信息，请参阅 [应用内购买](~/ios/platform/in-app-purchasing/index.md) 指南。

## <a name="requirements"></a>要求

本文档中讨论的商店工具包功能需要 iOS 6 和 Xcode 4.5 以及 Xamarin 6.0。

## <a name="in-app-content-display--purchasing"></a>应用内内容显示 & 购买

IOS 中的全新应用内购买功能允许用户查看产品信息并从应用中购买或下载产品。
之前的应用程序必须触发 iTunes、App Store 或 Ibooks store 色情，这会导致用户离开原始应用程序。 完成后，此新功能会自动将用户返回到应用。

[![在购买后自动返回到应用](changes-to-storekit-images/image1.png)](changes-to-storekit-images/image1.png#lightbox)

如何使用此方法的示例包括：

- **鼓励用户对你的应用进行评级** –你可以打开应用商店页面，使用户无需离开即可对应用进行分级和查看。
- **跨升级应用** –允许用户查看你发布的其他应用，并可立即购买/下载。
- 帮助**用户查找和下载内容**-帮助用户购买应用查找、管理或聚合 (例如的内容。 与音乐相关的应用可以提供歌曲的播放列表，并允许从应用) 中购买每首歌曲。

显示后， `SKStoreProductViewController` 用户可以与产品信息进行交互，就好像它们位于 iTunes、App Store 或 ibooks store 色情中。 用户可以：

- 查看) 的应用的屏幕截图 (
-  (音乐、电视节目和电影) 的歌曲或视频的示例。
- 阅读 (并撰写) 评审，
- 购买 & 下载，它完全在视图控制器和应用商店工具包中发生。

中的某些选项 `SKStoreProductViewController` 仍将强制用户离开你的应用程序并打开相关的应用商店应用程序，如单击 **相关产品** 或应用程序的 **支持** 链接。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

用于在任何应用中显示产品的 API 非常简单：只需创建并显示 `SKStoreProductViewController` 。 按照以下步骤创建并显示产品：

1. 创建一个  `StoreProductParameters` 对象，以将参数传递给视图控制器，包括 `productId` 构造函数中的。
1. 实例化 `SKProductViewController`。 将其分配给类级别字段。
1. 为视图控制器的事件分配一个处理程序  `Finished` ，这会关闭视图控制器。 当用户按 "取消" 时调用此事件;或以其他方式在视图控制器内完成事务。
1. 调用  `LoadProduct` 方法传入  `StoreProductParameters` 和完成处理程序。 完成处理程序应检查产品请求是否成功，如果是，则以模式方式提供  `SKProductViewController` 。 如果无法检索该产品，则应该添加适当的错误处理。

### <a name="example"></a>示例

本文的*StoreKit*示例代码中的*ProductView*项目实现了一个 `Buy` 方法，该方法接受任何产品的 Apple ID 并显示 `SKStoreProductViewController` 。 下面的代码显示了任何给定 Apple ID 的产品信息：

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

运行时，应用程序看起来像下面的屏幕截图–下载或购买完全在以下范围内 `SKStoreProductViewController` ：

[![此应用在运行时如下所示](changes-to-storekit-images/image2.png)](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支持较早的操作系统

该示例应用程序包含的代码演示如何在早期版本的 iOS 中打开应用商店、iTunes 或 Ibooks store 色情。 使用 `OpenUrl` 方法打开正确的精心设计的 **itunes.com** URL。

您可以实现版本检查以确定要运行的代码，如下所示：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>错误

如果你使用的 Apple ID 无效，则会出现以下错误，这可能会造成混淆，因为它表示某种类型的网络或身份验证问题。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>读取目标-C 文档

在 Apple 开发人员门户上阅读有关商店工具包的开发人员将看到协议– [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) –与此新功能相关。 委托协议仅有一种方法– productViewControllerDidFinish – `Finished` 在中，该方法已在上作为事件公开 `SKStoreProductViewController` 。

## <a name="determining-apple-ids"></a>确定 Apple Id

所需的 Apple ID `SKStoreProductViewController` 是一个 *数字* (不会与 "mwc2012" ) 等包 id 混淆。 你可以通过几种不同的方法来找出你想要显示的产品的 Apple ID，如下所示：

### <a name="itunesconnect"></a>iTunesConnect

对于发布的应用程序，可以轻松地在 iTunes Connect 中找到 **APPLE ID** ：

[![在 iTunes Connect 中查找 Apple ID](changes-to-storekit-images/image3.png)](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API"></a>

### <a name="search-api"></a>搜索 API

Apple 提供了一个动态搜索 API 来查询应用商店、iTunes 和 Ibooks store 色情中的所有产品。 有关如何访问搜索 API 的信息，请参阅 Apple 的关联资源，尽管 API 公开给任何人 (不只是注册的附属公司) 。 可以分析生成的 JSON，以发现作为 `trackId` 要用于的 APPLE ID 的 `SKStoreProductViewController` 。

结果还将包括其他元数据，包括可用于在应用程序中呈现产品的显示信息和图稿 Url。

下面是一些示例：

- **iBooks 应用**– [ https://itunes.apple.com/search?term=ibooks&amp ; 实体 = 软件 &amp; 国家/地区](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **点和 Kangaroo iBook** – [ https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp ; entity = 电子书 &amp; country = us](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>企业合作伙伴源

Apple 向已批准的合作伙伴提供所有产品的完整数据转储，格式为可下载的数据库就绪平面文件。 如果你有资格访问企业合作伙伴源，则可以在该数据集中找到任何产品的 Apple ID。

企业合作伙伴源的多个用户是 [关联计划](https://www.apple.com/itunes/affiliates) 的成员，它允许根据产品销售额获得佣金。 `SKStoreProductViewController` 不支持在编写) 时 (关联 Id。

### <a name="direct-product-links"></a>直接产品链接

可从其 iTunes 预览 URL 链接推断产品的 Apple ID。
在 (适用于应用、音乐或书籍的任何 iTunes 产品链接) 查找以开头的 URL 部分 `id` ，并使用后面的数字。

例如，指向 iBooks 的直接链接是

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

并且 Apple ID 为 **364709193**。 同样，对于 MWC2012 应用程序，直接链接

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

并且 Apple ID 为 **496963922**。

## <a name="in-app-purchase-hosted-content"></a>应用内购买托管内容

如果你的应用内购买包含可下载内容 (例如书籍或其他媒体、游戏级别图片和配置或其他大文件) 则这些文件将托管在你的 web 服务器上，而应用程序必须合并代码，以便在购买后安全下载这些文件。 从 iOS 6 开始，Apple 会将文件托管在其服务器上，从而消除了对单独服务器的需求。 此功能仅适用于不适用的产品， (不适用) 的订阅。 使用 Apple 托管服务的优点包括：

- 保存承载 & 带宽成本。
- 可能比当前使用的服务器主机更可伸缩。
- 编写的代码较少，因为无需生成任何服务器端处理。
- 已实现后台下载。

注意：不支持在 iOS 模拟器中测试托管的应用内购买内容，因此必须使用实际设备进行测试。

### <a name="hosted-content-basics"></a>托管内容基础知识

在 iOS 6 之前，可以通过两种方式提供 ([Xamarin 的应用内购买](~/ios/platform/in-app-purchasing/index.md) 文档中更详细地介绍的产品) ：

- **内置产品** –通过购买，但内置于应用程序中的功能 (为代码或嵌入的资源) 。 内置产品的示例包括未锁定的照片筛选器或游戏中的电源。
- **服务器交付的产品** –购买后，应用程序必须从您操作的服务器下载内容。 此内容在购买过程中下载，存储在设备上，并在提供产品的过程中呈现。 示例包括书籍、杂志问题或由背景图片和配置文件组成的游戏级别。

在 iOS 6 中，Apple 提供了服务器交付的产品的变化形式：它们会在其服务器上托管你的内容文件。 这使得生成服务器交付的产品变得更加简单，因为你无需运行单独的服务器，并且存储工具包提供了你之前必须自行编写的后台下载功能。 若要利用 Apple 托管，请为新的应用内购买产品启用内容托管，并修改应用商店工具包代码以利用它。 然后，使用 Xcode 构建产品内容文件，并将其上传到 Apple 的服务器进行审核和发布。

[![生成和交付过程](changes-to-storekit-images/image4.png)](changes-to-storekit-images/image4.png#lightbox)

使用应用商店， *通过托管内容* 提供应用内购买需要以下设置和配置：

- **ITunes Connect** –你 *必须* 已向 Apple 提供银行和税务信息，以便他们能够为你代表你收集的资金。 然后，你可以配置产品以销售，并设置沙盒用户帐户以测试购买。  _你还必须为你想要通过 Apple 托管的那些非耗用产品配置托管内容_。
- **IOS 预配门户** –创建捆绑标识符，并为应用程序启用应用商店访问，就像对任何支持应用内购买的应用程序一样。
- **应用商店工具包** –将代码添加到应用，以显示产品、购买产品和还原交易。  _在 iOS 6 应用商店工具包中，还将管理你的产品内容在后台下载，并在后台进行更新。_
- **自定义代码** -跟踪客户进行的购买，并提供他们购买的产品或服务。 利用新的 iOS 6 存储工具包类（如）  `SKDownload` 检索 Apple 托管的内容。

以下各节介绍如何使用本文的示例代码来实现托管的内容，从创建和上传包到管理购买和下载过程。

### <a name="sample-code"></a>代码示例

示例项目 *HostedNonConsumables* (StoreKitiOS6.zip) 使用托管内容。 该应用提供了两个 "书籍" 章节用于销售，其内容托管在 Apple 的服务器上。 内容由文本文件和图像组成，但在实际应用程序中可以使用更复杂的内容。

在购买过程中和之后，应用如下所示：

 [![在购买过程中和之后，应用如下所示](changes-to-storekit-images/image5.png)](changes-to-storekit-images/image5.png#lightbox)

下载文本文件和图像，并将其复制到应用程序的文档目录。 有关可用于应用程序存储的不同目录的详细信息，请参阅 [文件系统文档](~/ios/app-fundamentals/file-system.md)。

## <a name="itunes-connect"></a>iTunes Connect

创建将使用 Apple 内容托管的新产品时，请确保选择 **不** 需要的产品类型。 其他产品类型不支持内容承载。 此外，不应为销售的 *现有* 产品启用内容托管;只为新产品打开内容托管。

 [![选择非可耗用产品类型](changes-to-storekit-images/image6.png)](changes-to-storekit-images/image6.png#lightbox)

输入 **产品 ID**。 稍后在为此产品创建内容时，将需要此 ID。

 [![输入产品 ID](changes-to-storekit-images/image7.png)](changes-to-storekit-images/image7.png#lightbox)

内容托管在详细信息部分中进行设置。 在应用内购买活动之前，如果想要取消)  (，请取消选中 " **使用 Apple 提供主机内容** " 复选框。 但是，在应用内购买后，不能删除内容托管。

 [![通过 Apple 托管内容](changes-to-storekit-images/image8.png)](changes-to-storekit-images/image8.png#lightbox)

启用托管内容后，该产品将进入 " **等待上传** 状态" 并显示以下消息：

 [![该产品将进入等待上传状态并显示此消息](changes-to-storekit-images/image9.png)](changes-to-storekit-images/image9.png#lightbox)

应使用 Xcode 创建内容包，并使用存档工具上传该内容包。 下一节创建中提供了用于创建内容包的说明 **。.PKG 文件**。

## <a name="creating-pkg-files"></a>制定..PKG 文件

上载到 Apple 的内容文件必须满足以下限制：

- 大小不能超过 2 GB。
- 不能包含指向内容) 以外 (或符号链接的可执行代码。
- 必须格式正确 (包括 **info.plist** 文件) 并且文件扩展名为 **.pkg** 。 如果按这些说明使用 Xcode，则会自动执行此操作。

可以添加多个不同的文件和文件类型，只要它们符合这些限制即可。 内容在传递到应用程序之前压缩，并在代码访问之前由应用商店工具包解压缩。

上载内容包后，可以将其替换为较新的内容。 必须通过正常过程上载和提交新内容以便查看/批准。 递增 `ContentVersion` 更新的内容包中的字段，以指示它是较新的。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 应用内购买内容项目

为应用内购买产品创建内容包目前需要 Xcode。 不需要目标-C 编码;Xcode 为这些包提供了一个新的项目类型，这些包只包含文件和 info.plist。

我们的示例应用程序提供销售图书章节–每个章节内容包都包含：

- 文本文件和
- 用于表示章节的图像。

首先从菜单中选择 " **文件" > "新建项目** "，然后选择 " **应用内购买内容**"：

 [![选择应用内购买内容](changes-to-storekit-images/image10.png)](changes-to-storekit-images/image10.png#lightbox)

输入 " **产品名称** " 和 " **公司标识符** "，以使 " **捆绑标识符** " 与在 "此产品的 ITunes CONNECT" 中输入的 **产品 ID** 匹配。

[![输入名称和标识符](changes-to-storekit-images/image11.png)](changes-to-storekit-images/image11.png#lightbox)

现在，你将拥有一个空白 **的应用内购买内容** 项目。 你可以右键单击并 **添加文件 ...** 或将它们拖动到 **项目导航器**中。 确保 **ContentVersion** 正确 (应从1.0 开始，但如果稍后选择更新内容，请记得) 递增。

此屏幕截图显示了 Xcode，其中包含项目中包含的内容文件和主窗口中显示的 info.plist 项：

[![此屏幕截图显示了 Xcode，其中包含项目中包含的内容文件和主窗口中显示的 info.plist 条目](changes-to-storekit-images/image12.png)](changes-to-storekit-images/image12.png#lightbox)

添加了所有内容文件后，你可以保存此项目并稍后再次编辑它或开始上载过程。

## <a name="uploading-pkg-files"></a>正在..PKG 文件

上传内容包的最简单方法是通过 **Xcode 存档工具**。 从菜单中选择要开始的 **产品 > 存档** ：

![选择 Archiven](changes-to-storekit-images/image13.png)

内容包随后将显示在存档中，如下所示。
"存档类型" 和图标显示此行是 **应用内购买内容存档**。 单击 "**验证 ...** " 检查内容包是否有错误，而不实际执行上传。

[![验证包](changes-to-storekit-images/image14.png)](changes-to-storekit-images/image14.png#lightbox)

用 iTunes Connect 凭据登录：

[![用 iTunes 连接凭据登录](changes-to-storekit-images/image15.png)](changes-to-storekit-images/image15.png#lightbox)

选择正确的应用程序和应用内购买，以将此内容与相关联：

[![选择正确的应用程序和应用内购买，以将此内容与关联](changes-to-storekit-images/image16.png)](changes-to-storekit-images/image16.png#lightbox)

应会看到类似于以下屏幕截图的消息：

![无问题的示例消息](changes-to-storekit-images/image17.png "无问题的示例消息")

现在，请完成类似的过程，但单击 "**分发 ...** " 将实际上传内容。

[![分发应用程序](changes-to-storekit-images/image18.png "分发应用程序")](changes-to-storekit-images/image18.png#lightbox)

选择第一个选项，上传内容：

![上载内容](changes-to-storekit-images/image19.png "上载内容")

再次登录：

[![登录](changes-to-storekit-images/image15.png)](changes-to-storekit-images/image15.png#lightbox)

选择正确的应用程序和应用内购买记录，将内容上传到：

[![选择应用程序和应用内购买记录](changes-to-storekit-images/image20.png)](changes-to-storekit-images/image20.png#lightbox)

正在上载文件，请稍候：

[![内容上传对话框](changes-to-storekit-images/image21.png)](changes-to-storekit-images/image21.png#lightbox)

上传完成后，将显示一条消息，告知你已将内容提交到 App Store。

[![成功上传消息示例](changes-to-storekit-images/image22.png)](changes-to-storekit-images/image22.png#lightbox)

完成此操作后，当你返回 iTunes Connect 上的产品页时，将显示包详细信息并 **准备好提交** 状态。 当产品处于此状态时，您可以在沙盒环境中开始测试。 不需要在沙盒中 "提交" 产品进行测试。

[![iTunes Connect 它将显示包详细信息，并准备好提交状态](changes-to-storekit-images/image23.png)](changes-to-storekit-images/image23.png#lightbox)

这可能需要一些时间 (例如。 上传存档和更新 iTunes Connect 状态之间) 几分钟时间。 您可以单独提交产品以供查看，也可以与应用程序二进制文件一起提交。 仅在 Apple 正式批准后，它才会在生产应用商店中提供，以供在应用中购买。

### <a name="pkg-file-format"></a>.PKG 文件格式

使用 Xcode 和存档工具创建并上载托管的内容包意味着你永远看不到包本身的内容。 为示例应用程序创建的包中的文件和目录类似于下面的屏幕截图，根目录中的 **info.plist** 文件和 **目录** 子目录中的产品文件：

[![根目录中的 info.plist 文件和目录子目录中的产品文件](changes-to-storekit-images/image24.png)](changes-to-storekit-images/image24.png#lightbox)

请注意包的目录结构 (尤其是子目录中文件的位置 `Contents`) 因为你将需要了解此信息以便从设备上的包中提取文件。

### <a name="updating-package-content"></a>正在更新包内容

在批准内容后更新内容的过程：

- 在 Xcode 中编辑应用内购买内容项目。
- 增加了版本数。
- 请重新上传到 iTunes Connect。 后续购买者会自动获取最新版本，但已有旧版本的用户将不会收到任何通知。
- 您的应用程序负责通知用户并鼓励他们检索更新版本的内容。 应用程序还必须生成一个函数，该函数使用应用商店工具包的还原功能来下载新版本。
- 若要确定是否存在较新的版本，可以在应用中生成一个功能以提取 SKProducts (例如。 用于检索产品价格) 和比较 ContentVersion 属性的相同过程。

## <a name="purchasing-overview"></a>购买概述

阅读本部分之前，请查看现有的 [应用内购买文档](~/ios/platform/in-app-purchasing/index.md)。

在此图中演示了具有托管内容的产品购买和下载时发生的事件序列：

[![当购买包含托管内容的产品并下载时所发生的事件的顺序](changes-to-storekit-images/image25.png)](changes-to-storekit-images/image25.png#lightbox)

1. 新产品可在 iTunes Connect 中创建，并启用托管内容。 实际内容在 Xcode (中单独构造，只需将文件拖入文件夹) 然后存档并上传到 iTunes (无需) 编码。 然后，每个产品都将提交以供审批。 在示例代码中，这些产品 Id 是硬编码的，但如果将可用产品列表存储在远程服务器上，则使用 Apple 托管内容更灵活，以便在将新产品和内容提交到 iTunes Connect 时可以进行更新。
1. 当用户购买产品时，将在付款队列中放置一个交易以便进行处理。
1. 商店工具包将采购申请转发给 iTunes 服务器进行处理。
1. 在 iTunes 服务器上完成事务 (例如。 ) 向客户收取费用，并将收据返回到应用，其中附加了产品信息（包括是否 (可下载），其中包括文件大小和其他元数据) 。
1. 你的代码应检查产品是否可下载，如果是，还会将内容下载请求放入付款队列。 商店工具包将此请求发送到 iTunes 服务器。
1. 服务器将内容文件返回到存储工具包，它提供了一个回调，用于将下载进度和剩余时间估算返回到代码。
1. 完成后，会收到通知，并将文件位置传递到缓存文件夹中。
1. 你的代码应复制并验证这些文件，并保存你需要记住的产品是否已购买的任何状态。 利用此机会在新文件上正确设置备份标志 (提示：如果这些文件来自服务器并且永远不会由用户编辑，则您可能会跳过对它们的备份，因为用户在将来的) 中始终可以从 Apple 的服务器检索这些文件。
1. 调用 FinishTransaction。 此步骤非常重要，因为它会从付款队列中删除事务。 在将内容复制到缓存目录之外之前，不要调用 FinishTransaction，这一点也很重要。 调用 FinishTransaction 后，可能会快速清除缓存的文件。

## <a name="implementing-hosted-content-purchase"></a>实现托管内容购买

应将以下信息与完整的 [应用内购买文档](~/ios/platform/in-app-purchasing/index.md)一起阅读。 本文档中的信息重点介绍托管内容与以前的实现之间的差异。

### <a name="classes"></a>类

添加或更改了以下类以支持 iOS 6 中的托管内容：

- **SKDownload** –表示正在进行的下载的新类。 API 允许每个产品有多个，但最初仅实现一个。
- **SKProduct** –添加的新属性： `Downloadable` 、 `ContentVersion` 、 `ContentLengths` 数组。
- **SKPaymentTransaction** –添加的新属性： `Downloads` ，其中包含对象的集合（  `SKDownload` 如果此产品具有可供下载的托管内容）。
- **SKPaymentQueue** –添加的新方法： `StartDownloads` 。 对对象调用此方法 `SKDownload` 以获取其所承载的内容。 下载可以在后台进行。
- **SKPaymentTransactionObserver** –新方法： `UpdateDownloads` 。 应用商店工具包通过有关当前下载操作的进度信息调用此方法。

新类的详细信息 `SKDownload` ：

- **进度** –一个介于0-1 之间的值，该值可用于向用户显示完成百分比指示器。 不要使用进度 = = 1 来检测下载是否完成，检查状态 = = 是否已完成。
- **TimeRemaining** –估计剩余下载时间（以秒为单位）。 -1 表示仍在计算估算值。
- **状态** –活动、等待、完成、失败、已暂停、已取消。
- **ContentURL** –内容置于磁盘上的文件位置，位于  `Cache` 目录中。 仅在下载完成后填充。
- **错误** –如果状态为 "失败"，则检查此属性。

示例代码中的类之间的交互显示在此关系图中， (特定于托管内容购买的代码显示为绿色) ：

[![此关系图中以绿色显示托管内容购买](changes-to-storekit-images/image26.png)](changes-to-storekit-images/image26.png#lightbox)

本部分的其余部分显示了已使用这些类的示例代码：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver) 

更改现有 `UpdatedTransactions` 替代以检查可下载内容，并 `StartDownloads` 在必要时调用：

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

新重写 `UpdatedDownloads` 的方法如下所示。 应用商店工具包在中触发后调用此方法 `StartDownloads` `UpdatedTransactions` 。 此方法将按不确定的 *间隔多次调用* ，以提供下载进度，并在下载完成后再次进行。 请注意，方法接受对象的数组 `SKDownload` ，因此每个方法调用都可以在队列中提供多个下载的状态。 如以下实现中所示，每次都会检查下载状态，并采取适当的措施。

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate) 

此类包含一个新方法 `SaveDownload` ，该方法在每次下载成功完成后调用。

已成功下载托管内容，并将其解压缩到 `Cache` 目录。 的结构。.PKG 文件要求将所有文件保存在子目录中 `Contents` ，因此以下代码从子目录中提取文件 `Contents` 。

此代码循环访问内容包中的所有文件，并将它们复制到 `Documents` 名为的子文件夹中的目录中 `ProductIdentifier` 。 最后，它会调用 `CompleteTransaction` ，该调用 `FinishTransaction` 将从付款队列中删除事务。

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

`FinishTransaction`调用时，已下载的文件将不再保证位于 `Cache` 目录中。 在调用之前，应复制所有文件 `FinishTransaction` 。

## <a name="other-considerations"></a>其他注意事项

上面的示例代码演示了托管内容购买的一个相当简单的实现。 还必须考虑一些其他事项：

### <a name="detecting-updated-content"></a>检测更新的内容

尽管可以更新托管的内容包，但应用商店工具包并不提供将这些更新推送到已下载并购买产品的用户的任何机制。 若要实现此功能，你的代码可以在 `SKProduct.ContentVersion` `SKProduct`) 定期时检查新属性 (`Downloadable` ，并检测该值是否递增。 或者，你可以构建推送通知系统。

### <a name="installing-updated-content-versions"></a>安装更新的内容版本

如果文件已存在，上面的示例代码将跳过文件复制。 如果希望支持下载的更新版本，这不是一个好主意。

另一种方法是将内容复制到版本的名为的文件夹中，并跟踪当前版本 (例如）。 在 `NSUserDefaults` 或任何存储已完成的购买记录) 。

### <a name="restoring-transactions"></a>还原事务

`SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`调用时，商店工具包返回该用户以前的所有事务。 如果他们购买了大量的物品，或者每个购买的内容包都很大，则还原可能会导致大量的网络流量，因为所有内容都将排队等待下载。

请考虑跟踪产品是否已从关联内容包的实际下载中单独购买。

### <a name="pausing-restarting-and-canceling-downloads"></a>暂停、重新启动和取消下载

尽管示例代码不演示此功能，但可以暂停和重新启动托管内容下载。 `SKPaymentQueue.DefaultQueue`具有 `PauseDownloads` 、和的方法 `ResumeDownloads` `CancelDownloads` 。

如果在 `FinishTransaction` 下载之前对付款队列调用代码 `Finished` ，则会自动取消下载。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>在下载的内容上设置 SKIP-备份标志

Apple 的 iCloud 备份指导原则建议不要备份从服务器轻松还原的非用户内容， (因为这将 *不* 必要地占用 iCloud 存储) 。 有关设置备份属性的详细信息，请参阅 [文件系统](~/ios/app-fundamentals/file-system.md) 文档。

## <a name="summary"></a>总结

本文介绍了 iOS6 中的应用商店工具包的两项新功能：从应用中购买 iTunes 和其他内容，并利用 Apple 服务器来托管你自己的应用内购买。 本简介应与现有的 [应用内购买文档](~/ios/platform/in-app-purchasing/index.md) 一起阅读，以全面了解如何实现商店工具包功能。

## <a name="related-links"></a>相关链接

- [StoreKit (示例) ](/samples/xamarin/ios-samples/storekit)
- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit Framework 参考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 类引用](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 视频：利用商店工具包销售产品](https://developer.apple.com/videos/wwdc/2012/?include=302#302)