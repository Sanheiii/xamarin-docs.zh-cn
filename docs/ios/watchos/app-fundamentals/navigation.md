---
title: 在 Xamarin 中使用 watchOS 导航
description: 本文档介绍如何在 watchOS 应用程序中使用导航。 它讨论了模式接口、分层导航和基于页面的接口。
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a44d68426ff03ba0b6ab41f57e339caebce62c39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436711"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>在 Xamarin 中使用 watchOS 导航

监视上提供的最简单的导航选项是在当前场景顶部显示的简单 [模式弹出窗口](#modal) 。

对于多场景监视应用，有两个可用的导航范例：

- [分层导航](#Hierarchical_Navigation)
- [基于页面的接口](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>模式接口

使用 `PresentController` 方法以模式方式打开接口控制器。 接口控制器必须已在 **接口**中定义。

```csharp
PresentController ("pageController","some context info");
```

按模式提供的控制器使用整个屏幕 (覆盖) 上一个场景。 默认情况下，标题设置为 " **取消** "，然后点击它将关闭控制器。

若要以编程方式关闭按模式提供的控制器，请调用 `DismissController` 。

```csharp
DismissController();
```

模式屏幕可以是单个场景，也可以使用基于页面的布局。

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>分层导航

呈现可导航回来的场景，就像 `UINavigationController` 在 iOS 上工作一样。 可以按编程方式将场景推送到导航堆栈上，并 (以编程方式或通过用户选择) 弹出。

![可将场景推送到导航堆栈上](navigation-images/hierarchy-1.png) ![可以从导航堆栈中弹出场景](navigation-images/hierarchy-2.png)

与 iOS 一样，左侧的滑动向后导航到分层导航堆栈中的父控制器。

[WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog)和[WatchTables](/samples/xamarin/ios-samples/watchos-watchtables)示例都包含分层导航。

### <a name="pushing-and-popping-in-code"></a>在代码中推送和弹出

观看工具包不需要创建 arching 的 "导航控制器" （如 iOS），只需使用方法推送控制器 `PushController` ，就会自动创建一个导航堆栈。

```csharp
PushController("secondPageController","some context info");
```

该监视屏幕的左上方将有一个 **后退** 按钮，但你也可以使用以编程方式从导航堆栈中删除场景 `PopController` 。

```csharp
PopController();
```

对于 iOS，还可以使用返回到导航堆栈的根目录 `PopToRootController` 。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

可在情节提要中的场景之间创建 segue，以定义分层导航。 为了获取目标场景的上下文，操作系统将调用 `GetContextForSegue` 来初始化新的接口控制器。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"></a>

## <a name="page-based-interfaces"></a>基于页面的接口

基于页面的接口从左到右轻扫，就像 `UIPageViewController` 在 iOS 上工作一样。 指示器点显示在屏幕的底部，用于显示当前显示的页面。

![示例第一页](navigation-images/paged-1.png) ![示例第二页](navigation-images/paged-2.png) ![第五页示例](navigation-images/paged-5.png)

若要使基于页面的接口成为 watch 应用程序的主要 UI，请将 `ReloadRootControllers` 与接口控制器和上下文的数组结合使用：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

你还可以使用 `PresentController` 应用中的一个其他场景提供不是根的基于页面的控制器。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (示例) ](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)