---
title: 第 5 部分 - 实际代码共享策略
description: 本文档介绍了适用于各种方案（例如数据库、文件访问、网络操作和异步代码）的实用代码共享策略。
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: d10917471c37d91fa02db2585895f5694a5a4a60
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457440"
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5 部分 - 实际代码共享策略

本部分提供了有关如何为常见应用程序方案共享代码的示例。

## <a name="data-layer"></a>数据层

数据层由存储引擎和用于读取和写入信息的方法组成。 为实现性能、灵活性和跨平台兼容性，建议对 Xamarin 跨平台应用程序使用 SQLite 数据库引擎。
它在各种平台（包括 Windows、Android、iOS 和 Mac）上运行。

### <a name="sqlite"></a>SQLite

SQLite 是一个开源数据库实现。 可以在 [SQLite.org](https://www.sqlite.org/)中找到源和文档。SQLite 支持适用于每个移动平台：

- **iOS** –内置于操作系统。
- **Android** –内置于操作系统中，因为 android 2.2 (API 级别 10) 。
- **Windows** - [有关通用 Windows 平台扩展](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)，请参阅 SQLite。

即使在所有平台上都提供了数据库引擎，访问数据库的本机方法也不同。 IOS 和 Android 都提供内置 Api 来访问可从 Xamarin 或 Xamarin 使用的 SQLite，然而，使用本机 SDK 方法不能共享代码，而不是 SQL 查询本身 (，假设它们以字符串的形式存储) 。 有关 iOS 或 Android 类中的本机数据库功能搜索的详细信息， `CoreData` 请阅读 `SQLiteOpenHelper` 这些选项，因为这些选项不是跨平台的，它们超出了本文档的范围。

### <a name="adonet"></a>ADO.NET

Xamarin 和 Xamarin 都支持 `System.Data` 和 `Mono.Data.Sqlite` (参见 Xamarin [文档](~/ios/data-cloud/system.data.md) 以了解详细信息) 。
通过使用这些命名空间，可以编写适用于这两个平台的 ADO.NET 代码。 编辑项目的引用以包含 `System.Data.dll` 并 `Mono.Data.Sqlite.dll` 将这些 using 语句添加到代码中：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

下面的示例代码将起作用：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

实际的 ADO.NET 实现会在不同的方法和类中进行拆分 (此示例仅用于演示目的) 。

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET –跨平台 ORM

ORM (或对象关系映射器) 尝试简化在类中建模的数据的存储。 ORM 添加了一层代码来执行此工作，而不是手动编写创建表或选择、插入和删除从类字段和属性中手动提取的数据的 SQL 查询。 使用反射检查类的结构，ORM 可以自动创建与类匹配的表和列，并生成查询以读取和写入数据。 这样，应用程序代码就可以轻松地将对象实例发送和检索到 ORM，后者负责处理所有的 SQL 操作。

SQLite-NET 充当简单的 ORM，使你能够在 SQLite 中保存和检索类。 它通过编译器指令和其他技巧的组合，隐藏跨平台 SQLite 访问的复杂性。

SQLite-NET 的功能：

- 表是通过向模型类添加特性来定义的。
- 数据库实例由的子类表示，它是  `SQLiteConnection` SQLite 网络库中的主类。
- 可以使用对象插入、查询和删除数据。 不需要任何 SQL 语句 (虽然您可以根据需要编写 SQL 语句) 。
- 可对 SQLite-NET 返回的集合执行基本 Linq 查询。

在 [github 上，在 github 上](https://github.com/praeclarum/sqlite-net) 提供了有关 sqlite 网络的源代码和文档，并在这两种情况下均已实现。 下面显示了 *Tasky Pro* case 研究)  (的简单示例。

首先， `TodoItem` 类使用特性将字段定义为数据库主键：

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

这样， `TodoItem` 就可以使用以下代码行来创建表 (并且不会在实例上) 任何 SQL 语句 `SQLiteConnection` ：

```csharp
CreateTable<TodoItem> ();
```

还可以通过 (上的其他方法对表中的数据进行操作 `SQLiteConnection` ，而无需) 的 SQL 语句：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

有关完整示例，请参阅案例研究源代码。

## <a name="file-access"></a>文件访问

文件访问被确定为任何应用程序的关键部分。 可能是应用程序的一部分的常见文件示例包括：

- SQLite 数据库文件。
- 用户生成的数据 (文本、图像、声音、视频) 。
- 下载了用于缓存 (图像、html 或 PDF 文件) 的数据。

### <a name="systemio-direct-access"></a>System.IO 直接访问

Xamarin 和 Xamarin 都允许使用命名空间中的类进行文件系统访问 `System.IO` 。

每个平台都有不同的访问限制，必须加以考虑：

- iOS 应用程序在文件系统访问受限的沙盒中运行。 Apple 进一步决定了应如何通过指定 (的某些位置以及未) 的其他位置来使用文件系统。 有关更多详细信息，请参阅使用  [Xamarin 中的文件系统](~/ios/app-fundamentals/file-system.md) 指南。
- Android 还限制了对与应用程序相关的某些目录的访问，但它也支持外部媒体 (例如。 SD 卡) 和访问共享数据。
- Windows Phone 8 (Silverlight) 不允许直接访问文件–文件只能使用来处理 `IsolatedStorage` 。
- Windows 8.1 WinRT 和 Windows 10 UWP 项目仅通过 api 提供异步文件操作 `Windows.Storage` ，这些操作不同于其他平台。

#### <a name="example-for-ios-and-android"></a>IOS 和 Android 示例

下面显示了写入和读取文本文件的简单示例。
使用时 `Environment.GetFolderPath` ，可以在 iOS 和 Android 上运行相同的代码，每个代码都基于文件系统约定返回有效的目录。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

有关特定于 iOS 的文件系统功能的详细信息，请参阅 [使用文件系统](~/ios/app-fundamentals/file-system.md) 的 Xamarin。 编写跨平台文件访问代码时，请记住，某些文件系统区分大小写，并且具有不同的目录分隔符。 最好始终对文件名使用相同的大小写，并在 `Path.Combine()` 构造文件或目录路径时使用方法。

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>适用于 Windows 8 和 Windows 10 的 windows 存储

*创建具有 Xamarin 的移动应用程序。* [book](https://developer.xamarin.com/r/xamarin-forms/book/) 
 [Async 和 File i/o](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)包括[Windows 8.1 和 Windows 10 的示例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

通过使用 [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 支持的 api，可以在这些平台上读取和文件文件：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

请参阅 [书籍一章](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) 了解更多详细信息。

<a name="Isolated_Storage"></a>

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Windows Phone 7 & 8 (Silverlight 上的独立存储) 

独立存储是一种常见的 API，用于在所有 iOS、Android 和更早版本的 Windows Phone 平台上保存和加载文件。

这是 Windows Phone (Silverlight) 中的文件访问的默认机制，已在 Xamarin 和 Xamarin 中实现，以允许写入常见文件访问代码。 `System.IO.IsolatedStorage`可以在[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)中的所有三个平台之间引用此类。

有关详细信息，请参阅 [Windows Phone 的独立存储概述](/previous-versions/windows/apps/ff402541(v=vs.105)) 。

独立存储 Api 在 [可移植类库](~/cross-platform/app-fundamentals/pcl.md)中不可用。 PCL 的一种替代方法是 [PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Pcl 中的跨平台文件访问

此外，还提供了与 PCL 兼容的 NuGet – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) ，可跨平台文件访问支持 Xamarin 的平台和最新的 Windows api。

## <a name="network-operations"></a>网络运营

大多数移动应用程序都有网络组件，例如：

- 下载图像、视频和音频 (例如 缩略图、照片、音乐) 。
- 下载文档 (例如 HTML、PDF) 。
- 上载用户数据 (如照片或文本) 。
- 访问 web 服务或第三方 Api (包括 SOAP、XML 或 JSON) 。

.NET Framework 提供了用于访问网络资源的几个不同的类： `HttpClient` 、 `WebClient` 和 `HttpWebRequest` 。

### <a name="httpclient"></a>HttpClient

`HttpClient`命名空间中的类 `System.Net.Http` 在 Xamarin、xamarin 和大多数 Windows 平台中可用。 有一个 [MICROSOFT HTTP 客户端库 NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) ，可用于将此 API 引入可移植类库 (和 Windows Phone 8 Silverlight) 。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient`类提供一个简单的 API，用于从远程服务器检索远程数据。

通用 Windows 平台操作 *必须* 是异步的，尽管 Xamarin 和 xamarin 支持同步操作 (可在后台线程) 执行此操作。

简单的异步操作的代码 `WebClient` 是：

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` 还包含 `DownloadFileCompleted` 和 `DownloadFileAsync` 用于检索二进制数据。

<a name="HttpWebRequest"></a>

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供比和更多的自定义 `WebClient` ，因此需要更多代码才能使用。

简单同步操作的代码 `HttpWebRequest` 是：

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)中提供了一个示例。

 <a name="Reachability"></a>

### <a name="reachability"></a>可访问性

移动设备在各种网络条件下运行，从快速 Wi-fi 或4G 连接到不良接收区域和慢边缘数据链接。 因此，最好在尝试连接到远程服务器之前，检测网络是否可用，如果有，则可以使用哪种类型的网络。

移动应用在这些情况下可能需要执行的操作包括：

- 如果网络不可用，建议用户。 如果他们已手动禁用了它 (例如。 飞行模式或关闭 Wi-fi) ，它们可以解决此问题。
- 如果连接是3G，则应用程序的行为可能不同 (例如，Apple 不允许通过 3G) 下载大于20Mb 的应用。 应用程序可以使用此信息在检索大型文件时警告用户过多的下载时间。
- 即使网络可用，也最好在启动其他请求之前验证与目标服务器的连接。 这会阻止应用程序的网络操作重复超时，还会允许向用户显示更丰富的错误消息。

## <a name="webservices"></a>WebServices

请参阅有关使用 [Web 服务](~/cross-platform/data-cloud/web-services/index.md)的文档，其中介绍了如何使用 XAMARIN 访问 REST、SOAP 和 WCF 终结点。 可以手动创建 web 服务请求并分析响应，但是，有一些库可使此方法更简单，包括 Azure、RestSharp 和 ServiceStack。 甚至可以在 Xamarin 应用程序中访问基本 WCF 操作。

### <a name="azure"></a>Azure

Microsoft Azure 是一种云平台，可为移动应用提供各种服务，包括数据存储和同步以及推送通知。

若要免费试用，请访问 [azure.microsoft.com](https://azure.microsoft.com/) 。

### <a name="restsharp"></a>RestSharp

RestSharp 是一个 .NET 库，可包含在移动应用程序中，以提供简化对 web 服务的访问的 REST 客户端。 它通过提供用于请求数据和分析 REST 响应的简单 API 来帮助。 RestSharp 非常有用

[RestSharp 网站](http://restsharp.org/)包含了有关如何使用 RESTSHARP 实现 REST 客户端的[文档](https://github.com/restsharp/RestSharp/wiki)。
RestSharp 在 [github](https://github.com/restsharp/RestSharp/)上提供 Xamarin 和 xamarin。

[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)中也有一个 Xamarin iOS 代码片段。

 <a name="ServiceStack"></a>

### <a name="servicestack"></a>ServiceStack

与 RestSharp 不同，ServiceStack 既是用于承载 web 服务的服务器端解决方案，也是可在移动应用程序中实现以访问这些服务的客户端库。

[ServiceStack 网站](http://servicestack.net/)说明了项目的目的和文档和代码示例的链接。 这些示例包括 web 服务的完整服务器端实现，以及可以访问它的各种客户端应用程序。

### <a name="wcf"></a>WCF

Xamarin 工具可帮助你使用某些 Windows Communication Foundation (WCF) 服务。 通常，Xamarin 支持与 Silverlight 运行时随附的同一客户端的 WCF 子集。 这包括 WCF 的最常见的编码和协议实现：使用 HTTP 传输协议的文本编码的 SOAP 消息 `BasicHttpBinding` 。

由于 WCF 框架的大小和复杂性，可能存在当前和未来的服务实现，这些实现将超出 Xamarin 的客户端子集域支持的范围。 此外，WCF 支持需要使用仅在 Windows 环境中可用的工具来生成代理。

 <a name="Threading"></a>

## <a name="threading"></a>线程

对于移动应用程序而言，应用程序响应能力非常重要–用户希望应用程序能够快速加载和执行。 停止接受用户输入的 "冻结" 屏幕将显示，指出应用程序已崩溃，因此，不需要将 UI 线程与长时间运行的阻塞调用（如网络请求或慢本地操作）连接 (如将文件解压缩) ，这一点很重要。 特别是启动过程不应包含长时间运行的任务，所有移动平台都将终止加载时间太长的应用程序。

这意味着你的用户界面应实现 "进度指示器" 或其他快速显示的 "使用" UI，以及执行后台操作的异步任务。 执行后台任务需要使用线程，这意味着后台任务需要一种方法来与主线程通信以指示进度或完成时间。

 <a name="Parallel_Task_Library"></a>

### <a name="parallel-task-library"></a>并行任务库

用并行任务库创建的任务可以异步运行并在其调用线程上返回，使其在不阻止用户界面的情况下触发长时间运行的操作非常有用。

简单的并行任务操作可能如下所示：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

密钥是， `TaskScheduler.FromCurrentSynchronizationContext()` 它将重复使用调用方法的线程的 SynchronizationContext， (在此处运行) 的主线程将 `MainThreadMethod` 作为封送回线程调用的方法。 这意味着，如果在 UI 线程上调用方法，则会 `ContinueWith` 在 ui 线程上运行该操作。

如果代码要从其他线程启动任务，请使用下面的模式创建对 UI 线程的引用，并且任务仍可以回调到该线程：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread"></a>

### <a name="invoking-on-the-ui-thread"></a>在 UI 线程上调用

对于不利用并行任务库的代码，每个平台都有自己的语法，用于将操作封送回 UI 线程：

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS 和 Android 语法都要求有一个可用的 "context" 类，这意味着代码需要将此对象传递到需要在 UI 线程上回调的任何方法。

若 [要在共享](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) 代码中调用 UI 线程，请按照)  ([@follesoe](https://twitter.com/follesoe) 。 `IDispatchOnUIThread`在共享代码中声明和程序接口，然后实现特定于平台的类，如下所示：

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Xamarin. Forms 开发人员应 [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) 在常见代码 (共享项目或 PCL) 中使用。

 <a name="Platform_and_Device_Capabilities_and_Degradation"></a>

## <a name="platform-and-device-capabilities-and-degradation"></a>平台和设备功能和降级

平台功能文档中提供了处理不同功能的更具体的示例。 它处理的是检测不同的功能，以及如何适当地降低应用程序的性能，以提供良好的用户体验，即使应用程序无法充分发挥其潜能。