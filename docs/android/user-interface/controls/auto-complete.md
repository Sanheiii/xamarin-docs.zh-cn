---
title: Xamarin Android 自动完成
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/31/2018
ms.openlocfilehash: 5a11ab06321b890d8f1f5a35a8456b06a900fbcc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029364"
---
# <a name="auto-complete-for-xamarinandroid"></a>Xamarin Android 自动完成

`AutoCompleteTextView` 是在用户键入时自动显示完成建议的可编辑文本视图元素。 建议列表显示在下拉菜单中，用户可以从中选择要替换编辑框内容的项。

![自动完成的示例](images/auto-complete.png)

## <a name="overview"></a>概述

若要创建提供自动完成建议的文本输入小组件，请使用[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
小组件. 通过[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)从与小组件关联的字符串集合接收建议。

在本教程中，你将创建一个[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
提供国家/地区名称建议的小组件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

[`TextView`](xref:Android.Widget.TextView)是一个介绍[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)的标签
小组件.

## <a name="tutorial"></a>教程

启动名为*HelloAutoComplete*的新项目。

创建名为 `list_item.xml` 的 XML 文件，并将其保存在**资源/布局**文件夹中。 将此文件的生成操作设置为 `AndroidResource`。 编辑文件，使其类似于：

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView> 
```

此文件定义了一个简单的[`TextView`](xref:Android.Widget.TextView) ，将用于建议列表中显示的每一项。

打开**Resources/Layout/main.axml**并插入以下内容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

打开**MainActivity.cs**并为[`OnCreate()`](xref:Android.App.Activity.OnCreate*)插入以下代码
付款方式

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

内容视图设置为 `main.xml` 布局后， [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
将从布局中捕获小组件，并[`FindViewById`](xref:Android.App.Activity.FindViewById*)。 然后初始化一个新的[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) ，以将 `list_item.xml` 布局绑定到 `COUNTRIES` 字符串数组中的每个列表项（在下一步中定义）。 最后，调用 `SetAdapter()` 将[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)与[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
小组件，以便字符串数组将填充建议列表。

在 `MainActivity` 类中添加字符串数组：

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

这是当用户键入到[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)时，下拉列表中将提供的建议列表。
小组件.

运行该应用程序。 键入内容时，应会看到如下所示的内容：

[![示例自动完成屏幕截图，其中列出了包含 "ca" 的名称](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)

## <a name="more-information"></a>详细信息

请注意，不建议使用硬编码字符串数组，因为应用程序代码应侧重于行为，而不是内容。 应该从代码中外部化应用程序内容（例如字符串），以便更轻松地对内容进行修改，并促进内容的本地化。 在本教程中使用硬编码字符串只是为了简化并专注于[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
小组件. 相反，应用程序应在 XML 文件中声明此类字符串数组。 可以使用项目中的 `<string-array>` 资源 `res/values/strings.xml` 文件来完成此操作。 例如:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="countries_array">
        <item>Bahrain</item>
        <item>Bangladesh</item>
        <item>Barbados</item>
        <item>Belarus</item>
        <item>Belgium</item>
        <item>Belize</item>
        <item>Benin</item>
    </string-array>
</resources>
```

若要将这些资源字符串用于[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)，请替换原始[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
具有以下内容的构造函数行：

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```

### <a name="references"></a>reference

- [AutoCompleteTextView 食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input)&ndash; 适用于 `AutoCompleteTextView` 的 Xamarin Android 示例项目
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据[创造性 Commons 2.5 归属许可证](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用。本教程基于[Android 自动完成教程 *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)。_
