---
ms.assetid: 7CC11888-8DC6-4FEE-ACED-9FA476B2125E
description: 使用 Microsoft Store 提交 API 以编程方式创建和管理到合作伙伴中心帐户注册的应用的提交。
title: 创建和管理提交
ms.date: 06/04/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API
ms.localizationpriority: medium
ms.openlocfilehash: 0a926b9383231e7cec9dc168afe8d0a0b34136a2
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318598"
---
# <a name="create-and-manage-submissions"></a>创建和管理提交

使用*Microsoft Store 提交 API*以编程方式查询和创建的应用程序、 外接程序和包提交航班您或您组织的合作伙伴中心帐户。 如果你的帐户管理多个应用或加载项，并且想要自动执行并优化这些资源的提交过程，此 API 非常有用。 此 API 使用 Azure Active Directory (Azure AD) 验证来自应用或服务的调用。

以下步骤介绍了使用 Microsoft Store 提交 API 的端到端过程：

1.  确保已完成所有[先决条件](#prerequisites)。
3.  在 Microsoft Store 提交 API 中调用某个方法之前，请先[获取 Azure AD 访问令牌](#obtain-an-azure-ad-access-token)。 获取令牌后，可以在 60 分钟的令牌有效期内，使用该令牌调用 Microsoft Store 提交 API。 该令牌到期后，可以重新生成一个。
4.  [调用 Microsoft Store 提交 API](#call-the-windows-store-submission-api)。

<span id="not_supported" />

> [!IMPORTANT]
> 如果使用此 API 来创建提交的应用程序、 包航班或外接程序，请务必对提交进行进一步的更改，只能通过使用 API，而不是在合作伙伴中心。 如果您使用合作伙伴中心更改最初通过使用 API 创建的提交，将不能更改，或者通过 API 提交的提交。 在某些情况下，在提交过程中无法继续进行时，提交可能会处于错误状态。 如果发生这种情况，你必须删除提交并创建新的提交。

> [!IMPORTANT]
> 你无法使用此 API 来直接向企业发布[通过适用于企业的 Microsoft Store 和适用于教育的 Microsoft Store 批量购买](../publish/organizational-licensing.md)的提交或发布 [LOB 应用](../publish/distribute-lob-apps-to-enterprises.md)的提交。 对于这两个方案，必须使用在合作伙伴中心发布的提交。

> [!NOTE]
> 此 API 不能用于使用必需的应用更新和 Microsoft Store 管理的可消费加载项的应用或加载项。 如果将 Microsoft Store 提交 API 用于使用以下功能之一的应用或加载项，该 API 会返回错误代码 409。 在这种情况下，必须使用合作伙伴中心来管理应用程序或外接程序的提交。

<span id="prerequisites" />

## <a name="step-1-complete-prerequisites-for-using-the-microsoft-store-submission-api"></a>第 1 步：完成使用 Microsoft Store 提交 API 的先决条件

在开始编写调用 Microsoft Store 提交 API 的代码之前，确保已满足以下先决条件。

* 你（或你的组织）必须具有 Azure AD 目录，并且你必须具有该目录的[全局管理员](https://go.microsoft.com/fwlink/?LinkId=746654)权限。 如果你已使用 Office 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。 否则，你可以免费[在合作伙伴中心中创建新的 Azure AD](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)。

* 您必须[将 Azure AD 应用程序与你的合作伙伴中心帐户相关联](#associate-an-azure-ad-application-with-your-windows-partner-center-account)并获取你的租户 ID、 客户端 ID 和密钥。 获取 Azure AD 访问令牌（该令牌用于调用 Microsoft Store 提交 API）需要这些值。

* 使用 Microsoft Store 提交 API 对应用进行准备：

  * 如果在合作伙伴中心尚不存在您的应用程序，则必须[保留在合作伙伴中心名称创建您的应用程序](https://docs.microsoft.com/windows/uwp/publish/create-your-app-by-reserving-a-name)。 不能使用 Microsoft Store 提交 API 在合作伙伴中心; 创建应用必须在合作伙伴中心来创建它，工作，然后之后，您可以使用 API 来访问该应用程序和以编程方式创建它的提交。 不过，可以使用该 API 以编程方式创建加载项和软件包外部测试版，然后再为它们创建提交。

  * 可以创建使用此 API 为给定应用提交之前，你必须首先[在合作伙伴中心中创建一个应用程序提交](https://docs.microsoft.com/windows/uwp/publish/app-submissions)，其中包括回答[年龄分级](https://docs.microsoft.com/windows/uwp/publish/age-ratings)调查表。 完成此操作后，才可以使用该 API 为此应用以编程方式创建新的提交。 无需创建加载项提交或软件包外部测试版提交，即可将该 API 用于这些类型的提交。

  * 如果你要创建或更新应用提交并需要包括应用包，请事先[准备应用包](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements)。

  * 如果你要创建或更新应用提交并需要包括应用商店一览的屏幕截图或图像，请事先[准备应用屏幕截图和图像](https://docs.microsoft.com/windows/uwp/publish/app-screenshots-and-images)。

  * 如果你要创建或更新加载项提交并需要包括图标，请事先[准备图标](https://docs.microsoft.com/windows/uwp/publish/create-iap-descriptions)。

<span id="associate-an-azure-ad-application-with-your-windows-partner-center-account" />

### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何将 Azure AD 应用程序与你的合作伙伴中心帐户相关联

可以使用 Microsoft Store 提交 API 之前，必须将 Azure AD 应用程序与你的合作伙伴中心帐户相关联、 检索租户 ID 和应用程序的客户端 ID 和生成密钥。 Azure AD 应用程序是指你想要从中调用 Microsoft Store 提交 API 的应用或服务。 需要租户 ID、客户端 ID 和密钥，才可以获取将传递给 API 的 Azure AD 访问令牌。

> [!NOTE]
> 你只需执行一次此任务。 获取租户 ID、客户端 ID 和密钥后，当你需要创建新的 Azure AD 访问令牌时，可以随时重复使用它们。

1.  在合作伙伴中心[将组织的合作伙伴中心帐户与你组织的 Azure AD 目录相关联](../publish/associate-azure-ad-with-partner-center.md)。

2.  接下来，从**用户**页面**帐户设置**合作伙伴中心部分[添加 Azure AD 应用程序](../publish/add-users-groups-and-azure-ad-applications.md#add-azure-ad-applications-to-your-partner-center-account)，表示应用或服务，你将使用访问合作伙伴中心帐户提交。 请确保为此应用程序分配**管理员**角色。 如果不存在该应用程序尚未在 Azure AD 目录，你可以[创建一个新 Azure AD 应用程序在合作伙伴中心](../publish/add-users-groups-and-azure-ad-applications.md#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。  

3.  返回到**用户**页面、单击 Azure AD 应用程序的名称以转到应用程序设置，然后记下**租户 ID** 和**客户端 ID** 值。

4. 单击**添加新密钥**。 在接下来的屏幕上，记下**密钥**值。 在离开此页面后，你将无法再访问该信息。 有关详细信息，请参阅[管理 Azure AD 应用程序的密钥](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys)。

<span id="obtain-an-azure-ad-access-token" />

## <a name="step-2-obtain-an-azure-ad-access-token"></a>步骤 2：获取 Azure AD 访问令牌

在 Microsoft Store 提交 API 中调用任何方法之前，首先必须获取将传递给该 API 中每个方法的 **Authorization** 标头的 Azure AD 访问令牌。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以对它进行刷新，以便可以在之后调用该 API 时继续使用。

若要获取访问令牌，请按照 [使用客户端凭据的服务到服务调用](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) 中的说明将 HTTP POST 发送到 ```https://login.microsoftonline.com/<tenant_id>/oauth2/token``` 终结点。 示例请求如下所示。

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://manage.devcenter.microsoft.com
```

有关*租户\_id* POST URI 中的值和*客户端\_id*并*客户端\_机密*参数，指定的租户ID、 客户端 ID 和从上一节中的合作伙伴中心检索应用程序的密钥。 对于 *resource* 参数，必须指定 ```https://manage.devcenter.microsoft.com```。

在你的访问令牌到期后，可以按照[此处](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens)的说明刷新令牌。

对于演示如何使用 C#、Java 或 Python 代码获取访问令牌的示例，请参阅 Microsoft Store 提交 API [代码示例](#code-examples)。

<span id="call-the-windows-store-submission-api">

## <a name="step-3-use-the-microsoft-store-submission-api"></a>步骤 3:使用 Microsoft Store 提交 API

获取 Azure AD 访问令牌后，可以在 Microsoft Store 提交 API 中调用方法。 API 中包含的许多方法按照所适用的应用、加载项和软件包外部测试版方案进行分组。 若要创建或更新提交，一般需在 Microsoft Store 提交 API 中按特定顺序调用多个方法。 有关每个方案和每个方法的语法的信息，请参阅下表中的文章。

> [!NOTE]
> 获取访问令牌后，在令牌到期前，你有 60 分钟时间可以调用 Microsoft Store 提交 API 中的方法。

| 应用场景       | 描述                                                                 |
|---------------|----------------------------------------------------------------------|
| 应用 |  检索到合作伙伴中心帐户注册并创建的应用程序提交的所有应用数据。 有关这些方法的详细信息，请参阅以下文章： <ul><li>[获取应用程序数据](get-app-data.md)</li><li>[管理应用程序提交](manage-app-submissions.md)</li></ul> |
| 加载项 | 获取、创建或删除应用的加载项，然后获取、创建或删除这些加载项的提交。 有关这些方法的详细信息，请参阅以下文章： <ul><li>[管理加载项](manage-add-ons.md)</li><li>[管理外接程序提交](manage-add-on-submissions.md)</li></ul> |
| 软件包外部测试版 | 获取、创建或删除应用的软件包外部测试版，然后获取、创建或删除这些软件包外部测试版的提交。 有关这些方法的详细信息，请参阅以下文章： <ul><li>[管理包航班](manage-flights.md)</li><li>[管理包航班提交](manage-flight-submissions.md)</li></ul> |

<span id="code-samples"/>

## <a name="code-examples"></a>代码示例

下面的文章提供详细的代码示例，演示如何以不同的多种编程语言使用 Microsoft Store 提交 API。

* [C#示例： 应用、 外接程序和航班的提交](csharp-code-examples-for-the-windows-store-submission-api.md)
* [C#示例： 使用游戏选项和尾部的应用程序提交](csharp-code-examples-for-submissions-game-options-and-trailers.md)
* [Java 示例： 应用、 外接程序和航班的提交](java-code-examples-for-the-windows-store-submission-api.md)
* [Java 示例： 使用游戏选项和尾部的应用程序提交](java-code-examples-for-submissions-game-options-and-trailers.md)
* [Python 示例： 应用、 外接程序和航班的提交](python-code-examples-for-the-windows-store-submission-api.md)
* [Python 示例： 使用游戏选项和尾部的应用程序提交](python-code-examples-for-submissions-game-options-and-trailers.md)

## <a name="storebroker-powershell-module"></a>StoreBroker PowerShell 模块

除了直接调用 Microsoft Store 提交 API 的方式外，我们还提供在该 API 之上实现命令行界面的开源 PowerShell 模块。 此模块称为 [StoreBroker](https://aka.ms/storebroker)。 你可以使用此模块从命令行管理你的应用、外部测试版和加载项提交，而不是通过直接调用 Microsoft Store 提交 API，或者你可以浏览源以查看更多有关如何调用此 API 的示例。 在 Microsoft 内，StoreBroker 模块作为将许多第一方应用程序提交到 Microsoft Store 的主要方式被频繁使用。

有关详细信息，请参阅我们 [GitHub 上的 StoreBroker 页面](https://aka.ms/storebroker)。

## <a name="troubleshooting"></a>疑难解答

| 问题      | 分辨率                                          |
|---------------|---------------------------------------------|
| 在通过 PowerShell 调用 Microsoft Store 提交 API 后，如果使用 [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/5.1/microsoft.powershell.utility/ConvertFrom-Json) cmdlet 将该 API 的响应数据从 JSON 格式转换为 PowerShell 对象，然后使用 [ConvertTo-Json](https://docs.microsoft.com/powershell/module/5.1/microsoft.powershell.utility/ConvertTo-Json) cmdlet 将响应数据转换回为 JSON 格式，该响应数据会损坏。 |  默认情况下，[ConvertTo-Json](https://docs.microsoft.com/powershell/module/5.1/microsoft.powershell.utility/ConvertTo-Json) cmdlet 的 *-Depth* 参数设置为 2 级对象，这对于大多数由 Microsoft Store 提交 API 返回的 JSON 对象而言深度不够。 调用 [ConvertTo-Json](https://docs.microsoft.com/powershell/module/5.1/microsoft.powershell.utility/ConvertTo-Json) cmdlet 时，请将 *-Depth* 参数设置为较大数值（如 20）。 |

## <a name="additional-help"></a>其他帮助

如果你对 Microsoft Store 提交 API 有疑问或需要使用此 API 管理你的提交的帮助，请使用以下资源：

* 在我们的[论坛](https://social.msdn.microsoft.com/Forums/windowsapps/home?forum=wpsubmit)上提问。
* 请访问我们[支持页面](https://developer.microsoft.com/windows/support)并请求一个合作伙伴中心的协助的支持选项。 如果系统提示你选择问题类型和类别，请分别选择“应用提交和认证”和“提交应用”。    

## <a name="related-topics"></a>相关主题

* [获取应用程序数据](get-app-data.md)
* [管理应用程序提交](manage-app-submissions.md)
* [管理加载项](manage-add-ons.md)
* [管理外接程序提交](manage-add-on-submissions.md)
* [管理包航班](manage-flights.md)
* [管理包航班提交](manage-flight-submissions.md)
