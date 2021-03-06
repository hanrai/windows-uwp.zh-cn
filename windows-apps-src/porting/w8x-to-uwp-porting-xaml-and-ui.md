---
description: 以声明性 XAML 标记的形式定义 UI 的做法非常好地将通用 8.1 应用转换为通用 Windows 平台 (UWP) 应用。
title: 将 Windows 运行时 8.x XAML 和 UI 移植到 UWP
ms.assetid: 78b86762-7359-474f-b1e3-c2d7cf9aa907
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5fcc4312cd238279e01e275d2525c9ac8df98190
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322368"
---
# <a name="porting-windows-runtime-8x-xaml-and-ui-to-uwp"></a>将 Windows 运行时 8.x XAML 和 UI 移植到 UWP


上一主题是[疑难解答](w8x-to-uwp-troubleshooting.md)。

以声明性 XAML 标记的形式定义 UI 的做法非常好地将通用 8.1 应用转换为通用 Windows 平台 (UWP) 应用。 你会发现，你的大多数标记是兼容的，尽管你可能需要针对正在使用的系统资源键或自定义模板作相应调整。 视图模型中的强制性代码只需稍作更改或无需更改。 操纵 UI 元素的表示层中的许多强制性代码（甚至是大部分代码）也应易于移植。

## <a name="imperative-code"></a>强制性代码

如果你只是希望转到项目构建阶段，你可以注释或去掉任何非必要的代码。 然后循环访问一次一个问题，请参阅本部分中的以下主题 (和上一个主题：[故障排除](w8x-to-uwp-troubleshooting.md))，直到任何生成和运行时问题是烫出和您的端口已完成。

## <a name="adaptiveresponsive-ui"></a>自适应/响应式 UI

由于你的应用可以在种类可能很广泛的设备上运行（每种设备都具有自己的屏幕大小和分辨率），你不仅需要完成移植应用的最少步骤，而且还需要定制你的 UI 以使其在这些设备上具有最佳的外观。 你可以使用自适应视觉状态管理器功能来动态检测窗口大小并更改布局作为响应，还可以使用 Bookstore2 案例研究主题中的[自适应 UI](w8x-to-uwp-case-study-bookstore2.md) 部分中所示的有关如何执行此操作的示例。

## <a name="back-button-handling"></a>后退按钮处理

适用于通用 8.1 应用、 Windows 运行时 8.x 应用和 Windows Phone 应用商店应用程序具有不同的方法，显示 UI 和处理后退按钮的事件。 但是，对于 Windows 10 应用，可以在应用中使用一种方法。 在移动设备上，该按钮作为设备上的电容性按钮或外壳中的按钮向你提供。 在桌面设备上，只要你的应用内可进行后退导航，你便可以向该应用的镶边添加一个按钮，它将显示在窗口化的应用的标题栏中或平板电脑模式下的任务栏中。 后退按钮事件是所有设备系列的通用概念，并且硬件或软件中实现的按钮会引发相同的 [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.backrequested) 事件。

以下示例适用于所有设备系列，并且对将相同的处理操作应用于所有页面的情形以及不需要确认导航的情形（例如，就未保存的更改发出警告）十分有用。

```csharp
   // app.xaml.cs

    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        [...]

        Windows.UI.Core.SystemNavigationManager.GetForCurrentView().BackRequested += App_BackRequested;
        rootFrame.Navigated += RootFrame_Navigated;
    }

    private void RootFrame_Navigated(object sender, NavigationEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        // Note: On device families that have no title bar, setting AppViewBackButtonVisibility can safely execute 
        // but it will have no effect. Such device families provide back button UI for you.
        if (rootFrame.CanGoBack)
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Visible;
        }
        else
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Collapsed;
        }
    }

    private void App_BackRequested(object sender, Windows.UI.Core.BackRequestedEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        if (rootFrame.CanGoBack)
        {
            rootFrame.GoBack();
        }
    }
```

还有一个适用于所有设备系列的以编程方式退出应用的方法。

```csharp
   Windows.UI.Xaml.Application.Current.Exit();
```

## <a name="charms"></a>超级按钮

无需更改任何代码，集成了超级按钮，但需要将一些 UI 添加到您的应用程序来代替超级按钮栏中，不属于 Windows 10 shell。 Windows 10 上运行一个通用的 8.1 应用程序都有其自己的替代由系统呈现 chrome 应用的标题栏中提供用户界面。

## <a name="controls-and-control-styles-and-templates"></a>控件、控件样式和模板

Windows 10 上运行的通用 8.1 应用将保留 8.1 外观和行为对于控件。 但是，当你的应用移植到 Windows 10 应用，有一些差异的外观和行为，需要注意。 体系结构和设计的控件是 Windows 10 应用实质上是不变，因此所做的更改是主要是关于设计语言、 简化和可用性改进。

**请注意**   PointerOver 视觉状态是自定义样式/模板中的 Windows 10 应用和 Windows 运行时 8.x 应用，但不是在 Windows Phone 应用商店应用程序相关。 出于此原因 （和由于 Windows 10 应用支持的系统资源密钥），我们建议你重复使用每个模板的样式自定义 Windows 运行时 8.x 应用中时要移植到 Windows 10 应用。
如果你想要确保每个模板自定义的样式使用可视状态的最新一并受益于默认样式/模板，所做的性能改进，然后编辑新的 Windows 10 默认模板的副本并重新应用您自定义的。 性能改进的一个示例是，以前包含 **ContentPresenter** 或面板的任何 **Border** 已被删除，而子元素现在可呈现边框。

