---
title: Xamarin.Forms 形状：路径转换
description: Xamarin.Forms转换定义如何将路径对象从一个坐标空间转换到另一个坐标空间。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6bc61eadb40a66c278955936e15158f34709fd18
ms.sourcegitcommit: aeefe667156ca8d3ee2ee21d9bec0fa629589e33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90928650"
---
# <a name="no-locxamarinforms-shapes-path-transforms"></a>Xamarin.Forms 形状：路径转换

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Transform`定义如何将 `Path` 对象从一个坐标空间转换到另一个坐标空间。 将转换应用于 `Path` 对象时，它会更改对象在 UI 中的呈现方式。

转换可以分为四个通用分类：旋转、缩放、倾斜和平移。 Xamarin.Forms 为以下每种转换分类定义一个类：

- `RotateTransform`，它 `Path` 通过指定的旋转 `Angle` 。
- `ScaleTransform`，它 `Path` 按指定 `ScaleX` 和量缩放对象 `ScaleY` 。
- `SkewTransform`，它 `Path` 按指定 `AngleX` 和量倾斜对象 `AngleY` 。
- `TranslateTransform`，它 `Path` 按指定 `X` 和数量移动对象 `Y` 。

Xamarin.Forms 还提供了以下类来创建更复杂的转换：

- `TransformGroup`，它表示由多个转换对象组成的复合转换。
- `CompositeTransform`，它将多个转换操作应用到一个 `Path` 对象。
- `MatrixTransform`，它创建其他转换类未提供的自定义转换。

所有这些类均派生自 `Transform` 类，该类定义类型为 `Value` 的属性，该属性 `Matrix` 表示当前转换为 `Matrix` 对象。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。 有关结构的详细信息 `Matrix` ，请参阅 [变换矩阵](#transform-matrix)。

若要将转换应用到 `Path` ，可以创建转换类并将其设置为属性的值 `Path.RenderTransform` 。

## <a name="rotation-transform"></a>旋转转换

旋转变换围绕 `Path` 二维 x-y 坐标系统中的指定点顺时针旋转对象。

`RotateTransform`派生自类的类 `Transform` 定义以下属性：

- `Angle`类型为的， `double` 表示顺时针旋转的角度（以度为单位）。 此属性的默认值为0.0。
- `CenterX`类型为的， `double` 表示旋转中心点的 x 坐标。 此属性的默认值为0.0。
- `CenterY`类型为的， `double` 表示旋转中心点的 y 坐标。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`CenterX`和 `CenterY` 属性指定对象要旋转的点 `Path` 。 此中心点在转换对象的坐标空间中表示。 默认情况下，旋转 (0，0) ，这是对象的左上角 `Path` 。

下面的示例演示如何旋转 `Path` 对象：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象围绕其左上角旋转了45度。

## <a name="scale-transform"></a>缩放转换

缩放转换可缩放 `Path` 2d x-y 坐标系统中的对象。

`ScaleTransform`派生自类的类 `Transform` 定义以下属性：

- `ScaleX`，类型为 `double` ，它表示 x 轴缩放比例。 此属性的默认值为1.0。
- `ScaleY`，类型为 `double` ，它表示 y 轴的缩放比例。 此属性的默认值为1.0。
- `CenterX`，类型为 `double` ，表示此变换中心点的 x 坐标。 此属性的默认值为0.0。
- `CenterY`，类型为 `double` ，表示此转换的中心点的 y 坐标。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

和的值 `ScaleX` 对 `ScaleY` 生成的缩放产生大的影响：

- 介于0和1之间的值将减小缩放对象的宽度和高度。
- 大于1的值将增加缩放对象的宽度和高度。
- 值1表示不缩放对象。
- 负值用于水平和垂直翻转刻度对象。
- 介于0和-1 之间的值将翻转刻度对象并减小其宽度和高度。
- 小于-1 的值将翻转对象并增加其宽度和高度。
- 值-1 翻转缩放对象，但不更改其水平或垂直大小。

`CenterX`和 `CenterY` 属性指定了对象的 `Path` 缩放点。 此中心点在转换对象的坐标空间中表示。 默认情况下，缩放将应用于 (0，0) ，这是对象的左上角 `Path` 。 这 `Path` 会使对象移动并使其显得更大，因为当应用转换时，将更改对象所在的坐标空间 `Path` 。

下面的示例演示如何缩放 `Path` 对象：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象缩放到大小的1.5 倍。

## <a name="skew-transform"></a>倾斜变换

倾斜变换 `Path` 会在 2d x-y 坐标系统中倾斜对象，这对于在2d 对象中创建三维深度的错觉非常有用。

`SkewTransform`派生自类的类 `Transform` 定义以下属性：

- `AngleX`，类型为 `double` ，它表示 x 轴倾斜角度，该角度从 y 轴开始沿逆时针方向测量，以度为单位。 此属性的默认值为0.0。
- `AngleY`，类型为 `double` ，它表示 y 轴倾斜角度，该角度从 x 轴开始沿逆时针方向测量，以度为单位。 此属性的默认值为0.0。
- `CenterX`，类型为 `double` ，表示变换中心的 x 坐标。 此属性的默认值为0.0。
- `CenterY`，类型为 `double` ，表示变换中心的 y 坐标。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

若要预测斜切转换的效果，请考虑将 `AngleX` x 轴值与原始坐标系统相对倾斜。 因此，对于 `AngleX` 30，y 轴通过原点旋转30度，并将 x 的值从该原点倾斜30度。 同样，值为30时，将 `AngleY` 对象的 y 值 `Path` 从原点倾斜30度。

> [!NOTE]
> 若要 `Path` 就地倾斜对象，请将 `CenterX` 和属性设置 `CenterY` 为对象的中心点。

下面的示例演示如何倾斜 `Path` 对象：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 从 (0，0) 的中心点向对象应用45度的水平倾斜。

## <a name="translate-transform"></a>转换转换

转换转换会移动 2D x-y 坐标系统中的对象。

`TranslateTransform`派生自类的类 `Transform` 定义以下属性：

- `X`，类型为 `double` ，表示沿 x 轴移动的距离。 此属性的默认值为0.0。
- `Y`，类型为 `double` ，表示沿 y 轴移动的距离。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

负值 `X` 将对象向左移动，正值将对象向右移动。 负值 `Y` 将对象上移，正值将对象向下移动。

下面的示例演示如何转换 `Path` 对象：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象被移动到右侧50个与设备无关的单位，并向下移动50个与设备无关的单位。

## <a name="multiple-transforms"></a>多个转换

Xamarin.Forms 具有两个支持将多个转换应用于 `Path` 对象的类。 这些是 `TransformGroup` 、和 `CompositeTransform` 。 `TransformGroup`按任何所需顺序执行转换，同时 `CompositeTransform` 按特定顺序执行转换。

### <a name="transform-groups"></a>转换组

转换组表示由多个对象组成的复合转换 `Transform` 。

`TransformGroup`派生自类的类 `Transform` 定义 `Children` 类型的属性， `TransformCollection` 该属性表示对象的集合 `Transform` 。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

在使用类的复合转换中，转换顺序很重要 `TransformGroup` 。 例如，如果您首先旋转，然后缩放，然后再翻译，则会得到不同于第一次平移、旋转和缩放的结果。 其中一个原因是重要的是，旋转和缩放等转换是相对于坐标系统的原点执行的。 缩放以原点为中心的对象将产生不同的结果，以便缩放已移出原点的对象。 同样，旋转位于原点中心的对象会产生不同的结果，而不是旋转远离原点的对象。

下面的示例演示如何使用类执行复合转换 `TransformGroup` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象的大小调整为1.5 倍，然后旋转45度。

## <a name="composite-transforms"></a>复合转换

复合转换向对象应用多个转换。

`CompositeTransform`派生自类的类 `Transform` 定义以下属性：

- `CenterX`，类型为 `double` ，表示此变换中心点的 x 坐标。 此属性的默认值为0.0。
- `CenterY`，类型为 `double` ，表示此转换的中心点的 y 坐标。 此属性的默认值为0.0。
- `ScaleX`，类型为 `double` ，它表示 x 轴缩放比例。 此属性的默认值为1.0。
- `ScaleY`，类型为 `double` ，它表示 y 轴的缩放比例。 此属性的默认值为1.0。
- `SkewX`，类型为 `double` ，它表示 x 轴倾斜角度，该角度从 y 轴开始沿逆时针方向测量，以度为单位。 此属性的默认值为0.0。
- `SkewY`，类型为 `double` ，它表示 y 轴倾斜角度，该角度从 x 轴开始沿逆时针方向测量，以度为单位。 此属性的默认值为0.0。
- `Rotation`类型为的， `double` 表示顺时针旋转的角度（以度为单位）。 此属性的默认值为0.0。
- `TranslateX`，类型为 `double` ，表示沿 x 轴移动的距离。 此属性的默认值为0.0。
- `TranslateY`，类型为 `double` ，表示沿 y 轴移动的距离。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`CompositeTransform`按以下顺序应用转换：

1. 缩放 (`ScaleX` 和 `ScaleY`) 。
1. 倾斜 (`SkewX` 和 `SkewY`) 。
1. 旋转 (`Rotation`) 。
1. ) 转换 `TranslateX` (`TranslateY` 。

如果要按不同的顺序将多个转换应用于对象，则应创建 `TransformGroup` 并按照预期顺序插入转换。

> [!IMPORTANT]
> `CompositeTransform`对于所有转换，均使用相同的中心点 `CenterX` 和 `CenterY` 。 如果要为每个转换指定不同的中心点，请使用 `TransformGroup` ，

下面的示例演示如何使用类执行复合转换 `CompositeTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象的大小调整为1.5 倍，然后旋转45度，然后通过50与设备无关的单位进行转换。

