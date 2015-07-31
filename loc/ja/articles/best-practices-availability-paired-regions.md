<properties
	pageTitle="Improve Business Continuity with Azure Regional Pairs"
	description="Use Regional pairs to keep applications resilient during data center failures."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Azure 地域のペアを使用して可用性を向上させる

## 説明紺碧のペアになっている地域

Azure は、世界中の複数の地域で動作します。紺碧の地理学は、Azure 領域の少なくとも 1 つを含む世界の定義された領域です。紺碧の領域は、領域内の 1 つまたは複数のデータ センターを含む地理学です。

Azure 地域ごと、一緒に (を除いてブラジル南その地理外領域と対になっている)、同じ地理的地域別のペアになって地域のペアを作るします。


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

図 1 – Azure 地域組図



|地理学 | 地域を対 |                 |
|:-------------|:-------------   |:-------------   |
|北アメリカ |北部中央アメリカ |南部中央アメリカ |
|北アメリカ |東米国 |西米国 |
|北アメリカ |米国東 2 |米国中央 |
|ヨーロッパ |北ヨーロッパ |西ヨーロッパ |
|アジア |東南アジア |東アジア |
|中国 |東中国 |中国北部 |
|日本 |東日本 |西日本 |
|ブラジル |ブラジル南 (1) |南部中央アメリカ |
|オーストラリア |東オーストラリア |オーストラリア Southeast|
|米国政府 |米国 Gov アイオワ |米国 Gov バージニア |

表 1 - 紺碧の地域のペアのマッピング

> (ブラジル 1) サウスは、独自の地理以外の地域と共にためにユニークです。ブラジル南セカンダリ領域は南中央アメリカ南中央アメリカのセカンダリ領域はブラジル南部ではないに注意してください。

Azure の分離と可用性ポリシーからの利益のための地域のペア間でワークロードをレプリケートすることをお勧めします。順番にたとえば、計画的な紺碧システム更新の展開 (同時) ではなくペアの地域を渡る。それは障害のあるアップデートのまれなイベントでも両方の領域が受けないこと同時に意味します。さらに、広範な停電時の万一の場合にすべてのペアのうち、少なくとも 1 つの領域の回復を優先します。

## 地域のペアの例
下の図 2 は、災害復旧のための地域のペアを使用して仮想アプリケーションを示しています。緑の番号は、クロス地域活動 3 つの Azure サービス (Azure コンピューティング、ストレージ、およびデータベース) と地域間のレプリケーションを構成する方法を強調表示します。ペア地域全体に展開するユニークな利点は、オレンジ色の数字が強調表示されます。


![ペアになっている地域の利点の概要](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

図 2-仮想 Azure 地域ペア

## クロス地域活動
図 2 に呼ばれる。

![1 green エンバ](./media/best-practices-availability-paired-regions/1Green.png) **Azure コンピューティング (パース)** -リソースは、災害時に別の地域で使用できるように事前に追加の計算リソースをプロビジョニングが必要があります。詳細については、次を参照してください。 [Azure のビジネス継続性技術指導](https://msdn.microsoft.com/library/azure/hh873027.aspx)

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Azure ストレージ** -地理冗長ストレージ (GRS) は、Azure ストレージ アカウントの作成時に既定で構成されます。GRS、あなたのデータは 3 回をレプリケート自動的にペアになっている地域のプライマリ領域内と 3 回。詳細については、次を参照してください。 [Azure ストレージの冗長性オプション](../storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png) **Azure の SQL データベース** -Azure SQL 標準的な地理的レプリケーション、トランザクションのペアになっている地域への非同期レプリケーションを構成できます。プレミアム地理的レプリケーション、世界で任意の領域へのレプリケーションを構成できます。ただし、ほとんどの災害復旧シナリオでペアになっている地域でこれらのリソースを配置することをお勧めします。詳細については、次を参照してください。  [SQL Azure データベースで地理的レプリケーション](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Green](./media/best-practices-availability-paired-regions/4Green.png) **Azure のリソース マネージャー (腕)** アームは本質的に地域間でサービス管理コンポーネントの論理的分離を提供します。つまり、1 つの領域の論理エラーとは別に影響を与える可能性が低く。

## ペアになっている地域の利点
図 2 に呼ばれる。  

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png)
**物理的な分離** -可能な場合、Azure は、ではありませんが実用的または可能なすべての地域で地域のペアでは、データ センター間の分離、少なくとも 300 マイルを好みます。データ センターの物理的な分離は、自然災害、市民の不安、停電、または一度に両方の領域に影響を与える物理的なネットワークの停止の可能性を減らします。分離は、地理 (地理サイズ、電源/ネットワーク インフラストラクチャの可用性、規制等) の中で制約が適用されます。  

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)
**プラットフォームに装備されたレプリケーション** -地理冗長ストレージなどいくつかのサービスは、ペアになっている地域への自動レプリケーションを提供します。

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png)
**地域回復順序** -広範な停電の場合すべてのペアのうち 1 つの領域の回復を優先しています。ペアの地域で展開されているアプリケーションは、優先順位でリカバリ領域の 1 つを持っている保証されます。ペアになっていない地域間でアプリケーションを展開する場合回復が遅れる-最悪の場合選択した領域を回復する最後の 2 つがあります。

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png)
**順次更新** -計画された Azure システム更新、対地域に順次展開 (同時) ではなくダウンタイム、バグ、および不正な更新のまれなイベントに論理的障害の影響を最小限に抑える。


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png)
**データ レジデンシー** -地域は内 (を除いてブラジル南) のペアとして同じ地理的、税と法律の執行管轄権目的のデータの居住要件を満たすため。
