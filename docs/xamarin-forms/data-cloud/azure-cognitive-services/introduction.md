---
title: Xamarin.Forms 和 Azure 认知服务简介
description: 本文介绍了一个示例应用程序，演示如何调用某些 Microsoft 认知服务 Api。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f40220a29611bb365dc77eeb5e85445a0df19fd6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563245"
---
# <a name="no-locxamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.Forms 和 Azure 认知服务简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft 认知服务是一组可供开发人员使用的 Api、Sdk 和服务，通过添加面部识别、语音识别和语言理解等功能，使应用程序更智能。本文介绍了演示如何调用某些 Microsoft 认知服务 Api 的示例应用程序。_

## <a name="overview"></a>概述

随附的示例是一个待办事项列表应用程序，它提供以下功能：

- 查看任务的列表。
- 通过软键盘添加和编辑任务，或通过使用 Microsoft Speech API 执行语音识别。
- 使用必应拼写检查 API 对任务进行拼写检查。 有关详细信息，请参阅 [使用必应拼写检查 API 的拼写检查](spell-check.md)。
- 使用转换器 API 将任务从英语转换为德语。 有关详细信息，请参阅 [使用转换器 API 进行文本翻译](text-translation.md)。
- 删除任务。
- 将任务状态设置为 "已完成"。
- 使用人脸 API，使用情感识别对应用程序进行评级。 有关详细信息，请参阅 [使用人脸 API 的情感识别](emotion-recognition.md)。

> [!WARNING]
> 此必应语音 API 已弃用，以支持 Azure 语音服务。 有关专用于 Azure 语音服务的示例，请参阅语音 [识别和语音服务 API](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)。

任务存储在本地 SQLite 数据库中。 有关使用本地 SQLite 数据库的详细信息，请参阅使用 [本地数据库](~/xamarin-forms/data-cloud/data/databases.md)。

`TodoListPage`当应用程序启动时，将显示。 此页显示存储在本地数据库中的所有任务的列表，并允许用户创建新任务或对应用程序进行评分：

![TodoListPage](introduction-images/sample-application-1.png)

可以通过单击 " *+* 导航到" 按钮来创建新项 `TodoItemPage` 。 还可以通过选择任务导航到此页面：

![TodoItemPage](introduction-images/sample-application-2.png)

`TodoItemPage`允许创建、编辑、拼写检查、转换、保存和删除任务。 语音识别可用于创建或编辑任务。 这是通过以下方式实现的：按下麦克风按钮开始录制，并再次按下相同按钮以停止录制，这会将录制发送到必应语音识别 API。

单击上的 "smilies" 按钮将 `TodoListPage` 导航到 `RateAppPage` ，用于在面部表达式的图像上执行情感识别：

![RateAppPage](introduction-images/sample-application-3.png)

`RateAppPage`允许用户拍摄人脸照片，并将其提交到人脸 API 并显示返回的情感。

## <a name="understand-the-application-anatomy"></a>了解应用程序解析

示例应用程序的共享代码项目包含五个主要文件夹：

|Folder|目的|
|--- |--- |
|模型|包含应用程序的数据模型类。 这包括 `TodoItem` 类，该类用于对应用程序使用的单个数据项建模。 此文件夹还包括用于对从不同 Microsoft 认知服务 Api 返回的 JSON 响应建模的类。|
|存储库|包含 `ITodoItemRepository` `TodoItemRepository` 用于执行数据库操作的接口和类。|
|服务|包含用于访问不同 Microsoft 认知服务 Api 的接口和类，以及类用于在 `DependencyService` 平台项目中查找实现接口的类的接口。|
|Utils|包含 `Timer` 类，该类用于 `AuthenticationService` 每9分钟续订一次 JWT 访问令牌。|
|视图|包含应用程序的页面。|

共享代码项目还包含一些重要文件：

|文件|用途|
|--- |--- |
|Constants.cs|`Constants`类，它为调用的 Microsoft 认知服务 api 指定 API 密钥和终结点。 API 密钥常量需要更新才能访问不同的认知服务 Api。|
|App.xaml.cs|`App`该类负责实例化应用程序在每个平台上显示的第一页，以及 `TodoManager` 用于调用数据库操作的类。|

### <a name="nuget-packages"></a>NuGet 包

示例应用程序使用以下 NuGet 包：

- `Newtonsoft.Json` –提供适用于 .NET 的 JSON 框架。
- `PCLStorage` –提供一组跨平台本地文件 IO Api。
- `sqlite-net-pcl` –提供 SQLite 数据库存储。
- `Xam.Plugin.Media` –提供跨平台的照片拍摄和挑选 Api。

此外，这些 NuGet 包还会安装其自己的依赖项。

### <a name="model-the-data"></a>数据建模

示例应用程序使用 `TodoItem` 类对在本地 SQLite 数据库中显示和存储的数据进行建模。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`属性用于唯一标识每个 `TodoItem` 实例，并使用 SQLite 特性进行修饰，使属性成为数据库中自动递增的主键。

### <a name="invoke-database-operations"></a>调用数据库操作

`TodoItemRepository`类实现数据库操作，类的实例可以通过属性进行访问 `App.TodoManager` 。 `TodoItemRepository`类提供下列方法来调用数据库操作：

- **GetAllItemsAsync** –检索本地 SQLite 数据库中的所有项目。
- **GetItemAsync** –从本地 SQLite 数据库中检索指定的项。
- **SaveItemAsync** –创建或更新本地 SQLite 数据库中的项。
- **DeleteItemAsync** –从本地 SQLite 数据库中删除指定项。

### <a name="platform-project-implementations"></a>平台项目实现

`Services`共享代码项目中的文件夹包含 `IFileHelper` 类使用的和接口，这些 `IAudioRecorderService` 类用于在 `DependencyService` 平台项目中查找实现接口的类。

`IFileHelper`接口由 `FileHelper` 每个平台项目中的类实现。 此类包含单个方法，该方法 `GetLocalFilePath` 返回用于存储 SQLite 数据库的本地文件路径。

`IAudioRecorderService`接口由 `AudioRecorderService` 每个平台项目中的类实现。 此类包含 `StartRecording` 、 `StopRecording` 和支持方法，这些方法使用平台 api 从设备的麦克风记录音频，并将其存储为 wav 文件。 在 iOS 上， `AudioRecorderService` 使用 `AVFoundation` API 来记录音频。 在 Android 上， `AudioRecordService` 使用 `AudioRecord` API 来记录音频。 在通用 Windows 平台 (UWP) 上， `AudioRecorderService` 使用 `AudioGraph` API 来记录音频。

### <a name="invoke-cognitive-services"></a>调用认知服务

示例应用程序调用以下 Microsoft 认知服务：

- Microsoft 语音 API。 有关详细信息，请参阅 [使用 Microsoft SPEECH API 的语音识别](speech-recognition.md)。
- 必应拼写检查 API。 有关详细信息，请参阅 [使用必应拼写检查 API 的拼写检查](spell-check.md)。
- 转换 API。 有关详细信息，请参阅 [使用转换器 API 进行文本翻译](text-translation.md)。
- 人脸 API。 有关详细信息，请参阅 [使用人脸 API 的情感识别](emotion-recognition.md)。

## <a name="related-links"></a>相关链接

- [语音识别和语音服务 API](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Microsoft 认知服务文档](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 认知服务 (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)