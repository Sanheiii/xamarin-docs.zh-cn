---
title: 解决库安装错误
description: 在某些情况下，你可能会在安装 Android 支持库时遇到错误。 本指南提供了一些常见错误的解决方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724297"
---
# <a name="resolving-library-installation-errors"></a>解决库安装错误

在某些情况下，你可能会在安装 Android 支持库时遇到错误。本指南提供了一些常见错误的解决方法。

## <a name="overview"></a>概述

生成 Xamarin.Android 应用项目时，Visual Studio 或 Visual Studio for Mac 在尝试下载和安装依赖项库时可能会出现生成错误。 其中许多错误都是由于网络连接问题、文件损坏或版本控制问题所致。 本指南介绍了最常见的支持库安装错误，并提供解决这些问题的步骤，让你可以重新生成应用项目。

## <a name="errors-while-downloading-m2repository"></a>下载 m2Repository 时出错

引用 Android 支持库或 Google Play 服务的 NuGet 包时，可能会出现 m2repository 错误。 该错误消息类似于以下内容：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

此示例适用于 android\_m2repository\_r16，但其他版本也可能出现此错误消息，如 android\_m2repository\_r18 或 android\_m2repository\_r25  。

### <a name="automatic-recovery-from-m2repository-errors"></a>从 m2repository 错误自动恢复

通常，删除有问题的库并根据以下步骤重新生成即可解决此问题：

1. 导航到计算机上的支持库目录：

    - 在 Windows 上，支持库位于 C:\\Users\\username\\AppData\\Local\\Xamarin。

    - 在 Mac OS X 上，支持库位于 /Users/username/.local/share/Xamarin。

