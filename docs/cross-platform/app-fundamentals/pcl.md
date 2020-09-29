---
title: '可移植类库简介 (PCL) '
description: 本文介绍可移植类库 (PCL) 项目，并演练如何在 Visual Studio for Mac 和 Visual Studio 中创建和使用 PCL 项目。
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a1672937b8c7c0ce429b82aee34ecf45aa59d4c8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457414"
---
# <a name="portable-class-libraries-pcl"></a>可移植类库 (PCL)

> [!TIP]
> 在最新版本的 Visual Studio 中， (Pcl) 的可移植类库被视为已弃用。
> 尽管仍可以打开、编辑和编译 Pcl，但对于新项目，建议使用 [.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md) 来访问更大的 API 外围应用。

构建跨平台应用程序的一个关键组件是能够跨不同于平台的项目共享代码。 但是，这种情况很复杂，这是因为不同的平台通常使用不同的 .NET 基类库子集 (BCL) ，因此实际构建到不同的 .NET Core 库配置文件中。 这意味着，每个平台只能使用以相同配置文件为目标的类库，因此它们似乎需要为每个平台使用单独的类库项目。

有三种主要的代码共享方法可解决此问题： **.NET Standard 项目**、 **共享资产项目**和 **可移植类库 (PCL) 项目**。

- **.NET Standard 项目** 是用于共享 .net 代码的首选方法，请阅读有关 [.NET Standard 项目和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)的详细信息。
- **共享资产项目** 使用一组文件，并提供一种快速而简单的方法，用于在解决方案中共享代码，通常使用条件编译指令来指定将使用它 (的各种平台的代码路径。有关详细信息，请参阅 [共享项目一文](~/cross-platform/app-fundamentals/shared-projects.md)) 。
- **PCL** 项目面向支持一组已知的 BCL 类/功能的特定配置文件。 但是，PCL 的向下，它们通常需要额外的体系结构，以便将配置文件特定代码分隔到它们自己的库中。

本页说明如何创建以特定配置文件为目标的 **PCL** 项目，该配置文件随后可由多个特定于平台的项目引用。

## <a name="what-is-a-portable-class-library"></a>什么是可移植类库？

当你创建应用程序项目或库项目时，所生成的 DLL 将被限制为可以在为其创建它的特定平台上工作。 这会阻止你为 Windows 应用编写程序集，然后在 Xamarin 和 Xamarin 上重新使用该程序集。

但是，在创建可移植类库时，可以选择想要在其中运行代码的平台组合。 创建可移植类库时所做的兼容性选择会转换为 "Profile" 标识符，该标识符描述库支持哪些平台。

下表显示了由 .NET 平台变化的一些功能。 若要编写保证在特定设备/平台上运行的 PCL 程序集，只需选择创建项目时所需的支持。

|Feature|.NET Framework|UWP 应用|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|数据注释|4.0.3 +|Y|Y||Y|

Xamarin 列反映了 Xamarin iOS 和 Xamarin 支持 Visual Studio 随附的所有配置文件的事实，你创建的任何库中的功能可用性仅受你选择支持的其他平台的限制。

这包括组合的配置文件：

- .NET 4 或 .NET 4。5
- Silverlight 5
- Windows Phone 8
- UWP 应用

