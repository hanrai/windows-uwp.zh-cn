---
title: 创建和使用应用服务
description: 了解如何编写可以向其他 UWP 应用提供服务的通用 Windows 平台 (UWP) 应用，以及如何使用这些服务。
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
keywords: 应用的通信，进程间通信，IPC、 消息传送，后台通信，应用到应用，应用服务的背景
ms.date: 01/16/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: d122a51c53fc7eb32ab79f6decc570238af22973
ms.sourcegitcommit: 51d884c3646ba3595c016e95bbfedb7ecd668a88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67821046"
---
# <a name="create-and-consume-an-app-service"></a>创建和使用应用服务

应用服务是可向其他 UWP 应用提供服务的 UWP 应用。 它们与设备上的 Web 服务类似。 应用服务作为后台任务在主机应用中运行，并可向其他应用提供其服务。 例如，应用服务可能会提供其他应用可能使用的条形码扫描仪服务。 应用的企业套件中可能有一个通用的拼写检查应用服务，该服务可供套件中的其他应用使用。  应用服务允许你创建应用可在同一设备上调用的无 UI 服务，从 Windows 10 版本 1607 开始，应用可在远程设备上调用这些服务。

从 Windows 10 版本 1607 开始，可以创建在与主机应用相同的进程中运行的应用服务。 本文主要介绍如何在单独后台进程中创建和使用应用服务。 有关在提供程序所在的同一进程中运行应用服务的更多详细信息，请参阅[将应用服务转换为在其托管应用所在的同一进程中运行的服务](convert-app-service-in-process.md)。

有关应用服务代码示例，请参阅[通用 Windows 平台 (UWP) 应用示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)。

## <a name="create-a-new-app-service-provider-project"></a>创建新的应用服务提供程序项目

在本操作方法中，我们将创建一个适用于简单解决方案的所有内容。

1. 在 Visual Studio 2015 或更高版本，创建新的 UWP 应用项目并将其命名**AppServiceProvider**。
    1. 选择**文件 > 新建 > 项目...** 
    2. 在中**创建一个新项目**对话框中，选择**空白应用 (通用 Windows) C#** 。 这将是能够向其他 UWP 应用提供应用服务的应用。
    3. 单击**下一步**，然后命名项目**AppServiceProvider**，为其选择一个位置，然后单击**创建**。

2. 当系统询问是否选择**目标**并**最低版本**对于项目，请选择至少**10.0.14393**。 如果你想要使用的新**SupportsMultipleInstances**属性，则必须使用 Visual Studio 2017 或 Visual Studio 2019 和目标**10.0.15063** (**Windows 10 创意者更新**)或更高版本。

<span id="appxmanifest"/>

## <a name="add-an-app-service-extension-to-packageappxmanifest"></a>将应用服务扩展添加到 Package.appxmanifest

在中**AppServiceProvider**项目中，打开**Package.appxmanifest**文本编辑器中的文件： 

1. 右键单击该中**解决方案资源管理器**。 
2. 选择**使用打开**。 
3. 选择**XML （文本） 编辑器**。 

添加以下`AppService`内的扩展`<Application>`元素。 此示例介绍了 `com.microsoft.inventory` 服务，以及将此应用识别为应用服务提供程序的内容。 实际服务将作为后台任务来实现。 应用服务项目将该服务公开给其他应用。 我们建议将反向域名样式用于服务名称。

请注意，只有面向 Windows SDK 版本 10.0.15063 或更高版本，`xmlns:uap4` 命名空间前缀和 `uap4:SupportsMultipleInstances` 属性才有效。 如果面向较旧的 SDK 版本，则可以放心地删除它们。

``` xml
<Package
    ...
    xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    ...
    <Applications>
        <Application Id="AppServiceProvider.App"
          Executable="$targetnametoken$.exe"
          EntryPoint="AppServiceProvider.App">
          ...
          <Extensions>
            <uap:Extension Category="windows.appService" EntryPoint="MyAppService.Inventory">
              <uap3:AppService Name="com.microsoft.inventory" uap4:SupportsMultipleInstances="true"/>
            </uap:Extension>
          </Extensions>
          ...
        </Application>
    </Applications>
```

