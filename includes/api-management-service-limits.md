---
title: include 文件
description: include 文件
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: e01eebe41010135d0dc0a2cb4170e6b6687ff546
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292667"
---
| 资源 | 限制 |
| --- | --- |
| 最大缩放单位数 | 每个区域 10 个<sup>1</sup> |
| 缓存大小 | 每个单位 5 GB<sup>2</sup> |
| 每个 HTTP 颁发机构的并行后端连接<sup>3</sup> | 每个单位 2048 个<sup>4</sup> |
| 最大缓存响应大小 | 2MB |
| 最大策略文档大小 | 256 KB<sup>5</sup> | 
| 每个服务实例的最大自定义网关域<sup>6</sup> | 20 | 
| 每个订阅的最大服务实例数<sup>7</sup> | 5 | 
| 每个服务实例的最大订阅数<sup>7</sup> | 500 |
| 每个服务实例的最大客户端证书数<sup>7</sup> | 50 | 
| 每个服务实例的最大 API 数<sup>7</sup> | 50 | 
| 每个服务实例的最大 API 操作数<sup>7</sup> | 1000 | 
| 最大总请求持续时间<sup>7</sup> | 30 秒 | 
| 最大缓冲有效负载大小<sup>7</sup> | 2MB | 


<sup>1</sup> 缩放限制取决于定价层。 若要查看定价层及其缩放限制，请转到 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup> 每单位缓存大小取决于定价层。 若要查看定价层及其缩放限制，请转到 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup> 除非后端明确关闭，否则将合并并重新使用连接。<br/>
<sup>4</sup> 每个单位的基本、标准或高级层。 开发人员层限制为 1024。 不适用于消耗层。<br/> 
<sup>5</sup> 处于基本、标准或高级层。 在消耗层，策略文档的大小限制为 4 KB。<br/>
<sup>6</sup> 仅高级层中可用。<br/>
<sup>7</sup> 仅适用于消耗层。<br/>



