---
title: 在 Azure Stack 中提供虚拟机规模集 | Microsoft Docs
description: 了解云操作员如何向 Azure Stack 市场中添加虚拟机规模集
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 2c615dd781b40c3ebb78ae291453c5b4b2d2ef4d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971821"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure Stack 中提供虚拟机规模集

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*
  
虚拟机规模集是一种 Azure Stack 计算资源。 可以使用它们部署和管理一组相同的虚拟机。 使用相同的方式配置的所有虚拟机，规模集不需要预配的虚拟机。 可以更方便地构建面向大型计算、大数据、容器化工作负荷的大规模服务。

本文将指导你完成在 Azure Stack 市场中提供规模集的过程。 完成此过程之后，用户将可以将虚拟机规模集添加到其订阅。

Azure Stack 上的虚拟机规模集是类似于在 Azure 上的虚拟机规模集。 有关详细信息，请参阅以下视频：
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [Guy Bowerman 介绍虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 上虚拟机规模集不支持自动缩放。 可以使用资源管理器模板、CLI 或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>必备组件

- **Marketplace:** 注册 Azure Stack 与全球 Azure，以便在 Marketplace 中的项的可用性。 请遵照[将 Azure Stack 注册到 Azure](azure-stack-registration.md) 中的说明操作。
- **操作系统映像：** 可以创建虚拟机规模集 (VMSS) 之前，你必须下载用于从 VMSS 中的 VM 映像[Azure Stack Marketplace](azure-stack-download-azure-marketplace-item.md)。 必须已存在映像，然后用户才能创建新的 VMSS。 

## <a name="use-the-azure-stack-portal"></a>使用 Azure Stack 门户 

>[!IMPORTANT]  
> 当使用的是 Azure Stack 版本 1808 或更高版本时，本部分中的信息适用。 如果版本为 1807 或更低版本，请参阅[添加虚拟机规模集（1808 之前的版本）](#add-the-virtual-machine-scale-set-prior-to-version-1808)。

1. 登录到 Azure Stack 门户。 然后，转到**所有服务**，然后**虚拟机规模集**，然后在**计算**，选择**虚拟机规模集**。 
   ![选择虚拟机规模集](media/azure-stack-compute-add-scalesets/all-services.png)

2. 选择“创建虚拟机规模集”。
   ![创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 填写空字段，对于“操作系统磁盘映像”、“订阅”和“实例大小”，请从下拉列表中进行选择。 对于“使用托管磁盘”，请选择“是”。 然后单击“创建”。
    ![配置和创建](media/azure-stack-compute-add-scalesets/create.png)

4. 若要查看新的虚拟机规模集，请转到“所有资源”，搜索该虚拟机规模集名称，然后在搜索结果中单击其名称。 
   ![查看规模集](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>添加虚拟机规模集（版本 1808 之前的版本）

>[!IMPORTANT]  
> 当使用的是 1808 之前的 Azure Stack 版本时，本部分中的信息适用。 如果使用的是版本 1808 或更高版本，请参阅[使用 Azure Stack 门户](#use-the-azure-stack-portal)。

1. 打开 Azure Stack 市场并连接到 Azure。 选择**Marketplace 管理**，然后单击 **+ 从 Azure 添加**。

    ![市场管理](media/azure-stack-compute-add-scalesets/image01.png)

2. 添加并下载虚拟机规模集市场项。

    ![虚拟机规模集](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虚拟机规模集中的映像

创建虚拟机规模集之后，用户无需重新创建规模集即可更新规模集中的映像。 更新映像的过程取决于以下场景：

1. 指定虚拟机规模集部署模板**最新**有关**版本**:  

   当`version`设置为**最新**中`imageReference`部分的模板的规模集，纵向扩展操作在规模集使用的图像的最新可用版本为规模集实例。 向上扩展完成后，可以删除旧虚拟机规模集实例。 值`publisher`， `offer`，和`sku`保持不变。 

   下面的 JSON 示例指定`latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   向上扩展可以使用新映像之前，必须下载了新的映像：  

   - 当在 marketplace 映像是较新版本比规模集中的映像时，下载新映像来替换旧映像。 替换映像后，用户可以继续纵向扩展。 

   - 如果必须在规模集中的映像相同 Marketplace 的映像版本，删除在规模集中使用的映像，然后下载新映像。 在删除原始映像之后、下载新映像之前，无法执行纵向扩展。 
      
     此过程是必需的重新映像，使使用稀疏文件格式，版本 1803年中引入的。 
 
2. 虚拟机规模集部署模板不会为 **version** 指定 **latest**，而是改为指定版本号：  

    如果下载较新版本 （这会更改可用版本） 映像，规模集无法纵向扩展。 这是根据设计，因为规模集模板中指定的映像版本必须可用。  

有关详细信息，请参阅[操作系统磁盘和映像](./user/azure-stack-compute-overview.md#operating-system-disks-and-images)。  

## <a name="scale-a-virtual-machine-scale-set"></a>缩放虚拟机规模集

可以缩放*虚拟机规模集*的大小以使其更大或更小。  

1. 在门户中，选择你的规模集，然后选择“缩放”。

2. 使用滑动条设置新级别的扩展对于此虚拟机规模集，然后依次**保存**。
     ![缩放规模集](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>删除虚拟机规模集

若要删除虚拟机规模集库项，请运行以下 PowerShell 命令：

```PowerShell  
Remove-AzsGalleryItem
```

> [!NOTE]
> 库项可能不会立即删除。 可能需要刷新门户几次，该项才会从市场中删除。

## <a name="next-steps"></a>后续步骤

[从 Azure marketplace 项下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)