---
title: tvOS 12 简介
description: 本文档提供了 tvOS 12 中新增和更新功能的概述，其中 Xamarin 的预览版本当前提供C#绑定。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 94fea1786497d04602ea6cf06d875206cf69eb3e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030553"
---
# <a name="introduction-to-tvos-12"></a>tvOS 12 简介

本文档提供了新的和更新的 tvOS 12 的高级概述。

若要开始使用 Xamarin 生成 tvOS 12 应用，请查看[入门指南](~/ios/platform/introduction-to-ios12/get-started.md)。

## <a name="tvuikit"></a>TVUIKit

tvOS 12 包括 TVUIKit，这是一组 Api，使 tvOS 开发人员可以使用常用的 tvOS 控件，如海报视图、标题按钮、卡片视图和 monogram 视图。 tvOS 12 还引入了一个属性，该属性允许标签滚动太长而无法完全显示的文本。

## <a name="password-autofill"></a>密码自动填充

使用 tvOS 12，用户可以通过点击一次来使用其 iOS 设备登录到 tvOS 应用程序。 这是通过结合使用 `UITextContentType` 用法来指定用户名和密码字段、关联的域以建立 iOS 应用与 tvOS 应用之间的关系，以及首选焦点环境来选择要在用户之后接收焦点的项提供用户名和密码。

## <a name="focus-engine-enhancements"></a>重点引擎增强功能

tvOS 12 允许所有应用（无论如何呈现）与焦点引擎交互。 通过用户与 Siri 远程进行交互，可以在任何应用程序中使用焦点引擎来选择项目、提示可能的焦点更改和自然更新焦点。 在自定义应用程序中通过 UIKit 的 `IUIFocusItemContainer` 接口、`UIFocusMovementHint` 类、`IUIFocusItemScrollableContainer` 接口以及其他相关的类和方法启用了此功能。

## <a name="vision-framework"></a>远景框架

远景框架包含可检测各种方向的人脸的改进面部检测器。 而且，现在可以使用请求修订来选择特定的远景框架算法修订版本。

## <a name="natural-language-framework"></a>自然语言框架

自然语言框架使应用程序可以执行各种类型的语言分析。 例如，它可用于标识词性的各个部分，并确定文本块所代表的语言。

## <a name="deprecations"></a>弃用功能

使用 tvOS 12，Apple 已弃用 OpenGL ES，[鼓励开发人员](https://developer.apple.com/tvos/whats-new/)采用金属。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS-Apple 开发人员（Apple）](https://developer.apple.com/tvos/)
- [TvOS 12 （Apple）中的新增功能（视频）](https://developer.apple.com/videos/play/wwdc2018/208/)
- [电视（Apple）](https://www.apple.com/tv/)