`Category`属性标识该应用程序作为应用服务提供程序。

`EntryPoint`属性标识实现的服务，接下来，我们将实现的命名空间限定的类。

`SupportsMultipleInstances`属性表示每次调用应用服务时，它应运行在新的进程中。 这不是必需的但如果需要这种功能，并以 10.0.15063 为目标，则可供你 SDK (**Windows 10 创意者更新**) 或更高版本。 还应该对其加上 `uap4` 命名空间前缀。

## <a name="create-the-app-service"></a>创建应用服务

1.  应用服务可作为后台任务实现。 这允许前台应用程序调用另一个应用程序中的应用服务。 若要创建应用服务作为后台任务，向解决方案添加新的 Windows 运行时组件项目 (**文件&gt;添加&gt;新项目**) 名为**MyAppService**。 在中**添加新项目**对话框框中，选择**已安装 > Visual C# > Windows 运行时组件 (通用 Windows)** 。
2.  在中**AppServiceProvider**项目中，添加对新的项目到项目引用**MyAppService**项目 (在**解决方案资源管理器**，右键单击**AppServiceProvider**项目 >**添加** > **引用** > **项目** >  **解决方案**，选择**MyAppService** > **确定**)。 此步骤至关重要，因为如果不添加引用，应用服务将不会在运行时进行连接。
3.  在中**MyAppService**项目中，添加以下**使用**到顶部的语句**Class1.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    using Windows.ApplicationModel.Background;
    using Windows.Foundation.Collections;
    ```

4.  重命名**Class1.cs**到**Inventory.cs**，并为该存根 （stub） 代码替换**Class1**新的后台任务类具有名为**清单**:

    ```cs
    public sealed class Inventory : IBackgroundTask
    {
        private BackgroundTaskDeferral backgroundTaskDeferral;
        private AppServiceConnection appServiceconnection;
        private String[] inventoryItems = new string[] { "Robot vacuum", "Chair" };
        private double[] inventoryPrices = new double[] { 129.99, 88.99 };

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Get a deferral so that the service isn't terminated.
            this.backgroundTaskDeferral = taskInstance.GetDeferral();

            // Associate a cancellation handler with the background task.
            taskInstance.Canceled += OnTaskCanceled;

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // This function is called when the app service receives a request.
        }

        private void OnTaskCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
        {
            if (this.backgroundTaskDeferral != null)
            {
                // Complete the service deferral.
                this.backgroundTaskDeferral.Complete();
            }
        }
    }
    ```

    此类位于应用服务将起作用的位置。

    **运行**创建后台任务时调用。 由于 **Run** 完成后会终止后台任务，因此代码会执行延迟，以便后台任务可以继续服务请求。 约 30 秒，它接收调用，除非它再次调用该时间范围内或从延迟后，作为后台任务实现的应用服务将始终处于活动状态。如果在调用方与相同的进程中实施的应用服务，则应用服务的生存期取决于调用方的生存期。

    应用服务的生命周期取决于调用方：

    * 如果调用方位于前台时，应用程序服务生存期等同于调用方。
    * 如果调用方是在后台，应用服务将获取运行 30 秒。 执行延迟可一次多提供 5 秒。

    **OnTaskCanceled**任务被取消时调用。 任务被取消时客户端应用程序释放[AppServiceConnection](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection)、 客户端应用已挂起、 操作系统关闭的情况下或休眠，或者 OS 资源以运行此任务用尽。

## <a name="write-the-code-for-the-app-service"></a>编写应用服务的代码

**OnRequestReceived**是应用服务的代码的位置。 替换为存根**OnRequestReceived**中**MyAppService**的**Inventory.cs**与此示例中的代码。 此代码获取库存项目的索引，并将该索引以及命令字符串传递到服务以检索指定库存项目的名称和价格。 对于你自己的项目，请添加错误处理代码。

```cs
private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
{
    // Get a deferral because we use an awaitable API below to respond to the message
    // and we don't want this call to get canceled while we are waiting.
    var messageDeferral = args.GetDeferral();

    ValueSet message = args.Request.Message;
    ValueSet returnData = new ValueSet();

    string command = message["Command"] as string;
    int? inventoryIndex = message["ID"] as int?;

    if (inventoryIndex.HasValue &&
        inventoryIndex.Value >= 0 &&
        inventoryIndex.Value < inventoryItems.GetLength(0))
    {
        switch (command)
        {
            case "Price":
            {
                returnData.Add("Result", inventoryPrices[inventoryIndex.Value]);
                returnData.Add("Status", "OK");
                break;
            }

            case "Item":
            {
                returnData.Add("Result", inventoryItems[inventoryIndex.Value]);
                returnData.Add("Status", "OK");
                break;
            }

            default:
            {
                returnData.Add("Status", "Fail: unknown command");
                break;
            }
        }
    }
    else
    {
        returnData.Add("Status", "Fail: Index out of range");
    }

    try
    {
        // Return the data to the caller.
        await args.Request.SendResponseAsync(returnData);
    }
    catch (Exception e)
    {
        // Your exception handling code here.
    }
    finally
    {
        // Complete the deferral so that the platform knows that we're done responding to the app service call.
        // Note for error handling: this must be called even if SendResponseAsync() throws an exception.
        messageDeferral.Complete();
    }
}
```

请注意， **OnRequestReceived**是**异步**因为我们创建一个可等待方法调用[SendResponseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync)在此示例中。

延迟执行，以便服务可以使用**异步**中的方法**OnRequestReceived**处理程序。 它可确保对 **OnRequestReceived** 的调用不会在完成处理消息之前结束。  [SendResponseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync)将结果发送给调用方。 **SendResponseAsync** 不会在调用完成时发出信号。 它是向发出信号，延迟完成[SendMessageAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.sendmessageasync)的**OnRequestReceived**已完成。 在调用**SendResponseAsync**都包装在 try/finally 块中，因为必须完成延迟即使**SendResponseAsync**将引发异常。

应用服务使用[ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet)要交换信息的对象。 可以传递的数据大小仅受限于系统资源。 没有你可以在 **ValueSet** 中使用的预定义项。 你必须确定哪些项值将用于定义你的应用服务的协议。 请牢记，必须使用该协议编写调用方。 在此示例中，我们选择了名为 `Command` 的项，它具有一个值，用于指示我们是否希望应用服务提供库存项目的名称或其价格。 库存名称的索引存储在 `ID` 项下。 返回值存储在 `Result` 项下。

[AppServiceClosedStatus](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceClosedStatus)枚举返回给调用方以指示对应用服务的调用是成功还是失败。 对应用服务的调用可能失败的原因示例：操作系统中止服务端点，因为资源已耗尽。 可以通过 [ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) 返回其他错误信息。 在此示例中，我们使用名为 `Status` 的项将更详细的错误信息返回给调用方。

对 [SendResponseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync) 的调用将 [ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) 返回给调用方。

## <a name="deploy-the-service-app-and-get-the-package-family-name"></a>部署服务应用并获取程序包系列名称

从客户端调用它之前，必须部署应用服务提供程序。 可以将其部署通过选择**生成 > 部署解决方案**Visual Studio 中。

此外需要调用它的应用服务提供程序的包系列名称。 可以通过打开获得**AppServiceProvider**项目的**Package.appxmanifest**设计器视图中的文件 (中双击该**解决方案资源管理器**)。 选择**打包**选项卡上，复制值旁边**包系列名称**，并将其粘贴某处诸如记事本这样现在。

## <a name="write-a-client-to-call-the-app-service"></a>编写客户端以调用应用服务

1.  将新的空白 Windows 通用应用项目添加到解决方案（**文件 &gt; 添加 &gt; 新建项目**）。 在中**添加新项目**对话框框中，选择**已安装 > Visual C# > 空白应用 (通用 Windows)** 并将其命名**ClientApp**。

2.  在中**ClientApp**项目中，添加以下**使用**到顶部的语句**MainPage.xaml.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    ```

