<properties
   pageTitle="Availability checklist | Microsoft Azure"
   description="Checklist that provides guidance for availability concerns during design."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# 可用性のチェックリスト

![](media/best-practices-availability-checklist/pnp-logo.png)

## アプリケーションの設計

- **任意の単一障害点を避けてください。** すべてのコンポーネント、サービス、リソース、およびコンピューティング インスタンスは、単一障害点が可用性に影響を及ぼすを防ぐために複数のインスタンスとして配置する必要があります。これには、認証メカニズムが含まれています。複数のインスタンスを使用して、自動的に障害を検出し、プラットフォームしない自動的に障害のないインスタンスに要求をリダイレクト設定を可能にするアプリケーションを設計します。
- **異なる SLA 当たりの負荷を分解します。** サービスが構成される場合重要と重要度の低い作業負荷管理を別々 とサービス機能と可用性要件に対応するインスタンスの数を指定します。
- **最小限に抑え、サービスの依存関係を理解します。** 使用可能であれば、異なるサービスの数を最小限に抑え、すべてのシステムに存在する機能とサービスの依存関係を理解します。これはこれらの依存関係の性質と障害の影響が含まれています。 またはアプリケーション全体に各 1 つのパフォーマンスが低下します。マイクロソフトは保証、少なくとも 99.9% の可用性、サービスのほとんどが、これはつまり、アプリケーションが依存する可能性のあるすべての追加サービスの全体的な可用性、システムの SLA 0.1% 削減します。
- **可能な限り多重呼び出し不変メッセージやデザイン タスク** 重複要求の問題は発生しません。たとえば、サービスは生産者としての役割を果たすシステムの他の部分によって要求として送信されたメッセージを処理するコンシューマーとして動作できます。消費者には、メッセージの処理後、処理されていることを認める前に失敗した場合、プロデューサーは消費者の別のインスタンスによって処理できる再送要求を送信可能性があります。このため、消費者とを行なう操作される必要がありますアイデムポ テントなので、以前に実行した操作を繰り返すが表示されない結果が無効です。メッセージの重複を検出または競合の処理に楽観的なアプローチを使用して一貫性を確保することになります。
- **クリティカルなトランザクションの高可用性を実装するメッセージ ブローカーを使用します。** タスクの開始またはリモート サービスにアクセスするための多くのシナリオでは、メッセージングを使用アプリケーションとターゲット サービスとの間の指示を渡す。最高のパフォーマンス、アプリケーションがメッセージを送信し、応答を待機することがなくより多くの要求を処理に戻りますできなければなりません。メッセージの配信を保証するため、メッセージング システムは、高可用性を提供する必要があります。サービス バス メッセージのキューを実装します。 _少なくとも 1 回_ セマンティクスは、重複コピーが特定の状況で配信がというキューにポストされた各メッセージは、失われない。場合は、メッセージの処理は冪等 (前の項目を参照)、配信すべき問題ではない繰り返されます。
- **優雅に低下するようにアプリケーションを設計** when reaching resource limits, and take appropriate action to minimize the impact for the user. In some cases, the load on the application may exceed the capacity of one or more parts, causing reduced availability and failed connections. Scaling can help to alleviate this, but it may reach a limit imposed by factors such as resource availability or cost. Design the application so that, in this situation, it can automatically degrade gracefully. For example, in an ecommerce system, if the order-processing subsystem is under strain (or has even failed completely), that part of the system can be temporarily disabled while allowing other functionality (such as browsing the product catalog) to continue. It might be appropriate to postpone requests to a failing subsystem, for example still enabling customers to submit orders but saving them to a queue or other safe storage mechanism for later processing when the orders subsystem is available again.
- **急速なバースト イベントを適切に処理します。** ほとんどのアプリケーションは、ビジネス アプリケーションや e コマース サイトで新製品を発売、朝一番に山頂などの経時変化するワークロードを処理する必要があります。自動スケーリングの負荷を処理することができますが、追加のインスタンスがオンラインになるし、要求を処理するためのいくつかの時間がかかることがあります。それを使用して、サービス要求をキューに設計することにより、アプリケーションを圧倒的なから活動の突然で予想外のバーストを防ぐため、低下する正常にキューがフル稼働に近い。あることを確認十分なパフォーマンスと容量非バースト キューをドレインし、未処理の要求を処理する場合に利用できます。詳細についてを参照してください、 [キュー ベースの負荷パターンを平準化](https://msdn.microsoft.com/library/dn589783.aspx).

## 展開および保守

- **各サービスのロールの複数のインスタンスを展開します。** マイクロソフトは、作成して展開、サービスの可用性の保証、これらの保証は、サービスの各ロールの少なくとも 2 つのインスタンスを展開する場合は有効なだけです。これにより、1 つのロールが他のアクティブなままでは使用できません。これは、クライアントの活動を中断することがなく稼働中のシステムに更新プログラムを展開する場合に特に重要です。インスタンスを下ろし、オンライン他まま個別にアップグレードすることができます。
- **複数のデータ センターでアプリケーションをホストします。** 非常に可能性は低いです、自然災害やトランク ネット障害などのイベントを通じてオフラインにデータ センター全体の不可能です。重要なビジネス アプリケーションは、最高の可用性を提供するために 1 つ以上のデータ センターにホスト必要があります。これはローカル ユーザーの待機時間を減らすことができますという利点があるし、アプリケーションを更新するときの柔軟性のための追加の機会を提供します。
- **自動化、展開および保守のタスクをテストします。** 分散アプリケーションは、一緒に動作する必要があります複数の部分で構成されます。展開する必要がありますしたがって自動テストを使用して、スクリプトおよび配置アプリケーションを更新し、構成を検証するなど実績のあるメカニズムと展開プロセスを自動化します。自動化された技術は、アプリケーションの一部またはすべての更新を実行するも使用してください。すべてのエラーには、さらなるダウンタイムが発生しないように完全にこれらのプロセスをテストすることが重要です。すべての展開ツールは、展開されたアプリケーションを保護するために適切なセキュリティ制限をいる必要があります。定義、展開ポリシーを慎重に適用して人間の介入の必要性を最小限に抑えます。
- **プラットフォームのステージングとプロダクションの機能の使用を検討します。** どここれらがあります。たとえば、Azure クラウド サービスのステージングおよびプロダクション環境を使用すると、アプリケーションが仮想 IP アドレス交換 (VIP スワップ) により即座に別のいずれかから切り替えができます。ただし、オンプレミスをステージまたはアプリケーションの異なるバージョンを同時に配置し、徐々 にユーザーを移行する場合は、可能性がある VIP スワップ操作を使用することができます。
- **リサイクルせずに構成の変更を適用します。** 可能な場合のインスタンス。多くの場合、再起動する役割を必要とせず、Azure アプリケーションまたはサービスの構成設定を変更できます。役割は、構成の変更を検出し、アプリケーション内のコンポーネントに適用するために処理できるイベントを公開します。ただし、コア プラットフォームの設定にいくつか変更を再起動する役割が必要になります。コンポーネントおよびサービスを構築する場合可用性を最大化し、全体としてアプリケーションを再起動するを必要とせず構成設定への変更を受け入れるようにそれらを設計することでダウンタイムを最小限に抑えます。
- **更新中にダウンタイムのアップグレード ドメインを使用します。** Azure コンピューティング web などの単位、ドメインをアップグレードするワーカー ロールが割り当てられます。アップグレード ドメインの各ロールは、停止、更新、およびアプリケーションの可用性への影響を最小限にするために再起動ローリング アップデートが行われるときは、一緒にドメイン グループ ロール インスタンスをアップグレードします。サービスを配置するときは、サービスのアップグレード ドメインの数を作成必要がありますを指定できます。

	> [AZURE。メモ] 障害ドメイン、サーバー ラック、電源、およびロールのすべてのインスタンスに影響を与える障害の可能性を最小限に抑えるために、規定を冷却の面で他の障害ドメインから合理的に独立した、それぞれの役割も分散されます。この配布が自動的に発生し、それを制御することはできません。

- **Azure の仮想マシンの可用性の設定を構成します。** Placing two or more virtual machines in the same availability set guarantees that these virtual machines will not be deployed to the same fault domain. To maximize availability, you should create multiple instances of each critical virtual machine used by your system and place these instances in the same availability set. If you are running multiple virtual machines that serve different purposes, create an availability set for each virtual machine and add instances of each virtual machine to each availability set. For example, if you have created separate virtual machines to act as a web server and a reporting server, create an availability set for the web server and another availability set for the reporting server. Add instances of the web server virtual machine to the web server availability set, and add instances of the reporting server virtual machine to the reporting server availability set.

## データ管理

- **データ ・ レプリケーションを活用します。** を介してローカルおよび地理的な冗長性。Azure ストレージ内のデータは自動的にインフラストラクチャに失敗した場合の損失に対して保護するためにレプリケートされ、このレプリケーションのいくつかの要因を構成することができます。たとえば、データの読み取り専用コピーは、(読み取りアクセス グローバル冗長ストレージまたは RA と大赤斑と呼ばれる) 1 つ以上の地理的地域にレプリケート可能性があります。追加料金が、RA GRS を使用して注意してください、参照してください、 [Azure ストレージの価格](http://azure.microsoft.com/pricing/details/storage/) 詳細についてはマイクロソフトの web サイト上のページ。
- **オプティミスティック同時実行制御と最終的な一貫性を使用します。** 可能な限り。トランザクション (ペシミスティック同時実行制御) のロック リソースへのアクセスをブロックすることができますによりパフォーマンスが低下、かなり、可用性が低下します。これらの問題は、分散システムで特に深刻になることができます。多くの場合、慎重な設計と分割などの手法は競合する更新が発生する可能性を最小化できます。場所データがレプリケートされているまたはデータは最終的に一貫性のあるのみなりますが、利点は、通常のところ即時一貫性を確保するためのトランザクションを使用しての可用性への影響を上回る別々 に更新されたストアからの読み取りです。
- **定期的なバックアップと復元**, and ensure it meets the Recovery Point Objective (RPO). Regularly and automatically back up data that is not preserved elsewhere, and verify you can reliably restore both the data and the application itself should a failure occur. Data replication is not a backup feature because errors and inconsistencies introduced through failure, error, or malicious operations will be replicated across all stores. The backup process must be secure to protect the data in transit and in storage. Databases or parts of a data store can usually be recovered to a previous point in time by using transaction logs. Microsoft Azure provides a backup facility for data stored in Microsoft Azure SQL Database. The data is exported to a backup package on Microsoft Azure blob storage, and can be downloaded to a secure on-premises location for storage.
- **Redis キャッシュのセカンダリ ・ コピーを維持するために高可用性オプションを有効にします。** Redis キャッシュを使用する場合は、内容のセカンダリ ・ コピーを維持するために標準的なオプションを選択します。詳細については、ページを参照してください。 [Azure Redis キャッシュにキャッシュを作成します。](https://msdn.microsoft.com/library/dn690516.aspx) Microsoft の web サイト。

## エラーと失敗

- **タイムアウト時間の概念を紹介します。** サービスとリソースが使用できなく、要求の失敗の原因となります。適用するタイムアウトが各サービスまたはリソースに適していることを確認だけでなく、それらにアクセスしているクライアント (いくつかのケースで可能性があること適切なコンテキストとクライアントを実行するその他のアクションによって、クライアントの特定のインスタンスより長いタイムアウト時間)。非常に短いタイムアウトが原因でサービスとかなり待ち時間があるリソースの過度の再試行操作が、非常に長いタイムアウト多数の要求がキュー サービスまたは対応するリソースを待っている場合にブロッキングが発生することができます。
- **過渡的な障害によって引き起こされる失敗した操作を再試行します。** すべてのサービスにアクセスするための再試行の戦略を設計し、リソース、サポートしていない本質的に自動接続を再試行してください。リソースの過負荷を防ぐために、それは正常に回復し、キューに入れられた要求を処理できるようにエラーの数と再試行間隔の増加を含む戦略を使用します。非常に短い遅延で継続的な再試行が問題を悪化させる可能性があります。
- **カスケード故障を避けるために要求の送信を停止します。** when remote services are unavailable. There may be situations where transient or other faults, ranging in severity from a partial loss of connectivity to the complete failure of a service, take much longer than expected to return to normal. Additionally, if a service is very busy, failure in one part of the system may lead to cascading failures, and result in many operations becoming blocked while holding onto critical system resources such as memory, threads, and database connections. Instead of continually retrying an operation that is unlikely to succeed, the application should quickly accept that the operation has failed, and gracefully handle this failure. You can use the Circuit Breaker pattern to reject requests for specific operations for defined periods. The [遮断パターン](https://msdn.microsoft.com/library/dn589784.aspx)Microsoft の web サイト上のページでは、詳細を提供します。
- **作成するか、複数のコンポーネントに戻る** 特定のサービスがオフラインまたは使用不可の影響を軽減します。可能であれば、影響を与える操作との既存の接続なしの複数のインスタンスを活用するアプリケーションを設計します。複数のインスタンスを使用して、それらの間に要求を分散し検出し、可用性を最大化するために失敗した場合は、要求を送信するを回避します。
- **別のサービスまたはワークフローに戻る** 可能な限り。たとえば、SQL データベースへの書き込みが失敗した場合は、blob ストレージにデータを一時的に格納およびサービスが利用可能になるときに SQL データベースに blob ストレージに書き込みを再生する機能を提供します。場合によっては、失敗した操作はアプリケーションがコンポーネントまたはサービスが失敗したときにも作業を続行できるように代替アクションがあります。可能であれば、障害を検出し、適切な代替機能を提供することができます他のサービスにまたはバックアップまたはプライマリ サービスがオフラインのときにコア事業を維持できる機能のインスタンスを減らすに要求をリダイレクトします。

## 監視と災害復旧

- **可能性が高いエラーとエラー イベントの豊富なインストルメンテーションを提供します。** 運用スタッフに状況を報告。失敗する可能性が高いがまだしていない発生原因、状況を軽減する運用スタッフを有効にするのに十分なデータを提供し、システムの利用を確保します。既に発生した障害、アプリケーション必要がありますユーザーに適切なエラー メッセージを返すが、減らされた機能性を伴い、実行を継続ましょう。すべてのケースで監視システムは、運用スタッフが効果の迅速なリカバリを可能にする包括的な詳細をキャプチャする必要があります、デザイナー、および開発者は、再び発生状況を防ぐために、システムを変更するために必要な場合。
- **チェック機能を実装することによってシステムの状態を監視します。** 健康とアプリケーションのパフォーマンスは、それが失敗するまで顕著なことがなく時間の経過とともに低下します。これに対抗する一つの方法は、プローブを実装したり、アプリケーションの外部から定期的に実行される関数をチェックです。これらのチェックは、アプリケーション全体、アプリケーションの個々 の部分、アプリケーションを使用して、個々 のサービスまたは個々 のコンポーネントの応答時間の測定と同じくらい簡単することができます。チェックの関数は、有効な結果を生む、遅延を測定して空室状況、しシステムから情報を抽出プロセスを実行できます。
- **すべてのフェイル オーバーとフォールバック システムを定期的にテストします。** 利用可能な期待どおりに動作できるように。フェイル オーバーとフォールバック機能は、システムおよび操作への変更の影響が、メインのシステム障害が発生したり、過負荷になったまで、影響を検出されない場合があります。実行時にライブの問題を補正する必要がある前にそれをテストすることをお勧めします。
- **監視システムをテストします。** 自動フェイル オーバーとフォールバック システムとシステムの健全性とパフォーマンスの監視およびインストルメンテーション正常に依存するすべてのダッシュ ボードを使用して、手動の可視化。これらの要素が失敗した場合、重要な情報を逃すまたは不正確なデータを報告し、オペレーターはシステムが不健全なまたは失敗したことを気付かない可能性があります。
- **実行時間の長いワークフローの進行状況を追跡します。** 失敗した場合に再試行してください。実行時間の長いワークフローがしばしば複数のステップで構成されます。これらの種類のワークフローを設計するときは、各ステップは独立しており、ワークフロー全体をロールバックする必要がありますチャンスを最小限に抑えるために再試行することができます、または複数に補正するトランザクションを実行する必要があることを確認します。監視およびスケジューラ エージェント スーパーバイザーなどパターンを実装する実行時間の長いワークフローの進捗状況を管理します。詳細についてを参照してください、 [スケジューラ エージェント スーパーバイザー パターン](https://msdn.microsoft.com/library/dn589780.aspx) Microsoft の web サイト上のページ。
- **災害復旧のための計画。** あらゆる種類のパーツを表示可能性があります障害またはすべてのメイン システムから回復の文書、合意された、完全にテストされた計画はありませんを確認します。手順を定期的にテストし、すべての運用スタッフがプロセスに精通しています。
