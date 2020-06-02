---
title: ''
description: 本文说明如何在 Xamarin.Forms 应用程序中使用轻击手势进行轻击检测。 可通过 TapGestureRecognizer 类实现点击检测。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0470419dd5070424c362dec8d4b1978507985783
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137613"
---
# <a name="adding-a-tap-gesture-recognizer"></a>添加点击手势识别器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_点击手势用于点击检测，可通过 TapGestureRecognizer 类实现。_

若要让某个用户界面元素可以使用点击手势进行点击，请创建 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 实例，处理 [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) 事件，并将新的手势识别器添加到该用户界面元素上的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 以下代码示例展示了附加到 [`Image`](xref:Xamarin.Forms.Image) 元素的 `TapGestureRecognizer`：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

默认情况下，该图像将响应单击。 请将 [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) 属性设置为等待双击（必要时可设置为等待更多次点击）。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

当 [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) 设置为高于 1 时，只有在设定的时间段（此时间段不可配置）内进行点击时才会执行事件处理程序。 如果在该时间段内没有进行第二次（或后续）点击，系统会有效地忽略它们并重新启动“点击计数”。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 XAML

可以使用附加属性将手势识别器添加到 XAML 中的控件。 向图像添加 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 的语法如下所示（在本例中定义了*双击*事件）：

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

事件处理程序的代码（在示例中）对计数器进行递增，并将图像从彩色更改为黑色和白色&amp;。

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>使用 ICommand

使用模型-视图-视图模型 (MVVM) 模式的应用程序通常使用 `ICommand`，而不是直接绑定事件处理程序。 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 可轻松地支持 `ICommand`，方法为在代码中设置绑定：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

或使用 XAML：

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

可以在示例中找到此视图模型的完整代码。 相关的 `Command` 实现细节如下所示：

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>相关链接

- [TapGesture（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