3.  添加名为文本框**textBox**和一个按钮**MainPage.xaml**。

4.  添加一个按钮单击处理程序调用该按钮**button_Click**，并添加关键字**异步**向按钮处理程序的签名。

5. 将按钮单击处理程序内的存根区域替换为以下代码。 请确保包含 `inventoryService` 字段声明。
    ```cs
   private AppServiceConnection inventoryService;

   private async void button_Click(object sender, RoutedEventArgs e)
   {
       // Add the connection.
       if (this.inventoryService == null)
       {
           this.inventoryService = new AppServiceConnection();

           // Here, we use the app service name defined in the app service 
           // provider's Package.appxmanifest file in the <Extension> section.
           this.inventoryService.AppServiceName = "com.microsoft.inventory";

           // Use Windows.ApplicationModel.Package.Current.Id.FamilyName 
           // within the app service provider to get this value.
           this.inventoryService.PackageFamilyName = "Replace with the package family name";

           var status = await this.inventoryService.OpenAsync();

           if (status != AppServiceConnectionStatus.Success)
           {
               textBox.Text= "Failed to connect";
               this.inventoryService = null;
               return;
           }
       }

       // Call the service.
       int idx = int.Parse(textBox.Text);
       var message = new ValueSet();
       message.Add("Command", "Item");
       message.Add("ID", idx);
       AppServiceResponse response = await this.inventoryService.SendMessageAsync(message);
       string result = "";

       if (response.Status == AppServiceResponseStatus.Success)
       {
           // Get the data  that the service sent to us.
           if (response.Message["Status"] as string == "OK")
           {
               result = response.Message["Result"] as string;
           }
       }

       message.Clear();
       message.Add("Command", "Price");
       message.Add("ID", idx);
       response = await this.inventoryService.SendMessageAsync(message);

       if (response.Status == AppServiceResponseStatus.Success)
       {
           // Get the data that the service sent to us.
           if (response.Message["Status"] as string == "OK")
           {
               result += " : Price = " + response.Message["Result"] as string;
           }
       }

       textBox.Text = result;
   }
   ```
    
    使用在[部署服务应用并获取包系列名称](#deploy-the-service-app-and-get-the-package-family-name)中上面获取的 **AppServiceProvider** 项目的包系列名称来替换行 `this.inventoryService.PackageFamilyName = "Replace with the package family name";` 中的包系列名称。

    > [!NOTE]
    > 请确保将粘贴字符串文本，而不是将其放在变量中。 不起如果使用的变量。

    该代码首先建立了与应用服务的连接。 该连接将保持打开状态，直到你释放 `this.inventoryService`。 应用服务名称必须与匹配`AppService`元素的`Name`属性添加到**AppServiceProvider**项目的**Package.appxmanifest**文件。 在此示例中为 `<uap3:AppService Name="com.microsoft.inventory"/>`。

    一个[ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet)名为`message`创建来指定我们想要将发送到应用服务的命令。 示例应用服务需要命令指示要采取两种操作中的哪一种操作。 我们从客户端应用程序中，在文本框中获取其索引，然后调用该服务与`Item`命令来获取项的说明。 然后，我们使用 `Price` 命令进行调用，以获取项目的价格。 按钮文本设置为结果。

    因为[AppServiceResponseStatus](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceResponseStatus)仅指示操作系统是否能够连接到应用服务的调用，我们检查`Status`中的键[ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet)我们从应用接收若要确保它已能够完成请求的服务。

6. 设置**ClientApp**项目为启动项目 (右键单击该中**解决方案资源管理器** > **设为启动项目**) 并运行解决方案。 在文本框中输入数字 1 并单击该按钮。 应获取"椅子：价格 = 88.99"返回的服务。

    ![显示 chair price=88.99 的示例应用](images/appserviceclientapp.png)

如果应用程序服务调用失败，以下签入**ClientApp**项目：

1.  验证是否分配给清单服务连接的包系列名称匹配的包系列名称**AppServiceProvider**应用。 请参阅中的行**按钮\_单击**与`this.inventoryService.PackageFamilyName = "...";`。
2.  在中**按钮\_单击**，验证是否分配给清单服务连接的应用服务名称与中的应用服务名称匹配**AppServiceProvider**的**Package.appxmanifest**文件。 请参阅 `this.inventoryService.AppServiceName = "com.microsoft.inventory";`。
3.  絋粄**AppServiceProvider**部署应用。 (在**解决方案资源管理器**，右键单击该解决方案，然后选择**部署解决方案**)。

## <a name="debug-the-app-service"></a>调试应用服务

1.  确保调试之前已部署解决方案，因为必须先部署应用服务提供程序应用，才能调用服务。 （在 Visual Studio 中，**生成&gt;部署解决方案**）。
2.  在中**解决方案资源管理器**，右键单击**AppServiceProvider**项目，然后选择**属性**。 从**调试**选项卡中，将**开始操作**更改为**不启动，但在启动时调试代码**。 （请注意，如果你以前使用 C++ 来实现应用服务提供程序，则应从**调试**选项卡中将**启动应用程序**更改为**否**）。
3.  在中**MyAppService**项目，在**Inventory.cs**文件中中, 设置断点**OnRequestReceived**。
4.  设置**AppServiceProvider**项目为启动项目，并按**F5**。
5.  启动**ClientApp**从开始菜单 （而不是从 Visual Studio)。
6.  在文本框中输入数字 1 并按该按钮。 调试程序将停止应用服务中的断点上的应用服务调用。

