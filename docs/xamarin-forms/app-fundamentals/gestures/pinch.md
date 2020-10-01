---
title: 添加收缩手势识别器
description: 本文介绍如何使用捏合手势在捏合位置对图像进行交互式缩放。
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb471787a4b3304be3df008fc3ecea66ff8263b3
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560983"
---
# <a name="add-a-pinch-gesture-recognizer"></a>添加收缩手势识别器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

捏合手势用于执行交互式缩放，并通过 PinchGestureRecognizer 类实现。捏合手势的一个常见场景是在捏合位置对图像进行交互式缩放。这是通过缩放视图的内容来实现的，本文将对此进行演示。

若要让用户界面元素可以使用捏合手势进行缩放，请创建 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 实例，处理 [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) 事件，并将新的手势识别器添加到用户界面元素上的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 以下代码示例展示了附加到 [`Image`](xref:Xamarin.Forms.Image) 元素的 `PinchGestureRecognizer`：

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

这也可以在 XAML 中实现，如下面的代码示例所示：

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

然后将 `OnPinchUpdated` 事件处理程序的代码添加到代码隐藏文件中：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>创建 PinchToZoom 容器

处理捏合手势来执行缩放操作需要一些数学知识运算来转换用户界面。 本节包含一个通用的帮助程序类来执行数学运算，它可以用于交互式地缩放任何用户界面元素。 以下代码示例演示 `PinchToZoomContainer` 类：

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

可以将该类包装在用户界面元素周围，这样捏合手势就可以缩放包装的用户界面元素。 以下 XAML 代码示例介绍用于包装 [`Image`](xref:Xamarin.Forms.Image) 元素的 `PinchToZoomContainer`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以下代码示例展示了 `PinchToZoomContainer` 如何在 C# 页面中包装 [`Image`](xref:Xamarin.Forms.Image) 元素：

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

当 [`Image`](xref:Xamarin.Forms.Image) 元素接收到捏合手势时，显示的图像将被放大或缩小。缩放由 `PinchZoomContainer.OnPinchUpdated` 方法执行，如下面的代码示例所示：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

该方法根据用户的捏合手势更新包装的用户界面元素的缩放级别。 这是通过使用 [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) 实例的 [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale)、[`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) 和 [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) 属性的值来计算要应用于捏合手势原点的比例因子来实现的。 然后，通过将包装的用户元素的 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)、[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 和 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性设置为计算值，将其缩放到捏合手势的原点。

## <a name="related-links"></a>相关链接

- [PinchGesture（示例）](/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)