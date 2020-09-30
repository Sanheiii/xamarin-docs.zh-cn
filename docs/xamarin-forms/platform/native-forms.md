---
title: Xamarin.Forms 在 Xamarin 本机项目中
description: 本文介绍如何使用直接添加到 Xamarin 本机项目的 ContentPage 派生页面，以及如何在这些页面之间导航。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e34144f253b19796ea6ec83cb3873fe373aeb914
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560606"
---
# <a name="no-locxamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms 在 Xamarin 本机项目中

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

通常， Xamarin.Forms 应用程序包含一个或多个派生自的页面 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，这些页面由 .NET Standard 库项目或共享项目中的所有平台共享。 但是，本机窗体允许 `ContentPage` 派生页面直接添加到本机 Xamarin iOS、Xamarin 和 UWP 应用程序。 与本机项目 `ContentPage` 从 .NET Standard 库项目或共享项目使用派生页面相比，将页面直接添加到本机项目的优势在于，可以使用本机视图扩展页面。 然后，可以在 XAML 中将本机视图命名为，并在代码隐藏中对其进行 `x:Name` 引用。 有关本机视图的详细信息，请参阅 [本机视图](~/xamarin-forms/platform/native-views/index.md)。

Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在本机项目中使用派生页的过程如下所示：

1. 将 Xamarin.Forms NuGet 包添加到本机项目。
1. 将 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 派生的页和任何依赖项添加到本机项目。
1. 调用 `Forms.Init` 方法。
1. [`ContentPage`](xref:Xamarin.Forms.ContentPage)使用以下扩展方法之一构造派生页面的实例并将其转换为适当的本机类型： `CreateViewController` 适用于 IOS、 `CreateSupportFragment` Android 或 `CreateFrameworkElement` UWP。
1. 使用本机导航 API 定位到派生页面的本机类型表示形式 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

Xamarin.Forms 必须通过调用方法进行初始化， `Forms.Init` 本机项目才能构造 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 派生页。 选择执行此操作的时间主要取决于在应用程序流中最便利的时间，可以在应用程序启动时执行，也可以在 `ContentPage` 构造派生页面之前执行。 在本文和随附的示例应用程序中，将 `Forms.Init` 在应用程序启动时调用方法。

> [!NOTE]
> **NativeForms**示例应用程序解决方案不包含任何 Xamarin.Forms 项目。 相反，它包括 Xamarin iOS 项目、Xamarin Android 项目和 UWP 项目。 每个项目都是使用本机窗体来使用派生页面的本机项目 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 但是，本地项目无法使用 `ContentPage` 从 .NET Standard 库项目或共享项目派生的页面的原因。

使用本机窗体时，等功能（如 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 、 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 和）仍能正常工作。 但是，必须使用本机导航 API 执行页面导航。

## <a name="ios"></a>iOS

在 iOS 上， `FinishedLaunching` 类中的替代 `AppDelegate` 通常是执行与应用程序启动相关的任务的位置。 它在应用程序启动后调用，通常会被重写以配置主窗口和视图控制器。 下面的代码示例演示 `AppDelegate` 示例应用程序中的类：

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

`FinishedLaunching` 方法执行以下任务：

- Xamarin.Forms 通过调用方法进行初始化 `Forms.Init` 。
- 对类的引用 `AppDelegate` 存储在 `static` `Instance` 字段中。 这是为了为其他类提供一种机制，用于调用类中定义的方法 `AppDelegate` 。
- 创建的是 `UIWindow` 本机 iOS 应用程序中的视图的主要容器。
- `FolderPath`将属性初始化为设备上存储注释数据的路径。
- `NotesPage`类（ Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定义的派生页） `UIViewController` 使用扩展方法构造并转换为 `CreateViewController` 。
- `Title`设置的属性，该属性 `UIViewController` 将显示在上 `UINavigationBar` 。
- `AppNavigationController`创建用于管理分层导航的。 这是从派生的自定义导航控制器类 `UINavigationController` 。 `AppNavigationController`对象管理视图控制器的堆栈，并且 `UIViewController` 传递到构造函数中的将在加载时最初显示 `AppNavigationController` 。
- 将 `AppNavigationController` 对象设置为的顶级 `UIViewController` `UIWindow` ，并 `UIWindow` 将设置为应用程序的键窗口，并使其可见。

`FinishedLaunching`执行方法后，将显示类中定义的 UI Xamarin.Forms `NotesPage` ，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-notespage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

与 UI 交互（例如通过点击 **+** [`Button`](xref:Xamarin.Forms.Button) ），将导致代码隐藏执行中的以下事件处理程序 `NotesPage` ：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `AppDelegate.Instance` 字段允许 `AppDelegate.NavigateToNoteEntryPage` 调用方法，如以下代码示例所示：

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

`NavigateToNoteEntryPage`方法 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用扩展方法将派生页转换为 `UIViewController` `CreateViewController` ，并设置 `Title` 的属性 `UIViewController` 。 `UIViewController`然后由方法推送到 `AppNavigationController` `PushViewController` 。 因此，会显示类中定义的 UI Xamarin.Forms `NoteEntryPage` ，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-noteentrypage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