下面是对控件所做的更改的一些更具体的示例。

| 控件名称 | “更改” |
|--------------|--------|
| **AppBar**   | 如果使用的**AppBar**控件 ([**CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar)建议改为)，则它不隐藏默认情况下，在 Windows 10 应用中。 你可以使用 [**AppBar.ClosedDisplayMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode) 属性对其进行控制。 |
| **AppBar**、[**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | 在 Windows 10 应用中， **AppBar**并[ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar)具有**查看更多**（省略号） 按钮。 |
| [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | 在 Windows 运行时 8.x 应用中，辅助命令的[ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar)是始终可见的。 在 Windows Phone 应用商店应用中，并在 Windows 10 应用中，不出现，直到命令栏中打开。 |
| [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | 对于 Windows Phone 应用商店应用，[**CommandBar.IsSticky**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.issticky) 的值不影响该栏是否可通过轻触消除。 对于 Windows 10 应用，如果**issticky 设置**设置为 true，然后**CommandBar**忽略浅解除手势。 |
| [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | 在 Windows 10 应用中， [ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar)不会处理[ **EdgeGesture.Completed** ](https://docs.microsoft.com/uwp/api/windows.ui.input.edgegesture.completed)也不[ **UIElement.RightTapped** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.righttapped)事件。 同时也不会响应点击或向上轻扫操作。 你仍可以选择处理这些事件并设置 [**IsOpen**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.isopen)。 |
| [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker)， [ **TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker) | 通过从视觉上更改 [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker) 和 [**TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker)，查看你的应用的外观。 对于移动设备上运行 Windows 10 应用，这些控件不能再定位到所选内容的页面，而是使用 light 可不予理会的弹出窗口。 |
| [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker)， [ **TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker) | 在 Windows 10 应用中，无法将放[ **DatePicker** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker)或[ **TimePicker** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker)飞出中。如果你希望这些控件显示在弹出式控件中，则可以使用 [**DatePickerFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePickerFlyout) 和 [**TimePickerFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePickerFlyout)。 |
| **GridView**、**ListView** | 有关 **GridView**/**ListView**，请参阅 [GridView 和 ListView 更改](#gridview-and-listview-changes)。 |
| [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) | 在 Windows Phone 应用商店应用中，[**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) 控件从最后一部分环绕到第一部分。 在 Windows 运行时 8.x 应用中，并在 Windows 10 应用中，不会环绕中心部分。 |
| [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) | 在 Windows Phone 应用商店应用中，[**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) 控件的背景图像相对于中心区域在视差中移动。 在 Windows 运行时 8.x 应用中，并在 Windows 10 应用中，不使用视差。 |
| [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub)  | 在通用 8.1 应用中，[**HubSection.IsHeaderInteractive**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.hubsection.isheaderinteractive) 属性会导致区域标头（和呈现在它旁边的 V 型字型）变得具有交互性。 在 Windows 10 应用中，没有交互"查看更多"功能可见性： 旁边标头，但标头本身是不可交互。 **IsHeaderInteractive** 仍用于确定交互是否引发 [**Hub.SectionHeaderClick**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.hub.sectionheaderclick) 事件。 |
| **MessageDialog** | 如果你使用的是 **MessageDialog**，请考虑改用更加灵活的 [**ContentDialog**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)。 另请参阅 [XAML UI 基础知识](https://go.microsoft.com/fwlink/p/?linkid=619992)示例。 |
| **ListPickerFlyout**、**PickerFlyout**  | **ListPickerFlyout**并**PickerFlyout**不推荐使用的 Windows 10 应用。 对于单选浮出控件，请使用 [**MenuFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyout)；对于更复杂的体验，请使用 [**Flyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout)。 |
| [**PasswordBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox) | [ **PasswordBox.IsPasswordRevealButtonEnabled** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.ispasswordrevealbuttonenabled)属性已弃用在 Windows 10 应用中，并将其设置不起作用。 使用[ **PasswordBox.PasswordRevealMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordrevealmode)相反，其默认值为**扫视**（在其中为关注标志符号显示，如 Windows 运行时 8.x 应用中）。 另请参阅[密码框指南](https://docs.microsoft.com/windows/uwp/controls-and-patterns/password-box)。 |
| [**Pivot**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) | [  **Pivot**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) 控件现在是通用控件，它的使用不再限于移动设备。 |
| [**SearchBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SearchBox) | 尽管已在通用设备系列中实现了 [**SearchBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.searchbox)，但它无法在移动设备上正常运行。 请参阅[弃用 SearchBox 以支持 AutoSuggestBox](#searchbox-deprecated-in-favor-of-autosuggestbox)。 |
| **SemanticZoom** | 有关 **SemanticZoom**，请参阅 [SemanticZoom 更改](#semanticzoom-changes)。 |
| [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer)  | [  **ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) 的某些默认属性已更改。 [**HorizontalScrollMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode)是**自动**， [ **VerticalScrollMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode)是**自动**，和[ **ZoomMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.zoommode)是**已禁用**。 如果新的默认值不适合你的应用，你可以使用样式更改它们，或对控件本身的本地值进行更改。  |
| [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | 在 Windows 运行时 8.x 应用中，拼写检查默认处于关闭状态为[**文本框中**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)。 在 Windows Phone 应用商店应用中，并在 Windows 10 应用中，它是在默认情况下。 |
| [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | [  **TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) 的默认字体大小已从 11 更改为 15。 |
| [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | [  **TextBox.TextReadingOrder**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.textreadingorder) 的默认值已从 **Default** 更改为 **DetectFromContent**。 如果不适用，则使用 **UseFlowDirection**。 **Default** 已弃用。 |
| 多种 | 强调文字颜色应用到 Windows Phone 应用商店应用和 Windows 10 应用，而不是属于 Windows 运行时 8.x 应用。  |

有关 UWP 应用控件的详细信息，请参阅[按功能列出的控件](https://docs.microsoft.com/windows/uwp/controls-and-patterns/controls-by-function)、[控件列表](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/)和[控件指南](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/index)。

##  <a name="design-language-in-windows10"></a>Windows 10 中的设计语言

有一些小的但很重要的设计语言之间差异 Universal 8.1 应用和 Windows 10 应用。 有关所有详细信息，请参阅[设计](https://developer.microsoft.com/en-us/windows/apps/design)。 不考虑设计语言更改，我们的设计原则始终保持一致：关注细节却又力求简洁（专注于内容而不是外观），显著减少视觉元素，始终忠实于数字领域；使用可视化层次结构（尤其是版式）；基于网格进行设计；通过流畅的动画带给你生动的体验。

## <a name="effective-pixels-viewing-distance-and-scale-factors"></a>有效像素、观看距离和比例因子

以前，视图像素是从设备的实际物理大小和分辨率抽象表示 UI 元素的大小和布局的方法。 视图像素现在已发展为有效像素，下面是对该术语、该术语的意义以及它所提供的额外价值的说明。

术语“分辨率”是指像素密度的度量，而不是通常认为的像素计数。 “有效分辨率”是构成图像或字形的物理像素对肉眼解析的方法，因为设备的观看距离和物理像素大小之间有差异（像素密度是物理像素大小的倒数）。 有效分辨率是构建周围体验的良好指标，因为它是以用户为中心的。 通过了解所有因素并控制 UI 元素的大小，即可优化用户的体验。

不同设备的宽度的有效像素是不同的，范围从最小设备的 320 像素到中等大小监视器的 1024 像素，甚至更高宽度的有效像素。 你只需像往常那样继续使用可自动调整大小的元素和动态布局面板。 在某些情况下，你还需要将 XAML 标记中的 UI 元素的相关属性设置为固定大小。 根据应用运行所在的设备和用户所设置的显示设置，比例因子将自动应用于应用。 并且，该比例因子可使具有固定大小的任何 UI 元素在各种尺寸的屏幕上都能向用户显示一些大小恒定的触摸（和阅读）目标。 通过与动态布局结合使用，你的 UI 不仅仅在不同设备上进行视觉上的缩放。 它还会执行任何必要的操作来将相应的内容量纳入到可用空间中。

这样，应用便可在所有屏幕上提供最佳体验。我们建议你针对各种屏幕大小创建每个位图资源，其中每个资源均适用于特定的比例因子。 在大多数情况下，提供 100% 缩放、200% 缩放和 400% 缩放的资源（按优先级顺序）能在采用所有中间比例系数时均可提供极佳效果。

**请注意**  如果无论什么原因，你不能在多个大小中创建资产然后创建 100%比例的资产。 在 Microsoft Visual Studio 中，UWP 应用的默认项目模板仅使用一个大小提供品牌标识资源（磁贴图像和徽标），但这些资源并非 100% 缩放。 为自己的应用编写资源时，请按照本部分中的指南进行编写、提供 100%、200% 和 400% 尺寸，并使用资源包。

如果具有繁复的图案，则可能希望在更多尺寸中提供资源。 如果要从矢量图像开始，则生成采用任意比例系数的高质量资源相对容易。

我们不建议您尝试支持所有的缩放比例，但 Windows 10 应用的缩放比例的完整列表是 100%、 125%、 150%、 200%、 250%，300%到 400%。 如果你支持这些比例系数，应用商店将针对每台设备选取大小适合的资源，然后将仅下载这些资源。 应用商店将根据设备的 DPI 选择要下载的资产。 从 Windows 运行时 8.x 应用在例如 140%和 220%缩放比例可以重复使用的资产，但您的应用程序将运行一个新的缩放比例，因此某些位图缩放将不可避免的。 在各种设备上测试你的应用，以查看你是否满意相应的结果。

您可能正在重新使用 XAML 标记从 Windows 运行时 8.x 应用中的标记 （可能是到大小形状或其他元素，也许是出于版式） 使用的文本的维度值。 但在某些情况下，较大的缩放比例是在设备上使用比为 Windows 10 应用为通用 8.1 应用 （例如，150%已使用其中 140%时，以及其中 180%已使用 200%）。 因此，如果您发现这些文字值现在是 Windows 10 上太大，则尝试乘以 0.8。 有关详细信息，请参阅[适用于 UWP 应用的响应式设计基础知识](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design)。

## <a name="gridview-and-listview-changes"></a>GridView 和 ListView 更改

已对 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 默认样式资源库进行多个更改，以使控件垂直滚动（而不是像之前默认的那样水平滚动）。 如果你编辑过你的项目中的默认样式副本，则副本将不会具有这些更改，因此你将需要手动进行更改。 下面是这些更改的列表。

-   [  **ScrollViewer.HorizontalScrollBarVisibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility) 的资源库已从 **Auto** 更改为 **Disabled**。
-   [  **ScrollViewer.VerticalScrollBarVisibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility) 的资源库已从 **Disabled** 更改为 **Auto**。
-   [  **ScrollViewer.HorizontalScrollMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode) 的资源库已从 **Enabled** 更改为 **Disabled**。
-   [  **ScrollViewer.VerticalScrollMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode) 的资源库已从 **Disabled** 更改为 **Enabled**。
-   在 [**ItemsPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) 的资源库中，[**ItemsWrapGrid.Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.orientation) 的值已从 **Vertical** 更改为 **Horizontal**。

如果上一个更改（对 **Orientation** 的更改）看起来矛盾，请记住我们讨论的是包围式网格。 水平方向的包围式网格（新值）与文本水平流动的写入系统类似，并且在页面末尾中断到下一行。 这样的文本页面会垂直滚动。 相反，垂直方向的包围式网格（上一个值）与文本垂直流动的写入系统类似，因而水平滚动。

以下是的方面[ **GridView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)并[ **ListView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)的已更改或不支持在 Windows 10 中。

-   [ **IsSwipeEnabled** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.isswipeenabled)属性 （仅限 Windows 运行时 8.x 应用） 的 Windows 10 应用不支持。 API 仍存在，但设置它不起任何作用。 以前的所有选择手势都受支持，向下轻扫（它不受支持是因为数据显示其不容易被发现）和右键单击（为显示上下文菜单而保留）除外。
-   [ **ReorderMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.reordermode)属性 （仅限 Windows Phone 应用商店应用） 的 Windows 10 应用不支持。 API 仍存在，但设置它不起任何作用。 请改为将你的 **GridView** 或 **ListView** 的 [**AllowDrop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.allowdrop) 和 [**CanReorderItems**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.canreorderitems) 设置为 true，以便用户能够使用长按（或单击并拖动）手势重新排序。
-   为 Windows 10 开发时，使用[ **ListViewItemPresenter** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ListViewItemPresenter)而不是[ **GridViewItemPresenter** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.GridViewItemPresenter)项容器中设置样式，均适用[ **ListView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)对于[ **GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)。 如果你编辑了默认项容器样式的副本，你将获得正确的类型。
-   Windows 10 应用的情况下，选择视觉对象已更改。 如果你将 [**SelectionMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectionmode) 设置为 **Multiple**，则在默认情况下，将为每个项都呈现一个复选框。 **ListView** 项的默认设置意味着复选框在项旁边以内联方式布局，因此，该项的其余部分所占用的空间将稍微减少并进行移动。 对于 **GridView** 项，复选框默认叠加在该项上方。 但是，在任何一种情况下，你都可以通过项容器样式内的 [**ListViewItemPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter) 元素，控制复选框的布局方式（内联或叠加，通过 [**CheckMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkmode) 属性控制）以及是否完整显示它们（通过 [**SelectionCheckMarkVisualEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled) 属性），如以下示例所示。
-   在 Windows 10 中， [ **ContainerContentChanging** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging)期间 UI 虚拟化项每两次引发事件： 一次针对回收，一次以便重复使用。 如果 [**InRecycleQueue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.containercontentchangingeventargs.inrecyclequeue) 的值是 **true**，并且没有特定回收工作要执行，可立即退出事件处理程序，并可确保在重复使用该相同项时（此时 **InRecycleQueue** 将会是 **false**），将重新进入事件处理程序。

```xml
<Style x:Key="CustomItemContainerStyle" TargetType="ListViewItem|GridViewItem">
    ...
    <Setter.Value>
        <ControlTemplate TargetType="ListViewItem|GridViewItem">
            <ListViewItemPresenter CheckMode="Inline|Overlay" ... />
        </ControlTemplate>
    </Setter.Value>
    ...
</Style>
```

![带有内联复选框的 ListViewItemPresenter](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-inline.jpg)

带有内联复选框的 ListViewItemPresenter

![带有叠加复选框的 ListViewItemPresenter](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-overlay.jpg)

带有叠加复选框的 ListViewItemPresenter

-   在删除用于选择的向下轻扫和右键单击手势时（由于上述原因），交互模型已更改，其结果之一是，[**ItemClick**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.itemclick) 和 [**SelectionChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) 事件将不再互相排斥。 为 Windows 10 应用，查看你的方案并决定是否采用"选择"调用"交互模型。 有关详细信息，请参阅[如何更改交互模式](https://docs.microsoft.com/previous-versions/windows/apps/hh780625(v=win.10))。
-   用于设置 [**ListViewItemPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter) 样式的属性进行了一些更改。 新属性包括：[**CheckBoxBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkboxbrush)、[**PressedBackground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.pressedbackground)、[**SelectedPressedBackground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedpressedbackground) 和 [**FocusSecondaryBorderBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.focussecondaryborderbrush)。 对于 Windows 10 应用，将忽略的属性是[ **Padding** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.padding) (使用[ **ContentMargin** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.contentmargin)相反)， [ **CheckHintBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkhintbrush)， [ **CheckSelectingBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkselectingbrush)， [ **PointerOverBackgroundMargin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.pointeroverbackgroundmargin)， [ **ReorderHintOffset**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.reorderhintoffset)， [ **SelectedBorderThickness**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedborderthickness)，和[ **SelectedPointerOverBorderBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedpointeroverborderbrush)。

下表描述了对 [**ListViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem) 和 [**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem) 控件模板中的视觉状态和视觉状态组的更改。

| 8.1                 |                         | Windows 10        |                     |
|---------------------|-------------------------|-------------------|---------------------|
| CommonStates        |                         | CommonStates      |                     |
|                     | 正常                  |                   | 正常              |
|                     | PointerOver             |                   | PointerOver         |
|                     | Pressed                 |                   | Pressed             |
|                     | PointerOverPressed      |                   | [不可用]       |
|                     | Disabled                |                   | [不可用]       |
|                     | [不可用]           |                   | PointerOverSelected |
|                     | [不可用]           |                   | Selected            |
|                     | [不可用]           |                   | PressedSelected     |
| [不可用]       |                         | DisabledStates    |                     |
|                     | [不可用]           |                   | Disabled            |
|                     | [不可用]           |                   | Enabled             |
| SelectionHintStates |                         | [不可用]     |                     |
|                     | VerticalSelectionHint   |                   | [不可用]       |
|                     | HorizontalSelectionHint |                   | [不可用]       |
|                     | NoSelectionHint         |                   | [不可用]       |
| [不可用]       |                         | MultiSelectStates |                     |
|                     | [不可用]           |                   | MultiSelectDisabled |
|                     | [不可用]           |                   | MultiSelectEnabled  |
| SelectionStates     |                         | [不可用]     |                     |
|                     | Unselecting             |                   | [不可用]       |
|                     | Unselected              |                   | [不可用]       |
|                     | UnselectedPointerOver   |                   | [不可用]       |
|                     | UnselectedSwiping       |                   | [不可用]       |
|                     | 选择               |                   | [不可用]       |
|                     | Selected                |                   | [不可用]       |
|                     | SelectedSwiping         |                   | [不可用]       |
|                     | SelectedUnfocused       |                   | [不可用]       |

如果你有一个自定义 [**ListViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem) 或 [**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem) 控件模板，请按照上述更改查看它。 我们建议你通过编辑新默认模板的副本并向其重新应用你的自定义来重新开始。 如果出于任何原因而无法执行此操作，并且你需要编辑现有模板，下面是有关可以如何执行此操作的一些常规指南。

-   添加新的 MultiSelectStates 视觉状态组。
-   添加新的 MultiSelectDisabled 视觉状态。
-   添加新的 MultiSelectEnabled 视觉状态。
-   添加新的 DisabledStates 视觉状态组。
-   添加新的 Enabled 视觉状态。
-   在 CommonStates 视觉状态组中，删除 PointerOverPressed 视觉状态。
-   将 Disabled 视觉状态移动到 DisabledStates 视觉状态组中。
-   添加新的 PointerOverSelected 视觉状态。
-   添加新的 PressedSelected 视觉状态。
-   删除 SelectedHintStates 视觉状态组。
-   在 SelectionStates 视觉状态组中，将 Selected 视觉状态移动到 CommonStates 视觉状态组中。
-   删除整个 SelectionStates 视觉状态组。

## <a name="localization-and-globalization"></a>本地化和全球化

在 UWP App 项目中，你可以重新使用通用 8.1 项目中的 Resources.resw 文件。 复制完该文件后，将其添加到项目，然后将 **“生成操作”** 设置为**PRIResource**，并将 **“复制到输出目录”** 设置为 **“不复制”** 。 [  **ResourceContext.QualifierValues**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues) 主题介绍了如何基于设备系列资源选择规格加载特定于设备系列的资源。

## <a name="play-to"></a>播放到

中的 Api [ **Windows.Media.PlayTo** ](https://docs.microsoft.com/uwp/api/Windows.Media.PlayTo)命名空间不推荐使用的 Windows 10 应用[ **Windows.Media.Casting** ](https://docs.microsoft.com/uwp/api/Windows.Media.Casting)Api。

## <a name="resource-keys-and-textblock-style-sizes"></a>资源键和 TextBlock 样式大小

设计语言演变成适用于 Windows 10 以及因此更改某些系统样式。 在某些情况下，你需要重新访问视图的视觉设计，以查看它们是否能与已更改的样式属性协调运行。

在其他情况下，资源键将不再受支持。 Visual Studio 中的 XAML 标记编辑器突出显示对无法解析的资源键的引用。 例如，XAML 标记编辑器将使用红色波形曲线为对样式键 `ListViewItemTextBlockStyle` 的引用加下划线。 如果未更正该错误，则应用将在你尝试将其部署到模拟器或设备时立即终止。 因此，请务必留意 XAML 标记的正确性。 而且你将发现 Visual Studio 是捕获此类问题的绝佳工具。

对于仍受支持的键，设计语言的更改意味着由某些样式设置的属性已更改。 例如，`TitleTextBlockStyle`设置**FontSize**到在 Windows 运行时 8.x 应用和 Windows Phone 应用商店应用中的 18.14px 14.667px。 但是，相同的样式集**FontSize**到更大 24px 在 Windows 10 应用中。 查看你的设计和布局，并在合适的位置上使用适当的样式。 有关详细信息，请参阅[字体指南](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts)和[设计 UWP 应用](https://developer.microsoft.com/en-us/windows/apps/design)。

下面是不再受支持的键的完整列表。

-   CheckBoxAndRadioButtonMinWidthSize
-   CheckBoxAndRadioButtonTextPaddingThickness
-   ComboBoxFlyoutListPlaceholderTextOpacity
-   ComboBoxFlyoutListPlaceholderTextThemeMargin
-   ComboBoxHighlightedBackgroundThemeBrush
-   ComboBoxHighlightedBorderThemeBrush
-   ComboBoxHighlightedForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextThemeFontWeight
-   ComboBoxItemDisabledThemeOpacity
-   ComboBoxItemHighContrastBackgroundThemeMargin
-   ComboBoxItemMinHeightThemeSize
-   ComboBoxPlaceholderTextBlockStyle
-   ComboBoxPlaceholderTextThemeMargin
-   CommandBarBackgroundThemeBrush
-   CommandBarForegroundThemeBrush
-   ContentDialogButton1HostPadding
-   ContentDialogButton2HostPadding
-   ContentDialogButtonsMinHeight
-   ContentDialogContentLandscapeWidth
-   ContentDialogContentMinHeight
-   ContentDialogDimmingColor
-   ContentDialogTitleMinHeight
-   ControlContextualInfoTextBlockStyle
-   ControlHeaderContentPresenterStyle
-   ControlHeaderTextBlockStyle
-   FlyoutContentPanelLandscapeThemeMargin
-   FlyoutContentPanelPortraitThemeMargin
-   GrabberMargin
-   GridViewItemMargin
-   GridViewItemPlaceholderBackgroundThemeBrush
-   GroupHeaderTextBlockStyle
-   HeaderContentPresenterStyle
-   HighContrastBlack
-   HighContrastWhite
-   HubHeaderCharacterSpacing
-   HubHeaderFontSize
-   HubHeaderMarginThickness
-   HubSectionHeaderCharacterSpacing
-   HubSectionHeaderFontSize
-   HubSectionHeaderMarginThickness
-   HubSectionMarginThickness
-   InlineWindowPlayPauseMargin
-   ItemTemplate
-   LeftFullWindowMargin
-   LeftMargin
-   ListViewEmptyStaticTextBlockStyle
-   ListViewItemContentTextBlockStyle
-   ListViewItemContentTranslateX
-   ListViewItemMargin
-   ListViewItemMultiselectCheckBoxMargin
-   ListViewItemSubheaderTextBlockStyle
-   ListViewItemTextBlockStyle
-   MediaControlPanelAudioThemeBrush
-   MediaControlPanelPhoneVideoThemeBrush
-   MediaControlPanelVideoThemeBrush
-   MediaControlPanelVideoThemeColor
-   MediaControlPlayPauseThemeBrush
-   MediaControlTimeRowThemeBrush
-   MediaControlTimeRowThemeColor
-   MediaDownloadProgressIndicatorThemeBrush
-   MediaErrorBackgroundThemeBrush
-   MediaTextThemeBrush
-   MenuFlyoutBackgroundThemeBrush
-   MenuFlyoutBorderThemeBrush
-   MenuFlyoutLandscapeThemePadding
-   MenuFlyoutLeftLandscapeBorderThemeThickness
-   MenuFlyoutPortraitBorderThemeThickness
-   MenuFlyoutPortraitThemePadding
-   MenuFlyoutRightLandscapeBorderThemeThickness
-   MessageDialogContentStyle
-   MessageDialogTitleStyle
-   MinimalWindowMargin
-   PasswordBoxCheckBoxThemeMargin
-   PhoneAccentBrush
-   PhoneBackgroundBrush
-   PhoneBackgroundColor
-   PhoneBaseBlackColor
-   PhoneBaseHighColor
-   PhoneBaseLowColor
-   PhoneBaseLowSolidColor
-   PhoneBaseMediumHighColor
-   PhoneBaseMediumMidColor
-   PhoneBaseMediumMidSolidColor
-   PhoneBaseMidColor
-   PhoneBaseWhiteColor
-   PhoneBorderThickness
-   PhoneButtonBasePressedForegroundBrush
-   PhoneButtonContentPadding
-   PhoneButtonFontWeight
-   PhoneButtonMinHeight
-   PhoneButtonMinWidth
-   PhoneChromeBrush
-   PhoneChromeColor
-   PhoneControlBackgroundColor
-   PhoneControlDisabledColor
-   PhoneControlForegroundColor
-   PhoneDisabledBrush
-   PhoneDisabledColor
-   PhoneFontFamilyLight
-   PhoneFontFamilySemiBold
-   PhoneForegroundBrush
-   PhoneForegroundColor
-   PhoneHighContrastSelectedBackgroundThemeBrush
-   PhoneHighContrastSelectedForegroundThemeBrush
-   PhoneImagePlaceholderColor
-   PhoneLowBrush
-   PhoneMidBrush
-   PhonePageBackgroundColor
-   PhonePivotLockedTranslation
-   PhonePivotUnselectedItemOpacity
-   PhoneRadioCheckBoxBorderBrush
-   PhoneRadioCheckBoxBrush
-   PhoneRadioCheckBoxCheckBrush
-   PhoneRadioCheckBoxPressedBrush
-   PhoneStrokeThickness
-   PhoneTextHighColor
-   PhoneTextLowColor
-   PhoneTextMidColor
-   PhoneTextOverAccentColor
-   PhoneTouchTargetLargeOverhang
-   PhoneTouchTargetOverhang
-   PivotHeaderItemPadding
-   PlaceholderContentPresenterStyle
-   ProgressBarHighContrastAccentBarThemeBrush
-   ProgressBarIndeterminateRectagleThemeSize
-   ProgressBarRectangleStyle
-   ProgressRingActiveBackgroundOpacity
-   ProgressRingElipseThemeMargin
-   ProgressRingElipseThemeSize
-   ProgressRingTextForegroundThemeBrush
-   ProgressRingTextThemeMargin
-   ProgressRingThemeSize
-   RichEditBoxTextThemeMargin
-   RightFullWindowMargin
-   RightMargin
-   ScrollBarMinThemeHeight
-   ScrollBarMinThemeWidth
-   ScrollBarPanningThumbThemeHeight
-   ScrollBarPanningThumbThemeWidth
-   SliderThumbDisabledBorderThemeBrush
-   SliderTrackBorderThemeBrush
-   SliderTrackDisabledBorderThemeBrush
-   TextBoxBackgroundColor
-   TextBoxBorderColor
-   TextBoxDisabledHeaderForegroundThemeBrush
-   TextBoxFocusedBackgroundThemeBrush
-   TextBoxForegroundColor
-   TextBoxPlaceholderColor
-   TextControlHeaderMarginThemeThickness
-   TextControlHeaderMinHeightSize
-   TextStyleExtraExtraLargeFontSize
-   TextStyleExtraLargePlusFontSize
-   TextStyleMediumFontSize
-   TextStyleSmallFontSize
-   TimeRemainingElementMargin

## <a name="searchbox-deprecated-in-favor-of-autosuggestbox"></a>弃用搜索框以支持 AutoSuggestBox

尽管已在通用设备系列中实现了 [**SearchBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.searchbox)，但它无法在移动设备上正常运行。 将 [**AutoSuggestBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox) 用于通用搜索体验。 下面介绍通常如何使用 **AutoSuggestBox** 实现搜索体验。

用户开始键入后，将引发 **TextChanged** 事件，描述为 **UserInput**。 然后填充建议列表，并设置 [**AutoSuggestBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox) 的 **ItemsSource**。 在用户导航列表时，将引发 **SuggestionChosen** 事件（并且若已设置 **TextMemberDisplayPath**，文本框将自动填充指定的属性）。 当用户使用 Enter 键提交选择时，将引发 **QuerySubmitted** 事件，此时可对该建议执行相应操作（在此情况下，最有可能是导航至具有有关指定内容的更多详细信息的另一个页面）。 请注意，**SearchBoxQuerySubmittedEventArgs** 的 **LinguisticDetails** 和 **Language** 属性不再受支持（有支持该功能的等效 API）。 并且 **KeyModifiers** 也不再受支持。

[**AutoSuggestBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox)还具有对输入的法编辑器 (Ime) 的支持。 如果希望显示“查找”图标，也可以那样做（与图标交互将引发 **QuerySubmitted** 事件）。

```xml
   <AutoSuggestBox ... >
        <AutoSuggestBox.QueryIcon>
            <SymbolIcon Symbol="Find"/>
        </AutoSuggestBox.QueryIcon>
    </AutoSuggestBox>
```

另请参阅 [AutoSuggestBox 移植示例](https://go.microsoft.com/fwlink/p/?linkid=619996)。

## <a name="semanticzoom-changes"></a>SemanticZoom 更改

[  **SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 的缩小手势已在 Windows Phone 模型上进行了融合，该手势是点击或单击组标题（因此，在桌面计算机上，不再显示提供的用于缩小的减号按钮）。 现在，我们可以随意在所有设备上获取相同且一致的行为。 与 Windows Phone 模型相比的一个外观区别是缩小视图（跳转列表）替换放大视图，而不是覆盖它。 出于此原因，你可以从缩小视图中删除任何半透明背景。

在 Windows Phone 应用商店应用中，缩小的视图将扩展到屏幕的大小。 在 Windows 运行时 8.x 应用中，并在 Windows 10 应用中，缩小的视图的大小限制为的边界**SemanticZoom**控件。

在 Windows Phone 应用商店应用中，如果缩小视图的背景中有任何透明度，则缩小视图后面的内容将（采用 z 顺序）显示出来。 在 Windows 运行时 8.x 应用中，并在 Windows 10 应用中，没有可见后缩小的视图。

在 Windows 运行时 8.x 应用中，当应用已停用并重新激活，缩小的视图被关闭 （如果该服务器已显示），并改为显示放大的视图。 在 Windows Phone 应用商店应用中，并在 Windows 10 应用中，如果它当前显示仍显示缩小的视图。

在 Windows Phone 应用商店应用中，并在 Windows 10 应用中，按下后退按钮时，将消除缩小的视图。 对于 Windows 运行时 8.x 应用，所以不存在内置后退按钮处理，因此，问题不会应用。

## <a name="settings"></a>设置

Windows 运行时 8.x **SettingsPane**类不是适用于 Windows 10。 除了生成“设置”页面，还应为用户提供从应用内访问它的方式。 我们建议你在最高级别公开此应用“设置”页面来作为导航窗格上的最后一个固定项，但下面依然提供完整的选项集。

-   导航窗格。 “设置”应为选项的导航列表中最后一个项，并固定到底部。
-   应用栏/工具栏（在选项卡视图或透视布局内）。 “设置”应为应用栏或工具栏菜单浮出控件中的最后一个项。 不建议将“设置”作为导航内的顶级项之一。
-   中心。 “设置”应位于菜单浮出控件内部（可以在应用栏菜单或中心布局的工具栏菜单内）

同样不建议将“设置”隐藏在大纲细节窗格内。

“设置”页面应填充整个应用窗口，并且“设置”页面也是“关于”和“反馈”所在的位置。 有关“设置”页面的设计指南，请参阅[应用设置指南](https://docs.microsoft.com/windows/uwp/app-settings/guidelines-for-app-settings)。

## <a name="text"></a>Text

文本（或版式）是 UWP 应用的重要方面，并且在移植时，你可能希望回顾你的视图的视觉设计，以便它们与新设计语言相协调。 使用这些图示查找可用的通用 Windows 平台 (UWP) **TextBlock** 系统样式。 找到对应于你所用的 Windows Phone Silverlight 样式。 或者，可以创建您自己的通用样式，并将从 Windows Phone Silverlight 系统样式属性复制到那些。

![适用于 Windows 10 应用的 TextBlock 系统样式](images/label-uwp10stylegallery.png) <br/>Windows 10 应用的系统 TextBlock 样式

在 Windows 运行时 8.x 应用和 Windows Phone 应用商店应用程序中，默认字体系列是全局用户界面。 在 Windows 10 应用中，默认字体系列是 Segoe UI。 因此，你的应用中的字体指标可能看起来不同。 如果你希望重新生成 8.1 文本的外观，可以使用 [**LineHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.lineheight) 和 [**LineStackingStrategy**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.linestackingstrategy) 等属性来设置你自己的指标。

在 Windows 运行时 8.x 应用和 Windows Phone 应用商店应用程序中，文本的默认语言设置为生成的语言或为 en-我们。 在 Windows 10 应用中，默认语言设置为最匹配的应用语言 （字体回退）。 你可以显式设置 [**FrameworkElement.Language**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.language)，不过如果你未设置该属性的值，你将能够体验到更好的字体回退行为。

有关详细信息，请参阅[字体指南](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts)和[设计 UWP 应用](https://go.microsoft.com/fwlink/p/?LinkID=533896)。 有关对文本控件更改的信息，另请参阅上面的[控件](#controls-and-control-styles-and-templates)部分。

## <a name="theme-changes"></a>主题更改

对于通用 8.1 应用，默认主题为深色。 适用于 Windows 10 设备，默认主题已发生更改，但您可以控制使用声明在 App.xaml 中的请求的主题的主题。 例如，若要在所有设备上都使用深色主题，请将 `RequestedTheme="Dark"` 添加到根 Application 元素。

## <a name="tiles-and-toasts"></a>磁贴和 Toast

磁贴和 toast，当前正在使用的模板将继续在 Windows 10 应用中工作。 但有新的自适应模板可供你使用，它们在[通知、磁贴、Toast 和锁屏提醒](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-badges-notifications)中有相关说明。

以前在台式机上，Toast 通知是暂时的消息。 一旦丢失或被忽略，它将消失且无法再检索。 在 Windows Phone 上，如果 Toast 通知被忽略或暂时消除，它将转到操作中心。 现在，操作中心不再局限于移动设备系列。

若要发送 Toast 通知，不再需要声明一个功能。

## <a name="window-size"></a>窗口大小

对于 Universal 8.1 应用，将使用 [**ApplicationView**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema2013/element-applicationview) 应用清单元素声明最小窗口宽度。 在 UWP 应用中，你可以使用命令式代码指定最小大小（宽度以及高度）。 默认的最小大小为 500x320 epx，这也是可接受的最小大小的最小值。 可接受的最小大小的最大值为 500x500epx。

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

下一主题是[针对 I/O、设备和应用模型进行移植](w8x-to-uwp-input-and-sensors.md)。