## <a name="transform-matrix"></a>转换矩阵

转换可以按3x3 仿射转换矩阵（在二维空间中执行转换）进行描述。 此3x3 矩阵由 `Matrix` 结构表示，后者是三行和三列值的集合 `double` 。

`Matrix`结构定义以下属性：

- `Determinant`，类型为 `double` ，用于获取矩阵的行列式。
- `HasInverse`，类型为 `bool` ，指示矩阵是否可反转。
- `Identity`，类型为 `Matrix` ，用于获取恒等矩阵。
- `HasIdentity`，类型为 `bool` ，指示矩阵是否为恒等矩阵。
- `M11`，类型为 `double` ，表示矩阵的第一行和第一列的值。
- `M12`，类型为 `double` ，表示矩阵的第一行和第二列的值。
- `M21`，类型为 `double` ，表示矩阵中第二行、第一列的值。
- `M22`，类型为 `double` ，表示矩阵的第二行和第二列的值。
- `OffsetX`，类型为 `double` ，表示矩阵中第三行、第一列的值。
- `OffsetY`，类型为 `double` ，表示矩阵的第三行和第二列的值。

`OffsetX`和 `OffsetY` 属性是如此命名的，因为它们分别指定了沿 x 轴和 y 轴平移坐标空间的量。

