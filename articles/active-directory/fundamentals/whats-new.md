---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: 9453ceb143201e2b66604c0833d6b35dd2d2ad49
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995178"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`) 复制并粘贴到 ![RSS 图标](./media/whats-new/feed-icon-16x16.png)订阅源阅读器中获取有关何时重新访问此页以获得更新的通知。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---
## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>从同步范围删除的用户不再切换到仅限云的帐户

**类型：** 已修复  
**服务类别：** 用户管理  
**产品功能：** Directory

我们修复了一个 Bug。该 Bug 表现为：将 Active Directory 域服务 (AD DS) 对象从同步范围中排除后，如果接着在下一个同步周期中将其移到 Azure AD 中的回收站，则用户的 DirSyncEnabled 标志会被错误地切换为 **False**。 进行此修复以后，如果将用户从同步范围中排除，然后又将其从 Azure AD 回收站还原，则用户帐户会保持预期的从本地 AD 同步的状态，不能在云中托管，因为其授权源 (SoA) 仍旧为本地 AD。

在进行此修复之前，将 DirSyncEnabled 标志切换为 False 会出现问题。 它给人一个错误印象：这些帐户已转换为仅限云的对象，并且可以在云中托管。 但是，这些帐户仍将其 SoA 作为本地项保留，并保留所有来自本地 AD 的已同步属性（影子属性）。 这种情况在 Azure AD 和其他特定的云工作负荷（例如 Exchange Online）中导致多个问题，这些工作负荷预期将这些帐户作为从 AD 同步的帐户处理，但现在这些帐户的表现就像是仅限云的帐户一样。

目前，若要将从 AD 同步的帐户真正地转换为仅限云的帐户，唯一的方法是在租户级别禁用 DirSync，以便触发一项传输 SoA 的后端操作。 此类 SoA 更改要求（但不限于）清除所有本地的相关属性（例如 LastDirSyncTime 属性和影子属性）并将一个信号发送到其他云工作负荷，以便将相应的对象也转换为仅限云的帐户。

因此，此修复可以防止对从 AD 同步的用户的 ImmutableID 属性进行直接更新，这种更新在过去的某些情况下是必需的。 顾名思义，根据设计，Azure AD 中对象的 ImmutableID 是不可更改的。 在 Azure AD Connect Health 和 Azure AD Connect Synchronization 客户端中实现的新功能用于解决以下方案的问题：

- **一次性对多个用户进行大规模 ImmutableID 更新**

  例如，在实现 Azure AD Connect 时犯错，现在需要更改 SourceAnchor 属性。 解决方案：在租户级别禁用 DirSync，并清除所有无效的 ImmutableID 值。 有关详细信息，请参阅[禁用 Office 365 的目录同步](/office365/enterprise/turn-off-directory-synchronization)。

- **分阶段对多个用户进行大规模 ImmutableID 更新**
  
  例如，需要进行耗时很长的 AD DS 林间迁移。 解决方案：使用 Azure AD Connect **配置源定位点**，并在用户迁移时将现有的 ImmutableID 值从 Azure AD 复制到本地 AD DS 用户的新林的 ms-DS-Consistency-Guid 属性中。 有关详细信息，请参阅[将 msDS-ConsistencyGuid 用作 sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)。

