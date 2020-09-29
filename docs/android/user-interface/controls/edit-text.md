---
title: 编辑文本
description: 如何使用 EditText 小组件来接受用户输入。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: fb29478791c97028ff4d62f97922c672f7a8b17b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457247"
---
# <a name="xamarinandroid-edit-text"></a>Xamarin Android 编辑文本

在本部分中，你将使用 [EditText](xref:Android.Widget.EditText) 小组件来创建用户输入的文本字段。 在字段中输入文本后， **Enter** 键将显示 toast 消息中的文本。

打开 **Resources/layout/activity_main main.axml** ，并将 [EditText](xref:Android.Widget.EditText) 元素添加到包含的布局。 下面的示例 **activity_main main.axml** 具有已 `EditText` 添加到的 `LinearLayout` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

在此代码示例中， `EditText` 特性 `android:imeOptions` 设置为 `actionGo` 。 此设置会将 "已完成" 的默认操作更改[为 "](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) [执行](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE)" 操作，以便点击 " **Enter** " 键即可触发 `KeyPress` 输入处理程序。
通常，使用 (， `actionGo` 这样， **Enter** 键就会将用户转到在中键入的 URL 的目标 ) 

若要处理用户文本输入，请将以下代码添加到**MainActivity.cs**中[OnCreate](xref:Android.App.Activity.OnCreate*)方法的末尾：

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter)
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

此外，如果不存在以下语句，请将以下 `using` 语句添加到 **MainActivity.cs** 的顶部：

```csharp
using Android.Views;
```

此代码示例从布局中增加 [EditText](xref:Android.Widget.EditText) 元素，并添加了一个 [KeyPress](xref:Android.Views.View.KeyPress) 处理程序，用于定义在小组件有焦点的情况下按下某个键时要执行的操作。 在这种情况下，方法定义为在点击) 时侦听 **Enter** 键 (，然后使用已输入的文本弹出 [Toast](xref:Android.Widget.Toast) 消息。 请注意， [Handled](xref:Android.Views.View.KeyEventArgs.Handled) `true` 如果已处理事件，则已处理的属性应始终为。 这是为了防止事件向上冒泡 (这会导致文本字段) 中出现回车符。

运行应用程序，并在文本字段中输入一些文本。 按 **enter** 键时，toast 将显示在右侧：

[![在 EditText 中输入文本的示例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*[*创造性 Commons 2.5 归属许可证*](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用 *。本教程基于* [*Android 表单资料教程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*

## <a name="related-links"></a>相关链接

- [EditTextSample](/samples/xamarin/monodroid-samples/userinterface-edittextsample)