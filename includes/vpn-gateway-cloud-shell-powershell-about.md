---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6a0ea318f2e9b8f392ac7c0a1f1091c062c59d41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852347"
---
本文使用 PowerShell cmdlet。 若要运行 cmdlet，可以使用 Azure Cloud Shell（一个免费的交互式 shell）。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 请直接单击“复制”对代码进行复制，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 可通过多种方式来启动 Cloud Shell：

|  |   |
|-----------------------------------------------|---|
| 单击代码块右上角的“试用”。 | ![本文中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| 在浏览器中打开 Cloud Shell。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| 单击 Azure 门户右上角菜单上的“Cloud Shell”按钮。 | [![门户中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

如果不想使用 Azure Cloud Shell，则可以改为在本地安装 PowerShell。 如果选择在本地安装和使用 PowerShell，请务必安装最新版本的 Azure 资源管理器 PowerShell cmdlet 以获取最新的功能。

若要查找你在本地运行的 PowerShell 版本，请使用“Get-Module -ListAvailable AzureRM”cmdlet。 若要更新，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。