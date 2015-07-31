<properties 
   pageTitle="Offsite Batch Processing Tier (Azure Architecture Patterns)" 
   description="The Offsite Batch Processing Tier pattern is part of the Infrastructure area, which is described extensively in the CPIF Architecture document." 
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

# オフサイト バッチ処理の層 (Azure アーキテクチャ パターン)

、 [クラウド プラットフォーム統合フレームワーク (CPIF)](azure-architectures-cpif-overview.md) マイクロソフトのクラウド ソリューションにアプリケーションのワークロード統合ガイダンスを提供します。  

CPIF では、どのように組織、顧客およびパートナーの設計および Windows Server System Center、Azure のハイブリッド クラウド プラットフォームと管理機能を活用したクラウド向けのワークロードを展開をについて説明します。 

、 **オフサイト バッチ処理層** パターンの一部である、 **インフラストラクチャ** エリア、CPIF アーキテクチャ ドキュメントで詳しく解説されています。 

##  オフサイト バッチ処理層

オフサイト バッチ処理層デザイン パターン詳細 Azure 機能およびサービス両方のフォールト トレランスと拡張性の高いバックエンド データ処理を実現します。 これらのサービスは、azure で現在、Azure データ センターに展開できるクラウド サービスのワーカー ロールとして実現されています。   

バッチ処理などのワークロードは、彼らは通常、ほとんど、あるいは全くのユーザー インターフェイスを提供ユニークなです。 敷地内の負荷のこのタイプの例は、Windows Server で実行している Windows サービスでしょう。 クラウド環境におけるワークロードのこのタイプを検討する際は、計算、ストレージとネットワークの接続性は、何が本当に必要なときに、ワークロードを実行する全体のサーバーを展開する無駄になります。 ワーカー ロールは、Azure でこれの実装です。 

定義、Azure で実行されるジョブを処理バッチはワークロード リソースに接続する、(コンピューティング) いくつかのビジネス ロジックを提供し、いくつかの出力を提供します。 入力リソース呼び出し力リソースは、ユーザーによって定義され、フラット ファイル、Azure のブロブ ストレージ内の blob、NoSQL データベースまたはリレーショナル データベースから範囲で指定できます。   

ビジネス ロジックは、.NET ライブラリに必要なビジネス ロジックを定義することによって通常 Azure のワーカー ロールで実装されます。 Azure ワーカーの役割の展開は、簡単な操作ですが、フォールト トレランスと拡張性は、worker ロールを展開するサービスを実行して Azure 内で管理する方法に配慮した設計が必要です。 このパターンは、これらを実装する方法について説明し、これらの要件を考慮したデザインを詳しく説明します。 

## アーキテクチャ パターンの概要 

このドキュメントでは、Azure のホステッド サービス内に含まれるワーカー ロール インスタンスを利用したオフサイト バッチ処理のパターンについて説明します。 この設計の重要なコンポーネントは次のとおりです。 この図では、フォールト トレランスを達成するために最低限必要なインスタンスを示しています。 追加のインスタンスは、サービスのパフォーマンスを向上させるのに配置できます。 また、自動スケーリングは時間または追加のサーバーのメトリックによってインスタンスをスケールを支援する有効にすることができます。 

##  追加のリソース
[バッチ処理層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## また見なさい
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[グローバル ロード バランスの取れた Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[負荷バランスの取れたデータ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[紺碧検索層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

