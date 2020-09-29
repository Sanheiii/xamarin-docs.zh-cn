---
title: 对 Xamarin 使用 iCloud
description: 本文档介绍 iCloud 及其在 Xamarin iOS 应用程序中的用法。 它讨论了键值存储、文档存储和 iCloud 备份。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/09/2016
ms.openlocfilehash: cace68a1b85a4404774ca88ec697d419920d05cb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432898"
---
# <a name="using-icloud-with-xamarinios"></a>对 Xamarin 使用 iCloud

IOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和特定于应用程序的数据保存到一个中心位置，并从用户的所有设备访问这些项。

可使用四种类型的存储：

- **键-值存储** -与应用程序一起共享用户其他设备上的少量数据。

- **UIDocument 存储** -使用 UIDocument 的子类将文档和其他数据存储在用户的 iCloud 帐户中。

- **CoreData** -SQLite 数据库存储。

- **单独的文件和目录** -用于直接在文件系统中管理大量不同的文件。

本文档介绍前两种类型的键值对和 UIDocument 子类，以及如何在 Xamarin 中使用这些功能。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="requirements"></a>要求

- Xamarin 的最新稳定版本
- Xcode 10
- Visual Studio for Mac 或 Visual Studio 2019。

## <a name="preparing-for-icloud-development"></a>为 iCloud 开发做好准备

