---
title: Xamarin 中的 CloudKit
description: 本文档介绍如何在 Xamarin 中使用 CloudKit。 它概述了 CloudKit 并讨论了如何启用它、CloudKit 便利性 API、可伸缩性、用户帐户以及开发和生产环境。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/11/2016
ms.openlocfilehash: a01f0075ec7f47d6653313ea50047b7f89529bc6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431391"
---
# <a name="cloudkit-in-xamarinios"></a>Xamarin 中的 CloudKit

CloudKit 框架可简化访问 iCloud 的应用程序的开发。 这包括检索应用程序数据和资产权限以及能够安全地存储应用程序信息。 此工具包允许用户使用其 iCloud Id 访问应用程序，而无需共享个人信息。

开发人员可以专注于客户端应用程序，让 iCloud 不必编写服务器端应用程序逻辑。 CloudKit 提供身份验证、私有和公共数据库、结构化数据和资产存储服务。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="requirements"></a>要求

若要完成本文中所述的步骤，需要满足以下要求：

- 需要在开发人员的计算机上安装和配置**Xcode 和 IOS SDK** – Apple 的 Xcode 和 Ios 8 api。
- **Visual Studio for Mac** –应在用户设备上安装和配置 Visual Studio for Mac 的最新版本。
- **ios 8 设备** -运行最新版本 ios 8 的 ios 设备用于测试。

## <a name="what-is-cloudkit"></a>什么是 CloudKit？

CloudKit 是向开发人员授予对 iCloud 服务器的访问权限的一种方法。 它为 iCloud 驱动器和 iCloud 照片库提供基础。 MacOS 和 iOS 设备都支持 CloudKit。

