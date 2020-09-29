---
title: Xamarin 中的 watchOS 用户界面控件
description: 本文档介绍可在 watchOS 用户界面中使用的各种控件。 它提供标签、按钮、开关、滑块、图像、分隔符、地图等的说明。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 3d47b8d524d8f798ab9c0a654fde8246b9fbcc36
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437152"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Xamarin 中的 watchOS 用户界面控件

[**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)示例演示各种 watchOS 控件。 此处显示了应用的情节提要 (单击以缩放) ：

[![示例 watchOS 布局](images/storyboard-sml.png)](images/storyboard.png#lightbox)

所有控件的编程名称都以 `WKInterface` (示例为前缀。 `WKInterfaceLabel`, `WKInterfaceButton`).

|控制|说明|屏幕快照|
|---|---|---|
|标签|使用 `SetText` 和其他属性可控制标签控件中文本的外观。 `NSAttributedString` 也支持。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![标签屏幕快照](Images/label.png)|
|Button|在情节提要中创建和设置属性。 按住 Ctrl 并拖动以添加 `Action` ，以便在单击时实现处理程序。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![按钮屏幕快照](Images/button.png)|
|开关|使用 `SetOn` 控制开关状态。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![切换屏幕快照](Images/switch.png)|
|滑块|可能有许多不同的样式。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![滑块屏幕快照](Images/slider.png)|
|映像|使用在 `myImage.SetImage("MyWatchImage")` 手表上加载图像，或将 `WKInterfaceDevice.CurrentDevice.AddCachedImage` 其缓存以在手表上重复使用。<br />[图像控件文档](~/ios/watchos/user-interface/image.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![图像屏幕快照](Images/image.png)|
|Separator|使用分隔符帮助创建引人注目的监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![分隔符屏幕快照](Images/separator.png)|
|映射|地图图像在手表上静态显示，但您可以控制其外观的许多方面，包括添加 pin。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![地图屏幕快照](Images/map.png)|
|电影 & InlineMove|电影可以自行打开，也可以以内联方式打开<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![电影屏幕快照](Images/movie.png)|
|组|使用组来帮助创建引人注目的监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![组屏幕快照](Images/group.png)|
|表|IOS 上的表的简化版本。 实现 `DidSelectRow` 以响应用户选择 (或使用 segue) 。<br />[表控件文档](~/ios/watchos/user-interface/table.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![表屏幕快照](Images/table.png)|
|设备|`WKInterfaceDevice.CurrentDevice` 包括 `ScreenBounds` 、和等属性 `ScreenScale` `PreferredContentSizeCategory` 。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![设备屏幕快照](Images/device.png)|
|[菜单](~/ios/watchos/user-interface/menu.md)|在情节提要中定义强制新闻菜单，并为代码中的每个按钮实现操作。<br />[Force Touch) 文档 (菜单控件](~/ios/watchos/user-interface/menu.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![菜单屏幕快照](Images/controller.png)|
|文本输入|使用 `PresentTextInputController` 和 `WKTextInputMode` 枚举。<br />[文本输入文档](~/ios/watchos/user-interface/text-input.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![文本输入屏幕快照](Images/textinput.png)|
|Digital Crown|Digital Crown 可用于驱动选取器，也可在代码中跟踪旋转。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![数字王冠屏幕快照](Images/digital-crown.png)|
|笔势|有四种类型的手势识别可添加到场景：点击、刷平、平移和 LongPress。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![手势屏幕快照](Images/gestures.png)|

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [观看工具包 API 参考](xref:WatchKit)