---
title: Xamarin.Forms Web 服务简介
description: 本指南提供了 Xamarin.Forms 演示如何与不同 web 服务通信的示例应用程序的演练。 虽然每个 web 服务使用单独的示例应用程序，但它们在功能上类似并共享公共类。
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ebff259519776718f41ab0fcc0a7abb0c692dac6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563414"
---
# <a name="no-locxamarinforms-web-services-introduction"></a>Xamarin.Forms Web 服务简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_本主题提供了 Xamarin.Forms 演示如何与不同 web 服务通信的示例应用程序的演练。虽然每个 web 服务使用单独的示例应用程序，但它们在功能上类似并共享公共类。_

下面介绍的示例待办事项列表应用程序用于演示如何使用访问不同类型的 web 服务后端 Xamarin.Forms 。 它提供以下功能：

- 查看任务的列表。
- 添加、编辑和删除任务。
- 将任务状态设置为 "已完成"。
- 说出任务的 "名称" 和 "备注" 字段。

在所有情况下，任务都存储在通过 web 服务访问的后端中。

当应用程序启动时，将显示一个页面，其中列出了从 web 服务检索到的所有任务，并允许用户创建新任务。 单击某任务会将该应用程序导航到第二页，可在其中编辑、保存、删除和朗读该任务。 最终的应用程序如下所示：

![Todo 应用程序-第一页 ](introduction-images/app-example-1.png)
 ![ todo 应用程序-第二页](introduction-images/app-example-2.png)

本指南中的每个主题都提供一个下载链接，该链接指向演示特定类型的 web 服务后端的应用程序的 *不同* 版本。 下载与每个 web 服务样式相关的页面上的相关示例代码。

## <a name="understand-the-application-anatomy"></a>了解应用程序解析

每个示例应用程序的共享代码项目由三个主要文件夹组成：

|Folder|目的|
|--- |--- |
|数据|包含用于管理数据项以及与 web 服务进行通信的类和接口。 其中至少包括 `TodoItemManager` 类，该类通过类中的属性公开， `App` 用于调用 web 服务操作。|
|模型|包含应用程序的数据模型类。 其中至少包括 `TodoItem` 类，该类对应用程序使用的单个数据项建模。 此文件夹还可以包含用于对用户数据建模的任何其他类。|
|视图|包含应用程序的页面。 这通常由 `TodoListPage` 和类以及 `TodoItemPage` 用于身份验证的任何其他类组成。|

每个应用程序的共享代码项目还包含若干重要文件：

|文件|用途|
|--- |--- |
|Constants.cs|`Constants`类，它指定应用程序用来与 web 服务进行通信的任何常量。 这些常量要求更新以访问在提供程序上创建的个人后端服务。|
|ITextToSpeech.cs|`ITextToSpeech`接口，该接口指定 `Speak` 方法必须由任何实现类提供。|
|Todo.cs|`App`一个类，该类负责实例化将由应用程序在每个平台上显示的第一页，以及 `TodoItemManager` 用于调用 web 服务操作的类。|

### <a name="view-pages"></a>查看页面

大多数示例应用程序包含至少两个页面：

- **TodoListPage** –此页显示 `TodoItem` 实例列表，如果属性为，则显示勾选标记图标 `TodoItem.Done` `true` 。 单击某项会导航到 `TodoItemPage` 。 此外，还可以通过单击符号来创建新项 *+* 。
- **TodoItemPage** –此页显示所选的详细信息 `TodoItem` ，并允许对其进行编辑、保存、删除和朗读。

此外，某些示例应用程序还包含用于管理用户身份验证过程的其他页面。

### <a name="model-the-data"></a>数据建模

每个示例应用程序都使用 `TodoItem` 类对显示的数据进行建模并发送到用于存储的 web 服务。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

`ID`属性用于唯一标识每个 `TodoItem` 实例，每个 web 服务使用该属性来标识要更新或删除的数据。

### <a name="invoke-web-service-operations"></a>调用 web 服务操作

Web 服务操作通过 `TodoItemManager` 类访问，类的实例可以通过属性进行访问 `App.TodoManager` 。 `TodoItemManager`类提供以下方法来调用 web 服务操作：

- **GetTasksAsync** –此方法用于使用 `ListView` `TodoListPage` `TodoItem` 从 web 服务检索的实例填充上的控件。
- **SaveTaskAsync** –此方法用于创建或更新 `TodoItem` web 服务上的实例。
- **DeleteTaskAsync** –此方法用于删除 `TodoItem` web 服务上的实例。

此外，某些示例应用程序包含类中的其他方法 `TodoItemManager` ，这些方法用于管理用户身份验证过程。

`TodoItemManager`方法对注入构造函数的依赖类调用方法，而不是直接调用 web 服务操作 `TodoItemManager` 。 例如，一个示例应用程序将 `RestService` 类注入 `TodoItemManager` 构造函数，以提供使用 REST api 访问数据的实现。

## <a name="related-links"></a>相关链接

- [.ASMX (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todorest)