---
title: 在 XAML 中传递参数
description: 本文演示如何使用 XAML 属性，这些属性可用于将参数传递给非默认构造函数、调用工厂方法和指定泛型参数的类型。
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dcf09adc690aee5487107630eb74bb8c4e9599cb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562166"
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中传递参数

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_本文演示如何使用 XAML 属性，这些属性可用于将参数传递给非默认构造函数、调用工厂方法和指定泛型参数的类型。_

## <a name="overview"></a>概述

通常需要使用需要参数的构造函数或通过调用静态创建方法来实例化对象。 这可以通过使用和属性在 XAML 中 `x:Arguments` 实现 `x:FactoryMethod` ：

- `x:Arguments`特性用于指定非默认构造函数或工厂方法对象声明的构造函数参数。 有关详细信息，请参阅 [传递构造函数参数](#passing-constructor-arguments)。
- `x:FactoryMethod`特性用于指定可用于初始化对象的工厂方法。 有关详细信息，请参阅 [调用工厂方法](#calling-factory-methods)。

此外， `x:TypeArguments` 特性可用于指定泛型类型的构造函数的泛型类型参数。 有关详细信息，请参阅 [指定泛型类型参数](#specifying-a-generic-type-argument)。

## <a name="passing-constructor-arguments"></a>传递构造函数参数

可以使用特性将参数传递给非默认构造函数 `x:Arguments` 。 每个构造函数参数都必须在表示参数类型的 XML 元素中进行分隔。 Xamarin.Forms 支持基本类型的以下元素：

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

下面的代码示例演示如何将 `x:Arguments` 特性用于三个 [`Color`](xref:Xamarin.Forms.Color) 构造函数：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

标记内的元素数 `x:Arguments` 以及这些元素的类型必须与其中一个 [`Color`](xref:Xamarin.Forms.Color) 构造函数匹配。 `Color`带有单个参数的[构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double))需要从 0 (黑色) 到 1 (白色) 的灰度值。 `Color`带有三个参数的[构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))需要红色、绿色和蓝色值，范围从0到1。 `Color`带有四个参数的[构造函数](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))添加 alpha 通道作为第四个参数。

以下屏幕截图显示了 [`Color`](xref:Xamarin.Forms.Color) 用指定参数值调用每个构造函数的结果：

![BoxView 指定的 x:Arguments](passing-arguments-images/passing-arguments.png)

## <a name="calling-factory-methods"></a>调用工厂方法

可以在 XAML 中调用工厂方法，方法是使用属性指定方法的名称 `x:FactoryMethod` ，并使用属性指定其参数 `x:Arguments` 。 工厂方法是一种 `public static` 方法，该方法返回与定义方法的类或结构具有相同类型的对象或值。

[`Color`](xref:Xamarin.Forms.Color)结构定义了很多工厂方法，下面的代码示例演示如何调用其中的三种方法：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

标记内的元素数 `x:Arguments` 以及这些元素的类型必须与被调用的工厂方法的参数匹配。 [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))Factory 方法需要四个 [`Int32`](/dotnet/api/system.int32) 参数，分别表示红色、绿色、蓝色和 alpha 值，范围介于0到255之间。 [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))工厂方法需要四个 [`Double`](/dotnet/api/system.double) 参数，分别表示色相、饱和度、亮度和 alpha 值，范围从0到1。 [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))工厂方法需要一个 [`String`](/dotnet/api/system.string) 表示) RGB 颜色的十六进制 (。

下面的屏幕截图显示了 [`Color`](xref:Xamarin.Forms.Color) 用指定参数值调用每个工厂方法的结果：

![BoxView 通过 x:FactoryMethod 和 x:Arguments 指定的颜色](passing-arguments-images/factory-methods.png)

## <a name="specifying-a-generic-type-argument"></a>指定泛型类型参数

可以使用特性指定泛型类型的构造函数的泛型类型参数 `x:TypeArguments` ，如以下代码示例所示：

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用与目标类型匹配的属性对其进行实例化 `x:TypeArguments` 。 在 [`On`](xref:Xamarin.Forms.On) 类中， [`Platform`](xref:Xamarin.Forms.On.Platform) 特性可以接受单个 `string` 值或多个以逗号分隔的 `string` 值。 在此示例中， [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) 属性设置为特定于平台的 [`Thickness`](xref:Xamarin.Forms.Thickness) 。

有关泛型类型参数的详细信息，请参阅 [ Xamarin.Forms XAML 中的泛型](generics.md)。

## <a name="related-links"></a>相关链接

- [将构造函数参数传递 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [ (示例调用工厂方法) ](/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [XAML 中的泛型 Xamarin.Forms](generics.md)