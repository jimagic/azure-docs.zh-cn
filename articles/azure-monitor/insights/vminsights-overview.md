---
title: 什么是用于 VM 的 Azure Monitor（预览版）？ | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure Monitor 的一项功能，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文提供了概述。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 69aa2cbcaa6861b1d5c5c71769be2fb8046d9ea5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188484"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什么是用于 VM 的 Azure Monitor（预览版）？

用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 该服务分析 Windows 和 Linux VM 的性能和运行状况，监视它们的进程及其对其他资源和外部进程的依赖关系。 

作为一种解决方案，用于 VM 的 Azure Monitor 支持监视本地或其他云提供程序中托管的 VM 的性能和应用程序依赖关系。 三个主要功能提供深入的见解：

* **运行 Windows 和 Linux 的 Azure VM 的逻辑组件**：根据预配置的运行状况条件进行衡量，并在满足评估条件时提醒你。  

* **预定义的趋势性能图表**：显示来宾 VM 操作系统的核心性能指标。

* **依赖项映射**：显示来自各种资源组和订阅的 VM 的互连组件。  

这些功能分成三个透视图：

* 运行状况
* 性能
* 映射

>[!NOTE]
>目前，对于 Azure 虚拟机和虚拟机规模集，仅提供了运行状况功能。 性能和映射功能同时支持 Azure VM 和托管在环境或其他云提供商中的虚拟机。

与 Log Analytics 集成提供了强大的聚合和筛选功能，并可随时分析数据趋势。 单独使用 Azure Monitor、服务映射或 Log Analytics 无法实现此类全面的工作负荷监视。  

可直接从虚拟机在单个 VM 中查看此数据，也可使用 Azure Monitor 提供 VM 的聚合视图。 此视图基于每个功能的透视图：

* **运行状况**：VM 与资源组相关。
* 映射和性能：VM 配置为向特定的 Log Analytics 工作区进行报告。

![Azure 门户中的虚拟机见解透视图](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure DevOps 可以提供可预测的性能和重要应用程序的可用性。 它标识关键的操作系统事件、性能瓶颈和网络问题。 Azure DevOps 还有助于理解某个问题是否与其他依赖关系相关。  

## <a name="data-usage"></a>数据使用情况 

部署用于 VM 的 Azure Monitor 时，系统会引入 VM 收集的数据并将其存储在 Azure Monitor 中。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对用于 VM 的 Azure Monitor 进行计费：
* 引入和存储的数据。
* 受监视的运行状况条件指标时序的数量。
* 创建的预警规则。
* 发送的通知。 

日志大小根据计数器的字符串长度而变化，并且可能会随逻辑磁盘和网络适配器的数量而增大。 如果你已有一个工作区并且正在收集这些计数器，则不会重复收费。 如果已在使用服务映射，则你看到的唯一变化是发送到 Azure Monitor 的额外连接数据。

## <a name="next-steps"></a>后续步骤
请参阅[部署用于 VM 的 Azure Monitor](vminsights-onboard.md)，了解有助于监视虚拟机的要求和方法。
