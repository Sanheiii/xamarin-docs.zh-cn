---
title: 片段演练 - 第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: c0d4b0d1a31be43d16fb69eba18c07815631f496
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453930"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段演练 &ndash; 横向

[片段演练 &ndash; 第 1 部分](./walkthrough.md) 演示了如何在面向手机上较小屏幕的 Android 应用中创建和使用片段。 本演练的下一步是修改应用程序以利用平板电脑上的额外水平空间 &ndash; 会有一个活动始终是戏剧列表 (`TitlesFragment`)，而 `PlayQuoteFragment` 会动态添加到活动中，以响应用户进行的选择：

[![在平板电脑上运行的应用](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

在横向模式下运行的手机也会受益于此增强功能：

[![在处于横向模式的 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新应用以处理横向方向

以下修改基于在[片段演练 - 手机](./walkthrough.md)中完成的工作而构建

1. 创建备用布局以显示 `TitlesFragment` 和 `PlayQuoteFragment`。
1. 更新 `TitlesFragment` 以检测设备是否同时显示两个片段并相应地更改行为。
1. 当设备处于横向模式时，将 `PlayQuoteActivity` 更新为关闭状态。

## <a name="1-create-an-alternate-layout"></a>1.创建备用布局

当在 Android 设备上创建主活动时，Android 会基于设备的方向确定要加载的布局。 默认情况下，Android 会提供 Resources/layout/activity_main.axml  布局文件。 对于在横向模式下加载的设备，Android 会提供 Resources/layout-land/activity_main.axml  布局文件。 有关 [Android 资源](../../../app-fundamentals/resources-in-android/index.md)的指南包含有关 Android 如何确定要为应用程序加载的资源文件的更多详细信息。

按照[备用布局](../../../user-interface/android-designer/alternative-layout-views.md)指南中所述的步骤，创建面向横向  方向的备用布局。 这应将新的布局资源文件添加到项目 (Resources/layout/activity_main.axml)  ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![解决方案资源管理器中的备用布局](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![Solution Pad 中的备用布局](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

创建备用布局之后，编辑文件 Resources/layout-land/activity_main.axml  的源代码，使它与此 XML 匹配：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

向活动的根视图提供了资源 ID `two_fragments_layout`，它具有两个子视图 `fragment` 和 `FrameLayout`。 静态加载 `fragment` 时，`FrameLayout` 充当“占位符”，会在运行时由 `PlayQuoteFragment` 所替换。 每次在 `TitlesFragment` 中选择新戏剧时，`playquote_container` 都会使用 `PlayQuoteFragment` 的新实例进行更新。

每个子视图都会占据其父视图的整个高度。 每个子视图的宽度由 `android:layout_weight` 和 `android:layout_width` 属性进行控制。 在此示例中，每个子视图会占据父视图提供的宽度的 50%。 有关布局权重  的详细信息，请参阅[有关 LinearLayout 的 Google 文档](https://developer.android.com/guide/topics/ui/layout/linear.html)。

## <a name="2-changes-to-titlesfragment"></a>2.对 TitlesFragment 的更改

创建备用布局后，需要更新 `TitlesFragment`。 当应用在一个活动上显示两个片段时，`TitlesFragment` 应加载父活动中的 `PlayQuoteFragment`。 否则，`TitlesFragment` 应启动承载 `PlayQuoteFragment` 的 `PlayQuoteActivity`。 布尔型标志会帮助 `TitlesFragment` 确定它应该使用的行为。 此标志会在 `OnActivityCreated` 方法中进行初始化。

首先，在 `TitlesFragment` 类的顶部添加一个实例变量：

```csharp
bool showingTwoFragments;
```

然后，将以下代码片段添加到 `OnActivityCreated` 以初始化该变量： 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

如果设备在横向模式下运行，则资源 ID 为 `playquote_container` 的 `FrameLayout` 会在屏幕上可见，因此 `showingTwoFragments` 会初始化为 `true`。 如果设备在纵向模式下运行，则 `playquote_container` 不会显示在屏幕上，因此 `showingTwoFragments` 会是 `false`。

`ShowPlayQuote` 方法需要更改它显示引言的方式 &ndash; 在片段中或启动新活动。  更新 `ShowPlayQuote` 方法，以便在显示两个片段时加载片段，否则应启动活动：

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

如果用户选择的戏剧与当前在 `PlayQuoteFragment` 中显示的戏剧不同，则会创建一个新 `PlayQuoteFragment`，它会替换 `FragmentTransaction` 上下文中的 `playquote_container` 内容。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整代码

完成对 `TitlesFragment` 的所有上述更改之后，完整类应与以下代码相符：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3.对 PlayQuoteActivity 的更改

需要注意的最后一个细节是：当设备处于横向模式时，不需要 `PlayQuoteActivity`。 如果设备处于横向模式，则 `PlayQuoteActivity` 应不可见。 更新 `PlayQuoteActivity` 的 `OnCreate` 方法，使它关闭自己。 此代码是 `PlayQuoteActivity.OnCreate` 的最终版本：

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

此修改会针对设备方向添加检查。 如果它处于横向模式，则 `PlayQuoteActivity` 会关闭自己。

## <a name="4-run-the-application"></a>4.运行此应用程序

完成这些更改后，运行应用，将设备旋转到横向模式（如有必要），然后选择一个戏剧。 引言应与戏剧列表显示在同一个屏幕上：

[![在处于横向模式的 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板电脑上运行的应用](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)