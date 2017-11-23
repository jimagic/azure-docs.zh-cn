---
title: "最终用户身份验证：通过 Azure Active Directory 将 Python 与 Data Lake Store 配合使用 | Microsoft Docs"
description: "了解如何通过 Python 使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 48990c57fb10127733623000a105507b5a48d900
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>使用 Python 进行 Data Lake Store 最终用户身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [使用 .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-end-user-authenticate-python.md)
> * [使用 REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

本文介绍如何使用 Python SDK 进行 Azure Data Lake Store 最终用户身份验证。 最终用户身份验证可以进一步拆分为两个类别：

* 无需多重身份验证的最终用户身份验证
* 使用多重身份验证的最终用户身份验证

本文将讨论这两个选项。 有关使用 Python 的 Data Lake Store 服务到服务身份验证，请参阅[使用 Python 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="prerequisites"></a>先决条件

* **Python**。 可以从[此处](https://www.python.org/downloads/)下载 Python。 本文使用的是 Python 3.6.2。

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“本机”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)中的步骤。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Data Lake Store，需要安装三个模块。

* `azure-mgmt-resource` 模块，包括用于 Active Directory 的 Azure 模块，等等。
* `azure-mgmt-datalake-store`模块，包括 Azure Data Lake Store 帐户管理操作。 有关此模块的详细信息，请参阅 [Azure Data Lake Store 管理模块参考](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)。
* `azure-datalake-store` 模块，包括 Azure Data Lake Store 文件系统操作。 有关此模块的详细信息，请参阅 [Azure Data Lake Store 文件系统模块参考](http://azure-datalake-store.readthedocs.io/en/latest/)。

使用以下命令安装这些模块。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 在所选的 IDE 中创建新的 Python 应用程序，例如 **mysample.py**。

2. 添加以下代码片段以导入所需的模块

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. 将更改保存到 mysample.py。

## <a name="end-user-authentication-with-multi-factor-authentication"></a>使用多重身份验证的最终用户身份验证

### <a name="for-account-management"></a>适用于帐户管理

使用以下代码片段可对 Data Lake Store 帐户的帐户管理操作进行 Azure AD 身份验证。 可以使用以下代码片段通过多重身份验证在应用程序中进行身份验证。 为现有 Azure AD **本机**应用程序提供以下值。

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>适用于文件系统操作

使用此方法可对 Data Lake Store 帐户的文件系统操作进行 Azure AD 身份验证。 可以使用以下代码片段通过多重身份验证在应用程序中进行身份验证。 为现有 Azure AD **本机**应用程序提供以下值。

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>无需多重身份验证的最终用户身份验证

此方法已弃用。 有关详细信息，请参阅[使用 Python SDK 的 Azure 身份验证](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token)。
   
## <a name="next-steps"></a>后续步骤
本文介绍了如何通过 Python 使用最终用户身份验证进行 Azure Data Lake Store 身份验证。 现可查看以下介绍如何使用 Python 在 Azure Data Lake Store 中执行操作的文章。

* [Data Lake Store 上的帐户管理操作（使用 Python）](data-lake-store-get-started-python.md)
* [使用 Python 在 Data Lake Store 上进行的数据操作](data-lake-store-data-operations-python.md)
