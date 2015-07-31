<properties
	pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints"
	description="Provides a list of common Azure subscription and service limits, quotas, and constraints. This includes information on how to increase limits along with maximum values."
	services=""
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettem"
	editor="cgronlun"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# Azure サブスクリプションとサービスの制限、クォータ、および制約

## 概要

このドキュメントでは、最も一般的な Microsoft Azure 制限のいくつかを指定します。紺碧のすべてのサービスを現在カバーはないこれに注意してください。時間をかけて、これらの制限を拡張・ プラットフォームの詳細をカバーするように更新されます。

> [AZURE。メモ] 上記の制限を上げたい場合、 **既定の制限値**できます [無償でオンライン顧客サポート依頼を開きます](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).上記の限界を上げることができません、 **最大数の制限** 次の表の値です。あるかどうかないです。 **最大数の制限** 列、し、指定されたリソースは、調整可能な制限はありません。


### 制限と Azure のリソース マネージャー

今、単一の Azure リソース グループに複数の Azure リソースを組み合わせることが可能です。リソース グループを使用すると、グローバルはかつて制限が地域レベルで Azure リソース マネージャーで管理不能になります。Azure リソース グループの詳細については、次を参照してください。 [Azure のリソースを管理するのにリソース グループの使用](resource-group-portal.md).

以下の制限で、新しいテーブルは Azure のリソース マネージャーを使用する場合の制限の任意の違いを反映するようになりました。たとえば、ある、 **サイト購読の制限** テーブルと **購読の制限 - Azure リソース マネージャー** テーブル。両方のシナリオに制限が適用される場合は最初のテーブルにのみ表示されます。限り、制限はすべての地域にわたってグローバルです。

> [AZURE。メモ] サービス管理のクォータは、Azure リソース グループ内のリソースのクォータが地域ごとに、サブスクリプションにアクセスできるあり、サブスクリプションごとではないことを強調することが重要です。例としてコア クォータを使いましょう。コアのサポート、サーバ容量の増量を要求する場合に、どの地域で使用し、金額と領域を Azure リソース グループ中核のクォータのための特定の要求コアの数を決定する必要があります。したがってそこにアプリケーションを実行する西ヨーロッパの 30 のコアを使用する場合、具体的には、西ヨーロッパで 30 コアをリクエストしてください。しかし、他の地域で増加コア クォータがない - 西ヨーロッパだけは 30 コア クォータを持っています。
<!-- -->
As a result, you may find it useful to consider deciding what your Azure Resource Group quotas need to be for your workload in any one region, and request that amount in each region into which you are considering deployment. See [troubleshooting deployment issues](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) for more help discovering your current quotas for specific regions.

## サイト購読の制限

[AZURE。含まれます [azure サブスクリプション制限](../includes/azure-subscription-limits.md)]

### 購読の制限 - Azure リソース マネージャー

Azure リソース マネージャーと Azure リソース グループを使用する場合、次の制限が適用されます。Azure リソース マネージャーでを変更されていない制限は下記されていません。これらの制限、前述の表を参照してください。