必须将应用程序配置为在 [Apple 预配门户](https://developer.apple.com/account/ios/overview.action) 和项目本身中使用 iCloud。 在为 iCloud 开发 (或试用示例之前) 请按照以下步骤进行操作。

若要正确配置应用程序以访问 iCloud：

- **查找 TeamID** -登录到  [Developer.apple.com](https://developer.apple.com) 并访问  **成员中心 > 你的帐户 > 开发人员帐户摘要** ，以获取你的团队 id (或单个开发人员) 的单个 ID。 它将是10个字符串 ( **A93A5CM278** 示例) -这是 "容器标识符" 的一部分。

- **创建新的应用 id** -若要创建应用 id，请按照  [设备预配指南中的存储技术配置部分](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)中列出的步骤进行操作，并确保以允许的服务的形式检查 **iCloud** ：

 [![检查 iCloud 是否为允许的服务](introduction-to-icloud-images/icloud-sml.png)](introduction-to-icloud-images/icloud.png#lightbox)

- **创建新的预配配置文件** -若要创建预配配置文件，请执行  [设备预配指南](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) 中所述的步骤。

- **将容器标识符添加到 info.plist** -容器标识符格式是 `TeamID.BundleID` 。 有关详细信息，请参阅 [使用权利](~/ios/deploy-test/provisioning/entitlements.md) 指南。

- **配置项目属性**-在 Info.plist 文件中，确保**捆绑标识符**与[创建应用 ID](~/ios/deploy-test/provisioning/capabilities/index.md)时设置的**捆绑 id**匹配;IOS 捆绑签名使用一个**预配配置文件，该配置文件**包含包含 ICloud 应用服务的应用 ID，并选择**自定义权利**文件。 这一切都可在 Visual Studio 的 "项目属性" 窗格下完成。

- **在设备上启用 iCloud** -中转到 " **设置" > iCloud** ，并确保设备已登录。
选择并打开 " **文档 & 数据** " 选项。

- **必须使用设备来测试 iCloud** -它在模拟器上不起作用。
事实上，你确实需要两个或多个具有相同 Apple ID 的设备，以查看 iCloud 的操作。

## <a name="key-value-storage"></a>键-值存储

键-值存储适用于用户可能喜欢跨设备保留的少量数据（如他们在书籍或杂志中查看的最后一页）。 键-值存储不应用于备份数据。

使用键/值存储时，有一些限制需要注意：

- **最大密钥大小** -密钥名称不能超过64个字节。

- **最大值大小** -无法在单个值中存储超过 64 kb。

- **应用的最大键-值存储大小** -应用程序最多只能存储 64 kb 的键值数据。 尝试将密钥设置为超出该限制将失败，并且以前的值将保持不变。

- **数据类型** -只能存储基本类型，如字符串、数字和布尔值。

**ICloudKeyValue**示例演示了它的工作原理。 示例代码将为每个设备创建一个名为的密钥：你可以在一台设备上设置此密钥并观看该值传播到其他设备。 它还会创建一个名为 "Shared" 的密钥，该密钥可在任何设备上编辑-如果你同时在多个设备上编辑，则 iCloud 将决定哪些值 "wins" (对更改) 使用时间戳并传播。

此屏幕截图显示了使用中的示例。 从 iCloud 收到更改通知时，它们将打印在屏幕底部的滚动文本视图中，并在输入字段中更新。

 [![设备之间的消息流](introduction-to-icloud-images/icloud-kv-arrows.png)](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>设置和检索数据

此代码演示如何设置字符串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

调用同步可确保该值仅保存到本地磁盘存储。 到 iCloud 的同步在后台发生，并且应用程序代码不能 "强制"。 使用良好的网络连接，同步通常会在5秒内发生，但如果网络 (或断开连接) 则更新可能需要更长时间。

可以使用以下代码检索值：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

将从本地数据存储中检索该值，此方法不会尝试联系 iCloud 服务器来获取 "最新" 值。 iCloud 将根据其自己的计划更新本地数据存储。

### <a name="deleting-data"></a>删除数据

若要完全删除键/值对，请使用如下所示的 Remove 方法：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>观察更改

如果通过将观察程序添加到来更改值，应用程序也可以接收通知 `NSNotificationCenter.DefaultCenter` 。
**KeyValueViewController.cs**方法中的以下代码 `ViewWillAppear` 显示了如何侦听这些通知并创建了已更改的密钥列表：

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

然后，你的代码可以对更改的键的列表执行一些操作，例如更新它们的本地副本或使用新值更新 UI。

可能的更改原因包括： ServerChange (0) 、InitialSyncChange (1) 或 QuotaViolationChange (2) 。 如有必要，你可以访问原因并执行不同的处理 (例如，你可能需要删除某些键作为 *QuotaViolationChange*) 的结果。

## <a name="document-storage"></a>文档存储

iCloud 文档存储用于管理对应用程序 (和用户) 非常重要的数据。 它可用于管理应用程序需要运行的文件和其他数据，同时还在所有用户的设备上提供基于 iCloud 的备份和共享功能。

此图显示了它是如何结合在一起的。 每个设备都将数据保存在本地存储 (UbiquityContainer) ，操作系统的 iCloud 守护程序负责在云中发送和接收数据。 对 UbiquityContainer 的所有文件访问都必须通过 FilePresenter/FileCoordinator 完成，以防止并发访问。 `UIDocument`类为你实现这些; 此示例演示如何使用 UIDocument。

 [![文档存储概述](introduction-to-icloud-images/icloud-overview.png)](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 示例实现了一个简单的 `UIDocument` 子类，其中包含一个文本字段。 文本在中呈现 `UITextView` ，并且编辑内容将被 iCloud 传播到其他设备，并且通知消息显示为红色。 示例代码不处理更高级的 iCloud 功能，如冲突解决。

此屏幕截图显示示例应用程序-在更改文本后按 **UpdateChangeCount** 将文档通过 iCloud 同步到其他设备。

 [![此屏幕截图显示了更改文本和按 UpdateChangeCount 后的示例应用程序](introduction-to-icloud-images/iclouduidoc.png)](introduction-to-icloud-images/iclouduidoc.png#lightbox)

ICloudUIDoc 示例有五个部分：

1. **访问 UbiquityContainer** -确定是否已启用 iCloud，如果是，则为应用程序的 iCloud 存储区的路径。

1. **创建 UIDocument 子类** -创建类以在 iCloud 存储和模型对象之间中间。

1. **查找和打开 icloud 文档** -使用 `NSFileManager` 和 `NSPredicate` 查找 icloud 文档并将其打开。

1. **显示 iCloud 文档** -公开中的属性， `UIDocument` 以便您可以与 UI 控件交互。

1. **保存 iCloud 文档** -确保将 UI 中的更改保存到磁盘和 iCloud。

所有 iCloud 操作都 (运行，或者应异步运行) ，以使它们在等待发生某些事情时不会被阻止。 在示例中，您将看到三个不同的方法来实现此操作：

 在的初始调用中，**线程**在 `AppDelegate.FinishedLaunching` `GetUrlForUbiquityContainer` 另一个线程上完成，以防止主线程阻塞。

 **NotificationCenter** -在完成异步操作时注册通知 `NSMetadataQuery.StartQuery` 。

 **完成处理程序** -传入在完成异步操作时要运行的方法 `UIDocument.Open` 。

### <a name="accessing-the-ubiquitycontainer"></a>访问 UbiquityContainer

使用 iCloud 文档存储的第一步是确定是否启用 iCloud，如果为，则 "无处不容器" (在设备上存储已启用 iCloud 的文件的目录) 。

此代码位于示例的 `AppDelegate.FinishedLaunching` 方法中。

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

尽管此示例不执行此操作，但 Apple 建议在应用程序到达前台时调用 GetUrlForUbiquityContainer。

### <a name="creating-a-uidocument-subclass"></a>创建 UIDocument 子类

所有 iCloud 文件和目录 (ie。存储在 UbiquityContainer 目录中的任何内容都) 必须使用 NSFileManager 方法进行管理，实现 NSFilePresenter 协议，并通过 NSFileCoordinator 进行写入。
完成所有这些操作的最简单方法是不自行编写，而是 UIDocument 的子类。

只有两种方法必须在 UIDocument 子类中实现才能使用 iCloud：

- **LoadFromContents** -在文件的内容的外中传递，以便解压缩到模型类。

- **ContentsForType** -请求你提供模型类的外表示形式，以便 (和云) 保存到磁盘。

**ICloudUIDoc\MonkeyDocument.cs**中的此示例代码演示了如何实现 UIDocument。

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

在这种情况下，数据模型非常简单-一个文本字段。 你的数据模型可能会像所需一样复杂，如 Xml 文档或二进制数据。 UIDocument 实现的主要角色是在模型类和可以保存/加载到磁盘上的外表示形式之间进行转换。

### <a name="finding-and-opening-icloud-documents"></a>查找和打开 iCloud 文档

该示例应用仅处理单个文件 test.txt，因此 **AppDelegate.cs** 中的代码将创建一个 `NSPredicate` 并 `NSMetadataQuery` 专门查找该文件名。 `NSMetadataQuery`异步运行并在其完成后发送通知。 `DidFinishGathering` 由通知观察程序调用，停止查询并调用 LoadDocument，后者将 `UIDocument.Open` 方法与完成处理程序一起使用，以尝试加载文件并将其显示在中 `MonkeyDocumentViewController` 。

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>显示 iCloud 文档

显示 UIDocument 不应与任何其他模型类都有任何不同-属性显示在 UI 控件中，用户可能会编辑这些属性，然后将其写回到模型中。

在示例中， **iCloudUIDoc\MonkeyDocumentViewController.cs** 在中显示 MonkeyDocument 文本 `UITextView` 。 `ViewDidLoad` 侦听方法中发送的通知 `MonkeyDocument.LoadFromContents` 。 `LoadFromContents` 当 iCloud 包含文件的新数据时，将调用，以便通知指示文档已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

示例代码通知处理程序将调用一个方法来更新 UI，在这种情况下，不会发生任何冲突检测或解决方法。

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>正在保存 iCloud 文档

若要将 UIDocument 添加到 iCloud，你可以 `UIDocument.Save` 直接为新文档调用 () 或使用移动现有文件 `NSFileManager.DefaultManager.SetUbiquitious` 。 示例代码使用此代码直接在无处不容器中创建一个新文档 (有两个完成处理程序，一个用于操作， `Save` 另一个用于打开的) ：

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

对文档的后续更改不会直接 "保存"，而是告知 `UIDocument` 它已更改 `UpdateChangeCount` ，并将自动计划 "保存到磁盘" 操作：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 文档

用户可以通过 "设置" 在应用程序外部的 "无处不容器 **" 目录中** 管理 iCloud 文档;用户可以查看文件列表，然后轻扫即可删除。 应用程序代码应能够处理用户删除文档的情况。 不要在 **Documents** 目录中存储内部应用程序数据。

 [![管理 iCloud 文档工作流](introduction-to-icloud-images/icloudstorage.png)](introduction-to-icloud-images/icloudstorage.png#lightbox)

当用户尝试从其设备中删除启用了 iCloud 的应用程序时，也会收到不同的警告，通知他们与该应用程序相关的 iCloud 文档的状态。

 [![用户尝试从设备中删除已启用 iCloud 的应用程序时的示例对话框](introduction-to-icloud-images/icloud-delete1.png)](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![用户尝试从设备中删除已启用 iCloud 的应用程序时的示例对话框](introduction-to-icloud-images/icloud-delete2.png)](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 备份

尽管备份到 iCloud 并不是开发人员直接访问的功能，但设计应用程序的方式会影响用户体验。
Apple 提供 [Ios 数据存储准则](https://developer.apple.com/icloud/documentation/data-storage/) ，使开发人员可以遵循其 ios 应用程序。

最重要的注意事项是您的应用程序是否存储不是用户生成的大型文件 (例如，一种杂志阅读器应用程序，该应用程序在每个问题) 存储几百到 mb 的内容。 Apple 首选不要存储此类数据，在这些数据中将备份到 iCloud 并且不必要地填写用户的 iCloud 配额。

存储大量数据的应用程序应将数据存储在未备份的某个用户目录中 (例如， 缓存或 tmp) 或使用将 `NSFileManager.SetSkipBackupAttribute` 标志应用于这些文件，以便 iCloud 在备份操作期间忽略这些文件。

## <a name="summary"></a>总结

本文介绍了 iOS 5 中包含的新 iCloud 功能。 本指南检查了将项目配置为使用 iCloud 所需的步骤，并提供了如何实现 iCloud 功能的示例。

键-值存储示例演示了如何使用 iCloud 来存储少量的数据，这与存储 NSUserPreferences 的方式类似。 UIDocument 示例演示了如何通过 iCloud 存储多个设备并跨多个设备进行同步。

最后，它还简要讨论了如何添加 iCloud 备份来影响应用程序的设计。

## <a name="related-links"></a>相关链接

- [ICloud (示例简介) ](/samples/xamarin/ios-samples/introductiontoicloud)
- [iCloud 讨论会示例代码](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研讨会幻灯片](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 存储](https://support.apple.com/kb/HT4847)