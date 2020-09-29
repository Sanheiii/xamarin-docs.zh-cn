---
title: 使用 JSON 在 Xamarin 中创建用户界面
description: Monotouch.dialog (MT。D) 包括通过 JSON 数据对动态 UI 生成的支持。 在本教程中，我们将演练如何使用 JSONElement 从 JSON 创建用户界面，该用户界面既可以包含在应用程序中，也可以从远程 Url 加载。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c19cf373a89798703224574fe13724dfbade33bb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436681"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>使用 JSON 在 Xamarin 中创建用户界面

_Monotouch.dialog (MT。D) 包括通过 JSON 数据对动态 UI 生成的支持。在本教程中，我们将演练如何使用 JSONElement 从 JSON 创建用户界面，该用户界面既可以包含在应用程序中，也可以从远程 Url 加载。_

隶书.D 支持创建 JSON 中声明的用户界面。 使用 JSON，MT 声明元素。D 将自动为您创建关联的元素。 可以从本地文件、已分析的 `JsonObject` 实例甚至远程 Url 加载 JSON。

隶书.D 支持在使用 JSON 时元素 API 中可用的各种功能。 例如，以下屏幕截图中的应用程序使用 JSON 完全声明：

[ ![ 例如，使用 json 完全声明了此屏幕截图中的应用程序](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ，例如，此屏幕截图中的应用程序使用 json 完全声明](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox)

让我们从 [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) 教程中重新访问该示例，演示如何使用 JSON 添加任务详细信息屏幕。

## <a name="setting-up-mtd"></a>设置 MT。2-d

隶书.D 与 Xamarin 一起分发。 若要使用此方法，请右键单击 Visual Studio 2017 或 Visual Studio for Mac 中的 Xamarin iOS 项目的 " **引用** " 节点，然后添加对 **monotouch.dialog** 程序集的引用。 然后， `using MonoTouch.Dialog` 根据需要在源代码中添加语句。

## <a name="json-walkthrough"></a>JSON 演练

本演练的示例允许创建任务。 在第一个屏幕上选择任务时，将显示详细信息屏幕，如下所示：

 [![在第一个屏幕上选择任务时，将显示详细信息屏幕，如图所示](json-element-walkthrough-images/03-task-list.png)](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>创建 JSON

在此示例中，我们将从名为的项目中的文件加载 JSON `task.json` 。 隶书.D 要求 JSON 符合镜像元素 API 的语法。 与从代码使用元素 API 一样，使用 JSON 时，我们会在添加元素的部分中声明部分。 若要在 JSON 中声明节和元素，请分别使用字符串 "sections" 和 "元素" 作为键。 对于每个元素，将使用键设置关联的元素类型 `type` 。 每个其他元素属性都设置为属性名称作为键。

例如，下面的 JSON 描述了任务详细信息的部分和元素：

```json
{
    "title": "Task",
    "sections": [
        {
            "elements" : [
                {
                    "id" : "task-description",
                    "type": "entry",
                    "placeholder": "Enter task description"
                },
                {
                    "id" : "task-duedate",
                    "type": "date",
                    "caption": "Due Date",
                    "value": "00:00"
                }
            ]
        }
    ]
}
```

请注意，上面的 JSON 包含每个元素的 id。 任何元素都可以包含 id，以便在运行时引用它。 我们将介绍如何在代码中加载 JSON，稍后如何使用此方法。

## <a name="loading-the-json-in-code"></a>在代码中加载 JSON

定义 JSON 后，需要将其加载到 MT。D 使用 `JsonElement` 类。 假设在上面创建的 JSON 文件已添加到项目中，其名称为 sample.js，并给定内容的生成操作， `JsonElement` 则加载非常简单于调用以下代码行：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

由于我们将在每次创建任务时按需添加此项，因此，我们可以修改从前面的元素 API 示例中单击的按钮，如下所示：

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>在运行时访问元素

请记住，当我们在 JSON 文件中声明了两个元素时，我们向其中添加了一个 id。 我们可以使用 id 属性在运行时访问每个元素，以便在代码中修改它们的属性。 例如，以下代码引用 entry 和 date 元素，以从 task 对象设置值：

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    taskElement.Caption = task.Name;

    var description = taskElement ["task-description"] as EntryElement;

    if (description != null) {
        description.Caption = task.Name;
        description.Value = task.Description;       
    }

    var duedate = taskElement ["task-duedate"] as DateElement;

    if (duedate != null) {                
        duedate.DateValue = task.DueDate;
    }
    _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>从 url 加载 JSON

隶书.D 还支持通过将 Url 直接传递到的构造函数，从外部 Url 动态地加载 JSON `JsonElement` 。 隶书.当你在屏幕之间导航时，D 将按需扩展 JSON 中声明的层次结构。 例如，假设下面的一个 JSON 文件位于本地 web 服务器的根目录下：

```json
{
    "type": "root",
    "title": "home",
    "sections": [
        {
            "header": "Nested view!",
            "elements": [
                {
                    "type": "boolean",
                    "caption": "Just a boolean",
                    "id": "first-boolean",
                    "value": false
                },
                {
                    "type": "string",
                    "caption": "Welcome to the nested controller"
                }
            ]
        }
    ]
}
```

我们可以使用来加载此， `JsonElement` 如以下代码所示：

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

在运行时，MT 将检索并分析文件。D 当用户导航到第二个视图时，如以下屏幕截图中所示：

 [![此文件将由 MT 检索和分析。D 当用户导航到第二个视图时](json-element-walkthrough-images/04-json-web-example.png)](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>总结

本文介绍如何使用 MT 创建接口。D 从 JSON。 其中介绍了如何使用应用程序以及从远程 Url 加载文件中包含的 JSON。 它还演示了如何在运行时访问 JSON 中所述的元素。

## <a name="related-links"></a>相关链接

- [MTDJsonDemo (示例) ](/samples/xamarin/ios-samples/mtdjsondemo)
- [Monotouch.dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Github 上的 Monotouch.dialog 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)