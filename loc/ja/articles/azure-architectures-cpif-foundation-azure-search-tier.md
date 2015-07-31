<properties 
   pageTitle="Azure Search Tier (Azure Architecture Patterns)" 
   description="The Azure Search Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document" 
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

# 紺碧検索層 (Azure アーキテクチャ パターン)

、 [クラウド プラットフォーム統合フレームワーク (CPIF)](azure-architectures-cpif-overview.md) マイクロソフトのクラウド ソリューションにアプリケーションのワークロード統合ガイダンスを提供します。  

CPIF では、どのように組織、顧客およびパートナーの設計および Windows Server System Center、Azure のハイブリッド クラウド プラットフォームと管理機能を活用したクラウド向けのワークロードを展開をについて説明します。 

、 **紺碧検索層** パターンの一部である、 **財団** エリア、CPIF アーキテクチャ ドキュメントで詳しく解説されています。 

##  紺碧検索層

Azure 検索層デザイン パターン詳細 Azure 機能および地理的な境界の間で予測可能なパフォーマンスと高可用性を提供することができます、アーキテクチャ パターンは、Azure 探索を用いた検索ソリューションを提供する検索サービスを提供するために必要なサービス。 紺碧の検索は「検索-として-、-サービス」展開、することなくアプリケーションに検索機能を組み込む開発者ことができます Microsoft Azure 内で構築された管理またはアプリケーションにこの機能を提供するインフラストラクチャ サービスを維持します。このパターンの目的は、設計のさまざまな状況での使用を対象として再現性のあるソリューションを提供することです。 

## アーキテクチャ パターンの概要 

このドキュメントでは、Azure 検索コアの 2 つのバリエーションを用いたサービスの建築の範囲を示すためのコアのパターンについて説明します。 コア パターンは Azure 検索と周囲の Azure サービスから成り、エンド-エンドのデザインを作成するためのガイダンスを提供するものです。 パターン、特にサービスの共有と同時実行パターンのバリエーションもさまざまな要件、サービス レベル契約 (SLA) およびその他の特定の条件に基づいて指導を提供するためにここに含まれます。 

##  追加のリソース
[紺碧の検索層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

## また見なさい
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[グローバル ロード バランスの取れた Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[負荷バランスの取れたデータ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
