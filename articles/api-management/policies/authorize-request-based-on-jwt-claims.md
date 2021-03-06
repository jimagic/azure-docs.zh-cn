---
title: Azure API 管理策略示例 - 基于 JWT 声明授予访问权限 | Microsoft Docs
description: Azure API 管理策略示例 - 演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 60b36ceeac1cd4578ca81ac908c1a8a03c9d0180
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869120"
---
# <a name="authorize-access-based-on-jwt-claims"></a>基于 JWT 声明授权访问权限

本文介绍 Azure API 管理策略示例，该示例演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

