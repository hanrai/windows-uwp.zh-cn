---
title: 后台任务指南
description: 确保你的应用满足运行后台任务的要求。
ms.assetid: 18FF1104-1F73-47E1-9C7B-E2AA036C18ED
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10，uwp，后台任务
ms.localizationpriority: medium
ms.openlocfilehash: 31c0c272ae8ef2818ff1f26dc15dfb803c93e30b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360923"
---
# <a name="guidelines-for-background-tasks"></a>后台任务指南


确保你的应用满足运行后台任务的要求。

## <a name="background-task-guidance"></a>后台任务指南

在开发后台任务时以及发布应用之前，考虑以下指南。

如果你使用后台任务在后台播放媒体，请参阅[在后台播放媒体](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)，了解有关 Windows 10 版本 1607 中使此操作更加简单的改进信息。

**进程内与进程外后台任务：** Windows 10，版本 1607，引入[进程内的后台任务](create-and-register-an-inproc-background-task.md)该编辑器可以作为前台应用程序在同一进程中运行后台代码。 当决定执行进程内后台任务还是执行进程外后台任务时，请考虑以下因素：

|注意事项 | 影响 |
|--------------|--------|
|恢复能力   | 如果你的后台进程在另一个进程中运行，后台进程崩溃时不会关闭你的前台应用程序。 此外，如果后台活动运行时间超过了执行时间限制，就可能会终止，即使在你的应用中运行也是如此。 当前台进程和后台进程不需要相互通信时，将后台工作分离成一个独立于前台应用的任务可能是更好的选择（因为进程内后台任务的主要优势之一就是它们不需要进程之间相互通信）。 |
|简易性    | 进程内后台任务不需要跨进程通信，并且编写起来没有那么复杂。  |
|可用的触发器 | 进程内的后台任务不支持以下触发器：[DeviceUseTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceusetrigger?f=255&MSPPError=-2147217396)， [DeviceServicingTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceservicingtrigger)并**IoTStartupTask**。 |
|VoIP | 进程内后台任务不支持在应用程序中激活 VoIP 后台任务。 |  

**触发器实例数的限制：** 有多少个实例的某些触发器应用可以注册限制。 对于应用的每个实例，应用只能注册一次 [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger)、[MediaProcessingTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.mediaprocessingtrigger) 和 [DeviceUseTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceusetrigger?f=255&MSPPError=-2147217396)。 如果应用超出此限制，注册会引发异常。

**CPU 配额：** 后台任务都受其获取基于触发器类型的时钟使用时间量。 大多数触发器限制为 30 秒的时钟时间使用，而另一些触发器在完成耗时任务时最多可以运行 10 分钟。 为了延长电池使用时间并为前台应用提供最佳用户体验，后台任务应该是轻量级任务。 有关适用于后台任务的资源限制，请参阅[使用后台任务支持应用](support-your-app-with-background-tasks.md)。

**管理后台任务：** 您的应用程序应获取的已注册的后台任务列表、 进度和完成处理程序，注册并相应地处理这些事件。 你的后台任务类应该报告进度、取消以及完成。 有关详细信息，请参阅[处理取消的后台任务](handle-a-cancelled-background-task.md)和[监视后台任务进度和完成](monitor-background-task-progress-and-completion.md)。

**使用[BackgroundTaskDeferral](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskDeferral):** 如果您的后台任务类在运行异步代码，请确保使用延迟。 否则后台任务可能不得不提前终止时[运行](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run)方法将返回 (或[OnBackgroundActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onbackgroundactivated)方法在进程内的后台任务的情况下)。 有关详细信息，请参阅[创建和注册进程外后台任务](create-and-register-a-background-task.md)。

或者也可以请求一个延迟，并使用 **async/await** 来完成异步方法调用。 在 **await** 方法调用之后关闭延迟。

**更新应用程序清单：** 对于进程外运行的后台任务，声明应用程序清单，以及与一起使用的触发器的类型中每个后台任务。 否则，你的应用将不能在运行时注册后台任务。

如果有多个后台任务，请考虑应在同一个主机进程中运行它们，还是应将它们分配到多个主机进程。 如果担心某个后台任务失败可能会导致其他后台任务失败，请将它们分配到独立的主机进程。  使用清单设计器中的**资源组**条目将后台任务分组到不同的主机进程。 

要设置**资源组**，请打开 Package.appxmanifest 设计器，选择**声明**，然后添加一个**应用服务**声明：

