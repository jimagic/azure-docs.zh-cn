---
title: 管理 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9c4b66501cfb51088609de81fe00b5b041e056d3
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037522"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 可以使用 API 配置文件以不同的语言创建自定义客户端。 每个客户端都使用 API 配置文件来与 Azure Stack 的正确的资源提供程序和 API 版本通信。

可以创建一个应用来与 Azure 资源提供程序配合运行，而无需明确区分与 Azure Stack 兼容的每个资源提供程序 API 的版本。 只需将应用程序对应到某个配置文件，SDK 就能还原到正确的 API 版本。

本主题帮助读者了解：

 - Azure Stack 的 API 配置文件。
 - 了解如何使用 API 配置文件来开发解决方案。
 - 查看在何处查找代码特定的指导。

## <a name="summary-of-api-profiles"></a>API 配置文件的摘要

- API 配置文件用于表示一组 Azure 资源提供程序及其 API 版本。
- API 配置文件是为了让你创建跨多个 Azure 云的模板而创建的。 配置文件提供兼容且稳定的接口。
- 配置文件每年发布四次。
- 使用了以下三个配置文件命名约定：
    - **latest**  
        包含全球 Azure 中发布的最新 API 版本。
    - **yyyy-mm-dd-hybrid**  
    Biannually 发布，此版本侧重一致性和稳定性跨多个云。 此配置文件以实现最佳 Azure Stack 兼容性为目标。
    - **yyyy mm-dd 配置文件**平衡最佳稳定性和最新功能。

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 配置文件和 Azure Stack 兼容性

最新 Azure API 配置文件与 Azure Stack 不兼容。 可以使用以下命名约定来标识要将哪些配置文件用于 Azure Stack 解决方案。

**最新**  
此配置文件具有的最新 API 版本在全球 Azure 中找到的 Azure Stack 中不起作用。 **Latest** 包含的重大更改最多。 此配置文件不考虑稳定性以及与其他云的兼容性。 如果想要使用的最新的 API 版本**最新**是应使用的配置文件。

**Yyyy-mm-dd-hybrid**  
此配置文件在每年的三月份和九月份发布。 它具有最佳稳定性和兼容性的各类云。 **Yyyy-mm-dd-hybrid** 是以全球 Azure 和 Azure Stack 作为应用目标而设计的。 此配置文件中列出的 Azure API 版本将与 Azure Stack 上列出的相同。 可以使用此配置文件来为混合云解决方案开发代码。

**yyyy-mm-dd-profile**  
此配置文件在六月份和九月份针对全球 Azure 发布。 它并不适用于 Azure Stack;通常情况下，将有很多重大更改。 尽管它可以平衡最佳稳定性和最新功能之间的差异**最新**，并且此配置文件是**最新**始终包含最新的 API 版本，而不考虑 API 时发布。 例如，如果新的 API 版本明天创建计算 api，该 API 版本被列入**最新**，但不能在**yyyy mm-dd 配置文件**配置文件，因为此配置文件已存在。 **yyyy-mm-dd-profile** 涵盖了六月份或九月份之前发布的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure Stack 不使用全球 Azure 中提供的最新版 API。 创建解决方案时，必须将 API 版本查找为每个 Azure 资源提供程序与 Azure Stack 兼容。

你可以使用 API 配置文件，而无需研究 Azure Stack 支持的每个资源提供程序和特定版本。 配置文件会指定一组资源提供程序和 API 版本。 在 SDK 中或使用 SDK 构建工具将恢复目标为`api-version`配置文件中指定。 使用 API 配置文件，可以指定适用于整个模板，在运行时的配置文件版本，Azure 资源管理器选择正确版本的资源。

API 配置文件可与使用 Azure 资源管理器的工具（例如 PowerShell、Azure CLI、SDK 中提供的代码，以及 Microsoft Visual Studio）配合运行。 工具和 SDK 可以使用配置文件来读取生成应用程序时要包含的模块和库的版本。

例如，如果你使用 PowerShell 创建存储帐户使用**Microsoft.Storage**资源提供程序，它支持**api 版本**2016年-03-30 和 VM 使用**Microsoft.Compute**资源提供程序**api 版本**2015年-12-01，必须查找 PowerShell 模块支持 2016年-03-30 对于存储，以及哪个模块支持的计算，2015年-02-01，然后安装它们。 可以改用配置文件。 使用 cmdlet `Install-Profile <profilename>`，和 PowerShell 加载正确版本的模块。

同样，在使用 Python SDK 生成基于 Python 的应用程序时，可以指定配置文件。 SDK 将为脚本中指定的资源提供程序加载正确的模块。

开发人员可以专注于编写解决方案。 而不是研究哪些 API 版本、 资源提供程序和云协同工作，则可以使用配置文件，并了解你的代码跨所有支持该配置文件的云。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以借助代码示例，通过配置文件将采用偏好语言的解决方案与 Azure Stack 相集成。 目前可以找到以下语言的指导和示例：

- **.NET** 可以使用 .NET API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。 有关详细信息，请参阅[在 Azure Stack 中将 API 版本配置文件与 .NET 配合使用](azure-stack-version-profiles-net.md)。
- **PowerShell**  
可以使用通过 PowerShell 库提供的 **AzureRM.Bootstrapper** 模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用适用于 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI**  
可将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 有关信息，请参阅[使用适用于 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。
- **Go**  
在 Go SDK 中，配置文件是不同的资源类型使用不同的服务的不同版本的组合。 配置文件是可用配置文件/路径下，其版本采用**年-月-日**格式。 有关信息，请参阅[使用 API 版本配置文件适用于 Go](azure-stack-version-profiles-go.md)。
- **Ruby**  
用于 Azure Stack 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 有关信息，请参阅[将 API 版本配置文件与 Ruby 配合使用](azure-stack-version-profiles-ruby.md)
- **Python**  
Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack 和 Azure 公有云）用作目标。 API 配置文件可用于创建混合云的解决方案。 有关信息，请参阅[将 API 版本配置文件与 Python 配合使用](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [查看配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
