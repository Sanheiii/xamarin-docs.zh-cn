---
title: Xamarin 中的 EventKit
description: 本文档介绍了 EventKit 以及如何在 Xamarin 中使用它。 它讨论了日历、日历事件和提醒，查看了使用 EventKit 进行编程时通常使用的类。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1be6da2bbaf4aeffe00d90945bd06867f929c334
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032554"
---
# <a name="eventkit-in-xamarinios"></a>Xamarin 中的 EventKit

iOS 中内置了两个日历相关的应用程序：日历应用程序和提醒应用程序。 它非常简单，足以了解日历应用程序管理日历数据的方式，但提醒应用程序不太明显。 提醒实际上可能会有与这些日期相关联的日期、完成时间，等等。因此，iOS 会将所有日历数据（无论是日历事件还是提醒）存储在一个位置（称为*日历数据库*）中。

EventKit 框架提供了一种方法，用于访问日历数据库存储的*日历*、*日历事件*和*提醒*数据。 自 iOS 4 开始，对 "日历" 和 "日历" 事件的访问已可用，但对提醒的访问是 iOS 6 中的新增功能。

在本指南中，我们将介绍：

- **EventKit 基础知识**-这将通过主要类引入 EventKit 的基本部分，并提供对其使用的了解。 在处理文档的下一部分之前，此部分是必需的。 
- **常见任务**– "常见任务" 部分旨在作为常见操作（如）的快速参考。枚举日历、创建、保存和检索日历事件和提醒，以及使用内置控制器创建和修改日历事件。 此部分不需要从前台读取，因为它是特定任务的引用。 

