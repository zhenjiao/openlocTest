<properties 
   pageTitle="Cloud Platform Integration Framework - Azure Architecture Patterns" 
   description="The Cloud Platform Integration Framework provides workload integration guidance for onboarding applications into a Microsoft Cloud Solution consisting of architectural patterns for Microsoft Azure" 
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


# クラウド プラットフォーム統合フレームワーク (Azure アーキテクチャ パターン)

クラウド プラットフォーム統合フレームワーク (CPIF) は、マイクロソフトのクラウド ソリューションにアプリケーションのワークロード統合ガイダンスを提供します。 

CPIF では、どのように組織、顧客およびパートナーの設計および Windows Server System Center、Azure のハイブリッド クラウド プラットフォームと管理機能を活用したクラウド向けのワークロードを展開をについて説明します。CPIF ドメインは、次の機能に分解されてしまった。

![リソースおよびリソース グループのブレードにタグ部分](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF アーキテクチャ

両方のパブリックおよびプライベート クラウド環境では、複雑なワークロードの実行をサポートするための共通の要素を提供します。これらのアーキテクチャは、従来のオンプレミス環境物理および仮想環境で比較的よく理解されて、Microsoft Azure 内の構成体圏内にパブリック クラウド環境におけるインフラストラクチャとプラットフォームの機能を合理化するための追加の計画が必要です。Azure でホストされるアプリケーションやサービスの開発をサポートする一連のパターンが特定のワークロードのソリューションを作成するために必要なさまざまなコンポーネントの概要が必要です。  

これらのアーキテクチャ パターンは、次のカテゴリに分類します。

- **インフラストラクチャ**-マイクロソフト Azure は、いくつかの基になるサービスと機能から構成されるインフラストラクチャとプラットフォーム-として-サービス ソリューションです。 これらのサービスは、コンピューティング、ストレージ、ネットワーク サービス、これらの定義に当てはまらない可能性がありますいくつかの機能は、しかし、そこに主分解できます。 基盤パターンは、与えられた Azure サブスクリプション内でホストされる 1 つ以上のソリューションに特定のサービスを提供するために必要な Microsoft Azure の特定の機能領域を詳しく説明します。 
- **財団** -多階層のアプリケーションを Azure 内のサービスを構成するときいくつかのコンポーネントは適切なホスティング環境を提供する組み合わせで使用する必要があります。 基盤のパターンは、アプリケーション内の機能の特定のレイヤーをサポートする Microsoft Azure から 1 つまたは複数のサービスを作成します。これは上記基盤パターンで記述されている 1 つまたは複数のコンポーネントの使用を必要があります。たとえば、マルチ層アプリケーションのプレゼンテーション層には、機能するために Azure 内計算、ネットワーク、ストレージ機能が必要です。 基盤パターンは、特定のソリューションの一部として他のパターンで構成するものです。
- **ソリューション** -ソリューション パターン エンド アプリケーションを表すまたは開発中サービスのインフラストラクチャおよび/または財団のパターンで構成されます。 複雑なソリューションは、他のパターンとは無関係ない開発されるだろうと想定されます。 むしろ、コンポーネントと上記パターンのカテゴリのそれぞれに定義されているインタ フェースを活用しました。    

## Azure アーキテクチャ パターンの概念

Azure 内のソリューション ・ アーキテクチャの開発をサポートするためパターン ガイドのシリーズは、一般的なシナリオの提供しています。  これらのシナリオのガイドが時間をかけて建設され、想定パターンを含めます。

- グローバル ロード バランスの取れた Web 層 
- 負荷バランスの取れたデータ層
- バッチ処理層
- ハイブリッド ネットワーク
- 紺碧の検索 

##  追加のリソース
[CPIF アーキテクチャ (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

## また見なさい
[グローバル ロード バランスの取れた Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[負荷バランスの取れたデータ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[紺碧のネットワー キング](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[紺碧の検索](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)
