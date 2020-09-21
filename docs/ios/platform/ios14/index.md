---
title: IOS 14 简介
description: '本文档提供了一些适用于 Xamarin 提供 c # 绑定的 iOS 14 Api 的高级说明。'
ms.prod: xamarin
ms.assetid: 4953216e-472b-4484-9c1e-7263ac537f21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: e9793617c76813fb68a57213edd8b48529f19ac7
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843600"
---
# <a name="introduction-to-ios-14"></a>IOS 14 简介

按照这些 [说明开始操作](~/ios/platform/ios14/get-started.md) 。

## <a name="new-control-uicolorwell"></a>新控件： UIColorWell

[`UIColorWell`](https://developer.apple.com/documentation/uikit/uicolorwell) 是一个新的 UIKit 控件，用于从选择的样本选择颜色、使用吸管或手动输入值。 控件显示在点击时启动模式窗体的圆形按钮。

![UIColorWell](ios14-images/colorwell.png)

```xaml
<ios:UIColorWell
    SelectedColor="{x:Static ios:UIColor.Red}"
    ValueChanged="OnColorChanged" />
```

```csharp
private void OnColorChanged(object sender, EventArgs e)
{
    var colorWell = (UIColorWell)sender; 
    Debug.WriteLine(colorWell.SelectedColor);
}
```

## <a name="modified-controls"></a>修改的控件

若干控件已接收更新，最值得注意的是：

- [UIBarButtonItem](https://developer.apple.com/documentation/uikit/uibarbuttonitem) 现在可以添加将显示为 Segue 的 UIMenu。
- [UIDatePicker](https://developer.apple.com/documentation/uikit/uidatepicker) 现在支持多种样式：自动 (默认) 、压缩、内联和滚轮。
- [UISplitViewController](https://developer.apple.com/documentation/uikit/uisplitviewcontroller) 现在支持三列： Primary、辅助和补充。
 
![预发行版 API](~/media/shared/preview.png)

## <a name="embedded-widgetkit-support"></a>Embedded WidgetKit 支持

此版本的 SDK 增加了对嵌入到主 Xamarin iOS 应用程序的 WidgetKit 扩展的支持。 这使你可以立即生成具有小组件支持的应用。

使用此方法创建 "混合" 应用程序，使用 SwiftUI 生成小组件扩展，并将其嵌入到 Xamarin iOS 应用程序中。

利用 WidgetKit 支持将需要对项目文件进行一些手动更改。

向你的项目添加如下所示的节：

```xml
<AdditionalAppExtensions Include="$(MSBuildProjectDirectory)/../../native">
     <Name>NativeTodayExtension</Name>
     <BuildOutput Condition="'$(Platform)' == 'iPhone'">build/Debug-iphoneos</BuildOutput>
     <BuildOutput Condition="'$(Platform)' == 'iPhoneSimulator'">build/Debug-iphonesimulator</BuildOutput>
</AdditionalAppExtensions>
```

更改第一个链接中包含的路径，使其指向 Swift UI 扩展的生成目录。

若要在 Xcode 项目中启用项目相对输出位置 (文件→项目设置) 以获得更简单的路径来查找，这可能会有所帮助：

![Xcode 设置](ios14-images/xcode-settings.png)

此 [示例应用程序](https://github.com/chamons/xamarin-ios-swift-extension/blob/master/App/TestApplication/TestApplication.csproj#L143) 使用 JSON 序列化将数据从 Xamarin iOS 应用程序传输到用于显示的示例小组件。

对 WidgetKit 感兴趣的人会在 [此处提供反馈](https://github.com/xamarin/xamarin-macios/issues/8933)。

## <a name="related-links"></a>相关链接

- [Xamarin 14 发行说明](/xamarin/ios/release-notes/14/14.0)
- [UIColorWell 文档](https://developer.apple.com/documentation/uikit/uicolorwell)
