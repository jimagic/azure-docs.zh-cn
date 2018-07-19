---
title: 教程：Azure Active Directory 与 Moxtra 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Moxtra 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 8266da9c6b7aaa0aea2cd5cefb73febc090f3ba5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214886"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>教程：Azure Active Directory 与 Moxtra 的集成

本教程介绍了如何将 Moxtra与 Azure Active Directory (Azure AD) 集成。

将 Moxtra 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Moxtra
- 可以让用户使用其 Azure AD 帐户自动登录到 Moxtra（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Moxtra 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Moxtra 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Moxtra
2. 配置和测试 Azure AD 单一登录

## <a name="adding-moxtra-from-the-gallery"></a>从库中添加 Moxtra
要配置 Moxtra 与 Azure AD 的集成，需要从库中将 Moxtra 添加到托管 SaaS 应用列表。

**若要从库中添加 Moxtra，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Moxtra”。

    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/tutorial_moxtra_search.png)

5. 在结果面板中，选择“Moxtra”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Moxtra 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Moxtra 用户。 换句话说，需要在 Azure AD 用户与 Moxtra 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Moxtra 中“用户名”的值来建立此关联关系。

若要配置并测试 Moxtra 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Moxtra 测试用户](#creating-a-moxtra-test-user) - 在 Moxtra 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Moxtra 应用程序中配置单一登录。

**若要配置 Moxtra 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Moxtra 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. 在“Moxtra 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_url.png)

    在“登录 URL”文本框中，键入 URL `https://www.moxtra.com/service/#login`

4. Moxtra 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示了此配置的示例。 

    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ------------------- | -------------------- |    
    | 名 | user.givenname |
    | 姓 | user.surname |
    | idpid    | <SAML 实体 ID> 

    > [!Note]
    > idpid 属性的值不是真实值。 可以从“Moxtra 配置”下的“快速参考”部分获取实际值。
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_attribute_04.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_attribute_05.png)

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 单击“确定” 。
    
5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_general_400.png)

7. 在“Moxtra 配置”部分中，单击“配置 Moxtra”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_configure.png) 

8. 在另一个浏览器窗口中，以管理员身份登录到 Moxtra 公司站点。

9. 在左侧工具栏上，单击“管理控制台”>“SAML 单一登录”，然后单击“新建”。
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

10. 在 **SAML** 页上执行以下步骤：
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. 在“名称”文本框中，键入配置名称（例如：*SAML*）。 
  
    b. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。 
 
    c. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。 
 
    d. 在“AuthnContextClassRef”文本框中，键入“urn:oasis:names:tc:SAML:2.0:ac:classes:Password”。 
 
    e. 在“NameID 格式”文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress”。 
 
    f. 在记事本中打开从 Azure 门户下载的证书，复制内容，然后粘贴到“证书”文本框中。    
 
    g. 在“SAML 电子邮件域”文本框中，键入 SAML 电子邮件域。    
  
    >[!NOTE]
    >若要查看用来验证域的步骤，请单击下方的“**i**”。

    h. 单击“更新”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/moxtra-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-moxtra-test-user"></a>创建 Moxtra 测试用户

本部分的目的是在 Moxtra 中创建名为 Britta Simon 的用户。

**若要在 Moxtra 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 Moxtra 公司站点。

2. 在左侧工具栏上，单击“管理控制台”>“用户管理”，并单击“添加用户”。
   
    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

3. 在“添加用户”对话框中，执行以下步骤：
  
    a. 在“名字”文本框中，键入“Britta”。
  
    b. 在“姓氏”文本框中，键入“Simon”。
  
    c. 在“电子邮件”文本框中，键入 Britta 在 Azure 门户中使用的电子邮件地址。
  
    d. 在“分部”文本框中，键入“Dev”。
  
    e. 在“部门”文本框中，键入“IT”。
  
    f. 选择“管理员”。
  
    g. 单击 **“添加”**。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Moxtra 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Moxtra，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Moxtra”。

    ![配置单一登录](./media/moxtra-tutorial/tutorial_moxtra_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Moxtra 磁贴时，应当会自动登录到 Moxtra 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/moxtra-tutorial/tutorial_general_01.png
[2]: ./media/moxtra-tutorial/tutorial_general_02.png
[3]: ./media/moxtra-tutorial/tutorial_general_03.png
[4]: ./media/moxtra-tutorial/tutorial_general_04.png

[100]: ./media/moxtra-tutorial/tutorial_general_100.png

[200]: ./media/moxtra-tutorial/tutorial_general_200.png
[201]: ./media/moxtra-tutorial/tutorial_general_201.png
[202]: ./media/moxtra-tutorial/tutorial_general_202.png
[203]: ./media/moxtra-tutorial/tutorial_general_203.png
