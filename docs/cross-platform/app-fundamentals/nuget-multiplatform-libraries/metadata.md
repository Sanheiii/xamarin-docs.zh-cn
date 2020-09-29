---
title: 编辑 NuGet 元数据
description: 本文档介绍如何使用项目选项编辑多平台库的 NuGet 元数据。 它讨论了必需的和可选的元数据。
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: a3e1e8d1be1f84f707c80c42adb6b8d1e3f234a9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457011"
---
# <a name="editing-nuget-metadata"></a>编辑 NuGet 元数据

_使用项目选项编辑多平台库的 NuGet 元数据_

库项目类型 (例如 PCL 或 .NET Standard，或者新的 NuGet 项目类型) 在 "**项目选项**" 窗口中具有 " **nuget 包**" 部分。

**Metadata**节配置[ **nuspec** NuGet 包清单文件](/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)中使用的值。

## <a name="required-information"></a>必需信息

" **常规** " 选项卡包含四个字段，必须输入这些字段才能生成 NuGet 包：

[![NuGet 包所需的元数据窗口](metadata-images/metadata-general-sml.png)](metadata-images/metadata-general.png#lightbox)

- **ID** –包标识符，在 NuGet.org (或包将分发) 的任何位置，包标识符应是唯一的。 遵循本 [指南](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) ，只使用 URL 中有效的字符 (不含空格，并避免大多数特殊字符) 。
- **版本** –选择与 [NuGet 的版本控制规则](/nuget/create-packages/dependency-versions)一致的版本号。
- **作者** –以逗号分隔的名称列表。
- **说明** –用户选择包时所显示的包功能的概述。

> [!NOTE]
> 请记住，在生成用于分发到 NuGet 或其他用户的新版本时，会递增版本号。

有关详细信息，请参阅 [所需的元素参考](/nuget/schema/nuspec#required-metadata-elements) ，并详细了解如何 [选择唯一的包标识符并设置版本号](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) 和 [设置包类型](/nuget/create-packages/creating-a-package#setting-a-package-type)。

> [!IMPORTANT]
> 必须输入此选项卡上的所有字段;否则，将显示一条错误消息： _"项目没有 NuGet 元数据，因此将不会创建 nuget 包。可以在 "项目选项" 的 "元数据" 部分中指定 NuGet 包元数据_

## <a name="optional-metadata"></a>可选元数据

" **详细信息** " 选项卡包含要包含在 NuGet 包清单文件中的可选字段。

[![NuGet 包可选元数据窗口](metadata-images/metadata-detail-sml.png)](metadata-images/metadata-detail.png#lightbox)

有关必填字段和可选字段的详细信息，请参阅 [可选的元素参考](/nuget/schema/nuspec#optional-metadata-elements) 。

> [!NOTE]
> 如果 NuGet 包在 [NuGet.org](https://www.nuget.org) 上分发，则建议提供尽可能多的信息。

## <a name="related-links"></a>相关链接

- [.nuspec 引用](/nuget/schema/nuspec#general-form-and-schema)