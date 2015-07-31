<properties 
   pageTitle="Azure Government Overview" 
   description="This article provides an overview of the Azure Government Cloud capabilities and the trustworthy design and security used to support compliance applicable to federal, state, and local government organizations and their partners. " 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="03/13/2015"
   ms.author="john.harvey@microsoft.com"/>

#  Microsoft Azure 政府概要 

<p> Microsoft Azure Government is a physically and network isolated instance of Microsoft Azure.  This developers guide will provide details on the differences that application developers and administrators would need to interact and work with these seperate regions of Azure.


## <a name="Overview"></a>概要

紺碧の政府は政府コミュニティ クラウド (GCC) の米国政府機関の速度、規模、セキュリティ、コンプライアンス、経済学を必要とする戦略的な政府のシナリオをサポートするように設計。  それは、Microsoft CRM オンライン等 O365 GCC、Office 365 Azure パブリックなど他のマイクロソフト クラウド製品のソフトウェア、セキュリティ、コンプライアンス、およびコントロールを提供するマイクロソフトの広範な経験に基づいて開発されました。 

さらに、Azure の政府はより高いレベルのセキュリティを満たすために設計されています、コンプライアンスを必要と敏感な専用の米国公共部門の作業負荷の発見アメリカ合衆国連邦リスクおよび承認管理プログラム (FedRAMP)、部門の防衛企業クラウド サービス ブローカー (ECSB)、刑事正義の情報サービス (CJIS) などの規制でセキュリティ ポリシーおよび健康保険の携行性と責任に関する法律 (HIPAA)。     

以下、Azure 政府クラウド インフラストラクチャ、ファブリック、サービス、政府組織の目標を満たすためにハイブリッド クラウド ソリューションを構築を支援する利用可能なフレームワークの概要ビューです。 以下のいくつかのサービスは、プレビューで使用可能なのみを参照してください、 [地域ページ](http://azure.microsoft.com/regions/#services) 一般的に利用可能な最新の状態で、ほとんどのサービスが一覧表示されます。

![][2]

紺碧の政府には、サービスとしてのインフラストラクチャ (IaaS)、サービスとしてのプラットフォーム (PaaS) のコア コンポーネントが含まれています。 これには、インフラストラクチャ、ネットワーク、ストレージ、データ管理、id 管理、その他の多くのサービスが含まれます。 紺碧の政府は、公共紺碧顧客が静止のデータ レプリケーションと自動スケーリングのような活用して同じの偉大な機能をサポートします。マイクロソフトは両方のリーダーとして識別されています。 <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> と <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">イースター<a/> で業界をリードするアナリストです。

信頼性の高いサービスおよび公共 Azure の機能のほかに、Azure 政府はいくつかの米国政府機関は自分のデータを提供することにより安全を確保するために機能を提供します。

- **物理とネットワーク分離のインスタンス** -紺碧政府環境は、修飾された米国政府機関およびソリューション ・ プロバイダーで使用される Microsoft Azure パブリックとだけから完全に独立したインスタンスです。

- **セキュリティ、プライバシー & コンプライアンス** マイクロソフトは、その堅牢なセキュリティ、プライバシー、およびコンプライアンス コントロール フレームワークに加え、ECSB 影響レベルとその CJIS より高いレベルの要件を満たすために追加の厳格なコントロールを実施しています。 

- **データ ストレージ** -紺碧の政府の環境は、離れて 2 データ センター 500 マイル以上を維持します。コンチネンタル アメリカ合衆国 (イモガイ) データ センター内にあるすべての顧客管理データを格納します。

- **米国の人員** -Azure すべての政府オペレーターと管理者は、スクリーンの米国市民です。

- **アイデンティティ管理** -Azure の政府の環境の中で id 管理は、Azure Active Directory の個別のインスタンスです。

- **コンプライアンス** -マイクロソフトでは継続的に満たすために、厳格な維持、および FedRAMP などの連邦、州、および地方のコンプライアンス要件の変更、CJIS、ECSB、および HIPAA 米国政府のクラウド ソリューション。 

- **クラウド統合** -紺碧の政府は、クラウド サービスと 1 TB の OneDrive ストレージ容量など高度なサービス間で記号の 1 つを可能にする O365 政府との統合環境を提供します。

紺碧の政府組織は、既存のテクノロジへの投資を維持し、クラウド サービスのメリットを実現ができます。 Azure 政府は製品との相互運用可能なクラウド プラットフォームとテクノロジ組織を構築することができますので地面からより多くのアプリケーションを開きます。 機関には、ツール、サービス、オペレーティング システム、アーキテクチャ、およびクラウド ソリューションの Windows、Linux、Oracle、SharePoint、.NET、Java、PHP、Node.js を含むフレームワークを選択できます。Azure 政府プラットフォームの柔軟性により、複数機関の協力、アプリケーション開発、および統合の新たな形態の。  

米国政府機関のクラウド サービスに興味を持っては、Azure 政府が膨大な規模および発展的なニーズを満たすために厳格なセキュリティを提供する自信を持ってすることができます。 







## <a name="Features"></a> 現在、Microsoft Azure 政府で利用できる機能
紺碧政府現在アイオワ米国政府と米国政府ヴァージニアの地域で利用可能な次のサービスがあります。

- 仮想マシン
- クラウド サービス
- ストレージ
- アクティブ ディレクトリ
- スケジューラ
- 仮想ネットワーク
- SQL データベース
- 紺碧のファイル
- メディア サービス
- トラフィック マネージャー
- サービス バス

他のサービスが利用できるとより多くのサービスを継続的に追加されます。 サービスの最新の一覧を参照してくださいしてください、 [地域ページ](http://azure.microsoft.com/regions/#services) それぞれの利用可能な領域およびサービスを強調します。  

現在、米国 GOV アイオワ、米国 GOV バージニア州は、Azure の政府を支えるデータ センター。 現在のデータ センターとサービスの上記地域のページをご覧ください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>次のステップ

詳細と Azure の政府についての学習に興味があるなら以下のリンクのいくつかを活用してください。

- **<A href="http://azure.com/gov">取得し、Azure の政府へのアクセス</a>**

- **<A href="/azure-government-developer-guide">紺碧政府開発者ガイド</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
