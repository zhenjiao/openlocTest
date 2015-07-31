<properties 
   pageTitle="Cloud Platform Integration Framework - Azure Architecture Patterns" 
   description="The Cloud Platform Integration Framework provides workload integration guidance for onboarding applications into a Microsoft Cloud Solution consisting of architectural patterns for Microsoft Azure" 
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


# 云平台集成框架 (Azure 架构模式)

云平台集成框架 (下属) 提供入职到微软云计算解决方案的应用程序的工作量集成指导。 

奖励介绍如何组织、 客户和合作伙伴应设计和部署云有针对性的工作负载，利用 Azure、 系统中心和 Windows 服务器的混合云平台和管理能力。下属域有被分解为以下功能:

![资源和资源组叶片的标签部分](./media/azure-architecture-cpif-overview/overview.png)

##  奖励的体系结构

这两个公共云和私有云环境提供共同的元素来支持复杂的工作负载运行。虽然这些体系结构在传统上处所物理和虚拟环境中相对较好的理解，发现微软 Azure 的内部的构造需要更多的规划，以合理化的基础设施和平台的功能，发现内公共云环境。要在 Azure 支持的托管应用程序或服务的发展，一系列的模式则需要概述撰写给定工作负载的解决方案所需的各种组件。  

这些建筑的模式属于以下类别:

- **基础设施**— — 微软 Azure 是由组成的几个基本的服务和功能的基础设施和平台-作为-服务解决方案。 这些服务很大程度上可以分解为计算、 存储和网络服务，但是有几个功能可能不属于这些定义。 基础设施模式详细介绍微软 Azure 的向内给定蔚蓝订阅承载一个或多个解决方案提供某项服务所需的特定功能区。 
- **基础** — — 当构成的多层应用程序或服务在 Azure 内的，几个组件必须用于组合提供一个合适的宿主环境。 基础模式构成从微软 Azure 支持给定的层的功能在应用程序中的一个或多个服务。这可能需要在上文所述的基础设施模式描述的一个或多个组件的使用。例如，表示层的多层应用程序需要计算、 网络和存储的功能在 Azure 内成为功能。 基础模式注定会组成与其他模式作为给定的解决方案的一部分。
- **解决方案** — — 解决方案模式分为基础设施和/或基础的模式，以代表结束应用程序或服务正在开发。 这被设想表示复杂的解决方案，不会独立于其他模式发展。 相反，他们应该利用组件和接口定义每个上述模式类别中。    

## 蔚蓝的建筑模式概念

若要支持在 Azure 的解决方案体系结构的发展，为常见方案提供了一系列模式指南。  虽然这些方案指南将兴建随着时间的推移，所设想的模式包括:

- 全局负载平衡的 Web 层 
- 负载平衡的数据层
- 批量处理层
- 混合网络
- 蔚蓝的搜索 

##  更多的资源
[奖励的体系结构 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

## 请参见
[全局负载平衡的 Web 层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[负载平衡的数据层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[批量处理层](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[蔚蓝网络](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[蔚蓝的搜索](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)
