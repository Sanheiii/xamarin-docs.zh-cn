---
title: 将数据存储在本地 SQLite.NET 数据库中
description: 本文说明如何将数据存储在本地 SQLite.NET 数据库中。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f19595fdabd663312e6346deaed65f85aed1280
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436969"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>将数据存储在本地 SQLite.NET 数据库中

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

在本快速入门中，你将了解如何：

- 使用 NuGet 包管理器将 NuGet 包添加到项目。
- 在本地将数据存储在 SQLite.NET 数据库中。

本快速入门演练如何将数据存储在本地 SQLite.NET 数据库中。 最终的应用程序如下所示：

[![便笺页面](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox "便笺页面")
[![便笺输入页面](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox "便笺输入页面")

## <a name="prerequisites"></a>先决条件

在尝试本快速入门之前，应成功完成[上一个快速入门](multi-page.md)。 或者，下载[上一个快速入门示例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)并将它用作本快速入门的起点。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio 并打开 Notes 解决方案。

2. 在“解决方案资源管理器”中，选择“Notes”项目，然后右键单击并选择“管理 NuGet 包…”  ：

    ![添加 NuGet 包](database-images/vs/add-nuget-packages.png)    

3. 在“NuGet 包管理器”中，选择“浏览”选项卡，搜索“sqlite-net-pcl”NuGet 包，选择它，然后单击“安装”按钮将其添加到项目   ：

    ![添加包](database-images/vs/add-package.png)

    > [!NOTE]
    > 许多 NuGet 包都有着类似的名称。 正确的包具有以下属性：
    > - **创建者：** Frank A. Krueger
    > - **ID：** sqlite net pcl
    > - **NuGet 链接：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 尽管包的名称如此，此 NuGet 包也可以用于 .NET Standard 项目。

    此包将用于将数据库操作合并到应用程序。

4. 在“解决方案资源管理器”的“Notes”项目中，打开“Models”文件夹中的“Note.cs”，并将现有代码替换为以下代码：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义一个 `Note` 模型，该模型将在应用程序中存储有关每个便笺的数据。 使用 `PrimaryKey` 和 `AutoIncrement` 特性标记 `ID` 属性，以确保 SQLite.NET 数据库中的每个 `Note` 实例均具有 SQLite.NET 提供的唯一 ID。

    通过按 Ctrl+S，保存对 Note.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

5. 在“解决方案资源管理器”中，将名为“Data”的新文件夹添加到“Notes”项目。

6. 在“解决方案资源管理器”的“Notes”项目中，将名为“NoteDatabase”的新类添加到“Data”文件夹。

7. 在 NoteDatabase.cs 中，将现有代码替换为以下代码：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此类包含用于创建数据库、从中读取数据、向其写入数据以及从中删除数据的代码。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`NoteDatabase` 构造函数将数据库文件的路径作为参数。 在下一步中，此路径由 `App` 类提供。

    通过按 Ctrl+S，保存对 NoteDatabase.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

8. 在“解决方案资源管理器”的“Notes”项目中，双击“App.xaml.cs”将其打开  。 然后将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    此代码定义一个 `Database` 属性，该属性以单一实例的形式创建新 `NoteDatabase` 实例，将数据库的文件名作为参数传入 `NoteDatabase` 构造函数。 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

9. 在“解决方案资源管理器”的“Notes”项目中，双击“NotesPage.xaml.cs”将其打开  。 然后将 `OnAppearing` 方法替换为以下代码：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此代码使用存储在数据库中的任何便笺填充 [`ListView`](xref:Xamarin.Forms.ListView)。

    通过按 Ctrl+S，保存对 NotesPage.xaml.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

10. 在“解决方案资源管理器”中，双击“NoteEntryPage.xaml.cs”将其打开。 然后将 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 替换为以下代码：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` 将 `Note` 实例（表示单个便笺）存储在页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中。 执行 `OnSaveButtonClicked` 事件处理程序时，`Note` 实例会保存到数据库中，并且应用程序会导航回上一页。 执行 `OnDeleteButtonClicked` 事件处理程序时，`Note` 实例会从数据库中删除，并且应用程序会导航回上一页。

      通过按 Ctrl+S，保存对 NoteEntryPage.xaml.cs 所做的更改，然后关闭文件。

11. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage 上，按 + 按钮以导航到 NoteEntryPage 并输入便笺。 保存便笺之后，应用程序会导航回 NotesPage。

    输入一些不同长度的便笺，以观察应用程序行为。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac 并打开“Notes”项目。

2. 在“Solution Pad”中，选择“Notes”项目，然后右键单击并选择“添加”>“添加 NuGet 包…”  ：

    ![添加 NuGet 包](database-images/vsmac/add-nuget-packages.png)    

3. 在“添加包”窗口中，搜索“sqlite-net-pcl”NuGet 包，选择它，然后单击“添加包”按钮将其添加到项目  ：

    ![添加包](database-images/vsmac/add-package.png)

    > [!NOTE]
    > 许多 NuGet 包都有着类似的名称。 正确的包具有以下属性：
    > - **创建者：** Frank A. Krueger
    > - **ID：** sqlite net pcl
    > - **NuGet 链接：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 尽管包的名称如此，此 NuGet 包也可以用于 .NET Standard 项目。

    此包将用于将数据库操作合并到应用程序。

4. 在“Solution Pad”的“Notes”项目中，打开“Models”文件夹中的“Note.cs”，并将现有代码替换为以下代码：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义一个 `Note` 模型，该模型将在应用程序中存储有关每个便笺的数据。 使用 `PrimaryKey` 和 `AutoIncrement` 特性标记 `ID` 属性，以确保 SQLite.NET 数据库中的每个 `Note` 实例均具有 SQLite.NET 提供的唯一 ID。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 Note.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

5. 在“Solution Pad”中，将名为“Data”的新文件夹添加到“Notes”项目。

6. 在“Solution Pad”的“Notes”项目中，将名为“NoteDatabase”的新类添加到“Data”文件夹。

7. 在 NoteDatabase.cs 中，将现有代码替换为以下代码：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此类包含用于创建数据库、从中读取数据、向其写入数据以及从中删除数据的代码。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`NoteDatabase` 构造函数将数据库文件的路径作为参数。 在下一步中，此路径由 `App` 类提供。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NoteDatabase.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

8. 在“Solution Pad”的“Notes”项目中，双击“App.xaml.cs”将其打开  。 然后将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    此代码定义一个 `Database` 属性，该属性以单一实例的形式创建新 `NoteDatabase` 实例，将数据库的文件名作为参数传入 `NoteDatabase` 构造函数。 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

9. 在“Solution Pad”的“Notes”项目中，双击“NotesPage.xaml.cs”将其打开  。 然后将 `OnAppearing` 方法替换为以下代码：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此代码使用存储在数据库中的任何便笺填充 [`ListView`](xref:Xamarin.Forms.ListView)。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NotesPage.xaml.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

10. 在“Solution Pad”中，双击“NoteEntryPage.xaml.cs”将其打开。 然后将 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 替换为以下代码：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` 将 `Note` 实例（表示单个便笺）存储在页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中。 执行 `OnSaveButtonClicked` 事件处理程序时，`Note` 实例会保存到数据库中，并且应用程序会导航回上一页。 执行 `OnDeleteButtonClicked` 事件处理程序时，`Note` 实例会从数据库中删除，并且应用程序会导航回上一页。

      通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NoteEntryPage.xaml.cs 所做的更改，然后关闭文件。

11. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage 上，按 + 按钮以导航到 NoteEntryPage 并输入便笺。 保存便笺之后，应用程序会导航回 NotesPage。

    输入一些不同长度的便笺，以观察应用程序行为。

::: zone-end

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何：

- 使用 NuGet 包管理器将 NuGet 包添加到项目。
- 在本地将数据存储在 SQLite.NET 数据库中。

若要使用 XAML 样式为应用程序设置样式，请继续学习下一个快速入门。

> [!div class="nextstepaction"]
> [下一页](styling.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.Forms 快速入门深入探讨](deepdive.md)