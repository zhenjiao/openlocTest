<properties 
   pageTitle="Offsite Batch Processing Tier (Azure Architecture Patterns)" 
   description="The Offsite Batch Processing Tier pattern is part of the Infrastructure area, which is described extensively in the CPIF Architecture document." 
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

# 异地批量处理层 (Azure 架构模式)

的 [云平台集成框架 (奖励)](azure-architectures-cpif-overview.md) 提供入职到微软云计算解决方案的应用程序的工作量集成指导。  

奖励介绍如何组织、 客户和合作伙伴应设计和部署云有针对性的工作负载，利用 Azure、 系统中心和 Windows 服务器的混合云平台和管理能力。 

的 **异地批量处理层** 模式是的一部分 **基础设施** 地区，奖励体系结构文档中详尽地说明了。 

##  异地批量处理层

异地批量处理层设计图案细节的蔚蓝功能和交付是这两个故障容错和可扩展的后端数据处理所需的服务。 这些服务在 Azure，目前可以部署到任何 Azure 数据中心上实现作为工人在云服务中的作用。   

批量处理工作负载是独特的他们通常会提供很少或没有用户界面。 这种类型在处所的例子就是工作量的在 Windows 服务器上运行的 Windows 服务。 当考虑到这种类型的工作负载在云环境中，这将浪费部署整个服务器来运行工作负载，当真正需要的是计算、 存储和网络连接。 辅助角色是在实施本在 Azure 上。 

根据定义，在 Azure 运行作业的批处理是工作量，连接到资源，提供一些业务逻辑 (计算)，并提供一些输出。 输入和输出资源由用户定义，可以从平面文件、 蔚蓝 blob 存储 blob，NoSQL 数据库或关系数据库的范围。   

实现业务逻辑是在蔚蓝的辅助角色，通常通过在.NET 库中定义所需的业务逻辑。 虽然部署到 Azure 的工作者角色是一个简单的操作，部署是故障容错和可扩展的工作者角色需要考虑服务是如何执行和维持 Azure 的内部设计。 这种模式将详细的设计考虑了这些要求，描述了如何实现这些。 

## 建筑模式概述 

本文档描述为异地批量处理利用工人角色实例包含在 Azure 云服务模式。 这种设计的关键组件如下所示。 此图说明了要实现容错的最低所需的实例。 可以部署额外实例，以增加服务的性能。 此外，自动缩放，可以协助按时间或其他服务器度量缩放实例启用。 

##  更多的资源
[批量处理层 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## 请参见
[奖励的体系结构](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[全局负载平衡的 Web 层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[负载平衡的数据层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[混合网络](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[蔚蓝搜索层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

