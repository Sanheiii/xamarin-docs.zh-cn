---
title: Xamarin 中的文档选取器
description: 本文档介绍 iOS 文档选取器并讨论如何在 Xamarin 中使用它。 它会查看 iCloud、文档、通用安装代码、文档提供程序扩展等。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: c8cffc9593b6de059a877654994e6747204ce34d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435393"
---
# <a name="document-picker-in-xamarinios"></a>Xamarin 中的文档选取器

文档选取器允许在应用之间共享文档。 这些文档可以存储在 iCloud 或其他应用的目录中。 文档通过用户在其设备上安装的一组 [文档提供程序扩展](~/ios/platform/extensions.md) 来共享。

由于跨应用和云保持文档同步非常困难，它们会引入一定程度的必要的复杂性。

## <a name="requirements"></a>要求

若要完成本文中所述的步骤，需要满足以下要求：

- 需要在开发人员的计算机上安装和配置**Xcode 7 和 ios 8 或更高版本**的 Xcode 7 和 ios 8 或更高版本的 api。
- **Visual Studio 或 Visual Studio for Mac** –应安装最新版本的 Visual Studio for Mac。
- **Ios 设备** -运行 ios 8 或更高版本的 ios 设备。

## <a name="changes-to-icloud"></a>更改为 iCloud

若要实现文档选取器的新功能，已对 Apple 的 iCloud 服务进行了以下更改：

- 已使用 CloudKit 完全重新编写 iCloud 后台程序。
- 现有 iCloud 功能已重命名为 iCloud 驱动器。
- 已将对 Microsoft Windows 操作系统的支持添加到 iCloud。
- Mac OS 查找程序中添加了一个 iCloud 文件夹。
- iOS 设备可以访问 Mac OS iCloud 文件夹的内容。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="what-is-a-document"></a>什么是文档？

当引用 iCloud 中的文档时，该文档是单个独立的实体，应视为用户。 用户可能想要修改文档，或将其与其他用户共享，使用电子邮件 (，例如) 。

用户可以将多种类型的文件立即识别为文档，如页面、主题或数字文件。 但 iCloud 并不局限于这一概念。 例如，游戏的状态 (如国际象棋) 可以视为文档并存储为 iCloud。 此文件可以在用户的设备之间传递，并允许他们在不同设备上从其开始的位置选择游戏。

## <a name="dealing-with-documents"></a>处理文档

在深入探讨在 Xamarin 中使用文档选取器所需的代码之前，本文将介绍使用 iCloud 文档的最佳实践，以及对支持文档选取器所需的现有 Api 所做的一些修改。

### <a name="using-file-coordination"></a>使用文件协调

