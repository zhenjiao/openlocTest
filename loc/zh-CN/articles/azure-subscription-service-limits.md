<properties
	pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints"
	description="Provides a list of common Azure subscription and service limits, quotas, and constraints. This includes information on how to increase limits along with maximum values."
	services=""
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettem"
	editor="cgronlun"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# 蔚蓝的订阅和服务限制、 配额和限制

## 概述

此文档指定一些最常见的微软 Azure 限制。请注意这当前不包括所有的 Azure 服务。随着时间推移，这些限制将扩大和更新，以覆盖更多的平台。

> [AZURE。注意] 如果你想要提高限额以上 **默认限制**您可以 [打开在线客户支持请求，无费用](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).限制不能提出以上 **最大限制** 在下表中的值。如果有是没有 **最大限制** 列，然后指定的资源不存在可调的限制。


### 限制和蔚蓝的资源管理器

它是现在可以组合到单个 Azure 资源组多蔚蓝资源。当使用资源组，曾经是全球的限制成为托管在区域一级与 Azure 资源管理器中。关于 Azure 资源组的详细信息，请参阅 [使用资源组来管理你蔚蓝的资源](resource-group-portal.md).

在下面的限制，已添加一个新表以反映任何差异在限制时使用 Azure 资源管理器。例如，有的是 **订阅数限制** 表和 **订阅数限制-蔚蓝的资源管理器** 表。当限制仅适用于这两种方案时，它仅显示第一个表中。除非另有说明，限制是全球所有地区。

> [AZURE。注意] 它是重要的是强调 Azure 资源组中的资源的配额限制是每个区域可以访问由您的订阅、 并不是每个订阅，正如服务管理配额。作为一个例子，让我们使用核心配额。如果您需要申请配额增加内核的支持，您需要决定如何多个内核，您想要使用在哪些地区，然后进行 Azure 资源组核心配额的具体申请的金额和您想要的区域。因此，如果你需要使用 30 芯在西欧来运行您的应用程序;你应具体请求 30 芯在西欧。但你不会有任何其他地区增加核心配额 — — 只有西欧将有 30 芯配额。
<!-- -->
As a result, you may find it useful to consider deciding what your Azure Resource Group quotas need to be for your workload in any one region, and request that amount in each region into which you are considering deployment. See [troubleshooting deployment issues](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) for more help discovering your current quotas for specific regions.

## 订阅数限制

[AZURE。包括 [azure 订阅限制](../includes/azure-subscription-limits.md)]

### 订阅数限制-蔚蓝的资源管理器

以下限制适用时使用 Azure 资源管理器和 Azure 资源组。没有改变与 Azure 资源管理器的限制未在下面列出。请参阅上表为这些限制。

[AZURE。包括 [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## 资源组的限制

[AZURE。包括 [azure-资源-组-限制](../includes/azure-resource-groups-limits.md)]


## 虚拟机限制

[AZURE。包括 [azure 虚拟机限制](../includes/azure-virtual-machines-limits.md)]


### 虚拟机限制-蔚蓝的资源管理器

以下限制适用时使用 Azure 资源管理器和 Azure 资源组。没有改变与 Azure 资源管理器的限制未在下面列出。请参阅上表为这些限制。

[AZURE。包括 [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## 网络限制

[AZURE。包括 [azure 虚拟网络限制](../includes/azure-virtual-network-limits.md)]


### 网络限制 — — 蔚蓝的资源管理器

以下限制适用时使用 Azure 资源管理器和 Azure 资源组。没有改变与 Azure 资源管理器的限制未在下面列出。请参阅上表为这些限制。

[AZURE。包括 [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## 存储限制

### 标准的存储限制 

[AZURE。包括 [azure 存储限制](../includes/azure-storage-limits.md)]

存储帐户限制的更多详细信息，请参阅 [Azure 存储可扩展性和性能指标](../articles/storage/storage-scalability-targets.md).


### 特优的存储限制

[AZURE。包括 [azure 存储限制高端存储](../includes/azure-storage-limits-premium-storage.md)]


### 存储限制-蔚蓝的资源管理器

[AZURE。包括 [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## 云服务限制

[AZURE。包括 [azure 云服务限制](../includes/azure-cloud-services-limits.md)]


## 应用程序服务限制-Web 应用程序，移动应用程序，API 应用程序，应用程序逻辑

[AZURE。包括 [azure 网站限制](../includes/azure-websites-limits.md)]

## 批次限制

[AZURE。包括 [azure 批限制](../includes/azure-batch-limits.md)]


## DocumentDB 限制

[AZURE。包括 [azure-documentdb-限制](../includes/azure-documentdb-limits.md)]


## 移动接触限制

[AZURE。包括 [azure 移动接触限制](../includes/azure-mobile-engagement-limits.md)]


## 搜索限制

[AZURE。包括 [azure 搜索限制](../includes/azure-search-limits.md)]

Azure 搜索限制的更多详细信息，请参阅 [限制和约束](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## SQL 数据库限制

[AZURE。包括 [azure sql 数据库限制](../includes/azure-sql-database-limits.md)]

SQL 数据库限制的更多详细信息，请参阅下列主题:

 - [SQL azure 数据库服务层 (版)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [SQL azure 数据库服务层和性能级别](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [数据库吞吐量单元 (DTU) 配额](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [SQL 数据库资源限制](sql-database/sql-database-resource-limits.md)

## 媒体服务限制

[AZURE。包括 [azure 提供限制](../includes/azure-mediaservices-limits.md)]

## 移动服务限制

[AZURE。包括 [移动服务限制](../includes/mobile-services-limits.md)]

## 服务总线限制

[AZURE。包括 [azure-servicebus-限制](../includes/service-bus-quotas-table.md)]

## 流分析限制

[AZURE。包括 [流分析限制表](../includes/stream-analytics-limits-table.md)]

## 活动目录限制

[AZURE。包括 [反倾销协定 》 服务限制](../includes/active-directory-service-limits-include.md)]


## 蔚蓝的 RemoteApp 限制

[AZURE。包括 [azure remoteapp 限制](../includes/azure-remoteapp-limits.md)]

## StorSimple 系统限制

[AZURE。包括 [storsimple 限制表](../includes/storsimple-limits-table.md)]


## 业务洞察力限制

[AZURE。包括 [业务洞察力限制](../includes/operational-insights-limits.md)]

## 备份限制

[AZURE。包括 [azure 备份限制](../includes/azure-backup-limits.md)]

## 网站恢复限制

[AZURE。包括 [网站恢复限制](../includes/site-recovery-limits.md)]

## API 管理限制

[AZURE。包括 [api 管理服务限制](../includes/api-management-service-limits.md)]

## 天青穿红衣的缓存限制

[AZURE。包括 [穿红衣的缓存服务限制](../includes/redis-cache-service-limits.md)]

## 密钥存储库限制

[AZURE。包括 [密钥存储库限制](../includes/key-vault-limits.md)]

## 多因素身份验证
[AZURE。包括 [azure mfa 服务限制](../includes/azure-mfa-service-limits.md)]

## 请参见

[了解蔚蓝限制和增加](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[虚拟机和 Azure 云服务大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)
