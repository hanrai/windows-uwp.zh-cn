---
ms.assetid: 333f67f5-f012-4981-917f-c6fd271267c6
description: 此案例研究（基于 Bookstore1 中提供的信息生成）首先研究通用 8.1 应用，该应用可在 SemanticZoom 控件中显示分组数据。
title: Windows 运行时 8.x 到 UWP 案例研究：Bookstore2
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 14504626e433ed18ed6c7425a94679a64c9502ee
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372947"
---
# <a name="windows-runtime-8x-to-uwp-case-study-bookstore2"></a>Windows 运行时 8.x 到 UWP 案例研究：Bookstore2


此案例研究（基于 [Bookstore1](w8x-to-uwp-case-study-bookstore1.md) 中提供的信息生成）首先研究通用 8.1 应用，该应用可在 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 控件中显示分组数据。 在视图模型中，类 **Author** 的每个实例都表示一组由该作者创作的书籍，而在 **SemanticZoom** 中，我们可以按作者查看分组书籍的列表，或者可以缩小到可以看到包含作者的跳转列表。 与在书籍列表中上下滚动相比，跳转列表提供了更快速的浏览方式。 我们介绍将应用移植到 Windows 10 通用 Windows 平台 (UWP) 应用的步骤。

**请注意**  时打开 Bookstore2Universal\_10 在 Visual Studio 中，如果看到消息"需要 Visual Studio 更新"，然后按照中的步骤[TargetPlatformVersion](w8x-to-uwp-troubleshooting.md)。

## <a name="downloads"></a>下载

