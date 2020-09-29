---
title: Xamarin 和 Xamarin 中的 Visual Basic
description: 本演练演示如何生成使用 Visual Basic.NET 编写的业务逻辑的本机 Xamarin 和 Xamarin Android 应用。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 77df7d986f63861667c67b9c74b88ea6176f42ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458181"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Xamarin Android 和 iOS 中的 Visual Basic

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

[TaskyVB](/samples/xamarin/mobile-samples/visualbasic-taskyvb/)示例应用程序演示如何 Visual Basic 编译到 .NET Standard 库中的代码如何与 Xamarin 一起使用。 下面是在 Android 和 iOS 上运行的结果应用的一些屏幕截图：

 [![Android 和 iOS 运行使用 Visual Basic 构建的应用程序](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

示例中的 Android 和 iOS 项目都是用 c # 编写的。 每个应用程序的用户界面都是使用本机技术构建的，而 `TodoItem` 使用 XML 文件 (Visual Basic .NET Standard 库提供管理，而不是完整数据库) 。

## <a name="sample-walkthrough"></a>示例演练

本指南讨论如何在适用于 iOS 和 Android 的 [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) Xamarin 示例中实现 Visual Basic。

> [!NOTE]
> 继续本指南之前，请查看 [Visual Basic 和 .NET Standard](index.md) 上的说明。
>
> 有关如何使用共享用户界面 Visual Basic 代码构建应用的说明，请参阅[使用 Visual Basic 说明的 Xamarin。](xamarin-forms.md)

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic .NET Standard 库只能在 Windows 上的 Visual Studio 中创建。
示例库包含应用程序在以下 Visual Basic 文件中的基础知识：

- TodoItem
- TodoItemManager
- TodoItemRepositoryXML
- XmlStorage

### <a name="todoitemvb"></a>TodoItem

此类包含要在整个应用程序中使用的业务对象。 它将在 Visual Basic 中定义，并与用 c # 编写的 Android 和 iOS 项目共享。

类定义如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

该示例使用 XML 序列化和反序列化来加载并保存 TodoItem 对象。

### <a name="todoitemmanagervb"></a>TodoItemManager

管理器类提供可移植代码的 "API"。 它为类提供基本的 CRUD 操作 `TodoItem` ，但不实现这些操作。

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

构造函数使用 IXmlStorage 的实例作为参数。 这样，每个平台都可以提供自己的工作实现，同时仍允许可移植代码描述可共享的其他功能。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository

存储库类包含用于管理 TodoItem 对象列表的逻辑。 下面显示了完整的代码-逻辑存在，目的是为了在 TodoItems 中对其进行管理，并从集合中删除一个唯一的 ID 值。

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> 此代码是一个非常基本的数据存储机制的示例。
> 提供它是为了演示 .NET Standard 库如何在此示例中对接口进行代码访问以访问特定于平台的功能 (在这种情况下，加载和保存 XML 文件) 。 它不是一种生产质量的数据库备选方案。

## <a name="android-and-ios-application-projects"></a>Android 和 iOS 应用程序项目

### <a name="ios"></a>iOS

在 iOS 应用程序中，将 `TodoItemManager` `XmlStorageImplementation` 在 **AppDelegate.cs** 文件中创建和，如下面的代码段所示。 前四行只是生成数据将存储到的文件的路径;最后两行显示两个实例化的类。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

在 Android 应用程序中，将 `TodoItemManager` `XmlStorageImplementation` 在 **Application.cs** 文件中创建和，如下面的代码段所示。 前三行只是生成存储数据的文件的路径;最后两行显示两个实例化的类。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

应用程序代码的其余部分主要涉及用户界面，并使用 `TaskMgr` 类加载和保存 `TodoItem` 类。

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 for Mac

> [!WARNING]
> Visual Studio for Mac 不支持编辑 Visual Basic 语言–没有用于创建 Visual Basic 项目或文件的菜单项。 如果打开 **.vb** ，则没有语言语法突出显示、自动完成或 IntelliSense。

Visual Studio 2019 for Mac _可以_ 编译在 Windows 上创建的 visual studio .NET Standard 项目，因此 iOS 应用可以引用这些项目。

Visual Studio 2017 根本 _无法_ 生成 Visual Basic 项目。

## <a name="summary"></a>总结

本文演示了如何使用 Visual Studio 和 .NET Standard 库在 Xamarin 应用程序中使用 Visual Basic 代码。 尽管 Xamarin 不支持直接 Visual Basic，但将 Visual Basic 编译到 .NET Standard 库允许将使用 Visual Basic 编写的代码包含在 iOS 和 Android 应用中。

## <a name="related-links"></a>相关链接

- [TaskyVB ( .NET Standard 示例) ](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [.NET Standard 中的新增功能](/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)