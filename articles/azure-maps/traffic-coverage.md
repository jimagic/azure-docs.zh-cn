---
title: Azure Maps 中的交通覆盖区域 | Microsoft Docs
description: 了解 Azure Maps 中的交通覆盖区域
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 08a4f3fc135aae2772bd60c67cbd282603cd4f8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-maps-traffic-coverage"></a>Azure Maps 交通覆盖区域

Azure Maps 以交通**流**和**事件**的形式提供丰富的交通信息。 此数据可以在地图上直观显示，或生成在实际驾驶条件下作为因素考虑的更智能路线。 

但是，Maps 并非对所有区域提供相同级别的信息和准确性。 下表提供了可从每个区域请求的交通信息类别的相关信息： 

|区域  |事故  |流向  |
|---------|:---------:|:---------:|
|阿根廷      |         |✓         |
|澳大利亚     |✓         |✓        |
|奥地利     |✓         |✓         |
|巴林     |         |✓         |
|比利时     |✓         |✓         |
|巴西     |✓         |✓         |
|保加利亚     |✓         |✓         |
|加拿大     |✓         |✓         |
|智利     |✓         |✓         |
|哥伦比亚      |         |✓         |
|克罗地亚     |         |✓         |
|捷克共和国     |✓         |✓         |
|丹麦     |✓         |✓         |
|埃及     |         |✓         |
|爱沙尼亚     |         | ✓        |
|芬兰     |✓         |✓         |
|+奥兰群岛      |✓         |✓         |
|法国     |✓         |✓         |
|+摩纳哥     |✓         |✓         |
|德国     |✓         |✓         |
|希腊     |✓         |✓         |
|中国香港特别行政区     |✓         |✓         |
|匈牙利     |✓         |✓         |
|冰岛     |         |✓         |
|印度尼西亚     |✓         |✓         |
|爱尔兰      |✓         |✓         |
|以色列     |         |✓         |
|意大利     |✓         |✓        |
|+圣马力诺     |✓         |✓         |
|科威特     |✓         |✓         |
|拉脱维亚     |         |✓         |
|莱索托     |✓         |✓         |
|立陶宛     |         |✓         |
|卢森堡     |✓         |✓         |
|澳门特别行政区     |         |✓         |
|马来西亚     |✓         |✓         |
|马耳他     |✓         |✓         |
|墨西哥     |✓         |✓         |
|摩洛哥     |         |✓         |
|荷兰     |✓         |✓         |
|新西兰     |✓         |✓         |
|挪威     |✓         |✓         |
|阿曼     |         |✓         |
|波兰     |✓         |✓         |
|葡萄牙     |✓         |✓         |
|+亚速尔群岛和马德拉群     |         |✓         |
|卡塔尔     |         |✓         |
|罗马尼亚     |         |✓         |
|俄罗斯联邦     |✓         |✓         |
|沙特阿拉伯     |✓         |✓         |
|新加坡     |✓         |✓         |
|斯洛伐克     |✓         |✓         |
|斯洛文尼亚     |✓         |✓         |
|南非     |✓         |✓         |
|西班牙     |✓         |✓         |
|+安道尔     |✓         |✓         |
|+巴利亚里群岛     |✓         |✓         |
|+加那利群岛     |✓         |✓         |
|+直布罗陀     |✓         |✓         |
|瑞典     |✓         |✓         |
|瑞士     |✓         |✓        |
|+列支敦士登      |✓         |✓         |
|中国台湾     |✓         |✓        |
|泰国     |✓         |✓        |
|土耳其     |✓         |✓         |
|乌克兰     |✓         |✓         |
|阿拉伯联合酋长国     |✓         |✓         |
|英国     |✓         |✓         |
|（格恩西岛和泽西岛）     |✓         |✓         |
|马恩岛     |✓         |✓         |
|美国     |✓         |✓        |
|+波多黎各     |✓         |✓         |

有关 Azure Maps 交通数据的详细信息，请参阅[交通](https://docs.microsoft.com/rest/api/azure-maps/traffic)参考页面。