[![MacOS 和 iOS 设备上支持的 CloudKit](intro-to-cloudkit-images/image1.png)](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 使用 iCloud 帐户基础结构。 如果用户登录到该设备上的 iCloud 帐户，CloudKit 将使用其 ID 来标识该用户。 如果没有可用的帐户，则将提供受限的只读访问权限。

CloudKit 支持公用和专用数据库的概念。 公共数据库提供用户有权访问的所有数据的 "soup"。 专用数据库用于存储绑定到特定用户的专用数据。

CloudKit 支持结构化数据和大容量数据。 它能够无缝地处理大型文件传输。 CloudKit 在后台与 iCloud 服务器之间有效地传输大型文件，使开发人员能够专注于其他任务。

> [!NOTE]
> 请务必注意，CloudKit 是一种 _传输技术_。 它不提供任何持久性;它仅允许应用程序有效地从服务器发送和接收信息。

到撰写本文时为止，Apple 最初免费提供 CloudKit，同时提供带宽和存储容量的高限制。 对于具有大型用户群的大型项目或应用程序，Apple 将提供经济实惠的定价比例。

## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 应用程序中启用 CloudKit

在 Xamarin 应用程序可以利用 CloudKit 框架之前，必须正确预配应用程序，如使用 [功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) 和使用 [权利](~/ios/deploy-test/provisioning/entitlements.md) 指南中所述。

若要访问 CloudKit， **info.plist** 文件必须包含 **Enable iCloud**、 **键值存储**和 **CloudKit** 权限。

### <a name="sample-app"></a>示例应用

[CloudKitAtlas 示例](/samples/xamarin/ios-samples/ios8-cloudkitatlas)演示如何将 CloudKit 与 Xamarin 配合使用。 下面的步骤演示了如何配置示例–除了需要对 CloudKit 的要求以外，它还需要其他设置：

1. 在 Visual Studio for Mac 或 Visual Studio 中打开项目。
2. 在 **解决方案资源管理器**中，打开 **info.plist** 文件，并确保 **捆绑标识符** 与在设置设置过程中创建的 **应用 ID** 中定义的标识符相匹配。
3. 向下滚动到 **info.plist** 文件的底部，选择 " **启用后台模式**"、" **位置更新**" 和 " **远程通知**"。
4. 右键单击解决方案中的 iOS 项目，然后选择 " **选项**"。
5. 选择 " **IOS 捆绑签名**"，选择上面创建的 **开发人员标识** 和 **预配配置文件** 。
6. 确保 **info.plist** 包括 **启用 iCloud**、 **键值存储**和 **CloudKit**。
7. 请确保应用程序存在 **无处不容器** 。 示例： `iCloud.com.your-company.CloudKitAtlas`
8. 保存对文件所做的更改。

设置好这些设置后，示例应用现已准备好访问 CloudKit Framework Api 以及后台、位置和通知服务。

## <a name="cloudkit-api-overview"></a>CloudKit API 概述

在 Xamarin iOS 应用程序中实现 CloudKit 之前，本文将介绍 CloudKit Framework 的基础知识，其中包括以下主题：

1. **容器** – iCloud 通信的隔离接收器。
2. **数据库** –公共和专用于应用程序。
3. **记录** –结构化数据在 CloudKit 之间移动的机制。
4. **记录区域** –是记录组。
5. **记录标识符** –完全规范化并表示记录的特定位置。
6. **引用** –提供给定数据库内相关记录之间的父子关系。
7. **资产** –允许将大型非结构化数据的文件上传到 iCloud，并将其与给定记录相关联。

### <a name="containers"></a>容器

在 iOS 设备上运行的给定应用程序始终与该设备上的其他应用程序和服务一起运行。 在客户端设备上，应用程序将以某种方式进行孤立或沙盒处理。 在某些情况下，这是一个文本沙箱，而在其他情况下，应用程序只是在自己的内存空间中运行。

使用客户端应用程序并运行与其他客户端分离的概念非常强大，并且具有以下优势：

1. **安全性** –一个应用程序不能干扰其他客户端应用程序或操作系统本身。
1. **稳定性** -如果客户端应用程序发生故障，它将无法排除 OS 的其他应用。
1. **隐私** –每个客户端应用程序都具有对存储在设备中的个人信息的有限访问权限。

CloudKit 旨在提供与上面列出的相同的优点，并将其应用到使用基于云的信息：

 [![CloudKit apps 使用容器进行通信](intro-to-cloudkit-images/image31.png)](intro-to-cloudkit-images/image31.png#lightbox)

就像应用程序是在设备上运行的多个应用程序一样，应用程序与 iCloud 一对多通信。 其中每个不同的通信接收器称为容器。

在 CloudKit 框架中通过类公开容器 `CKContainer` 。 默认情况下，一个应用程序与一个容器进行通信，此容器分隔开来该应用程序的数据。 这意味着，多个应用程序可以将信息存储到同一个 iCloud 帐户，但此信息永远不会混合。

ICloud 数据的容器化还允许 CloudKit 封装用户信息。 通过这种方式，应用程序将对 iCloud 帐户和存储在中的用户信息有一定限制的访问权限，同时仍保护用户的隐私和安全性。

容器由应用程序的开发人员通过 WW 门户进行完全管理。 容器的命名空间在所有 Apple 开发人员中都是全局性的，因此，对于给定的开发人员和应用程序，该容器不仅必须是唯一的。

Apple 建议在为应用程序容器创建命名空间时使用反向 DNS 表示法。 示例： `iCloud.com.company-name.application-name`

默认情况下，容器默认绑定到给定应用程序，可以在应用程序之间共享。 因此，多个应用程序可以协调单个容器。 单个应用程序也可以与多个容器进行通信。

### <a name="databases"></a>数据库

CloudKit 的主要功能之一是使用应用程序的数据模型和将模型复制到 iCloud 服务器。 某些信息适用于创建它的用户，其他信息是可供用户公开使用 (如餐厅审核) 的公共数据，也可以是开发人员为应用程序发布的信息。 无论是哪种情况，受众都不只是单个用户，而是用户群。

 [![CloudKit 容器关系图](intro-to-cloudkit-images/image32.png)](intro-to-cloudkit-images/image32.png#lightbox)

在容器中，首先和最重要的是公共数据库。 这就是所有公共信息生存和 mingles 的地方。 此外，应用程序的每个用户都有多个单独的专用数据库。

在 iOS 设备上运行时，应用程序将仅对当前登录的 iCloud 用户的信息具有访问权限。 因此，应用程序的容器视图将如下所示：

 [![容器的应用程序视图](intro-to-cloudkit-images/image33.png)](intro-to-cloudkit-images/image33.png#lightbox)

它只能看到公共数据库和与当前登录的 iCloud 用户相关联的专用数据库。

数据库在 CloudKit 框架中通过 `CKDatabase` 类公开。 每个应用程序都有权访问两个数据库：公共数据库和专用数据库。

容器是 CloudKit 中的初始入口点。 下面的代码可用于访问应用程序默认容器中的公共和专用数据库：

```csharp
using CloudKit;
//...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
//...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

下面是数据库类型之间的差异：

||公共数据库|私有数据库|
|---|--- |--- |
|**数据类型**|共享数据|当前用户的数据|
|**配额**|考虑开发人员的配额|在用户的配额中考虑|
|**默认权限**|世界可读性|用户可读|
|**编辑权限**|iCloud 仪表板角色通过记录类级别|空值|

### <a name="records"></a>记录

容器保存数据库，在数据库中是记录。 记录是将结构化数据移入和移出 CloudKit 的机制：

 [![容器保存数据库，在数据库中是记录](intro-to-cloudkit-images/image34.png)](intro-to-cloudkit-images/image34.png#lightbox)

记录通过 `CKRecord` 类（用于包装键值对）在 CloudKit 框架中公开。 应用程序中对象的实例等效于 `CKRecord` CloudKit 中的。 此外，每个都 `CKRecord` 拥有一种记录类型，它等效于对象的类。

记录具有实时架构，因此在移交数据进行处理之前，会将数据描述为 "CloudKit"。 从该点开始，CloudKit 将解释信息并处理存储和检索记录的后勤。

`CKRecord`类还支持范围广泛的元数据。 例如，记录包含有关创建时间和创建时间的用户的信息。 记录还包含有关它的上次修改时间和修改时间的信息。

记录包含更改标记的概念。 这是给定记录的以前版本的版本。 更改标记用于确定客户端和服务器是否具有同一版本的给定记录的轻型方法。

如上所述，将 `CKRecords` 键/值对进行包装，因此可以将以下类型的数据存储在记录中：

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`

除了单个值类型外，记录还可以包含上述任何一种类型的同类数组。

可以使用以下代码创建新记录，并将其存储在数据库中：

```csharp
using CloudKit;
//...

private const string ReferenceItemRecordName = "ReferenceItems";
//...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>记录区域

记录在给定的数据库中不存在–记录组在记录区域内同时存在。 可以将记录区域视为传统关系数据库中的表：

 [![记录组在记录区域内同时存在](intro-to-cloudkit-images/image35.png)](intro-to-cloudkit-images/image35.png#lightbox)

给定的 "记录" 区域和给定数据库中可以有多个记录。 每个数据库都包含一个默认的记录区域：

 [![每个数据库都包含默认的记录区域和自定义区域](intro-to-cloudkit-images/image36.png)](intro-to-cloudkit-images/image36.png#lightbox)

默认情况下，记录存储在此处。 此外，还可以创建自定义记录区域。 记录区域表示执行原子提交和更改跟踪的基本粒度。

## <a name="record-identifiers"></a>记录标识符

记录标识符表示为一个元组，其中包含客户端提供的记录名称和记录所在的区域。 记录标识符具有以下特征：

- 它们是由客户端应用程序创建的。
- 它们完全规范化并表示记录的特定位置。
- 通过在外部数据库中将记录的唯一 ID 分配给记录名称，可以使用它们来桥接未存储在 CloudKit 中的本地数据库。

开发人员创建新记录时，可以选择传入记录标识符。 如果未指定记录标识符，则会自动创建一个 UUID 并将其分配给记录。

当开发人员创建新记录标识符时，他们可以选择指定每个记录所属的记录区域。 如果未指定，则将使用默认记录区域。

记录标识符在 CloudKit 框架中通过 `CKRecordID` 类公开。 下面的代码可用于创建新的记录标识符：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>参考

引用在给定的数据库中提供相关记录之间的关系：

 [![引用在给定的数据库中提供相关记录之间的关系](intro-to-cloudkit-images/image37.png)](intro-to-cloudkit-images/image37.png#lightbox)

在上面的示例中，父代为子项，使子记录成为父记录的子记录。 关系从子记录转到父记录，称为 *返回引用*。

在 CloudKit 框架中通过类公开了引用 `CKReference` 。 它们是一种使 iCloud 服务器了解记录之间的关系的方式。

引用提供级联删除之后的机制。 如果从数据库中删除了父记录，则会自动从数据库中删除任何 (在关系) 中指定的子记录。

> [!NOTE]
> 使用 CloudKit 时，可以使用无关联的指针。 例如，当应用程序提取记录指针列表，选择一条记录，然后请求记录时，该记录可能不再存在于数据库中。 必须对应用程序进行编码以适当地处理这种情况。

虽然不是必需的，但在使用 CloudKit 框架时，应首选向后引用。 Apple 已经对系统进行了微调，使其成为最有效的引用类型。

创建引用时，开发人员可以提供已在内存中的记录，也可以创建对记录标识符的引用。 如果使用记录标识符，并且数据库中不存在指定的引用，则将创建一个无关联指针。

下面是针对已知记录创建引用的示例：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>资产

资产允许将大型非结构化数据的文件上传到 iCloud，并与给定记录相关联：

 [![资产允许将大型非结构化数据的文件上传到 iCloud，并将其与给定记录相关联](intro-to-cloudkit-images/image38.png)](intro-to-cloudkit-images/image38.png#lightbox)

在客户端上， `CKRecord` 将创建一个描述要上载到 iCloud 服务器上的文件的。 `CKAsset`创建以包含文件并将其链接到描述它的记录。

当文件上传到服务器时，该记录将放置在数据库中，并将文件复制到特殊的大容量存储数据库中。 在记录指针和上传的文件之间创建链接。

资产通过类在 CloudKit 框架中公开 `CKAsset` ，并用于存储大量非结构化数据。 由于开发人员绝不希望在内存中具有大量的非结构化数据，因此使用磁盘上的文件实现资产。

资产由记录拥有，这允许从 iCloud 使用记录作为指针来检索资产。 这样，在删除拥有资产的记录后，服务器可以对资产进行垃圾收集。

由于 `CKAssets` 旨在处理大型数据文件，因此 Apple 设计为 CloudKit 高效上传和下载资产。

以下代码可用于创建资产，并将其与记录相关联：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

我们现在涵盖了 CloudKit 中的所有基础对象。 容器关联到应用程序，并包含数据库。 数据库包含分组到记录区域并由记录标识符指向的记录。 父子关系在使用引用的记录之间定义。 最后，可以使用资产将大型文件上传和关联到记录。

## <a name="cloudkit-convenience-api"></a>CloudKit 便利 API

Apple 提供两个不同的 API 集用于处理 CloudKit：

- **操作 API** –提供 CloudKit 的每个功能。 对于更复杂的应用程序，此 API 提供对 CloudKit 的精细控制。
- **便利性 API** –提供常见的预配置 CloudKit 功能子集。 它为在 iOS 应用程序中包含 CloudKit 功能提供了一种方便、便捷的访问解决方案。

对于大多数 iOS 应用程序而言，便利性 API 通常是最佳选择，而 Apple 建议从它开始。 本部分的其余部分将介绍以下便利 API 主题：

- 保存记录。
- 提取记录。
- 更新记录。

### <a name="common-setup-code"></a>常见安装代码

在开始 CloudKit 便利 API 之前，需要提供一些标准的安装代码。 首先修改应用程序的 `AppDelegate.cs` 文件，使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
    }
}
```

上述代码公开了公共和专用 CloudKit 数据库作为快捷方式，使其更易于在应用程序的其余部分使用。

接下来，将以下代码添加到将使用 CloudKit 的任何视图或视图容器：

```csharp
using CloudKit;
//...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

这将添加访问的快捷方式 `AppDelegate` ，并访问上面创建的公用和专用数据库快捷方式。

使用此代码后，让我们看看如何在 Xamarin iOS 8 应用程序中实现 CloudKit 便利 API。

### <a name="saving-a-record"></a>保存记录

在讨论记录时使用上面提供的模式，下面的代码将创建一个新记录，并使用便利 API 将其保存到公共数据库：

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

有关上述代码，请注意以下三个事项：

1. 通过调用  `SaveRecord` 的方法  `PublicDatabase` ，开发人员无需指定如何发送数据、写入的区域，等等。便利性 API 负责处理所有这些细节。
1. 调用是异步的，并在调用完成时提供回调例程，不管是成功还是失败。 如果调用失败，则会提供一条错误消息。
1. CloudKit 不提供本地存储/持久性;仅限传输媒介。 因此，当请求保存记录时，会立即将其发送到 iCloud 服务器。

> [!NOTE]
> 由于移动网络通信的 "有损" 性质，连接持续被丢弃或中断，开发人员在使用 CloudKit 时必须做出的首要考虑因素之一是错误处理。

### <a name="fetching-a-record"></a>提取记录

在 iCloud 服务器上创建并成功存储了记录后，请使用以下代码检索该记录：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

就如同保存记录一样，以上代码是异步的，很简单，需要大量的错误处理。

### <a name="updating-a-record"></a>更新记录

从 iCloud 服务器中提取记录后，可以使用以下代码修改记录，并将更改保存回数据库：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

`FetchRecord`如果调用成功，则的方法将 `PublicDatabase` 返回 `CKRecord` 。 然后，应用程序会修改该记录，并 `SaveRecord` 再次调用以将更改写回到数据库。

本部分显示了应用程序在使用 CloudKit 便利 API 时将使用的典型循环。 应用程序会将记录保存到 iCloud，从 iCloud 中检索这些记录，修改记录并将这些更改保存回 iCloud。

## <a name="designing-for-scalability"></a>设计以实现可伸缩性

到目前为止，本文介绍了如何从 iCloud 服务器存储和检索应用程序的整个对象模型，每次都将使用它。 虽然这种方法非常适用于少量的数据和非常小的用户库，但当信息量和/或用户基准量增加时，它不能很好地进行缩放。

### <a name="big-data-tiny-device"></a>大数据，小型设备

应用程序的最热门程度，数据库中的数据越多，但在设备上缓存整个数据的可能性越低。 可以使用以下方法解决此问题：

- 将**大型数据保存在云中**– CloudKit 旨在有效地处理大数据。
- **客户端只应查看该数据的一个切片** –在给定时间减少处理任何任务所需的最小数据量。
- **客户端视图可以更改** –因为每个用户都有不同的首选项，正在显示的数据切片可以从用户更改为用户，而用户的任何给定切片的单个视图可能不同。
- **客户端使用查询来重点介绍视点** –查询允许用户查看云中存在的较大数据集的一个小部分。

### <a name="queries"></a>查询

如上所述，查询允许开发人员选择云中存在的较大数据集的一小部分。 查询在 CloudKit 框架中通过 `CKQuery` 类公开。

查询合并了三个不同的内容：一种记录类型 ( `RecordType`) ， () 的谓词 `NSPredicate` ，还可以是 () 的排序描述符 `NSSortDescriptors` 。 CloudKit 支持大多数 `NSPredicate` 。

#### <a name="supported-predicates"></a>支持的谓词

使用查询时，CloudKit 支持以下类型 `NSPredicates` ：

1. 匹配名称等于变量中存储的值的记录：

    ```csharp
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```

2. 允许匹配基于动态密钥值，以便在编译时不需要知道该密钥：

    ```csharp
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```

3. 匹配记录的值大于给定值的记录：

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 记录的位置在给定位置的100米以内的匹配记录：

    ```csharp
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支持标记化搜索。 此调用将创建两个标记，一个标记用于， `after` 另一个用于 `session` 。 它将返回包含这两个标记的记录：

    ```csharp
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```

6. CloudKit 支持使用运算符联接的组合谓词 `AND` 。

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```

#### <a name="creating-queries"></a>创建查询

以下代码可用于 `CKQuery` 在 Xamarin iOS 8 应用程序中创建：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它创建一个谓词来仅选择与给定名称匹配的记录。 然后，它创建一个查询，该查询将选择与谓词匹配的给定记录类型的记录。

#### <a name="performing-a-query"></a>执行查询

创建查询后，请使用以下代码执行该查询并处理返回的记录：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

上面的代码使用上面创建的查询，并对公共数据库执行该查询。 由于未指定任何记录区域，因此会搜索所有区域。 如果未发生错误，则 `CKRecords` 将返回与查询参数匹配的数组。

考虑查询的一种方法是对查询进行轮询，非常适合通过大型数据集进行切片。 然而，由于以下原因，查询不太适合用于大、大部分静态数据集：

- 它们对于设备电池寿命而言是错误的。
- 它们对于网络通信是不正确的。
- 它们对于用户体验是不正确的，因为他们看到的信息受到应用程序轮询数据库的频率的限制。 现在，用户会在发生更改时收到推送通知。

### <a name="subscriptions"></a>订阅

当处理大型的、大部分静态数据集时，不应在客户端设备上执行查询，它应该代表客户端在服务器上运行。 查询应在后台运行，并且应在每次保存单个记录后执行，不管是由当前设备还是其他设备触及同一数据库。

最后，在运行服务器端查询时，应将推送通知发送到连接到数据库的每个设备。

订阅在 CloudKit 框架中通过 `CKSubscription` 类公开。 它们将记录类型 ( `RecordType`) 、谓词 ( `NSPredicate`) 和 Apple 推送通知 () 组合在一起 `Push` 。

> [!NOTE]
> CloudKit 推送会略微增加，因为它们包含一个包含特定信息的负载，如导致推送发生的原因。

#### <a name="how-subscriptions-work"></a>订阅如何工作

在使用 c # 代码实现订阅之前，让我们快速了解订阅的工作方式：

 [![订阅如何工作的概述](intro-to-cloudkit-images/image39.png)](intro-to-cloudkit-images/image39.png#lightbox)

上图显示了典型的订阅过程，如下所示：

1. 客户端设备创建一个新的订阅，该订阅包含将触发订阅的条件集，以及触发器发生时将发送的推送通知。
2. 订阅将发送到数据库，并将其添加到现有订阅的集合中。
3. 第二个设备创建新记录，并将该记录保存到数据库。
4. 数据库会搜索其订阅列表，以查看新记录是否与其条件匹配。
5. 如果找到匹配项，则会将推送通知发送到注册了该订阅的设备，该设备的相关信息导致触发该订阅。

了解这一点后，我们来看看如何在 Xamarin iOS 8 应用程序中创建订阅。

#### <a name="creating-subscriptions"></a>正在创建订阅

下面的代码可用于创建订阅：

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

首先，它创建一个谓词，该谓词提供触发订阅的条件。 接下来，它根据特定记录类型创建订阅，并设置测试该触发器的时间的选项。 最后，它定义在触发订阅并将其附加到订阅时将发生的通知类型。

### <a name="saving-subscriptions"></a>正在保存订阅

创建订阅后，以下代码会将其保存到数据库：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用便利 API，调用是异步的，并且简单，并提供简单的错误处理。

#### <a name="handling-push-notifications"></a>处理推送通知

如果开发人员以前使用过 Apple 推送通知 (的 AP) ，则应对 CloudKit 生成的通知的过程非常熟悉。

在中 `AppDelegate.cs` ，重写 `ReceivedRemoteNotification` 类，如下所示：

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

上述代码要求 CloudKit 将用户信息分析为 CloudKit 通知。 接下来，将提取有关警报的信息。 最后，测试通知的类型并相应地处理通知。

本部分介绍了如何使用查询和订阅回答上面显示的大数据、小型设备问题。 应用程序会将其大型数据留在云中，并使用这些技术提供此数据集的视图。

## <a name="cloudkit-user-accounts"></a>CloudKit 用户帐户

正如本文开头所述，CloudKit 是在现有 iCloud 基础结构的基础上构建的。 以下部分将详细介绍如何使用 CloudKit API 向开发人员公开帐户。

### <a name="authentication"></a>身份验证

处理用户帐户时，首先要考虑的是身份验证。 CloudKit 支持通过设备上的当前登录 iCloud 用户进行身份验证。 身份验证发生在幕后，并由 iOS 进行处理。 这样，开发人员就不必担心实现身份验证的详细信息。 它们只会测试用户是否已登录。

### <a name="user-account-information"></a>用户帐户信息

CloudKit 向开发人员提供以下用户信息：

- **标识** –唯一标识用户的方式。
- **元数据** –保存和检索有关用户的信息的能力。
- **隐私** –所有信息都在隐私意识 manor 中得到处理。 除非用户同意，否则不会公开任何内容。
- **发现** –使用户能够发现使用同一应用程序的朋友。

接下来，我们将详细介绍这些主题。

#### <a name="identity"></a>标识

如上所述，CloudKit 为应用程序提供了一种唯一标识给定用户的方法：

 [![唯一确定给定用户](intro-to-cloudkit-images/image40.png)](intro-to-cloudkit-images/image40.png#lightbox)

在用户的设备上运行的客户端应用程序以及 CloudKit 容器中的所有特定用户专用数据库。 客户端应用程序将链接到这些特定用户之一。 这取决于在设备本地登录到 iCloud 的用户。

因为这是来自 iCloud，所以有一个丰富的后备存储的用户信息。 因为 iCloud 实际上是托管容器，所以它可以关联用户。 在上图中，其 iCloud 帐户  `user@icloud.com` 链接到当前客户端的用户。

在容器基础上，将创建唯一的、随机生成的用户 ID，并将其与用户的 iCloud 帐户关联 (电子邮件地址) 。 此用户 ID 将返回到应用程序，并可供开发人员认为合适的任何方式使用。

> [!NOTE]
> 同一 iCloud 用户在同一设备上运行的不同应用程序将具有不同的用户 Id，因为它们连接到不同的 CloudKit 容器。

以下代码获取设备上当前已登录 iCloud 用户的 CloudKit 用户 ID：

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

上面的代码要求 CloudKit 容器提供当前已登录用户的 ID。 由于此信息来自 iCloud 服务器，因此调用是异步的，需要进行错误处理。

#### <a name="metadata"></a>Metadata

CloudKit 中的每个用户都具有描述它们的特定元数据。 此元数据表示为 CloudKit 记录：

 [![CloudKit 中的每个用户都具有描述它们的特定元数据](intro-to-cloudkit-images/image41.png)](intro-to-cloudkit-images/image41.png#lightbox)

在专用数据库中查找容器的特定用户，有一个定义该用户的记录。 公共数据库中有许多用户记录，容器的每个用户各有一个。 其中一项将包含与当前登录的用户的记录 ID 相匹配的记录 ID。

公共数据库中的用户记录是世界上可读的。 在大多数情况下，它们被视为普通记录，并且具有一种类型的 `CKRecordTypeUserRecord` 。 这些记录由系统保留，不能用于查询。

使用以下代码访问用户记录：

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

上面的代码要求公共数据库返回上述用户记录的用户记录。 由于此信息来自 iCloud 服务器，因此调用是异步的，需要进行错误处理。

#### <a name="privacy"></a>隐私

默认情况下，CloudKit 为设计保护当前登录用户的隐私。 默认情况下，不公开有关用户的个人标识信息。 在某些情况下，应用程序将需要有关用户的有限信息。

在这些情况下，应用程序可以请求用户泄露此信息。 系统将向用户显示一个对话框，询问他们是否选择公开其帐户信息。

#### <a name="discovery"></a>发现

假设用户已选择允许应用程序访问其用户帐户信息的权限，则可以通过应用程序的其他用户发现这些用户：

 [![用户可以在应用程序的其他用户中发现](intro-to-cloudkit-images/image42.png)](intro-to-cloudkit-images/image42.png#lightbox)

客户端应用程序正在与一个容器进行通信，而容器正在对话 iCloud 访问用户信息。 用户可以提供电子邮件地址，发现可用于获取有关用户的信息。 另外，还可以使用用户 ID 来发现有关用户的信息。

CloudKit 还提供了一种方法，用于通过查询整个通讯簿来发现可能为当前已登录 iCloud 用户的朋友的任何用户的相关信息。 CloudKit 进程将请求用户的 Contact 账簿，并使用电子邮件地址查看是否可以找到与这些地址匹配的其他用户的应用程序。

这样，应用程序便可以利用用户的联系人书籍，而无需提供对联系人的访问权限或要求用户批准对联系人的访问。 在任何时候都不能提供应用程序的联系信息，只有 CloudKit 进程有权访问。

概括而言，有三种不同类型的输入可用于用户发现：

- **用户记录 ID** -可以针对当前已登录的 CloudKit 用户的用户 ID 执行发现。
- **用户电子邮件地址** –用户可以提供电子邮件地址，并可用于发现。
- **联系簿** –用户的通讯簿可用于发现应用程序的用户，该用户的电子邮件地址与联系人中列出的相同。

用户发现将返回以下信息：

- **用户记录 ID** -公共数据库中用户的唯一 ID。
- **名字和姓氏** ，存储在公共数据库中。

只会为已选择发现的用户返回此信息。

以下代码将发现有关设备上当前登录到 iCloud 的用户的信息：

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
//...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

使用以下代码查询联系人书籍中的所有用户：

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

在本部分中，我们介绍了 CloudKit 可提供给应用程序的用户帐户的四个主要区域。 从获取用户的身份和元数据，到 CloudKit 中内置的隐私策略，并最终发现应用程序的其他用户。

## <a name="the-development-and-production-environments"></a>开发和生产环境

CloudKit 为应用程序的记录类型和数据提供单独的开发和生产环境。 开发环境是一个更灵活的环境，仅适用于开发团队的成员。 当应用程序将新字段添加到记录并将该记录保存到开发环境中时，服务器会自动更新架构信息。

开发人员可以使用此功能在开发期间对架构进行更改，从而节省时间。 需要注意的一点是，在字段添加到记录后，不能以编程方式更改与该字段相关联的数据类型。 若要更改字段的类型，开发人员必须在 [CloudKit 仪表板](https://icloud.developer.apple.com/dashboard/) 中删除该字段，并使用新类型再次添加它。

在部署应用程序之前，开发人员可以使用 **CloudKit 仪表板**将其架构和数据迁移到生产环境。 针对生产环境运行时，服务器禁止应用程序以编程方式更改架构。 开发人员仍然可以使用 **CloudKit 仪表板** 进行更改，但尝试将字段添加到生产环境中的记录会导致错误。

> [!NOTE]
> IOS 模拟器仅适用于 **开发环境**。 当开发人员准备好在 **生产环境**中测试应用程序时，需要使用物理 iOS 设备。

## <a name="shipping-a-cloudkit-enabled-app"></a>传送启用了 CloudKit 的应用

在装运使用 CloudKit 的应用程序之前，需要将它配置为面向 **生产 CloudKit 环境** ，否则 Apple 将拒绝该应用程序。

执行以下操作：

1. 在 Visual Studio for Ma 中，编译应用程序以**发布**  >  **iOS 设备**：

    [![编译应用程序以发布](intro-to-cloudkit-images/shipping01.png)](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 在 " **生成** " 菜单中选择 " **存档**"：

    [![选择存档](intro-to-cloudkit-images/shipping02.png)](intro-to-cloudkit-images/shipping02.png#lightbox)

3. 将创建 **存档** 并显示 Visual Studio for Mac：

    [![将创建并显示存档](intro-to-cloudkit-images/shipping03.png)](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 启动 **Xcode**。
5. 从 " **窗口** " 菜单中，选择 " **管理器**"：

    [![选择组织程序](intro-to-cloudkit-images/shipping04.png)](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 选择应用程序的存档，并单击 " **导出 ...** " 按钮：

    [![应用程序的存档](intro-to-cloudkit-images/shipping05.png)](intro-to-cloudkit-images/shipping05.png#lightbox)

7. 选择要导出的方法，然后单击 " **下一步** " 按钮：

    [![选择导出方法](intro-to-cloudkit-images/shipping06.png)](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 从下拉列表中选择 **开发团队** ，并单击 " **选择** " 按钮：

    [![从下拉列表中选择开发团队](intro-to-cloudkit-images/shipping07.png)](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 从下拉列表中选择 " **生产** "，并单击 " **下一步** " 按钮：

    [![从下拉列表中选择 "生产"](intro-to-cloudkit-images/shipping08.png)](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 查看设置并单击 " **导出** " 按钮：

    [![查看设置](intro-to-cloudkit-images/shipping09.png)](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 选择要生成的应用程序文件所在的位置 `.ipa` 。

此过程类似于将应用程序直接提交到 iTunes Connect，只需单击 " **提交 ...** " 按钮而不是 "导出 ..."在 "管理器" 窗口中选择存档后。

## <a name="when-to-use-cloudkit"></a>何时使用 CloudKit

正如我们在本文中所见，CloudKit 为应用程序提供了一种简单的方法来存储和检索 iCloud 服务器中的信息。 也就是说，CloudKit 未过时或弃用任何现有工具或框架。

### <a name="use-cases"></a>用例

以下用例应有助于开发人员决定何时使用特定的 iCloud 框架或技术：

- **ICloud 键值存储** –以异步方式使少量数据保持最新状态，非常适合使用应用程序首选项。 但是，它受限于极少量的信息。
- **ICloud 驱动器** -构建在现有 ICloud 文档 api 之上，并提供一个用于同步文件系统中的非结构化数据的简单 API。 它在 Mac OS X 上提供完全脱机缓存，非常适用于以文档为中心的应用程序。
- **ICloud 核心数据** -允许在用户的所有设备之间复制数据。 数据是单用户的，非常适合用于保持私有的结构化数据同步。
- **CloudKit** –提供结构化和批量的公共数据，并能够处理大型数据集和大型非结构化文件。 它与用户的 iCloud 帐户关联，并提供客户端定向数据传输。

考虑到这些用例，开发人员应选择正确的 iCloud 技术来提供当前所需的应用程序功能，并为将来的增长提供良好的可伸缩性。

## <a name="summary"></a>总结

本文介绍了 CloudKit API 的简介。 它演示了如何预配和配置 Xamarin iOS 应用程序以使用 CloudKit。 它已涵盖 CloudKit 便利性 API 的功能。 它演示了如何使用查询和订阅为可缩放性设计 CloudKit 启用的应用程序。 最后，它显示了通过 CloudKit 向应用程序公开的用户帐户信息。

## <a name="related-links"></a>相关链接

- [CloudKit (Apple) ](https://developer.apple.com/icloud/cloudkit/)
- [CloudKitAtlas (示例) ](/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [创建预配配置文件](~/ios/get-started/installation/device-provisioning/index.md)