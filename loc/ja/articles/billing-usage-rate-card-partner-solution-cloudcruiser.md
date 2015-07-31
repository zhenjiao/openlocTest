<properties
   pageTitle="Cloud Cruiser and Microsoft Azure Billing API Integration"
   description="Provides a unique perspective from Microsoft Azure Billing partner Cloud Cruiser, on their experiences integrating the Azure Billing APIs into their product.  This is especially useful for Azure and Cloud Cruiser customers that are interested in using/trying Cloud Cruiser for Microsoft Azure Pack."
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
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# クラウド ・ クルーザー、Microsoft Azure 課金 API の統合 

この記事では、クラウドのクルーザーで新しい Microsoft Azure 課金 Api から収集した情報を使用して、ワークフロー コスト シミュレーションと解析の方法について説明します。

## 紺碧 RateCard API
RateCard API は、Azure から速度情報を提供します。適切な資格情報を持つ認証、ID を提供に関連付けられている金利と一緒に azure で利用できるサービスに関するメタデータを収集するために API を照会できます。 

A0 の価格を示す API からの応答の例を以下です (Windows) インスタンス。

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## 雲の Azure RateCard API にクルーザーのインターフェイス
クラウドのクルーザーは、さまざまな方法で RateCard API 情報を活用できます。この記事の IaaS をするための使用方法を示すがワークロードのコストのシミュレーションと解析。

この使用例を示すためには、Microsoft Azure パック (WAP) に実行されているいくつかのインスタンスの作業負荷を想像してください。目的は、azure でこの同じ負荷をシミュレートし、そのような移行を行うためのコストを見積もる。このシミュレーションを作成するために 2 つの主要なタスクを実行するがあります。

1. **サービス情報が RateCard API から採集されたインポート ・ プロセス** -このタスクは、ブック、RateCard API からの抽出物は変換、新しい料金プランに公開でも実行されます。この新料金プランは、Azure の価格を推定するシミュレーションに使用されます。

2. **WAP サービスと Azure サービス IaaS を正規化します。** -デフォルトでは、WAP サービスが基づいている Azure ながら個々 のリソース (CPU、メモリのサイズ、ディスク ・ サイズ、等) のサービスのインスタンスのサイズ (A0、A1、A2 など) に基づいています。この最初のタスクはクラウド クルーザーの ETL エンジンによって実行されることをインスタンス サイズ、Azure インスタンス サービスに類似してこれらのリソースをバンドルすることができます、ブックと呼ばれます。

## RateCard API からデータをインポートします。

クラウド クルーザー ブックは、自動的に収集および RateCard API からの情報を処理する方法を提供します。 ETL (抽出、変換、読み込み) ブックでは、コレクション、変換、およびクラウド クルーザー データベースにデータの公開を構成することができます。

各ブックには、1 つまたは複数のコレクションを持つことができます。補完または利用状況のデータを拡張するさまざまなソースから情報を関連付けることができます。以下 2 つのスクリーン ショットの作成を表示します。 *コレクション* 既存のブックに情報をインポートするのには *コレクション* RateCard API: から

![Figure 1 - Creating a new collection][1]

![Figure 2 - Import data from the new collection][2]

After importing the data into the workbook, it’s possible to create multiple steps and transformation processes, to modify and model the data. For this example, since we are only interested in infrastructure-as-a-Service (IaaS,) we can use the transformation steps to remove unnecessary rows, or records, related to services other than IaaS.

The screenshot below shows the transformation steps used to process the data collected from RateCard API:

![Figure 3 - Transformation steps to process collected data from RateCard API][3]

## Defining New Services and Rate Plans

There are different ways to define services on Cloud Cruiser. One of the options is to import the services from the usage data. This method is commonly used when working with public clouds, where the services are already defined by the provider. 

A Rate Plan is a set of rates or prices that can be applied to different services, based on effective dates, or group of customers, among other options. Rate Plans can also be used on Cloud Cruiser to create simulation or “What-if” scenarios, to understand how changes in services can affect the total cost of a workload. 

In this example, we will use the service information from the RateCard API to define new services in Cloud Cruiser. In the same way, we can use the rates associated to the services to create a new Rate Plan on Cloud Cruiser.

At the end of the transformation process, it is possible to create a new step and publish the data from the RateCard API as new services and rates.

![Figure 4 - Publishing the data from the RateCard API as new Services and Rates][4]

## Verify Azure Services and Rates

After publishing the services and rates, you can verify the list of imported services in Cloud Cruiser’s *Services* tab:

![Figure 5 - Verifying the new Services][5]

On the *Rate Plans* tab, you can check the new rate plan called “AzureSimulation” with the rates imported from the RateCard API.

![Figure 6 - Verifying the new Rate Plan and associated rates][6]

## Normalize WAP and Azure Services

By default, WAP provides usage information based on the use of compute, memory and network resources. In Cloud Cruiser, you can define your services based directly on the allocation or metered usage of these resources. For example, you can set a basic rate for each hour of CPU usage, or charge the GB of memory allocated to an instance.

For this example, in order to compare costs between WAP and Azure, we need to aggregate the resource usage on WAP into bundles, which can then be mapped to Azure services. This transformation can be implemented easily in the workbooks:

![Figure 7 - Transforming WAP data to normalize services][7]

The last step at the workbook is to publish the data into the Cloud Cruiser database. During this step, the usage data is now bundled into services (that map to the Azure Services) and tied to default rates to create the charges.

After finishing the workbook, you can automate the processing of the data, by adding a new task on the scheduler and specifying the frequency and time for the workbook to run.

![Figure 8 - Workbook scheduling][8]

## Create Reports for Workload Cost Simulation Analysis

As the usage is collected and the charges are loaded into the Cloud Cruiser database, we can then leverage the Cloud Cruiser Insights module – an advanced analytics reporting tool – to create the workload cost simulation that we desire. 

In order to demonstrate this scenario, we created the following report: 

![Cost Comparison][9]

The top graph shows a cost comparison broken by services and compares the price of running the workload for each specific service between WAP (dark blue color) and Azure (light blue color). 

The bottom graph shows the same data but broken down by department, demonstrating the costs for each department to run their workload on WAP and Azure, along with the difference between these two – Savings bar (green color).

## 次のステップ

+ For detailed instructions on creating Cloud Cruiser workbooks and reports, please refer to Cloud Cruiser’s online [documentation](http://docs.cloudcruiser.com/) (valid login required).  For more information about Cloud Cruiser, please contact [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ See [Microsoft Azure リソースの消費量を把握します。](billing-usage-rate-card-overview.md) for an overview of the Azure Resource Usage and RateCard APIs. 
+ Check out the [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) for more information on both APIs, which are part of the set of APIs provided by the Azure Resource Manager.
+ If you would like to dive right into the sample code, check out our [Microsoft Azure Billing API Code Samples on Github](https://github.com/Azure/BillingCodeSamples).

## Learn More
+ See the [Azure Resource Manager Overview](resource-group-overview.md) article to learn more about the Azure Resource Manager.

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 - Creating a new collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 - Import data from the new collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 - Transformation steps to process collected data from RateCard API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 - Publishing the data from the RateCard API as new Services and Rates"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 - Verifying the new Services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 - Verifying the new Rate Plan and associated rates"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 - Transforming WAP data to normalize services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 - Workbook scheduling"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 - Sample Report for the Workload cost comparison scenario"