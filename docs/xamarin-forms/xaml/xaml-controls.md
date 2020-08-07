---
title: XAML 控件
description: Xamarin.Forms可以从 XAML 文件引用中定义的所有视图。
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94c451305f04294924b3f7115c4f2b3a7a6bad07
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918673"
---
# <a name="xaml-controls"></a>XAML 控件

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

视图是用户界面对象（如标签、按钮和滑块），这些对象通常称为*控件*或其他图形编程环境中的*小组件*。 Xamarin.Forms所有从类派生的所支持视图 [`View`](xref:Xamarin.Forms.View) 。

Xamarin.Forms可以从 XAML 文件引用中定义的所有视图。

## <a name="views-for-presentation"></a>用于演示的视图

| 查看 | 示例 |
| --- | --- |
| <h3>BoxView</h3>显示特定颜色的矩形。<p align="center">![BoxView 的屏幕截图](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  / [指南](~/xamarin-forms/user-interface/boxview.md) | <p valign="center"><pre>&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>椭圆形</h3>显示椭圆或圆。<p align="center">![椭圆的屏幕截图](xaml-controls-images/Ellipse.png "椭圆形")</p>[API](xref:Xamarin.Forms.Shapes.Ellipse)  / [指南](~/xamarin-forms/user-interface/shapes/ellipse.md) | <p valign="center"><pre>&lt;Ellipse Fill="Red"<br />         WidthRequest="150"<br />         HeightRequest="50"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Expander</h3>提供可扩展容器来托管任何内容。<p align="center">![扩展器的屏幕截图](xaml-controls-images/Expander.png "Expander")</p>[向导](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text="Baboon" /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source="Baboon.png"<br />           Aspect="AspectFill" /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>映像</h3>显示位图。<p align="center">![图像的屏幕截图](xaml-controls-images/Image.png "图像")</p>[API](xref:Xamarin.Forms.Image)  / [指南](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>标签</h3>显示一个或多个文本行。<p align="center">![标签屏幕截图](xaml-controls-images/Label.png "Label")</p>[API](xref:Xamarin.Forms.Label)  / [指南](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>线</h3>显示线条。<p align="center">![线条屏幕截图](xaml-controls-images/Line.png "线")</p>[API](xref:Xamarin.Forms.Shapes.Line)  / [指南](~/xamarin-forms/user-interface/shapes/line.md) | <p valign="center"><pre>&lt;Line X1="40"<br />      Y1="0"<br />      X2="0"<br />      Y2="120"<br />      Stroke="Red"<br />      HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>映射</h3>显示地图。<p align="center">![图的屏幕截图](xaml-controls-images/Map.png "映射")</p>[API](xref:Xamarin.Forms.Maps.Map)  / [指南](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>播放视频或音频。<p align="center">![MediaElement 的屏幕截图](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  / [指南](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>路径</h3>显示曲线和复杂形状。<p align="center">![路径的屏幕截图](xaml-controls-images/Path.png "路径")</p>[API](xref:Xamarin.Forms.Shapes.Path)  / [指南](~/xamarin-forms/user-interface/shapes/path.md) | <p valign="center"><pre>&lt;Path Stroke="Black"<br />      Aspect="Uniform"<br />      HorizontalOptions="Center"<br />      HeightRequest="100"<br />      WidthRequest="100"<br />      Data="M13.9,16.2<br />            L32,16.2 32,31.9 13.9,30.1Z<br />            M0,16.2<br />            L11.9,16.2 11.9,29.9 0,28.6Z<br />            M11.9,2<br />            L11.9,14.2 0,14.2 0,3.3Z<br />            M32,0<br />            L32,14.2 13.9,14.2 13.9,1.8Z" /&gt;</pre></p> |
| <h3>多边形</h3>显示多边形。<p align="center">![多边形的屏幕截图](xaml-controls-images/Polygon.png "多边形")</p>[API](xref:Xamarin.Forms.Shapes.Polygon)  / [指南](~/xamarin-forms/user-interface/shapes/polygon.md) | <p valign="center"><pre>&lt;Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96,<br/>                 50 96, 48 192, 150 200 144 48"<br />         Fill="Blue"<br />         Stroke="Red"<br />         StrokeThickness="3"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>折线</h3>显示一系列连接的直线。<p align="center">![折线屏幕截图](xaml-controls-images/Polyline.png "折线")</p>[API](xref:Xamarin.Forms.Shapes.Polyline)  / [指南](~/xamarin-forms/user-interface/shapes/Polyline.md) | <p valign="center"><pre>&lt;Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0<br />                  43,60 48,30 100,30"<br />          Stroke="Red"<br />          HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>矩形</h3>显示矩形或正方形。<p align="center">![矩形的屏幕截图](xaml-controls-images/Rectangle.png "矩形")</p>[API](xref:Xamarin.Forms.Shapes.Rectangle)  / [指南](~/xamarin-forms/user-interface/shapes/rectangle.md) | <p valign="center"><pre>&lt;Rectangle Fill="Red"<br />           WidthRequest="150"<br />           HeightRequest="50"<br />           HorizontalOptions="Center" /&gt;</pre></p> |  
| <h3>WebView</h3>显示网页或 HTML 内容。<p align="center">![Web 视图的屏幕截图](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  / [指南](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>启动命令的视图

| 查看 | 示例 |
| --- | --- |
| <h3>Button</h3>显示矩形对象中的文本。<p align="center">![按钮的屏幕截图](xaml-controls-images/Button.png "Button")</p>[API](xref:Xamarin.Forms.Button)  / [指南](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>在矩形对象中显示图像。<p align="center">![ImageButton 的屏幕截图](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  / [指南](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>允许从集中选择一个选项。<p align="center">![单选按钮的屏幕截图](xaml-controls-images/RadioButton.png "RadioButton")</p>[向导](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text="Pineapple"<br/>             CheckedChanged="OnRadioButtonCheckedChanged" /&gt;</pre></p> |
| <h3>RefreshView</h3>提供可滚动内容的请求刷新功能。<p align="center">![RefreshView 的屏幕截图](xaml-controls-images/RefreshView.png "RefreshView")</p>[向导](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>搜索栏</h3> 接受用于执行搜索的用户输入。<p align="center">![SearchBar 的屏幕截图](xaml-controls-images/SearchBar.png "搜索栏")</p>[向导](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder="Enter search term"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> 提供通过轻扫手势显示的上下文菜单项。<p align="center">![SwipeView 的屏幕截图](xaml-controls-images/SwipeView.png "SwipeView")</p>[向导](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>设置值的视图

| 查看 | 示例 |
| --- | --- |
| <h3>CheckBox</h3>允许选择 `boolean` 值。<p align="center">![复选框的屏幕截图](xaml-controls-images/CheckBox.png "CheckBox")</p> [向导](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>滑块</h3>允许 `double` 从连续范围中选择值。<p align="center">![滚动条的屏幕截图](xaml-controls-images/Slider.png "Slider")</p>[API](xref:Xamarin.Forms.Slider)  / [指南](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>步进器</h3>允许 `double` 从增量范围中选择值。<p align="center">![分档器的屏幕截图](xaml-controls-images/Stepper.png "步进器")</p>[API](xref:Xamarin.Forms.Stepper)  / [指南](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>开关</h3>允许选择 `boolean` 值。<p align="center">![交换机屏幕截图](xaml-controls-images/Switch.png "开关")</p>[API](xref:Xamarin.Forms.Switch)  / [指南](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>允许选择日期。<p align="center">![DatePicker 的屏幕截图](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  / [指南](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>允许选择一段时间。<p align="center">![TimePicker 的屏幕截图](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker)  / [指南](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>用于编辑文本的视图

| 查看 | 示例 |
| --- | --- |
| <h3>条目</h3>允许输入和编辑单个文本行。<p align="center">![条目的屏幕截图](xaml-controls-images/Entry.png "条目")</p>[API](xref:Xamarin.Forms.Entry)  / [指南](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>编辑器</h3>允许输入和编辑多行文本。<p align="center">![编辑器的屏幕截图](xaml-controls-images/Editor.png "标签")</p>[API](xref:Xamarin.Forms.Editor)  / [指南](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>指示活动的视图

| 查看 | 示例 |
| --- | --- |
| <h3>ActivityIndicator</h3>显示一个动画来表明，应用程序参与了长时间的活动，而不提供任何进度指示。<p align="center">![ActivityIndicator 的屏幕截图](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  / [指南](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>显示一个动画，用于显示应用程序正在经历长时间的活动。<p align="center">![ProgressBar 的屏幕截图](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar)  / [指南](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>显示集合的视图

| 查看 | 示例 |
| --- | --- |
| <h3>CarouselView</h3>显示一个可滚动的数据项列表。<p align="center">![CarouselView 的屏幕截图](xaml-controls-images/CarouselView.png "CarouselView")</p>[向导](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>使用不同的布局规范显示可滚动的数据项列表。<p align="center">![CollectionView 的屏幕截图](xaml-controls-images/CollectionView.png "CollectionView")</p>[向导](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />                ItemsLayout="VerticalGrid, 2" /&gt;</pre></p> |
| <h3>IndicatorView</h3>显示表示中的项数的指示器 `CarouselView` 。<p align="center">![IndicatorView 的屏幕截图](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[向导](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>显示可选择数据项的可滚动列表。<p align="center">![ListView 的屏幕截图](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView)  / [指南](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>选取器</h3>显示文本字符串列表中的选择项。<p align="center">![选取器的屏幕截图](xaml-controls-images/Picker.png "选取器")</p>[API](xref:Xamarin.Forms.Picker)  / [指南](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>显示交互式行的列表。<p align="center">![TableView 的屏幕截图](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  / [指南](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.FormsFormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsAPI 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
