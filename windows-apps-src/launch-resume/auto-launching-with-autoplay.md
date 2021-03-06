---
title: 借助自动播放功能自动启动
description: 可以使用自动播放功能在用户将设备连接到其电脑时，将应用作为一个选项提供。 这包括非卷设备（如相机或媒体播放器）或卷设备（如 U 盘、SD 卡或 DVD）。
ms.assetid: AD4439EA-00B0-4543-887F-2C1D47408EA7
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1cc13470c1f07d1ee420253c8a147ff7a5c3fc40
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318772"
---
# <a name="span-iddevlaunchresumeauto-launchingwithautoplayspanauto-launching-with-autoplay"></a><span id="dev_launch_resume.auto-launching_with_autoplay"></span>使用自动播放自动启动

可以使用“自动播放”  功能在用户将设备连接到其电脑时，将应用作为一个选项提供。 这包括非卷设备（如相机或媒体播放器）或卷设备（如 U 盘、SD 卡或 DVD）。 还可以使用“自动播放”  功能在用户使用邻近感应（点击）在两台电脑之间共享文件时，将应用作为一个选项提供。

> **请注意**  如果你是设备制造商和你想要关联您[Microsoft Store 的设备应用程序](https://go.microsoft.com/fwlink/p/?LinkID=301381)作为**自动播放**为你的设备的处理程序，可以确定在该应用设备元数据。 有关详细信息，请参阅 [Microsoft Store 设备应用的自动播放](https://go.microsoft.com/fwlink/p/?LinkId=306684)。

## <a name="register-for-autoplay-content"></a>注册自动播放内容

可以将应用注册为“自动播放”  内容事件的选项。 当卷设备（如相机内存卡、指状驱动器或 DVD）插入到电脑时，会引发“自动播放”  内容事件。 下面我们介绍如何在插入来自相机的卷设备时，将应用标识为的“自动播放”  选项。

在本教程中，你创建了一个用于显示图像文件或将其复制到“图片”的应用。 为自动播放 **ShowPicturesOnArrival** 内容事件注册了应用。

“自动播放”功能还为在使用邻近感应（点击）的电脑之间共享的内容引发内容事件。 你可以使用本部分中的步骤和代码来处理使用邻近感应的电脑之间共享的文件。 下表列出了可以使用邻近感应共享内容的“自动播放”内容事件。

| 操作         | “自动播放内容”事件  |
|----------------|-------------------------|
| 共享音乐  | PlayMusicFilesOnArrival |
| 共享视频 | PlayVideoFilesOnArrival |

 
使用邻近感应共享文件时，**FileActivatedEventArgs** 对象的 **Files** 属性包含对拥有所有共享文件的根文件夹的引用。

### <a name="step-1-create-a-new-project-and-add-autoplay-declarations"></a>第 1 步：创建一个新项目并添加自动播放声明

1.  打开 Microsoft Visual Studio，然后从“文件”  菜单中选择“新建项目”  。 在“Visual C#”  部分的“Windows”  下，选择“空白应用(通用 Windows)”  。 将应用命名为 **AutoPlayDisplayOrCopyImages** 并单击“确定”  。
2.  打开 Package.appxmanifest 文件并选择“功能”  选项卡。选择“可移动存储”  和“图片库”  功能。 如此一来，该应用便可访问相机内存的可移动存储设备，也可访问本地图片。
3.  在清单文件中，选择“声明”  选项卡。在“可用声明”  下拉列表中，选择“自动播放内容”  ，然后单击“添加”  。 选择已添加到“支持的声明”  列表中的新“自动播放内容”  项。
4.  “自动播放内容”  声明会在自动播放引发内容事件时将你的应用标识为一个选项。 该事件基于卷设备（如 DVD 或 U 盘）的内容。 “自动播放”会检查卷设备的内容并确定要引发的内容事件。 如果卷的根目录包含 DCIM、 AVCHD 或专用\\ACHD 文件夹，或如果用户已启用**选择要执行的操作与每种类型的媒体**中的自动播放控制面板和图片位于根目录中的卷，然后自动播放会引发**ShowPicturesOnArrival**事件。 在“启动操作”  部分中，为第一个启动操作输入表 1 中的以下值。
5.  在“自动播放内容”  项的“启动操作”  部分中，单击“新增”  可添加第二个启动操作。 为第二个启动操作输入表 2 中的以下值。
6.  在“可用声明”  下拉列表中，选择“文件类型关联”  ，然后单击“添加”  。 中的新属性**文件类型关联**声明中，设置**显示名称**字段**自动播放复制或显示映像**和**名称**字段**映像\_association1**。 在“支持的文件类型”  部分中，单击“新增”  。 将“文件类型”  字段设置为 **.jpg**。 在“支持的文件类型”  部分中，将新文件关联的“文件类型”  字段设置为 **.png**。 对于内容事件，自动播放功能会筛选掉任何未与应用显式关联的文件类型。
7.  保存并关闭清单文件。

**表 1**

| 设置             | ReplTest1                 |
|---------------------|-----------------------|
| Verb                | 显示                  |
| 操作显示名称 | 显示图片         |
| 内容事件       | ShowPicturesOnArrival |

“操作显示名称”  设置标识自动播放为你的应用显示的字符串。 “谓词”  设置标识针对所选选项传递给你的应用的值。 你可以为自动播放事件指定多个启动操作并且可以使用“谓词”  设置确定用户为你的应用选择的选项。 你可以通过检查传递给应用的启动事件参数的 **verb** 属性来标识用户选择的选项。 你可以为“谓词”  设置使用任何值（但保留的 **open** 除外）。

**表 2**  

| 设置             | 值                      |
|--------------------:|----------------------------|
| Verb                | copy                       |
| 操作显示名称 | 将图片复制到库 |
| 内容事件       | ShowPicturesOnArrival      |

### <a name="step-2-add-xaml-ui"></a>步骤 2：添加 XAML UI

打开 MainPage.xaml 文件并将以下 XAML 添加到默认的 &lt;Grid&gt; 部分。

```xml
<TextBlock FontSize="18">File List</TextBlock>
<TextBlock x:Name="FilesBlock" HorizontalAlignment="Left" TextWrapping="Wrap"
           VerticalAlignment="Top" Margin="0,20,0,0" Height="280" Width="240" />
<Canvas x:Name="FilesCanvas" HorizontalAlignment="Left" VerticalAlignment="Top"
        Margin="260,20,0,0" Height="280" Width="100"/>
```

### <a name="step-3-add-initialization-code"></a>步骤 3:添加初始化代码

此步骤中的代码检查 **Verb** 属性中的 verb 值，该属性是在 **OnFileActivated** 事件期间传递给应用的启动参数之一。 代码随后调用与用户所选选项相关的方法。 对于相机内存事件，“自动播放”功能将相机存储的根文件夹传递给应用。 可以从 **Files** 属性的第一个元素检索该文件夹。

打开 App.xaml.cs 文件，然后将以下代码添加到 **App** 类。

```cs
protected override void OnFileActivated(FileActivatedEventArgs args)
{
    if (args.Verb == "show")
    {
        Frame rootFrame = (Frame)Window.Current.Content;
        MainPage page = (MainPage)rootFrame.Content;

        // Call DisplayImages with root folder from camera storage.
        page.DisplayImages((Windows.Storage.StorageFolder)args.Files[0]);
    }

    if (args.Verb == "copy")
    {
        Frame rootFrame = (Frame)Window.Current.Content;
        MainPage page = (MainPage)rootFrame.Content;

        // Call CopyImages with root folder from camera storage.
        page.CopyImages((Windows.Storage.StorageFolder)args.Files[0]);
    }

    base.OnFileActivated(args);
}
```

> **请注意**  `DisplayImages`和`CopyImages`方法添加以下步骤中。

### <a name="step-4-add-code-to-display-images"></a>步骤 4：添加代码以显示图像

在 MainPage.xaml.cs 文件中，将以下代码添加到 **MainPage** 类。

```cs
async internal void DisplayImages(Windows.Storage.StorageFolder rootFolder)
{
    // Display images from first folder in root\DCIM.
    var dcimFolder = await rootFolder.GetFolderAsync("DCIM");
    var folderList = await dcimFolder.GetFoldersAsync();
    var cameraFolder = folderList[0];
    var fileList = await cameraFolder.GetFilesAsync();
    for (int i = 0; i < fileList.Count; i++)
    {
        var file = (Windows.Storage.StorageFile)fileList[i];
        WriteMessageText(file.Name + "\n");
        DisplayImage(file, i);
    }
}

async private void DisplayImage(Windows.Storage.IStorageItem file, int index)
{
    try
    {
        var sFile = (Windows.Storage.StorageFile)file;
        Windows.Storage.Streams.IRandomAccessStream imageStream =
            await sFile.OpenAsync(Windows.Storage.FileAccessMode.Read);
        Windows.UI.Xaml.Media.Imaging.BitmapImage imageBitmap =
            new Windows.UI.Xaml.Media.Imaging.BitmapImage();
        imageBitmap.SetSource(imageStream);
        var element = new Image();
        element.Source = imageBitmap;
        element.Height = 100;
        Thickness margin = new Thickness();
        margin.Top = index * 100;
        element.Margin = margin;
        FilesCanvas.Children.Add(element);
    }
    catch (Exception e)
    {
       WriteMessageText(e.Message + "\n");
    }
}

// Write a message to MessageBlock on the UI thread.
private Windows.UI.Core.CoreDispatcher messageDispatcher = Window.Current.CoreWindow.Dispatcher;

private async void WriteMessageText(string message, bool overwrite = false)
{
    await messageDispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
        () =>
        {
            if (overwrite)
                FilesBlock.Text = message;
            else
                FilesBlock.Text += message;
        });
}
```

### <a name="step-5-add-code-to-copy-images"></a>步骤 5：添加代码以将映像复制

在 MainPage.xaml.cs 文件中，将以下代码添加到 **MainPage** 类。

```cs
async internal void CopyImages(Windows.Storage.StorageFolder rootFolder)
{
    // Copy images from first folder in root\DCIM.
    var dcimFolder = await rootFolder.GetFolderAsync("DCIM");
    var folderList = await dcimFolder.GetFoldersAsync();
    var cameraFolder = folderList[0];
    var fileList = await cameraFolder.GetFilesAsync();

    try
    {
        var folderName = "Images " + DateTime.Now.ToString("yyyy-MM-dd HHmmss");
        Windows.Storage.StorageFolder imageFolder = await
            Windows.Storage.KnownFolders.PicturesLibrary.CreateFolderAsync(folderName);

        foreach (Windows.Storage.IStorageItem file in fileList)
        {
            CopyImage(file, imageFolder);
        }
    }
    catch (Exception e)
    {
        WriteMessageText("Failed to copy images.\n" + e.Message + "\n");
    }
}

async internal void CopyImage(Windows.Storage.IStorageItem file,
                              Windows.Storage.StorageFolder imageFolder)
{
    try
    {
        Windows.Storage.StorageFile sFile = (Windows.Storage.StorageFile)file;
        await sFile.CopyAsync(imageFolder, sFile.Name);
        WriteMessageText(sFile.Name + " copied.\n");
    }
    catch (Exception e)
    {
        WriteMessageText("Failed to copy file.\n" + e.Message + "\n");
    }
}
```

### <a name="step-6-build-and-run-the-app"></a>步骤 6：生成并运行应用

1.  按 F5 生成并部署应用（在调试模式下）。
2.  若要运行应用，请将相机内存卡或相机的其他存储设备插入电脑。 然后，从自动播放选项列表中选择在你的 package.appxmanifest 文件中指定的内容事件选项之一。 此示例代码仅显示或复制相机内存卡的 DCIM 文件夹中的图片。 如果您的照相机内存卡将图片存储在 AVCHD 或专用\\ACHD 文件夹中，你将需要相应地更新代码。
    **请注意**  如果没有照相机内存卡，如果它具有名为的文件夹，则可以使用闪存驱动器**DCIM**根目录中且如果 DCIM 文件夹可以包含图像的子文件夹。

## <a name="register-for-an-autoplay-device"></a>注册自动播放设备


可以将应用注册为“自动播放”  设备事件的选项。 “自动播放”  设备事件会在设备连接到电脑时引发。

下面显示了如何将应用标识为在将相机连接到电脑时的“自动播放”  选项。 该应用将注册一个处理程序**WPD\\ImageSourceAutoPlay**事件。 当相机和其他图像设备通知事件它们为使用 MTP 的 ImageSource 时，此为 Windows Portable Device (WPD) 系统引发的常见事件。 有关详细信息，请参阅 [Windows Portable Device](https://docs.microsoft.com/previous-versions/ff597729(v=vs.85))。

**重要**   [ **Windows.Devices.Portable.StorageDevice** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Portable.StorageDevice) Api 属于[桌面设备系列](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)。 应用程序可以仅在桌面设备系列 （如电脑） 中的 Windows 10 设备上使用这些 Api。

 

### <a name="step-1-create-a-new-project-and-add-autoplay-declarations"></a>第 1 步：创建一个新项目并添加自动播放声明

1.  打开 Visual Studio，然后从“文件”  菜单中选择“新建项目”  。 在“Visual C#”  部分的“Windows”  下，选择“空白应用(通用 Windows)”  。 命名应用程序**AutoPlayDevice\_照相机**单击**确定。**
2.  打开 Package.appxmanifest 文件并选择“功能”  选项卡。选择“可移动存储”  功能。 这会使该应用能够访问作为可移动存储卷设备的相机上的数据。
3.  在清单文件中，选择“声明”  选项卡。在“可用声明”  下拉列表中，选择“自动播放设备”  ，然后单击“添加”  。 选择已添加到“支持的声明”  列表中的新“自动播放设备”  项。
4.  “自动播放设备”  声明会在“自动播放”引发已知事件的设备事件时将你的应用标识为一个选项。 在“启动操作”  部分中，为第一个启动操作输入下表中的以下值。
5.  在“可用声明”  下拉列表中，选择“文件类型关联”  ，然后单击“添加”  。 中的新属性**文件类型关联**声明中，设置**显示名称**字段**显示图像从照相机传送**和**名称**字段**照相机\_association1**。 在“支持的文件类型”  部分中，单击“新增”  （如果需要）。 将“文件类型”  字段设置为 **.jpg**。 在“支持的文件类型”  部分中，再次单击“新增”  。 将新文件关联的“文件类型”  字段设置为 **.png**。 对于内容事件，自动播放功能会筛选掉任何未与应用显式关联的文件类型。
6.  保存并关闭清单文件。

| 设置             | ReplTest1            |
|---------------------|------------------|
| Verb                | 显示             |
| 操作显示名称 | 显示图片    |
| 内容事件       | WPD\\ImageSource |

“操作显示名称”  设置标识自动播放为你的应用显示的字符串。 “谓词”  设置标识针对所选选项传递给你的应用的值。 你可以为自动播放事件指定多个启动操作并且可以使用“谓词”  设置确定用户为你的应用选择的选项。 你可以通过检查传递给应用的启动事件参数的 **verb** 属性来标识用户选择的选项。 你可以为“谓词”  设置使用任何值（但保留的 **open** 除外）。 有关在单个应用中使用多个谓词的示例，请参阅[注册自动播放内容](#register-for-autoplay-content)。

### <a name="step-2-add-assembly-reference-for-the-desktop-extensions"></a>步骤 2：添加桌面扩展的程序集引用

访问 Windows 便携设备上的存储所需的 API [**Windows.Devices.Portable.StorageDevice**](https://docs.microsoft.com/uwp/api/Windows.Devices.Portable.StorageDevice) 是[桌面设备系列](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)的一部分。 这表示使用这些 API 需要特殊的程序集，并且这些调用仅适用于桌面设备系列中的设备（例如电脑）。

1.  在“解决方案资源管理器”  中，右键单击“引用”  ，然后单击“添加引用...”  。
2.  展开“通用 Windows”  并单击“扩展”  。
3.  然后选择“适用于 UWP 的 Windows 桌面扩展”  并单击“确定”  。

### <a name="step-3-add-xaml-ui"></a>步骤 3:添加 XAML UI

打开 MainPage.xaml 文件并将以下 XAML 添加到默认的 &lt;Grid&gt; 部分。

```xml
<StackPanel Orientation="Vertical" Margin="10,0,-10,0">
    <TextBlock FontSize="24">Device Information</TextBlock>
    <StackPanel Orientation="Horizontal">
        <TextBlock x:Name="DeviceInfoTextBlock" FontSize="18" Height="400" Width="400" VerticalAlignment="Top" />
        <ListView x:Name="ImagesList" HorizontalAlignment="Left" Height="400" VerticalAlignment="Top" Width="400">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Vertical">
                        <Image Source="{Binding Path=Source}" />
                        <TextBlock Text="{Binding Path=Name}" />
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
            <ListView.ItemsPanel>
                <ItemsPanelTemplate>
                    <WrapGrid Orientation="Horizontal" ItemHeight="100" ItemWidth="120"></WrapGrid>
                </ItemsPanelTemplate>
            </ListView.ItemsPanel>
        </ListView>
    </StackPanel>
</StackPanel>
```

### <a name="step-4-add-activation-code"></a>步骤 4：添加激活代码

此步骤中的代码通过将相机的设备信息 ID 传递给 [**FromId**](https://docs.microsoft.com/uwp/api/windows.devices.portable.storagedevice.fromid) 方法来以 [**StorageDevice**](https://docs.microsoft.com/uwp/api/Windows.Devices.Portable.StorageDevice) 形式引用相机。 通过以下方法获取相机的设备信息 ID：首先将事件参数强制转换为 [**DeviceActivatedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.DeviceActivatedEventArgs)，然后从 [**DeviceInformationId**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.deviceactivatedeventargs.deviceinformationid) 属性获取值。

打开 App.xaml.cs 文件，然后将以下代码添加到 **App** 类。

```cs
protected override void OnActivated(IActivatedEventArgs args)
{
   if (args.Kind == ActivationKind.Device)
   {
      Frame rootFrame = null;
      // Ensure that the current page exists and is activated
      if (Window.Current.Content == null)
      {
         rootFrame = new Frame();
         rootFrame.Navigate(typeof(MainPage));
         Window.Current.Content = rootFrame;
      }
      else
      {
         rootFrame = Window.Current.Content as Frame;
      }
      Window.Current.Activate();

      // Make sure the necessary APIs are present on the device
      bool storageDeviceAPIPresent =
      Windows.Foundation.Metadata.ApiInformation.IsTypePresent("Windows.Devices.Portable.StorageDevice");

      if (storageDeviceAPIPresent)
      {
         // Reference the current page as type MainPage
         var mPage = rootFrame.Content as MainPage;

         // Cast the activated event args as DeviceActivatedEventArgs and show images
         var deviceArgs = args as DeviceActivatedEventArgs;
         if (deviceArgs != null)
         {
            mPage.ShowImages(Windows.Devices.Portable.StorageDevice.FromId(deviceArgs.DeviceInformationId));
         }
      }
      else
      {
         // Handle case where APIs are not present (when the device is not part of the desktop device family)
      }

   }

   base.OnActivated(args);
}
```

> **请注意**  `ShowImages`方法将添加在下一步。

### <a name="step-5-add-code-to-display-device-information"></a>步骤 5：添加代码以显示设备信息

可以从 [**StorageDevice**](https://docs.microsoft.com/uwp/api/Windows.Devices.Portable.StorageDevice) 类的属性中获取有关相机的信息。 此步骤中的代码在应用运行时，向用户显示设备名称和其他信息。 此代码随后调用 GetImageList 和 GetThumbnail 方法，这些方法将在下一步中添加，用于显示相机上所存储图像的缩略图。

在 MainPage.xaml.cs 文件中，将以下代码添加到 **MainPage** 类。

```cs
private Windows.Storage.StorageFolder rootFolder;

internal async void ShowImages(Windows.Storage.StorageFolder folder)
{
    DeviceInfoTextBlock.Text = "Display Name = " + folder.DisplayName + "\n";
    DeviceInfoTextBlock.Text += "Display Type =  " + folder.DisplayType + "\n";
    DeviceInfoTextBlock.Text += "FolderRelativeId = " + folder.FolderRelativeId + "\n";

    // Reference first folder of the device as the root
    rootFolder = (await folder.GetFoldersAsync())[0];
    var imageList = await GetImageList(rootFolder);

    foreach (Windows.Storage.StorageFile img in imageList)
    {
        ImagesList.Items.Add(await GetThumbnail(img));
    }
}
```

> **请注意**  `GetImageList`和`GetThumbnail`下一个步骤中添加方法。

### <a name="step-6-add-code-to-display-images"></a>步骤 6：添加代码以显示图像

此步骤中的代码显示相机上所存储图像的缩略图。 此代码对相机进行异步调用以获取缩略图。 但是，只有在上一个异步调用完成后，才会进行下一个异步调用。 这可确保一次仅对相机发出一个请求。

在 MainPage.xaml.cs 文件中，将以下代码添加到 **MainPage** 类。

```cs
async private System.Threading.Tasks.Task<List<Windows.Storage.StorageFile>> GetImageList(Windows.Storage.StorageFolder folder)
{
    var result = await folder.GetFilesAsync();
    var subFolders = await folder.GetFoldersAsync();
    foreach (Windows.Storage.StorageFolder f in subFolders)
        result = result.Union(await GetImageList(f)).ToList();

    return (from f in result orderby f.Name select f).ToList();
}

async private System.Threading.Tasks.Task<Image> GetThumbnail(Windows.Storage.StorageFile img)
{
    // Get the thumbnail to display
    var thumbnail = await img.GetThumbnailAsync(Windows.Storage.FileProperties.ThumbnailMode.SingleItem,
                                                100,
                                                Windows.Storage.FileProperties.ThumbnailOptions.UseCurrentScale);

    // Create a XAML Image object bind to on the display page
    var result = new Image();
    result.Height = thumbnail.OriginalHeight;
    result.Width = thumbnail.OriginalWidth;
    result.Name = img.Name;
    var imageBitmap = new Windows.UI.Xaml.Media.Imaging.BitmapImage();
    imageBitmap.SetSource(thumbnail);
    result.Source = imageBitmap;

    return result;
}
```

### <a name="step-7-build-and-run-the-app"></a>步骤 7：生成并运行应用

1.  按 F5 生成并部署应用（在调试模式下）。
2.  若要运行你的应用，请将相机连接到你的计算机。 然后从“自动播放”选项列表中选择该应用。
    **请注意**  不是所有的照相机的播发**WPD\\ImageSource**自动播放设备事件。

## <a name="configure-removable-storage"></a>配置可移动存储

当卷设备（如内存卡或指状驱动器）连接到电脑时，你可以将这些设备标识为“自动播放”  设备。 当你希望关联特定的应用以便为卷设备的用户提供“自动播放”  功能时，这尤其有用。

下面显示了如何将卷设备标识为“自动播放”  设备。

若要将卷设备标识为“自动播放”  设备，请在设备的根驱动器中添加一个 autorun.inf 文件。 在 autorun.inf 文件中，向 **AutoRun** 部分中添加一个 **CustomEvent** 键。 当卷设备连接到电脑时，“自动播放”  将查找 autorun.inf 文件并将你的卷视为一台设备。 “自动播放”  将通过使用你为 **CustomEvent** 键提供的名称创建一个“自动播放”  事件。 然后，你可以创建一个应用并将其注册为该“自动播放”  事件的处理程序。 当设备连接到电脑时，“自动播放”  功能会将该应用显示为卷设备的处理程序。 有关 autorun.inf 文件的详细信息，请参阅 [autorun.inf 条目](https://docs.microsoft.com/windows/desktop/shell/autorun-cmds)。

### <a name="step-1-create-an-autoruninf-file"></a>第 1 步：创建一个 autorun.inf 文件

在卷设备的根驱动器中，添加一个名为 autorun.inf 的文件。 打开 autorun.inf 文件并添加以下文本。

``` syntax
[AutoRun]
CustomEvent=AutoPlayCustomEventQuickstart
```

### <a name="step-2-create-a-new-project-and-add-autoplay-declarations"></a>步骤 2：创建一个新项目并添加自动播放声明

1.  打开 Visual Studio，然后从“文件”  菜单中选择“新建项目”  。 在“Visual C#”  部分的“Windows”  下，选择“空白应用(通用 Windows)”  。 将该应用程序命名为 **AutoPlayCustomEvent**，然后单击“确定”  。
2.  打开 Package.appxmanifest 文件并选择“功能”  选项卡。选择“可移动存储”  功能。 这使应用能够访问可移动存储设备上的文件和文件夹。
3.  在清单文件中，选择“声明”  选项卡。在“可用声明”  下拉列表中，选择“自动播放内容”  ，然后单击“添加”  。 选择已添加到“支持的声明”  列表中的新“自动播放内容”  项。

    **请注意**  或者，您还可以添加**自动播放设备**声明自定义的自动播放事件。  
4.  在“自动播放内容”  事件声明的“启动操作”  部分中，为第一个启动操作输入下表中的以下值。
5.  在“可用声明”  下拉列表中，选择“文件类型关联”  ，然后单击“添加”  。 中的新属性**文件类型关联**声明中，设置**显示名称**字段**显示.ms 文件**并**名称**字段**ms\_关联**。 在“支持的文件类型”  部分中，单击“新增”  。 将“文件类型”  字段设置为 **.ms**。 对于内容事件，“自动播放”功能会筛选掉任何未与应用显式关联的文件类型。
6.  保存并关闭清单文件。

| 设置             | ReplTest1                         |
|---------------------|-------------------------------|
| Verb                | 显示                          |
| 操作显示名称 | 显示文件                    |
| 内容事件       | AutoPlayCustomEventQuickstart |

“内容事件”  值是在 autorun.inf 文件中为 **CustomEvent** 键提供的文本。 “操作显示名称”  设置标识自动播放为你的应用显示的字符串。 “谓词”  设置标识针对所选选项传递给你的应用的值。 你可以为自动播放事件指定多个启动操作并且可以使用“谓词”  设置确定用户为你的应用选择的选项。 你可以通过检查传递给应用的启动事件参数的 **verb** 属性来标识用户选择的选项。 你可以为“谓词”  设置使用任何值（但保留的 **open** 除外）。

### <a name="step-3-add-xaml-ui"></a>步骤 3:添加 XAML UI

打开 MainPage.xaml 文件并将以下 XAML 添加到默认的 &lt;Grid&gt; 部分。

```xml
<StackPanel Orientation="Vertical">
    <TextBlock FontSize="28" Margin="10,0,800,0">Files</TextBlock>
    <TextBlock x:Name="FilesBlock" FontSize="22" Height="600" Margin="10,0,800,0" />
</StackPanel>
```

### <a name="step-4-add-activation-code"></a>步骤 4：添加激活代码

此步骤中的代码调用一个方法以显示卷设备的根驱动器中的文件夹。 对于“自动播放内容”事件，“自动播放”功能在执行 **OnFileActivated** 事件期间传递给应用程序的启动参数中传递存储设备的根文件夹。 可以从 **Files** 属性的第一个元素检索该文件夹。

打开 App.xaml.cs 文件，然后将以下代码添加到 **App** 类。

```cs
protected override void OnFileActivated(FileActivatedEventArgs args)
{
    var rootFrame = Window.Current.Content as Frame;
    var page = rootFrame.Content as MainPage;

    // Call ShowFolders with root folder from device storage.
    page.DisplayFiles(args.Files[0] as Windows.Storage.StorageFolder);

    base.OnFileActivated(args);
}
```

> **请注意**  `DisplayFiles`方法将添加在下一步。

 

### <a name="step-5-add-code-to-display-folders"></a>步骤 5：添加代码以显示文件夹

在 MainPage.xaml.cs 文件中，将以下代码添加到 **MainPage** 类。

```cs
internal async void DisplayFiles(Windows.Storage.StorageFolder folder)
{
    foreach (Windows.Storage.StorageFile f in await ReadFiles(folder, ".ms"))
    {
        FilesBlock.Text += "  " + f.Name + "\n";
    }
}

internal async System.Threading.Tasks.Task<IReadOnlyList<Windows.Storage.StorageFile>>
    ReadFiles(Windows.Storage.StorageFolder folder, string fileExtension)
{
    var options = new Windows.Storage.Search.QueryOptions();
    options.FileTypeFilter.Add(fileExtension);
    var query = folder.CreateFileQueryWithOptions(options);
    var files = await query.GetFilesAsync();

    return files;
}
```

### <a name="step-6-build-and-run-the-qpp"></a>步骤 6：生成并运行 qpp

1.  按 F5 生成并部署应用（在调试模式下）。
2.  若要运行应用，请将内存卡或其他存储设备插入电脑。 然后从“自动播放”处理程序选项的列表中选择你的应用。

## <a name="autoplay-event-reference"></a>自动播放事件参考


使用“自动播放”  系统，应用可以注册各种设备和卷（磁盘）到达事件。 若要注册“自动播放”  内容事件，则必须在程序包清单中启用“可移动存储”  功能。 此表显示了可以注册的事件及其引发时间。

| 应用场景                                                           | Event                              | 描述   |
|--------------------------------------------------------------------|------------------------------------|---------------|
| 使用相机上的照片                                           | **WPD\ImageSource**                | 针对标识为 Windows Portable Devices 且提供 ImageSource 功能的相机引发。 |
| 使用自动播放器上的音乐                                     | **WPD\AudioSource**                | 针对标识为 Windows Portable Devices 且提供 AudioSource 功能的媒体播放器引发。 |
| 使用摄像机上的视频                                     | **WPD\VideoSource**                | 针对标识为 Windows Portable Devices 且提供 VideoSource 功能的摄像机引发。 |
| 访问所连接的闪存驱动器或外部硬盘驱动器              | **StorageOnArrival**               | 在驱动器或卷连接到电脑时引发。   如果驱动器或卷的磁盘根目录中包含 DCIM、AVCHD 或 PRIVATE\ACHD 文件夹，则会改为引发 **ShowPicturesOnArrival** 事件。 |
| 使用大容量存储（旧功能）中的照片                            | **ShowPicturesOnArrival**          | 当驱动器或卷的磁盘根目录中包含 DCIM、AVCHD 或 PRIVATE\ACHD 文件夹时引发。 如果用户已启用自动播放控制面板中的**为每种媒体类型选择相应的操作**，则自动播放会检查连接到电脑的卷以确定磁盘中内容的类型。 找到图片时，将引发 **ShowPicturesOnArrival**。 |
| 使用邻近感应共享（点击并发送）接收照片             | **ShowPicturesOnArrival**          | 当用户使用邻近感应（点击并发送）发送内容时，自动播放会检查共享文件以确定内容的类型。 如果找到图片，则会引发 **ShowPicturesOnArrival**。 |
| 使用大容量存储（旧功能）中的音乐                             | **PlayMusicFilesOnArrival**        | 如果用户已启用自动播放控制面板中的“为每种媒体类型选择相应的操作”  ，则自动播放会检查连接到电脑的卷以确定磁盘中内容的类型。  找到音乐文件时，将引发 **PlayMusicFilesOnArrival**。 |
| 使用邻近感应共享（点击并发送）接收音乐              | **PlayMusicFilesOnArrival**        | 当用户使用邻近感应（点击并发送）发送内容时，自动播放会检查共享文件以确定内容的类型。 如果找到音乐文件，则会引发 **PlayMusicFilesOnArrival**。 |
| 使用大容量存储（旧功能）中的视频                            | **PlayVideoFilesOnArrival**        | 如果用户已启用自动播放控制面板中的“为每种媒体类型选择相应的操作”  ，则自动播放会检查连接到电脑的卷以确定磁盘中内容的类型。 找到视频文件时，将引发 **PlayVideoFilesOnArrival**。 |
| 使用邻近感应共享（点击并发送）接收视频             | **PlayVideoFilesOnArrival**        | 当用户使用邻近感应（点击并发送）发送内容时，自动播放会检查共享文件以确定内容的类型。 如果找到视频文件，则会引发 **PlayVideoFilesOnArrival**。 |
| 处理所连接设备中的混合文件集               | **MixedContentOnArrival**          | 如果用户已启用自动播放控制面板中的“为每种媒体类型选择相应的操作”  ，则自动播放会检查连接到电脑的卷以确定磁盘中内容的类型。 如果未找到特定的内容类型（例如，图片），则会引发 **MixedContentOnArrival**。 |
| 使用邻近感应共享（点击并发送）处理混合文件集 | **MixedContentOnArrival**          | 当用户使用邻近感应（点击并发送）发送内容时，自动播放会检查共享文件以确定内容的类型。 如果未找到特定的内容类型（例如，图片），则会引发 **MixedContentOnArrival**。 |
| 处理光学媒体上的视频                                    | **PlayDVDMovieOnArrival**<br />**PlayBluRayOnArrival**<br />**PlayVideoCDMovieOnArrival**<br />**PlaySuperVideoCDMovieOnArrival** | 将光盘插入光驱时，自动播放将检查文件以确定内容的类型。 发现视频文件时，会引发与光盘类型相对应的事件。 |
| 处理光学媒体上的音乐                                    | **PlayCDAudioOnArrival**<br />**PlayDVDAudioOnArrival** | 将光盘插入光驱时，自动播放将检查文件以确定内容的类型。 发现音乐文件时，会引发与光盘类型相对应的事件。 |
| 播放增强磁盘                                                | **PlayEnhancedCDOnArrival**<br />**PlayEnhancedDVDOnArrival** | 将光盘插入光驱时，自动播放将检查文件以确定内容的类型。 发现增强磁盘时，会引发与光盘类型相对应的事件。 |
| 处理可写入的光盘                                     | **HandleCDBurningOnArrival** <br />**HandleDVDBurningOnArrival** <br />**HandleBDBurningOnArrival** | 将光盘插入光驱时，自动播放将检查文件以确定内容的类型。 发现可写磁盘时，会引发与光盘类型相对应的事件。 |
| 处理任何其他设备或卷连接                       | **UnknownContentOnArrival**        | 在找到与任何“自动播放内容”事件都不匹配的内容时，会针对所有事件引发。 不建议使用此事件。 只应当针对你的应用可以处理的特定“自动播放”事件注册你的应用程序。 |

你可以指定“自动播放”使用 autorun.inf 文件中的 **CustomEvent** 条目来为卷引发自定义的“自动播放内容”事件。 有关详细信息，请参阅 [Autorun.inf 条目](https://docs.microsoft.com/windows/desktop/shell/autorun-cmds)。

你可以通过向应用的 package.appxmanifest 文件添加扩展，将应用注册为“自动播放内容”或“自动播放设备”事件处理程序。 如果你使用的是 Visual Studio，则可在“声明”  选项卡中添加“自动播放内容”  或“自动播放设备”  声明。如果你要直接编辑应用的 package.appxmanifest 文件，则将 [**Extension**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-1-extension) 元素添加到程序包清单，以将 **windows.autoPlayContent** 或 **windows.autoPlayDevice** 指定为 **Category**。 例如，程序包清单中的以下条目添加“自动播放内容”  扩展，以将应用注册为 **ShowPicturesOnArrival** 事件的处理程序。

```xml
  <Applications>
    <Application Id="AutoPlayHandlerSample.App">
      <Extensions>
        <Extension Category="windows.autoPlayContent">
          <AutoPlayContent>
            <LaunchAction Verb="show" ActionDisplayName="Show Pictures"
                          ContentEvent="ShowPicturesOnArrival" />
          </AutoPlayContent>
        </Extension>
      </Extensions>
    </Application>
  </Applications>
```

 

 
