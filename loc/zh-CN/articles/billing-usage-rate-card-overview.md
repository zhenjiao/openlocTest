<properties
   pageTitle="Gain insights into your Microsoft Azure resource consumption"
   description="Provides a conceptual overview of the Azure Billing Usage and RateCard APIs, which are used to provide insights into Azure resource consumption and trends."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="07/7/2015"
   ms.author="mobandyo;bryanla"/>

# 洞察你微软 Azure 资源消耗 

客户和合作伙伴需要能够准确预测和管理他们蔚蓝的成本。 他们从资本支出将移到 Opex 模型，他们也需要做 showback 与计费分析以及提供模式富达估计和计费，特别是对于大的云部署的能力。 

Azure 资源的使用和率卡 Api 讨论在本文针对这些需求，通过启用蔚蓝资源你消费的新见解。  

## 引入的蔚蓝的资源使用情况和 RateCard Api 

Azure 资源使用和 RateCard Api 作为一个资源供应商，作为家庭公开由 Azure 资源管理器的 Api 的一部分实现。  

### 蔚蓝的资源使用 API (预览)
客户和合作伙伴可以使用 Azure 资源使用 API 来获取其估计的蔚蓝消费数据。功能包括:
	
- **蔚蓝的基于角色的访问控制** -客户和合作伙伴可以在上配置其访问策略 [天青预览门户](https://portal.azure.com) 或通过 [蔚蓝的 PowerShell cmdlet](powershell-install-configure.md) 若要指定哪些用户或应用程序可以访问订阅的使用数据。调用方必须使用标准的 Azure Active Directory 令牌进行身份验证。调用方还必须添加到要获取访问使用数据为特定蔚蓝订阅的读者，所有者或参与者角色。

- **每小时或每天聚合** -调用方可以指定是否他们想要他们中每小时水桶或日常水桶的蔚蓝使用率数据。默认值是每日。

- **提供的实例元数据 (包括资源标记)** — — 实例级别详细信息，例如完全限定的资源 uri (/subscriptions/ {订阅 id} /......)，以及资源组信息和资源标签将在响应中提供。这将帮助客户确定性和以编程方式分配使用的标记，为像跨充电的使用情况。

- **提供的资源元数据** -资源的详细信息，如表名称、 米类、 米子类别、 单位和地区也会通过在响应中，给调用者更好地理解什么消耗。我们也正在努力将资源元数据术语对齐整个蔚蓝的门户网站，Azure 使用 CSV，EA 计费 CSV 和其他面向公众的经验，使客户能够跨经验关联数据。

- **所有报价类型使用情况** – Usage data will be accessible for all offer types including Pay-as-you-go, MSDN, Monetary commitment, Monetary credit, and EA among others.

### Azure Resource RateCard API (Preview)
Customers and partners can use the Azure Resource RateCard API to get the list of available Azure resources, along with estimated pricing information for each. The features include:

- **蔚蓝的基于角色的访问控制** -客户和合作伙伴可以在上配置其访问策略 [天青预览门户](https://portal.azure.com) 或通过 [蔚蓝的 PowerShell cmdlet](powershell-install-configure.md) to specify which users or applications can get access to the RateCard data. Callers must use standard Azure Active Directory tokens for authentication. The caller must also be added to either the Reader, Owner or Contributor role to get access to the usage data for a particular Azure subscription.
	
- **Support for Pay-as-you-go, MSDN, Monetary commitment, and Monetary credit offers (EA not supported)** - This API provides Azure offer-level rate information, vs. subscription-level.  The caller of this API must pass in the offer information to get resource details and rates.  As EA offers have customized rates per enrollment, we are unable to provide the EA rates at this time.

## Scenarios

Here are some of the scenarios that are made possible with the combination of the Usage and the RateCard APIs:

- **Azure spend during the month** - Customers can use the Usage and RateCard APIs in combination to get better insights into their cloud spend during the month, by analyzing the hourly and daily buckets of usage and charge estimates. 

- **Set up alerts** – Customers and partners can set up resource-based or monetary-based alerts on their cloud consumption by getting the estimated consumption and charge estimate using the Usage and the RateCard API.

- **Predict bill** – Customers and partners can get their estimated consumption and cloud spend and apply machine learning algorithms to predict what their bill would be at the end of the billing cycle.

- **Pre-consumption cost analysis** – Customers can also use the RateCard API to predict how much their bill would be if they were to move their workloads to Azure, by providing desired usage numbers. If customers have existing workloads in other clouds or private clouds, they can also map their usage with the Azure rates to get a better estimate of their estimated Azure spend. This provides an enhanced view of what can be obtained via the [Azure Pricing Calculator](http://azure.microsoft.com/pricing/calculator/), as (for example) our Billing partners provide the ability to pivot on offer and compare/contrast between different offer types beyond Pay-As-You-Go, including Monetary commitment and Monetary credit. The APIs also provide the ability to do cost estimation changes by region, enabling the type of what-if analysis required to make deployment decisions, as deploying resources in different DCs around the world can have a direct impact on total cost.

- **What-if analysis** -

	- Customers and partners can determine whether it would be more cost-effective to run their workloads in another region, or on another configuration of the Azure resource. Azure resource costs may differ based on the Azure region in which they are running, and this allows customers and partners to get cost optimizations.

	- Customers and partners can also determine if another Azure offer type gives a better rate on an Azure resource.

## Partner solutions

[微软 Azure 的用法和 RateCard Api 使 Cloudyn 为客户提供独创](billing-usage-rate-card-partner-solution-cloudyn.md) describes the integration experience offered by Azure Billing API partner [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  This article provides detailed coverage of their experiences, including a short video which shows how an Azure customer can use Cloudyn and the Azure Billing APIs to gains insights from their Azure consumption data. 

[云的巡洋舰和微软 Azure 计费 API 集成](billing-usage-rate-card-partner-solution-cloudcruiser.md) describes how [Cloud Cruiser's Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) works directly from the  WAP portal, enabling customers to seamlessly manage both the operational and financial aspects of their Microsoft Azure private or hosted public cloud from a single user interface.   

## 接下来的步骤
+ 签出 [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) for more details on both APIs, which are part of the set of APIs provided by the Azure Resource Manager.
+ If you would like to dive right into the sample code, check out our [Microsoft Azure Billing API Code Samples on Github](https://github.com/Azure/BillingCodeSamples).

## Learn more
+ See the [Azure Resource Manager Overview](resource-group-overview.md) article to learn more about the Azure Resource Manager.
+ For additional information on the suite of tools necessary to help in gaining an understanding of cloud spend, please refer to  Gartner article [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


