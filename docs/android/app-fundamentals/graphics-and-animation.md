---
title: 图形和动画
description: Android 提供了一个非常丰富的多样框架，用于支持2D 图形和动画。 本主题介绍了这些框架，并讨论了如何创建自定义图形和动画以便在 Xamarin Android 应用程序中使用。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 41ebc8b1f89d814fa15fc3157ae497e1e8fb32b4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456129"
---
# <a name="android-graphics-and-animation"></a>Android 图形和动画

_Android 提供了一个非常丰富的多样框架，用于支持2D 图形和动画。本主题介绍了这些框架，并讨论了如何创建自定义图形和动画以便在 Xamarin Android 应用程序中使用。_

## <a name="overview"></a>概述

尽管在传统上运行的设备上运行，但最高等级的移动应用程序通常具有复杂的用户体验 (UX) ，具有高质量的图形和动画，可提供直观、响应迅速的动态感觉。 随着移动应用程序越来越复杂，用户已经开始从应用程序中获得越来越多的应用程序。

幸运的是，新式移动平台提供了非常强大的框架，可用于创建复杂的动画和自定义图形，同时保持易用性。 这使开发人员能够轻松地添加丰富的交互性。

Android 中的 UI API 框架可以大致分为两个类别：图形和动画。

图形会进一步分成2D 和3D 图形的不同方法。 3D 图形可通过多种内置框架提供，例如 OpenGL ES (OpenGL) 的移动特定版本、第三方框架（如 MonoGame (与工具包) 兼容的跨平台工具包。 虽然三维图形不在本文的讨论范围内，但我们会检查内置的2D 绘图技术。

Android 提供两个用于创建2D 图形的不同 API。 一种是高级声明性方法，另一种是编程低级别 API：

- **绘制资源** &ndash; 这些方法用于以编程方式创建自定义图形，或通过在 XML 文件中嵌入绘图指令 (更常见的) 。 可绘制资源通常定义为 XML 文件，其中包含 Android 用于呈现2D 图形的指令或操作。 

- **画布** &ndash; 这是一种低级别的 API，涉及直接在基础位图上绘图。 它提供对显示内容的精细控制。 

除了这些2D 图形技术以外，Android 还提供了几种不同的方法来创建动画：

- **绘制动画** &ndash; Android 还支持称为可 *绘制动画*的逐帧动画。 这是最简单的动画 API。 Android 按顺序加载并显示序列化的资源， (非常类似于卡通) 。

- **查看动画** &ndash;*视图动画*是 android 中的原始动画 API，适用于所有版本的 android。 此 API 只适用于视图对象，只能对这些视图执行简单转换。
    视图动画通常是在文件夹中找到的 XML 文件中定义的 `/Resources/anim` 。

- **属性动画** &ndash; Android 3.0 引入了一组新的动画 API，称为 *属性动画*。 这些新 API 引入了一个可扩展的灵活系统，可用于对任何对象的属性进行动画处理，而不仅仅是查看对象。 这种灵活性允许将动画封装在不同的类中，使代码共享变得更加容易。

视图动画更适用于必须支持较旧的 Android 3.0 API (API level 11) 的应用程序。 否则，应用程序应使用较新的属性动画 API，因为以上所述的原因。

所有这些框架都是可行的选项，但在可能的情况下，应该为属性动画提供首选项，因为它是一个更灵活的 API。 属性动画允许将动画逻辑封装在不同的类中，使代码共享变得更简单，并简化代码维护。

## <a name="accessibility"></a>可访问性

图形和动画有助于使 Android 应用更引人注目且有趣但是，请务必记住，某些交互是通过阅读器、备用输入设备或辅助缩放发生的。
此外，某些交互可能发生，但没有音频功能。

如果应用在设计时考虑了可访问性，则在这些情况下，应用更易于使用：在用户界面中提供提示和导航帮助，并确保为 UI 的图形化元素提供文本内容或说明。

