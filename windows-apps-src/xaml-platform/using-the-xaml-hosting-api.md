---
author: mcleans
description: 本文介绍了如何在桌面应用程序中托管 UWP XAML 用户界面。
title: 使用 UWP XAML 中的桌面应用程序托管 API
ms.author: mcleans
ms.date: 09/21/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp, windows forms, wpf
keywords: windows 10，uwp，windows 窗体、 wpf win32
ms.localizationpriority: medium
ms.openlocfilehash: 536d2aa3eca88fb10561e1589ec6f5b5c556aa29
ms.sourcegitcommit: 194ab5aa395226580753869c6b66fce88be83522
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "4153467"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-desktop-application"></a>使用 UWP XAML 中的桌面应用程序托管 API

> [!NOTE]
> UWP XAML 托管 API 是为开发人员预览当前可用。 尽管我们鼓励你尝试在原型代码中的此 API 现在，我们不建议你使用它在生产代码中这一次。 此 API 将继续成熟并在将来稳定的 Windows 版本。 Microsoft 对于此处提供的信息不作任何明示或默示的担保。

从 Windows 10 Insider Preview SDK 开始构建 17709，非 UWP 桌面应用程序 （包括 WPF、 Windows 窗体和 C/c + + Win32 应用程序） 可以在任何与窗口句柄相关联的 UI 元素中使用*UWP XAML 托管 API*到托管 UWP 控件(HWND)。 此 API 允许非 UWP 桌面应用程序使用最新的 Windows 10 UI 功能仅可通过 UWP 控件提供的。 例如，非 UWP 桌面应用程序可以使用此 API 对使用[Fluent 设计系统](../design/fluent-design-system/index.md)和支持[Windows Ink](../design/input/pen-and-stylus-interactions.md)托管 UWP 控件。

托管 API UWP XAML 提供的更广泛的一组控件，我们提供了启用开发人员将 Fluent UI 带到非 UWP 桌面应用程序的基础。 这种情况有时称为*XAML 群岛*。 有关此开发人员方案的详细信息，请参阅[在桌面应用程序的 UWP 控件](xaml-host-controls.md)。

## <a name="is-the-uwp-xaml-hosting-api-right-for-your-desktop-application"></a>UWP XAML 托管 API 适合你的桌面应用程序？

UWP XAML 托管 API 用于托管在桌面应用程序的 UWP 控件提供的低级别的基础结构。 某些类型的桌面应用程序可以选择使用备用、 更便利的 Api 来实现此目的。  

* 如果你有 C/c + + Win32 桌面应用程序，并且你希望你的应用程序中托管 UWP 控件，则必须使用 UWP XAML 托管 API。 没有针对这些类型的应用程序的方法。

