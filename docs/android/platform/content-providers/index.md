---
title: ContentProvider 简介
description: Android 操作系统使用内容提供程序来帮助访问共享数据，例如媒体文件、联系人和日历信息。 本文介绍 ContentProvider 类，并提供两个示例，来介绍如何使用它。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 00da8b668ea26aa9146ff0c30d07a60cefe6695a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454008"
---
# <a name="intro-to-contentproviders"></a>ContentProvider 简介

_Android 操作系统使用内容提供程序来帮助访问共享数据，例如媒体文件、联系人和日历信息。本文介绍 ContentProvider 类，并提供两个示例，来介绍如何使用它。_

## <a name="content-providers-overview"></a>内容提供程序概述

ContentProvider 封装数据存储库，并提供了一个用于访问它的 API。 该提供程序是 Android 应用程序的一部分，通常还提供用于显示/管理数据的 UI。 使用内容提供程序的主要好处是使其他应用程序可以使用提供程序客户端对象（称为 ContentResolver）轻松访问封装的数据。 内容提供程序和内容解析器一起提供一个易于生成和使用的一致的应用程序间 API，用于访问数据。 任何应用程序都可以选择使用 `ContentProviders` 在内部管理数据，也可以将其公开给其他应用程序。

应用程序还需要 `ContentProvider` 来提供自定义搜索建议，或者希望能够将复杂数据从你的应用程序复制并粘贴到其他应用程序，也会需要它。 本文档说明了如何使用 Xamarin.Android 访问和生成 `ContentProviders`。

本节的结构如下：

- **工作原理** &ndash; `ContentProvider` 的用途及其工作原理的概述。

- **使用内容提供程序** &ndash; 访问“联系人”列表的示例。

- **使用 ContentProvider 共享数据** &ndash; 在同一应用程序中编写和使用 `ContentProvider`。

`ContentProviders` 和对其数据进行操作的游标通常用于填充 ListView。 有关如何使用这些类的更多信息，请参阅 [ListView 和 Adapter 指南](~/android/user-interface/layouts/list-view/index.md)。

Android（或其他应用程序）公开的 `ContentProviders` 是在应用程序中包含来自其他源的数据的简便方法。 借助它们，可以从应用程序内部访问和显示数据（例如，“联系人”列表、照片或日历事件），并允许用户与该数据进行交互。

自定义 `ContentProviders` 是打包数据以在自己的应用内使用或供其他应用程序使用（包括自定义搜索和复制/粘贴之类的特殊用途）的便捷方法。

本节中的主题提供了一些使用和编写 `ContentProvider` 代码的简单示例。

## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示（示例）](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider（示例）](/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [内容提供程序开发人员指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 类引用](xref:Android.Content.ContentProvider)
- [ContentResolver 类引用](xref:Android.Content.ContentResolver)
- [ListView 类引用](xref:Android.Widget.ListView)
- [CursorAdapter 类引用](xref:Android.Widget.CursorAdapter)
- [UriMatcher 类引用](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [ContactsContract 类引用](xref:Android.Provider.ContactsContract)