有关如何使用 Android 辅助功能 Api 的详细信息，请参阅 [Google 的辅助功能指南](https://developer.android.com/guide/topics/ui/accessibility/) 。

## <a name="2d-graphics"></a>2D 图形

可绘制资源是 Android 应用程序中的一种常用技术。 与其他资源一样，可绘制资源是 &ndash; 在 XML 文件中定义的。 此方法允许从资源中完全分离代码。 这可以简化开发和维护，因为无需更改代码即可更新或更改 Android 应用程序中的图形。 但是，尽管可绘制资源对许多简单和常见图形要求都很有用，但它们缺乏画布 API 的强大功能和控制。

使用 [Canvas](xref:Android.Graphics.Canvas) 对象的另一种方法非常类似于其他传统 API 框架，如 System.object 或 IOS 的核心绘制。 使用 Canvas 对象可以最大程度地控制2D 图形的创建方式。 这适用于可绘制资源不起作用或难以使用的情况。 例如，可能需要绘制一个自定义滑块控件，其外观将根据与滑块的值相关的计算而更改。

首先，让我们检查可绘制资源。 它们较为简单，涵盖最常见的自定义绘图事例。

### <a name="drawable-resources"></a>绘制资源

可绘制资源是在目录的 XML 文件中定义的 `/Resources/drawable` 。 与嵌入 PNG 或 JPEG 不同，并不需要提供特定于密度的可绘制资源版本。
在运行时，Android 应用程序将加载这些资源，并使用这些 XML 文件中包含的指令创建2D 图形。
Android 定义了几种不同类型的 "绘制资源"：

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; 这是一个可绘制的对象，用于绘制基元几何形状，并对该形状应用有限的图形效果集。 它们对于自定义按钮或设置 TextViews 背景非常有用。 本文稍后将介绍如何使用的示例 `ShapeDrawable` 。

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; 这是一个可绘制的资源，将根据小组件/控件的状态更改外观。 例如，按钮可能会根据是否已按下而更改其外观。

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; 这是一种可绘制的资源，它将多个绘图。 以下屏幕截图显示了 *LayerDrawable* 的示例：

    ![LayerDrawable 示例](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; 这是一个 *LayerDrawable* ，但有一个不同之处。 *TransitionDrawable*可以使一层上的显示在另一个层上。

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; 这非常类似于 *StateListDrawable* ，因为它会根据特定条件显示图像。 但是，与 *StateListDrawable*不同， *LevelListDrawable* 会根据整数值显示图像。 *LevelListDrawable*的一个示例就是显示 WiFi 信号的强度。 当 WiFi 信号的强度改变时，显示的可绘制将相应更改。

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale) /[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;顾名思义，这些绘图提供了缩放和剪裁功能。 *ScaleDrawable*将缩放其他可绘制的，而*ClipDrawable*将剪裁另一个可绘制的。

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; 此可绘制的将应用于其他可绘制资源的两侧。 当视图需要的背景小于视图的实际边界时，可使用此方法。

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; 此文件是一组在 XML 中对实际位图执行的指令。 Android 可以执行的某些操作包括平铺、抖动和抗锯齿。 这种情况的一个常见用途是在布局的背景上平铺位图。

#### <a name="drawable-example"></a>绘制示例

让我们看一下如何使用创建2D 图形的快速示例 `ShapeDrawable` 。 `ShapeDrawable`可以定义四个基本形状之一：矩形、椭圆、直线和环。 还可以应用基本效果，如渐变、颜色和大小。 下面的 XML 是在 `ShapeDrawable` *AnimationsDemo* 伴生项目 (的文件) 中找到的 `Resources/drawable/shape_rounded_blue_rect.xml` 。
它定义带有紫色渐变背景和圆角的矩形：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

可以在布局或其他可绘制的视图中以声明方式引用此可绘制资源，如以下 XML 所示：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

还可以通过编程方式应用可绘制资源。 下面的代码段演示如何以编程方式设置 TextView 的背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看它的外观，请运行 *AnimationsDemo* 项目，然后从主菜单中选择形状可绘制项。 应该会看到类似于以下屏幕截图的内容：

[![使用自定义背景的 Textview，使用渐变和圆角进行绘制](graphics-and-animation-images/image2-sml.png)](graphics-and-animation-images/image2.png#lightbox)

若要详细了解如何绘制资源的 XML 元素和语法，请参阅 [Google 文档](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。

### <a name="using-the-canvas-drawing-api"></a>使用画布绘图 API

绘图具有强大的功能，但有其局限性。 某些事情可能是不可能的，也可能非常复杂 (例如：将筛选器应用于设备) 上的照相机拍摄的图片。 使用可绘制资源来应用红眼降低会非常困难。
相反，画布 API 允许应用程序进行极精细的控制，以便有选择地更改图片特定部分中的颜色。

通常与画布一起使用的一个类是 " [Paint](xref:Android.Graphics.Paint) 类"。 此类包含有关如何绘制的颜色和样式信息。 它用于提供颜色和透明度等功能。

画布 API 使用 *刷模型* 绘制2d 图形。
操作在彼此之上的相邻层中应用。 每个操作都将涵盖基础位图的某个区域。 当区域重叠先前绘制的区域时，新的 "画图" 将部分或完全遮蔽旧的。 这与其他许多绘图 Api （如 System.object 和 iOS 的核心图形）的工作方式相同。

可通过两种方式获取 `Canvas` 对象。 第一种方法涉及定义 [位图](xref:Android.Graphics.Bitmap) 对象，然后使用该对象实例化 `Canvas` 对象。 例如，以下代码片段创建了一个具有基础位图的新画布：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

获取对象的另一种方法 `Canvas` 是提供[视图](xref:Android.Views.View)基类的[OnDraw](xref:Android.Views.View.OnDraw*)回调方法。 当该方法决定视图需要自行绘制并传入对象以使视图可以使用时，Android 会调用此方法 `Canvas` 。

Canvas 类公开方法，以编程方式提供绘制指令。 例如：

- [DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*) &ndash; 用指定的 paint 填充整个画布的位图。

- [DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash; 使用指定的绘制绘制指定的几何形状。

- [DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash; 用指定的颜色在画布上绘制文本。 在位置绘制文本 `x,y` 。

#### <a name="drawing-with-the-canvas-api"></a>用画布 API 绘制

下面是一个操作中画布 API 的示例。 下面的代码段演示如何绘制视图：

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

上面的代码首先创建一个红色的 paint 和一个绿色的 paint 对象。 它使用红色填充画布的内容，然后指示画布绘制一个绿色矩形，该矩形是画布宽度的25%。 本文的源代码中包含的项目中显示了这种情况的一个示例 `AnimationsDemo` 。 通过启动应用程序并从主菜单中选择 "绘制项"，应显示类似于下面的屏幕：

[![带有红色画图和绿色画图对象的屏幕](graphics-and-animation-images/image3-sml.png)](graphics-and-animation-images/image3.png#lightbox)

## <a name="animation"></a>动画

用户喜欢在其应用程序中移动的东西。 动画是改善应用程序的用户体验并帮助其突出的好方法。最好的动画是用户不会注意的，因为它们的外观非常简单。 Android 为动画提供了以下三个 API：

- **查看动画** &ndash; 这是原始 API。 这些动画与特定视图相关联，并且可以对视图内容执行简单转换。 由于这种做法很简单，因此，此 API 对于 alpha 动画、旋转等操作仍然很有用。

- **属性动画** &ndash; Android 3.0 中引入了属性动画。 它们使应用程序可以对几乎任何内容进行动画处理。 属性动画可用于更改任何对象的任何属性，即使该对象在屏幕上不可见。

- **绘制动画** &ndash; 这是一种特殊的可绘制资源，用于将非常简单的动画效果应用于布局。

通常，属性动画是要使用的首选系统，因为它更灵活，并提供更多功能。

### <a name="view-animations"></a>查看动画

视图动画仅限视图，并且只能对值（如开始点和终点、大小、旋转和透明度）执行动画。
这些类型的动画通常称为 *补间动画*。 可以通过 &ndash; 编程方式在代码中或使用 XML 文件以编程方式定义视图动画。 XML 文件是声明视图动画的首选方法，因为它们更具可读性且更易于维护。

动画 XML 文件将存储在 `/Resources/anim` Xamarin Android 项目的目录中。 此文件必须具有以下元素之一作为根元素：

- `alpha`&ndash;淡入或淡出动画。

- `rotate`&ndash;旋转动画。

- `scale`&ndash;调整动画大小。

- `translate`&ndash;水平和/或垂直动作。

- `set`&ndash;可以容纳一个或多个其他动画元素的容器。

默认情况下，将同时应用 XML 文件中的所有动画。 若要使动画按顺序发生，请 `android:startOffset` 在上面定义的一个元素上设置属性。

使用 *插值*可以影响动画中的变化速率。 使用插插程序，可以加速、重复或 decelerated 动画效果。 Android framework 提供了几个现成的 interpolators，例如 (，但不限于) ：

- `AccelerateInterpolator`/ `DecelerateInterpolator` &ndash;这些 interpolators 提高或降低动画的变化速率。

- `BounceInterpolator`&ndash;更改将在末尾弹跳。

- `LinearInterpolator`&ndash;更改速率是固定的。

下面的 XML 显示了合并其中一些元素的动画文件示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

此动画将同时执行所有动画。 第一个缩放动画将水平拉伸图像并将其垂直收缩，然后，图像将沿顺时针方向旋转45度，并从屏幕上消失。

可以通过因为这样做动画并将其应用到视图，以编程方式将动画应用于视图。 Android 提供帮助器类 `Android.Views.Animations.AnimationUtils` ，它会使动画资源膨胀并返回的实例 `Android.Views.Animations.Animation` 。 此对象通过调用 `StartAnimation` 并传递对象应用于视图 `Animation` 。 下面的代码片段演示了一个示例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

现在，我们对视图动画的工作方式有了基本的了解，接下来请转到属性动画。

### <a name="property-animations"></a>属性动画

属性 animators 是 Android 3.0 中引入的新 API。
它们提供了更可扩展的 API，可用于对任何对象上的任何属性进行动画处理。

所有属性动画都由 [Animator](xref:Android.Animation.Animator) 子类的实例创建。 应用程序不直接使用此类，而是使用它的一个子类：

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; 此类是整个属性动画 API 中最重要的类。 它计算需要更改的属性的值。 不 `ViewAnimator` 会直接更新这些值，而是引发可用于更新动画对象的事件。

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) &ndash; 此类是的子类 `ValueAnimator` 。 它旨在通过接受要更新的目标对象和属性来简化对象的动画处理过程。

- [AnimationSet](xref:Android.Animation.AnimatorSet) &ndash; 此类负责协调动画的相互关系。 动画可以按顺序同时运行，也可以在它们之间的指定延迟时间运行。

*计算器* 是 animators 用来计算动画过程中的新值的特殊类。 Android 提供以下计算器：

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; 计算整数属性的值。

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; 计算 float 属性的值。

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; 计算颜色属性的值。

如果要进行动画处理的属性不是 `float` `int` 或颜色，应用程序可以通过实现接口来创建自己的计算器 `ITypeEvaluator` 。  (实现自定义计算器超出了本主题的讨论范围。 ) 

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

任何动画都有两个部分：计算动画值，然后在某个对象的属性上设置这些值。 
[ValueAnimator](xref:Android.Animation.ValueAnimator) 将只计算这些值，但不会直接对对象进行操作。 相反，对象会在事件处理程序中进行更新，这些事件处理程序将在动画生命周期中调用。 此设计允许从一个动画值更新多个属性。

您可以 `ValueAnimator` 通过调用以下工厂方法之一来获取的实例：

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

完成此操作后， `ValueAnimator` 实例必须设置其持续时间，然后才能启动。 下面的示例演示如何在1000毫秒的范围内对0到1之间的值进行动画处理：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

但是，上面的代码片段并不是非常有用， &ndash; animator 将运行，但不存在更新值的目标。 `Animator`当类确定需要向侦听器通知新值时，该类将引发 Update 事件。 应用程序可以提供事件处理程序来响应此事件，如以下代码片段所示：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

现在我们已经了解了，接下来 `ValueAnimator` 了解更多相关信息 `ObjectAnimator` 。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) 是的一个子类 `ViewAnimator` ，它将的计时引擎和值计算 `ValueAnimator` 与绑定事件处理程序所需的逻辑结合起来。 `ValueAnimator`要求应用程序显式连接事件处理程序 &ndash; `ObjectAnimator` 将为我们执行此步骤。

的 API `ObjectAnimator` 非常类似于的 api `ViewAnimator` ，但要求你提供对象以及要更新的属性的名称。 下面的示例演示使用的示例 `ObjectAnimator` ：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

如前面的代码片段所示， `ObjectAnimator` 可以减少和简化对对象进行动画处理所需的代码。

### <a name="drawable-animations"></a>绘制动画

最终的动画 API 是可绘制的动画 API。 可绘制动画将一系列可绘制资源逐个加载并按顺序显示，类似于 "翻转"。

可绘制资源在一个 XML 文件中定义，该文件将 `<animation-list>` 元素作为根元素，并使用一系列 `<item>` 元素来定义动画中的每个帧。 此 XML 文件存储在 `/Resource/drawable` 应用程序的文件夹中。 以下 XML 是可绘制动画的示例：

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

此动画将通过六帧运行。 `android:duration`特性声明每个帧的显示时间长度。 下面的代码片段演示了一个示例，演示如何创建可绘制动画并在用户单击屏幕上的按钮时启动它：

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

此时，我们已介绍 Android 应用程序中提供的动画 Api 的基础知识。

## <a name="summary"></a>总结

本文介绍了许多新概念和 API，有助于将一些图形添加到 Android 应用程序。 首先，它讨论了各种2D 图形 API，并演示了 Android 如何允许应用程序使用 Canvas 对象直接在屏幕上进行绘制。 我们还看到了一些替代方法，允许使用 XML 文件以声明方式创建图形。 接下来，我们讨论了用于在 Android 中创建动画的新 API 和新 API。

## <a name="related-links"></a>相关链接

- [动画演示 (示例) ](/samples/xamarin/monodroid-samples/animationdemo)
- [动画和图形](https://developer.android.com/guide/topics/graphics/index.html)
- [使用动画将你的移动应用带入现实](https://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [画布](xref:Android.Graphics.Canvas)
- [对象 Animator](xref:Android.Animation.ObjectAnimator)
- [值 Animator](xref:Android.Animation.ValueAnimator)