![资源组设置](images/resourcegroup.png)

有关资源组设置的详细信息，请参阅[应用程序架构参考](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application)。

在与前台应用相同的进程中运行的后台任务不需要在应用程序清单中声明自己。 有关在清单中声明在进程外运行的后台任务的详细信息，请参阅[在应用程序清单中声明后台任务](declare-background-tasks-in-the-application-manifest.md)。

**准备应用更新：** 如果将更新您的应用程序，创建并注册**ServicingComplete**后台任务 (请参阅[SystemTriggerType](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType)) 来注册和注销的后台任务的以前版本的应用程序中，有关最新版本的后台任务。 此时也非常适合在前台运行的上下文之外执行所需的应用更新。

**若要执行后台任务的请求：**

> **重要**  从 Windows 10 开始，应用程序是否不再需要在锁定屏幕上运行后台任务的必备组件。

通用 Windows 平台 (UWP) 应用无需固定到锁屏界面，即可运行所有受支持的任务类型。 但是，应用必须在注册任何类型的后台任务之前调用 [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync)。 如果用户在设备设置中显式拒绝了应用的后台任务权限，此方法将返回 [**BackgroundAccessStatus.DeniedByUser**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundAccessStatus)。 有关后台活动和节电模式的相关用户选项的详细信息，请参阅[优化后台活动](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity)。 
## <a name="background-task-checklist"></a>后台任务清单

*适用于这两个进程内和进程外后台任务*

-   将后台任务与正确的触发器关联。
-   添加条件以帮助确保你的后台任务成功运行。
-   处理后台任务进度、完成以及取消。
-   在应用启动期间重新注册后台任务。 这可以确保在应用首次启动时注册它们。 它还提供了一种检测用户是否已禁用应用的后台执行功能的方法（如果事件注册失败）。
-   检查是否存在后台任务注册错误。 如果适用，请尝试使用不同参数值来再次注册后台任务。
-   对于除台式机以外的所有设备系列，如果设备内存不足，后台任务可能会终止。 如果没有呈现内存不足异常，或者应用没有处理该异常，则后台任务将在没有警告且不引发 OnCanceled 事件的情况下终止。 这有助于确保前台中应用的用户体验。 应该将后台任务设计为处理此情形。

*仅适用于进程外后台任务*

-   在 Windows 运行时组件中创建后台任务。
-   不要通过后台任务显示 UI，但 Toast、磁贴以及锁屏提醒更新除外。
-   在 [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) 方法中，为每个异步方法调用请求延迟并在完成该方法时关闭它们。 或者，通过 **async/await** 使用一个延迟。
-   使用永久性存储在后台任务与应用之间共享数据。
-   在应用程序清单中声明每个后台任务及其使用的触发器类型。 确保入口点和触发器类型正确。
-   不要在清单中指定 Executable 元素，除非使用的是应在与应用相同的上下文中运行的触发器（例如 [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger)）。

*仅适用于进程内的后台任务*

- 当取消任务时，请确保 `BackgroundActivated` 事件处理程序退出后再取消，否则整个进程都将终止。
-   编写生存时间较短的后台任务。 后台任务限制为在 30 秒的时钟时间内使用。
-   不要依赖后台任务中的用户交互。

## <a name="related-topics"></a>相关主题

* [创建和注册进程内后台任务](create-and-register-an-inproc-background-task.md)。
* [创建和注册进程外后台任务](create-and-register-a-background-task.md)
* [在应用程序清单中声明后台任务](declare-background-tasks-in-the-application-manifest.md)
* [在背景中播放媒体](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)
* [处理取消的后台任务](handle-a-cancelled-background-task.md)
* [监视后台任务进度和完成](monitor-background-task-progress-and-completion.md)
* [注册后台任务](register-a-background-task.md)
* [使用后台任务响应系统事件](respond-to-system-events-with-background-tasks.md)
* [设置后台任务的运行条件](set-conditions-for-running-a-background-task.md)
* [使用后台任务更新动态磁贴](update-a-live-tile-from-a-background-task.md)
* [使用维护触发器](use-a-maintenance-trigger.md)
* [在计时器上运行后台任务](run-a-background-task-on-a-timer-.md)
* [调试后台任务](debug-a-background-task.md)
* [如何在触发挂起、 继续和后台 UWP 应用中的事件 （在调试）](https://go.microsoft.com/fwlink/p/?linkid=254345)

 

 
