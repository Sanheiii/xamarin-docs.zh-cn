---
title: Xamarin. Forms Web 服务简介
description: 本指南提供了一个示例，演示如何与不同的 web 服务通信。 尽管每个 web 服务使用单独的示例应用程序，但它们是功能上类似，并且共享公共类。
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68656644"
---
# <a name="xamarinforms-web-services-introduction"></a>Xamarin. Forms Web 服务简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_本主题提供的演练演示如何与另一个 web 服务进行通信的 Xamarin.Forms 示例应用程序。尽管每个 web 服务使用单独的示例应用程序，但它们是功能上类似，并且共享公共类。_

如下所述的示例待办事项列表应用程序用于演示如何访问不同类型的 web 服务后端使用 Xamarin.Forms。 它提供的功能：

- 查看任务的列表。
- 添加、 编辑和删除任务。
- 设置任务的状态设置为完成。
- 任务的名称和说明字段进行朗读。

在所有情况下，这些任务存储在通过 web 服务访问后端。

启动应用程序时，会显示页面，其中列出了从 web 服务检索的任何任务，并允许用户创建新的任务。 单击任务导航到第二个页面，其中该任务可以编辑、 保存、 删除和所说的应用程序。 最终的应用程序如下所示：

![](introduction-images/app-example-1.png "待办事项应用程序的第一页")
![](introduction-images/app-example-2.png "Todo 应用程序的第二页")

本指南中的每个主题提供的下载链接*不同*演示特定类型的 web 服务后端应用程序的版本。 下载与每个 web 服务样式相关的页面上的相关示例代码。

## <a name="understand-the-application-anatomy"></a>了解应用程序解析

每个示例应用程序的共享代码项目由三个主要文件夹组成：

|文件夹|目标|
|--- |--- |
|数据|包含的类和接口用于管理数据的项，并与 web 服务通信。 至少，这包括`TodoItemManager`类，该类通过中的属性公开`App`类来调用 web 服务操作。|
|Models|包含应用程序的数据模型类。 至少，这包括`TodoItem`类，该类使用的应用程序数据的单个项。 该文件夹还可以包含用于对用户数据的其他任何类。|
|Views|包含应用程序页。 这通常包括`TodoListPage`和`TodoItemPage`类和其他用于进行身份验证的任何类。|

每个应用程序的共享代码项目还包含若干重要文件：

|文件|目标|
|--- |--- |
|Constants.cs|`Constants`指定应用程序使用与 web 服务进行通信的任何常量的类。 这些常量需要更新，以访问您的个人的后端服务提供程序上创建。|
|ITextToSpeech.cs|`ITextToSpeech`接口，它可以指定`Speak`方法必须由任何实现类提供。|
|Todo.cs|`App`负责实例化的这两个将显示的每个平台上的应用程序的第一页的类和`TodoItemManager`类，用于调用 web 服务操作。|

### <a name="view-pages"></a>查看页面

大多数示例应用程序包含至少两个页面：

- **TodoListPage** – 此页显示的列表`TodoItem`实例，并对勾图标如果`TodoItem.Done`属性是`true`。 单击的项目导航到`TodoItemPage`。 此外，通过单击创建新项 *+* 符号。
- **TodoItemPage** – 此页显示所选的详细信息`TodoItem`，并允许编辑、 保存、 删除和解说。

此外，某些示例应用程序包含用于管理用户身份验证过程的其他页。

### <a name="model-the-data"></a>为数据建模

每个示例应用程序使用`TodoItem`类显示并发送到的 web 服务进行存储的数据建模。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

`ID`属性用于唯一地标识每个`TodoItem`实例，并且每个 web 服务的情况下用于标识要更新或删除数据。

### <a name="invoke-web-service-operations"></a>调用 web 服务操作

通过访问 web 服务操作`TodoItemManager`类和类的实例可以通过访问`App.TodoManager`属性。 `TodoItemManager`类提供了以下方法来调用 web 服务操作：

- **GetTasksAsync** – 使用此方法来填充`ListView`控制上`TodoListPage`与`TodoItem`从 web 服务检索实例。
- **SaveTaskAsync** – 此方法用于创建或更新`TodoItem`web 服务上的实例。
- **DeleteTaskAsync** – 此方法用于删除`TodoItem`web 服务上的实例。

此外，某些示例应用程序包含中的其他方法`TodoItemManager`类，用于管理用户身份验证过程。

而不是直接调用 web 服务操作`TodoItemManager`注入到依赖类的方法调用的方法`TodoItemManager`构造函数。 例如，一个示例应用程序注入`RestService`类到`TodoItemManager`构造函数，以提供使用 REST Api 访问数据的实现。

## <a name="related-links"></a>相关链接

- [.ASMX （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