因为文件可以从多个不同的位置修改，所以必须使用协调来防止数据丢失。

 [![使用文件协调](document-picker-images/image1.png)](document-picker-images/image1.png#lightbox)

让我们看看上图：

1. 使用文件协调的 iOS 设备会创建一个新文档，并将其保存到 iCloud 文件夹中。
2. iCloud 将修改后的文件保存到云中，以便分发给每个设备。
3. 附加的 Mac 查看 iCloud 文件夹中修改的文件，并使用文件协调将更改复制到该文件中。
4. 不使用文件协调的设备会对文件进行更改并将其保存到 iCloud 文件夹中。 这些更改会立即复制到其他设备。

假设原始 iOS 设备或 Mac 正在编辑该文件，现在，其更改会丢失，并会被不协调设备中的文件版本覆盖。 若要防止数据丢失，文件协调在使用基于云的文档时是必需的。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument` 通过执行开发人员的所有繁重操作，使操作简单 (或 `NSDocument` macOS) 。 它提供与后台队列的内置文件协调，防止阻止应用程序的 UI。

 `UIDocument` 公开多个高级别 Api，这些 Api 可简化 Xamarin 应用程序的开发工作，以实现开发人员所需的任何目的。

下面的代码将创建一个子类 `UIDocument` ，以实现可用于存储和检索 iCloud 中的文本的基于文本的通用文档：

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

`GenericTextDocument`当在 Xamarin 8 应用程序中使用文档选取器和外部文档时，将在本文中使用上面提供的类。

## <a name="asynchronous-file-coordination"></a>异步文件协调

iOS 8 通过新的文件协调 Api 提供了多种新的异步文件协调功能。 在 iOS 8 之前，所有现有文件协调 Api 完全同步。 这意味着开发人员负责实现自己的后台队列，以防止文件协调阻止应用程序的 UI。

新 `NSFileAccessIntent` 类包含指向该文件的 URL，以及用于控制所需协调类型的多个选项。 下面的代码演示如何使用意向将文件从一个位置移到另一个位置：

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>发现和列出文档

发现和列出文档的方式是使用现有的 `NSMetadataQuery` api。 本部分将介绍添加到 `NSMetadataQuery` 中的新功能，使文档的处理更加简单。

### <a name="existing-behavior"></a>现有行为

在 iOS 8 之前，对 `NSMetadataQuery` 拾取本地文件更改（如删除、创建和重命名）的速度很慢。

 [![NSMetadataQuery 本地文件更改概述](document-picker-images/image2.png)](document-picker-images/image2.png#lightbox)

在上图中：

1. 对于应用程序容器中已存在的文件，  `NSMetadataQuery` 具有  `NSMetadata` 预先创建和假脱机的现有记录，以便这些记录立即可用于应用程序。
1. 应用程序会在应用程序容器中创建一个新文件。
1. 在  `NSMetadataQuery` 看到对应用程序容器的修改并创建所需记录之前，会有一段延迟  `NSMetadata` 。

由于创建记录的延迟 `NSMetadata` ，应用程序必须打开两个数据源：一个用于本地文件更改，另一个用于基于云的更改。

### <a name="stitching"></a>装订

在 iOS 8 中，可以 `NSMetadataQuery` 更方便地直接使用称为 "拼接" 的新功能：

 [![带有称为 "缝合" 的新功能的 NSMetadataQuery](document-picker-images/image3.png)](document-picker-images/image3.png#lightbox)

在上面的关系图中使用缝合：

1. 如前所述，对于已存在于应用程序容器中的文件，  `NSMetadataQuery` 具有  `NSMetadata` 预先创建和假脱机的现有记录。
1. 应用程序使用文件协调在应用程序容器中创建一个新文件。
1. 应用程序容器中的挂钩可查看修改和调用  `NSMetadataQuery` 以创建所需的  `NSMetadata` 记录。
1. `NSMetadata`记录是在文件后直接创建的，并可用于应用程序。

通过使用 "应用程序"，将不再需要打开数据源来监视本地和基于云的文件更改。 现在，应用程序可以 `NSMetadataQuery` 直接依赖。

> [!IMPORTANT]
> 仅当应用程序正在使用上一节中所述的文件协调时，才可以使用。 如果未使用文件协调，则 Api 默认为现有的 iOS 8 之前的行为。

### <a name="new-ios-8-metadata-features"></a>新的 iOS 8 元数据功能

IOS 8 中添加了以下新功能 `NSMetadataQuery` ：

- `NSMetatadataQuery` 现在可以列出存储在云中的非本地文档。
- 添加了新的 Api 来访问基于云的文档的元数据信息。
- 有一个新的  `NSUrl_PromisedItems` API，它将访问文件的文件属性，这些文件可能或可能不具有本地可用内容。
- 使用  `GetPromisedItemResourceValue` 方法获取有关给定文件的信息，或使用  `GetPromisedItemResourceValues` 方法一次获取多个文件的相关信息。

添加了两个新的文件协调标志来处理元数据：

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly`
- `NSFileCoordinatorWriteContentIndependentMetadataOnly`

使用上述标志，文档文件的内容不需要本地可用即可供使用。

下面的代码段演示了如何使用 `NSMetadataQuery` 查询是否存在特定的文件并在文件不存在时生成该文件：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

#region Static Properties
public const string TestFilename = "test.txt";
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),             NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>文档缩略图