可以在 [Microsoft 网站](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library) 上阅读有关不同配置文件功能的详细信息，并查看另一个社区成员的 [PCL 配置文件摘要](https://portablelibraryprofiles.stephencleary.com/) ，其中包括受支持的框架信息和其他说明。

**优点**

1. 集中式代码共享–在可由其他库或应用程序使用的单个项目中编写和测试代码。
2. 重构操作将影响解决方案中加载的所有代码 (可移植类库和) 平台特定的项目。
3. 解决方案中的其他项目可以轻松地引用 PCL 项目，或者可以共享输出程序集以便其他人在其解决方案中引用。

**缺点**

1. 因为在多个应用程序之间共享相同的可移植类库，所以不能引用特定于平台的库 (例如。 CsharpSqlite. WP7) 。
2. 可移植类库子集不能包含在 Monotouch.dialog 和 Mono for Android (如 DllImport 或) 中都可用的类。

> [!NOTE]
> Visual Studio 的最新版本中已弃用可移植类库，但建议改用 [.NET Standard 库](net-standard.md) 。

在某种程度上，可以使用提供程序模式或依赖项注入对平台项目中的实际实现进行编码，以便对可移植类库中定义的接口或基类进行编码。

此图显示了使用可移植类库共享代码的跨平台应用程序的体系结构，但也使用依赖关系注入来传递依赖于平台的功能：

[![此图显示了使用可移植类库共享代码的跨平台应用程序的体系结构，还使用依赖关系注入来传入依赖于平台的功能](pcl-images/image1.png)](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练

本部分介绍如何使用 Visual Studio for Mac 创建和使用可移植类库。 有关完整实现，请参考 PCL 示例部分。

### <a name="creating-a-pcl"></a>创建 PCL

向解决方案添加可移植类库与添加常规库项目非常相似。

1. 在 " **新建项目** " 对话框中，选择 "多 **平台 > 库 > 可移植库** " 选项：

    ![创建新的 PCL 项目](pcl-images/image2.png)

1. 当在中创建 PCL 时 Visual Studio for Mac 它会自动配置一个适用于 Xamarin 和 Xamarin 的配置文件。 此时将显示 PCL 项目，如以下屏幕截图所示：

    ![解决方案板中的 PCL 项目](pcl-images/image3.png)

PCL 现在已准备好添加代码。 它还可由其他项目引用 (应用程序项目、库项目甚至) 其他 PCL 项目。

### <a name="editing-pcl-settings"></a>编辑 PCL 设置

若要查看和更改此项目的 PCL 设置，请右键单击该项目，然后选择 " **选项" > 生成 > 常规** "以查看此处所示的屏幕：

[![用于设置配置文件的 PCL 项目选项](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

单击 " **更改 ...** " 更改此可移植类库的目标配置文件。

如果在将代码添加到 PCL 后，配置文件发生了更改，则在代码引用了不是新选择的配置文件的一部分的功能时，库将无法再编译。

## <a name="working-with-a-pcl"></a>使用 PCL

在 PCL 库中编写代码时，Visual Studio for Mac 编辑器将识别所选配置文件的限制，并相应地调整自动完成选项。 例如，此屏幕截图显示了 System.IO 的自动完成选项，其中使用了 Visual Studio for Mac 中使用的默认配置文件 (Profile136) ，请注意，显示大约一半可用类的滚动条会显示 (，实际上只有14个类) 可用。

[![PCL 的 System.IO 类中14类的 Intellisense 列表](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

将其与 Xamarin 或 Xamarin 项目中的 System.IO 自动完成进行比较–提供了40类，包括常用类（如 `File` 和 `Directory` ），它们不在任何 PCL 配置文件中。

[![.NET Framework System.IO 命名空间中40类的 Intellisense 列表](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

这反映了使用 PCL 的底层权衡–能够无缝地跨多个平台共享代码，这意味着某些 Api 不能供你使用，因为它们没有跨所有可能平台的可比较实现。

### <a name="using-pcl"></a>使用 PCL

创建 PCL 项目后，可以采用与通常添加引用相同的方式从任何兼容的应用程序或库项目中添加对该项目的引用。 在 Visual Studio for Mac 中，右键单击 "引用" 节点，然后选择 " **编辑引用 ...** "，然后切换到 " **项目** " 选项卡，如下所示：

[![通过 "编辑引用" 选项添加对 PCL 的引用](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

以下屏幕截图显示了 TaskyPortable 示例应用的解决方案板，其中显示了底部的 PCL 库，并在 Xamarin 项目中显示了对该 PCL 库的引用。

[![显示 PCL 项目的 TaskyPortable 示例解决方案](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

PCL (的输出。还可以将生成的程序集 DLL) 添加为对大多数项目的引用。 这使 PCL 成为交付跨平台组件和库的理想方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 演练

本部分介绍如何使用 Visual Studio 创建和使用可移植类库。 有关完整实现，请参考 PCL 示例部分。

### <a name="creating-a-pcl"></a>创建 PCL

在 Visual Studio 中向解决方案添加 PCL 与添加常规项目略有不同：

1. 在 " **添加新项目** " 屏幕中，选择 "类库 ** (旧版可移植) ** " 选项。 请注意，右侧的说明建议此项目类型已弃用。

    [![用于创建可移植类库的 "新建项目" 窗口](pcl-images/image10-sml.png "可移植类库")](pcl-images/image10.png#lightbox)

2. Visual Studio 会立即提示以下对话框，以便可以配置配置文件。
 勾选需要支持的平台，并按 "确定"。

    [![选择库的目标平台](pcl-images/image11-sml.png "勾选需要支持的平台，并按 "确定"")](pcl-images/image11.png#lightbox)

3. PCL 项目将显示在解决方案资源管理器 &ndash; 文本 ** (可移植) ** 显示在项目名称旁边，以指示它是 PCL：

    ![PCL 配置文件定义的 NET Framework](pcl-images/image12.png "PCL 配置文件定义的 NET Framework")

PCL 现在已准备好添加代码。 它还可由其他项目引用 (应用程序项目、库项目甚至) 的其他 PCL 项目。

### <a name="editing-pcl-settings"></a>编辑 PCL 设置

可以通过右键单击项目并选择 " **属性" > 库** "来查看和更改 PCL 设置，如以下屏幕截图所示：

[![编辑平台目标](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

如果在将代码添加到 PCL 后，配置文件发生了更改，则在代码引用了不是新选择的配置文件的一部分的功能时，库将无法再编译。

> [!TIP]
> 还有一条消息通知 **。建议使用 NETStandard 来共享代码**。 这表明，在仍支持 Pcl 的情况下，建议升级到 .NET Standard。

### <a name="working-with-a-pcl"></a>使用 PCL

当代码以 PCL 库编写时，Visual Studio 将识别所选配置文件的限制，并相应地调整 Intellisense 选项。 例如，下面的屏幕截图显示了使用默认配置文件 (Profile136) 自动完成的选项 System.IO –请注意，显示大约一半可用类的滚动条将显示 (事实上，只有14个类可) 。

[![减少了 PCL 中可用的 IO 类的数目](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

将其与常规项目中的 System.IO 自动完成进行比较–提供了40个类，包括常用类（如 `File` 和 `Directory` ），它们不在任何 PCL 配置文件中。

[![.NET Framework 中提供了更多的 IO 类](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

这反映了使用 PCL 的底层权衡–能够无缝地跨多个平台共享代码，这意味着某些 Api 不能供你使用，因为它们没有跨所有可能平台的可比较实现。

> [!TIP]
> .NET Standard 2.0 表示比 Pcl 更大的 API 外围应用，包括 System.IO 命名空间。 对于新项目，建议使用 PCL .NET Standard。

### <a name="using-pcl"></a>使用 PCL

创建 PCL 项目后，可以采用与通常添加引用相同的方式从任何兼容的应用程序或库项目中添加对该项目的引用。 在 Visual Studio 中，右键单击 "引用" 节点， `Add Reference...` 然后选择 " **> 项目** " 选项卡切换到解决方案，如下所示：

[![通过 "添加引用项目" 选项卡添加对 PCL 的引用](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

以下屏幕截图显示了 TaskyPortable 示例应用的解决方案窗格，其中显示了底部的 PCL 库，并在 Xamarin 项目中显示了对该 PCL 库的引用。

[![显示 PCL 库的 TaskyPortable 示例解决方案](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

PCL (的输出。还可以将生成的程序集 DLL) 添加为对大多数项目的引用。
这使 PCL 成为交付跨平台组件和库的理想方法。

-----

## <a name="pcl-example"></a>PCL 示例

[TaskyPortable](/samples/xamarin/mobile-samples/taskyportable/)示例应用程序演示如何在 Xamarin 中使用可移植类库。
下面是在 iOS 和 Android 上运行的应用程序的一些屏幕截图：

[![下面是在 iOS、Android 和 Windows Phone 上运行的应用程序的一些屏幕截图](pcl-images/image18.png)](pcl-images/image18.png#lightbox)

它共享多个纯粹是可移植代码的数据和逻辑类，还演示了如何使用 SQLite 数据库实现的依赖项注入来合并平台特定的要求。

解决方案结构在下面分别 (Visual Studio for Mac 和 Visual Studio 中) ：

[![解决方案结构分别显示在 Visual Studio for Mac 和 Visual Studio 中](pcl-images/image19.png)](pcl-images/image19.png#lightbox)

由于 SQLite 代码具有特定于平台的部分， (在每个不同操作系统上使用 SQLite 实现) 出于演示目的，它已重构为可编译为可移植类库的抽象类，以及在 iOS 和 Android 项目中作为子类实现的实际代码。

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

可移植类库受支持的 .NET 功能的限制。 由于它编译为在多个平台上运行，因此它不能使用 `[DllImport]` SQLite-NET 中使用的功能。 相反，SQLite 作为抽象类实现，然后通过其余共享代码进行引用。 抽象 API 的提取如下所示：

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

共享代码的其余部分使用抽象类从数据库 "存储" 和 "检索" 对象。 在使用此抽象类的任何应用程序中，都必须传入提供实际数据库功能的完整实现。

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS

IOS 和 Android 应用程序项目包含用户界面和其他特定于平台的代码，用于在 PCL 中连接共享代码。

这些项目还包含在该平台上运行的抽象数据库 API 的实现。 在 iOS 和 Android 上，Sqlite 数据库引擎内置于操作系统中，因此实现可以使用 `[DllImport]` 来提供数据库连接的具体实现。 下面显示了特定于平台的实现代码摘录：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

可以在示例代码中查看完整的实现。

## <a name="summary"></a>总结

本文简要介绍了可移植类库的优点和缺陷，演示了如何从 Visual Studio for Mac 和 Visual Studio 内部创建和使用 Pcl;最后引入了一个完整的示例应用程序– TaskyPortable –它显示了操作中的 PCL。

## <a name="related-links"></a>相关链接

- [TaskyPortable (示例) ](/samples/xamarin/mobile-samples/taskyportable/)
- [生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [ (Microsoft 的 .NET Framework 跨平台开发) ](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)