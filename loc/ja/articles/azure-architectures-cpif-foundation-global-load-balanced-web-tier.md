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

# グローバル ロード バランスの取れた Web 層 (Azure アーキテクチャ パターン)

、 [クラウド プラットフォーム統合フレームワーク (CPIF)](azure-architectures-cpif-overview.md) マイクロソフトのクラウド ソリューションにアプリケーションのワークロード統合ガイダンスを提供します。 

CPIF では、どのように組織、顧客およびパートナーの設計および Windows Server System Center、Azure のハイブリッド クラウド プラットフォームと管理機能を活用したクラウド向けのワークロードを展開をについて説明します。 

、 **グローバル ロード バランスの取れた Web 層** パターンの一部である、 **財団** エリア、CPIF アーキテクチャ ドキュメントで詳しく解説されています。 

##  グローバル ロード バランスの取れた Web 層

Azure 機能および地理的な境界の間で予測可能なパフォーマンスと高可用性を提供することができます web 層サービスを提供するために必要なサービス、グローバル負荷分散された Web 層デザイン パターン詳細です。 

このデザイン パターン web の目的のため層は、分離された方法でまたは多階層の web アプリケーションの一部として伝統的な HTTP/HTTPS コンテンツを提供するサービスまたはアプリケーション サービス層として定義されます。 このパターン内で web 層の負荷分散は、共にローカル地域内および地域間で。計算の観点からマイクロソフト Azure バーチャル マシン、web サイトまたは両方の組み合わせを介してこれらのサービスを提供することができます。 Web サービスを提供する仮想マシンは、サポートされている Microsoft Windows または Linux ディストリビューション ゲスト オペレーティング システム Microsoft Azure 内を使用してコンテンツをホストできます。 


## アーキテクチャ パターンの概要 

このドキュメントでは、可用性と冗長性のための複数の地域 web サービスまたは web サーバーのコンテンツへのアクセスを提供するためのパターンについて説明します。 Web プラットフォームの制約または web サービス内で開発手法に注意せず重要なサービスを以下に示します。 このパターンは-web コンテンツまたは仮想マシン上のサービスをホストしている 1 つに 2 つのバリエーションがあります (オペレーティング システムおよび家族サポート Azure を使用) と Azure の web サイトを使用するもの。 次の図は、関連サービスと仮想マシンの例を使用してこのパターンの一部としての使用方法を単純な図です。   

##  追加のリソース
[グローバル負荷分散された Web の層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

## また見なさい
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[負荷バランスの取れたデータ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[紺碧検索層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

