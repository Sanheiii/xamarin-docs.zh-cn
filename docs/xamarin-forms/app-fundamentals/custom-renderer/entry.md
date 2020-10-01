---
title: 自定义 Entry
description: Xamarin.Forms Entry 控件允许对单行文本进行编辑。 本文演示了如何为 Entry 控件创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a96e81314ad48ab0618083c215048e0678d09768
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557369"
---
# <a name="customizing-an-entry"></a>自定义 Entry

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

Xamarin.Forms Entry 控件允许对单行文本进行编辑。本文演示了如何为 Entry 控件创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。

每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 Xamarin.Forms 应用程序呈现 [`Entry`](xref:Xamarin.Forms.Entry) 控件时，在 iOS 中实例化 `EntryRenderer` 类，而该操作又会实例化本机 `UITextField` 控件。 在 Android 平台上，`EntryRenderer` 类实例化 `EditText` 控件。 在通用 Windows 平台 (UWP) 上，`EntryRenderer` 类实例化 `TextBox` 控件。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`Entry`](xref:Xamarin.Forms.Entry) 控件和实现它的相应本机控件之间的关系：

![Entry 控件和实现的本机控件之间的关系](entry-images/entry-classes.png)

通过在每个平台上为 [`Entry`](xref:Xamarin.Forms.Entry) 控件创建自定义呈现器，可以利用呈现过程来实现特定于平台的自定义。 执行此操作的过程如下：

1. [创建](#creating-the-custom-entry-control) Xamarin.Forms 自定义控件。
1. [使用](#consuming-the-custom-control) Xamarin.Forms 中的自定义控件。
1. 在每个平台上为控件[创建](#creating-the-custom-renderer-on-each-platform)自定义呈现器。

现在将依次讨论每个项，以在每个平台上实现具有不同背景颜色的 [`Entry`](xref:Xamarin.Forms.Entry) 控件。

> [!IMPORTANT]
> 本文介绍如何创建简单的自定义呈现器。 但是，要在每个平台上实现具有不同背景色的 `Entry`，无需创建自定义呈现器。 这可以通过使用 [`Device`](xref:Xamarin.Forms.Device) 类或 `OnPlatform` 标记扩展来轻松实现，以提供特定于平台的值。 有关详细信息，请参阅[提供特定于平台的值](~/xamarin-forms/platform/device.md#provide-platform-specific-values)和 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="creating-the-custom-entry-control"></a>创建自定义 Entry 控件

通过子类化 `Entry` 控件，可以创建自定义 [`Entry`](xref:Xamarin.Forms.Entry) 控件，如以下示例代码所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry` 控件创建在 .NET Standard 库项目中，并且只是 [`Entry`](xref:Xamarin.Forms.Entry) 控件。 将在自定义呈现器中进行控件自定义，因此 `MyEntry` 控件中无需任何其他实现。

## <a name="consuming-the-custom-control"></a>使用自定义控件

通过在控件元素上声明 `MyEntry` 自定义控件位置的命名空间并使用命名空间前缀，可以在 .NET Standard 库项目的 XAML 中引用该控件。 下面的代码示例演示 XAML 页可以如何使用 `MyEntry` 控件：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local` 命名空间前缀可以命名为任何内容。 但是，`clr-namespace` 和 `assembly` 值必须与自定义控件的详细信息相匹配。 声明命名空间后，前缀用于引用自定义控件。

下面的代码示例演示 C# 页可以如何使用 `MyEntry` 控件：

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

此代码实例化新的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象，该对象将在页面上垂直居中以及水平居中显示 [`Label`](xref:Xamarin.Forms.Label) 和 `MyEntry` 控件。

现在可以向每个应用程序项目添加自定义呈现器，以便在每个平台上自定义控件外观。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建呈现本机控件的 `EntryRenderer` 类的子类。
1. 替代呈现本机控件的 `OnElementChanged` 方法并写入逻辑以自定义控件。 创建相应的 Xamarin.Forms 控件时将调用此方法。
1. 向自定义呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 控件。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 可选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![MyEntry 自定义呈现器项目的职责](entry-images/solution-structure.png)

`MyEntry` 控件由平台特定的 `MyEntryRenderer` 类呈现，这些类均派生自各平台的 `EntryRenderer` 类。 这导致每个 `MyEntry` 控件都使用特定于平台的背景色呈现，如下面的屏幕截图所示：

![每个平台上的 MyEntry 控件](entry-images/screenshots.png)

`EntryRenderer` 类公开 `OnElementChanged` 方法，创建 Xamarin.Forms 控件时调用此方法以呈现对应的本机控件。 此方法采用 `ElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素 。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `MyEntry` 控件的引用。

`MyEntryRenderer` 类中 `OnElementChanged` 方法的替代版本可执行本机控件自定义。 可以通过 `Control` 属性访问平台上使用的对本机控件的类型化引用。 此外，可以通过 `Element` 属性获取对正在呈现的 Xamarin.Forms 控件的引用，尽管它没有在示例应用程序中使用。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 控件的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各部分讨论每个平台特定的 `MyEntryRenderer` 自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

以下代码示例展示了适用于 iOS 平台的自定义呈现器：

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

对基类的 `OnElementChanged` 方法的调用实例化 iOS `UITextField` 控件，并且将对控件的引用分配给呈现器的 `Control` 属性。 然后，使用 `UIColor.FromRGB` 方法将背景色设置为淡紫色。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

以下代码示例展示了适用于 Android 平台的自定义呈现器：

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

对基类的 `OnElementChanged` 方法的调用实例化 Android `EditText` 控件，并且将对控件的引用分配给呈现器的 `Control` 属性。 然后，使用 `Control.SetBackgroundColor` 方法将背景色设置为淡绿色。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

以下代码示例展示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

对基类的 `OnElementChanged` 方法的调用实例化 `TextBox` 控件，并且将对控件的引用分配给呈现器的 `Control` 属性。 然后通过创建 `SolidColorBrush` 实例将背景色设置为蓝绿色。

## <a name="summary"></a>总结

本文演示了如何创建 Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 控件的自定义控件呈现器，使开发人员能够使用自己特定于平台的呈现替代默认本机呈现。 自定义呈现器提供了用于自定义 Xamarin.Forms 控件外观的一种强大方法。 可使用它们进行细微的样式更改，也可进行复杂的特定于平台的布局和行为自定义。

## <a name="related-links"></a>相关链接

- [CustomRendererEntry（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-entry)