---
title: Azure Stack 虚拟机简介
description: 了解 Azure Stack 虚拟机
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 61b923b278d13bdcf97e05859c36b14bf9edba6e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050978"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 虚拟机简介

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 提供一种类型的按需、 可缩放的计算资源与虚拟机 (Vm)。 在需要更好地控制计算环境时，可以选择 VM。 在创建第一个 VM 之前，本文提供了详细信息。

Azure Stack VM 可提供虚拟化的灵活性，而无需管理群集或单个计算机。 不过，仍然需要通过执行任务（例如，配置、修补和安装在 VM 上运行的软件）来维护 VM。

以下几种方式，可以使用 Azure Stack 虚拟机。 例如：

- **开发和测试**  
    Azure Stack Vm，可以创建具有所需的代码的特定配置的计算机和测试应用程序。

- **云中的应用程序**  
    由于应用程序的需求会不断变化，在 Azure Stack 中的 VM 上运行应用程序可能会较具经济效益。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。

- **扩展的数据中心**  
    Azure Stack 虚拟网络中的虚拟机可以连接到你组织的网络或 Azure。

您的应用程序使用可以纵向扩展，或向外扩展为任意以满足您需求的 Vm。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>若要创建 VM 之前考虑一下，我需要什么

始终有设计注意事项时构建在 Azure Stack 中应用程序基础结构。 在开始创建基础结构之前，必须考虑到 VM 的以下重要方面：

- 应用程序资源的名称。
- VM 的大小。
- 可以创建的 VM 数目上限。
- VM 运行的操作系统。
- VM 在启动后的配置。
- VM 所需的相关资源。

### <a name="naming"></a>命名

虚拟机会被指定名称，也具有在操作系统中所配置的计算机名称。 VM 的名称最多可包含 15 个字符。

如果使用 Azure Stack 创建操作系统磁盘，则计算机名称与虚拟机名称相同。 如果上传并使用自己的映像（该映像包含先前配置的操作系统），并使用它创建虚拟机，则名称可能会不同。 上传自己的图像文件时最佳做法是，请确保计算机名称中操作系统和虚拟机名称相同。

### <a name="vm-size"></a>VM 大小

使用的 VM 大小取决于要运行的工作负荷。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure Stack 提供各种大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

你的订阅中，可能会影响你的项目的部署的 Vm 具有默认的配额限制。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

虚拟机使用虚拟硬盘 (VHD) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，可以选择某个映像来安装 OS。 Azure Stack 提供一个市场，适用于各种版本和类型的操作系统。 Marketplace 映像由映像发布者、 产品/服务、 SKU 和版本 (通常为指定的版本**最新**。)

下表显示了如何查找映像信息：

|方法|描述|
|---------|---------|
|Azure Stack 门户|选择要使用的映像时，系统会自动指定值。|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[列出映像发布者](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[列出映像产品](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[列出映像 SKU](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

可以选择上传并使用自己的映像。 如果这样做，则不会使用发布者名称、 产品和 SKU。

### <a name="extensions"></a>扩展

VM 扩展通过部署后配置和自动化任务来增加 VM 的功能。
可以使用扩展完成以下常见任务：

- **运行自定义脚本**  
    自定义脚本扩展可帮助你在 VM 上配置工作负荷，通过预配 VM 时运行脚本。

- **部署和管理配置**  
    PowerShell Desired State Configuration (DSC) 扩展可帮助您设置的 VM 上的 DSC 来管理配置和环境。

- **收集诊断数据**  
    Azure 诊断扩展可帮助你将 VM 配置为收集诊断数据，可用于监视你的应用程序的运行状况。

### <a name="related-resources"></a>相关资源

下表中的资源由 VM 使用，并且需要存在，或者在创建 VM 时创建：

|资源|需要|描述|
|---------|---------|---------|
|资源组|是|VM 必须包含在资源组中。|
|存储帐户|否|如果使用托管磁盘，则 VM 不需要存储帐户来存储其虚拟硬盘。 <br>如果使用非托管磁盘，则 VM 确实需要存储帐户来存储其虚拟硬盘。|
|虚拟网络|是|VM 必须是虚拟网络的成员。|
|公共 IP 地址|否|可以向 VM 分配一个公共 IP 地址，以便远程访问它。|
|网络接口|是|VM 需要使用网络接口在网络中通信。|
|数据磁盘数|否|VM 可以包含数据磁盘，以便扩展存储功能。|

## <a name="create-your-first-vm"></a>创建第一个 VM

创建 VM 有多种选择。 你的选择取决于环境。 下表提供了信息以帮助您开始创建 VM:

|方法|文章|
|---------|---------|
|Azure Stack 门户|使用 Azure Stack 门户创建 Windows 虚拟机<br>[使用 Azure Stack 门户创建 Linux 虚拟机](azure-stack-quick-linux-portal.md)|
|模板|Azure Stack 快速入门模板位于以下位置：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[在 Azure Stack 中使用 PowerShell 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure Stack 中使用 PowerShell 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure Stack 中使用 CLI 创建 Windows 虚拟机](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure Stack 中使用 CLI 创建 Linux 虚拟机](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>管理 VM

可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 API 管理 VM。 可能执行的一些典型管理任务包括：

- 获取有关 VM 的信息
- 连接到 VM
- 管理可用性
- 进行备份

### <a name="get-information-about-your-vm"></a>获取有关 VM 的信息

下表显示了获取有关 VM 的信息的一些方法。

|方法|描述|
|---------|---------|
|Azure Stack 门户|在中心菜单中，单击“虚拟机”，然后从列表中选择 VM。 在 VM 的页面上，可以访问概述信息、设置值以及监视指标。|
|Azure PowerShell|在 Azure 和 Azure Stack 中，管理 VM 的方法很相似。 有关使用 PowerShell 的详细信息，请参阅以下 Azure 主题：<br>[使用 Azure PowerShell 模块创建和管理 Windows VM](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|客户端 SDK|在 Azure 和 Azure Stack 中，使用 C# 管理 VM 的方法很相似。 有关详细信息，请参阅以下 Azure 主题：<br>[在 Azure 中使用 C# 创建和管理 Windows VM](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>连接到 VM

在 Azure Stack 门户中，可以使用“连接”按钮连接到 VM。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)
