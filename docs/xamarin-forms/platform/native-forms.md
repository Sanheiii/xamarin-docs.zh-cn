---
title: Xamarin 本机项目中的 Xamarin.Forms
description: 本文介绍如何使用直接添加到 Xamarin 本机项目的 ContentPage 派生页以及如何在它们之间导航。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 83b445b8379ad5181ab1dce142cca06625be79ad
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487329"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin 本机项目中的 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

通常情况下，Xamarin.Forms 应用程序包括一个或多个页派生自[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，，这些页面在所有平台共享.NET Standard 库项目或共享项目中。 但是，本机窗体允许`ContentPage`-派生页来直接添加到 Xamarin.iOS、 Xamarin.Android 和 UWP 的本机应用程序。 相比于使用的本机项目`ContentPage`-派生的页面从.NET Standard 库项目或共享项目中，直接向本机项目中添加页面的优点是，可以使用本机视图扩展页。 然后可以使用 XAML 中名为本机视图`x:Name`和代码隐藏中被引用。 有关本机视图的详细信息，请参阅[本机视图](~/xamarin-forms/platform/native-views/index.md)。

使用 Xamarin.Forms 的过程[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-本机项目中派生的页面如下所示：

1. 对本机项目中添加的 Xamarin.Forms NuGet 包。
1. 添加[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页上，以及任何依赖项，对本机项目。
1. 调用 `Forms.Init` 方法。
1. 构造的实例[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页并将其转换为适当的本机类型使用以下扩展方法之一：`CreateViewController`适用于 iOS，`CreateSupportFragment`对于 Android，或`CreateFrameworkElement`为UWP。
1. 导航到的本机类型表示形式[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页使用本机导航 API。

必须通过调用初始化 Xamarin.Forms`Forms.Init`方法之前本机项目可以构造[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页。 选择何时执行此操作主要取决于你的应用程序流中最方便的时候 – 在应用程序启动时或之前无法执行其`ContentPage`-构造派生的页面。 在这篇文章，并随附的示例应用程序，`Forms.Init`在应用程序启动时调用方法。

> [!NOTE]
> **NativeForms**示例应用程序解决方案不包含任何 Xamarin.Forms 项目。 相反，它包含在 Xamarin.iOS 项目、 Xamarin.Android 项目，和 UWP 项目。 每个项目是使用本机窗体来使用本机项目[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页。 但是，没有的理由为何无法使用本机项目`ContentPage`-派生自.NET Standard 库项目或共享项目的页面。

在使用本机窗体，Xamarin.Forms 等功能[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)， [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)，和数据绑定引擎，仍的所有工作。 但是，必须使用本机导航 API 执行页面导航。

## <a name="ios"></a>iOS

在 iOS 上，`FinishedLaunching`重写中`AppDelegate`类通常是可以执行的应用程序启动相关的任务。 应用程序已启动，并通常可以配置主窗口和查看控制器重写之后调用它。 下面的代码示例演示`AppDelegate`示例应用程序中的类：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

`FinishedLaunching`方法执行以下任务：

- 通过调用初始化 Xamarin.Forms`Forms.Init`方法。
- 对引用`AppDelegate`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的一种机制`AppDelegate`类。
- `UIWindow`，这本机 iOS 应用程序中的视图的主容器创建。
- `FolderPath` 属性被初始化为设备上存储注释数据的路径。
- `NotesPage`类，该类是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生在 XAML 中定义的页中，会构造并转换为`UIViewController`使用`CreateViewController`扩展方法。
- `Title`的属性`UIViewController`设置，这将显示在`UINavigationBar`。
- 一个`AppNavigationController`创建用于管理分层导航。 这是一个自定义导航控制器类，派生自 `UINavigationController`。 `AppNavigationController` 对象管理视图控制器的堆栈，并且传递到构造函数的 `UIViewController` 最初在 `AppNavigationController` 加载时呈现。
- 将 `AppNavigationController` 对象设置为 `UIWindow`的顶级 `UIViewController`，并将 `UIWindow` 设置为应用程序的键窗口，并使其可见。

一次`FinishedLaunching`方法执行，在 Xamarin.Forms 中定义 UI`NotesPage`将显示类，如以下屏幕截图中所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-notespage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `AppDelegate.Instance`字段允许`AppDelegate.NavigateToNoteEntryPage`方法被调用，下面的代码示例中所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

`NavigateToNoteEntryPage`方法将为 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页`UIViewController`与`CreateViewController`扩展方法，并设置`Title`属性`UIViewController`。 `UIViewController`然后推送到`AppNavigationController`通过`PushViewController`方法。 因此，在 Xamarin.Forms 中定义 UI`NoteEntryPage`将显示类，如以下屏幕截图中所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-noteentrypage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

显示 `NoteEntryPage` 时，后退导航将从 `AppNavigationController`弹出 `NoteEntryPage` 类的 `UIViewController`，并将用户返回到 `UIViewController` 类的 `NotesPage`。 但是，从 iOS 本机导航堆栈中弹出 `UIViewController` 不会自动释放 `UIViewController` 并附加 `Page` 对象。 因此，`AppNavigationController` 类将重写 `PopViewController` 方法，以释放反向导航中的视图控制器：

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

`PopViewController` 重写对从 iOS 本机导航堆栈中弹出的 `UIViewController` 对象调用 `Dispose` 方法。 如果未执行此操作，则会导致 `UIViewController` 并使附加 `Page` 对象变得孤立。

> [!IMPORTANT]
> 不能对孤立对象进行垃圾回收，因此会导致内存泄露。

## <a name="android"></a>Android

在 Android 上，`OnCreate`重写中`MainActivity`类通常是可以执行的应用程序启动相关的任务。 下面的代码示例演示`MainActivity`示例应用程序中的类：

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

`OnCreate`方法执行以下任务：

- 通过调用初始化 Xamarin.Forms`Forms.Init`方法。
- 对引用`MainActivity`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的一种机制`MainActivity`类。
- `Activity`内容设置从布局资源。 在示例应用程序，布局组成`LinearLayout`，其中包含`Toolbar`，和一个`FrameLayout`充当片段容器。
- `Toolbar`检索并设置为在操作栏`Activity`，并设置操作栏标题。
- `FolderPath` 属性被初始化为设备上存储注释数据的路径。
- `NotesPage`类，该类是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生在 XAML 中定义的页中，会构造并转换为`Fragment`使用`CreateSupportFragment`扩展方法。
- `SupportFragmentManager`类创建并提交事务，用于替换`FrameLayout`实例与`Fragment`为`NotesPage`类。

片段有关的详细信息，请参阅[片段](~/android/platform/fragments/index.md)。

一次`OnCreate`方法执行，在 Xamarin.Forms 中定义 UI`NotesPage`将显示类，如以下屏幕截图中所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-notespage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainActivity.Instance`字段允许`MainActivity.NavigateToNoteEntryPage`方法被调用，下面的代码示例中所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

`NavigateToNoteEntryPage`方法将为 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页`Fragment`与`CreateSupportFragment`扩展方法，并将添加`Fragment`片段到后退堆栈。 因此，在 Xamarin.Forms 中定义 UI`NoteEntryPage`将显示，如以下屏幕截图中所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-noteentrypage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

时`NoteEntryPage`点击后的显示箭头会弹出`Fragment`有关`NoteEntryPage`片段 back 堆栈中，从已注册到用户`Fragment`为`NotesPage`类。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

`SupportFragmentManager`类具有`BackStackChanged`片段 back 堆栈的内容发生更改时激发的事件。 `OnCreate`中的方法`MainActivity`类包含此事件的匿名事件处理程序：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

此事件处理程序在操作栏上显示后退按钮，前提是没有一个或多个`Fragment`实例在片段上的后退堆栈。 点击后退按钮的响应由`OnOptionsItemSelected`重写：

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

`OnOptionsItemSelected`替代选择选项菜单中的项时调用。 此实现中弹出的当前片段中片段 back 堆栈，前提是已选择后退按钮，并且有一个或多个`Fragment`实例在片段上的后退堆栈。

### <a name="multiple-activities"></a>多个活动

当应用程序组成的多个活动[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生的页可以嵌入到每个活动。 在此方案中，`Forms.Init`仅在需要调用方法`OnCreate`的第一个重写`Activity`嵌入 Xamarin.Forms `ContentPage`。 但是，这会产生以下影响：

- 值`Xamarin.Forms.Color.Accent`来自`Activity`调用`Forms.Init`方法。
- 值`Xamarin.Forms.Application.Current`将与相关联`Activity`调用`Forms.Init`方法。

### <a name="choose-a-file"></a>选择文件

嵌入内容时[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生使用页面[ `WebView` ](xref:Xamarin.Forms.WebView) ，需要支持的 HTML"选择文件"按钮，则`Activity`将需要重写`OnActivityResult`方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

UWP，本机上`App`类通常是可以执行的应用程序启动相关的任务。 Xamarin.Forms 通常在初始化时，在 Xamarin.Forms UWP 应用程序中`OnLaunched`重写中本机`App`类中，以传递`LaunchActivatedEventArgs`参数`Forms.Init`方法。 出于此原因，本机 UWP 应用程序中使用 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生的页面可以最轻松地调用`Forms.Init`方法从`App.OnLaunched`方法。

默认情况下，本机`App`类启动`MainPage`的第一页作为应用程序的类。 下面的代码示例演示`MainPage`示例应用程序中的类：

```csharp
public sealed partial class MainPage : Page
{
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

`MainPage`构造函数将执行以下任务：

- 为页上，启用缓存，以便新`MainPage`当用户导航回页不构造。
- 对引用`MainPage`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的一种机制`MainPage`类。
- `FolderPath` 属性被初始化为设备上存储注释数据的路径。
- `NotesPage`类，该类是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生在 XAML 中定义的页中，会构造并转换为`FrameworkElement`使用`CreateFrameworkElement`扩展方法，然后将设置为的内容`MainPage`类。

一次`MainPage`构造函数已执行，在 Xamarin.Forms 中定义 UI`NotesPage`将显示类，如以下屏幕截图中所示：

[![使用用 Xamarin 定义的 UI 的 UWP 应用程序的屏幕截图](native-forms-images/uwp-notespage.png "具有 Xamarin. Forms XAML UI 的 UWP 应用")](native-forms-images/uwp-notespage-large.png#lightbox "具有 Xamarin. Forms XAML UI 的 UWP 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainPage.Instance`字段允许`MainPage.NavigateToNoteEntryPage`方法被调用，下面的代码示例中所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

通常使用执行 UWP 中的导航`Frame.Navigate`方法，它使用`Page`参数。 Xamarin.Forms 定义了`Frame.Navigate`扩展方法采用[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页实例。 因此，当`NavigateToNoteEntryPage`执行方法时，在 Xamarin.Forms 中定义的 UI`NoteEntryPage`将显示，如以下屏幕截图中所示：

[![使用用 Xamarin 定义的 UI 的 UWP 应用程序的屏幕截图](native-forms-images/uwp-noteentrypage.png "具有 Xamarin. Forms XAML UI 的 UWP 应用")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有 Xamarin. Forms XAML UI 的 UWP 应用")

时`NoteEntryPage`点击后的显示箭头会弹出`FrameworkElement`有关`NoteEntryPage`从应用程序内 back 堆栈中，返回到用户`FrameworkElement`为`NotesPage`类。

### <a name="enable-page-resizing-support"></a>启用页面大小调整支持

调整 UWP 应用程序窗口的大小时，还应调整 Xamarin 内容的大小。 这是通过在 `MainPage` 构造函数中注册 `Loaded` 事件的事件处理程序来实现的：

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

当该页布局、呈现并准备好交互时，将触发 `Loaded` 事件，并在响应中执行 `OnMainPageLoaded` 方法：

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

`OnMainPageLoaded` 方法为 `Frame.SizeChanged` 事件注册匿名事件处理程序，当 `Frame`上的 `ActualHeight` 或 `ActualWidth` 属性发生更改时，将引发该事件处理程序。 作为响应，将通过调用 `Layout` 方法调整活动页的 Xamarin 内容的大小。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

在 UWP 中，应用程序必须启用后退导航的所有硬件和软件后退按钮，在不同设备外观造型上。 这可以通过注册 `BackRequested` 事件的事件处理程序来完成，该事件处理程序可以在 `MainPage` 构造函数中执行：

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

当启动应用程序时，`GetForCurrentView`方法检索`SystemNavigationManager`对象与当前的视图，然后注册的事件处理程序`BackRequested`事件。 应用程序仅接收此事件，如果它是前台应用程序，并在响应中，调用`OnBackRequested`事件处理程序：

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
        noteEntryPage = null;
    }
}
```

`OnBackRequested`事件处理程序调用`GoBack`方法的应用程序和设置在根框架`BackRequestedEventArgs.Handled`属性设置为`true`将该事件标记为已处理。 如果无法将事件标记为 "已处理"，则可能导致事件被忽略。

该应用程序选择是否在标题栏上显示 "后退" 按钮。 为此，可在 `App` 类中将 `AppViewBackButtonVisibility` 属性设置为 `AppViewBackButtonVisibility` 枚举值之一：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`事件处理程序，响应执行`Navigated`页面导航发生时，事件的激发，更新的标题栏中后退按钮的可见性。 这可确保，标题栏后退按钮是可见的如果应用程序内 back 堆栈不为空，或从中删除的标题栏中应用程序的 back 堆栈是否为空。

UWP 上后退导航的支持的详细信息，请参阅[导航历史记录和向后导航适用于 UWP 应用](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相关链接

- [NativeForms （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [本机视图](~/xamarin-forms/platform/native-views/index.md)
