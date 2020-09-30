---
title: 是否可以将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9dc5b8e63a35c3a0b797d0794af7a31aea4969c9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555588"
---
# <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>是否可以将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？

本指南使用 Xamarin.Forms .NET Standard 库模板作为示例，但相同的常规方法也适用于 Xamarin.Forms 共享项目模板。 本指南是通过从1.5.1.6471 更新到2.1.0.6529 的示例编写的 Xamarin.Forms ，但也可以改为将其他版本设置为默认值。

1. 复制原始模板 `.zip` ：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. 将解压缩 `.zip` 到临时位置。

3. 将包的旧版本的所有匹配项更改 Xamarin.Forms 为要使用的新版本。
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    示例：`<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. 将主 [多项目模板文件](/visualstudio/ide/how-to-create-multi-project-templates) 的 "name" 元素更改 (`Xamarin.Forms.PCL.vstemplate`) 使其唯一。 例如：

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. 重新压缩整个模板文件夹。 请确保与文件的原始文件结构相匹配 `.zip` 。 `Xamarin.Forms.PCL.vstemplate`文件应位于文件的顶部 `.zip` ，而不是位于任何文件夹中。

6. 在每个用户的 Visual Studio 模板文件夹中创建 "移动应用" 子目录：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. 将新的压缩模板文件夹复制到新的 "移动应用" 目录。

8. 下载与步骤3中的版本匹配的 NuGet 包。 例如， [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (另请参阅 [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) ，然后将其复制到 Xamarin Visual Studio extensions 文件夹的相应子文件夹中：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`