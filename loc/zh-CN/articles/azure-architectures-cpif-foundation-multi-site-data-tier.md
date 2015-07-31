<properties 
   pageTitle="Multi-Site Data Tier (Azure Architecture Patterns)" 
   description="The Multi-Site Data Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 多站点数据层 (Azure 架构模式)

的 [云平台集成框架 (奖励)](azure-architectures-cpif-overview.md) 提供入职到微软云计算解决方案的应用程序的工作量集成指导。 

奖励介绍如何组织、 客户和合作伙伴应设计和部署云有针对性的工作负载，利用 Azure、 系统中心和 Windows 服务器的混合云平台和管理能力。 

的 **多站点数据层** 模式是的一部分 **基础** 地区，奖励体系结构文档中详尽地说明了。 

## 多站点数据层

多站点数据层设计图案细节的蔚蓝功能和提供可以跨越地理边界提供可预测的性能和高可用性的数据层服务所需的服务。这种设计模式用于数据层被定义作为传统数据平台在提供服务或孤立的地服务层或多层应用程序的一部分。 此模式中负载平衡的数据层被提供两局部区域内和跨区域。   

介绍了 SQL Server 2012，AlwaysOn 可用性组是完全支持在 Azure 基础设施服务的高可用性和灾难恢复功能。 AlwaysOn 可用性组文章中，可以发现详细的信息和官方支持 AlwaysOn 可用性组对 Windows Azure 基础设施服务的通告。   

本文档提供在 Azure 的多站点数据层建筑概述利用 SQL AlwaysOn 可用性组。与可选只读辅助节点在额外的 Azure 数据中心的附加功能和灾难恢复。在 Azure 中使用 SQL AlwaysOn 提供可以消耗的 web 或应用程序层的高可用性数据层。  

本文档重点建筑模式和做法，可以在官方的教程中，其中简要说明了 Azure AlwaysOn 可用性组的配置和配置的 AlwaysOn 可用性组侦听器发现全面部署指导。 

## 建筑模式概述 

本文档描述的可用性和冗余目的为在多个地域提供对微软 SQL 服务器内容的访问模式。 关键服务都没有注意到应用程序或 web 层将访问数据本身，如下所示。 下面的图表是一个简单的例子，有关的服务和他们如何使用这种模式的一部分。   

每个主要服务领域概述了更多的细节图。 
 
![资源和资源组叶片的标签部分](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  更多的资源
[负载平衡数据层 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## 请参见
[奖励的体系结构](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[全局负载平衡的 Web 层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[混合网络](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[蔚蓝搜索层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[批量处理层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
