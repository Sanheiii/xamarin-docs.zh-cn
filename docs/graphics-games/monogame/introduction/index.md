---
title: MonoGame 的游戏开发简介
description: 此多部分演练演示了如何使用 MonoGame 创建简单的2D 应用程序。  它介绍常见的游戏编程概念，如图形、输入、游戏实体和物理学。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: bdbce0b001d5baf5ef751e092a2083efdf3ca992
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436265"
---
# <a name="introduction-to-game-development-with-monogame"></a>MonoGame 的游戏开发简介

_此多部分演练演示了如何使用 MonoGame 创建简单的2D 应用程序。 它介绍常见的游戏编程概念，如图形、输入、游戏实体和物理学。_

本文介绍了用于创建跨平台游戏的 MonoGame API 技术。 有关完整的平台列表，请参阅 [MonoGame 网站](http://www.monogame.net/)。 本教程将使用 c # 编写代码示例，但 MonoGame 在 F # 中也能完全正常运行。

MonoGame 是一个跨平台的硬件加速 API，为导入资产提供了图形、音频、游戏状态管理、输入和内容管道。 与大多数游戏引擎不同，MonoGame 不提供任何模式或项目结构。  尽管这意味着开发人员可以随意按自己的方式组织代码，但这也意味着，在首次启动新项目时需要进行一些安装代码。

本演练的第一部分重点介绍如何设置空项目。 最后一部分介绍如何编写我们的所有游戏逻辑和内容-其中大多数都是跨平台。

本演练结束时，我们将创建一个简单的游戏，播放机可以使用触控输入来控制动画字符。  尽管从技术上讲，这并不是完整的游戏 (因为它在) 的情况下不会有任何，它演示了许多游戏开发概念，并可用作多种游戏类型的基础。

下面显示了本演练的结果：

![鼠标后的示例游戏字符的动画](images/image1.gif)

## <a name="monogame-and-xna"></a>MonoGame 和

MonoGame 库旨在模拟 Microsoft 的具有语法和功能的功能库。  MonoGame 命名空间下的所有对象都存在，允许在 MonoGame 中使用大多数未进行修改的代码。

熟悉 MonoGame 的开发人员已经熟悉了的语法，而开发人员查找有关使用 MonoGame 的其他信息时，将能够引用现有的联机文件中演练、API 文档和讨论。

## <a name="walkthrough-parts"></a>演练部分

- [第1部分-创建跨平台 MonoGame 项目](~/graphics-games/monogame/introduction/part1.md)
- [第2部分-实现 WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相关链接

- [WalkingGame MonoGame Project (示例) ](/samples/xamarin/mobile-samples/walkinggamemg/)
- [NuGet 上的 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [NuGet 上的 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)