[下载 Bookstore2\_81 通用 8.1 应用](https://go.microsoft.com/fwlink/?linkid=532951)。

[下载 Bookstore2Universal\_10 个 Windows 10 应用](https://go.microsoft.com/fwlink/?linkid=532952)。

## <a name="the-universal-81-app"></a>通用 8.1 应用

还有哪些 Bookstore2\_81-我们要到端口的应用，如下所示。 它是水平滚动（在 Windows Phone 上垂直滚动）的 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom)，按作者分组显示书籍。 你可以缩小到跳转列表，并且可以从该列表导航回任一组。 此应用包含两个主要部分：提供分组数据源的视图模型，以及绑定到该视图模型的用户界面。 我们将看到，这两个部分从 WinRT 8.1 技术轻松地移植到 Windows 10。

![bookstore2\-放大的视图在 windows 上的 81](images/w8x-to-uwp-case-studies/c02-01-win81-zi-how-the-app-looks.png)

Bookstore2\_81 上 Windows，放大的视图
 

![bookstore2\-缩小的视图在 windows 上的 81](images/w8x-to-uwp-case-studies/c02-02-win81-zo-how-the-app-looks.png)

Bookstore2\_81 上 Windows，缩小的视图

![bookstore2\-81 在 windows phone、 放大的视图](images/w8x-to-uwp-case-studies/c02-03-wp81-zi-how-the-app-looks.png)

Bookstore2\_81 上 Windows Phone，放大的视图

![bookstore2\-81 在 windows phone、 缩小的视图](images/w8x-to-uwp-case-studies/c02-04-wp81-zo-how-the-app-looks.png)

Bookstore2\_81 上 Windows Phone，缩小的视图

##  <a name="porting-to-a-windows10-project"></a>移植到 Windows 10 项目

Bookstore2\_81 解决方案是 8.1 通用应用项目。 Bookstore2\_81.Windows 项目生成的应用程序包的 Windows 8.1 和 Bookstore2\_81.WindowsPhone 项目生成适用于 Windows Phone 8.1 应用包。 Bookstore2\_81.共享是包含源代码、 标记文件和其他资产和资源，供这两个其他两个项目的项目。

就像上一个案例研究，选项我们将采用 (的中所述的那些[如果您有一个通用的 8.1 应用程序](w8x-to-uwp-root.md)) 是要移植到 Windows 10 的通用设备系列为目标的共享项目的内容。

首先创建新的空白应用程序（Windows 通用）项目。 其命名为 Bookstore2Universal\_10。 这些是要通过从 Bookstore2 复制的文件\_81 到 Bookstore2Universal\_10。

**从共享项目**

-   将复制包含书籍封面图像 PNG 文件的文件夹 (文件夹是\\资产\\CoverImages)。 复制该文件夹后，在 **“解决方案资源管理器”** 中，请确保将 **“显示所有文件”** 切换为打开。 右键单击你复制的文件夹，然后单击**包括在项目中**。 该命令的意思是将文件或文件夹“包括”在某个项目中。 每次你复制文件或文件夹、每个副本时，请在**解决方案资源管理器**中单击**刷新**，然后将文件或文件夹包括在项目中。 无需为你将在目标位置替换的文件执行此操作。
-   将复制包含视图模型的源文件的文件夹 (文件夹是\\ViewModel)。
-   复制 MainPage.xaml 并替换目标位置中的文件。

**从 Windows 项目**

-   复制 BookstoreStyles.xaml。 我们将使用此一个良好起点，因为此文件中的所有资源密钥将都解决在 Windows 10 应用程序中;等效 WindowsPhone 文件中的其中一些则不会。
-   复制 SeZoUC.xaml 和 SeZoUC.xaml.cs。 我们将从此视图的 Windows 版本开始（此版本适用于宽窗口），然后我们将使其适应较小的窗口，从而适应较小的设备。

编辑你刚才复制的源代码和标记文件，更改任何引用到 Bookstore2\_81 命名空间到 Bookstore2Universal\_10。 执行此操作的快速方法是使用“在文件中替换”功能。  视图模型中和任何其他强制性代码中都不需要更改任何代码。 不过，只是为了更加轻松地了解哪个版本的应用程序是否正在运行，更改返回的值**Bookstore2Universal\_10.BookstoreViewModel.AppName**属性从"Bookstore2\_81"到"BOOKSTORE2UNIVERSAL\_10"。

现在，你可以执行生成和运行操作。 下面是如何完成后没有要尚未移植到 Windows 10 后看起来我们新的 UWP 应用。

![在桌面设备上运行的初始源代码发生更改的 Windows 10 应用，放大视图](images/w8x-to-uwp-case-studies/c02-05-desk10-zi-initial-source-code-changes.png)

在桌面设备，放大的视图上运行的初始源的代码更改对 Windows 10 应用

![在桌面设备上运行的初始源代码发生更改的 Windows 10 应用，缩小视图](images/w8x-to-uwp-case-studies/c02-06-desk10-zo-initial-source-code-changes.png)

在桌面设备，缩小的视图上运行的初始源的代码更改对 Windows 10 应用

视图模型与放大和缩小视图正确协作，尽管存在一些问题使其难以体现出来。 一个问题是，[**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 无法滚动。 这是因为，在 Windows 10 中的默认样式[ **GridView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)会导致它要垂直布局 （和 Windows 10 设计指南建议我们使用它通过这种方式中新的和已移植的应用程序中）。 但是，水平滚动我们从 Bookstore2 复制自定义项面板模板中的设置\_81 项目 (它适用 8.1 的应用) 与在 Windows 10 默认样式中的垂直滚动设置冲突由于我们应用具有移植到 Windows 10 应用。 第二个问题是，该应用尚未调整其用户界面以在不同大小的窗口和小型设备中提供最佳体验。 第三，尚未使用正确的样式和画笔，从而导致许多文本不可见（包括你可以通过单击缩小的组标题）。 因此在接下来的三个部分（[SemanticZoom 和 GridView 设计更改](#semanticzoom-and-gridview-design-changes)、[自适应 UI](#adaptive-ui) 和[通用样式](#universal-styling)）中，我们将修复这三个问题。

## <a name="semanticzoom-and-gridview-design-changes"></a>SemanticZoom 和 GridView 设计更改

Windows 10 中的设计更改[ **SemanticZoom** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom)控制部分所述[SemanticZoom 更改](w8x-to-uwp-porting-xaml-and-ui.md)。 在此部分中，我们无需为响应这些更改而进行任何工作。

[GridView/ListView 设计更改](w8x-to-uwp-porting-xaml-and-ui.md)部分中描述了对 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 的更改。 为了适应这些更改，我们需要进行一些非常微小的调整，如下所述。

-   在 SeZoUC.xaml 中，在 `ZoomedInItemsPanelTemplate` 中设置 `Orientation="Horizontal"` 和 `GroupPadding="0,0,0,20"`。
-   在 SeZoUC.xaml 中，从缩小视图中删除 `ZoomedOutItemsPanelTemplate` 并移除 `ItemsPanel` 属性。

就这么简单！

## <a name="adaptive-ui"></a>自适应 UI

进行该更改后，SeZoUC.xaml 向我们提供的 UI 布局最适用于应用在宽窗口（只可能出现在带有大屏幕的设备上）中运行的情况。 但是，当应用的窗口较窄时（会出现在小型设备上，但也可能出现在大型设备上），Windows Phone 应用商店应用中所具有的 UI 可以说是最合适的。

我们可以使用自适应视觉状态管理器功能来实现此目的。 我们将在视觉元素上设置属性，以便默认使用我们在 Windows Phone 应用商店应用中所使用的较小模板，将 UI 的布局设置为较窄的状态。 然后，我们将检测到应用窗口大于或等于特定大小（以[有效像素](w8x-to-uwp-porting-xaml-and-ui.md)为测量单位）的情况，并更改视觉元素的属性作为回应，以获取更大且更宽的布局。 我们将这些属性更改置于视觉状态中，并且将使用自适应触发器持续监视并确定是否要应用该视觉状态，具体取决于窗口的宽度（以有效像素为单位）。 在此情况下，我们既可以针对窗口宽度进行触发，也可以针对窗口高度进行触发。

最小窗口宽度 548 epx 适用于此用例，因为这是我们希望在其上显示宽布局的最小设备大小。 手机通常小于 548 epx，因此在诸如此类的小型设备上，我们将保留默认的较窄布局。 在电脑上，默认情况下窗口将在足够宽的状态下启动，以触发向较宽状态的切换。 你将能够从此处将窗口拖动到最窄宽度以显示两列 250x250 大小的项。 如果比这更窄一些，则触发器将停用、宽视觉状态将被删除，并且默认的窄布局将生效。

因此，为了实现这两个不同的布局，我们需要设置（和更改）哪些属性？ 有两个替代项，每一项都需要不同的方法。

1.  我们可以在标记中放置两个 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 控件。 一是我们在 Windows 运行时 8.x 应用中使用的标记的副本 (使用[ **GridView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)它所包含控件)，并默认处于折叠状态。 另一个将是我们在 Windows Phone 应用商店应用中所使用的标记的副本（使用其内部的 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 控件），并且在默认情况下处于可见状态。 视觉状态将切换两个 **SemanticZoom** 控件的可见性属性。 这只需非常少的工作即可实现，但一般情况下不是一个高性能技术。 因此，如果你要使用它，你应该分析你的应用并确保它仍然满足你的性能目标。
2.  我们可以使用包含 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 控件的单个 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom)。 若要实现这两个布局，在宽视觉状态中，我们将更改 **ListView** 控件的属性（包括应用于它们的模板），以使它们采用与 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 相同的方式进行布局。 这可能会提高性能，但是在 **GridView** 和 **ListView** 的各种样式和模板之间以及它们的各种项类型之间有许多细小的差异，因此这是一个更难实现的解决方案。 此解决方案此时还与默认样式和模板的设计方式紧密耦合，因此该解决方案容易受到将来对默认值的任何更改的影响。

在此案例研究中，我们将选择第一个替代项。 但如果你愿意，你可以尝试第二个替代项然后看它是否更适合你。 下面是实现第一个替代项需要采取的步骤。

-   在新项目的标记中的 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 上，设置 `x:Name="wideSeZo"` 和 `Visibility="Collapsed"`。
-   返回到 Bookstore2\_81.WindowsPhone 项目，然后打开 SeZoUC.xaml。 从该文件复制 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 元素标记并将其粘贴在紧挨着新项目中的 `wideSeZo` 后的位置。 在刚粘贴的元素上设置 `x:Name="narrowSeZo"`。
-   但是 `narrowSeZo` 需要一些我们尚未复制的样式。 再次在 Bookstore2\_81.WindowsPhone、 复制这两个样式 (`AuthorGroupHeaderContainerStyle`和`ZoomedOutAuthorItemContainerStyle`) 的 SeZoUC.xaml 并将其粘贴到 BookstoreStyles.xaml 在新项目中。
-   现在在新的 SeZoUC.xaml 中有两个 [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) 元素。 将这两个元素包裹在 **Grid** 中。
-   在新项目的 BookstoreStyles.xaml 中，将单词 `Wide` 附加到以下三个资源键（并附加到它们在 SeZoUC.xaml 中的引用，但仅附加到 `wideSeZo` 内的引用）：`AuthorGroupHeaderTemplate`、`ZoomedOutAuthorTemplate` 和 `BookTemplate`。
-   在 Bookstore2\_81.WindowsPhone 项目中，打开 BookstoreStyles.xaml。 从该文件将复制 （以上所述），这些相同的三个资源和两个跳转列表项转换器和命名空间前缀声明 Windows\_UI\_Xaml\_控件\_基元，并将其所有粘贴到 BookstoreStyles.xaml 在新项目中。
-   最后，在新项目的 SeZoUC.xaml 中，将相应的视觉状态管理器标记添加到你在上面添加的 **Grid**。

```xml
    <Grid>
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState x:Name="WideState">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="548"/>
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Target="wideSeZo.Visibility" Value="Visible"/>
                        <Setter Target="narrowSeZo.Visibility" Value="Collapsed"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

    ...

    </Grid>
```

## <a name="universal-styling"></a>通用样式设置

现在，让我们来修复某些样式设置问题（包括上面我们在从旧项目中进行复制时介绍的问题）。

-   在 MainPage.xaml 中，将 `LayoutRoot` 的 Background 更改为 `"{ThemeResource ApplicationPageBackgroundThemeBrush}"`。
-   在 BookstoreStyles.xaml 中，将资源 `TitlePanelMargin` 的值设置为 `0`（或你认为合适的任意值）。
-   在 SeZoUC.xaml 中，将 `wideSeZo` 的 Margin 设置为 `0`（或你认为合适的任意值）。
-   在 BookstoreStyles.xaml 中，从 `AuthorGroupHeaderTemplateWide` 中删除 Margin 属性。
-   从 `AuthorGroupHeaderTemplate` 和 `ZoomedOutAuthorTemplate` 中删除 FontFamily 属性。
-   Bookstore2\_使用 81 `BookTemplateTitleTextBlockStyle`， `BookTemplateAuthorTextBlockStyle`，和`PageTitleTextBlockStyle`资源密钥作为间接寻址以使单个键的两个应用中有不同的实现。 我们不再需要该间接寻址；我们可以直接引用系统样式。 因此分别使用 `TitleTextBlockStyle`、`CaptionTextBlockStyle` 和 `HeaderTextBlockStyle` 在整个应用中替换这些引用。 你可以使用 Visual Studio的 **“在文件中替换”** 功能快速且准确地执行此操作。 然后，你可以删除这三个未使用的资源。
-   在 `AuthorGroupHeaderTemplate` 中，使用 `SystemControlBackgroundAccentBrush` 替换 `PhoneAccentBrush`，然后在 **TextBlock** 上设置 `Foreground="White"`，以便它在移动设备系列上运行时外观正确。
-   在 `BookTemplateWide` 中，将第二个 **TextBlock** 中的 Foreground 属性复制到第一个。
-   在 `ZoomedOutAuthorTemplateWide` 中，将对 `SubheaderTextBlockStyle` 的引用（现在有点过大）更改为对 `SubtitleTextBlockStyle` 的引用。
-   缩小视图（跳转列表）不再覆盖新平台中的放大视图，因此我们可以从 `narrowSeZo` 的缩小视图中删除 `Background` 属性。
-   因此，所有样式和模板位于一个文件中，将 `ZoomedInItemsPanelTemplate` 移出 SeZoUC.xaml 并移入 BookstoreStyles.xaml 中。

在样式设置操作的最后一步中，应用的外观如下所示。

![在桌面设备上运行的已移植的 Windows 10 应用，放大视图，两个窗口大小](images/w8x-to-uwp-case-studies/c02-07-desk10-zi-ported.png)

移植桌面设备、 放大的视图、 两个大小的窗口上运行的 Windows 10 应用

![在桌面设备上运行的已移植的 Windows 10 应用，缩小视图，两个窗口大小](images/w8x-to-uwp-case-studies/c02-08-desk10-zo-ported.png)

移植桌面设备、 缩小的视图、 两个大小的窗口上运行的 Windows 10 应用

![在移动设备上运行的已移植的 Windows 10 应用，放大视图](images/w8x-to-uwp-case-studies/c02-09-mob10-zi-ported.png)

移植移动设备，放大的视图上运行的 Windows 10 应用

![在移动设备上运行的已移植的 Windows 10 应用，缩小视图](images/w8x-to-uwp-case-studies/c02-10-mob10-zo-ported.png)

移植移动设备，缩小的视图上运行的 Windows 10 应用

## <a name="conclusion"></a>结束语

在此案例研究涉及了一个比上一个用户界面更为大胆的用户界面。 和上一个案例研究一样，此特定视图模型不需要进行任何工作，我们的主要工作集中在重构用户界面。 某些更改是将两个项目组合为一个项目同时仍然支持许多外形规格（事实上，比我们以前所能支持的多很多）的必然结果。 一些更改与对平台进行的更改有关。

下一个案例研究是 [QuizGame](w8x-to-uwp-case-study-quizgame.md)，我们将从中了解有关访问和显示分组数据的信息。
