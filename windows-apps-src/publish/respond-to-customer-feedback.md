---
title: 回复客户反馈
description: 可以直接回复客户在反馈中心中留下的反馈。
author: JnHs
ms.author: wdg-dev-content
ms.date: 06/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 04983b80-2a18-4ace-93d3-e8c33c04bfb9
ms.localizationpriority: medium
ms.openlocfilehash: 7c9819890dffcf70f56f6c0b09d9a1a24a8818db
ms.sourcegitcommit: 914b38559852aaefe7e9468f6f53a7465bf36e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "3393893"
---
# <a name="respond-to-customer-feedback"></a>回复客户反馈

可以使用[反馈报告](feedback-report.md)查看 Windows 10 客户就你的应用在反馈中心中留下的反馈，然后直接回复该反馈。 可以在反馈中心中发布供所有人查看的回复（作为个别评论，或通过更新一条反馈的状态并添加描述），告知客户相关新功能或 Bug 修复，或者就如何改进你的应用请求更多特定反馈。 还可以将你的回复作为电子邮件直接发送给留下反馈的客户。

> [!TIP]
> 可以按以下方式鼓励客户留下反馈：使用 [Microsoft Store Services SDK](http://aka.ms/store-em-sdk) 中的反馈 API 添加允许客户直接[从 UWP 应用启动反馈中心](../monetize/launch-feedback-hub-from-your-app.md)的控件。 请记住，已在支持反馈中心的 Windows 10 设备上下载了你的应用的任何客户可以直接通过“反馈中心”应用留下该应用的反馈。 因此，你可能会在此报告中看到客户反馈，即使未从你的应用内明确请求反馈。

若要对任何一条反馈进行回复，请单击**反馈报告**中该条反馈旁边显示的“回复反馈”**** 链接。

Windows 开发人员中心支持用于回复就你的应用提供反馈的客户的三个选项。 无论你选择哪个选项，请记住，每个回复有 1000 个字符的限制。

## <a name="public-comments-in-feedback-hub"></a>反馈中心中的公共评论

默认情况下，单击“回复反馈”**** 后，“评论”**** 单选按钮就处于选中状态。 若要对客户的反馈发布公共回复，请保留此按钮的选中状态。 在框中输入你的评论，然后单击“提交”****。

在反馈中心中，你输入的评论将显示为一条评论，还会显示其他客户提交的评论。 会将你的发布者名称和应用名称以及你的评论一同显示，以标识你的开发人员身份。 针对一条反馈可以编写的评论没有数量限制，但请注意，无法编辑或删除已提交的评论。 将在你的**反馈报告**（以及反馈中心中）显示一条反馈的五条最新评论。 当存在五条以上的评论时，在反馈中心中单击“显示所有评论”**** 即可查看所有评论。


## <a name="private-responses-via-email"></a>通过电子邮件发送私下回复

如果你不愿意发布公共回复，可以选中“通过电子邮件发送评论”**** 框，直接向客户发送私下回复（如果客户提供了电子邮件地址，并且未选择退出通过电子邮件接收回复）。 当你执行此操作时，Microsoft 会以你的名义向客户发送一封电子邮件。 该封电子邮件中会包含客户的原始反馈以及你编写的回复。

在选中“通过电子邮件发送评论”**** 框后，输入你的评论，然后单击“提交”****。 请注意，使用此选项时，必须在“支持联系人电子邮件”**** 字段中提供电子邮件地址。 默认情况下，我们使用你在你的帐户联系人信息中提供的电子邮件地址。 如果你想要使用其他电子邮件地址，更新“支持联系人电子邮件”**** 字段即可使用其他电子邮件地址。 收到你回复的客户将可以直接向此电子邮件地址进行回复。


## <a name="public-status-updates-and-descriptions-in-feedback-hub"></a>反馈中心中的公共状态更新和描述

用于公共回复的第三个选项是设置某条反馈的状态，使你的客户知道你正在解决该问题或已解决该问题。 当更新某条反馈的状态时，会在反馈中心中显示该条反馈及其状态。

若要使用此选项，请选中“更新状态”**** 单选按钮。 然后选择以下选项之一：

- **正在调查**：已意识到问题，正在进行调查。
- **正在解决**：正在解决问题或正在添加请求的功能。
- **已完成**：已发布解决问题或添加请求的功能的更新。

更新状态的同时，还可以输入提供更多信息的评论（例如，估计何时解决问题或有关最新更改的详细信息）。 该描述将显示在评论列表顶部（反馈报告将显示当前状态和描述）。

使用“更新状态”**** 选项，使你可以随时更改状态（以及提供每个状态更改的已更新描述）。 每当更改某条反馈的状态时，会在反馈中心中更新该状态，以便查看你的回复的客户将看到最新状态。


## <a name="guidelines-for-responses"></a>回复准则

无论采用哪种方法回复客户的反馈，都必须遵循适用于所有回复的以下准则：
- 回复不得超过 1000 个字符。
- 不得向用户提供任何类型的补偿（包括数字应用项目）来获取其公共评论。
- 请勿在你的回复中包含任何市场营销内容或广告。 请记住，留下反馈的用户已经是你的客户。
- 请勿在你的回复中推广其他应用或服务。
- 你的回复应与特定的应用和反馈直接相关。
- 请勿在你的回复中包含任何不文明、攻击性、个人或恶意的评论。 请始终使用礼貌用语并请牢记：满意的客户将很有可能成为你应用的最大推动者。

> [!NOTE]
> 如果客户收到不恰当的反馈回复，可以向 Microsoft 举报开发人员。 客户也可以选择不通过电子邮件接收反馈回复。

你与客户的关系与你自己相关。 如果开发人员与客户之间有争议，Microsoft 将不介入其中。 但是，如果你认为客户关于你产品的反馈内容不合理，请提交[支持票证](http://go.microsoft.com/fwlink/p/?LinkID=401178)。