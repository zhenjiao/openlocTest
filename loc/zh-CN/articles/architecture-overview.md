<properties 
	pageTitle="Application Architecture on Microsoft Azure" 
	description="Architecture overview that covers common design patterns" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

在微软 Azure 上 #Application 体系结构
建筑使用微软 Azure 应用程序的资源。

##Azure 建筑设计模式
Microsoft 发布的建筑设计模式，以帮助您撰写您自己的自定义设计的系列。模式的目的是要简明建筑指南，可以共同组成，按顺序提供了指导如何以最佳方式利用微软 Azure 平台，解决您组织的业务需求。


[概述](../azure-architectures-cpif-overview/) - 
[混合网络](../azure-architectures-cpif-infrastructure-hybrid-networking/) - 
[异地批处理](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) -
[多站点数据层](../azure-architectures-cpif-foundation-multi-site-data-tier/) -
[全局负载平衡的 web 层](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) -
[蔚蓝搜索层](../azure-architectures-cpif-foundation-azure-search-tier/)
 
每个模式包含
 
- 服务描述
- Azure 服务利用模式所需的列表
- 架构图
- 架构的依赖项
- 设计限制或可能影响模式的思考
- 接口和终结点
- 反模式
- 关键的高层建筑考虑因素包括可用性和可恢复性、 复合 Sla 使用服务、 规模和性能、 成本和操作方面的注意事项。

##Microsoft 体系结构蓝图

Microsoft 发布了一套高水平的建筑设计图显示如何生成特定类型的系统包括预言产品和微软 Azure 服务。每个蓝图包括 2D 的基于 Visio 的文件，您可以下载和修改、 更加丰富多彩的 3D PDF 文件介绍，蓝图和走过的 3D 版本的视频。请参见 
[微软体系结构蓝图](http://msdn.microsoft.com/dn630664). 

2D 的蓝图图使用云计算和企业符号设置下面提到。  

在一个非 Microsoft 工具中创建 3D 蓝图 Pdf 但 Visio 模板正在发展。请参见 [Beta 版培训视频的模板在这里。](http://aka.ms/3dBlueprintTemplate).若要获取 Visio 3d 蓝图模板测试版，通过电子邮件发送 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). 

![微软架构蓝图 3D 图](./media/architecture-overview/BluePrintThumb.jpg)

##Cloud 和企业的符号图标集

[下载的云计算和企业的符号图标集](http://aka.ms/CnESymbols) 若要创建描述 Azure、 Windows 服务器、 SQL Server 和其他 Microsoft 产品的技术资料。如果这本书训练人们使用微软的产品，你可以使用他们在体系结构关系图、 培训材料、 演示文稿、 数据表、 数据图表、 白皮书和甚至第三方书。符号是在 Visio 和 PNG 格式。有关如何在 PowerPoint 中使用 Png 说明是包括在内。 

符号集船舶每季度和更新，并根据发布了新的服务。如果你想要的最新的发行版，其中可能包括额外的 Azure 服务，电子邮件预览 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).  

我们想知道你怎么想的所以有指令，提供下载的反馈。如果你已经使用过的符号，填写短 5 问题 [调查](http://aka.ms/azuresymbolssurveyv2) 或给我们发电子邮件在上面，让我们知道他们是否有用的地址以及您如何使用它们。  

其他符号可用在 [微软 Office Visio 模具](http://www.microsoft.com/en-us/download/details.aspx?id=35772)虽然它们不适合像 CnE 集的架构图。  

![云计算和企业的符号图标集](./media/architecture-overview/CnESymbols.png)

##Design 模式
微软模式和实践已出版了这本书 [云的设计模式](http://msdn.microsoft.com/library/dn568099.aspx) 这是可在 MSDN 上和 PDF 下载中。也是大幅面的海报可用其中列出的所有模式。 

![模式和做法云模式海报](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Architecture 图表
Microsoft 发布了几种体系结构相关海报/图表。他们包括 [建立真实世界云应用程序](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) 和 [缩放与云服务](http://azure.microsoft.com/documentation/infographics/cloud-services/) . 

![Azure 架构信息图表](./media/architecture-overview/AzureArchInfographicThumb.jpg)
