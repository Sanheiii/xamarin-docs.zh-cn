---
title: 使用跨平台应用中的本机类型
description: 本文介绍如何在跨平台应用程序中使用新的 iOS Unified API 本机类型（nint、nuint、nfloat），该应用程序中的代码与 Android 或 Windows Phone Os 等非 iOS 设备共享。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: davidortinau
ms.author: daortin
ms.date: 04/07/2016
ms.openlocfilehash: c86a00f325f9799b16f6244d3d1cb68de31be005
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015543"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>使用跨平台应用中的本机类型

_本文介绍如何在跨平台应用程序中使用新的 iOS Unified API 本机类型（nint、nuint、nfloat），该应用程序中的代码与 Android 或 Windows Phone Os 等非 iOS 设备共享。_

64类型本机类型适用于 iOS 和 Mac Api。 如果你正在编写在 Android 或 Windows 上运行的共享代码，则需要管理统一类型到你可以共享的常规 .NET 类型的转换。

本文档讨论了不同的方法，可与共享/公共代码中的 Unified API 进行互操作。

## <a name="when-to-use-the-native-types"></a>何时使用本机类型

Xamarin 和 Xamarin 统一 Api 仍包括 `int`、`uint` 和 `float` 数据类型，以及 `RectangleF`、`SizeF` 和 `PointF` 类型。 应继续在任何共享的跨平台代码中使用这些现有数据类型。 仅当对 Mac 或 iOS API 进行调用时，才应使用新的本机数据类型，其中需要支持体系结构感知类型。

根据所共享代码的性质，可能存在跨平台代码可能需要处理 `nint`、`nuint` 和 `nfloat` 数据类型的一些情况。 例如：在以前使用 `System.Drawing.RectangleF` 来共享应用的 Xamarin 和 Xamarin Android 版本间的功能的矩形数据上处理转换的库，需要更新才能处理 iOS 上的本机类型。

处理这些更改的方式取决于应用程序的大小和复杂程度以及已使用的代码共享形式，如以下部分中所示。

## <a name="code-sharing-considerations"></a>代码共享注意事项

如[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)文档中所述，在跨平台项目之间共享代码有两种主要方法：共享项目和可移植类库。 使用了两种类型中的哪一种，将限制处理跨平台代码中的本机数据类型时所拥有的选项。

### <a name="portable-class-library-projects"></a>可移植类库项目

可移植类库（PCL）允许目标为要支持的平台，并使用接口提供特定于平台的功能。

由于 PCL 项目类型将被编译到 `.DLL` 并且没有 Unified API，因此您将被迫继续使用 PCL 源代码中现有的数据类型（`int`、`uint`、`float`），并键入将调用转换为 PCL 的类前端应用程序中的和方法。 例如:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共享项目

使用共享资产项目类型，可以在单独的项目中组织源代码，然后将其包含并编译到单个特定于平台的前端应用中，并根据需要使用 `#if` 编译器指令来管理特定于平台的要求.

使用共享代码的前端移动应用程序的大小和复杂程度以及所共享代码的大小和复杂性需要考虑在跨平台中选择本机数据类型的支持方法时需要考虑的问题共享资产项目。

根据这些因素，可以使用 `if __UNIFIED__ ... #endif` 编译器指令来实现以下类型的解决方案，以处理对代码所做的 Unified API 特定更改。

#### <a name="using-duplicate-methods"></a>使用重复方法

采用在上面给出的矩形数据进行转换的库示例。 如果库只包含一种或两种非常简单的方法，可以选择为 Xamarin 和 Xamarin 创建这些方法的重复版本。 例如:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

在上面的代码中，由于 `CalculateArea` 例程非常简单，因此我们使用了条件编译并创建了单独的 Unified API 版本的方法。 另一方面，如果库包含多个例程或几个复杂例程，则此解决方案是不可行的，因为这会出现一个问题，使所有方法保持同步以进行修改或 bug 修复。

#### <a name="using-method-overloads"></a>使用方法重载

在这种情况下，解决方案可能是使用32位数据类型创建方法的重载版本，以便它们现在作为参数和/或返回值 `CGRect`，并将该值转换为 `RectangleF` （知道从 `nfloat` 转换为 `float`是有损转换），并调用原始版本的例程来执行实际工作。 例如:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

同样，这是一个很好的解决方案，前提是精度损失不会影响应用程序的特定需求的结果。

#### <a name="using-alias-directives"></a>使用 Alias 指令

对于失去精度损失的区域，另一个可能的解决方案是使用 `using` 指令，通过将以下代码添加到共享源代码文件的顶部，并转换任何所需的来创建本机和 CoreGraphics 数据类型的别名 `int`、`uint` 或 `float` 值 `nint`、`nuint` 或 `nfloat`：

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

这样，我们的示例代码就会变成：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

请注意，我们已将 `CalculateArea` 方法更改为返回 `nfloat` 而不是标准 `float`。 这样做的目的是，我们不会因为尝试_隐式_转换计算的 `nfloat` 结果（因为这两个值都是类型 `nfloat`）到 `float` 返回值而不会出现编译错误。

如果在非 Unified API 设备上编译并运行代码，则 `using nfloat = global::System.Single;` 会将 `nfloat` 映射到将隐式转换为 `float` 的 `Single`，从而使使用的前端应用程序无需修改即可调用 `CalculateArea` 方法。

#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端应用中使用类型转换

如果你的前端应用程序只对你的共享代码库进行少量调用，另一个解决方案是将库保持不变，并在调用现有例程时在 Xamarin 或 Xamarin 应用程序中执行类型强制转换。 例如:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果使用的应用程序对共享代码库进行数百次调用，则这可能不是一个好的解决方案。

根据应用程序的体系结构，我们可能会使用一个或多个上述解决方案来支持跨平台代码中的本机数据类型（需要时）。

## <a name="xamarinforms-applications"></a>Xamarin. Forms 应用程序

需要使用以下内容才能使用适用于跨平台 Ui 的 Xamarin 窗体，还将与 Unified API 应用程序共享：

- 整个解决方案必须使用 1.3.1 NuGet 包的版本（或更高版本）。
- 对于任何 Xamarin 自定义呈现，使用上面提供的相同类型的解决方案，这些解决方案基于 UI 代码的共享方式（共享项目或 PCL）。

与在标准跨平台应用程序中一样，在大多数情况下，应该在任何共享的跨平台代码中使用现有的32位数据类型。 仅当对 Mac 或 iOS API 进行调用时，才应使用新的本机数据类型，其中需要支持体系结构感知类型。

有关更多详细信息，请参阅[更新现有 Xamarin. Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)文档。

## <a name="summary"></a>总结

在本文中，我们已了解何时在 Unified API 应用程序中使用本机数据类型，以及它们在跨平台上的含义。 我们提供了几种解决方案，可用于在跨平台库中必须使用新的本机数据类型的情况下使用。 此外，我们还了解了如何在 Xamarin 中支持统一 Api 的快速指南。

## <a name="related-links"></a>相关链接

- [Unified API](~/cross-platform/macios/unified/index.md)
- [本机类型](~/cross-platform/macios/nativetypes.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [代码共享示例](https://docs.microsoft.com/samples/xamarin/mobile-samples/sharingcode/)
