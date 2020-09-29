---
title: Xamarin 中的文件系统访问
description: 本文档介绍如何在 Xamarin 中使用文件系统。 它介绍目录、读取文件、XML 和 JSON 序列化、应用程序沙盒、通过 iTunes 共享文件等。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: ef632b4bff3313de82c71cb5839ecdc24c9242ad
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431486"
---
# <a name="file-system-access-in-xamarinios"></a>Xamarin 中的文件系统访问

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

您可以使用 Xamarin 和 `System.IO` .Net 基类库中的类 * (BCL) * 来访问 iOS 文件系统。 `File` 类可以创建、删除和读取文件，`Directory` 类可以创建、删除或枚举目录的内容。 你还可以使用 `Stream` 子类，这可以提供对文件操作的更大程度的控制， (如文件中的压缩或位置搜索) 。

iOS 对应用程序使用文件系统执行的操作施加了一些限制，以保留应用程序数据的安全性，并保护 malignant 应用程序中的用户。 这些限制是 *应用程序沙箱* 的一部分–一组规则，用于限制应用程序访问文件、首选项、网络资源、硬件等。应用程序只能在其主目录中读取和写入文件 (安装的位置) ;它无法访问其他应用程序的文件。

iOS 还具有一些特定于文件系统的功能：某些目录需要在备份和升级方面进行特殊处理，应用程序还可以彼此共享文件和 **文件** 应用 (自 iOS 11) ，以及通过 iTunes。

本文介绍了 iOS 文件系统的功能和限制，并提供了演示如何使用 Xamarin 执行一些简单文件系统操作的示例应用程序：

