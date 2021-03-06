---
title: 教程：Azure Active Directory 与 Ivanti Service Manager (ISM) 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Ivanti Service Manager (ISM) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.openlocfilehash: 3b394ff8e3638a9663e756fd6db866b0c3e5d2ef
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447537"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>教程：Azure Active Directory 与 Ivanti Service Manager (ISM) 的集成

本教程介绍如何将 Ivanti Service Manager (ISM) 与 Azure Active Directory (Azure AD) 集成。

将 Ivanti Service Manager (ISM) 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Ivanti Service Manager (ISM)。
- 可让用户使用 Azure AD 帐户自动登录到 Ivanti Service Manager (ISM)（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Ivanti Service Manager (ISM) 的集成，需准备好以下各项：

- Azure AD 订阅
- 已启用 Ivanti Service Manager (ISM) 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Ivanti Service Manager (ISM)
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>从库中添加 Ivanti Service Manager (ISM)

若要配置 Ivanti Service Manager (ISM) 与 Azure AD 的集成，需要将库中的 Ivanti Service Manager (ISM) 添加到托管 SaaS 应用列表。

**若要从库中添加 Ivanti Service Manager (ISM)，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **Ivanti Service Manager (ISM)**，在结果面板中选择“Ivanti Service Manager (ISM)”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为“Britta Simon”的测试用户配置并测试 Ivanti Service Manager (ISM) 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Ivanti Service Manager (ISM) 用户。 也就是说，需要在 Azure AD 用户与 Ivanti Service Manager (ISM) 中的相关用户之间建立链接关系。

若要配置和测试 Ivanti Service Manager (ISM) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Ivanti Service Manager (ISM) 测试用户](#creating-an-ivanti-service-manager-ism-test-user)** - 在 Ivanti Service Manager (ISM) 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录，并在 Ivanti Service Manager (ISM) 应用程序中配置单一登录。

**若要配置 Ivanti Service Manager (ISM) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Ivanti Service Manager (ISM)”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial-general-301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Ivanti Service Manager (ISM) 域和 URL 单一登录信息](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识符”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Ivanti Service Manager (ISM) 域和 URL 单一登录信息](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://<customer>.saasit.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Ivanti Service Manager (ISM) 客户端支持团队](https://www.ivanti.com/support/contact)获取这些值。

6. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(原始)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. 在“设置 Ivanti Service Manager (ISM)”部分，根据要求复制相应的 URL。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![Ivanti Service Manager (ISM) 配置](common/configuresection.png)

8. 若要在 **Ivanti Service Manager (ISM)** 端配置单一登录，需将已下载的“证书(原始)”和已复制的“登录 URL”、“Azure AD 标识符”和“注销 URL”发送到 [Ivanti Service Manager (ISM) 客户端支持团队](https://www.ivanti.com/support/contact)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create-aaduser-01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create-aaduser-02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>创建 Ivanti Service Manager (ISM) 测试用户

本部分的目的是在 Ivanti Service Manager (ISM) 中创建名为 Britta Simon 的用户。 Ivanti Service Manager (ISM) 支持默认已启用的实时预配。 此部分不存在任何操作项。 尝试访问 Ivanti Service Manager (ISM) 期间，如果尚不存在用户，则会创建一个新用户。
>[!Note]
>如果需要手动创建用户，请联系  [Ivanti Service Manager (ISM) 支持团队](https://www.ivanti.com/support/contact)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们将通过向 Britta Simon 授予对 Ivanti Service Manager (ISM) 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Ivanti Service Manager (ISM)”。

    ![配置单一登录](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Ivanti Service Manager (ISM) 磁贴时，应会自动登录到 Ivanti Service Manager (ISM) 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