## <a name="debug-the-client"></a>调试客户端

1.  按照前面步骤中的说明来调试调用应用服务的客户端。
2.  启动**ClientApp**从开始菜单。
3.  附加到调试器**ClientApp.exe**过程 (不**ApplicationFrameHost.exe**过程)。 （在 Visual Studio 中，依次选择**调试&gt;附加到进程...** 。）
4.  在中**ClientApp**项目中中, 设置断点**按钮\_单击**。
5.  输入数字 1 到文本框中的时，现在会命中客户端和应用服务中的断点**ClientApp** ，然后单击按钮。

## <a name="general-app-service-troubleshooting"></a>常规应用服务疑难解答

如果遇到**AppUnavailable**后尝试连接到应用服务的状态检查以下各项：

- 确保部署了应用服务提供程序项目和应用服务项目。 二者都需要在运行客户端之前进行部署，否则客户端将没有可连接到的任何对象。 你可以使用**版本** > **部署解决方案**从 Visual Studio 中部署。
- 在中**解决方案资源管理器**，请确保你的应用服务提供程序项目具有对实现应用服务的项目的项目到项目引用。
- 确认`<Extensions>`条目，并在其子元素添加到**Package.appxmanifest**属于应用程序服务提供程序项目，在上面指定的文件[添加到应用服务扩展Package.appxmanifest](#appxmanifest)。
- 絋粄[AppServiceConnection.AppServiceName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.appservicename)调用应用服务提供程序在客户端中的字符串匹配`<uap3:AppService Name="..." />`应用服务提供程序项目中指定**Package.appxmanifest**文件。
- 絋粄[AppServiceConnection.PackageFamilyName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.packagefamilyname)在如上所示的应用服务提供程序组件的包系列名称匹配[将应用服务扩展添加到 Package.appxmanifest](#appxmanifest)
- 对于如在此示例中的进程外的应用服务，验证`EntryPoint`中指定`<uap:Extension ...>`在应用服务提供程序项目的元素**Package.appxmanifest**文件匹配其命名空间和实现的公共类的类名[IBackgroundTask](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask)在应用服务项目中。

### <a name="troubleshoot-debugging"></a>调试疑难解答

如果调试程序在应用服务提供程序或应用服务项目中的断点处未停止，请检查以下各项：

- 确保部署了应用服务提供程序项目和应用服务项目。 二者都需要在运行客户端之前进行部署。 你可以使用**版本** > **部署解决方案**从 Visual Studio 中部署它们。
- 确保你想要调试的项目被设置为启动项目，该项目的调试属性设置不运行该项目时**F5**按下。 右键单击项目，然后依次单击**属性**和**调试**（或者在 C++ 中单击**调试**）。 在 C# 中，将**开始操作**更改为**不启动，但在启动时调试代码**。 在 C++ 中，将**启动应用程序**设置为**否**。

## <a name="remarks"></a>备注

本示例介绍了创建一个作为后台任务运行的应用服务并从另一个应用调用它的情形。 需要注意的重要事项是：

* 创建用于托管应用服务的后台任务。
* 添加`windows.appService`到应用服务提供程序的扩展**Package.appxmanifest**文件。
* 获取应用服务提供程序的包系列名称，以便我们可以从客户端应用程序连接到它。
* 从应用服务提供程序项目的项目到项目引用添加到应用程序服务项目。
* 使用[Windows.ApplicationModel.AppService.AppServiceConnection](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection)来调用服务。

## <a name="full-code-for-myappservice"></a>MyAppService 的完整代码

```cs
using System;
using Windows.ApplicationModel.AppService;
using Windows.ApplicationModel.Background;
using Windows.Foundation.Collections;

namespace MyAppService
{
    public sealed class Inventory : IBackgroundTask
    {
        private BackgroundTaskDeferral backgroundTaskDeferral;
        private AppServiceConnection appServiceconnection;
        private String[] inventoryItems = new string[] { "Robot vacuum", "Chair" };
        private double[] inventoryPrices = new double[] { 129.99, 88.99 };

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Get a deferral so that the service isn't terminated.
            this.backgroundTaskDeferral = taskInstance.GetDeferral();

            // Associate a cancellation handler with the background task.
            taskInstance.Canceled += OnTaskCanceled;

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // Get a deferral because we use an awaitable API below to respond to the message
            // and we don't want this call to get canceled while we are waiting.
            var messageDeferral = args.GetDeferral();

            ValueSet message = args.Request.Message;
            ValueSet returnData = new ValueSet();

            string command = message["Command"] as string;
            int? inventoryIndex = message["ID"] as int?;

            if (inventoryIndex.HasValue &&
                 inventoryIndex.Value >= 0 &&
                 inventoryIndex.Value < inventoryItems.GetLength(0))
            {
                switch (command)
                {
                    case "Price":
                        {
                            returnData.Add("Result", inventoryPrices[inventoryIndex.Value]);
                            returnData.Add("Status", "OK");
                            break;
                        }

                    case "Item":
                        {
                            returnData.Add("Result", inventoryItems[inventoryIndex.Value]);
                            returnData.Add("Status", "OK");
                            break;
                        }

                    default:
                        {
                            returnData.Add("Status", "Fail: unknown command");
                            break;
                        }
                }
            }
            else
            {
                returnData.Add("Status", "Fail: Index out of range");
            }

            // Return the data to the caller.
            await args.Request.SendResponseAsync(returnData);

            // Complete the deferral so that the platform knows that we're done responding to the app service call.
            // Note for error handling: this must be called even if SendResponseAsync() throws an exception.
            messageDeferral.Complete();
        }


        private void OnTaskCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
        {
            if (this.backgroundTaskDeferral != null)
            {
                // Complete the service deferral.
                this.backgroundTaskDeferral.Complete();
            }
        }
    }
}
```

## <a name="related-topics"></a>相关主题

* [将应用服务转换为与其主机应用在同一个进程中运行](convert-app-service-in-process.md)
* [支持使用后台任务对应用程序](support-your-app-with-background-tasks.md)
* [应用服务的代码示例 (C#， C++，和 VB)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)