Apple 认为，为应用程序列出文档时最好的用户体验是使用预览。 这将为最终用户提供上下文，因此他们可以快速识别要使用的文档。

在 iOS 8 之前，显示文档预览需要自定义实现。 IOS 8 的新增特性是文件系统属性，使开发人员能够快速处理文档缩略图。

#### <a name="retrieving-document-thumbnails"></a>检索文档缩略图

通过调用 `GetPromisedItemResourceValue` 或 `GetPromisedItemResourceValues` 方法， `NSUrl_PromisedItems` `NSUrlThumbnailDictionary` 将返回 API。 此字典中的唯一键是 `NSThumbnial1024X1024SizeKey` 和其匹配项 `UIImage` 。

#### <a name="saving-document-thumbnails"></a>保存文档缩略图

保存缩略图的最简单方法是使用 `UIDocument` 。 通过调用 `GetFileAttributesToWrite` 的方法 `UIDocument` 并设置缩略图，在文档文件为时，它将自动保存。 ICloud 守护程序会看到此更改并将其传播到 iCloud。 在 Mac OS X 上，快速查找插件会自动为开发人员生成缩略图。

利用基础知识，以及对现有 API 的修改，我们已准备好在 Xamarin iOS 8 移动应用程序中实现文档选取器视图控制器。

## <a name="enabling-icloud-in-xamarin"></a>在 Xamarin 中启用 iCloud

需要在应用程序中和 Apple 中启用 iCloud 支持，然后才能在 Xamarin iOS 应用程序中使用文档选取器。

以下步骤演练为 iCloud 进行设置的过程。

1. 创建 iCloud 容器。
2. 创建包含 iCloud 应用服务的应用 ID。
3. 创建包含此应用 ID 的预配配置文件。

使用 [功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) 指南演练前两个步骤。 若要创建预配配置文件，请按照 [预配配置文件](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) 指南中的步骤进行操作。

以下步骤演练为 iCloud 配置应用程序的过程：

执行以下操作：

1. 在 Visual Studio for Mac 或 Visual Studio 中打开项目。
2. 在 **解决方案资源管理器**中，右键单击项目，然后选择 "选项"。
3. 在 "选项" 对话框中，选择 " **IOS 应用程序**"，确保 **捆绑标识符** 与上面为应用程序创建的 **应用程序 ID** 中定义的标识符相匹配。
4. 选择 " **IOS 捆绑签名**"，选择上面创建的 **开发人员标识** 和 **预配配置文件** 。
5. 单击 **"确定"** 按钮保存更改并关闭对话框。
6. 右键单击 `Entitlements.plist` " **解决方案资源管理器** " 以在编辑器中打开它。

    > [!IMPORTANT]
    > 在 Visual Studio 中，你可能需要通过右键单击来打开 "权限编辑器"，然后选择 "**打开方式 ...** " 和选择属性列表编辑器

7. 选中 "  **启用 icloud** 、  **icloud 文档** 、  **键值存储** 和  **CloudKit** "。
8. 请确保应用程序 () 上创建的 **容器** 存在。 示例： `iCloud.com.your-company.AppName`
9. 保存对文件所做的更改。

有关权利的详细信息，请参阅 [使用权利](~/ios/deploy-test/provisioning/entitlements.md) 指南。

设置好上述设置后，应用程序现在可以使用基于云的文档和新的文档选取器视图控制器。

## <a name="common-setup-code"></a>常见安装代码

开始处理文档选取器视图控制器之前，需要提供一些标准的安装代码。 首先修改应用程序的 `AppDelegate.cs` 文件，使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt";
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                 new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }

        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");    

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {    
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();

        }

        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }

        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }

        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }

        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> 上面的代码包含上面的 "发现和列出文档" 部分中的代码。 它将在整个中提供，因为它会出现在实际的应用程序中。 为简单起见，此示例仅使用单个硬编码文件 (`test.txt`) 。

