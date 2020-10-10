---
title: 在多页 Xamarin.Forms 应用程序中执行导航
description: 本文说明如何将存储单个便笺的单页应用程序转换为能够存储多个便笺的多页应用程序。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 06cd68c56192664f6d11ec9f6adbe30f9ae5168c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433471"
---
# <a name="perform-navigation-in-a-multi-page-no-locxamarinforms-application"></a>在多页 Xamarin.Forms 应用程序中执行导航

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

在本快速入门中，你将了解如何：

- 向 Xamarin.Forms 解决方案添加其他页面。
- 在页面之间执行导航。
- 使用数据绑定在用户界面元素与其数据源之间同步数据。

本快速入门演练如何将能够存储单个便笺的单页跨平台 Xamarin.Forms 应用程序转换为能够存储多个便笺的多页应用程序。 最终的应用程序如下所示：

[![便笺页面](multi-page-images/screenshots1-sml.png)](multi-page-images/screenshots1.png#lightbox "便笺页面")
[![便笺输入页面](multi-page-images/screenshots2-sml.png)](multi-page-images/screenshots2.png#lightbox "便笺输入页面")

### <a name="prerequisites"></a>先决条件

在尝试本快速入门之前，应成功完成[上一个快速入门](single-page.md)。 或者，下载[上一个快速入门示例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)并将它用作本快速入门的起点。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio。 在开始窗口中，单击最近使用的项目/解决方案列表中的“Notes”解决方案，或单击“打开项目或解决方案”，然后在“打开项目/解决方案”对话框中，选择“Notes”项目的解决方案文件：

    ![打开项目](multi-page-images/vs/open-solution.png)

2. 在“解决方案资源管理器”中，右键单击“Notes”项目，并依次选择“添加”>“新文件夹”  ：

    ![添加新项](multi-page-images/vs/add-new-item.png)

3. 在“解决方案资源管理器”中，将新文件夹命名为“Models”：

    ![Models 文件夹](multi-page-images/vs/name-folder.png)

4. 在“解决方案资源管理器”中选择“Models”文件夹，右键单击，然后选择“添加”>“新建项...”  ：

    ![添加新文件](multi-page-images/vs/add-new-models-file.png)

5. 在“添加新项”对话框中，选择“Visual C# 项”>“类”，将新文件命名为“Note”，然后单击“添加”按钮：

    ![添加 Note 类](multi-page-images/vs/add-note-class.png)

    这会将名为 Note 的类添加到“Notes”项目的“Models”文件夹中。

6. 在 Note.cs 中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义一个 `Note` 模型，该模型将在应用程序中存储有关每个便笺的数据。    

    通过按 Ctrl+S，保存对 Note.cs 所做的更改，然后关闭文件。

7. 在“解决方案资源管理器”中，右键单击“Notes”项目，并依次选择“添加”>“新建项...”  。在“添加新项”对话框中，选择“Visual C# 项”>“Xamarin.Forms”>“内容页”，将新文件命名为“NoteEntryPage”，然后单击“添加”按钮   ：

    ![添加 Xamarin.FormsContentPage](multi-page-images/vs/add-note-entry-page.png)

    这会将名为 NoteEntryPage 的新页添加到项目的根文件夹中。 此页会是应用程序中的第二页。

8. 在 NoteEntryPage.xaml 中，删除所有模板代码并将其替换为以下代码：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      该代码以声明的方式定义页面的用户界面，它包含一个用于文本输入的[`Editor`](xref:Xamarin.Forms.Editor)，以及两个指示应用程序保存或删除文件的 [`Button`](xref:Xamarin.Forms.Button) 实例。 这两个 `Button` 实例水平放置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Editor` 和 `Grid` 垂直放置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。 此外，`Editor` 使用数据绑定来绑定到 `Note` 模型的 `Text` 属性。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[数据绑定](deepdive.md#data-binding)。

      通过按 Ctrl+S，保存对 NoteEntryPage.xaml 所做的更改，然后关闭文件。

9. 在 NoteEntryPage.xaml.cs 中，删除所有模板代码并将其替换为以下代码：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此代码将 `Note` 实例（表示单个便笺）存储在页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中。 按“保存”[`Button`](xref:Xamarin.Forms.Button) 时，会执行 `OnSaveButtonClicked` 事件处理程序，它会将 `Editor` 的内容保存到具有随机生成的文件名的新文件，或者保存到现有文件（如果正在更新便笺）。 在这两种情况下，文件都存储在应用程序的本地应用程序数据文件夹中。 然后该方法会导航回上一页。 按“删除”`Button` 时，会执行 `OnDeleteButtonClicked` 事件处理程序，它会删除文件（前提是它存在）并导航回上一页。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

      通过按 Ctrl+S，保存对 NoteEntryPage.xaml.cs 所做的更改，然后关闭文件。

      > [!WARNING]
      > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

10. 在“解决方案资源管理器”中，右键单击“Notes”项目，并依次选择“添加”>“新建项...”  。在“添加新项”对话框中，选择“Visual C# 项”>“Xamarin.Forms”>“内容页”，将新文件命名为“NotesPage”，然后单击“添加”按钮   。

      这会将名为 NotesPage 的页面添加到项目的根文件夹中。 此页面会是应用程序的根页面。

11. 在 NotesPage.xaml 中，删除所有模板代码并将其替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 组成。 `ListView` 使用数据绑定来显示应用程序检索到的任何便笺，选择便笺会导航到 `NoteEntryPage`，可以在其中修改便笺。 或者，可以通过按 `ToolbarItem` 来创建新便笺。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[数据绑定](deepdive.md#data-binding)。

    通过按 Ctrl+S，保存对 NotesPage.xaml 所做的更改，然后关闭文件。

12. 在 NotesPage.xaml.cs 中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此代码定义 `NotesPage` 的功能。 显示该页面时，会执行 `OnAppearing` 方法，该方法使用从本地应用程序数据文件夹中检索到的任何便笺填充 [`ListView`](xref:Xamarin.Forms.ListView)。 按 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 时，会执行 `OnNoteAddedClicked` 事件处理程序。 此方法导航到 `NoteEntryPage`，将 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为新 `Note` 实例。 选择 `ListView` 中的项时，会执行 `OnListViewItemSelected` 事件处理程序。 此方法导航到 `NoteEntryPage`，将 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为所选 `Note` 实例。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

    通过按 Ctrl+S，保存对 NotesPage.xaml.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

13. 在“解决方案资源管理器”中，双击“App.xaml.cs”将其打开。 然后将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    此代码为 `System.IO` 命名空间添加命名空间声明，并为类型为 `string` 的静态 `FolderPath` 属性添加声明。 `FolderPath` 属性用于存储在设备上存储便笺数据的路径。 此外，该代码会在 `App` 构造函数中初始化 `FolderPath` 属性，并将 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性初始化为承载 `NotesPage` 实例的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在“解决方案资源管理器”的“Notes”项目中，右键单击“MainPage.xaml”，然后选择“删除”   。 在出现的对话框中，按“确定”按钮，以从硬盘中删除该文件。

    这会删除不再使用的页面。

15. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage 上，按 + 按钮以导航到 NoteEntryPage 并输入便笺。 保存便笺之后，应用程序会导航回 NotesPage。

    输入一些不同长度的便笺，以观察应用程序行为。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac。 在开始窗口单击“打开”，然后在对话框中选择“Notes”项目的解决方案文件：

    ![打开解决方案](multi-page-images/vsmac/open-solution.png)

2. 在“Solution Pad”中，选择“Notes”项目，然后右键单击并选择“添加”>“新建文件夹”  ：

    ![添加新文件夹](multi-page-images/vsmac/add-new-folder.png)

3. 在“Solution Pad”中，将新文件夹命名为“Models”：

    ![Models 文件夹](multi-page-images/vsmac/name-folder.png)

4. 在“Solution Pad”中选择“Models”文件夹，右键单击，然后选择“添加”>“新建文件...”  ：

    ![添加新文件](multi-page-images/vsmac/add-new-models-file.png)

5. 在“新建文件”对话框中，选择“常规”>“空类”，将新文件命名为“Note”，然后单击“新建”按钮：

    ![添加 Note 类](multi-page-images/vsmac/add-note-class.png)

    这会将名为 Note 的类添加到“Notes”项目的“Models”文件夹中。

6. 在 Note.cs 中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义一个 `Note` 模型，该模型将在应用程序中存储有关每个便笺的数据。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 Note.cs 所做的更改，然后关闭文件。

7. 在“Solution Pad”中，选择“Notes”项目，然后右键单击并选择“添加”>“新建文件...”  。在“新建文件”对话框中，选择“窗体”>“窗体 ContentPage Xaml”，将新文件命名为“NoteEntryPage”，然后单击“新建”按钮：

    ![添加 Xamarin.FormsContentPage](multi-page-images/vsmac/add-note-entry-page.png)

    这会将名为 NoteEntryPage 的新页添加到项目的根文件夹中。 此页会是应用程序中的第二页。

8. 在 NoteEntryPage.xaml 中，删除所有模板代码并将其替换为以下代码：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      该代码以声明的方式定义页面的用户界面，它包含一个用于文本输入的[`Editor`](xref:Xamarin.Forms.Editor)，以及两个指示应用程序保存或删除文件的 [`Button`](xref:Xamarin.Forms.Button) 实例。 这两个 `Button` 实例水平放置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Editor` 和 `Grid` 垂直放置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。 此外，`Editor` 使用数据绑定来绑定到 `Note` 模型的 `Text` 属性。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[数据绑定](deepdive.md#data-binding)。

      通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NoteEntryPage.xaml 所做的更改，然后关闭文件。

9. 在 NoteEntryPage.xaml.cs 中，删除所有模板代码并将其替换为以下代码：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此代码将 `Note` 实例（表示单个便笺）存储在页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中。 按“保存”[`Button`](xref:Xamarin.Forms.Button) 时，会执行 `OnSaveButtonClicked` 事件处理程序，它会将 `Editor` 的内容保存到具有随机生成的文件名的新文件，或者保存到现有文件（如果正在更新便笺）。 在这两种情况下，文件都存储在应用程序的本地应用程序数据文件夹中。 然后该方法会导航回上一页。 按“删除”`Button` 时，会执行 `OnDeleteButtonClicked` 事件处理程序，它会删除文件（前提是它存在）并导航回上一页。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

      通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NoteEntryPage.xaml.cs 所做的更改，然后关闭文件。

      > [!WARNING]
      > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

10. 在“Solution Pad”中，选择“Notes”项目，然后右键单击并选择“添加”>“新建文件...”  。在“新建文件”对话框中，选择“窗体”>“窗体 ContentPage Xaml”，将新文件命名为“NotesPage”，然后单击“新建”按钮。

      这会将名为 NotesPage 的页面添加到项目的根文件夹中。 此页面会是应用程序的根页面。

11. 在 NotesPage.xaml 中，删除所有模板代码并将其替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 组成。 `ListView` 使用数据绑定来显示应用程序检索到的任何便笺，选择便笺会导航到 `NoteEntryPage`，可以在其中修改便笺。 或者，可以通过按 `ToolbarItem` 来创建新便笺。 有关数据绑定的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[数据绑定](deepdive.md#data-binding)。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NotesPage.xaml 所做的更改，然后关闭文件。

12. 在 NotesPage.xaml.cs 中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此代码定义 `NotesPage` 的功能。 显示该页面时，会执行 `OnAppearing` 方法，该方法使用从本地应用程序数据文件夹中检索到的任何便笺填充 [`ListView`](xref:Xamarin.Forms.ListView)。 按 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 时，会执行 `OnNoteAddedClicked` 事件处理程序。 此方法导航到 `NoteEntryPage`，将 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为新 `Note` 实例。 选择 `ListView` 中的项时，会执行 `OnListViewItemSelected` 事件处理程序。 此方法导航到 `NoteEntryPage`，将 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为所选 `Note` 实例。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

    通过选择“文件”>“保存”，或按 &#8984; + S，保存对 NotesPage.xaml.cs 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致要在后续步骤中修复的错误。

13. 在“Solution Pad”中，双击“App.xaml.cs”将其打开。 然后将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    此代码为 `System.IO` 命名空间添加命名空间声明，并为类型为 `string` 的静态 `FolderPath` 属性添加声明。 `FolderPath` 属性用于存储在设备上存储便笺数据的路径。 此外，该代码会在 `App` 构造函数中初始化 `FolderPath` 属性，并将 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性初始化为承载 `NotesPage` 实例的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。 有关导航的详细信息，请参阅 [Xamarin.Forms 快速入门深入探讨](deepdive.md)中的[导航](deepdive.md#navigation)。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在“Solution Pad”的“Notes”项目中，右键单击“MainPage.xaml”，然后选择“删除”   。 在出现的对话框中，按“删除”按钮，以从硬盘中删除该文件。

    这会删除不再使用的页面。

15. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    在 NotesPage 上，按 + 按钮以导航到 NoteEntryPage 并输入便笺。 保存便笺之后，应用程序会导航回 NotesPage。

    输入一些不同长度的便笺，以观察应用程序行为。

::: zone-end

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何：

- 向 Xamarin.Forms 解决方案添加其他页面。
- 在页面之间执行导航。
- 使用数据绑定在用户界面元素与其数据源之间同步数据。

若要修改应用程序以便它将其数据存储在本地 SQLite.NET 数据库中，请继续学习下一个快速入门。

> [!div class="nextstepaction"]
> [下一页](database.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin.Forms 快速入门深入探讨](deepdive.md)