此外，该 `Matrix` 结构公开了一系列方法，这些方法可用于操作矩阵值，包括、、 `Append` `Invert` `Multiply` 等 `Prepend` 。

下表显示了矩阵的结构 Xamarin.Forms ：

:::row:::
    :::column:::
        M11
    :::column-end:::
    :::column:::
        M12
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        M21
    :::column-end:::
    :::column:::
        M22
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        OffsetX
    :::column-end:::
    :::column:::
        OffsetY
    :::column-end:::
    :::column:::
        1.0
    :::column-end:::
:::row-end:::

> [!NOTE]
> 仿射转换矩阵的最终列等于 (0，0，1) ，因此只需指定前两列中的成员。

通过操作矩阵值，你可以旋转、缩放、倾斜和转换 `Path` 对象。 例如，如果将 `OffsetX` 值更改为100，则可以使用它将 `Path` 100 对象与设备无关的单位沿 x 轴移动。 如果将此 `M22` 值更改为3，则可以使用它将对象拉伸 `Path` 到其当前高度的三倍。 如果更改这两个值，则将 `Path` 沿 x 轴移动与设备无关的对象100，并将其高度拉伸3倍。 此外，可以将仿射变换矩阵相乘以形成任意数量的线性转换，如旋转和斜切，然后是平移。

## <a name="custom-transforms"></a>自定义转换

`MatrixTransform`派生自类的类 `Transform` 定义 `Matrix` 类型的属性， `Matrix` 该属性表示定义转换的矩阵。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

可以使用、、或对象描述的任何转换都 `TranslateTransform` `ScaleTransform` `RotateTransform` `SkewTransform` 可以同样通过进行描述 `MatrixTransform` 。 但是，与 `TranslateTransform` `ScaleTransform` `RotateTransform` `SkewTransform` 在中设置向量组件相比，、、和类更易于理解 `Matrix` 。 因此， `MatrixTransform` 类通常用于创建由 `RotateTransform` 、 `ScaleTransform` 、 `SkewTransform` 或类不提供的自定义转换 `TranslateTransform` 。

下面的示例演示如何 `Path` 使用转换对象 `MatrixTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

在此示例中， `Path` 对象在 X 和 Y 维中拉伸、倾斜和偏移。

此外，还可以使用内置的类型转换器以简化的形式编写 Xamarin.Forms ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

在此示例中，将 `Matrix` 属性指定为由六个成员组成的以逗号分隔的字符串： `M11` 、 `M12` 、 `M21` 、、 `M22` `OffsetX` 和 `OffsetY` 。 虽然在此示例中，成员用逗号分隔，但也可以用一个或多个空格来分隔。

此外，通过指定与属性的值相同的六个成员，可以进一步简化前面的示例 `RenderTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)
