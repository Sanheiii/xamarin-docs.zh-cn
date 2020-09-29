---
title: 在 Xamarin 中使用 watchOS 布局
description: 本文档介绍了如何使用 Xamarin 创建 watchOS 布局。 它讨论了接口控制器、组、分隔符和内容控件。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1fdb7a10bd767085ba8758fa2e026cc36c93639a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436917"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>在 Xamarin 中使用 watchOS 布局

为 Apple Watch [屏幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md) 设计布局带来了独特的挑战。

## <a name="design-tips"></a>设计提示

关键点是：使用大手指使用户界面在小型观看屏幕上可读和使用。 不要陷入 **IOS 模拟器** 设计的陷阱 (这会显示非常大的) 和 **鼠标指针** ， (可与小接触目标) ！

- 使用黑色背景-它使用手表的黑色面板创建更大屏幕的错觉。

- 不要在屏幕布局的周围进行填充-档板形成了一个自然的可视化填充。

- 专注于可读性。 谨慎使用字体大小和颜色，以确保文本可读。 使用内置文本样式可获取自动动态类型支持。

![动态类型支持示例](layout-images/type.png)

- 专注于触摸目标大小。 带有文本标签的按钮/tappable 表行应横跨整个屏幕。 Apple 显示 "从不超过三个项目并排"，如果使用图标而不是文本标签，则为。

- 使用[ `Menu` 控件](~/ios/watchos/user-interface/menu.md)公开不太常用的功能，以使应用程序的设计清晰明了。

## <a name="implementation"></a>实现

观看工具包包含以下控件，可帮助你构建引人注目的监视应用布局：

### <a name="interface-controller"></a>接口控制器

`WKInterfaceController`是所有场景的基本类。

接口控制器的设计图面的行为类似于垂直 **组**：可以将其他控件拖动到接口控制器上，它们会自动布局在另一个上方：

![控件会自动向外布局一个控件](layout-images/controller-scene.png)

可以设置每个控件的 **位置** 和 **大小** 属性，以控制其外观：

![设置每个控件的位置和大小属性](layout-images/positionsize-attributes.png)

如果将大小设置为 **相对于容器** ，则可以提供比例值和偏移量调整。 此屏幕截图显示了一个按钮，该按钮已设置为使用80% 的手表屏幕宽度 (**0.8**) ：

![提供比例值和偏移量调整](layout-images/button-attributes.png)

### <a name="group"></a>组

`WKInterfaceGroup` 是一个简单的布局容器，可以配置为垂直或水平堆叠控件。 默认情况下，它包含每个控件之间的间距，但你可以在 " **属性** " 检查器中修改 (和插入) 的间距。

![在属性检查器中修改间距和嵌入项](layout-images/group-attributes.png)

组本身可以相对于其周围的控件调整大小和位置，并且可以嵌套组来创建复杂的布局。

![可以嵌套组以创建复杂的布局](layout-images/group-scene.png)

### <a name="separator"></a>Separator

分隔符控件旨在帮助提供布局中的视觉指导。 使用分隔符 (或背景色或图像) ，以帮助用户了解你的屏幕上相关的内容。

![分隔符用法示例](layout-images/separator-scene.png)

请注意，未使用整个屏幕宽度的蓝色和绿色分隔符已配置为 **固定** 或 **相对于容器** 大小。

### <a name="content-controls"></a>内容控件

无需 `Label` 、、 `Image` `Button` 、 `Switch` 、、 `Slider` `Map` 和 [其他控件](~/ios/watchos/user-interface/index.md)即可完成布局。
可以使用 **组** 或每个控件上的位置和大小设置将这些设置定位到布局中。

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的布局引用](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的颜色 & 版式参考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)