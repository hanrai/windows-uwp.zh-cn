---
ms.assetid: c92c0ea8-f742-4fc1-a3d7-e90aac11953e
description: 使用 Microsoft Store 评价 API，以编程方式在 Microsoft Store 中提交针对应用评价的回复。
title: 使用 Microsoft Store 服务回复评价
ms.date: 06/04/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 评价 API, 回复评价
ms.localizationpriority: medium
ms.openlocfilehash: 677108e692bbc702778cad3c42a45b4f5408b8cd
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57653162"
---
# <a name="respond-to-reviews-using-store-services"></a>使用 Microsoft Store 服务回复评价

使用 *Microsoft Store 评价 API* 可以编程方式在 Microsoft Store 中提交对你的应用评价的回复。 此 API 的开发人员想要进行大容量对许多评审的响应而无需使用合作伙伴中心是特别有用。 此 API 使用 Azure Active Directory (Azure AD) 验证来自应用或服务的调用。

以下步骤介绍端到端过程：

1.  确保已完成所有[先决条件](#prerequisites)。
2.  在 Microsoft Store 评价 API 中调用某个方法之前，请先[获取 Azure AD 访问令牌](#obtain-an-azure-ad-access-token)。 获取访问令牌后，可以在 60 分钟的令牌有效期内，使用该令牌调用 Microsoft Store 评价 API。 该令牌到期后，可以重新生成一个。
3.  [调用 Microsoft Store 评价 API](#call-the-windows-store-reviews-api)。

> [!NOTE]
> 除了使用 Microsoft Store 评审 API 以编程方式响应评论，或者可以响应评论[使用合作伙伴中心](../publish/respond-to-customer-reviews.md)。

<span id="prerequisites" />

## <a name="step-1-complete-prerequisites-for-using-the-microsoft-store-reviews-api"></a>第 1 步：使用 Microsoft Store 的完成先决条件检查 API

在开始编写调用 Microsoft Store 评价 API 的代码之前，确保已完成以下先决条件。

* 你（或你的组织）必须具有 Azure AD 目录，并且你必须具有该目录的[全局管理员](https://go.microsoft.com/fwlink/?LinkId=746654)权限。 如果你已使用 Office 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。 否则，你可以免费[在合作伙伴中心中创建新的 Azure AD](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)。

* 必须将 Azure AD 应用程序与你的合作伙伴中心帐户相关联、 检索租户 ID 和应用程序的客户端 ID 和生成密钥。 Azure AD 应用程序是指你想要从中调用 Microsoft Store 评价 API 的应用或服务。 需要租户 ID、客户端 ID 和密钥，才可以获取将传递给 API 的 Azure AD 访问令牌。
    > [!NOTE]
    > 你只需执行一次此任务。 获取租户 ID、客户端 ID 和密钥后，当你需要创建新的 Azure AD 访问令牌时，可以随时重复使用它们。

若要将 Azure AD 应用程序与你的合作伙伴中心帐户相关联，并检索所需的值：

1.  在合作伙伴中心[将组织的合作伙伴中心帐户与你组织的 Azure AD 目录相关联](../publish/associate-azure-ad-with-partner-center.md)。

2.  接下来，从**用户**页面**帐户设置**合作伙伴中心部分[添加 Azure AD 应用程序](../publish/add-users-groups-and-azure-ad-applications.md#add-azure-ad-applications-to-your-partner-center-account)，表示应用或服务，你将使用对评审的响应。 请确保为此应用程序分配**管理员**角色。 如果不存在该应用程序尚未在 Azure AD 目录，你可以[创建一个新 Azure AD 应用程序在合作伙伴中心](../publish/add-users-groups-and-azure-ad-applications.md#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。 

3.  返回到**用户**页面、单击 Azure AD 应用程序的名称以转到应用程序设置，然后记下**租户 ID** 和**客户端 ID** 值。

4. 单击**添加新密钥**。 在接下来的屏幕上，记下**密钥**值。 在离开此页面后，你将无法再访问该信息。 有关详细信息，请参阅[管理 Azure AD 应用程序的密钥](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys)。

<span id="obtain-an-azure-ad-access-token" />

## <a name="step-2-obtain-an-azure-ad-access-token"></a>步骤 2：获取 Azure AD 访问令牌

在 Microsoft Store 评价 API 中调用任何方法之前，首先必须获取将传递给该 API 中每个方法的 **Authorization** 标头的 Azure AD 访问令牌。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以对它进行刷新，以便可以在之后调用该 API 时继续使用。

若要获取访问令牌，请按照 [使用客户端凭据的服务到服务调用](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) 中的说明将 HTTP POST 发送到 ```https://login.microsoftonline.com/<tenant_id>/oauth2/token``` 终结点。 示例请求如下所示。

```syntax
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

<span id="call-the-windows-store-reviews-api" />

## <a name="step-3-call-the-microsoft-store-reviews-api"></a>步骤 3:调用 Microsoft Store 评审 API

获取 Azure AD 访问令牌后，你可随时调用 Microsoft Store 评价 API。 必须将访问令牌传递到每个方法的 **Authorization** 标头。

Microsoft Store 评价 API 包含多种方法，可用于确定你是否能够回复给定评价和提交对一条或多条评价的回复。 请按照此过程使用该 API：

1. 获取要回复的评价的 ID。 评价 ID 位于 Microsoft Store 分析 API 中的[获取应用评价](get-app-reviews.md)方法的回复数据中，以及[评价报告](../publish/reviews-report.md)的[脱机下载](../publish/download-analytic-reports.md)中。
2. 调用[获取应用评价的回复信息](get-response-info-for-app-reviews.md)方法以确定你是否能回复评价。 当客户提交评价时，他们可以选择不接收对其评价的回复。 你无法回复由选择不接收评价回复的客户提交的评价。
3. 调用[提交对应用评价的回复](submit-responses-to-app-reviews.md)方法以可编程方式回复评价。


## <a name="related-topics"></a>相关主题

* [获取应用程序评论](get-app-reviews.md)
* [获取响应信息的应用程序评论](get-response-info-for-app-reviews.md)
* [答复提交到应用程序评论](submit-responses-to-app-reviews.md)

 
