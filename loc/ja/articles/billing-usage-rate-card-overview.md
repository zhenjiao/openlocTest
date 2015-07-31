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

# Microsoft Azure リソースの消費量を把握します。 

顧客やパートナーを正確に予測し、Azure コスト管理能力が必要です。 彼らは、設備投資から運用モデルに移動とチャージ バック分析、対ショーバックを行うだけでなく、推定および計算、特に大規模なクラウド展開のモード忠実度を提供する機能を必要があります。 

Azure リソース使用量とレート カード Api で説明この記事のアドレス、これらのニーズ Azure のリソースの消費に新しい洞察力を有効にします。  

## Azure のリソースの使用状況と RateCard Api を導入 

Azure リソースの使用状況と RateCard Api は、Azure リソース マネージャーによって公開されている Api の家族の一員として、リソース プロバイダーとして実装されます。  

### Azure のリソース使用状況 API (プレビュー)
顧客やパートナーは、Azure 消費データを取得するのに Azure リソース使用状況 API を使用できます。機能があります。
	
- **Azure のロール ベースのアクセス制御** お客様やパートナーに彼らのアクセス ポリシーを構成できます、 [紺碧プレビュー ポータル](https://portal.azure.com) 使用するか、 [紺碧の PowerShell コマンドレット](powershell-install-configure.md) ユーザーまたはアプリケーションを指定するのには、サブスクリプションの利用状況データへのアクセスを得ることができます。呼び出し元は、認証の標準の Azure Active Directory トークンを使用する必要があります。呼び出し元は、特定の Azure サブスクリプションの利用状況データへのアクセスを取得するリーダー、所有者または共同作成者ロールにも追加する必要があります。

- **毎時または毎日集計** -呼び出し元は、1 時間ごとバケツや毎日バケツで Azure の使用データかどうかを指定できます。既定では、毎日。

- **インスタンスのメタデータが提供 (リソース タグが含まれています)** -インスタンス レベルの完全修飾リソース uri など詳細 (/subscriptions/{サブスクリプション id}/.)、リソースと共にグループの情報とリソースのタグは、応答で提供されるでしょう。これは確定的にお客様を支援し、クロス充電使用例のように、タグ使用をプログラムで割り当てます。

- **リソースのメタデータが提供** メーター名、計カテゴリ、メーターなどのリソースの詳細サブ カテゴリ、単位や地域も呼び出し元に消費されたものの理解を与えるへの応答で渡されます。また、Azure ポータル全体リソース メタデータ用語を合わせて取り組んでいます Azure 利用 CSV、EA 請求 CSV および他の一般向けの体験経験の間でデータを相互に顧客を有効にします。

- **すべてのオファーの種類用途** – Usage data will be accessible for all offer types including Pay-as-you-go, MSDN, Monetary commitment, Monetary credit, and EA among others.

### Azure Resource RateCard API (Preview)
Customers and partners can use the Azure Resource RateCard API to get the list of available Azure resources, along with estimated pricing information for each. The features include:

- **Azure のロール ベースのアクセス制御** お客様やパートナーに彼らのアクセス ポリシーを構成できます、 [紺碧プレビュー ポータル](https://portal.azure.com) 使用するか、 [紺碧の PowerShell コマンドレット](powershell-install-configure.md) to specify which users or applications can get access to the RateCard data. Callers must use standard Azure Active Directory tokens for authentication. The caller must also be added to either the Reader, Owner or Contributor role to get access to the usage data for a particular Azure subscription.
	
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

[Microsoft Azure 利用と RateCard Api 向け ITFM を提供する Cloudyn を有効にします。](billing-usage-rate-card-partner-solution-cloudyn.md) describes the integration experience offered by Azure Billing API partner [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  This article provides detailed coverage of their experiences, including a short video which shows how an Azure customer can use Cloudyn and the Azure Billing APIs to gains insights from their Azure consumption data. 

[クラウド ・ クルーザー、Microsoft Azure 課金 API の統合](billing-usage-rate-card-partner-solution-cloudcruiser.md) describes how [Cloud Cruiser's Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) works directly from the  WAP portal, enabling customers to seamlessly manage both the operational and financial aspects of their Microsoft Azure private or hosted public cloud from a single user interface.   

## 次のステップ
+ Check out the [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) for more details on both APIs, which are part of the set of APIs provided by the Azure Resource Manager.
+ If you would like to dive right into the sample code, check out our [Microsoft Azure Billing API Code Samples on Github](https://github.com/Azure/BillingCodeSamples).

## Learn more
+ See the [Azure Resource Manager Overview](resource-group-overview.md) article to learn more about the Azure Resource Manager.
+ For additional information on the suite of tools necessary to help in gaining an understanding of cloud spend, please refer to  Gartner article [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


