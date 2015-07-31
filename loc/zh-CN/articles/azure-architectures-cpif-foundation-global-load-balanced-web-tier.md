<properties 
   pageTitle="Global Load Balanced Web Tier (Azure Architecture Patterns)" 
   description="The Global Load Balanced Web Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document." 
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

# 全局负载平衡的 Web 层 (Azure 架构模式)

的 [云平台集成框架 (奖励)](azure-architectures-cpif-overview.md) 提供入职到微软云计算解决方案的应用程序的工作量集成指导。 

奖励介绍如何组织、 客户和合作伙伴应设计和部署云有针对性的工作负载，利用 Azure、 系统中心和 Windows 服务器的混合云平台和管理能力。 

的 **全局负载平衡的 Web 层** 模式是的一部分 **基础** 地区，奖励体系结构文档中详尽地说明了。 

##  全局负载平衡的 Web 层

全球负载平衡 Web 层设计图案细节的蔚蓝功能和提供可以跨越地理边界提供可预测的性能和高可用性的 web 层服务所需的服务。 

出于这种设计模式的 web 层定义作为服务提供传统的 HTTP/HTTPS 内容或应用服务层，要么隔绝的方式或作为多层 web 应用程序的一部分。 此模式中负载平衡的 web 层提供两局部区域内和跨区域。从计算的角度来看，这些服务可以提供通过微软 Azure 虚拟机、 web 站点或两者的组合。 提供 web 服务的虚拟机可以承载使用任何支持的 Microsoft Windows 或 Linux 分布来宾操作系统微软 Azure 的内部内容。 


## 建筑模式概述 

本文档描述为在多个地域的可用性和冗余目的提供对 web 服务或 web 服务器内容的访问模式。 关键服务都没有注意到 web 平台的限制或 web 服务本身内的开发方法，如下所示。 有两种变到这种模式 — — 一种承载 web 内容或在虚拟机上的服务 (使用 Azure 支持操作系统和家庭) 和其中使用 Azure 网站。 下面的图表是一个简单的例子有关的服务以及如何使用它们是作为这种模式使用虚拟机的示例的一部分。   

##  更多的资源
[全球负载平衡 Web 层 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

## 请参见
[奖励的体系结构](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[负载平衡的数据层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[混合网络](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[蔚蓝搜索层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[批量处理层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

