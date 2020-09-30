---
title: 平台特定内容
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用和创建平台细节。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b029ffdb0a16d9f54951ae3754ca0f1eb7f69aaa
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556875"
---
# <a name="platform-specifics"></a>平台特定内容

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。_

通过 XAML 使用特定于平台的过程，或通过流畅的代码 API 执行的过程如下所示：

1. `xmlns` `using` 为命名空间添加声明或指令 [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) 。
1. `xmlns` `using` 为包含特定于平台的功能的命名空间添加声明或指令：
    1. 在 iOS 上，这是 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空间。
    1. 在 Android 上，这是 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空间。 对于 Android AppCompat，这是 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 命名空间。
    1. 在通用 Windows 平台上，这是 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空间。
1. 从 XAML 应用特定于平台的，或使用 Fluent API 的代码 `On<T>` 。 的值 `T` 可以是 [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) 命名空间中的、或类型 [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) 。

> [!NOTE]
> 请注意，尝试在其不可用的平台上使用特定于平台的将不会导致错误。 相反，代码将在不应用特定于平台的情况下执行。

通过流畅代码 API 使用的平台详细信息 `On<T>` 返回 [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) 对象。 这允许在方法级联的同一对象上调用多个平台细节。

有关提供的平台细节的详细信息 Xamarin.Forms ，请参阅 [IOS 平台说明](~/xamarin-forms/platform/ios/index.md)、 [Android 平台细节](~/xamarin-forms/platform/android/index.md)和 [Windows 平台细节](~/xamarin-forms/platform/windows/index.md)。

## <a name="creating-platform-specifics"></a>创建平台细节

供应商可以创建自己的平台说明和效果。 效果提供特定功能，然后通过特定于平台的进行公开。 其结果是可以更轻松地通过 XAML 使用，通过流畅的代码 API 使用。

创建平台特定的过程如下所示：

1. 实现特定功能。 有关详细信息，请参阅 [创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 创建平台特定的类，该类将公开效果。 有关详细信息，请参阅 [创建平台特定的类](#creating-a-platform-specific-class)。
1. 在特定于平台的类中，实现附加属性，以允许通过 XAML 使用特定于平台的。 有关详细信息，请参阅 [添加附加属性](#adding-an-attached-property)。
1. 在特定于平台的类中，实现扩展方法，使平台特定的能够通过流畅的代码 API 使用。 有关详细信息，请参阅 [添加扩展方法](#adding-extension-methods)。
1. 修改效果实现，以便仅当在与效果相同的平台上调用了平台特定时才应用该效果。 有关详细信息，请参阅 [创建效果](#creating-the-effect)。

将效果公开为特定于平台的结果是可以更轻松地通过 XAML 和通过流畅的代码 API 使用该效果。

> [!NOTE]
> 设想，供应商将使用此技术来创建自己的平台细节，以方便用户消费。 尽管用户可以选择创建自己的平台细节，但应注意，它需要更多的代码，而不是创建和使用效果。

该 [示例应用程序](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) 演示了一个 `Shadow` 特定于平台的，它将阴影添加到控件所显示的文本 [`Label`](xref:Xamarin.Forms.Label) ：

![特定于影子平台](images/screenshots.png)

该 [示例应用程序](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) `Shadow` 在每个平台上实现特定于平台的，以便于理解。 但除每个平台特定的效果实现外，影子类的实现在很大程度上与每个平台相同。 因此，本指南重点介绍了影子类的实现，并对单个平台产生关联的影响。

有关效果的详细信息，请参阅 [自定义具有效果的控件](~/xamarin-forms/app-fundamentals/effects/index.md)。

### <a name="creating-a-platform-specific-class"></a>创建平台特定的类

平台特定的创建为 `public static` 类：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

以下各节讨论 `Shadow` 平台特定的和关联的效果的实现。

#### <a name="adding-an-attached-property"></a>添加附加属性

附加的属性必须添加到特定于 `Shadow` 平台的，以允许通过 XAML 使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed`附加属性用于向 `MyCompany.LabelShadowEffect` 类附加到的控件添加效果，并将其从中移除 `Shadow` 。 该附加属性注册属性值更改时执行的 `OnIsShadowedPropertyChanged` 方法。 反过来，此方法会调用 `AttachEffect` 或 `DetachEffect` 方法，以根据附加属性的值添加或删除效果 `IsShadowed` 。 通过修改控件的集合，将该效果添加到控件中或从该控件中移除 [`Effects`](xref:Xamarin.Forms.Element.Effects) 。

> [!NOTE]
> 请注意，通过指定一个值，此值是解析组名称与在效果实现中指定的唯一标识符的串联。 有关详细信息，请参阅 [创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

#### <a name="adding-extension-methods"></a>添加扩展方法

扩展方法必须添加到特定于 `Shadow` 平台的，以允许通过流畅的代码 API 使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed`和 `SetIsShadowed` 扩展方法分别调用附加属性的 get 和 set 访问器 `IsShadowed` 。 每个扩展方法都对 `IPlatformElementConfiguration<iOS, FormsElement>` 类型进行操作，该类型指定平台特定的可在 [`Label`](xref:Xamarin.Forms.Label) iOS 实例上调用。

#### <a name="creating-the-effect"></a>创建效果

`Shadow`特定于平台的将添加 `MyCompany.LabelShadowEffect` 到 [`Label`](xref:Xamarin.Forms.Label) ，并将其删除。 以下代码示例展示了 iOS 项目的 `LabelShadowEffect` 实现：

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`UpdateShadow`方法设置 `Control.Layer` 属性以创建阴影，前提是 `IsShadowed` 附加属性设置为 `true` ，并且 `Shadow` 已在实现了该效果的同一平台上调用了平台特定的。 将通过方法执行此检查 `OnThisPlatform` 。

如果 `Shadow.IsShadowed` 附加属性值在运行时更改，则此效果需要通过删除阴影来做出响应。 因此，方法的重写版本 `OnElementPropertyChanged` 用于通过调用方法来响应可绑定的属性更改 `UpdateShadow` 。

有关创建效果的详细信息，请参阅 [创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md) 和将 [效果参数作为附加属性传递](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

### <a name="consuming-the-platform-specific"></a>使用特定于平台的

`Shadow`通过将 `Shadow.IsShadowed` 附加属性设置为一个值，在 XAML 中使用特定于平台的 `boolean` ：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [iOS 平台-详细信息](~/xamarin-forms/platform/ios/index.md)
- [Android 平台-详细信息](~/xamarin-forms/platform/android/index.md)
- [Windows 平台-详细信息](~/xamarin-forms/platform/windows/index.md)
- [自定义具有效果的控件](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)