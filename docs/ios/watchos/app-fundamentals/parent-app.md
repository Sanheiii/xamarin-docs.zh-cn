---
title: 在 Xamarin 中使用 watchOS 父应用程序
description: 本文档介绍如何在 Xamarin 中使用 watchOS 父应用程序。 它讨论了 watchOS 应用扩展、iOS 应用、共享存储等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 002c57a1549201018cb2068f000a038f686eb2c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436734"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>在 Xamarin 中使用 watchOS 父应用程序

可以通过不同的方式在监视应用和它所捆绑到的 iOS 应用之间进行通信：

- Watch 应用可以在 iPhone 上的父应用上 [运行代码](#run-code) 。

- 监视扩展可以与父 iPhone 应用 [共享存储位置](#shared-storage) 。

- 使用移交将数据从通知传递到监视应用，并将用户发送到应用中的特定界面控制器。

父应用程序有时也称为容器应用。

## <a name="run-code"></a>运行代码

这两个示例演示了如何使用 `WCSession` 运行代码，并在手表应用和配对的 iPhone 之间发送消息：

- [观看连接](/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>共享存储

如果配置 [应用组](~/ios/watchos/app-fundamentals/app-groups.md) ，则 iOS 8 扩展 (包括监视扩展) 可以与父应用共享数据。

### <a name="nsuserdefaults"></a>使用 nsuserdefaults

可以在 "监视应用扩展" 和 "父 iPhone" 应用中编写以下代码，以便它们可以引用一组常见的 `NSUserDefaults` ：

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files"></a>

### <a name="files"></a>文件

IOS 应用和监视扩展还可以使用公共文件路径共享文件。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意：如果路径为， `null` 则检查 [应用程序组配置](~/ios/watchos/app-fundamentals/app-groups.md) 以确保配置文件已正确配置，并且已在开发计算机上下载/安装。

有关详细信息，请参阅 [应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) 文档。

## <a name="related-links"></a>相关链接

- [Apple 的 WKInterfaceController 参考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 与你的包含应用共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)