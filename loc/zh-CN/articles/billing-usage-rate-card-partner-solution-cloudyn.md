<properties
   pageTitle="Microsoft Azure Usage and RateCard APIs Enable Cloudyn to Provide ITFM for Customers"
   description="Provides a unique perspective from Microsoft Azure Billing partner Cloudyn, on their experiences integrating the Azure Billing APIs into their product.  This is especially useful for Azure and Cloudyn customers that are interested in using/trying Cloudyn for Azure Services."
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
   ms.date="06/14/2015"
   ms.author="mobandyo;bryanla"/>

# 微软 Azure 的用法和 RateCard Api 使 Cloudyn 为客户提供独创 

Cloudyn，微软的发展伙伴和领先的云管理功能，被选为新的微软 Azure 资源使用和 RateCard Api 的私人预览。 使用 API 提供订阅估计蔚蓝消费数据的访问。RateCard API 提供蔚蓝的所有服务，非企业协议 EA 客户完整的定价的信息。集成在一起，这些 Api 提供了一个完整的信息基础投入它金融管理 (独创) 工具，例如那些由 Cloudyn 提供。

## 介绍 

所谓的"乘法"的数据从使用 API 和数据从 RateCard API (用法 [单位] 价格[$unit] 详细的使用和成本 =) 创建最精细、 准确、 可靠计费信息可用 azure 今天。

![独创概述][1]

使用这些 Api 客户的使用情况和成本，使 Cloudyn 在一种简单、 编程的方式，分析客户帐户并执行各种独创任务为其客户提供关键信息。 

## 将 Cloudyn 与 RateCard 和使用 Api 集成
The RateCard API requires several input parameters -- like region info, currency and locale -- but the most important one is OfferDurableID, which specifies the type of Azure offering the customer is using (Pay-as-you-Go, legacy 6 and 12-month commitment plans, MSDN offers, MPN offers, promotional offers and others). The OfferDurableID can be found in the [Azure Usage and Billing portal](https://account.windowsazure.com/Subscriptions), under the "Offer ID" for the given subscription. 

Upon registration for [Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) services, customers can add their OfferDurableID code, which allows Cloudyn to pull their relevant pricing information through the RateCard API.  Information on the different types of offers can be found one the [Microsoft Azure Offer Details](http://azure.microsoft.com/en-gb/support/legal/offer-details/) page.

![Cloudyn ITFM Engine Overview][2]

Cloudyn uses both the Usage and RateCard APIs, in addition to the Azure Performance API, to create additional layers of visualization, analytics, alerting, reporting, cost management and actionable recommendations, providing Azure customers a reliable enterprise cloud ITFM tool.

## Cloudyn ITFM use cases enabled by Usage and RateCard API integration 
Common Cloudyn ITFM use cases enabled by usage and RateCard APIs include:

+ **Cost Analysis** - Allows cloud costs to be broken down to any native identifying dimension (provider, service, account, region etc.). The Azure Usage and RateCard APIs make this an easy task, by providing the most granular breakdown of usage and cost data per account, which is then grouped and filtered by Cloudyn and presented to the user, in a graphic or tabular form.

![Cost Analysis Pie Chart][3]

+ **Cost Allocation 360** - Enables finance and IT managers to uncover the actual cost breakdown, drivers and trends of their cloud deployment. It further allows managers to easily associate deployment expenses with business units, departments, regions, and more, providing unprecedented insights into cloud costs, and facilitating enterprise chargebacks and showbacks. The Azure Usage and RateCard APIs serve as input to Cloudyn’s cost allocation engine, which complements the APIs by defining methods and business logic for allocating untagged or untaggable resources.

![Cost Allocation 360 Chart][4]

+ **Cost-Effective Sizing** - Provides right-sizing recommendations for underutilized virtual machines, thus reducing the customer’s expenses on oversized or over-provisioned machines. It does so by examining virtual machine CPU and RAM metrics (via Performance API), hours of run-time (via Usage API) and cost (via RateCard API). Cloudyn then provides right-sizing recommendations based on underutilized CPU or RAM resources (Performance), and calculates estimated savings by multiplying the price delta (RateCard) between the VMs by the actual time-utilization (Usage) of the underutilized machine. 

![Cost Effective Sizing][5]

+ **Cloud Porting Recommendations** - Provides financial advice on cloud porting. It examines a user's current costs of cloud resources which are deployed on major cloud vendors, and compares it to the cost of an equivalent deployment on Azure. It then provides granular, per-resource, financially-based porting recommendations to Azure. After assessing the equivalent deployment required on Azure (based on performance metrics and user preferences), Cloudyn uses the RateCard API to evaluate the cost of the equivalent deployment on Azure.

+ **Performance Reports** - Enabled by Azure’s performance API, these reports provide an array of features from CPU and RAM utilization to optimization recommendations. Below is an instance utilization report example, presenting instance breakdown by average CPU utilization.

![Performance Reports][6]

+ **Category manager** - A powerful feature in Cloudyn that brings order to unorganized cloud resources. It provides users the freedom to create their own unique categories (tags) for effective measuring and reporting that is in line with business practices. Further, users can easily regulate and categorize inconsistent tagging (i.e. typos and other discrepancies) and automatically detect untagged resources for accurate cost attribution.

![Category Manager][7]

## Video 

Here's a short video which shows how an Azure customer can use Cloudyn for Azure and the Azure Billing APIs, to gain insights from their Azure consumption data.
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## 接下来的步骤

+ Start a free [Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) trial to see how you can obtain cost transparency with customized reporting and analytics for your Microsoft Azure cloud deployment.
+ See [洞察你微软 Azure 资源消耗](billing-usage-rate-card-overview.md) for an overview of the Azure Resource Usage and RateCard APIs. 
+ 签出 [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) for more information on both APIs, which are part of the set of APIs provided by the Azure Resource Manager.
+ If you would like to dive right into the sample code, check out our [Microsoft Azure Billing API Code Samples on Github](https://github.com/Azure/BillingCodeSamples).

## Learn More
+ To learn more about Microsoft Azure Enterprise Agreement (EA) offers, please visit [Licensing Azure for the Enterprise] (http://azure.microsoft.com/pricing/enterprise-agreement/)
+ See the [Azure Resource Manager Overview](resource-group-overview.md) article to learn more about the Azure Resource Manager.
+ For additional information on the suite of tools necessary to help in gaining an understanding of cloud spend, please refer to  Gartner article [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png