* 对于 WPF 和 Windows 窗体应用程序，我们建议你使用的[包装的控件](xaml-host-controls.md#wrapped-controls)和[主机控件](xaml-host-controls.md#host-controls)而不是 UWP XAML 在 Windows 社区工具包中托管 API。 这些控件使用 UWP XAML 内部托管 API，并提供更简单的开发体验。 但是，你可以使用 UWP XAML 托管 API 直接在这些类型的应用程序中，如果你选择。

## <a name="related-samples"></a>相关示例

使用 UWP XAML 在代码中托管 API 的方式取决于你的应用程序类型，你的应用程序，以及其他因素的设计。 若要帮助说明如何在完整的应用程序的上下文中使用此 API，本文指的是代码从下面的示例。

  * **C/c + + Win32:** GitHub 上[XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32)示例。 此示例演示了如何使用 UWP XAML 在一个简单的 Win32 应用程序中托管 API 和处理 DPI 的更改。

  * **WPF:** 在 Windows 社区工具包中[**WindowsXamlHost**](https://github.com/Microsoft/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Wpf.UI.XamlHost)控件的 WPF 版本。 此控件派生自[**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost.aspx)并使用 UWP XAML 内部托管 API。 有关在 WPF 应用程序中使用**System.Windows.Interop.HwndHost**背景信息，请参阅[托管内容在 WPF 的 Win32](https://docs.microsoft.com/dotnet/framework/wpf/advanced/hosting-win32-content-in-wpf)。

  * **Windows 窗体：** 在 Windows 社区工具包中[**WindowsXamlHost**](https://github.com/Microsoft/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Forms.UI.XamlHost)控件的 Windows 窗体版本。 此控件派生自[**System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control)并使用 UWP XAML 内部托管 API。

## <a name="prerequisites"></a>必备条件

UWP XAML 托管 API 具有以下先决条件。

* Windows 10 Insider Preview 版本 17709 （或更高版本） 和相应的 Windows SDK Insider Preview 版本。 由于这是不断发展的功能，我们建议使用最新可用的最佳体验用于生成。

* 若要使用 UWP XAML 在桌面应用程序中托管 API，你将需要配置你的项目，以便你可以调用 UWP Api:

    * **C/c + + Win32:** 我们建议你配置你的项目以使用[C + + WinRT](../cpp-and-winrt-apis/index.md)。 下载并安装[C + + /winrt Visual Studio 扩展 (VSIX)](https://aka.ms/cppwinrt/vsix)从 Visual Studio Marketplace，然后添加```<CppWinRTEnabled>true</CppWinRTEnabled>```作为描述[此处](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).vcxproj 文件的属性。

    * **Windows 窗体和 WPF:** 请按照[以下说明](../porting/desktop-to-uwp-enhance.md#modify-a-net-project-to-use-uwp-apis)。

## <a name="architecture-of-xaml-islands"></a>体系结构的 XAML 群岛

UWP XAML 托管 API 包括[**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource)、 [**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)和其他相关的类型[**Windows.UI.Xaml.Hosting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting)命名空间中。 桌面应用程序可以使用此 API 呈现的 UWP 控件和路由入和滑出元素的键盘焦点导航。 桌面应用程序还可以大小，并根据需要 UWP 控件的位置。

创建使用 XAML 中的桌面应用程序中托管 API 的 XAML 岛时，你将有以下层次结构的对象：

* 在基本级别是想要托管的 XAML 岛中你的应用程序的 UI 元素。 此 UI 元素必须具有窗口句柄 (HWND)。 你可以在其中承载 XAML 岛的 UI 元素示例包括[**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) WPF 应用程序、 Windows 窗体应用程序， [**System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control)和 C/c + + Win32 应用程序[窗口](https://docs.microsoft.com/windows/desktop/winmsg/about-windows)。

* 在下一级别是一个**DesktopWindowXamlSource**对象。 此对象提供用于托管的 XAML 岛的基础结构。 你的代码负责创建此对象并将其连接到父 UI 元素。

* 在创建**DesktopWindowXamlSource**时，此对象将自动创建本机子窗口来托管 UWP 控件。 此本机子窗口主要抽象代码，但你可以访问其句柄 (HWND) 如有必要。

* 最后，在顶层是要在桌面应用程序中托管 UWP 控件。 这可以是任何 UWP 对象派生自[**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement)，包括由 Windows SDK 以及自定义用户控件提供的任何 UWP 控件。

下图说明了 XAML 岛中的对象的层次结构。

![DesktopWindowXamlSource 体系结构](images/xaml-hosting-api-rev2.png)

## <a name="how-to-host-uwp-xaml-controls"></a>如何以托管 UWP XAML 控件

下面是使用 UWP XAML 托管 API 来托管你的应用程序中的 UWP 控件的主要步骤。

1. 你的应用程序创建的任何[**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement)对象，它将承载在[**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource)之前，请初始化的 UWP XAML 框架的当前线程。

    * 实例化**DesktopWindowXamlSource**对象时，如果你的应用程序创建**DesktopWindowXamlSource**对象创建的任何**Windows.UI.Xaml.UIElement**对象之前，将为你中初始化此框架. 在此方案中，你无需添加你自己来初始化框架的任何代码。

    * 但是，如果你的应用程序创建**Windows.UI.Xaml.UIElement**对象创建将承载它们的**DesktopWindowXamlSource**对象之前，你的应用程序必须调用静态[**WindowsXamlManager.InitializeForCurrentThread**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread)方法来显式初始化 UWP XAML 框架之前**Windows.UI.Xaml.UIElement**对象实例化。 你的应用程序通常应时应调用此方法进行实例化承载**DesktopWindowXamlSource**的父 UI 元素。

    ```cppwinrt
    Windows::UI::Xaml::Hosting::WindowsXamlManager windowsXamlManager =
        Windows::UI::Xaml::Hosting::WindowsXamlManager::InitializeForCurrentThread();
    ```

    ```csharp
    global::Windows.UI.Xaml.Hosting.WindowsXamlManager windowsXamlManager =
        global::Windows.UI.Xaml.Hosting.WindowsXamlManager.InitializeForCurrentThread();
    ```

    > [!NOTE]
    > 此方法返回一个[**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)对象包含对 UWP XAML 框架的引用。 你可以创建多个**WindowsXamlManager**对象根据需要在给定的线程上。 但是，由于每个对象包含对 UWP XAML 框架的引用，你应释放的对象，以确保将最终释放 XAML 资源。

2. 创建[**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource)对象并将其附加到在你的应用程序与窗口句柄的父 UI 元素。

    若要执行此操作，你将需要按照以下步骤：

    1. 创建**DesktopWindowXamlSource**对象并将其转换到**IDesktopWindowXamlSourceNative** COM 接口。 此接口中声明```windows.ui.xaml.hosting.desktopwindowxamlsource.h```Windows SDK 中的标头文件。 在 C/c + + Win32 项目中，你可以直接引用此头文件。 在 WPF 或 Windows 窗体项目中，你将需要声明此接口在你的应用程序代码中使用的[**ComImport**](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute)属性。 请确保你接口声明完全匹配中的接口声明```windows.ui.xaml.hosting.desktopwindowxamlsource.h```。

    2. 调用**AttachToWindow**方法的**IDesktopWindowXamlSourceNative**接口，然后传入你的应用程序中的父 UI 元素的窗口句柄。

    3. 设置初始**DesktopWindowXamlSource**中包含内部子窗口的大小。 默认情况下，此内部子窗口设置为的宽度和高度为 0。 如果你未设置窗口的大小，你将添加到**DesktopWindowXamlSource**任何 UWP 控件将不可见。 若要访问**DesktopWindowXamlSource**中的内部子窗口，使用**WindowHandle**的**IDesktopWindowXamlSourceNative**接口。 以下示例使用[SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos)函数来设置窗口的大小。

    下面是一些代码示例，演示了此过程。

    ```cppwinrt
    // This example assumes you already have an HWND variable named 'parentHwnd' that
    // contains the handle of the parent window.
    Windows::UI::Xaml::Hosting::DesktopWindowXamlSource desktopWindowXamlSource;
    auto interop = desktopWindowXamlSource.as<IDesktopWindowXamlSourceNative>();
    check_hresult(interop->AttachToWindow(parentHwnd));

    HWND childInteropHwnd = nullptr;
    interop->get_WindowHandle(&childInteropHwnd);

    SetWindowPos(childInteropHwnd, 0, 0, 0, 300, 300, SWP_SHOWWINDOW);
    ```

    ```csharp
    // This WPF example assumes you already have an HwndHost named 'parentHwndHost'
    // that will act as the parent UI elemnt for your XAML island. It also assumes
    // you have used the DllImport attribute to import SetWindowPos from user32.dll
    // as a static method into a class named NativeMethods.
    Windows.UI.Xaml.Hosting.DesktopWindowXamlSource desktopWindowXamlSource =
        new Windows.UI.Xaml.Hosting.DesktopWindowXamlSource();

    IntPtr iUnknownPtr = System.Runtime.InteropServices.Marshal.GetIUnknownForObject(
        desktopWindowXamlSource);
    IDesktopWindowXamlSourceNative desktopWindowXamlSourceNative =
        System.Runtime.InteropServices.Marshal.Marshal.GetTypedObjectForIUnknown(
            iUnknownPtr, typeof(IDesktopWindowXamlSourceNative))
            as IDesktopWindowXamlSourceNative;

    desktopWindowXamlSourceNative.AttachToWindow(parentHwndHost.Handle);

    var childInteropHwnd = desktopWindowXamlSourceNative.WindowHandle;
    NativeMethods.SetWindowPos(childInteropHwnd, HWND_TOP, 0, 0, 300, 300, SWP_SHOWWINDOW);
    ```

3. 设置**Windows.UI.Xaml.UIElement**你想要主机到**DesktopWindowXamlSource**对象的[**内容**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content)属性。 以下示例设置了名为[**Windows.UI.Xaml.Controls.Grid**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) ```myGrid```的**内容**属性。

   ```cppwinrt
   desktopWindowXamlSource.Content(myGrid);
   ```

   ```csharp
   desktopWindowXamlSource.Content = myGrid;
   ```

有关完整示例，演示了有效示例应用程序的上下文中的这些任务，请参阅下面的代码文件：

  * **C/c + + Win32:** GitHub 上查看[XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32)示例中的[Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp)文件。
  * **WPF:** 请参阅 Windows 社区工具包中的[WindowsXamlHostBase.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs)和[WindowsXamlHost.cs](https://github.com/Microsoft/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Win32/Microsoft.Windows.Interop.WindowsXamlHost.WPF/WindowsXamlHost.cs)文件。  
  * **Windows 窗体：** 请参阅 Windows 社区工具包中的[WindowsXamlHostBase.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs)和[WindowsXamlHost.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs)文件。


## <a name="how-to-host-custom-uwp-xaml-controls"></a>如何自定义的主机 UWP XAML 控件

> [!IMPORTANT]
> 目前，仅在 C# WPF 和 Windows 窗体应用程序中支持从第三方的自定义 UWP XAML 控件。 你必须具有控件的源代码，因此你的应用程序编译。

如果你想要托管自定义 UWP XAML 控件 （你定义自己的控件或第三方提供的控件），则必须执行下列任务除了在[上一节](#how-to-host-uwp-xaml-controls)中所述的过程。

1. 定义派生自[**Windows.UI.Xaml.Application**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application)并且还将实现[**IXamlMetadataProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider)的自定义类型。 此类型可用作根元数据提供程序并加载你的应用程序的自定义 UWP XAML 类型在当前目录中的程序集元数据。

    有关示例，演示了如何执行此操作，请参阅 Windows 社区工具包中的[XamlApplication.cs](https://github.com/Microsoft/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Win32/Microsoft.Windows.Interop.WindowsXamlHost.Shared/XamlApplication.cs)代码文件。 此文件是共享 WPF 和 Windows 窗体，帮助说明了如何使用 UWP XAML 承载这些类型的应用中的 API 实现**WindowsXamlHost**类的一部分。

2. 调用[**GetXamlType**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider.getxamltype)根元数据提供程序分配 UWP XAML 控件的类型名称时 （这可能在代码中分配在运行时，或者你可能会选择启用该选项可在 Visual Studio 属性窗口中分配）。

    有关示例，演示了如何执行此操作，请参阅 Windows 社区工具包中的[UWPTypeFactory.cs](https://github.com/Microsoft/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Win32/Microsoft.Windows.Interop.WindowsXamlHost.Shared/UWPTypeFactory.cs)代码文件。 此文件是实现的 WPF 和 Windows 窗体共享**WindowsXamlHost**类的一部分。

3. 将自定义 UWP XAML 控件的源代码集成到你的主机应用程序解决方案，生成自定义控件，并按以下[这些说明](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/docs/controls/WindowsXAMLHost.md#add-a-custom-uwp-control)在你的应用程序中使用它。

## <a name="how-to-handle-keyboard-focus-navigation"></a>如何处理键盘焦点导航

当用户在应用程序 （例如，按**tab 键**或方向/箭头键） 使用键盘的 UI 元素之间进行导航时，你将需要以编程方式将焦点移入和滑出**DesktopWindowXamlSource**对象。 当用户的键盘导航达到**DesktopWindowXamlSource**，到你的 ui，导航顺序中的第一个[**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement)对象移动焦点时继续将焦点移至以下**Windows.UI.Xaml.UIElement**对象作为用户将循环的元素，然后重新出**DesktopWindowXamlSource**并父 UI 元素的移动焦点。  

托管 API UWP XAML 提供了多种类型和成员，以帮助你完成这些任务。

1. 当键盘导航输入你**DesktopWindowXamlSource**时，会引发的[**GotFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus)事件。 处理此事件并以编程方式将焦点移到第一个托管**Windows.UI.Xaml.UIElement**使用[**NavigateFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus)方法。

2. 当用户位于你**DesktopWindowXamlSource**的最后一个可聚焦元素，并按下**Tab**键或箭头键时，会引发的[**TakeFocusRequested**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested)事件。 处理此事件并以编程方式将焦点移至主机应用程序中的下一个可聚焦元素。 例如，在**DesktopWindowXamlSource** [**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost.aspx)中的托管位置 WPF 应用程序，你可以使用[**MoveFocus**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus)方法将焦点传输到主机应用程序中的下一个可聚焦元素。

有关演示如何执行此操作的有效示例应用程序上下文中的示例，请参阅下面的代码文件：
  * **WPF:** 请参阅 Windows 社区工具包中的[WindowsXamlHostBase.Focus.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs)文件。  
  * **Windows 窗体：** 请参阅 Windows 社区工具包中的[WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs)文件。

## <a name="how-to-handle-layout-changes"></a>如何处理的布局更改

当用户更改父 UI 元素的大小时，你需要处理任何必要的布局更改，以确保你的 UWP 控件按预期显示。 下面是要考虑的一些重要方案。

1. 当父 UI 元素需要获取适合**DesktopWindowXamlSource**托管**Windows.UI.Xaml.UIElement**所需的矩形区域的大小时，调用**Windows.UI.Xaml.UIElement 的[**度量**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure)方法**. 例如：
    * 在 WPF 应用程序可能会执行此操作的托管**DesktopWindowXamlSource** [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost.aspx) [**MeasureOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride)方法。
    * 在 Windows 窗体应用程序可能会执行此操作的[**控件**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control)的托管**DesktopWindowXamlSource** [**GetPreferredSize**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize)方法。

2. 大小父 UI 元素更改，调用的根**Windows.UI.Xaml.UIElement** [**排列**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange)方法时**DesktopWindowXamlSource**上托管。 例如：
    * 在 WPF 应用程序中你可能会从托管**DesktopWindowXamlSource** [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost.aspx)对象的[**ArrangeOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride)方法执行此操作。
    * 在 Windows 窗体应用程序中你可以这样做从[**控件**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) [**SizeChanged**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged)事件的处理程序承载**DesktopWindowXamlSource**。

有关演示如何执行此操作的有效示例应用程序上下文中的示例，请参阅下面的代码文件：
  * **WPF:** 请参阅 Windows 社区工具包中的[WindowsXamlHost.Layout.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs)文件。  
  * **Windows 窗体：** 请参阅 Windows 社区工具包中的[WindowsXamlHost.Layout.cs](https://github.com/Microsoft/WindowsCommunityToolkit/blob/master/Microsoft.Toolkit.Win32/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs)文件。

## <a name="how-to-handle-dpi-changes"></a>如何处理 DPI 的更改

如果你想要处理窗口的 DPI 更改你的 UWP 控件 （例如，如果用户将窗口拖动之间使用不同的屏幕 DPI 的监视器） 的主机，你将需要使用呈现转换配置 UWP 控件，侦听 DPI 更改你的应用并更新窗口位置和呈现转换的 UWP 控件以响应 DPI 更改。

以下步骤演示了处理此过程的 C/c + + Win32 应用程序上下文中的一种方法。 有关完整的示例，请参阅 GitHub 上的[XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32)示例中的[Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp)和[Desktop.h](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.h)代码文件。

1. 维护你的应用中的[**ScaleTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform)对象并将其分配给你的 UWP 控件的[**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform)方法。 下面的示例中的 C/c + + Win32 应用程序的[**Windows.UI.Xaml.Controls.Grid**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid)控件执行此操作。

    ```cppwinrt
    // Private fields maintained by your app, such as in a window class you have defined.
    Windows::UI::Xaml::Media::ScaleTransform m_scale;
    Windows::UI::Xaml::Controls::Grid m_rootGrid;

    // Code that runs during initialization, such as the constructor for a window class you have defined.
    m_rootGrid.RenderTransform(m_scale);
    ```

2. 在[**WindowProc**](https://msdn.microsoft.com/library/windows/desktop/ms633573.aspx)函数中，侦听[**WM_DPICHANGED**](https://docs.microsoft.com/windows/desktop/hidpi/wm-dpichanged)消息。 为了响应此消息：
    * 使用[**SetWindowPos**](https://msdn.microsoft.com/library/windows/desktop/ms633545)函数来调整窗口，其中包含 UWP 控件传递给该消息的矩形的大小。
    * 更新的新的 DPI 值根据你**ScaleTransform**对象的 x 轴和 y 轴比例系数。
    * 对的外观和 UWP 控件的布局进行任何必要的调整。 下面的代码示例调整[**填充**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.padding)响应 DPI 更改的托管**Windows.UI.Xaml.Controls.Grid**控件。

    ```cppwinrt
    LRESULT HandleDpiChange(HWND hWnd, WPARAM wParam, LPARAM lParam)
    {
        HWND hWndStatic = GetWindow(hWnd, GW_CHILD);
        if (hWndStatic != nullptr)
        {
            UINT uDpi = HIWORD(wParam);

            // Resize the window
            auto lprcNewScale = reinterpret_cast<RECT*>(lParam);

            SetWindowPos(hWnd, nullptr, lprcNewScale->left, lprcNewScale->top,
                lprcNewScale->right - lprcNewScale->left, lprcNewScale->bottom - lprcNewScale->top,
                SWP_NOZORDER | SWP_NOACTIVATE);

            NewScale(uDpi);
          }
          return 0;
    }

    void NewScale(UINT dpi) {

        auto scaleFactor = (float)dpi / 100;

        if (m_scale != nullptr) {
            m_scale.ScaleX(scaleFactor);
            m_scale.ScaleY(scaleFactor);
        }

        ApplyCorrection(scaleFactor);
    }

    void ApplyCorrection(float scaleFactor) {
        float rightCorrection = (m_rootGrid.Width() * scaleFactor - m_rootGrid.Width()) / scaleFactor;
        float bottomCorrection = (m_rootGrid.Height() * scaleFactor - m_rootGrid.Height()) / scaleFactor;

        m_rootGrid.Padding(Windows::UI::Xaml::ThicknessHelper::FromLengths(0, 0, rightCorrection, bottomCorrection));
    }
    ```

2. 若要配置你的应用程序，为每个显示器的 DPI 感知，向项目添加[并排的程序集清单](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests)，并设置```<dpiAwareness>```元素中的```PerMonitorV2```。 有关此值的详细信息，请参阅[**DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2**](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context)的描述。

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
        <application xmlns="urn:schemas-microsoft-com:asm.v3">
            <windowsSettings>
                <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2</dpiAwareness>
            </windowsSettings>
        </application>
    </assembly>
    ```

    对于完整的示例通过并行集清单，请参阅 GitHub 上的[XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32)示例中的[XamlIslandsWin32.exe.manifest](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/XamlIslandsWin32.exe.manifest)文件。

## <a name="limitations"></a>限制

托管 API 的 XAML 共享相同限制在 Windows 10 的所有其他类型的 XAML 主机控件。 有关详细的列表，请参阅[XAML 主机控件限制](xaml-host-controls.md#limitations)。

## <a name="troubleshooting"></a>疑难解答

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>使用 UWP XAML 托管 API 在 UWP 应用中的错误

| 问题 | 解决方案 |
|-------|------------|
| 你的应用接收**COMException**以下消息:"无法激活 DesktopWindowXamlSource。 此类型不能在 UWP 应用。" 或者"无法激活 WindowsXamlManager。 此类型不能在 UWP 应用。" | 此错误指示想要使用 UWP XAML 托管 API （具体而言，想要实例化[**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource)或[**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)类型） 的 UWP 应用中。 UWP XAML 托管 API 仅用于非 UWP 的桌面应用程序，如 WPF、 Windows 窗体和 C/c + + Win32 应用程序时。 |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>附加到不同的线程上的窗口的错误

| 问题 | 解决方案 |
|-------|------------|
| 你的应用接收**COMException**以下消息:"AttachToWindow 方法失败，因为指定的 HWND 创建不同的线程上。 | 此错误指示你的应用程序调用**IDesktopWindowXamlSourceNative.AttachToWindow**方法，并传递它在不同的线程创建一个窗口的 HWND。 你必须通过此方法在调用该方法的代码所在的相同线程创建一个窗口的 HWND。 |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>附加到在不同的顶级窗口的窗口的错误

| 问题 | 解决方案 |
|-------|------------|
| 你的应用接收**COMException**以下消息:"AttachToWindow 方法失败，因为指定的 HWND 递减从同一个线程之前传递到 AttachToWindow HWND 不同的顶级窗口。 | 此错误指示你的应用程序调用**IDesktopWindowXamlSourceNative.AttachToWindow**方法，并将其传递递减从你在之前调用此方法中指定一个窗口不同的顶级窗口的窗口的 HWND在相同的线程。</p></p>你的应用程序在特定的线程上调用**IDesktopWindowXamlSourceNative.AttachToWindow**后，在相同的线程上的所有其他[**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource)对象只能将附加到同一顶级窗口的后代的 windows**IDesktopWindowXamlSourceNative.AttachToWindow**首次调用中传递。 为特定线程关闭所有**DesktopWindowXamlSource**对象下, 一步**DesktopWindowXamlSource**时，然后自由地将附加到任何窗口再次。</p></p>若要解决此问题，请关闭所有在此线程上, 绑定到其他顶级窗口或为此**DesktopWindowXamlSource**创建一个新线程的**DesktopWindowXamlSource**对象。 |

## <a name="related-topics"></a>相关主题

* [在桌面应用程序的 UWP 控件](xaml-host-controls.md)