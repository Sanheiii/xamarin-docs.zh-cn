---
title: 自定义动画 Xamarin.Forms
description: 本文演示如何使用 Xamarin 动画类来创建和取消动画、同步多个动画以及创建自定义动画，以对现有动画方法未进行动画处理的属性进行动画处理。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3731f35ab03edf9b65c3adca7a2091c2a609b552
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563388"
---
# <a name="custom-animations-in-no-locxamarinforms"></a>自定义动画 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_动画类是所有动画的构建基块 Xamarin.Forms ，ViewExtensions 类中的扩展方法可创建一个或多个动画对象。本文演示如何使用动画类来创建和取消动画，同步多个动画，以及创建自定义动画，以对未被现有动画方法进行动画处理的属性进行动画处理。_

创建对象时必须指定多个参数 `Animation` ，包括正在进行动画处理的属性的开始值和结束值，以及更改属性值的回调。 `Animation`对象还可以维护可运行和同步的子动画的集合。 有关详细信息，请参阅 [子动画](#child-animations)。

若要运行使用类创建的动画 [`Animation`](xref:Xamarin.Forms.Animation) （可能包含或不包括子动画），可以通过调用 [ `Commit` ] (x： (来实现 Xamarin.Forms Xamarin.Forms 。System.windows.media.animation.ianimatable>，System.string，system.web，， Xamarin.Forms 。缓动、System.object {system.exception}、system.exception {system.string} ) # A3 方法。） 此方法指定动画的持续时间，并指定其他项中的一个回调，该回调控制是否重复动画。

此外， [`Animation`](xref:Xamarin.Forms.Animation) 类还提供了一个 `IsEnabled` 属性，可对其进行检查，以确定操作系统是否已禁用动画（例如，当电源节能模式处于激活状态时）。

## <a name="create-an-animation"></a>创建动画

创建对象时 [`Animation`](xref:Xamarin.Forms.Animation) ，通常需要至少三个参数，如以下代码示例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此代码定义 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 实例的属性 [`Image`](xref:Xamarin.Forms.Image) 从值1到值2的动画。 派生的动画值 Xamarin.Forms 被传递给指定为第一个参数的回调，该回调用于更改属性的值 `Scale` 。

通过调用 [ `Commit` ] (x： (来启动动画。 Xamarin.Forms Xamarin.FormsSystem.windows.media.animation.ianimatable>，System.string，system.web，， Xamarin.Forms 。如以下代码示例中所示，"缓动"、"system.string"、"system.string"、"system.string" ) # A3 方法：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

请注意，[ `Commit` ] (x Xamarin.Forms (Xamarin.Forms 。System.windows.media.animation.ianimatable>，System.string，system.web，， Xamarin.Forms 。缓动、System.object {system.exception、system.object} ) # A3 方法不会返回对象。 "）" `Task` 。 相反，通过回调方法提供通知。

方法中指定了以下参数 `Commit` ：

- 第一个参数 (*所有者*) 标识动画的所有者。 这可以是应用动画的视觉元素，也可以是另一个可视元素（如页面）。
- 第二个参数 (*名称*) 标识带有名称的动画。 该名称与用于唯一标识动画的所有者组合在一起。 然后，可以使用此唯一标识来确定动画是否正在 ( [ `AnimationIsRunning` ] (x：运行 Xamarin.Forms 。AnimationExtensions. AnimationIsRunning (Xamarin.Forms 。System.windows.media.animation.ianimatable>、System.string) # A4 # A5，或将其取消 ( [ `AbortAnimation` ] (x： Xamarin.Forms 。AnimationExtensions. AbortAnimation (Xamarin.Forms 。System.windows.media.animation.ianimatable>，System.string) # A10 # A11。
- 第三个参数 (*速率*) 指示每次调用构造函数中定义的回调方法之间的毫秒数 [`Animation`](xref:Xamarin.Forms.Animation) 。
- 第四个参数 (*长度*) 指示动画的持续时间（以毫秒为单位）。
- 第五个参数 (*缓动*) 定义要在动画中使用的缓动函数。 或者，可将缓动函数指定为构造函数的参数 [`Animation`](xref:Xamarin.Forms.Animation) 。 有关缓动函数的详细信息，请参阅 [缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。
-  (*完成* 的第六个参数) 是在动画完成后将执行的回调。 此回调采用两个参数，第一个参数指示最终值，第二个参数 `bool` 设置为（ `true` 如果动画已取消）。 此外，还可以将 *已完成* 的回调指定为 [`Animation`](xref:Xamarin.Forms.Animation) 构造函数的参数。 但是，对于单个动画，如果在构造函数和方法中同时指定了 *完成* `Animation` 的回调，则只会 `Commit` 执行方法中指定的回调 `Commit` 。
- 第七个参数 (*重复*) 是允许重复动画的回调。 它在动画结束时调用，并返回 `true` 指示动画应重复。

总体效果是使用缓动函数创建一个动画，将的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性 [`Image`](xref:Xamarin.Forms.Image) 从1增加到2（超过2秒 (2000 毫秒) ） [`Linear`](xref:Xamarin.Forms.Easing.Linear) 。 每次动画完成后，其 `Scale` 属性将重置为1，动画将重复。

> [!NOTE]
> 可以通过 `Animation` 为每个动画创建一个对象，然后 `Commit` 在每个动画上调用方法，来构造并发动画（独立运行）。

### <a name="child-animations"></a>子动画

[`Animation`](xref:Xamarin.Forms.Animation)类还支持子动画，子动画涉及到创建 `Animation` 其他对象要添加到的对象 `Animation` 。 这样，便可以运行和同步一系列动画。 下面的代码示例演示如何创建和运行子动画：

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

或者，可以更简洁地编写代码示例，如下面的代码示例所示：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在这两个代码示例中，将创建一个父 [`Animation`](xref:Xamarin.Forms.Animation) 对象， `Animation` 然后向其中添加其他对象。 [ `Add` ] (x：的前两个参数 Xamarin.Forms 。动画。添加 (。双精度 Xamarin.Forms 型。动画) # A3 方法指定开始和完成子动画的时间。 参数值必须介于0和1之间，表示指定子动画将处于活动状态的父动画内的相对时间段。 因此，在此示例中，将在动画的前半 `scaleUpAnimation` 部分处于活动状态，对于动画的后半部分将处于活动状态， `scaleDownAnimation` 并且将在 `rotateAnimation` 整个持续时间内处于活动状态。

总体效果是，动画出现 (4000 毫秒) 。 在 `scaleUpAnimation` [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 2 秒内对属性进行动画处理，从1到2。 `scaleDownAnimation`然后 `Scale` ，在2秒内对属性进行动画处理，从2到1。 当同时出现这两种缩放动画时，将在 `rotateAnimation` [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 4 秒内对属性进行动画处理，范围为0至360。 请注意，缩放动画也使用缓动函数。 [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)缓动函数使在 [`Image`](xref:Xamarin.Forms.Image) 获取更大值之前开始进行收缩， [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 缓动函数会使在 `Image` 整个动画结束时变得小于其实际大小。

[`Animation`](xref:Xamarin.Forms.Animation)使用子动画的对象与不使用子动画的对象有很多差异：

- 使用子动画时，子动画上 *完成* 的回调将指示子动画完成的时间，并且传递给该方法的 *已完成* 回调将 `Commit` 指示整个动画完成的时间。
- 使用子动画时， `true` 从方法的 *重复* 回调中返回 `Commit` 将不会导致动画重复，但动画将继续运行，而不会产生新值。
- 如果在方法中包含缓动函数 `Commit` ，并且缓动函数返回一个大于1的值，则将终止动画。 如果缓动函数返回小于0的值，则将值限制为0。 若要使用返回小于0或大于1的值的缓动函数，则必须在其中一个子动画（而不是方法）中指定该值 `Commit` 。

[`Animation`](xref:Xamarin.Forms.Animation)类还包括 [ `WithConcurrent` ] (x： Xamarin.Forms 。WithConcurrent (Xamarin.Forms 。动画、双头、system.exception) # A3 方法，可用于将子动画添加到父 `Animation` 对象。 但是，它们的 *开始* 和 *结束* 参数值不会限制为0到1，但只有对应于0到1范围的子动画部分才会处于活动状态。 例如，如果一个 `WithConcurrent` 方法调用定义的子动画的目标为 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 1 到6之间的一个属性，但其 *begin* 和 *finish* 值为-2 和3，则-2 的 *开始* 值对应于 `Scale` 值1，而3的 *完成* 值对应于 `Scale` 值6。 由于不在0和1范围内的值在动画中的任何部分都不起作用，因此 `Scale` 属性将仅从3到6进行动画处理。

## <a name="cancel-an-animation"></a>取消动画

应用程序可以通过调用 [ `AbortAnimation` ] (x：来取消动画 Xamarin.Forms 。AnimationExtensions. AbortAnimation (Xamarin.Forms 。System.windows.media.animation.ianimatable>、System.string) # A3 扩展方法，如以下代码示例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

请注意，动画由动画所有者和动画名称的组合唯一标识。 因此，在运行动画时指定的所有者和名称必须指定为取消动画。 因此，该代码示例将立即取消 `SimpleAnimation` 由页面拥有的名为的动画。

## <a name="create-a-custom-animation"></a>创建自定义动画

到目前为止，这里显示的示例演示了使用类中的方法可以同样实现的动画 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 但是，类的优点 [`Animation`](xref:Xamarin.Forms.Animation) 是它有权访问回调方法，该方法是在动画值更改时执行的。 这允许回调实现任何所需的动画。 例如，下面的代码示例 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 通过将页面的属性设置为方法创建的值来对其进行动画 [`Color`](xref:Xamarin.Forms.Color) [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) 处理，色相值范围从0到1：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

生成的动画提供了通过彩虹的颜色使页面背景前进的外观。

有关创建复杂动画的更多示例，包括贝塞尔曲线动画，请参阅[创建使用 Xamarin.Forms 的移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)的第[22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)。

## <a name="create-a-custom-animation-extension-method"></a>创建自定义动画扩展方法

类中的扩展方法将 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 属性从其当前值动态到指定的值。 例如，这样做很难创建 `ColorTo` 动画方法，该方法可用于从一个值向另一个值对颜色进行动画处理，因为：

- [`Color`](xref:Xamarin.Forms.Color)类定义的唯一属性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 是 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) ，这并不总是 `Color` 要进行动画处理的所需属性。
- 通常，属性的当前值 [`Color`](xref:Xamarin.Forms.Color) 为 [`Color.Default`](xref:Xamarin.Forms.Color.Default) ，而不是实际颜色，并且不能在内插计算中使用。

此问题的解决方法是不让方法以 `ColorTo` 特定属性为目标 [`Color`](xref:Xamarin.Forms.Color) 。 相反，可以使用回调方法编写该方法，该方法将内插 `Color` 值传递回调用方。 此外，该方法将采用开始和结束 `Color` 参数。

`ColorTo`方法可以实现为扩展方法，该方法使用 [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) 类中的方法 [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) 来提供其功能。 这是因为 `Animate` ，可以使用方法来面向不属于类型的属性 `double` ，如下面的代码示例所示：

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

此 [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) 方法需要一个 *转换* 参数，该参数是一个回调方法。 此回调的输入始终是 `double` 从0到1的范围。 因此，该 `ColorTo` 方法定义了其自己的转换， `Func` 该转换接受 `double` 范围从0到1的，并返回与该值 [`Color`](xref:Xamarin.Forms.Color) 对应的值。 `Color`值是通过对 [`R`](xref:Xamarin.Forms.Color.R) [`G`](xref:Xamarin.Forms.Color.G) 提供的 [`B`](xref:Xamarin.Forms.Color.B) [`A`](xref:Xamarin.Forms.Color.A) 两个参数的、、和值 `Color` 进行插值计算得出的。 然后，将 `Color` 值传递给特定属性的应用程序的回调方法。

此方法允许 `ColorTo` 方法对任何属性进行动画处理 [`Color`](xref:Xamarin.Forms.Color) ，如下面的代码示例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此代码示例中， `ColorTo` 方法对的 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 和 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 属性 [`Label`](xref:Xamarin.Forms.Label) 、 `BackgroundColor` 页的属性和的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 属性 [`BoxView`](xref:Xamarin.Forms.BoxView) 进行动画处理。

## <a name="related-links"></a>相关链接

- [ (示例的自定义动画) ](/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [动画 API](xref:Xamarin.Forms.Animation)
- [AnimationExtensions API](xref:Xamarin.Forms.AnimationExtensions)