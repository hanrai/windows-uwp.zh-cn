---
description: 业务和数据层位于你的 UI 之后。
title: 移植到 UWP 的 Windows Phone Silverlight 业务和数据层
ms.assetid: 27c66759-2b35-41f5-9f7a-ceb97f4a0e3f
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 25e7fdcb4195dcc0dffed7657d41bd02bea8a5c2
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322299"
---
#  <a name="porting-windowsphone-silverlight-business-and-data-layers-to-uwp"></a>移植到 UWP 的 Windows Phone Silverlight 业务和数据层


上一主题是[针对 I/O、设备和应用模型进行移植](wpsl-to-uwp-input-and-sensors.md)。

业务和数据层位于你的 UI 之后。 这两个层中的代码将调用操作系统和 .NET Framework API（例如，后台处理、位置、相机、文件系统、网络和其他数据访问）。 绝大多数这些代码都[适用于通用 Windows 平台 (UWP) 应用](https://docs.microsoft.com/previous-versions/windows/br211369(v=win.10))，因此希望能够在不进行更改的情况下移植大部分的此类代码。

## <a name="asynchronous-methods"></a>异步方法

通用 Windows 平台 (UWP) 的首要任务之一是使你能够生成真实、一致且高响应性的应用。 动画始终保持流畅，并且触摸交互（例如平移和轻扫）是瞬间操作且不会出现滞后，就像 UI 与你的手指融为一体一样。 若要实现此目的，则不能保证在 50 毫秒内完成的任何 UWP API 都需进行异步操作，并且其名称后缀为 **Async**。 UI 线程将通过调用 **Async** 方法立即返回，该操作将在另一个线程上进行。 在语法上使用 C# **await** 运算符、JavaScript promise 对象以及 C++ 延续，以便于轻松使用 **Async** 方法。 有关详细信息，请参阅[异步编程](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps)。

## <a name="background-processing"></a>后台处理

Windows Phone Silverlight 应用程序可以使用托管**ScheduledTaskAgent**对象不在前台应用程序时执行的任务。 UWP 应用使用 [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) 类以类似的方式创建和注册后台任务。 定义用于实现后台任务操作的类。 系统通过调用要执行该操作的类的 [**Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) 方法，定期运行后台任务。 在 UWP 应用中，请记得设置应用包清单中的 **“后台任务”** 声明。 有关详细信息，请参阅[使用后台任务支持应用](https://docs.microsoft.com/windows/uwp/launch-resume/support-your-app-with-background-tasks)。

若要传输较大的数据文件在后台，Windows Phone Silverlight 应用程序使用**BackgroundTransferService**类。 UWP 应用将使用 [**Windows.Networking.BackgroundTransfer**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer) 命名空间中的 API 来执行此操作。 这些功能使用相似的模式启动传输，但是新的 API 已改进功能和性能。 有关详细信息，请参阅[在后台传输数据](https://docs.microsoft.com/previous-versions/windows/apps/hh452975(v=win.10))。

Windows Phone Silverlight 应用程序使用中的托管的类**Microsoft.Phone.BackgroundAudio**命名空间以播放音频时应用不在前景中。 有关 UWP 使用 Windows Phone 应用商店应用模型的信息，请参阅[后台音频](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)和[后台音频](https://go.microsoft.com/fwlink/p/?linkid=619997)示例。

## <a name="cloud-services-networking-and-databases"></a>云服务、网络和数据库

使用 Azure 可将数据和应用服务托管在云中。 请参阅[移动服务入门](https://go.microsoft.com/fwlink/p/?LinkID=403138)。 解决方案需要联机和脱机数据，请参阅：[移动服务中使用脱机数据同步](https://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/)。

UWP 部分支持 **System.Net.HttpWebRequest** 类，但 **System.Net.WebClient** 类不受支持。 推荐的预期备用项为 [**Windows.Web.Http.HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) 类 （或 [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))，前提是你需要将你的代码移植到支持 .NET 的其他平台）。 这些 API 使用 [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) 来表示 HTTP 请求。

UWP 应用目前不提供对数据密集型应用场景（如业务线 (LOB) 应用场景）的内置支持。 但是，你可以将 SQLite 用于本地事务性数据库服务。 有关详细信息，请参阅 [SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform)。

将绝对 URI（而非相对 URI）传递给 Windows 运行时类型。 请参阅 [将 URI 传递给 Windows 运行时](https://docs.microsoft.com/dotnet/standard/cross-platform/passing-a-uri-to-the-windows-runtime)。

## <a name="launchers-and-choosers"></a>启动器和选择器

使用启动器和选择器 (位于**Microsoft.Phone.Tasks**命名空间)，Windows Phone Silverlight 应用程序可以与操作系统来执行常见操作，如撰写一封电子邮件，选择一张照片，进行交互或与另一个应用程序共享某些种类的数据。 搜索**Microsoft.Phone.Tasks**主题中的[Windows 10 命名空间和类映射到 Windows Phone Silverlight](wpsl-to-uwp-namespace-and-class-mappings.md)查找等效的 UWP 类型。 类似机制（名为启动器和选取器）中的这些范围，用于通过在应用之间共享数据来实现合约。

Windows Phone Silverlight 应用程序可以放到休眠状态或甚至被逻辑删除时使用，例如，照片选择器任务。 当使用 [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 类时，UWP 应用将保持活动状态并在运行。

## <a name="monetization-trial-mode-and-in-app-purchases"></a>盈利（试用模式和应用内购买）

Windows Phone Silverlight 应用程序可以使用 UWP [**CurrentApp** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp)类的大部分其试用模式和应用内购买功能，以便代码不需要可移植。 但 Windows Phone Silverlight 应用程序调用**MarketplaceDetailTask.Show**提供适用于购买的应用：

```csharp
    private void Buy()
    {
        MarketplaceDetailTask marketplaceDetailTask = new MarketplaceDetailTask();
        marketplaceDetailTask.ContentType = MarketplaceContentType.Applications;
        marketplaceDetailTask.Show();
    }
```

移植该代码以调用 UWP [**RequestAppPurchaseAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) 方法：

```csharp
    private async void Buy()
    {
        await Windows.ApplicationModel.Store.CurrentApp.RequestAppPurchaseAsync(false);
    }
```

如果你具有可模拟应用购买和应用内购买功能以供测试的代码，则可以移植该代码以改用 [**CurrentAppSimulator**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) 类。

## <a name="notifications-for-tile-or-toast-updates"></a>磁贴或 Toast 更新的通知

通知是一种扩展的推送通知模型的 Windows Phone Silverlight 应用程序。 当你从 Windows 推送通知服务 (WNS) 接收通知时，你可以看到带有磁贴更新或 Toast 的 UI 的信息。 有关移植通知功能的 UI 一侧的信息，请参阅[磁贴和 Toasts](w8x-to-uwp-porting-xaml-and-ui.md)。

有关在 UWP 应用中使用通知的详细信息，请参阅[发送 Toast 通知](https://docs.microsoft.com/previous-versions/windows/apps/hh868266(v=win.10))。

有关在使用 C++、C# 或 Visual Basic 的 Windows 运行时应用中使用磁贴、Toast、锁屏提醒、横幅和通知的信息和教程，请参阅[使用磁贴、锁屏提醒和 Toast 通知](https://docs.microsoft.com/previous-versions/windows/apps/hh868259(v=win.10))。

## <a name="storage-file-access"></a>存储（文件访问）

将应用设置存储在独立存储中的键 / 值对形式的 Windows Phone Silverlight 代码轻松地移植。 下面是之前和之后的示例中，第一次 Windows Phone Silverlight 版本：

```csharp
    var propertySet = IsolatedStorageSettings.ApplicationSettings;
    const string key = "favoriteAuthor";
    propertySet[key] = "Charles Dickens";
    propertySet.Save();
    string myFavoriteAuthor = propertySet.Contains(key) ? (string)propertySet[key] : "<none>";
```

第二个是 UWP 等效项：

```csharp
    var propertySet = Windows.Storage.ApplicationData.Current.LocalSettings.Values;
    const string key = "favoriteAuthor";
    propertySet[key] = "Charles Dickens";
    string myFavoriteAuthor = propertySet.ContainsKey(key) ? (string)propertySet[key] : "<none>";
```

尽管的子集**Windows.Storage**命名空间是提供给他们，许多 Windows Phone Silverlight 应用程序执行文件与 i/o **IsolatedStorageFile**类，因为它已支持的更长。 假设**IsolatedStorageFile**是正在使用，下面是编写和读取文件，首先将 Windows Phone Silverlight 版本的之前和之后的示例：

```csharp
    const string filename = "FavoriteAuthor.txt";
    using (var store = IsolatedStorageFile.GetUserStoreForApplication())
    {
        using (var streamWriter = new StreamWriter(store.CreateFile(filename)))
        {
            streamWriter.Write("Charles Dickens");
        }
        using (var StreamReader = new StreamReader(store.OpenFile(filename, FileMode.Open, FileAccess.Read)))
        {
            string myFavoriteAuthor = StreamReader.ReadToEnd();
        }
    }
```

而在使用 UWP 中也具有相同的功能：

```csharp
    const string filename = "FavoriteAuthor.txt";
    var store = Windows.Storage.ApplicationData.Current.LocalFolder;
    Windows.Storage.StorageFile file = await store.CreateFileAsync(filename, Windows.Storage.CreationCollisionOption.ReplaceExisting);
    await Windows.Storage.FileIO.WriteTextAsync(file, "Charles Dickens");
    file = await store.GetFileAsync(filename);
    string myFavoriteAuthor = await Windows.Storage.FileIO.ReadTextAsync(file);
```

Windows Phone Silverlight 应用程序具有只读访问权限的可选的 SD 卡。 UWP 应用对 SD 卡具有读写访问权限。 有关详细信息，请参阅[访问 SD 卡](https://docs.microsoft.com/windows/uwp/files/access-the-sd-card)。

有关在 UWP 应用中访问照片、音乐和视频文件的信息，请参阅[音乐、图片和视频库中的文件和文件夹](https://docs.microsoft.com/windows/uwp/files/quickstart-managing-folders-in-the-music-pictures-and-videos-libraries)。

有关详细信息，请参阅[文件、文件夹和库](https://docs.microsoft.com/windows/uwp/files/index)。

下一主题是[针对外形规格和 UX 进行移植](wpsl-to-uwp-form-factors-and-ux.md)。

## <a name="related-topics"></a>相关主题

* [Namespace 和类映射](wpsl-to-uwp-namespace-and-class-mappings.md)
 