本指南中的所有任务都在随附示例应用程序中提供：

 [![](eventkit-images/01.png "The companion sample application screens")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>要求

IOS 4.0 中引入了 EventKit，但在 iOS 6.0 中引入了对提醒数据的访问。 因此，若要执行常规的 EventKit 开发，你需要至少以4.0 版为目标，以及6.0 以获得提醒。

此外，"提醒" 应用程序在模拟器上不可用，这意味着提醒数据也将不可用，除非首先添加它们。 此外，访问请求仅在实际设备上显示给用户。 因此，最好在设备上测试 EventKit 开发。

## <a name="event-kit-basics"></a>事件包基础

使用 EventKit 时，请务必抓住公共类及其用法。 所有这些类都可以在 `EventKit` 和 `EventKitUI` （用于 `EKEventEditController`）中找到。

### <a name="eventstore"></a>EventStore

*EventStore*类是 EventKit 中最重要的类，因为需要在 EventKit 中执行任何操作。 可以将其视为适用于所有 EventKit 数据的持久存储或数据库引擎。 从 `EventStore` 你有权访问日历应用程序中的日历和日历事件，以及提醒应用程序中的提醒。

因为 `EventStore` 类似于数据库引擎，所以它应该是长时间生存期，这意味着在应用程序实例的生存期内应尽可能少地创建并销毁它。 事实上，建议您在应用程序中创建一个 `EventStore` 的实例后，在应用程序的整个生存期内保留该引用，除非您确定不再需要它。 此外，所有调用都应发送到单个 `EventStore` 实例。 出于此原因，建议使用单一实例模式使单个实例可用。

#### <a name="creating-an-event-store"></a>创建事件存储

下面的代码演示了创建 `EventStore` 类的单个实例并使其从应用程序中静态可用的有效方法：

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

上面的代码使用单独模式在应用程序加载时实例化 `EventStore` 的实例。 然后，可以从应用程序中全局访问 `EventStore`，如下所示：

```csharp
App.Current.EventStore;
```

请注意，此处的所有示例都使用此模式，因此它们通过 `App.Current.EventStore`引用 `EventStore`。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>请求访问日历和提醒数据

在允许通过 EventStore 访问任何数据之前，应用程序必须先请求访问日历事件数据或提醒数据，具体取决于所需的数据。 为了便于实现此目标，`EventStore` 公开一个名为 `RequestAccess` 的方法，该方法在调用时将向用户显示一个警报视图，告诉他们应用程序正在请求访问日历数据或提醒数据，具体取决于传递到的 `EKEntityType`以便. 因为它会引发警报视图，所以调用是异步的，并且将调用作为 `NSAction` （或 Lambda）传递给它的完成处理程序，这将接收两个参数;如果已授予访问权限 `NSError`，则为布尔值; 如果不为 null，则将包含请求中的任何错误信息。 例如，以下代码将请求访问日历事件数据，并在未授予请求时显示警报视图。

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

授予请求后，只要在设备上安装了应用程序，就会记住该请求，而不会向用户弹出警报。
但是，只会将访问权限分配给所授予的资源类型。 如果应用程序需要访问两者，则它应同时请求两个。

因为需要记住权限，所以每次发出请求相对便宜，因此最好始终在执行操作之前请求访问。

此外，由于在单独的（非 UI）线程上调用了完成处理程序，因此在完成处理程序中对 UI 的任何更新都应通过 `InvokeOnMainThread`调用，否则将引发异常，如果未捕获，应用程序将崩溃。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 是描述 `EventKit` 项或数据类型的枚举。 它有两个值： `Event` 和提醒。 它用于多种方法，包括 `EventStore.RequestAccess` 告诉 `EventKit` 要获取或检索的数据的类型。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*表示日历，其中包含一组日历事件。 可以在很多不同的位置（例如，在本地，以*iCloud*形式）将日历存储在第三方提供商位置（如*Exchange Server*或*Google*等）中。很多时候 `EKCalendar` 用于告诉 `EventKit` 在何处查找事件或在何处保存事件。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 可以在 `EventKitUI` 命名空间中找到*EKEventEditController* ，它是可用于编辑或创建日历事件的内置控制器。 与内置照相机控制器非常类似，`EKEventEditController` 在显示 UI 和处理保存方面，会为您带来巨大的提升。

### <a name="ekevent"></a>EKEvent

 *EKEvent*表示日历事件。 `EKEvent` 和 `EKReminder` 都从 `EKCalendarItem` 继承，并且具有 `Title`、`Notes`等字段。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*表示提醒项。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一个枚举，用于描述修改可以重复的事件时的事件范围，并有两个值： *ThisEvent*和*FutureEvents*。 `ThisEvent` 意味着任何更改只会发生在正在引用的序列中的特定事件，而 `FutureEvents` 会影响该事件和所有将来的重复周期。

## <a name="tasks"></a>任务

为了便于使用，EventKit 的用法已分解为常见任务，如以下各节所述。

### <a name="enumerate-calendars"></a>枚举日历

若要枚举用户在设备上配置的日历，请对 `EventStore` 调用 `GetCalendars`，并传递希望接收的日历类型（提醒或事件）：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>使用内置控制器添加或修改事件

如果要使用日历应用程序在使用日历应用程序时向用户显示的同一 UI 创建或编辑事件， *EKEventEditViewController*会为你执行大量繁重的操作：

 [![](eventkit-images/02.png "The UI that is presented to the user when using the Calendar Application")](eventkit-images/02.png#lightbox)

若要使用此方法，您需要将其声明为类级别的变量，以便在方法中声明时不会进行垃圾回收：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然后，若要启动该程序，请执行以下操作：实例化它、向其授予对 `EventStore`的引用、向其*EKEventEditViewDelegate*委托，然后使用 `PresentViewController`显示该委托：

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

（可选）若要预先填充事件，可以创建全新的事件（如下所示），也可以检索保存的事件：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

如果确实要预填充 UI，请确保在控制器上设置事件属性：

```csharp
eventController.Event = newEvent;
```

若要使用现有事件，请参阅后面的*按 ID 检索事件*部分。

委托应该重写 `Completed` 方法，该方法在用户使用对话框完成后由控制器调用：

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

（可选）在委托中，可以检查 `Completed` 方法中的*操作*，以修改事件并重新保存，或者执行其他操作（如果已取消）等：

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>以编程方式创建事件

若要在代码中创建事件，请对 `EKEvent` 类使用*FromStore*工厂方法，并在其上设置任意数据：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

必须设置要保存事件的日历，但如果没有首选项，则可以使用默认值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要保存该事件，请对 `EventStore`调用*SaveEvent*方法：

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

保存后，将使用可用于检索事件的唯一标识符更新*EventIdentifier*属性：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是一个字符串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以编程方式创建提醒

在代码中创建提醒与创建日历事件大致相同：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要保存，请对 `EventStore`调用*SaveReminder*方法：

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>按 ID 检索事件

若要按 ID 检索事件，请使用 `EventStore` 的*EventFromIdentifier*方法，并向其传递从该事件中提取的 `EventIdentifier`：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

对于事件，有两个其他标识符属性，但 `EventIdentifier` 是适用于这种情况的唯一属性。

### <a name="retrieving-a-reminder-by-id"></a>按 ID 检索提醒

若要检索提醒，请对 `EventStore` 使用*GetCalendarItem*方法，并将其传递给*CalendarItemIdentifier*：

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

由于 `GetCalendarItem` 返回 `EKCalendarItem`，因此，如果需要访问提醒数据或在以后将该实例用作 `EKReminder`，则必须将其强制转换为 `EKReminder`。

不要将 `GetCalendarItem` 用于日历事件，在撰写本文时，它不起作用。

### <a name="deleting-an-event"></a>删除事件

若要删除日历事件，请在 `EventStore` 上调用*RemoveEvent* ，并传递对该事件的引用以及适当的 `EKSpan`：

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

但请注意，在删除事件之后，将 `null`事件引用。

### <a name="deleting-a-reminder"></a>删除提醒

若要删除提醒，请在 `EventStore` 上调用*RemoveReminder*并传递对该提醒的引用：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

请注意，在上面的代码中，有一个转换为 `EKReminder`，因为 `GetCalendarItem` 用于检索它

### <a name="searching-for-events"></a>搜索事件

要搜索日历事件，必须通过 `EventStore`上的*PredicateForEvents*方法创建*NSPredicate*对象。 `NSPredicate` 是 iOS 用于查找匹配项的查询数据对象：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

创建 `NSPredicate`后，请使用 `EventStore`上的*EventsMatching*方法：

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

请注意，查询是同步的（阻止），可能需要很长时间，具体取决于查询，因此，你可能需要启动一个新的线程或任务来执行此操作。

### <a name="searching-for-reminders"></a>搜索提醒

搜索提醒类似于事件;它需要谓词，但调用已是异步的，因此您无需担心阻塞线程：

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>总结

本文档概述了 EventKit 框架的重要部分和多个最常见的任务。 不过，EventKit 框架非常大且功能强大，其中包括未在此处引入的功能，例如：批处理更新、配置警报、配置事件的重复周期、注册和侦听日历数据库上的更改。设置现成等。  有关详细信息，请参阅 Apple 的[日历和提醒编程指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。

## <a name="related-links"></a>相关链接

- [日历（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)
- [日历和提醒简介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