当 `NoteEntryPage` 显示时，后退导航将从中弹出 `UIViewController` 类的 `NoteEntryPage` `AppNavigationController` ，并将用户返回到类的 `UIViewController` `NotesPage` 。 但是， `UIViewController` 从 iOS 本机导航堆栈中弹出会自动释放 `UIViewController` 和附加的 `Page` 对象。 因此， `AppNavigationController` 类会重写 `PopViewController` 方法，以释放反向导航中的视图控制器：

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

`PopViewController`重写 `Dispose` `UIViewController` 从 iOS 本机导航堆栈中弹出的对象上调用方法。 如果不这样做，将导致 `UIViewController` 和附加的 `Page` 对象被孤立。

> [!IMPORTANT]
> 不能对孤立对象进行垃圾回收，因此会导致内存泄露。

## <a name="android"></a>Android

在 Android 上， `OnCreate` 类中的替代 `MainActivity` 通常是执行与应用程序启动相关的任务的位置。 下面的代码示例演示 `MainActivity` 示例应用程序中的类：

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

`OnCreate` 方法执行以下任务：

- Xamarin.Forms 通过调用方法进行初始化 `Forms.Init` 。
- 对类的引用 `MainActivity` 存储在 `static` `Instance` 字段中。 这是为了为其他类提供一种机制，用于调用类中定义的方法 `MainActivity` 。
- `Activity`内容是从布局资源设置的。 在示例应用程序中，布局 `LinearLayout` 包含一个，它包含一个 `Toolbar` 和一个用作 `FrameLayout` 片段容器的。
- `Toolbar`检索并将设置为的操作栏 `Activity` ，并设置操作栏标题。
- `FolderPath`将属性初始化为设备上存储注释数据的路径。
- `NotesPage`类（ Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定义的派生页） `Fragment` 使用扩展方法构造并转换为 `CreateSupportFragment` 。
- `SupportFragmentManager`类创建并提交一个事务，该事务将 `FrameLayout` 实例替换 `Fragment` 为类的 `NotesPage` 。

有关片段的详细信息，请参阅 [片段](~/android/platform/fragments/index.md)。

`OnCreate`执行方法后，将显示类中定义的 UI Xamarin.Forms `NotesPage` ，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-notespage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

与 UI 交互（例如通过点击 **+** [`Button`](xref:Xamarin.Forms.Button) ），将导致代码隐藏执行中的以下事件处理程序 `NotesPage` ：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainActivity.Instance` 字段允许 `MainActivity.NavigateToNoteEntryPage` 调用方法，如以下代码示例所示：

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

`NavigateToNoteEntryPage`方法 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用扩展方法将派生的页转换为 `Fragment` `CreateSupportFragment` ，并将添加 `Fragment` 到片段返回堆栈中。 因此，将显示中定义的 UI Xamarin.Forms `NoteEntryPage` ，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-noteentrypage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

当 `NoteEntryPage` 显示时，点击后退箭头将 `Fragment` `NoteEntryPage` 从片段的堆栈中弹出，并将用户返回到 `Fragment` 类的 `NotesPage` 。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

`SupportFragmentManager`类有一个在 `BackStackChanged` 片段的内容发生更改时触发的事件。 `OnCreate`类中的方法 `MainActivity` 包含此事件的匿名事件处理程序：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

此事件处理程序将在操作栏上显示一个后退按钮，前提是该片段上有一个或多个 `Fragment` 实例。 通过重写来处理点击 "后退" 按钮的响应 `OnOptionsItemSelected` ：

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

`OnOptionsItemSelected`只要选择了 "选项" 菜单中的项，就会调用该重写。 如果已选择 "后退" 按钮，并且 `Fragment` 片段反向堆栈上有一个或多个实例，则此实现将从片段返回堆栈中弹出当前片段。

### <a name="multiple-activities"></a>多个活动

如果应用程序由多个活动组成，则 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可将派生的页面嵌入到每个活动中。 在这种情况下， `Forms.Init` 只需在 `OnCreate` 第一个嵌入的重写中调用方法 `Activity` Xamarin.Forms `ContentPage` 。 但是，这会产生以下影响：

- 的值 `Xamarin.Forms.Color.Accent` 将从调用该方法的中获取 `Activity` `Forms.Init` 。
- 的值 `Xamarin.Forms.Application.Current` 将与调用方法的关联 `Activity` `Forms.Init` 。

### <a name="choose-a-file"></a>选择文件

在嵌入 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用 [`WebView`](xref:Xamarin.Forms.WebView) 需要支持 HTML "选择文件" 按钮的派生页面时， `Activity` 将需要重写 `OnActivityResult` 方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP 上，本机 `App` 类通常用于执行与应用程序启动相关的任务。 Xamarin.Forms 通常在 Xamarin.Forms UWP 应用程序中，在 `OnLaunched` 本机类的重写中初始化， `App` 以将 `LaunchActivatedEventArgs` 参数传递给 `Forms.Init` 方法。 出于此原因，使用派生页的本机 UWP 应用程序 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以很容易地 `Forms.Init` 从方法中调用方法 `App.OnLaunched` 。

默认情况下，本机 `App` 类将 `MainPage` 类作为应用程序的第一页启动。 下面的代码示例演示 `MainPage` 示例应用程序中的类：

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

`MainPage`构造函数执行以下任务：

- 为页面启用了缓存，以便 `MainPage` 在用户导航回页面时不会构造新的。
- 对类的引用 `MainPage` 存储在 `static` `Instance` 字段中。 这是为了为其他类提供一种机制，用于调用类中定义的方法 `MainPage` 。
- `FolderPath`将属性初始化为设备上存储注释数据的路径。
- `NotesPage`类（ Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定义的派生页）使用扩展方法构造并转换为 `FrameworkElement` `CreateFrameworkElement` ，然后设置为类的内容 `MainPage` 。

`MainPage`执行构造函数后，将显示类中定义的 UI Xamarin.Forms `NotesPage` ，如以下屏幕截图所示：

[![UWP 应用程序的屏幕截图，此应用程序使用通过：：： no (Xamarin. Forms) ：：： XAML 定义的 UI](native-forms-images/uwp-notespage.png "具有：：： no (Xamarin 的 UWP 应用) ：：： XAML UI")](native-forms-images/uwp-notespage-large.png#lightbox "具有：：： no (Xamarin 的 UWP 应用) ：：： XAML UI")

与 UI 交互（例如通过点击 **+** [`Button`](xref:Xamarin.Forms.Button) ），将导致代码隐藏执行中的以下事件处理程序 `NotesPage` ：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainPage.Instance` 字段允许 `MainPage.NavigateToNoteEntryPage` 调用方法，如以下代码示例所示：

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