[![执行一些简单文件系统操作的 iOS 示例](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>常规文件访问

Xamarin 允许在 `System.IO` ios 上使用 .net 类进行文件系统操作。

以下代码片段演示了一些常见的文件操作。 在本文的示例应用程序中，你将在 **SampleCode.cs** 文件中找到这些文件。

### <a name="working-with-directories"></a>使用目录

此代码枚举当前目录中的子目录 (由 "./" 参数) 指定，该参数是应用程序可执行文件的位置。
你的输出将是一个列表，其中列出了你的应用程序部署的所有文件和文件夹 (在调试) 时显示在控制台窗口中。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>读取文件

若要读取文本文件，只需要一行代码。 此示例将在 "应用程序输出" 窗口中显示文本文件的内容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>XML 序列化

尽管使用完整 `System.Xml` 命名空间超出了本文的范围，但你可以使用类似于以下代码片段的 StreamReader 从文件系统中轻松地反序列化 XML 文档：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

有关详细信息，请参阅 [System.Xml](xref:System.Xml) 和 [序列化](xref:System.Xml.Serialization)的文档。 请参阅链接器上的 [Xamarin 文档](~/ios/deploy-test/linker.md) –通常需要将 `[Preserve]` 属性添加到要序列化的类。

### <a name="creating-files-and-directories"></a>创建文件和目录

此示例演示如何使用 `Environment` 类访问文档文件夹，在该文件夹中可以创建文件和目录。

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

创建目录的过程类似于：

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

有关详细信息，请参阅 [SYSTEM.IO API 参考](xref:System.IO)。

### <a name="serializing-json"></a>序列化 JSON

[Json.NET](http://www.newtonsoft.com/json) 是一种高性能 Json 框架，适用于 Xamarin，可在 NuGet 上获取。 将 NuGet 包添加到应用程序项目，并在 Visual Studio for Mac 中使用 " **添加 nuget** "：

[![将 NuGet 包添加到应用程序项目](file-system-images/json01.png)](file-system-images/json01.png#lightbox)

接下来，添加一个类，用作序列化/反序列化的数据模型 (在此示例中 `Account.cs`) ：

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

最后，创建类的实例 `Account` ，将其序列化为 json 数据并将其写入文件：

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

有关使用 .NET 应用程序中的 json 数据的详细信息，请参阅 Json。 NET 的 [文档](https://www.newtonsoft.com/json/help)。

## <a name="special-considerations"></a>特殊注意事项

尽管 Xamarin 和 .NET 文件操作之间的相似之处，但在一些重要方面，iOS 和 Xamarin 不同于 .NET。

### <a name="making-project-files-accessible-at-runtime"></a>使项目文件在运行时可访问

默认情况下，如果你将文件添加到项目，则它不会包含在最终程序集中，因此不会提供给你的应用程序使用。 若要将文件包含在程序集中，必须使用一个名为 Content 的特殊生成操作对其进行标记。

若要标记要包含的文件，请右键单击该文件 (") 并选择" Visual Studio for Mac 中的 " **生成操作 &gt; 内容** "。 您还可以在文件的**属性**表中更改 "**生成" 操作**。

### <a name="case-sensitivity"></a>事例敏感性

必须了解 iOS 文件系统 *区分大小写*。 区分大小写意味着文件和目录名称必须完全匹配– **README.txt** 和 **readme.txt** 被视为不同的文件名。

对于更熟悉 Windows 文件系统（不 *区分大小写* ）的 .net 开发人员而言，这可能会造成混淆– **文件**、 **文件**和 **文件** 都将引用相同的目录。

> [!WARNING]
> IOS 模拟器不区分大小写。
> 如果文件本身和代码中的文件名大小写不同，则你的代码可能仍在模拟器中运行，但在实际设备上会失败。 这是很重要的原因，就是在 iOS 开发过程的早期和经常部署和测试。

### <a name="path-separator"></a>路径分隔符

iOS 使用正斜杠 "/" 作为路径分隔符 (该路径分隔符不同于 Windows，后者使用反斜杠 "\" ) 。

由于这种混乱的差异，因此最好使用为 `System.IO.Path.Combine` 当前平台调整的方法，而不是硬编码特定路径分隔符。 这是一个简单的步骤，使你的代码更易于移植到其他平台。

## <a name="application-sandbox"></a>应用程序沙盒

出于安全方面的考虑，应用程序对文件系统 (和其他资源（如网络和硬件功能) ）的访问受到限制。 此限制称为 *应用程序沙盒*。 就文件系统而言，应用程序只能在其主目录中创建和删除文件和目录。

主目录是在文件系统中存储应用程序及其所有数据的唯一位置。 不能选择 (或更改应用程序的主目录的位置) 但是，iOS 和 Xamarin 会提供属性和方法来管理中的文件和目录。

## <a name="the-application-bundle"></a>应用程序捆绑包

*应用程序捆绑包*是包含应用程序的文件夹。
可以通过将 app.config 后缀添加到目录名称来区分其他文件夹。 应用程序捆绑包包含你的可执行文件和你的项目所需的所有内容 (文件、映像等 ) 。

当你在 Mac OS 中浏览到应用程序捆绑包时，它会显示一个图标，而不是其他目录中看到的图标 (并且 **应用** 后缀将隐藏) ;但是，它只是操作系统显示方式不同的常规目录。

若要查看示例代码的应用程序捆绑包，请在 **Visual Studio for Mac** 中右键单击该项目，然后选择 **"在查找器中显示**"。 然后导航到要在其中找到应用程序图标 (类似于下面的屏幕截图) 的 **bin/** 目录。

![浏览 bin 目录，查找类似于此屏幕截图的应用程序图标](file-system-images/40-bundle.png)

右键单击此图标，然后选择 " **显示包内容** "，浏览应用程序包目录的内容。 内容与常规目录的内容一样，如下所示：

[![应用捆绑包的内容](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

应用程序捆绑包是在模拟器上或在测试期间在设备上安装的，最终会将其提交到 Apple 以包含在 App Store 中。

## <a name="application-directories"></a>应用程序目录

当你的应用程序安装在设备上时，操作系统将为你的应用程序创建一个主目录，并在应用程序根目录内创建可供使用的多个目录。 由于 iOS 8，用户可访问的目录 [不](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) 在应用程序根目录中，因此不能从用户目录派生应用程序捆绑包的路径，反之亦然。

下面列出了这些目录、如何确定其路径及其用途：

&nbsp;

|Directory|说明|
|---|---|
|[ApplicationName] app.config/|**在 iOS 7 及更早版本中**，这是 `ApplicationBundle` 存储应用程序可执行文件的目录。 在应用程序中创建的目录结构存在于此目录中 (例如，在 Visual Studio for Mac 项目) 中标记为资源的图像和其他文件类型。<br /><br />如果需要访问应用程序捆绑中的内容文件，则可以通过属性访问此目录的路径 `NSBundle.MainBundle.BundlePath` 。|
|文档|使用此目录来存储用户文档和应用程序数据文件。<br /><br />此目录的内容可以通过 iTunes 文件共享 (提供给用户，尽管默认情况下禁用了此功能) 。 将一个 `UIFileSharingEnabled` 布尔键添加到 info.plist 文件，以允许用户访问这些文件。<br /><br />即使应用程序不会立即启用文件共享，也应避免将此目录中用户隐藏的文件 (例如数据库文件中，除非你想要将这些文件共享) 。 只要敏感文件保持隐藏，iTunes) 将不会公开这些文件 (并可能被的移动、修改或删除，前提是在未来版本中启用了文件共享。<br /><br /> 可以使用 `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` 方法获取应用程序的文档目录路径。<br /><br />此目录的内容由 iTunes 备份。|
|类库|库目录是存储不是由用户直接创建的文件（如数据库或其他应用程序生成的文件）的好地方。 此目录的内容绝不会通过 iTunes 公开给用户。<br /><br />可以在库中创建自己的子目录;但是，这里已经有一些系统创建的目录，你应该注意这些目录，包括首选项和缓存。<br /><br />此目录的内容 (除了缓存子目录) ，由 iTunes 备份。 将备份您在库中创建的自定义目录。|
|库/首选项/|特定于应用程序的首选项文件存储在此目录中。 不要直接创建这些文件。 而应使用 `NSUserDefaults` 类。<br /><br />此目录的内容由 iTunes 备份。|
|库/缓存/|缓存目录是存储可以帮助你的应用程序运行，但可以轻松重新创建的数据文件的好地方。 应用程序应根据需要创建和删除这些文件，并在必要时重新创建这些文件。 iOS 5 还可能在) 低存储情况下删除这些文件 (，但在应用程序运行时不会这样做。<br /><br />ITunes 不会备份此目录的内容，这意味着，如果用户还原设备，则不会显示此目录的内容，并且在安装更新版本的应用程序后可能不会显示这些内容。<br /><br />例如，如果应用程序无法连接到网络，则可以使用 cache 目录来存储数据或文件，以提供良好的脱机体验。 应用程序可以在等待网络响应时快速保存和检索此数据，但不需要进行备份，也可以在还原或版本更新后轻松恢复或重新创建。|
|tmp|应用程序可以在此目录中存储只需要很短时间的临时文件。 若要节省空间，应在不再需要文件时将其删除。 当应用程序未运行时，操作系统也可能会删除此目录中的文件。<br /><br />ITunes 不会备份此目录的内容。<br /><br />例如，tmp 目录可用于存储下载以显示给用户 (如 Twitter 头像或电子邮件附件) 的临时文件，但如果将来) 需要这些文件，则这些文件可能会被查看 (并再次下载。|

此屏幕截图显示查找器窗口中的目录结构：

[![此屏幕截图显示查找器窗口中的目录结构](file-system-images/08-library-directory.png)](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>以编程方式访问其他目录

前面的目录和文件示例访问了 `Documents` 目录。 若要写入另一个目录，必须使用 ".." 语法构造路径，如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

创建目录与此类似：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

可以按如下 `Caches` 方式 `tmp` 构造和目录的路径：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>与文件应用共享

iOS 11 引入了 **文件** 应用程序-适用于 iOS 的文件浏览器，用户可以在 iCloud 中查看其文件并与其交互，还可以通过支持它的任何应用程序来存储这些文件。 若要允许用户直接访问应用中的文件，请在 **info.plist** 文件中创建一个新的布尔键， `LSSupportsOpeningDocumentsInPlace` 并将其设置为 `true` ，如下所示：

![设置 LSSupportsOpeningDocumentsInPlace 中的 info.plist](file-system-images/51-supports-opening.png)

现在，应用的 **文档** 目录将可用于在 **文件** 应用中浏览。 在 **文件** 应用中，导航到 **iPhone 上** 的，并且每个具有共享文件的应用都将可见。 下面的屏幕截图显示了 [FileSystem 示例应用](/samples/xamarin/ios-samples/filesystemsamplecode) 的外观：

![iOS 11 文件应用](file-system-images/50-files-app-1-sml.png) ![浏览我的 iPhone 文件](file-system-images/50-files-app-2-sml.png) ![示例应用文件](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>通过 iTunes 与用户共享文件

用户可以通过 `Info.plist` 在源视图中编辑和创建**支持 iTunes 共享** () 条目的应用程序来访问应用程序文档目录中的文件 `UIFileSharingEnabled` ，如下所示： **Source**

[![添加应用程序支持 iTunes 共享属性](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

当设备已连接并且用户选择该选项卡时，可在 iTunes 中访问这些文件 `Apps` 。例如，以下屏幕截图显示通过 iTunes 共享的选定应用中的文件：

[![此屏幕截图显示通过 iTunes 共享的选定应用中的文件](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

用户只能通过 iTunes 访问此目录中的顶级项目。 它们看不到任何子目录的内容，但它们可以将它们复制到其计算机上，或)  (。 例如，对于 GoodReader，PDF 和 EPUB 文件可以与应用程序共享，以便用户可以在其 iOS 设备上阅读这些文件。

如果用户不小心，修改文档文件夹内容的用户可能会遇到问题。 应用程序应考虑到这一点，并可灵活应对 Documents 文件夹的破坏性更新。

本文的示例代码在 **SampleCode.cs**) 中的 Documents 文件夹 (创建文件和文件夹，并在 **info.plist** 文件中启用文件共享。 此屏幕截图显示了这些内容在 iTunes 中的显示方式：

[![此屏幕截图显示了文件在 iTunes 中的显示方式](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

有关如何为应用程序以及您创建的任何自定义文档类型设置图标的信息，请参阅使用 [图像](~/ios/app-fundamentals/images-icons/index.md) 。

如果该 `UIFileSharingEnabled` 密钥为 false 或不存在，则默认情况下，"文件共享" 将处于禁用状态，并且用户将无法与文档目录交互。

## <a name="backup-and-restore"></a>备份和还原

当 iTunes 在设备上进行备份时，将保存在应用程序的主目录中创建的所有目录，但以下目录除外：

- **[ApplicationName]. app** –不要写入此目录，因为它已签名，因此在安装后必须保持不变。 它可能包含从代码访问的资源，但不需要备份，因为这些资源将通过重新下载应用来还原。
- **库/缓存** -缓存目录适用于无需备份的工作文件。
- **tmp** –此目录用于在不再需要时创建和删除的临时文件，或 iOS 在需要空间时删除的文件。

备份大量数据可能需要较长时间。 如果你决定需要备份任何特定文档或数据，则你的应用程序应使用 "文档" 和 "库" 文件夹。 对于可以轻松从网络检索的暂时性数据或文件，请使用缓存或 tmp 目录。

> [!NOTE]
> 当设备的磁盘空间严重不足时，iOS 将 "清理" 文件系统。
> 此过程将删除当前未运行的应用程序的库/缓存和 tmp 文件夹中的所有文件。

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>遵循 iOS 5 iCloud 备份限制

> [!NOTE]
> 尽管此策略是在 iOS 5 (首次引入的，但似乎很长一段时间) 本指南仍与应用程序相关。

Apple 在 iOS 5 中引入了 *ICloud 备份* 功能。 启用 iCloud 备份后，应用程序的主目录中的所有文件 (都不包括通常备份的目录，例如，应用程序捆绑包、 `Caches` 和 `tmp`) 备份到 iCloud 服务器。 此功能在设备丢失、被盗或损坏时为用户提供完整的备份。

由于 iCloud 仅向每个用户提供 5 Gb 的可用空间，并避免不必要地使用带宽，因此 Apple 预期应用程序仅备份用户生成的重要数据。 若要遵守 iOS 数据存储准则，应通过遵循以下各项来限制备份的数据量：

- 仅将用户生成的数据或无法重新创建的数据存储在已备份)  (文档目录中。
- 将任何其他可以轻松重新创建或重新下载的数据存储在 `Library/Caches` 或 `tmp` (不会进行备份，并且可以 ) 进行 "清理"。
- 如果你的文件可能适用于 `Library/Caches` 或 `tmp` 文件夹，但你不希望进行 "清理"，请将其存储在其他位置 (例如 `Library/YourData`) 并应用 "不备份" 属性，以防止文件使用 up 备份带宽和存储空间。 此数据仍占用设备上的空间，因此你应仔细管理此数据，并在可能的情况下将其删除。

使用类设置 "不备份" 特性 `NSFileManager` 。 确保你的类为 `using Foundation` ，并调用 `SetSkipBackupAttribute` 如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

如果 `SetSkipBackupAttribute` 为 `true` ，则不会备份文件，无论该文件存储在 (目录中，即使目录) 也是如此 `Documents` 。 您可以使用方法查询属性 `GetSkipBackupAttribute` ，还可以通过调用方法来重置该属性 `SetSkipBackupAttribute` `false` ，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>在 iOS 应用和应用扩展之间共享数据

由于应用扩展作为主机应用程序的一部分运行 (而不是包含应用) ，因此不会自动包含数据共享，因此需要额外的工作。 应用组是 iOS 用于允许不同应用共享数据的机制。 如果应用程序已正确配置了正确的权利和预配，则它们可以访问其正常 iOS 沙箱之外的共享目录。

### <a name="configure-an-app-group"></a>配置应用组

通过[应用组](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，这是在 [iOS 开发中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符和描述文件**部分配置的。 还必须在每个项目的 **info.plist**中引用此值。

有关创建和配置应用组的信息，请参阅 [应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) 指南。

### <a name="files"></a>文件

IOS 应用和扩展还可以使用公共文件路径共享文件， (假设已使用正确的权利和预配) 正确配置文件：

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> 如果返回的组路径为 `null` ，请检查权利和预配配置文件的配置，并确保它们是正确的。

## <a name="application-version-updates"></a>应用程序版本更新

下载新版本的应用程序时，iOS 会创建一个新的主目录，并在其中存储新的应用程序捆绑包。 然后，iOS 将以下文件夹从您的应用程序捆绑包的先前版本移至新的主目录：

- 文档
- **Library**

其他目录还可以在新的主目录中进行复制和放置，但是不能保证复制它们，因此应用程序不应依赖于此系统行为。

## <a name="summary"></a>总结

本文说明了 Xamarin 的文件系统操作与任何其他 .NET 应用程序类似。 它还引入了应用程序沙箱，并检查了它所造成的安全隐患。 接下来，它探讨了应用程序捆绑包的概念。 最后，它枚举了可用于你的应用程序的专用目录，并在应用程序升级和备份过程中解释了其角色。

## <a name="related-links"></a>相关链接

- [文件系统示例代码](/samples/xamarin/ios-samples/filesystemsamplecode)
- [文件系统编程指南](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [注册应用支持的文件类型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)