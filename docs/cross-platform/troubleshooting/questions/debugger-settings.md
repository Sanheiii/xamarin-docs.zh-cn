---
title: 调试器需要哪些项目设置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 856c04d129058e8cbac30dcdf619e8b2b5a66cb6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014264"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>调试器需要哪些项目设置？

为了使调试器能够按预期工作（命中断点、显示调试日志等），必须同时启用开发人员检测和调试信息显示。

请按照以下步骤检查您的环境设置：

## <a name="visual-studio"></a>Visual Studio

1. 打开项目选项
2. 中转到 "**生成 > 高级 ...** "将调试信息设置为**完整**
3. 每个平台的设置：
   - **> 调试选项**中转到 "Android 选项"。 勾选 "**启用开发人员检测**" 框。
   - 请参阅**IOS 调试 > 调试 & 检测**。 勾选 "**启用调试**" 框。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 打开项目选项
2. **> 编译器 "> 常规" 选项**中转到 "生成"。 将调试信息设置为**完整**
3. 每个平台的设置：
    - 请参阅 **> Android build > 调试选项**中的 "生成"。 勾选 "**启用开发人员检测**" 框。
    - 请参阅**生成 > IOS 调试**。 勾选 "**启用调试**" 框。
