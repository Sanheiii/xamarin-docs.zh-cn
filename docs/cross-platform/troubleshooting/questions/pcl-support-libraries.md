---
title: 如何查看 PCL 中支持的库？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: davidortinau
ms.author: daortin
ms.date: 07/27/2018
ms.openlocfilehash: 9484bcac199118bb1beb1b520be8e231dc9181ce
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457843"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何查看 PCL 中支持的库？

- 可在此页的 " *支持的功能* " 部分下找到各种 PCL 目标平台支持的各种功能的概述： [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- 另一种方法是使用 " [.net 可移植性分析器](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) " 来评估现有库是否可转换为 PCL 配置文件。

- 第三种可能的做法是浏览可能使用的实际配置文件的内容。 例如，使用配置文件78，你可以执行以下操作： `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` 并查看其中的所有程序集。

无论选择哪种方法，请注意，必须通过 NuGet 和 Microsoft BCL 库下载某些功能。