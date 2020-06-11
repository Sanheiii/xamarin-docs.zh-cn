---
标题： "Android 上的按钮填充和阴影" 说明： "平台细节使你可以使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 android 平台特定的，该平台使用 Android 按钮的默认填充和阴影值。
ms-chap： xamarin assetid： BD2B60D1-DE6E-4691-A777-8EC5F560A4E9 毫秒： xamarin 窗体作者： davidbritch 毫秒. 作者： dabritch 毫秒。日期：07/10/2018 非 loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="button-padding-and-shadows-on-android"></a>Android 上的按钮填充和阴影

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定控制按钮是否 Xamarin.Forms 使用 Android 按钮的默认填充和阴影值。 它通过将 [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) 和 [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) 附加属性设置为值在 XAML 中使用 `boolean` ：

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `Button.SetUseDefaultPadding` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultPadding （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Button}，System.object）和 [ `Button.SetUseDefaultShadow` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultShadow （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Button}，System.object）方法， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于控制 Xamarin.Forms 按钮是否使用 Android 按钮的默认填充和阴影值。 此外，[ `Button.UseDefaultPadding` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultPadding （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Button}））和 [ `Button.UseDefaultShadow` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultShadow （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Button}））方法可用于返回某个按钮是否分别使用默认填充值和默认阴影值。

结果是， Xamarin.Forms 按钮可以使用 Android 按钮的默认填充和阴影值：

![](button-padding-shadow-images/button-padding-and-shadow.png "Default Padding and Shadow Values on Android Buttons")

请注意，在上面的屏幕截图中，每个 [`Button`](xref:Xamarin.Forms.Button) 都具有相同的定义，只不过右侧 `Button` 使用 Android 按钮的默认填充和阴影值。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
