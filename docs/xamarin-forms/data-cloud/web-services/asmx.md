---
title: 使用 ASP.NET Web 服务 (ASMX)
description: 本文演示如何从应用程序使用 .ASMX SOAP 服务 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 90f93c2fd17771ccd8bc5dc67404b76cd4ab9c30
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562998"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服务 (ASMX)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_通过使用简单对象访问协议 (SOAP) ，你可以使用该功能生成用于生成 web 服务的 web 服务。SOAP 是一种独立于平台和语言的协议，用于生成和访问 web 服务。对于用于实现服务的平台、对象模型或编程语言，使用该服务的使用者不需要了解有关该服务的任何信息。它们只需要了解如何发送和接收 SOAP 消息。本文演示如何从应用程序使用 .ASMX SOAP 服务 Xamarin.Forms 。_

SOAP 消息是包含以下元素的 XML 文档：

- 一个名为 " *信封* " 的根元素，它将 XML 文档标识为 SOAP 消息。
- 一个可选的 *标头* 元素，其中包含应用程序特定的信息，例如身份验证数据。 如果 *标头* 元素存在，则它必须是 *信封* 元素的第一个子元素。
- 一个必需的 *Body* 元素，其中包含用于接收方的 SOAP 消息。
- 用于指示错误消息的可选 *错误* 元素。 如果出现 *错误* 元素，则它必须是 *Body* 元素的子元素。

SOAP 可以在许多传输协议（包括 HTTP、SMTP、TCP 和 UDP）上运行。 但是，只能通过 HTTP 进行操作。 Xamarin 平台通过 HTTP 支持标准 SOAP 1.1 实现，这包括对许多标准 .ASMX 服务配置的支持。

此示例包括在物理设备或模拟设备上运行的移动应用程序，以及提供用于获取、添加、编辑和删除数据的方法的一个服务。 当移动应用程序运行时，它们将连接到本地托管的 .ASMX 服务，如以下屏幕截图所示：

![示例应用程序](asmx-images/portal.png)

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制在 internet (资源（如应用的后端服务器) 和应用）之间执行安全连接，从而防止意外泄漏敏感信息。 由于默认情况下在为 iOS 9 构建的应用中启用了 ATS，因此所有连接都将受到 ATS 的安全要求。 如果连接不满足这些要求，它们将失败并出现异常。
> 如果无法对 `HTTPS` internet 资源使用协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的 **info.plist** 文件来实现。 有关详细信息，请参阅 [应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>使用 Web 服务

.ASMX 服务提供以下操作：

|操作|说明|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关应用程序中使用的数据模型的详细信息，请参阅 [对数据进行建模](~/xamarin-forms/data-cloud/web-services/introduction.md)。

## <a name="create-the-todoservice-proxy"></a>创建 TodoService 代理

名为的代理类 `TodoService` 扩展 `SoapHttpClientProtocol` 并提供通过 HTTP 与 .asmx 服务进行通信的方法。 通过在 Visual Studio 2019 或 Visual Studio 2017 中添加对每个特定于平台的项目的 web 引用来生成代理。 Web 引用为服务的 Web 服务描述语言 (WSDL) 文档中定义的每个操作生成方法和事件。

例如， `GetTodoItems` 服务操作将导致代理中的 `GetTodoItemsAsync` 方法和 `GetTodoItemsCompleted` 事件。 生成的方法的返回类型为 void，并 `GetTodoItems` 对父类调用该操作 `SoapHttpClientProtocol` 。 当被调用的方法接收到来自服务的响应时，它将触发 `GetTodoItemsCompleted` 事件，并在事件的属性中提供响应数据 `Result` 。

## <a name="create-the-isoapservice-implementation"></a>创建 ISoapService 实现

若要启用共享的跨平台项目以使用该服务，此示例定义了 `ISoapService` 接口，该接口遵循 [c # 中的任务异步编程模型](/dotnet/csharp/programming-guide/concepts/async/)。 每个平台都实现 `ISoapService` 以公开特定于平台的代理。 该示例使用 `TaskCompletionSource` 对象将代理作为任务异步接口公开。 有关使用的详细信息， `TaskCompletionSource` 请参阅以下各节中每种操作类型的实现。

示例 `SoapService` ：

1. 实例化为 `TodoService` 类级别实例
1. 创建一个名为的集合 `Items` ，用于存储 `TodoItem` 对象
1. 指定上的可选属性的自定义终结点 `Url``TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用 `TodoItem` 类来为数据建模。 若要 `TodoItem` 在 web 服务中存储项，必须先将其转换为代理生成的 `TodoItem` 类型。 这是通过方法完成的 `ToASMXServiceTodoItem` ，如下面的代码示例所示：

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
    return new ASMXService.TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

此方法创建一个新的 `ASMService.TodoItem` 实例，并将每个属性设置为实例中的相同属性 `TodoItem` 。

同样，从 web 服务检索数据时，必须将其从代理生成的 `TodoItem` 类型转换为 `TodoItem` 实例。 这是通过方法完成的 `FromASMXServiceTodoItem` ，如下面的代码示例所示：

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
    return new TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

此方法从代理生成的 `TodoItem` 类型中检索数据，并在新创建的实例中设置数据 `TodoItem` 。

### <a name="retrieve-data"></a>检索数据

`ISoapService`接口要求 `RefreshDataAsync` 方法返回 `Task` 具有项集合的。 但是，该 `TodoService.GetTodoItemsAsync` 方法返回 void。 若要满足接口模式，必须调用 `GetTodoItemsAsync` ，等待 `GetTodoItemsCompleted` 事件激发，并填充集合。 这允许您将有效集合返回到 UI。

下面的示例创建一个新的 `TaskCompletionSource` ，在方法中开始异步调用 `RefreshDataAsync` ，并等待 `Task` 提供的 `TaskCompletionSource` 。 在 `TodoService_GetTodoItemsCompleted` 调用事件处理程序时，它将 `Items` 填充集合并更新 `TaskCompletionSource` ：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

有关详细信息，请参阅 [异步编程模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) 和 [TPL 和传统 .NET Framework 异步编程](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>创建或编辑数据

创建或编辑数据时，必须实现 `ISoapService.SaveTodoItemAsync` 方法。 此方法将检测是否 `TodoItem` 为新的或更新的项，并对对象调用适当的方法 `todoService` 。 `CreateTodoItemCompleted` `EditTodoItemCompleted` 还应实现和事件处理程序，以便您知道何时 `todoService` 接收到来自该服务的响应 (这些操作可以合并为单个处理程序，因为它们) 执行相同的操作。 下面的示例演示接口和事件处理程序的实现，以及 `TaskCompletionSource` 用于异步操作的对象：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>删除数据

删除数据需要类似实现。 定义 `TaskCompletionSource` ，实现事件处理程序和 `ISoapService.DeleteTodoItemAsync` 方法：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>测试 Web 服务

使用本地托管的服务测试物理或模拟设备需要就地自定义 IIS 配置、终结点地址和防火墙规则。 有关如何设置环境以进行测试的更多详细信息，请参阅 [配置远程访问 IIS Express](wcf.md#configure-remote-access-to-iis-express)。 测试 WCF 和 .ASMX 之间唯一的区别是 TodoService 的端口号。

## <a name="related-links"></a>相关链接

- [TodoASMX (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](/dotnet/api/system.iasyncresult)