---
title: Xamarin 中的后台传输和 NSURLSession
description: 本文档提供的演练演示如何使用后台传输和 NSUrlSession 来开始下载大图像，并在应用置于后台时继续下载。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/02/2020
ms.openlocfilehash: b95b78ed36fcec122006cab7ce9663c43f9b3096
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436665"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Xamarin 中的后台传输和 NSURLSession

通过配置后台 `NSURLSession` 和排队上传或下载任务来启动后台传输。 如果任务在应用程序被 backgrounded、挂起或终止时完成，则 iOS 会通过调用应用程序的 *AppDelegate*中的完成处理程序来通知应用程序。 下图演示了此操作：

 [![通过配置后台 NSURLSession 和排队上传或下载任务来启动后台传输](background-transfer-walkthrough-images/transfer.png)](background-transfer-walkthrough-images/transfer.png#lightbox)

## <a name="configuring-a-background-session"></a>配置后台会话

若要创建后台会话，请使用对象创建一个新的 `NSUrlSession` 并对其进行配置 `NSUrlSessionConfiguration` 。

配置对象确定会话可以执行的操作以及可运行的任务类型。
使用方法配置的会话 `CreateBackgroundSessionConfiguration` 将在单独的进程中运行，并执行随机 (WiFi) 传输以保留数据和电池寿命。
下面的代码示例演示了如何使用 `CreateBackgroundSessionConfiguration` 方法和唯一的字符串标识符正确设置后台传输会话：

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, new MySessionDelegate(), new NSOperationQueue());

}
```

除了配置对象之外，会话还需要会话委托和队列。
队列确定任务完成的顺序。 会话委托 chaperones 传输过程，并处理身份验证、缓存和其他与会话相关的问题。

## <a name="working-with-tasks-and-delegates"></a>使用任务和委托

现在我们已配置了后台会话，接下来让我们开始处理此传输的任务。 我们可以使用称为会话委托的实例来跟踪这些任务 `NSUrlSessionDelegate` 。 会话委托负责在后台唤醒终止或挂起的应用程序，以处理身份验证、错误或传输完成。

`NSUrlSessionDelegate`提供以下用于检查传输状态的基本方法：

- *DidFinishEventsForBackgroundSession* -当所有任务都已完成并且传输完成时，将调用此方法。
- *DidReceiveChallenge* -在需要授权时调用以请求凭据。
- *DidBecomeInvalidWithError* -如果变为无效，则调用  `NSURLSession` 。

后台会话需要更多的专用委托，具体取决于正在运行的任务的类型。 后台会话仅限两种类型的任务：

- *上传任务* -类型的任务  `NSUrlSessionUploadTask` 使用 `INSUrlSessionTaskDelegate` 实现的接口 `INSUrlSessionDelegate` 。 这提供了用于跟踪上传进度、处理 HTTP 重定向等的其他方法。
- *下载任务* -类型的任务  `NSUrlSessionDownloadTask` 使用 `INSUrlSessionDownloadDelegate` 实现和的接口 `INSUrlSessionDelegate` `INSUrlSessionTaskDelegate` 。 这提供了用于上传任务的所有方法，以及用于跟踪下载进度并确定下载任务恢复或完成时间的下载特定方法。

下面的代码定义可用于从 URL 下载图像的任务。 该任务通过 `CreateDownloadTask` 在后台会话上调用并传入 URL 请求来启动：

```csharp
const string DownloadURLString = "http://xamarin.com/images/xamarin.png"; // or other hosted file
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接下来，创建新的会话下载委托，以跟踪此会话中的所有下载任务。 委托类应从继承 `NSObject` ，并实现所需的接口：

```csharp
public class MySessionDelegate : NSObject, INSUrlSessionDownloadDelegate
{
  public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

若要了解下载任务的进度，请重写 `DidWriteData` 方法以跟踪进度，甚至更新 UI。 如果应用程序处于前台，则 UI 更新将立即显示，或在用户下次打开应用程序时等待用户。

会话委托 API 提供了用于与任务交互的广泛工具包。 有关会话委托方法的完整列表，请参阅 `NSUrlSessionDelegate` API 文档。

> [!IMPORTANT]
> 后台会话是在后台线程上启动的，因此，对更新 UI 的任何调用都必须通过调用来在 UI 线程上显式运行， `InvokeOnMainThread` 以避免 iOS 终止应用。 

## <a name="handling-transfer-completion"></a>处理传输完成

最后一步是让应用程序知道与会话相关的所有任务都已完成，并处理新内容。

在中 `AppDelegate` ，订阅 `HandleEventsForBackgroundUrl` 事件。 当应用程序进入后台并且传输会话正在运行时，将调用此方法，系统会向我们传递一个完成处理程序：

```csharp
public System.Action backgroundSessionCompletionHandler;

public void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

使用完成处理程序，让 iOS 知道应用程序处理完成的时间。

请记住，会话可能会生成多个任务来处理传输。 当最后一个任务完成时，暂停或终止的应用程序将重新启动到后台。 然后，应用程序 `NSURLSession` 使用唯一会话标识符重新连接到，并调用 `DidFinishEventsForBackgroundSession` 会话委托。 此方法是应用程序处理新内容的机会，包括更新 UI，以反映传输的结果：

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

完成对新内容的处理后，调用完成处理程序，让系统知道可以安全地获取应用程序的快照并返回睡眠：

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

本演练介绍了在 iOS 7 和更高版本中实现后台传输服务所要执行的基本步骤。

## <a name="related-links"></a>相关链接

- [简单的后台传输 (示例) ](/samples/xamarin/ios-samples/simplebackgroundtransfer)