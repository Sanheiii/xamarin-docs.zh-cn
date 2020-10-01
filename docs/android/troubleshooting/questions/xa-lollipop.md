---
title: 哪个版本的 Xamarin.Android 添加了 Lollipop 支持？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 27631d19ea157c1af08666b55a067e729b806434
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453631"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪个版本的 Xamarin.Android 添加了 Lollipop 支持？

> [!NOTE]
> 本指南最初是为 Android L 预览版编写的。

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) 添加了 Android L 预览版支持。
- [Xamarin.Android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) 添加了 Android Lollipop 支持。

Xamarin 仅主动支持 Xamarin 工具的当前稳定版本。 对于较旧版本的工具，以下信息“按原样”提供。 有关 Xamarin 版本的最新信息，请查看[发行说明](/xamarin/android/release-notes/)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>Android L 预览版中“缺少 API 21 级别的 android.jar”

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

可能会显示以下错误消息（或类似消息）：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此消息表示未安装 API 21 级别的 Android SDK 平台。 请在 Android SDK 管理器中安装它（“工具”>“打开 Android SDK 管理器...”  ），或更改 Xamarin.Android 项目以面向已安装的 API 版本。

此问题有几种解决方法：

1. 更改项目，使其面向 API 19 或更低级别。

2. 将 android-21 文件夹从 android-21 重命名为 android-L。 （最佳做法是，应仅将其用作临时修补程序，并且它可能无法正常工作。）

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. 暂时降级到 Android API 级别 21“L”预览版 [1]：

    1. 删除 **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21** 
    2. 将 [1] 提取到 **C:\\Users\\&lt;username&gt;\\AppData\\Local\\Android\\android-sdk\\platforms** 以创建“android-L”  文件夹。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

可能会显示以下错误消息（或类似消息）：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

这意味着，不会安装 API 21 级别的 Android SDK 平台。请在 Android SDK 管理器中安装它（“工具”>“SDK 管理器...”），或更改 Xamarin.Android 项目以面向已安装的 API 版本。

此问题有几种解决方法：

1. 更改项目，使其面向 API 19 或更低级别。

2. 将 android-21 文件夹从 android-21 重命名为 android-L。 （最佳做法是，应仅将其用作临时修补程序，并且它可能无法正常工作。）

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暂时降级到 Android API 级别 21“L”预览版 [1]：

    1. 删除 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2. 将 [1] 提取到 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx**，以创建“android-L”  文件夹。

-----

[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)