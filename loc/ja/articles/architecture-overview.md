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

#Application Microsoft Azure アーキテクチャ
Microsoft Azure を使用するアプリケーションを構築するためのリソース。

##Azure 建築設計パターン
マイクロソフトは、独自のカスタム デザインを作成するためのアーキテクチャ設計パターンのシリーズを発行しています。簡潔にするためのパターンは、順序で一緒に構成することがでく建築ガイドは、最大限に活用する方法についてのガイダンスを提供する組織のビジネス ニーズを解決するためにマイクロソフト Azure プラットフォーム。


[概要](../azure-architectures-cpif-overview/) - 
[ハイブリッド ネットワーク](../azure-architectures-cpif-infrastructure-hybrid-networking/) - 
[オフサイト バッチ処理](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) -
[マルチサイトのデータ層](../azure-architectures-cpif-foundation-multi-site-data-tier/) -
[グローバル ロード バランスの取れた web 層](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) -
[紺碧検索層](../azure-architectures-cpif-foundation-azure-search-tier/)
 
各パターンが含まれています
 
- サービスの説明
- パターンを活用するために必要な Azure サービスの一覧
- アーキテクチャの図
- 建築の依存関係
- 設計上の制限やパターンに影響を与える可能性の考慮事項
- インターフェイスとエンドポイント
- アンチ パターン
- キーの高度なアーキテクチャの考慮事項が可用性と復元、使用されるサービスの複合 Sla、スケール、パフォーマンス、コスト、運用に関する考慮事項を含みます。

##Microsoft アーキテクチャを青写真します。

マイクロソフトは、Mirosoft 製品、Microsoft Azure サービス システムの特定の種類をビルドする方法を示す高レベルのアーキテクチャの青写真のセットを公開します。各設計図には Visio ベースの 2D ファイル ダウンロードし、変更にはが含まれています, よりカラフルな 3 D の PDF ファイル、青写真と 3 D 版を歩くビデオを紹介します。参照してください。 
[Microsoft アーキテクチャの青写真](http://msdn.microsoft.com/dn630664). 

2次元の設計図を使用して、クラウドと下記企業シンボルを設定します。  

Microsoft 以外のツールでは、3 D の設計図 Pdf が作成されますが、Visio テンプレートの開発中です。参照してください、 [ここでテンプレートのベータ版トレーニング ビデオ。](http://aka.ms/3dBlueprintTemplate).Visio 3 d 設計テンプレートのベータ版は、電子メールを取得するには [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). 

![Microsoft アーキテクチャ ・ ブルー プリント 3 D 図](./media/architecture-overview/BluePrintThumb.jpg)

##Cloud と企業シンボル/アイコン セット

[クラウドと企業シンボル/アイコンのセットをダウンロードします。](http://aka.ms/CnESymbols) Azure、Windows Server、SQL Server、他のマイクロソフト製品について説明する技術資料を作成します。本は、マイクロソフト製品を使用する人々 を訓練する場合、アーキテクチャ図、トレーニング資料、プレゼンテーション、データシート、インフォ グラフィック、ホワイト ペーパーなども第 3 パーティの書籍でそれらを使用できます。シンボルは、Visio と PNG 形式です。PowerPoint で、Png を使用する方法の手順が含まれます。 

シンボル セットは、四半期ごとに発送、新サービスがリリースされるに更新されます。場合は、最新のリリースでは、追加の Azure サービス、電子メールを含めることができますのプレビュー [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).  

ダウンロードでのフィードバックを提供するための説明がありますのであなたの考えを知っているしたいと思います。シンボルを使用した、ショートの 5 つの質問にご記入します。 [調査](http://aka.ms/azuresymbolssurveyv2) またはメールでお問い合わせ私たちは有用なかどうか知っているように上記のアドレスとどのようにそれらを使用します。  

追加のシンボルで利用できる、 [Microsoft Office Visio ステンシル](http://www.microsoft.com/en-us/download/details.aspx?id=35772)、CnE のセットのようなアーキテクチャの図には最適化されていませんが。  

![クラウドと企業シンボル/アイコン セット](./media/architecture-overview/CnESymbols.png)

##Design パターン
Microsoft パターンとプラクティスが本を出版しました。 [クラウド デザイン パターン](http://msdn.microsoft.com/library/dn568099.aspx) MSDN と pdf ファイルをダウンロードの両方利用可能であります。また、大判ポスター使用可能なすべてのパターンの一覧です。 

![パターンとプラクティス雲パターン ポスター](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Architecture インフォ グラフィック
マイクロソフトは、いくつかのアーキテクチャを発行し関連のポスター/インフォ グラフィック。彼らは含まれて [リアルなクラウド アプリケーションの構築](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) と [クラウド サービスとスケーリング](http://azure.microsoft.com/documentation/infographics/cloud-services/) . 

![Azure アーキテクチャ インフォ グラフィック](./media/architecture-overview/AzureArchInfographicThumb.jpg)
