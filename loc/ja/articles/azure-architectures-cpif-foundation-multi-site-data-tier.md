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

# マルチサイト ・ データ層 (Azure アーキテクチャ パターン)

、 [クラウド プラットフォーム統合フレームワーク (CPIF)](azure-architectures-cpif-overview.md) マイクロソフトのクラウド ソリューションにアプリケーションのワークロード統合ガイダンスを提供します。 

CPIF では、どのように組織、顧客およびパートナーの設計および Windows Server System Center、Azure のハイブリッド クラウド プラットフォームと管理機能を活用したクラウド向けのワークロードを展開をについて説明します。 

、 **マルチサイトのデータ層** パターンの一部である、 **財団** エリア、CPIF アーキテクチャ ドキュメントで詳しく解説されています。 

## マルチサイトのデータ層

マルチサイトのデータ層は、Azure の機能および地理的な境界の間で予測可能なパフォーマンスと高可用性を提供することができますデータ層サービスを提供するために必要なサービス パターン詳細を設計します。このデザイン パターンのデータ層は分離された方法のいずれかで伝統的なデータ プラットフォーム サービスを提供するサービスの層または多層アプリケーションの一部として定義されています。 このパターン内でデータ層の負荷分散は、共にローカル地域内および地域間で。   

SQL Server 2012 で導入された、AlwaysOn 可用性グループは、Azure インフラストラクチャ サービスで完全にサポートされている高可用性と災害復旧機能です。 詳細な情報と Windows Azure インフラストラクチャ サービスの AlwaysOn 可用性グループの公式サポート発表は、AlwaysOn 可用性グループの記事で見つけることが。   

このドキュメントは、SQL AlwaysOn 可用性グループを利用した Azure でマルチサイトのデータ層のアーキテクチャの概要を提供します。省略可能な読み取り専用セカンダリ ノードに追加機能および災害復旧用の追加 Azure データ センターで。Azure の SQL の AlwaysOn を使用して web アプリケーションまたはアプリケーション階層で使用できる高可用性のデータ層を提供します。  

このドキュメントはアーキテクチャ パターンとプラクティスに焦点を当てて、フル展開の手引き Azure の AlwaysOn 可用性グループの構成および AlwaysOn 可用性グループ リスナーの構成の概要公式のチュートリアルで見つけることができます。 

## アーキテクチャ パターンの概要 

このドキュメントでは、可用性と冗長性のため複数の地域のマイクロソフト SQL サーバーのコンテンツへのアクセスを提供するためのパターンについて説明します。 重要なサービスは、アプリケーションやデータ自体にアクセスする web 層に注意せず次に示します。 次の図は、関連サービスとこのパターンの一部としての使用方法を単純な図です。   

それぞれの主要なサービス エリアの詳細については次の図のとおりです。 
 
![リソースおよびリソース グループのブレードにタグ部分](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  追加のリソース
[負荷分散されたデータ層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## また見なさい
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[グローバル ロード バランスの取れた Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[紺碧検索層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