上述代码公开了若干 iCloud 驱动器快捷方式，使其更易于在应用程序的其余部分中使用。

接下来，将以下代码添加到将使用文档选取器或使用基于云的文档的任何视图或视图容器：

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

这将添加访问的快捷方式 `AppDelegate` ，并访问上面创建的 iCloud 快捷方式。

使用此代码后，让我们看看如何在 Xamarin iOS 8 应用程序中实现文档选取器视图控制器。

## <a name="using-the-document-picker-view-controller"></a>使用文档选取器视图控制器

在 iOS 8 之前，从另一个应用程序访问文档是非常困难的，因为无法从应用内发现应用程序之外的文档。

### <a name="existing-behavior"></a>现有行为

 [![现有行为概述](document-picker-images/image31.png)](document-picker-images/image31.png#lightbox)

让我们看一下如何访问 iOS 8 之前的外部文档：

1. 首先，用户必须打开最初创建文档的应用程序。
1. 将选择文档，并  `UIDocumentInteractionController` 使用将文档发送到新应用程序。
1. 最后，将原始文档的副本放置在新应用程序的容器中。

在该文档中，第二个应用程序可以打开和编辑。

### <a name="discovering-documents-outside-of-an-apps-container"></a>在应用容器外发现文档

在 iOS 8 中，应用程序能够轻松地在其自己的应用程序容器外访问文档：

 [![在应用容器外发现文档](document-picker-images/image32.png)](document-picker-images/image32.png#lightbox)

使用新的 iCloud 文档选取器 ( `UIDocumentPickerViewController`) ，iOS 应用程序可以直接在其应用程序容器外发现和访问。 `UIDocumentPickerViewController`提供了一种机制，使用户能够通过权限授予和编辑所发现的文档。

应用程序必须选择将其文档显示在 iCloud 文档选取器中，并可供其他应用程序发现和使用它们。 若要让 Xamarin iOS 8 应用程序共享其应用程序容器，请 `Info.plist` 在标准文本编辑器中编辑该文件，然后将以下两行添加到字典的底部 (在 `<dict>...</dict>` 标记) 之间：

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供了一个强大的新 UI，允许用户选择文档。 若要在 Xamarin iOS 8 应用程序中显示文档选取器视图控制器，请执行以下操作：

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> 开发人员必须先调用 `StartAccessingSecurityScopedResource` 的方法， `NSUrl` 然后才能访问外部文档。 当 `StopAccessingSecurityScopedResource` 文档加载后，必须调用方法以释放安全锁。

### <a name="sample-output"></a>示例输出

下面的示例演示了上述代码在 iPhone 设备上运行时如何显示文档选取器：

1. 用户启动应用程序并显示主界面：   

    [![显示主界面](document-picker-images/image33.png)](document-picker-images/image33.png#lightbox)
1. 用户点击屏幕顶部的 " **操作** " 按钮，并要求从可用提供程序的列表中选择 **文档提供程序** ：   

    [![从可用提供程序的列表中选择文档提供程序](document-picker-images/image34.png)](document-picker-images/image34.png#lightbox)
1. 显示选定**文档提供程序**的**文档选取器视图控制器**：   

    [![显示文档选取器视图控制器](document-picker-images/image35.png)](document-picker-images/image35.png#lightbox)
1. 用户点击 **文档文件夹** 以显示其内容：   

    [![文档文件夹内容](document-picker-images/image36.png)](document-picker-images/image36.png#lightbox)
1. 用户选择 **文档** 并且 **文档选取器** 已关闭。
1. 主界面重新显示， **文档** 从外部容器加载，并显示其内容。

文档选取器视图控制器的实际显示取决于用户已安装在设备上的文档提供程序以及已实现的文档选取器模式。 上面的示例使用的是打开模式，将在下面详细介绍其他模式类型。

## <a name="managing-external-documents"></a>管理外部文档

如上所述，在 iOS 8 之前，应用程序只能访问应用程序容器中的文档。 在 iOS 8 中，应用程序可以从外部源访问文档：

 [![管理外部文档概述](document-picker-images/image37.png)](document-picker-images/image37.png#lightbox)

当用户选择外部源中的文档时，会将引用文档写入指向原始文档的应用程序容器。

为了帮助将这一新功能添加到现有的应用程序中，API 中添加了几个新功能 `NSMetadataQuery` 。 通常，应用程序使用广泛的文档范围来列出其应用程序容器中的文档。 使用此作用域时，将只显示应用程序容器中的文档。

使用新的通用外部文档范围将返回应用程序容器外部的文档，并为其返回元数据。 `NSMetadataItemUrlKey`将指向文档实际所在的 URL。

有时，应用程序不希望使用按引用指向的文档。 相反，应用程序需要直接使用引用文档。 例如，应用可能需要在 UI 的应用程序文件夹中显示文档，或者允许用户在文件夹中移动引用。

在 iOS 8 中，提供了一个新的， `NSMetadataItemUrlInLocalContainerKey` 用于直接访问引用文档。 此键指向对应用程序容器中的外部文档的实际引用。

`NSMetadataUbiquitousItemIsExternalDocumentKey`用于测试文档是否位于应用程序的容器的外部。 `NSMetadataUbiquitousItemContainerDisplayNameKey`用于访问承载外部文档的原始副本的容器的名称。

### <a name="why-document-references-are-required"></a>为什么需要文档引用

IOS 8 使用引用访问外部文档的主要原因是安全的。 没有为应用程序提供对其他任何应用程序容器的访问权限。 只有文档选取器可以这样做，因为在进程外运行并且具有系统范围的访问权限。

若要访问应用程序容器外的文档，唯一的方法是使用文档选取器，如果该选取器返回的 URL 具有安全作用域，则为。 安全范围 URL 包含的信息足以用于选择该文档，以及向应用程序授予对该文档的访问权限所需的作用域权限。

需要注意的是，如果安全范围的 URL 已序列化为字符串，然后反序列化，则安全信息将丢失，并且无法从 URL 中访问该文件。 "文档引用" 功能提供了一种机制，可返回到这些 Url 指向的文件。

因此，如果应用程序 `NSUrl` 从一个引用文档中获取，则该应用程序已附加了安全作用域，并可用于访问该文件。 出于此原因，强烈建议开发人员使用， `UIDocument` 因为它会处理所有这些信息和过程。

### <a name="using-bookmarks"></a>使用书签

枚举应用程序的文档以返回到特定文档（例如，在执行状态还原时）并不总是可行。 iOS 8 提供一种机制，用于创建直接以给定文档为目标的书签。

以下代码将从 `UIDocument` 的属性创建书签 `FileUrl` ：

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

现有书签 API 用于创建针对现有 `NSUrl` 的书签，可以保存和加载此书签以提供对外部文件的直接访问。 以下代码将还原上面创建的书签：

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>打开与导入模式和文档选取器

文档选取器视图控制器具有两种不同的操作模式：

1. **打开模式** –在此模式下，当用户选择和外部文档时，文档选取器将在应用程序容器中创建一个安全范围书签。   

    [![应用程序容器中的安全作用域书签](document-picker-images/image37.png)](document-picker-images/image37.png#lightbox)
1. **导入模式** –在此模式下，当用户选择和外部文档时，文档选取器不会创建书签，而是将文件复制到临时位置，并为应用程序提供对位于此位置的文档的访问权限：   

    [![文档选取器会将文件复制到一个临时位置，并为应用程序提供对此位置的文档的访问权限](document-picker-images/image38.png)](document-picker-images/image38.png#lightbox)   
 一旦应用程序因某种原因终止，临时位置就会被清空，并删除该文件。 如果应用程序需要维护对文件的访问，则应创建一个副本并将其放入其应用程序容器中。

当应用程序希望与其他应用程序协作并与该应用程序共享对文档所做的任何更改时，打开模式非常有用。 当应用程序不希望与其他应用程序共享对文档所做的修改时，可以使用导入模式。

## <a name="making-a-document-external"></a>使文档成为外部文档

如上所述，iOS 8 应用程序无法访问其自己的应用程序容器之外的容器。 应用程序可以在本地或临时位置写入其自己的容器，然后使用特殊文档模式将应用程序容器外的结果文档移动到用户选择的位置。

若要将文档移动到外部位置，请执行以下操作：

1. 首先在本地或临时位置创建新文档。
1. 创建一个  `NSUrl` 指向新文档的。
1. 打开新的文档选取器视图控制器，并将其传递到的  `NSUrl` 模式 `MoveToService` 。
1. 用户选择新位置后，该文档将从其当前位置移动到新位置。
1. 引用文档将写入应用程序的应用程序容器，以便创建应用程序仍可以访问该文件。

下面的代码可用于将文档移动到外部位置： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

上述过程返回的引用文档与文档选取器的打开模式创建的文档完全相同。 但是，有时应用程序可能希望在不保留对文档的引用的情况下移动文档。

若要移动文档而不生成引用，请使用 `ExportToService` 模式。 示例： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

使用模式时 `ExportToService` ，会将文档复制到外部容器，现有副本将保留在其原始位置。

## <a name="document-provider-extensions"></a>文档提供程序扩展

使用 iOS 8，Apple 希望最终用户能够访问其任何基于云的文档，而不管它们实际存在于何处。 为了实现此目标，iOS 8 提供了一个新的文档提供程序扩展机制。

### <a name="what-is-a-document-provider-extension"></a>什么是文档提供程序扩展？

简单地说，文档提供商扩展是一种面向开发人员或第三方提供的应用程序备用文档存储的一种方法，可使用与现有 iCloud 存储位置完全相同的方式进行访问。

用户可以从文档选取器中选择这些备用存储位置之一，并且它们可以使用完全相同的访问模式 (打开、导入、移动或导出) 来处理该位置中的文件。

这是使用两个不同的扩展实现的：

- **文档选取器扩展** –提供一个  `UIViewController` 子类，该子类提供一个图形界面，使用户能够从备用存储位置中选择文档。 此子类将作为文档选取器视图控制器的一部分显示。
- **文件提供扩展** -这是一个处理实际提供文件内容的非 UI 扩展。 这些扩展通过文件协调 ( ) 提供 `NSFileCoordinator` 。 这是需要文件协调的另一个重要情况。

下图显示了使用文档提供程序扩展时的典型数据流：

 [![此图显示了使用文档提供程序扩展时的典型数据流](document-picker-images/image39.png)](document-picker-images/image39.png#lightbox)

将发生以下过程：

1. 应用程序将显示一个文档选取器控制器，以允许用户选择要使用的文件。
1. 用户选择另一个文件位置，并调用自定义  `UIViewController` 扩展插件来显示用户界面。
1. 用户从该位置选择文件，并将该 URL 传递回文档选取器。
1. 文档选取器选择文件的 URL 并将其返回给应用程序以供用户使用。
1. URL 将传递给文件协调器，以将文件内容返回到应用程序。
1. 文件协调器调用自定义文件提供程序扩展以检索文件。
1. 文件的内容将返回到文件协调器。
1. 文件的内容将返回到应用程序。

### <a name="security-and-bookmarks"></a>安全性和书签

本部分将简要介绍通过书签如何使用文档提供程序扩展来进行安全和持久的文件访问。 与 iCloud 文档提供程序不同，该提供程序会自动将安全性和书签保存到应用程序容器中，因为它们不是文档引用系统的一部分。

例如：在提供自己公司范围的安全数据存储的企业设置中，管理员不希望公共 iCloud 服务器访问或处理机密公司信息。 因此，不能使用内置文档引用系统。

书签系统仍可使用，并由文件提供程序扩展负责正确处理书签 URL 并返回由其指向的文档内容。

出于安全方面的考虑，iOS 8 具有隔离层，可将有关哪些应用程序具有访问权限的信息保留在哪个应用程序内。 应注意的是，所有文件访问都受此隔离层控制。

下图显示了使用书签和文档提供程序扩展时的数据流：

 [![此图显示了在使用书签和文档提供程序扩展时的数据流](document-picker-images/image40.png)](document-picker-images/image40.png#lightbox)

将发生以下过程：

1. 应用程序将进入背景，并需要保持其状态。 它将调用 `NSUrl` 以在备用存储中为文件创建书签。
1. `NSUrl` 调用文件提供程序扩展以获取文档的持久性 URL。
1. 文件提供程序扩展将 URL 以字符串的形式返回到 `NSUrl` 。
1. 将 `NSUrl` URL 捆绑到一个书签，并将其返回给应用程序。
1. 当应用程序 awakes 在后台并且需要还原状态时，它会将书签传递给 `NSUrl` 。
1. `NSUrl` 调用文件提供程序扩展和文件的 URL。
1. 文件扩展提供程序将访问该文件，并将该文件的位置返回到 `NSUrl` 。
1. 文件位置与安全信息捆绑在一起并返回到应用程序。

在此，应用程序可以访问该文件并像平常一样使用它。

### <a name="writing-files"></a>写入文件

本部分将简要介绍如何使用文档提供程序扩展将文件写入到备用位置。 IOS 应用程序将使用文件协调将信息保存到应用程序容器中的磁盘。 文件写入完毕后不久，文件提供程序扩展就会收到更改通知。

此时，文件提供程序扩展可以开始将文件上传到备用位置 (或者将文件标记为已更新，并要求上传) 。

### <a name="creating-new-document-provider-extensions"></a>创建新的文档提供程序扩展

创建新的文档提供程序扩展超出了本文的介绍范围。 此处提供的信息可根据用户已加载到其 iOS 设备中的扩展显示，应用程序可能有权访问 Apple 提供的 iCloud 位置之外的文档存储位置。

开发人员在使用文档选取器并使用外部文档时应注意到这种情况。 它们不应假定这些文档托管于 iCloud。

有关创建存储提供程序或文档选取器扩展的详细信息，请参阅 [应用扩展简介](~/ios/platform/extensions.md) 文档。

## <a name="migrating-to-icloud-drive"></a>迁移到 iCloud 驱动器

在 iOS 8 上，用户可以选择继续使用 iOS 7 (和先前系统) 中使用的现有 iCloud 文档系统，也可以选择将现有文档迁移到新的 iCloud 驱动器机制。

在 Mac OS X Yosemite 上，Apple 不提供向后兼容性，因此必须将所有文档迁移到 iCloud 驱动器，否则它们将不再跨设备更新。

用户的帐户迁移到 iCloud 驱动器后，只有使用 iCloud 驱动器的设备才能将更改传播到这些设备上的文档。

> [!IMPORTANT]
> 开发人员应注意，本文中所述的新功能仅在用户帐户已迁移到 iCloud 驱动器时才可用。

## <a name="summary"></a>总结

本文介绍了支持 iCloud 驱动器和新的文档选取器视图控制器所需的现有 iCloud Api 更改。 它涵盖了文件协调以及在使用基于云的文档时它非常重要的原因。 它介绍了在 Xamarin iOS 应用程序中启用基于云的文档所需的设置，并提供了介绍如何使用文档选取器视图控制器在应用的应用程序容器外使用文档的介绍。

此外，本文简要介绍了文档提供程序扩展，以及开发人员在编写可处理基于云的文档的应用程序时应注意到这些扩展的原因。

## <a name="related-links"></a>相关链接

- [DocPicker (示例) ](/samples/xamarin/ios-samples/ios8-docpicker)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [应用扩展简介](~/ios/platform/extensions.md)