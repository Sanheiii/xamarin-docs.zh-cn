---
title: 文件处理 Xamarin.Forms
description: 使用 Xamarin.Forms .NET Standard 库中的代码或使用嵌入的资源即可实现文件处理。
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f8d81e037d63a7144263ce4b3520647e6829bd57
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557252"
---
# <a name="file-handling-in-no-locxamarinforms"></a>文件处理 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)

_使用 Xamarin.Forms .NET Standard 库中的代码或使用嵌入的资源即可实现文件处理。_

## <a name="overview"></a>概述

Xamarin.Forms 代码在多个平台上运行-每个平台都有自己的文件系统。 以前，这意味着在每个平台上使用本机文件 API 最容易执行文件读写。 此外，嵌入式资源是一种更简单的解决方案，可以通过应用分发数据文件。 但是，使用 .NET Standard 2.0 可以共享 .NET Standard 库中的文件访问代码。

有关处理图像文件的信息，请参阅[处理图像](~/xamarin-forms/user-interface/images.md)页。

## <a name="saving-and-loading-files"></a>保存和加载文件

可以使用 `System.IO` 类访问每个平台上的文件系统。 `File` 类可以创建、删除和读取文件，`Directory` 类可以创建、删除或枚举目录的内容。 此外，还可以使用 `Stream` 子类，它们可以实现对文件操作（例如，文件内的压缩或位置搜索）更大程度的控制。

可以使用 `File.WriteAllText` 方法编写文本文件：

```csharp
File.WriteAllText(fileName, text);
```

可以使用 `File.ReadAllText` 方法读取文本文件：

```csharp
string text = File.ReadAllText(fileName);
```

此外，`File.Exists` 方法可确定指定的文件是否存在：

```csharp
bool doesExist = File.Exists(fileName);
```

可以通过使用枚举的值 [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) 作为方法的第一个参数，从 .NET Standard 库确定每个平台上的文件的路径 `Environment.GetFolderPath` 。 然后可以使用 `Path.Combine` 方法将其与文件名组合：

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

示例应用演示了这些操作，其中包括保存和加载文本的页面：

[![保存和加载文本](files-images/saveandload-sml.png "在应用程序中保存和加载文件")](files-images/saveandload.png#lightbox "在应用程序中保存和加载文件")

## <a name="loading-files-embedded-as-resources"></a>加载作为资源嵌入的文件

若要将文件嵌入到“.NET Standard”程序集中，请创建或添加文件并确保“生成操作：EmbeddedResource”********。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![配置嵌入资源生成操作](files-images/vs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![嵌入到 .NET standard 库中的文本文件，配置嵌入资源生成操作](files-images/xs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` 用于使用其“资源 ID”访问嵌入文件****。 默认情况下，资源 ID 是以其嵌入的项目的默认命名空间为前缀的文件名，在这种情况下，程序集是 **WorkingWithFiles** 的，文件名是 **LibTextResource.txt**的，因此资源 ID 是 `WorkingWithFiles.LibTextResource.txt` 。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.LibTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream))
{  
    text = reader.ReadToEnd ();
}
```

然后，可以使用 `text` 变量来显示文本，或者在代码中以其他方式使用它。 [示例应用](/samples/xamarin/xamarin-forms-samples/workingwithfiles)的屏幕截图显示了在 `Label` 控件中呈现的文本。

 [![.NET standard 库中嵌入的文本文件](files-images/pcltext-sml.png "应用中显示 .NET Standard 库中的嵌入文本文件")](files-images/pcltext.png#lightbox "应用中显示 .NET Standard 库中的嵌入文本文件")

加载和反序列化 XML 也同样简单。 下面的代码显示了从资源加载和反序列化 XML 文件，然后绑定到 `ListView` 进行显示。 XML 文件包含一个 `Monkey` 对象数组（在示例代码中定义的类）。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.LibXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![嵌入到 .NET standard 库中，显示在 ListView 中的 Xml 文件](files-images/pclxml-sml.png "显示在 ListView 中的 .NET standard 库中的嵌入 XML 文件")](files-images/pclxml.png#lightbox "显示在 ListView 中的 .NET standard 库中的嵌入 XML 文件")

## <a name="embedding-in-shared-projects"></a>在共享项目中嵌入

共享项目也可以包含作为嵌入式资源的文件，但是由于共享项目的内容被编译到引用项目中，用于嵌入式文件资源 ID 的前缀可能更改。 这意味着每个嵌入式文件的资源 ID 对于每个平台都可能是不同的。

通过共享项目，有两种解决方案：

- **同步项目** - 编辑每个平台的项目属性，以使用相同程序集名称和默认命名空间****。 然后，可以将该值硬编码，作为共享项目中嵌入式资源 ID 的前缀。
- **#if 编译器指令** - 使用编译器指令设置正确的资源 ID 前缀，并使用该值动态构造正确的资源 ID。

说明第二个选项的代码如下所示。 编译器指令用于选择硬编码的资源前缀（通常与引用项目的默认命名空间相同）。 然后，将 `resourcePrefix` 变量与嵌入的资源文件名串联起来，从而创建有效的资源 ID。

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

### <a name="organizing-resources"></a>组织资源

上面的例子假设该文件已嵌入 .NET Standard 库项目的根目录中，在这种情况下，资源 ID 的形式是“Namespace.Filename.Extension”，如 `WorkingWithFiles.LibTextResource.txt` 和 `WorkingWithFiles.iOS.SharedTextResource.txt`****。

可以在文件夹中组织嵌入式资源。 当嵌入式资源被放置在一个文件夹中时，文件夹名称就成为资源 ID 的一部分（以句点分隔），因此资源 ID 格式就变成了“Namespace.Folder.Filename.Extension”****。 将示例应用中使用的文件放入文件夹“MyFolder”将生成相应的资源 ID `WorkingWithFiles.MyFolder.LibTextResource.txt` 和 `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`****。

### <a name="debugging-embedded-resources"></a>调试嵌入的资源

由于有时很难理解为什么没有加载特定资源，因此可以将以下调试代码临时添加到应用程序中，以帮助确认资源配置正确。 它将把给定程序集中嵌入的所有已知资源输出到“错误”面板，以帮助调试资源加载问题****。

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>总结

本文介绍了一些用于在设备上保存和加载文本以及加载嵌入式资源的简单文件操作。 使用 .NET Standard 2.0，可以共享 .NET Standard 库中的文件访问代码。

## <a name="related-links"></a>相关链接

- [FilesSample](/samples/xamarin/xamarin-forms-samples/workingwithfiles)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [使用 Xamarin.iOS 中的文件系统](~/ios/app-fundamentals/file-system.md)