2. 找到错误消息对应的库和版本文件夹。 例如，上面的错误消息对应的库和版本文件夹位于 Android.Support.v4\\22.2.1：

    [![22.2.1 支持库的示例文件夹位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. 删除版本文件夹的内容。 请确保删除此文件夹中的 .zip 文件以及 content 和 embedded 子目录  。 对于上面所示的示例错误消息，此屏幕截图中显示的文件和子目录（content、embedded 和 android_m2repository_r16.zip）都会被删除  ：

    [![22.2.1 支持库文件夹的示例内容](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   注意，请务必删除此文件夹中的所有内容。 虽然此文件夹最初可能包含“缺少的”android\_m2repository\_r16.zip 文件，但此文件可能只下载了一部分或已损坏。

4. 重新生成项目 &ndash; 执行此操作将导致生成过程重新下载缺少的库。

在大多数情况下，这些步骤可以解决生成错误，使你能够继续操作。 如果删除此库无法解决生成错误，则必须按照下一部分中所述手动下载并安装 android\_m2repository\_r_nn_.zip 文件。

### <a name="manually-downloading-m2repository"></a>手动下载 m2repository

如果已尝试使用上述自动恢复步骤但仍遇到生成错误，则可以手动下载 android\_m2repository\_r_nn_.zip 文件（使用 Web 浏览器）并按照以下步骤安装。 如果开发计算机上没有 Internet 访问权限，但你可以使用其他计算机下载存档，则此过程也很有用。

1. 下载错误消息对应的 android\_m2repository\_r_nn_.zip 文件 &ndash; 以下列表中提供了各个链接（以及每个链接 URL 的相应 MD5 哈希）：

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    如果此表中未显示 m2repository 存档，则可以通过在要下载的 m2repository 的名称前面加上 `https://dl-ssl.google.com/android/repository/` 来创建下载 URL 。 例如，使用 https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip 下载 android\_ m2repository\_ r10.zip 。

2. 将该文件重命名为下载 URL 对应的 MD5 哈希，如上表所示。 例如，如果下载 android\_m2repository\_r25.zip，请将其重命名为 0B3F1796C97C707339FB13AE8507AF50.zip 。 如果表中未显示已下载文件的下载 URL 的 MD5 哈希，则可以使用[联机 MD5 生成器](http://www.webconfs.com/online-md5-generator.php)将 URL 转换为 MD5 哈希字符串。

3. 将该文件复制到 Xamarin zips 文件夹：

    - 在 Windows 上，此文件夹位于 C:\\Users\\username\\AppData\\Local\\Xamarin\\zips。

    - 在 Mac OS X 上，此文件夹位于 username/.local/share/Xamarin/zips。

    例如，以下屏幕截图说明了在 Windows上下载 android\_m2repository\_r16.zip 并将其重命名为其下载 URL 的 MD5 哈希时的结果：

    [![重命名为 0595E577D19D31708195A83087881EE6.zip 的 r16.zip 存储库示例](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

如果此过程无法解决生成错误，则必须手动下载 android\_m2repository\_r_nn_.zip 文件，将其解压缩，然后安装其内容（如以下部分所述）。

### <a name="manually-downloading-and-installing-m2repository-files"></a>手动下载和安装 m2repository 文件

要完全通过手动方式从 m2repository 错误恢复，需要下载 android\_m2repository\_r_nn_.zip 文件（使用 Web 浏览器），将其解压缩并将其内容复制到计算机上的支持库目录 。 在下面的示例中，我们将从此错误消息恢复：

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用以下步骤下载 m2repository 并安装其内容：

1. 删除错误消息对应的库文件夹的内容。 例如，在上面的错误消息中，你将删除以下目录中的内容：C:\\Users\\username\\AppData\\Local\\Xamarin\\Android.Support.v4\\23.1.1.0。
    如前文所述，必须删除此目录的全部内容：

    [![从 23.1.1.0 文件夹中删除 content、embedded 和 android_m2repository 文件夹](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. 从 Google 下载错误消息对应的 android\_m2repository\_r_nn_.zip 文件（请参阅上一节中的链接）。

3. 将此 .zip 存档提取到任意位置（如桌面）。 此操作应该会创建与 .zip 存档名称相对应的目录。 在此目录中，可以找到名为 m2repository 的子目录：

    [![在提取的 zip 存档中找到的 m2repository 文件夹](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. 在步骤 1 中清除的版本控制库目录中，重新创建 content 和 embedded 子目录 。 例如，以下屏幕截图说明了在 android\_m2repository\_r25.zip 的 23.1.1.0 文件夹中创建的 content 和 embedded 子目录   ：

    [![在 23.1.1.0 文件夹中创建 content 和 embedded 文件夹](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. 从解压缩的 .zip 中将 m2repository 复制到上一步创建的 content 目录中  ：

    [![复制到 23.1.1.0/content 文件夹的 m2repository 的屏幕截图](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. 在解压缩的 .zip 目录中，浏览到 m2repository\\com\\android\\support\\support-v4 并打开与上面创建的版本号相对应的文件夹（在本例中为 23.1.1）  ：

    [![support-v4/23.1.1 文件夹中包含的示例文件](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. 将此文件夹中的所有文件复制到在步骤 4 中创建的 embedded 目录：

    [![复制到 23.1.1.0/embedded 文件夹的文件示例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. 验证是否复制了所有文件。 embedded 目录现应包含 .jar、.aar 和 .pom 等文件   。

9. 将任何提取的 .aar 文件的内容解压缩到 embedded 目录 。 在 Windows 上，将 .zip 扩展名附加到 .aar 文件，打开该文件，然后将内容复制到 embedded 目录  。
    在 macOS 上，使用终端（例如 unzip file.aar）中的 unzip 命令解压缩 .aar 文件  。

此时，你已手动安装了缺少的组件，生成项目时应不会遇到任何错误。 如果遇到错误，请验证是否已下载与错误消息中的版本完全对应的 m2repository.zip 存档版本，并验证是否已按上述步骤所述将其内容安装到正确位置 。

## <a name="summary"></a>总结

本文说明了如何从自动下载和安装依赖项库过程发生的常见错误进行恢复。 介绍了如何删除有问题的库，以及如何重新生成项目，以便重新下载并重新安装库。
描述了如何下载库并将其安装在 zips 文件夹中。 此外，还介绍了一种更复杂的手动下载和安装所需文件的方法，以解决无法通过自动方式解决的问题。
