---
title: 在 Azure Stack 中创建计划 | Microsoft Docs
description: 作为云管理员，创建一个让订阅方预配虚拟机的计划。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 7a3a87c70402374bea6357d1b89a19938ca08868
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243519"
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中创建计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[计划](azure-stack-key-features.md)是对一个或多个服务的分组。 作为提供商，可以创建提供给用户的计划。 反过来，用户可以订阅套餐，以便使用其所包括的计划和服务。 此示例演示如何创建一个包括计算、网络和存储资源提供程序的计划。 订阅方使用此计划可以预配虚拟机。

1. 登录到[Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建计划和用户可以订阅的产品/服务，请选择 **+ 创建资源**，然后**产品/服务 + 计划**，然后**计划**。
  
   ![选择计划](media/azure-stack-create-plan/select-plan.png)

3. 在“新建计划”下，输入**显示名称**和**资源名称**。 显示名称是用户可以看到计划的友好名称。 只有管理员可以看到资源名称，管理员使用作为 Azure 资源管理器资源的计划处理。

   ![指定详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择“服务”，然后选择与“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”对应的复选框。 接下来，选择“选择”以保存配置。 将鼠标指针悬停在每个选项上时会显示复选框。
  
   ![选择服务](media/azure-stack-create-plan/services.png)

6. 选择“配额”、“Microsoft.Storage (本地)”，然后选择默认配额，或选择“新建配额”来创建自定义的配额。
  
   ![配额](media/azure-stack-create-plan/quotas.png)

7. 如果要新建配额，请输入配额的**名称** > 指定配额值 > 选择“确定”。 “创建配额”对话框关闭。

   ![新建配额](media/azure-stack-create-plan/new-quota.png)

   然后选择创建的新配额。 选择配额会分配它并关闭选择对话框。
  
   ![分配配额](media/azure-stack-create-plan/assign-quota.png)

8. 重复步骤 6 和 7 来为“Microsoft.Network (本地)”和“Microsoft.Compute (本地)”创建并分配配额。 为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![完成配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 在“配额”下选择“确定”，然后在“新建计划”下选择“创建”以创建计划。

    ![创建计划](media/azure-stack-create-plan/create.png)

10. 若要查看新计划，请选择“所有资源”，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”来通过名称定位你的计划。

   ![检查计划](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
