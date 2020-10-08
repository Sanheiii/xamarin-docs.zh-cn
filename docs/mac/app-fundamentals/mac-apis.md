---
title: 适用于 Xamarin 开发人员的 macOS Api
description: '本文档介绍如何读取 Ansi-c 选择器，以及如何在 Xamarin 应用程序中查找其相应的 c # 方法。'
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: efac76338af710e716decf47635652bf8de74910
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851504"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>适用于 Xamarin 开发人员的 macOS Api

## <a name="overview"></a>概述

在大多数情况下，通过 Xamarin 进行开发，您可以考虑、读取和编写 c #，而不会对基础的目标-C Api 有很多关注。 但是，有时你需要从 Apple 中阅读 API 文档，将 Stack Overflow 的答案转换为你的问题的解决方案，或将其与现有示例进行比较。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>读取足够的目标-C 是危险的

有时需要读取目标 C 定义或方法调用，并将其转换为等效的 c # 方法。 让我们看看一个目标 C 函数定义并细分各个部分。 此方法 (可在) 中找到的目标中的 *选择器* `NSTableView` ：

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

声明可以从左到右读取：

- `-`前缀表示 (非静态) 方法中的实例。 + 表示它是类 (静态) 方法
- `(BOOL)` 是 (在 c # 中为布尔值的返回类型 ) 
- `canDragRowsWithIndexes` 名称的第一部分。
- `(NSIndexSet *)rowIndexes` 是第一个参数，它的类型为。 第一个参数的格式为： `(Type) paramName`
- `atPoint:(NSPoint)mouseDownPoint` 是第二个参数及其类型。 第一个参数之后的每个参数都是以下格式： `selectorPart:(Type) paramName`
- 此消息选择器的完整名称为： `canDragRowsWithIndexes:atPoint:` 。 请注意， `:` 最后是重要的。
- 实际的 Xamarin c # 绑定为： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

此选择器调用可以采用相同的方式读取：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 实例的 `v` `canDragRowsWithIndexes:atPoint` 选择器会通过两个参数传递， `set` 并 `point` 传入。
- 在 c # 中，方法调用如下所示： `v.CanDragRows (set, point);`

<a name="finding_selector"></a>

## <a name="finding-the-c-member-for-a-given-selector"></a>查找给定选择器的 c # 成员

找到需要调用的目标-C 选择器后，下一步是将其映射到等效的 c # 成员。 有四种方法可以尝试 (继续 `NSTableView CanDragRows` 执行示例) ：

1. 使用自动完成列表快速扫描名称相同的内容。 由于我们知道它是一个实例 `NSTableView` ，因此你可以键入：

    - `NSTableView x;`
    - `x.` [ctrl + space （如果列表未出现) 。
    - `CanDrag` 回车
    - 右键单击方法，然后单击 "声明" 以打开 "程序集浏览器"，您可以在其中将 `Export` 属性与相关的选择器进行比较

2. 搜索整个类绑定。 由于我们知道它是一个实例 `NSTableView` ，因此你可以键入：

    - `NSTableView x;`
    - 右键单击，然后单击 `NSTableView` "声明到程序集浏览器"
    - 搜索相关选择器

3. 可以使用 [XAMARIN API 联机文档](/dotnet/api/?view=xamarinmac-3.0) 。

4. Miguel 在 [此处](https://tirania.org/tmp/rosetta.html) 提供了 Xamarin For Mac api 的 "Rosetta 石头" 视图，你可以在其中搜索给定的 API。 如果你的 API 不是 AppKit 或 macOS 特定的，你可能会发现它。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