UWP 中的导航通常使用方法执行 `Frame.Navigate` ，该方法采用 `Page` 自变量。 Xamarin.Forms 定义一个 `Frame.Navigate` 扩展方法，该方法采用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 派生的页实例。 因此，当 `NavigateToNoteEntryPage` 方法执行时，将显示中定义的 UI Xamarin.Forms `NoteEntryPage` ，如以下屏幕截图所示：

[![UWP 应用程序的屏幕截图，此应用程序使用通过：：： no (Xamarin. Forms) ：：： XAML 定义的 UI](native-forms-images/uwp-noteentrypage.png "具有：：： no (Xamarin 的 UWP 应用) ：：： XAML UI")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有：：： no (Xamarin 的 UWP 应用) ：：： XAML UI")

当 `NoteEntryPage` 显示时，点击后退箭头将 `FrameworkElement` `NoteEntryPage` 从应用程序后端堆栈中弹出，并将用户返回到 `FrameworkElement` 类的 `NotesPage` 。

### <a name="enable-page-resizing-support"></a>启用页面大小调整支持

当调整 UWP 应用程序窗口的大小时， Xamarin.Forms 还应调整内容的大小。 这可通过 `Loaded` 在构造函数中注册事件的事件处理程序来完成 `MainPage` ：

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

`Loaded`当该页布局、呈现并准备好交互时，将触发该事件，并 `OnMainPageLoaded` 在响应中执行方法：

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

`OnMainPageLoaded`方法注册事件的匿名事件处理程序，该事件在 `Frame.SizeChanged` `ActualHeight` 上的或 `ActualWidth` 属性发生更改时引发 `Frame` 。 作为响应，将 Xamarin.Forms 通过调用方法调整活动页的内容的大小 `Layout` 。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

在 UWP 上，应用程序必须在不同的设备外观上为所有硬件和软件后退按钮启用后退导航。 这可以通过注册事件的事件处理程序来完成，该事件处理程序 `BackRequested` 可以在 `MainPage` 构造函数中执行：

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

当应用程序启动时， `GetForCurrentView` 方法将检索 `SystemNavigationManager` 与当前视图关联的对象，然后注册事件的事件处理程序 `BackRequested` 。 仅当应用程序为前台应用程序时，应用程序才会收到此事件，并在响应中调用 `OnBackRequested` 事件处理程序：

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

`OnBackRequested`事件处理程序 `GoBack` 在应用程序的根框架上调用方法，并将 `BackRequestedEventArgs.Handled` 属性设置为， `true` 以将事件标记为已处理。 如果无法将事件标记为 "已处理"，则可能导致事件被忽略。

该应用程序选择是否在标题栏上显示 "后退" 按钮。 为此，可将 `AppViewBackButtonVisibility` 属性设置为 `AppViewBackButtonVisibility` 类中的枚举值之一 `App` ：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`发生页面导航时，为响应事件触发而执行的事件处理程序将 `Navigated` 更新标题栏后退按钮的可见性。 这可确保在应用程序后端堆栈不为空时，标题栏的 "后退" 按钮可见，或在应用内返回堆栈为空的情况下从标题栏中删除。

有关 UWP 的后退导航支持的详细信息，请参阅 [uwp 应用的导航历史记录和向后导航](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相关链接

- [NativeForms (示例) ](/samples/xamarin/xamarin-forms-samples/native2forms)
- [本机视图](~/xamarin-forms/platform/native-views/index.md)