- **将本地用户与 Azure AD 中的现有用户重新匹配** 例如，在 AD DS 中重新创建的用户会在 Azure AD 帐户中生成一个重复的用户，系统不会将其与现有的 Azure AD 帐户（孤立对象）重新匹配。 解决方案：在 Azure 门户中使用 Azure AD Connect Health 重新映射源定位点/ImmutableID。 有关详细信息，请参阅[孤立对象场景](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大更改：更新了通过 Azure Monitor 提供的审核和登录日志的架构

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

目前，我们正在通过 Azure Monitor 发布审核和登录日志流，使你能够将日志文件与 SIEM 工具或 Log Analytics 无缝集成。 根据客户的反馈，同时为此功能的正式版通告做好准备，我们正在对架构进行以下更改。 在 1 月份的第一周，我们将完成这些架构更改并更新其相关的文档。

#### <a name="new-fields-in-the-audit-schema"></a>审核架构中的新字段
我们将添加一个新的“操作类型”字段，以提供针对资源执行的操作类型。 例如“添加”、“更新”或“删除”。

#### <a name="changed-fields-in-the-audit-schema"></a>审核架构中已更改的字段
审核架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|类别|以前它是“服务名称”字段， 现在是“审核类别”字段。 “服务名称”已重命名为“loggedByService”字段。|<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|<ul><li>用户管理</li><li>组管理</li><li>应用管理</li></ul>|
|targetResources|包括顶层的 **TargetResourceType**。|&nbsp;|<ul><li>策略</li><li>应用</li><li>用户</li><li>组</li></ul>|
|loggedByService|提供生成审核日志的服务的名称。|Null|<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|
|结果|提供审核日志的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失败</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>登录架构中已更改的字段
登录架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|以前它是“conditionalaccessPolicies”字段， 现在是“appliedConditionalAccessPolicies”字段。|无变化|无变化|
|conditionalAccessStatus|在登录时提供条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|
|appliedConditionalAccessPolicies: result|在登录时提供单个条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|

有关架构的详细信息，请参阅[解释 Azure Monitor 中的 Azure AD 审核日志架构（预览版）](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>对监督式机器学习模型和风险评分引擎做出的 Identity Protection 改进

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 风险评分

对 Identity Protection 相关的用户和登录风险评估引擎所做的改进有助于提高用户风险评估的准确度和覆盖度。 管理员可能会注意到，用户风险级别不再与特定检测的风险级别直接相关，并且有风险登录事件的数量和级别都已增加。

风险检测现在由监督式机器学习模型评估。该模型使用附加的用户登录和检测模式功能来计算用户风险。 基于此模型，管理员可以发现风险评分较高的用户，即使与该用户关联的检测具有中低型的风险。 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理员可以使用 Microsoft Authenticator 应用（公共预览版）重置自己的密码

**类型：** 已更改的功能  
**服务类别：** 自助密码重置  
**产品功能：** 用户身份验证

现在，Azure AD 管理员可以使用 Microsoft Authenticator 应用通知或者任何移动 Authenticator 应用或硬件令牌提供的代码重置自己的密码。 若要重置自己的密码，管理员现在可以使用以下两种方法：

- Microsoft Authenticator 应用通知

- 其他移动 Authenticator 应用/硬件令牌代码

- 电子邮件

- 电话呼叫

- 短信

有关使用 Microsoft Authenticator 应用重置密码的详细信息，请参阅 [Azure AD 自助密码重置 - 移动应用和 SSPR（预览版）](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新的 Azure AD 云设备管理员角色（公共预览版）

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 访问控制

管理员可将用户分配到新的云设备管理员角色，以执行云设备管理员任务。 分配有“云设备管理员”角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。

有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>在 Azure AD 中使用新的活动时间戳管理设备（公共预览版）

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 设备生命周期管理

我们已认识到，在一段时间后，客户必须在 Azure AD 中更新和淘汰组织的设备，以避免陈旧的设备在环境中挂起。 为了帮助完成此过程，Azure AD 现在会使用新的活动时间戳更新设备，以帮助管理设备生命周期。

有关如何获取和使用此时间戳的详细信息，请参阅[如何：在 Azure AD 中管理陈旧的设备](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理员可以要求用户在每台设备上接受使用条款

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控
 
现在，管理员可以启用“要求用户在每台设备上同意”选项，以要求用户在租户中所用的每台设备上接受使用条款。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“按设备实施的使用条款”部分](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理员可将使用条款配置为按重复性计划过期

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控
 

现在，管理员可以启用“使同意状态过期”选项，以根据指定的重复性计划，使所有用户对使用条款的同意状态过期。 可以实施每年、半年、每季或每月计划。 使用条款过期后，用户必须重新接受。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“添加使用条款”部分](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理员可将使用条款配置为按每个用户的计划过期

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控

现在，管理员可以指定用户必须接受使用条款的持续时间。 例如，管理员可以指定用户必须每隔 90 天重新接受一次使用条款。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“添加使用条款”部分](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)。
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 角色的新 Azure AD Privileged Identity Management (PIM) 电子邮件

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
使用 Azure AD Privileged Identity Management (PIM) 的客户现在可以接收每周摘要电子邮件，其中包括过去七天的以下信息：

- 最符合条件的和永久性的角色分配概述

- 激活角色的用户数

- 已分配到 PIM 中的角色的用户数

- 已分配到 PIM 外部的角色的用户数

- PIM 中“永久”的用户数

有关 PIM 和可用电子邮件通知的详细信息，请参阅 [PIM 中的电子邮件通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)。

---

### <a name="group-based-licensing-is-now-generally-available"></a>基于组的许可现已推出正式版

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** Directory

基于组的许可已过公共预览期，现已推出正式版。 正式版提高了此功能的可伸缩性，并添加了为单个用户重新处理基于组的许可分配的功能，以及结合 Office 365 E3/A3 许可证使用基于组的许可的功能。

有关基于组的许可的详细信息，请参阅 [Azure Active Directory 中基于组的许可是什么？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 11 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 11 月将这 26 款支持联合的新应用添加到了应用库：

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://www.drivedollar.com/Account/Login)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 日志现在适用于 Azure Log Analytics（公共预览版）

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们很高兴地宣布，现在可将 Azure AD 日志转发到 Azure Log Analytics！ 这项呼声最高的功能有助于更好地访问业务、运营和安全分析数据，以及监视基础结构。 有关详细信息，请参阅博客文章 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)（Azure Log Analytics 中的 Azure Active Directory 活动日志现已推出）。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 10 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 10 月将这 14 款支持联合的新应用添加到了应用库：

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial)、[Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)、ambyint、[MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial)、[BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)、Dialpad、[ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial)、[Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial)、[Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)、[Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial)、[Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial)、[Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial)、[Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD 域服务电子邮件通知

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

Azure AD 域服务在 Azure 门户中提供有关托管域配置错误或问题的警报。 这些警报包含分步引导，使你能够在不联系支持人员的情况下尝试解决问题。

从 10 月份开始，可以自定义托管域的通知设置，以便在发生新警报时，向指定的一组人员发送电子邮件，而无需频繁地在门户中检查最新信息。

有关详细信息，请参阅 [Azure AD 域服务中的通知设置](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD 门户支持使用 ForceDelete 域 API 删除自定义域 

**类型：** 已更改的功能  
**服务类别：** 目录管理  
**产品功能：** Directory

我们很高兴地宣布，现在可以使用 ForceDelete 域 API，通过将用户、组和应用等引用从自定义域名 (contoso.com) 异步重命名回到初始默认域名 (contoso.onmicrosoft.com)，来删除自定义域名。

如果组织不再使用自定义域名，或者你需要使用其他 Azure AD 的域名，此项更改可帮助你更快地删除自定义域名。

有关详细信息，请参阅[删除自定义域名](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)。

---

## <a name="september-2018"></a>2018 年 9 月
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>已更新动态组的管理员角色权限

**类型：** 已修复  
**服务类别：** 组管理  
**产品功能：** 协作

我们修复了相关的问题，使特定的管理员角色无需成为组的所有者，即可创建和更新动态成员身份规则。

这些角色为：

- 全局管理员或公司写入者

- Intune 服务管理员

- 用户帐户管理员

有关详细信息，请参阅[创建动态组和检查状态](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>简化了某些第三方应用的单一登录 (SSO) 配置设置

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们已认识到，由于每种应用配置的独特性，为软件即服务 (SaaS) 应用设置单一登录 (SSO) 可能有一定的难度。 我们构建了一个简化的配置体验，可以自动填充以下第三方 SaaS 应用的 SSO 配置设置：

- Zendesk

- ArcGis Online

- Jamf Pro

若要开始使用这种单键式的体验，请转到应用的“Azure 门户” > “SSO 配置”页。 有关详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>“Azure Active Directory - 数据位于何处?”页

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** GoLocal

在“Azure Active Directory - 数据位于何处”页中选择公司所在的区域，查看哪个 Azure 数据中心托管了所有 Azure AD 服务的 Azure AD 静态数据。 可以根据公司所在区域的特定 Azure AD 服务来筛选信息。

若要访问此功能和详细信息，请参阅 [Azure Active Directory - 数据位于何处](https://aka.ms/AADDataMap)。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>适用于“我的应用”访问面板的新部署计划

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** SSO

查看适用于“我的应用”访问面板的新部署计划 (https://aka.ms/deploymentplans)。
“我的应用”访问面板为用户提供查找和访问其应用的单一位置。 此门户还为用户提供自助服务功能，例如，请求访问应用和组，或代表他人管理对这些资源的访问。

有关详细信息，请参阅[什么是“我的应用”门户？](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure 门户“登录日志”页上的新“故障排除和支持”选项卡

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

Azure 门户“登录”页上的新“故障排除和支持”选项卡旨在帮助管理员和支持工程师排查 Azure AD 登录相关的问题。此新选项卡提供错误代码、错误消息和建议的补救措施（如果有）来帮助解决问题。 如果无法解决问题，我们还提供使用“复制到剪贴板”体验创建支持票证的新方法，该体验可在支持票证中填充日志文件的“请求 ID”和“日期(UTC)”字段。  

![新选项卡中的登录日志](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>增强了用于创建动态成员身份规则的自定义扩展属性的支持

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作

借助此项更新，在为用户创建动态成员身份规则时，现在在动态用户组规则生成器中单击“获取自定义扩展属性”链接，输入唯一的应用 ID，然后即可收到要使用的完整自定义扩展属性列表。 还可以刷新此列表，以获取该应用的任何新自定义扩展属性。

有关为动态成员身份规则使用自定义扩展属性的详细信息，请参阅[扩展属性和自定义扩展属性](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

**类型：** 更改计划  
**服务类别：** 条件性访问  
**产品功能：** 标识安全性和保护

以下应用包含在[批准的客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)列表中：

- 微软待办

- Microsoft Stream

有关详细信息，请参阅：

- [Azure AD 基于应用的条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 锁屏界面中新的自助密码重置支持

**类型：** 新功能  
**服务类别：** SSPR  
**产品功能：** 用户身份验证

设置此新功能后，运行 Windows 7、8 或 Windows 8.1 的设备的**锁屏**界面中会向用户显示一个用于重置密码的链接。 单击该链接时，系统会引导用户完成与 Web 浏览器中相同的密码重置流程。

有关详细信息，请参阅[如何在 Windows 7、8 和 8.1 中启用密码重置](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>更改通知：授权代码不再可重复使用 

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 9 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 9 月将这 16 款支持联合的新应用添加到了应用库：

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial)、[Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial)、[Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial)、[ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial)、[Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial)、[JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial)、[Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial)、NavigoCloud、[Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)、join.me、[ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial)、[Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)、Riverbed Xirrus EasyPass、[Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial)、Enlyft SSO for Azure、SurveyMonkey、[Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial)、[dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="support-for-additional-claims-transformations-methods"></a>对其他声明转换方法的支持

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们引入了新的声明转换方法 ToLower() 和 ToUpper()，可以在基于 SAML 的“单一登录配置”页中将这些方法应用到 SAML 令牌。

有关详细信息，请参阅[如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>更新了基于 SAML 的应用配置 UI（预览版）

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO

已更新的基于 SAML 的应用配置 UI 提供：

- 用于配置基于 SAML 的应用的已更新演练体验。

- 为配置中缺失或错误的设置提供更直观的显示。

- 为证书过期通知添加多个电子邮件地址的功能。

- 新的声明转换方法 ToLower() 和 ToUpper() 等。

- 为企业应用上传你自己的令牌签名证书的方法。

- 为 SAML 应用设置 NameID 格式的方法，以及将 NameID 值设置为目录扩展的方法。

若要启用此更新视图，请在“单一登录”页的顶部单击“尝试新体验”链接。 有关详细信息，请参阅[教程：通过 Azure Active Directory 为应用程序配置基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>对 Azure Active Directory IP 地址范围的更改

**类型：** 更改计划  
**服务类别：** 其他  
**产品功能：** 平台

我们正在为 Azure AD 引入更大的 IP 范围，这意味着如果你已为防火墙、路由器或网络安全组配置了 Azure AD IP 地址范围，则需要更新它们。 我们正在进行此更新，因此，在 Azure AD 添加新的终结点时，你不必再次更改防火墙、路由器或网络安全组 IP 范围配置。 

在接下来的两个月中，网络流量将迁移到这些新范围。 若要保持不间断地提供服务，必须在 2018 年 9 月 10 日之前将这些更新的值添加到你的 IP 地址：

- 20.190.128.0/18 

- 40.126.0.0/18 

我们强烈建议不要删除旧的 IP 地址范围，直到你的所有网络流量都已迁移到新范围。 若要了解有关迁移的更新并了解何时可以删除旧范围，请参阅 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>更改通知：授权代码不再可重复使用 

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)。
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>为自助密码重置 (SSPR) 和多重身份验证 (MFA) 融合了安全信息管理

**类型：** 新功能  
**服务类别：** SSPR  
**产品功能：** 用户身份验证
 
此新功能可帮助用户在单个位置和体验中管理 SSPR 和 MFA 的安全信息（例如，电话号码、移动应用等），而以前必须在两个不同的位置进行管理。

此融合体验也适用于使用 SSPR 或 MFA 的用户。 此外，如果组织未强制实施 MFA 或 SSPR 注册，则用户仍可通过“我的应用”门户注册组织允许的任何 MFA 或 SSPR 安全信息方法。

这是一个选用的公共预览版。 管理员可以针对选定的一组用户或者租户中的所有用户启用新体验（如果需要）。 有关融合体验的详细信息，请参阅[融合体验博客](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 应用程序代理应用中的新 HTTP-Only Cookie 设置

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** Access Control

应用程序代理应用中有一个名为“HTTP-Only Cookie”的新设置。 此设置在应用程序代理的访问 Cookie 和会话 Cookie 的 HTTP 响应标头中包含 HTTPOnly 标志，阻止从客户端侧脚本访问 Cookie，并进一步阻止复制或修改 Cookie 等操作，以此提供更高的安全性。 尽管以前未使用此标志，但 Cookie 始终经过加密并通过 SSL 连接传输，以帮助防范不当的修改。

此项设置与使用 ActiveX 控件的应用（例如远程桌面）不兼容。 如果遇到这种情况，我们建议禁用此设置。

有关 HTTP-Only Cookie 设置的详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 资源的 Privileged Identity Management (PIM) 支持管理组资源类型

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
现在，可将实时激活和分配设置应用到管理组资源类型，就像应用到订阅、资源组和资源（例如 VM、应用服务等）一样。 此外，对管理组拥有管理员访问权限的任何人都可以在 PIM 中发现和管理该资源。

有关 PIM 和 Azure 资源的详细信息，请参阅[使用 Privileged Identity Management 发现和管理 Azure 资源](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>使用“应用程序访问”（预览版）可以更快地访问 Azure AD 门户

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
目前，在使用 PIM 激活某个角色时，可能需要 10 分钟以上才能让权限生效。 如果选择使用“应用程序访问”（目前以公共预览版提供），则管理员可以在激活请求完成后立即访问 Azure AD 门户。

目前，“应用程序访问”仅支持 Azure AD 门户体验和 Azure 资源。 有关 PIM 和“应用程序访问”的详细信息，请参阅 [Azure AD Privileged Identity Management 是什么？](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 8 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 8 月将这 16 款支持联合的新应用添加到了应用库：

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial)、[Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)、[Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial)、[Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)、[Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)、[Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial)、[ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial)、SchoolBooking、[4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial)、[Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial)、[N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial)、[Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial)、[SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial)、[ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial)、[eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial)、[Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Azure AD 应用程序代理现已提供本机 Tableau 支持

**类型：** 已更改的功能  
**服务类别：** 应用代理  
**产品功能：** Access Control

随着预身份验证协议已从 OpenID Connect 更新为 OAuth 2.0 代码授予协议，不再需要进行任何附加的配置就能在应用程序代理中使用 Tableau。 此项协议变更还有助于应用程序代理使用仅限 HTTP 的重定向（通常在 JavaScript 和 HTML 标记中受支持）来更好地支持更多新式应用。

有关 Tableau 本机支持的详细信息，请参阅 [Azure AD 应用程序代理现已提供本机 Tableau 支持](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>将 Google 添加为 Azure Active Directory 中 B2B 来宾用户的标识提供者的新支持（预览版）

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C

在组织中设置 Google 联合时，可让受邀的 Gmail 用户使用其现有 Google 帐户登录到共享的应用和资源，而无需创建个人 Microsoft 帐户 (MSA) 或 Azure AD 帐户。

这是一个选用的公共预览版。 有关 Google 联合的详细信息，请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>对 Azure Active Directory 电子邮件通知的改进

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 标识生命周期管理
 
通过以下服务发送的 Azure Active Directory (Azure AD) 电子邮件现在采用更新的设计，并应用对发件人电子邮件地址和发件人显示名称的更改：
 
- Azure AD 访问评审
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD 特权标识管理
- 企业应用到期证书通知
- 企业应用预配服务通知
 
电子邮件通知将从以下电子邮件地址和显示名称发送：

- 电子邮件地址：azure-noreply@microsoft.com
- 显示名称：Microsoft Azure
 
有关一些新电子邮件设计的示例以及详细信息，请参阅 [Azure AD PIM 中的电子邮件通知](https://go.microsoft.com/fwlink/?linkid=2005832)。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD 活动日志现在通过 Azure Monitor 提供

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

Azure AD 活动日志现已推出适用于 Azure Monitor（Azure 的平台级监视服务）的公共预览版。 Azure Monitor 提供长期保留和无缝集成，此外还做出了以下方面的改进：

- 通过将日志文件路由到 Azure 存储帐户来实现长期保留。

- 无缝 SIEM 集成，无需编写或维护自定义脚本。

- 与自己的自定义解决方案、分析工具或事件管理解决方案无缝集成。

有关这些新功能的详细信息，请参阅博客文章 [Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)（Azure Monitor 诊断中的 Azure AD 活动日志现已推出公共预览版）和文档 [Azure Monitor 中的 Azure Active Directory 活动日志（预览版）](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>已将条件访问信息添加到 Azure AD 登录报告

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 标识安全性和保护
 
通过此项更新，可以查看用户登录时会评估哪些策略以及策略结果。 此外，报告现在包括用户使用的客户端应用类型，以便可以识别旧式协议流量。 现在还可以在报告条目中搜索关联 ID，这可以在面向用户的错误消息中找到。此 ID 可用于识别匹配的登录请求及排查其问题。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>通过登录活动日志查看旧式身份验证

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
由于在登录活动日志中引入了“客户端应用”字段，客户现在可以查看使用旧式身份验证的用户。 客户将能够在 Azure AD 门户中通过登录 MS Graph API 或登录活动日志访问此信息。在该门户中，你可以使用“客户端应用”控件对旧式身份验证进行筛选。 查看文档可了解更多详细信息。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 7 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已于 2018 年 7 月将这 16 款支持联合的新应用添加到了应用库中：

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial)、[Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial)、[Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial)、PSUC Staging、[iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial)、[Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)、PowerSchool Unified Classroom、[Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)、[Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial)、[Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial)、[Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial)、[Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial)、[SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial)、[Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial)、[SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial)、[Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新用户预配 SaaS 应用集成 - 2018 年 7 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
可以通过 Azure AD 自动创建、维护和删除 SaaS 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 对于 2018 年 7 月版本，我们为 Azure AD 应用库中的以下应用程序添加了用户预配支持：

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

有关 Azure AD 库中支持用户预配的所有应用程序的列表，请参阅 [SaaS 应用程序与 Azure Active Directory 的集成](https://aka.ms/appstutorial)。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 解决孤立和重复属性同步错误的更简单方法

**类型：** 新功能  
**服务类别：** AD Connect  
**产品功能：** 监视和报告
 
Azure AD Connect Health 引入了自助补救，以帮助突出显示和解决同步错误。 此功能可以排查重复属性同步错误，并修复从 Azure AD 孤立的对象。 此诊断功能具有以下优点：

- 缩小重复属性同步错误的范围，并提供具体的修复方法

- 对专用 Azure AD 方案应用修复，通过一个步骤解决错误

- 无需升级或配置即可启用此功能

有关详细信息，请参阅[诊断并修正重复的属性同步错误](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>对 Azure AD 和 MSA 登录体验做了视觉更新

**类型：** 已更改的功能  
**服务类别：** Azure AD  
**产品功能：** 用户身份验证

我们已更新 Office 365 和 Azure 等 Microsoft 联机服务登录体验的 UI。 此项更改使得屏幕更简洁、更直观。 有关此项更改的详细信息，请参阅博客文章 [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)（即将对 Azure AD 登录体验的改进）。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect 新版本 - 2018 年 7 月

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

Azure AD Connect 的最新版本包括： 

- Bug 修复和支持性更新 

- Ping-Federate 集成正式版

- 对最新版 SQL 2012 客户端的更新 

有关此项更新的详细信息，请参阅 [Azure AD Connect：版本发行历史记录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>对使用条款 (ToU) 最终用户 UI 的更新

**类型：** 已更改的功能  
**服务类别：** 使用条款  
**产品功能：** 调控

我们正在更新 TOU 最终用户 UI 中的“接受”字符串。

**当前文本。** 若要访问 [tenantName] 资源，必须接受使用条款。<br>**新文本。** 若要访问 [tenantName] 资源，必须阅读使用条款。

**当前文本：** 选择接受表示你同意上述所有使用条款。<br>**新文本：** 请单击“接受”，确认已阅读并理解使用条款。

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>直通身份验证支持旧式协议和应用程序

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
直通身份验证现在支持旧式协议和应用。 现在完全支持以下限制：

- 用户无需完成新式新式身份验证，即可登录到旧式 Office 客户端应用程序 Office 2010 和 Office 2013。

- 仅在 Office 2010 上可以访问 Exchange 混合环境中的日历共享功能和闲/忙信息。

- 用户无需完成新式身份验证，即可登录到 Skype for Business 客户端应用程序。

- 用户登录到 PowerShell 版本 1.0。

- 使用 iOS 设置助手的 Apple 设备注册计划 (Apple DEP)。 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>为自助密码重置和多重身份验证融合了安全信息管理

**类型：** 新功能  
**服务类别：** SSPR  
**产品功能：** 用户身份验证

此新功能可让用户在单个体验中管理自助密码重置 (SSPR) 和多重身份验证 (MFA) 的安全信息（例如，电话号码、电子邮件地址、移动应用等）。 用户不再需要在两个不同的体验中为 SSPR 和 MFA 注册相同的安全信息。 此新体验也适用于具有 SSPR 或 MFA 的用户。

如果组织未强制实施 MFA 或 SSPR 注册，则用户可以通过“我的应用”门户注册安全信息。 在此门户中，用户可以注册针对 MFA 或 SSPR 启用的任何方法。 

这是一个选用的公共预览版。 管理员可为租户中选定的一组用户或所有用户启用新体验（如果需要）。

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>重置密码时使用 Microsoft Authenticator 应用验证身份

**类型：** 已更改的功能  
**服务类别：** SSPR  
**产品功能：** 用户身份验证

此功能可让非管理员使用 Microsoft Authenticator（或其他任何验证器应用）提供的通知或代码验证其身份。 管理员启用此自助密码重置方法后，已通过 aka.ms/mfasetup 或 aka.ms/setupsecurityinfo 注册移动应用的用户可以在重置密码时，使用其移动应用作为验证方法。

只能在要求使用两种方法重置密码的策略中启用移动应用通知。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>更改通知：对使用 Azure AD 活动日志 API 的应用程序的委派授权流的安全修补

**类型：** 更改计划  
**服务类别：** 报告  
**产品功能：** 监视和报告

由于我们实施了更强的安全性，我们已对使用委派的授权流访问 [Azure AD 活动日志 API](https://aka.ms/aadreportsapi) 的应用进行了权限更改。 此更改在 **2018 年 6 月 26 日**前生效。

如果你有任何应用使用 Azure AD 活动日志 API，请在更改发生后执行以下步骤来确保应用不会损坏。

**更新应用权限**

1. 登录 Azure 门户，选择“Azure Active Directory”，然后选择“应用注册”。
2. 选择使用 Azure AD 活动日志 API 的应用，依次选择“设置”、“所需权限”和“Microsoft Azure Active Directory”API。
3. 在“启用访问权限”边栏选项卡的“委派的权限”区域中，选中“读取目录数据”旁边的框，然后选择“保存”。
4. 选择“授予权限”，然后选择“是”。
    
    >[!Note]
    >你必须是全局管理员才能向应用授予权限。

有关详细信息，请参阅访问 Azure AD 报告 API 的先决条件文章的[授予权限](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions)部分。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>配置 TLS 设置以连接到 Azure AD 服务从而实现 PCI DSS 符合性

**类型：** 新功能  
**服务类别：** 不适用  
**产品功能：** 平台

传输层安全性 (TLS) 是一种在两个通信应用程序间提供隐私和数据完整性的协议，是目前使用最广泛的安全协议。

[PCI 安全标准委员会](https://www.pcisecuritystandards.org/)已决定禁用早期版本的 TLS 和安全套接字层 (SSL)，以此支持启用新的和更安全的应用协议，该决定于 2018 年 6 月 30 日开始生效。 这一改变意味着如果连接到 Azure AD 服务并要求符合 PCI DSS，则必须禁用 TLS 1.0。 有多个 TLS 版本可供使用，但 TLS 1.2 是可供 Azure Active Directory 服务使用的最新版本。 强烈建议直接为客户端/服务器和浏览器/服务器组合使用 TLS 1.2。

过时的浏览器可能不支持较新的 TLS 版本，例如 TLS 1.2。 若要查看浏览器支持哪些版本的 TLS，请转到 [Qualys SSL 实验室](https://www.ssllabs.com/)网站，然后单击“Test your browser”（测试浏览器）。 建议将 Web 浏览器升级到最新版本，并且最好只启用 TLS 1.2。

**在各种浏览器中启用 TLS 1.2**

- **Microsoft Edge和 Internet Explorer（均使用 Internet Explorer 设置）**

    1. 打开 Internet Explorer，选择“工具” > “Internet 选项” > “高级”。
    2. 在“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”。
    3. 关闭所有浏览器窗口并重启 Internet Explorer。 

- **Google Chrome**

    1. 打开 Google Chrome，在地址栏键入“chrome://settings/”，然后按 Enter。
    2. 展开“高级”选项，转到“系统”部分，然后选择“打开代理设置”。
    3. 在“Internet 属性”框中，选择“高级”选项卡，转到“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”。
    4. 关闭所有浏览器窗口并重启 Google Chrome。

- **Mozilla Firefox**

    1. 打开 Firefox，在地址栏键入“about:config”，然后按 Enter。
    2. 搜索 TLS 一词，然后选择“security.tls.version.max”条目。
    3. 将值设置为 3，强制浏览器使用最高 TLS 1.2 的版本，然后选择“确定”。

        >[!NOTE]
        >Firefox 版本 60.0 支持 TLS 1.3，因此还可将 security.tls.version.max 值设置为 4。

    4. 关闭所有浏览器窗口并重启 Mozilla Firefox。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 6 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已于 2018 年 6 月将这 15 款支持联合的新应用添加到了应用库中：

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial)[Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial)[SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial)[Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)[Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)[Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)[Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial)Smartway2、[TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial)[Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial)[Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial)[SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial)[ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial)[Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)[ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 有关在 Azure AD 应用库中列出应用程序的详细信息，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>公共预览版中提供 Azure AD 密码保护功能

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 用户身份验证

使用 Azure AD 密码保护有助于杜绝环境中出现易于猜到的密码。 消除这些密码有助于降低遭受密码喷射型攻击时密码泄露的风险。

具体来说，Azure AD 密码保护有助于：

- 保护 Azure AD 和 Windows Server Active Directory (AD) 中的组织帐户。 
- 阻止用户使用最常用密码列表上的密码，该列表包含 500 多个密码，以及这些密码的 100 多万个字符替换变体。
- 从 Azure AD 门户中的单一位置管理 Azure AD 密码保护，既适用于 Azure AD，也适用于本地 Windows Server AD。

有关 Azure AD 密码保护的详细信息，请参阅[消除组织中的劣质密码](https://aka.ms/aadpasswordprotectiondocs)。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>创建使用条款 (ToU) 期间创建全新的“所有来宾”条件访问策略模板

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控

在创建使用条款 (ToU) 期间，还会为“所有来宾”和“所有应用”创建新的条件访问策略模板。 此全新的策略模板采用新创建的 ToU，简化了来宾的创建和执行过程。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>创建使用条款 (ToU) 期间创建全新的“自定义”条件访问策略模板

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控

在创建使用条款 (ToU) 期间，还会创建新的“自定义”条件访问策略模板。 通过此新策略模板可创建 ToU，然后立即转到条件访问策略创建边栏选项卡，无需手动浏览门户。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>有关部署 Azure 多重身份验证的全新详尽指南

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 标识安全性和保护
 
我们发布了有关如何在组织中部署 Azure 多重身份验证 (MFA) 的新分步指南。

若要查看 MFA 部署指南，请转到 GitHub 上的[身份部署指南](https://aka.ms/DeploymentPlans)存储库。 若要提供有关部署指南的反馈，请使用[部署计划反馈表](https://aka.ms/deploymentplanfeedback)。 如对部署指南有任何疑问，请通过 [IDGitDeploy](mailto:idgitdeploy@microsoft.com) 与我们联系。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD 委派的应用管理角色处于公共预览状态

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** Access Control

管理员现可委派应用管理任务，无需分配全局管理员角色。 新的角色和功能有：

- **新的标准 Azure AD 管理员角色：**

    - **应用程序管理员。** 授予管理所有应用各个方面的权限，包括注册、SSO 设置、应用分配和许可、应用代理设置和同意（Azure AD 资源除外）。

    - **云应用程序管理员。** 授予所有应用程序管理员权限，但应用代理除外，因为它不提供本地访问权限。

    - **应用程序开发人员。** 即使“允许用户注册应用”选项已关闭，该角色也可授予创建应用注册的权限。

- **所有权（设置每个应用注册和每个企业应用，类似于群组所有权流程：**
 
    - **应用注册所有者。** 授予管理自有应用注册各个方面的权限，包括应用清单和添加其他所有者。

    - **企业应用所有者。** 授予管理自有企业应用许多方面的权限，包括 SSO 设置、应用分配和同意（Azure AD 资源除外）。

有关公共预览版的详细信息，请参阅 [Azure AD 委派的应用程序管理角色处于公共预览状态！](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) 博客。 有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)。

---