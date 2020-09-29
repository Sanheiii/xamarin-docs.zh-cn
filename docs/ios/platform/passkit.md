---
title: Xamarin 中的 PassKit
description: 钱包应用允许 iOS 用户在其设备上存储数字刀路。 PassKit 框架允许开发人员以编程方式与传递交互。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2018
ms.openlocfilehash: edbdf656774026eab66ada748bedb61cead3a31a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435331"
---
# <a name="passkit-in-xamarinios"></a>Xamarin 中的 PassKit

IOS 钱包应用允许用户在其设备上存储数字刀路。
这些阶段由商家生成，并通过电子邮件、Url 或商家自己的 iOS 应用发送给客户。 这些刀路可以表示各种因素，从电影入场券到会员卡再到 "刀路"。 PassKit 框架允许开发人员以编程方式与传递交互。

本文档介绍钱包并将 PassKit API 与 Xamarin 配合使用。

 [![钱包在手机上存储和组织所有刀路](passkit-images/image1.png)](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>要求

本文档中讨论的 PassKit 功能需要 iOS 6 和 Xcode 4.5 以及 Xamarin 6.0。

## <a name="introduction"></a>简介

PassKit 解决的关键问题是分发和管理条形码。 当前使用条码的方式的一些真实示例包括：

- **在线购买电影入场券** –客户通常通过电子邮件发送表示其票证的条形码。 将打印此条形码，并将其转到电影以进行输入。
- **会员卡** –客户在其钱包或钱包中携带大量不同的商店特定卡，以便在购买货物时进行显示和扫描。
- **赠券** –赠券通过电子邮件分发，作为可打印网页，通过 letterboxes，并作为报纸和杂志中的条形码。 客户将他们带入商店，以便在退货中接收商品、服务或折扣。
- **入职** pass –类似于购买电影票据。

PassKit 提供了一种替代方案：

- **电影票据** –购买后，客户通过电子邮件或) 的网站链接添加了事件票证 pass (。 作为电影方法的时间，刀路会自动在锁屏界面上显示为提醒，在电影院上，可以轻松地检索刀路，并将其显示在钱包中进行扫描。
- **会员卡** –除了提供物理卡) 以外，商店还可以通过电子邮件或在网站登录) 商店卡后发出 ( (。 该商店可以提供其他功能，例如通过推送通知更新 pass 上的帐户余额，使用地理位置服务当客户在商店位置附近时，传递可能会自动出现在锁屏界面上。
- **赠券** –可以通过独特的特征轻松生成赠券，通过电子邮件或网站链接进行跟踪。 当用户在特定位置附近时，或在给定日期 (如到期日期接近) 时，下载的优惠券会自动出现在锁屏界面上。 由于优惠券存储在用户的手机上，因此它们始终很方便，并且不会出现错放。 赠券可能鼓励客户下载相关应用，因为应用商店链接可以合并到 Pass 中，从而增加与客户的参与。
- **入职** pass –在联机签入过程之后，客户将通过电子邮件或链接接收其参与 pass。 传输提供商提供的伴随应用可能包括签入过程，还允许客户执行其他功能，例如选择其座位或食物。 当传输延迟或取消时，传输提供程序可以使用推送通知来更新传递。 随着入职时间的接近，传递将作为提醒出现在锁屏界面上，并提供对 Pass 的快速访问。

PassKit 提供了一种简单而方便的方法，可在 iOS 设备上存储和显示条形码。 随着额外的时间和位置锁定屏幕集成，推送通知和配套应用程序集成可为非常复杂的销售、票证和计费服务提供基础。

## <a name="passkit-ecosystem"></a>PassKit 生态系统

PassKit 不只是 CocoaTouch 内的 API，而是更大的应用、数据和服务生态系统的一部分，有助于安全共享和管理条形码及其他数据。 此高级关系图显示了在创建和使用传递过程中可以涉及的不同实体：

 [![此高级别关系图显示了在创建和使用 pass 过程中涉及的实体](passkit-images/image2.png)](passkit-images/image2.png#lightbox)

生态系统的每个部分都有明确定义的角色：

- **钱包** –存储并显示刀路的 Apple 内置 iOS 应用。 这是用于在现实世界 (的情况下呈现的传递的唯一位置，该位置将显示条码以及 pass) 中的所有本地化数据。
- **伴生应用** –由 pass 提供商构建的 iOS 6 应用程序，用于扩展其发出的传递的功能，例如，将值添加到存储卡、更改代表的座位或其他特定于业务的功能。 要使 pass 有用，传递应用不是必需的。
- **你的服务器** -一种安全服务器，可在其中生成传递并对其进行签名以进行分发。 你的附属应用可能会连接到你的服务器，以生成新的 pass 或请求更新现有的阶段。 你可以选择实现钱包为更新通过而调用的 web 服务 API。
- **Apns 服务器** –您的服务器能够使用 APNS 在给定设备上通知经过更新的钱包。 将通知推送到钱包，然后将联系你的服务器以获取更改的详细信息。 伴生应用不需要为此功能实现 APNS (它们可以侦听  `PKPassLibraryDidChangeNotification` ) 。
- **管道应用** 程序-不直接操作 (的应用程序（例如，配套应用程序) ），但它可以通过识别 pass 并允许将其添加到钱包来改善其实用工具。 邮件客户端、社交网络浏览器和其他数据聚合应用程序都可能会遇到要传递的附件或链接。

整个生态系统看起来很复杂，因此，值得注意的是，某些组件是可选的，并且可能会有更简单的 PassKit 实现。

## <a name="what-is-a-pass"></a>什么是传递？

Pass 是表示票据、优惠券或卡片的数据集合。 它可能适用于单个 (，因此包含航班号和座位分配等详细信息) 或者可以由任意数量的用户共享的多个使用令牌 (如折扣优惠券) 。 [有关 "传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)" 文档中提供了有关详细说明，请访问 Apple。

### <a name="types"></a>类型

目前有五种受支持的类型，可通过刀路的布局和上边缘来区分钱包应用：

- **事件票证** –小型半圆切口。
- **入职 Pass** –两侧的凹槽，可以 (例如指定传输特定的图标。 总线、定型、飞机) 。
- **存储卡** –向上舍入，如信用卡或借记卡。
- **优惠券** – perforated 在顶部。
- **一般** –与存储卡相同，向上舍入。

此屏幕截图中显示了五个传递类型， (按顺序：赠券、通用、商店卡、入职 pass 和事件票证) ：

 [![此屏幕截图显示了五种传递类型](passkit-images/image3.png)](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>文件结构

传递文件实际上是扩展名为 **pkpass** 的 ZIP 存档，其中包含一些特定的 JSON 文件 (所需的) 、 (可选) 中的各种图像文件以及本地化字符串 (也是可选) 。

- **pass.js** （必需）。 包含通过的所有信息。
- **manifest.js** （必需）。 包含传递中每个文件的 SHA1 哈希，签名文件和此文件 ( # B0 on) 。
- **签名** –必需。 通过  `manifest.json` 使用 IOS 预配门户中生成的证书对文件进行签名来创建。
- **logo.png** –可选。
- **background.png** –可选。
- **icon.png** –可选。
- 可**本地化的字符串文件**–可选。

传递文件的目录结构如下所示 (这是 ZIP 存档) 的内容：

 [![传递文件的目录结构如下所示](passkit-images/image4.png)](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.js

JSON 格式是因为通常在服务器上创建传递，这意味着在服务器上，生成代码是不可知的。 每个阶段的三个关键信息是：

- **teamIdentifier** –这会将生成的所有传递链接到应用商店帐户。 此值在 iOS 预配门户中可见。
- **passTypeIdentifier** –如果生成了多个类型) ，则在预配门户中注册以组合 (。 例如，咖啡店可能会创建商店卡 pass 类型，以允许其客户获得会员信用额度，还可以创建单独的优惠券 pass 类型来创建和分发折扣优惠券。 该咖啡店甚至可能包含实时音乐事件，并针对这些活动发出事件票证传递。
- **serialNumber** –此中的唯一字符串  `passTypeidentifier` 。 该值对于钱包是不透明的，但对于在与服务器通信时跟踪特定经历非常重要。

每次传递中有大量其他 JSON 密钥，示例如下所示：

```
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>条形码

仅支持2D 格式： PDF417、Aztec、QR。 Apple 声称，一 unsuited 的条形码在 backlit 的电话屏幕上进行扫描。

条形码下显示的备用文本是可选的–某些商家希望能够手动读取/键入内容。

ISO-8859-1 编码是最常见的，可检查将读取你的阶段的扫描系统所使用的编码。

### <a name="relevancy-lock-screen"></a>锁定屏幕)  (关联性

有两种类型的数据可导致在锁屏界面上显示传递：

 **位置**

在一次中最多可以指定10个位置，例如客户经常访问的商店，或电影院或机场的位置。 客户可以通过配套应用设置这些位置，或者提供商可以通过使用情况数据 (来确定这些位置（如果使用客户的权限) 收集的）。

如果在锁屏界面上显示了 pass，则会计算出一个围栏，这样当用户离开该区域时，将会在锁定屏幕中隐藏刀路。 Radius 绑定到传递样式，以防止滥用。

 **日期和时间**

在一个阶段中只能指定一个日期/时间。 "日期和时间" 可用于触发用于 "刀路" 和 "活动" 票证的锁屏提醒。

可以通过推送或 via PassKit API 进行更新，以便在使用多个使用 (票证时可以更新日期/时间，例如，将季节票据发送到一个或多个体育) 。

### <a name="localization"></a>本地化

翻译成多种语言的翻译方式类似于本地化 iOS 应用程序–使用扩展名创建语言特定的目录 `.lproj` ，并将本地化的元素放置在内部。 应将文本翻译输入到 `pass.strings` 文件中，而本地化的图像应与在 Pass 根中替换的图像的名称相同。

## <a name="security"></a>安全性

将使用你在 iOS 预配门户中生成的私有证书对传递进行签名。 对通过进行签名的步骤如下：

1. 计算传递目录中每个文件的 SHA1 哈希 (不包括  `manifest.json` 或  `signature` 文件，这两个位置都不应存在于) 。
1. 编写  `manifest.json` 为每个文件名的 JSON 键/值列表及其哈希值。
1. 使用证书对文件进行签名  `manifest.json` ，并将结果写入到名为的文件  `signature` 。
1. 压缩所有内容，并为结果文件指定 `.pkpass` 文件扩展名。

因为你需要使用私钥对传递进行签名，所以仅应在你控制的安全服务器上执行此过程。 不要分发密钥来尝试在应用程序中生成 pass。

## <a name="configuration-and-setup"></a>配置和设置

本部分包含的说明可帮助设置预配详细信息并创建第一个阶段。

### <a name="provisioning-passkit"></a>预配 PassKit

为了使 pass 进入应用商店，必须将其链接到开发人员帐户。 这需要两个步骤：

1. 必须使用唯一标识符（称为传递类型 ID）注册传递。
1. 必须生成有效的证书才能使用开发人员的数字签名对传递进行签名。

若要创建 Pass 类型 ID，请执行以下操作。

#### <a name="create-a-pass-type-id"></a>创建传递类型 ID

第一步是为要支持的每种不同 _类型_ 的传递设置传递类型 ID。 Pass ID (或 Pass 类型标识符) 为传递创建唯一标识符。 我们将使用此 ID 将该传递与开发人员帐户结合使用证书。

1. 在  [IOS 设置门户的 "证书、标识符和配置文件" 部分](https://developer.apple.com/account/overview.action)中，导航到 "  **标识符** "，然后选择 "  **传递类型 id** "。 然后选择 **+** 按钮以创建新的 "传递类型"： "新建" " [ ![ 传递类型](passkit-images/passid.png)](passkit-images/passid.png#lightbox)"

2. 为通过 (名称) 和**标识符** (唯一字符串) 提供**说明**。 请注意，所有传递类型 id 必须以 `pass.` 在此示例中使用的字符串开头，我们将使用 `pass.com.xamarin.coupon.banana` ： [ ![ 提供说明和标识符](passkit-images/register.png)](passkit-images/register.png#lightbox)

3. 按 " **注册** " 按钮确认传递 ID。

#### <a name="generate-a-certificate"></a>生成证书

若要为此 Pass 类型 ID 创建新证书，请执行以下操作：

1. 从列表中选择新创建的 "传递 id"，然后单击 "**编辑**"： [ ![ 从列表中选择新的 pass id](passkit-images/pass-done.png)](passkit-images/pass-done.png#lightbox)

    然后选择 "**创建证书 ...** " :

    [![选择 "创建证书"](passkit-images/cert-dist.png)](passkit-images/cert-dist.png#lightbox)

2. 按照以下步骤创建 (CSR) 的证书签名请求。
  
3. 按开发人员门户中的 " **继续** " 按钮，并上传 CSR 以生成证书。

4. 下载证书，并双击它以将其安装在密钥链中。

现在，我们已创建了此 Pass 类型 ID 的证书，接下来的部分将介绍如何手动生成 pass。

有关为钱包预配的详细信息，请参阅 [使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) 指南。

### <a name="create-a-pass-manually"></a>手动创建 Pass

现在，我们已创建了 Pass 类型，接下来可以手动在模拟器或设备上创建一个 pass 测试。 创建传递的步骤如下：

- 创建目录以包含传递文件。
- 在包含所有所需数据的文件上创建 pass.js。
- 如果需要) ，请在文件夹中包含图像 (。
- 计算文件夹中每个文件的 SHA1 哈希，并在上写入 manifest.js。
- 用下载的证书 p12 文件对 manifest.js进行签名。
- 压缩目录的内容，并将其重命名为 pkpass 扩展名。

本文的 [示例代码](/samples/xamarin/ios-samples/passkit) 中有一些源文件可用于生成通过。 使用 CreateAPassManually 目录的目录中的文件 `CouponBanana.raw` 。 存在下列文件：

 [![存在这些文件](passkit-images/image18.png)](passkit-images/image18.png#lightbox)

打开 pass.js，并编辑 JSON。 至少必须更新 `passTypeIdentifier` 和， `teamIdentifer` 才能匹配 Apple 开发人员帐户。

```json
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

然后，必须计算每个文件的哈希值并创建 `manifest.json` 文件。 完成后，它将如下所示：

```json
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下来，必须使用为此传递类型 ID 生成 ( p12 文件) 的证书为此文件生成签名。

#### <a name="signing-on-a-mac"></a>在 Mac 上签名

从[Apple 下载](https://developer.apple.com/downloads/index.action?name=Passbook)站点下载**钱包 Seed 支持材料**。 使用 `signpass` 工具将文件夹转换为 pass (这还会计算 SHA1 哈希，并将输出压缩为 pkpass 文件) 。

#### <a name="testing"></a>测试

如果要检查这些工具的输出 (通过将文件名设置为 .zip，然后将其打开) ，你会看到以下文件 (注意：添加了 `manifest.json` 和 `signature` 文件) ：

 [![检查这些工具的输出](passkit-images/image19.png)](passkit-images/image19.png#lightbox)

签名后，压缩并重命名文件 (例如。 若要 `BananaCoupon.pkpass`) 可以将其拖动到模拟器进行测试，或通过电子邮件将其发送给你自己以在真实设备上检索。 应该会看到一个屏幕来 **添加** 刀路，如下所示：

 [![添加 "传递" 屏幕](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

通常，该过程会在服务器上自动执行，但对于仅创建不需要后端服务器支持的优惠券的小型企业，手动传递创建可能是一个选项。

## <a name="wallet"></a>Wallet

钱包是 PassKit 生态系统的核心部分。 此屏幕截图显示了空钱包，并说明了传递列表和各个刀路的外观：

 [![此屏幕截图显示了空钱包，并说明了传递列表和各个刀路的外观](passkit-images/image21.png)](passkit-images/image21.png#lightbox)

钱包的功能包括：

- 只会将传递的位置与其条形码一起用于扫描。
- 用户可以更改更新的设置。 如果启用，推送通知可以触发对传递中数据的更新。
- 用户可以启用或禁用锁定屏幕集成。 如果启用此项，则可根据嵌入在传递中的相关时间和位置数据，自动在其锁定屏幕上显示。
- 如果在传递 JSON 中提供了 web 服务器 URL，则 pass 的反向支持请求刷新。
- 如果在传递 JSON 中提供应用的 ID，则可以 (或下载相关应用) 。
- 可以 (使用经过) 的清除动画删除传递。

## <a name="adding-passes-into-wallet"></a>将刀路添加到钱包

可以通过以下方式将 pass 添加到钱包：

- **管道应用程序** -这些操作不会直接操作传递，它们只是加载传递文件并向用户显示将其添加到钱包的选项。 

- **附属应用** -这些应用程序由提供商编写，用于分发刀路，并提供其他功能来浏览或编辑这些应用。 Xamarin iOS 应用程序可以完全访问 PassKit API，以创建和操作 pass。 然后，可以使用将传递添加到钱包 `PKAddPassesViewController` 。 本文档的相关 **应用程序** 部分详细介绍了此过程。

### <a name="conduit-applications"></a>管道应用程序

管道应用程序是可代表用户接收传入的中间应用，应对其进行编程以识别其内容类型，并提供要添加到钱包中的功能。 管道应用的示例包括：

- **Mail** –将附件识别为 Pass。
- **Safari** –在单击 "传递 URL" 链接时识别 "传递内容类型"。
- **其他自定义应用** –接收附件或打开 (社交媒体客户端、邮件读取器等) 的链接的任何应用。

此屏幕截图显示了 iOS 6 中的 **邮件** 如何识别传递附件，并在) 产品/服务 **将其添加** 到钱包时 (。

 [![此屏幕截图显示了 iOS 6 中的邮件如何识别传递附件](passkit-images/image22.png)](passkit-images/image22.png#lightbox)

 [![此屏幕截图显示了 Mail 如何向钱包添加传递附件](passkit-images/image23.png)](passkit-images/image23.png#lightbox)

如果要构建的应用可能是用于传递的管道，可以通过以下方式识别它们：

- **文件扩展名** -. pkpass
- **MIME 类型** -application/vnd.apple.mpegurl/pkpass
- **UTI** – pkpass

管道应用程序的基本操作是检索传递文件并调用 PassKit 的， `PKAddPassesViewController` 以便为用户授予向其钱包添加传递的选项。 此视图控制器的实现将在下一节中介绍的 **应用程序**。

不需要针对特定的传递类型 ID 设置管道应用程序，这与伴随应用程序的方式相同。

## <a name="companion-applications"></a>助理应用程序

伴生应用程序提供了用于处理 Pass 的附加功能，包括创建 Pass、更新与 Pass 关联的信息，以及管理与应用程序关联的传递。

助理应用程序不应尝试复制钱包的功能。 它们不用于显示扫描的通过。

本部分的其余部分介绍如何生成与 PassKit 交互的基本辅助应用。

### <a name="provisioning"></a>设置

因为钱包是一种存储技术，所以需要单独预配应用程序，并且不能使用团队预配配置文件或通配符应用 ID。 请参阅 [使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) 指南，为钱包应用程序创建唯一的应用程序 ID 和预配配置文件。

### <a name="entitlements"></a>权利

**Info.plist**文件应包含在所有最新的 Xamarin iOS 项目中。 若要添加新的 info.plist 文件，请按照 [使用权利](~/ios/deploy-test/provisioning/entitlements.md) 指南中的步骤进行操作。

若要设置权利，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

双击 "Solution Pad 中的 **info.plist** 文件以打开 info.plist 编辑器：

![Settings.plst 编辑器](passkit-images/image31.png)

在 "钱包" 部分下，选择 " **启用钱包** " 选项

![启用钱包权利](passkit-images/image32.png)

默认选项是应用允许所有传递类型。 但是，可以限制你的应用程序，并且只允许一部分团队传递类型。 若要启用此选择，请选择 " **允许团队传递类型子集** "，然后输入你希望允许的子集的 "传递类型" 标识符。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

双击 **info.plist** 文件以打开 XML 源文件。

若要添加钱包权利，请在下拉列表中将 **属性** 设置为， `Passbook Identifiers` 这将自动设置 **类型** `Array` 。 然后，将字符串 **值** 设置为 `$(TeamIdentifierPrefix)*` ：

![启用钱包权利](passkit-images/image33.png)

这会使应用允许所有凭证类型。 若要限制应用并仅允许一部分团队凭证类型，请将字符串值设置为 

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中 `pass.$(CFBundleIdentifier)` ，是[上面](~/ios/platform/passkit.md)创建的传递 ID

-----

### <a name="debugging"></a>调试

如果在部署应用程序时遇到问题，请检查是否正在使用正确的**预配配置文件**，以及 `Entitlements.plist` 是否在**iPhone 捆绑签名**选项中选择作为**自定义权利**文件。

如果在部署时遇到此错误：

```
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

否则， `pass-type-identifiers` 权利数组 (不正确，或者与 **预配配置文件**) 不匹配。 验证 Pass 类型 Id 和你的团队 ID 是否正确。

## <a name="classes"></a>类

以下 PassKit 类可用于应用程序访问：

- **PKPass** –传递的实例。
- **PKPassLibrary** –提供用于访问设备上的传递的 API。
- **PKAddPassesViewController** -用于显示用户在其钱包内保存的通过。
- **PKAddPassesViewControllerDelegate** – Xamarin iOS 开发人员

## <a name="example"></a>示例

请参阅本文中的 [示例代码](/samples/xamarin/ios-samples/passkit) 中的 PassLibrary 项目。 它演示了在钱包伴应用程序中需要的以下常见函数：

### <a name="check-that-wallet-is-available"></a>检查钱包是否可用

钱包在 iPad 上不可用，因此应用程序应在尝试访问 PassKit 功能之前进行检查。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>创建传递库实例

PassKit 库不是单一实例，应用程序应创建并存储和实例，以访问 PassKit API。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>获取通过列表

应用程序可以从库中请求传递列表。 此列表由 PassKit 自动筛选，因此，你只能看到已使用你的团队 ID 创建并在你的权利中列出的传递。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

请注意，模拟器不会筛选返回的传递列表，因此应始终在实际设备上测试此方法。 此列表可以显示在 UITableView 中。 添加两个优惠券后， [示例应用](/samples/xamarin/ios-samples/passkit) 如下所示：

 [![添加了两个优惠券后，示例应用如下所示](passkit-images/image29.png)](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>显示刀路

一组有限的信息可用于呈现辅助应用内的传递。

如示例代码所示，从此标准属性集中进行选择以显示刀路列表。

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

此字符串在 [示例](/samples/xamarin/ios-samples/passkit)中显示为警报：

 [![示例中所选优惠券警报](passkit-images/image30.png)](passkit-images/image30.png#lightbox)

您还可以使用 `LocalizedValueForFieldKey()` 方法从您设计的 "阶段" 中的字段中检索数据，因为您知道应该出现哪些字段)  (。 示例代码不显示此代码。

### <a name="loading-a-pass-from-a-file"></a>从文件加载传递

由于只能向钱包添加用户的权限，因此必须提供视图控制器来决定。 此代码用在示例中的 " **添加** " 按钮，可加载嵌入在应用中的预建传递 (应将其替换为已签名) ：

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

通过 " **添加** " 和 " **取消** " 选项提供传递：

 [![随 "添加" 和 "取消" 选项一起提供的传递](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>替换现有传递

替换现有的传递不需要用户的权限，但如果尚未存在此传递，则会失败。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>编辑 Pass

PKPass 不是可变的，因此不能在代码中更新传递对象。 若要更改 pass 中的数据，应用程序必须有权访问 web 服务器，该服务器可以保留传递的记录并生成一个新的传递文件，其中包含可供应用程序下载的更新值。

必须在服务器上完成传递文件创建，因为必须使用必须保密的证书来对传递进行签名。

生成更新的传递文件后，使用 `Replace` 方法覆盖设备上的旧数据。

### <a name="display-a-pass-for-scanning"></a>显示扫描的通过

如前所述，只有钱包可以显示扫描的通过。 可以使用方法显示传递 `OpenUrl` ，如下所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收更改通知

应用程序可以使用侦听对传递库所做的更改 `PKPassLibraryDidChangeNotification` 。 更改可能是由于触发了后台更新的通知引起的，因此最好在应用程序中侦听。

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

注册通知时传递库实例很重要，因为 PKPassLibrary 不是单一实例。

## <a name="server-processing"></a>服务器处理

有关构建支持 PassKit 的服务器应用程序的详细讨论超出了本文的介绍范围。

请参阅 [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook) 开源 c # 服务器端代码。

## <a name="push-notifications"></a>推送通知

有关使用推送通知更新阶段的详细讨论不在本文介绍的讨论范围之内。

需要进行更新时，你将需要实现 Apple 定义的类似于 REST 的 API，以响应来自钱包的 web 请求。

有关详细信息，请参阅 Apple 的 [更新 Pass](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) 指南。

## <a name="summary"></a>总结

本文介绍了 PassKit，其中的一些原因非常有用，并描述了必须为完全 PassKit 解决方案实现的不同部分。 本文介绍了配置 Apple 开发人员帐户以创建 pass、手动进行传递的过程，以及如何从 Xamarin iOS 应用程序访问 PassKit Api 所需的步骤。

## <a name="related-links"></a>相关链接

- [面向开发人员的钱包](https://developer.apple.com/wallet/)
- [PassKit 示例](/samples/xamarin/ios-samples/passkit)
- [钱包开发人员指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [框架– Apple Pay 和钱包 (WWDC 视频) ](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [PassKit Framework 参考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服务参考](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [关于传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook)，用于生成 iOS 钱包包的开源库
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)