[AZURE。含まれます [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## リソース グループの制限

[AZURE。含まれます [azure リソース グループ範囲](../includes/azure-resource-groups-limits.md)]


## 仮想マシンの制限

[AZURE。含まれます [azure の仮想マシンの制限](../includes/azure-virtual-machines-limits.md)]


### 仮想マシンの制限 - Azure リソース マネージャー

Azure リソース マネージャーと Azure リソース グループを使用する場合、次の制限が適用されます。Azure リソース マネージャーでを変更されていない制限は下記されていません。これらの制限、前述の表を参照してください。

[AZURE。含まれます [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## ネットワークの制限

[AZURE。含まれます [azure 仮想ネットワーク制限](../includes/azure-virtual-network-limits.md)]


### ネットワークの制限 – Azure リソース マネージャー

Azure リソース マネージャーと Azure リソース グループを使用する場合、次の制限が適用されます。Azure リソース マネージャーでを変更されていない制限は下記されていません。これらの制限、前述の表を参照してください。

[AZURE。含まれます [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## 格納域の制限

### 標準的な記憶域の制限 

[AZURE。含まれます [azure ストレージ制限](../includes/azure-storage-limits.md)]

ストレージ アカウントの制限の詳細については、次を参照してください。 [Azure ストレージのスケーラビリティとパフォーマンスの目標](../articles/storage/storage-scalability-targets.md).


### プレミアムの格納域の制限

[AZURE。含まれます [azure ストレージ制限プレミアム ストレージ](../includes/azure-storage-limits-premium-storage.md)]


### 格納域の制限 - Azure リソース マネージャー

[AZURE。含まれます [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## クラウド サービスの制限

[AZURE。含まれます [azure クラウド サービス範囲](../includes/azure-cloud-services-limits.md)]


## アプリ サービスの制限 - Web アプリ、携帯アプリ API アプリ ロジック

[AZURE。含まれます [azure web サイト制限](../includes/azure-websites-limits.md)]

## バッチの制限

[AZURE。含まれます [azure バッチ制限](../includes/azure-batch-limits.md)]


## DocumentDB の制限

[AZURE。含まれます [azure documentdb 制限](../includes/azure-documentdb-limits.md)]


## モバイル契約の制限

[AZURE。含まれます [azure-モバイル-婚約-制限](../includes/azure-mobile-engagement-limits.md)]


## 検索の制限

[AZURE。含まれます [azure 検索制限](../includes/azure-search-limits.md)]

Azure の検索制限の詳細については、次を参照してください。 [制限および制約](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## SQL データベースの制限

[AZURE。含まれます [azure sql データベース範囲](../includes/azure-sql-database-limits.md)]

SQL データベースの制限の詳細については、次のトピックを参照してください。

 - [SQL azure データベース サービス層 (エディション)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [SQL azure データベース サービス層とパフォーマンス レベル](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [データベースのスループット ユニット (DTU) クォータ](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [SQL データベースのリソース制限](sql-database/sql-database-resource-limits.md)

## メディア サービスの制限

[AZURE。含まれます [azure mediaservices 制限](../includes/azure-mediaservices-limits.md)]

## モバイル サービスの制限

[AZURE。含まれます [モバイル サービス制限](../includes/mobile-services-limits.md)]

## サービス バスの制限

[AZURE。含まれます [azure servicebus 制限](../includes/service-bus-quotas-table.md)]

## ストリーム解析の限界

[AZURE。含まれます [ストリーム分析制限テーブル](../includes/stream-analytics-limits-table.md)]

## Active Directory の制限

[AZURE。含まれます [AAD サービス制限](../includes/active-directory-service-limits-include.md)]


## 紺碧 RemoteApp 制限

[AZURE。含まれます [azure remoteapp 制限](../includes/azure-remoteapp-limits.md)]

## StorSimple システムの制限

[AZURE。含まれます [storsimple limits テーブル](../includes/storsimple-limits-table.md)]


## 運用洞察力の制限

[AZURE。含まれます [操作上の洞察力制限](../includes/operational-insights-limits.md)]

## バックアップの制限

[AZURE。含まれます [azure のバックアップ制限](../includes/azure-backup-limits.md)]

## サイト回復の制限

[AZURE。含まれます [サイト回復制限](../includes/site-recovery-limits.md)]

## API 管理限界

[AZURE。含まれます [api 管理サービス範囲](../includes/api-management-service-limits.md)]

## 紺碧 Redis キャッシュ制限

[AZURE。含まれます [redis キャッシュ サービス範囲](../includes/redis-cache-service-limits.md)]

## キー保管庫の制限

[AZURE。含まれます [キー保管庫制限](../includes/key-vault-limits.md)]

## 多要素認証
[AZURE。含まれます [azure mfa サービス範囲](../includes/azure-mfa-service-limits.md)]

## また見なさい

[紺碧の制限および増加の理解](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[仮想マシンと Azure のクラウド サービス サイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)
