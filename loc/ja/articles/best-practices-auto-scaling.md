<properties
   pageTitle="Autoscaling guidance | Microsoft Azure"
   description="Guidance upon how to autoscale to dynamically allocate resources required by an application."
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

# 自動スケール ガイダンス

![](media/best-practices-auto-scaling/pnp-logo.png)

## 概要
自動スケーリングは、パフォーマンス要件に一致し、ランタイム コストを最小限に抑えながら、サービス レベル契約 (Sla) を満たすためにアプリケーションが必要なリソースを動的に割り当てるのプロセスです。仕事の量が増えるにつれて、アプリケーションはタイムリーでそのタスクを実行できるようにするための追加のリソースを必要があります。需要が止めれば、リソースまだ sla を適切なパフォーマンスを維持しコストを最小限に抑えるために割り当てを解除できます。
自動スケーリングは、オペレーターが継続的にシステムのパフォーマンスを監視して、追加またはリソースを削除を決定するための必要性を減らすことによってオーバーヘッド管理を緩和しながらクラウドでホストされる環境の弾力性の利点を取ります。
> 自動スケーリングは、コンピューティング リソースだけでなく、アプリケーションによって使用されているリソースのすべてに適用されます。たとえば、あなたのシステムは、情報を送受信するメッセージ キューを使用する場合それはスケールとして、追加のキューを作成それでした。

## スケールの種類
2 つの形式は、通常のスケーリング-垂直および水平スケーリングします。

- **垂直方向の目盛り** (多くの場合と呼ばれるに _上下に拡大/縮小_) requires that you modify the hardware (expand or reduce its capacity and performance), or redeploy the solution using alternative hardware that has the appropriate capacity and performance. In a cloud environment, the hardware platform is typically a virtualized environment. Unless the original hardware was substantially overprovisioned, with the consequent upfront capital expense, vertically scaling up in this environment involves provisioning more powerful resources, and then moving the system onto these new resources. Vertical scaling is often a disruptive process that requires making the system temporarily unavailable while it is being redeployed. It may be possible to keep the original system running while the new hardware is provisioned and brought online, but there will likely be some interruption while the processing transitions from the old environment to the new one. It is uncommon to use autoscaling to implement a vertical scaling strategy.
- **水平方向のスケーリング** (多くの場合と呼ばれるに _スケール アウトとのスケール_) 追加のソリューションまたはハイパワー システムよりもむしろ商品の資源は、通常より少ないリソースを展開する必要があります。ソリューションは、これらのリソースをプロビジョニング中に中断することがなく実行を続行できます。プロビジョニング プロセスが完了したら、ソリューションを構成する要素のコピーをこれらの追加リソース上に配置され、使用可能にできます。需要が低下すると、それらを使用して要素が正常にシャット ダウンした後に、追加のリソースがクリアできます。Microsoft Azure を含む多くのクラウド ベースのシステムでは、スケーリングのこの形式のオートメーションをサポートします。

## スケーリング戦略を実装します。
通常自動スケーリング戦略を実装するには、次のコンポーネントおよびプロセスが含まれます。

- 計装とレスポンス ・ タイム、キューの長さ、CPU 使用率、メモリ使用量など主要な統計情報をキャプチャするアプリケーション、サービス、およびインフラストラクチャのレベルでシステムを監視します。
- 意思決定ロジックの定義済みのシステムのしきい値またはスケジュールに対して監視されるスケール ファクターを評価でき、かどうかをスケールするかどうかに関する決定を行います。
- プロビジョニング リソースをプロビジョニング解除など、システムのスケーリングに関連付けられたタスクを遂行するための責任があるコンポーネント。
- テスト、監視、およびそれが期待どおりに機能することを確認する自動スケーリング戦略のチューニングします。

Microsoft Azure などのクラウド ベースの環境を最も一般的なシナリオに対応する組み込みスケーリング メカニズムを提供します。環境や使用するサービスは、必要な自動スケーリング機能またはカスタム実装を運用とシステムのメトリクスを収集する必要ことがある場合は、能力以上の極端なスケーリング要件がある場合は、用意されていない場合、関連データを識別し、リソースをそれに応じてスケールを分析できます。

## 自動スケーリングを実装するための考慮事項
自動スケーリングは、インスタント ソリューションではありません。単にシステム リソースを追加またはプロセスの複数のインスタンスを実行して、システムの性能が向上するとは限りません。 スケーリング戦略を設計するときは、次の点を考慮してください。

- The system must be designed to be horizontally scalable. Avoid making assumptions about instance affinity; do not design solutions that require that the code is always running in a specific instance of a process. When scaling a cloud service or web site horizontally, do not assume that a series of requests from the same source will always be routed to the same instance. For the same reason, design services to be stateless to avoid requiring a series of requests from an application to always be routed to the same instance of a service. When designing a service that reads messages from a queue and processes them, do not make any assumptions about which instance of the service handles a specific message because autoscaling could start additional instances of a service as the queue length grows. The [競合する消費者パターン](http://msdn.microsoft.com/library/dn568101.aspx) このシナリオを処理する方法について説明します。
- ソリューションは、実行時間の長いタスクを実装している場合は、このタスクをスケール アウトとスケールの両方をサポートする設計します。みだりケア、このようなタスクは、システムがスケールするときにシャット ダウンをきれいにされるからプロセスのインスタンスを防ぐことができるまたはプロセスが強制的に終了した場合、それはデータを失う可能性があります。理想的には、実行時間の長いタスクをリファクタリングし、それが小さな、離散単位に実行する処理を分割します。、 [パイプとフィルター パターン](http://msdn.microsoft.com/library/dn568100.aspx) これを実現する方法の例を示します。また、レコードが状態、定期的にタスクに関する情報とタスクを実行中のプロセスのすべてのインスタンスがアクセスできる永続的なストレージでこの状態を保存チェックポイント ・ メカニズムを実装できます。この方法でプロセスがシャット ダウン場合、それを実行していた作業を再開できます最後のチェックポイントから別のインスタンスを使用しています。
- クラウド サービスのワーカー ロールのように独立したコンピューティング インスタンスに実行するバック グラウンド タスクには、アプリケーションがホストされている場合は、異なるスケールのポリシーを使用してアプリケーションのさまざまな部分をスケールする必要があります。たとえば、背景コンピューティング インスタンス数、またはこの反対を増加させることがなく追加の UI のコンピューティング インスタンスを配置する必要があります。(基本とプレミアムのサービス パッケージ) などのサービスのさまざまなレベルを提供する場合は、Sla を満たすために基本的なサービス パッケージより積極的にプレミアム サービス パッケージのコンピューティング リソースを拡張する必要があります。
- UI とバック グラウンドを計算するインスタンスは自動スケーリング戦略のドライバーとして通信キューの長さを使用してください。これは、不均衡や現在の負荷とバック グラウンド タスクの処理能力の違いの最良の指標です。
- ビジネス プロセスは、複雑なトランザクションの平均実行時間または 1 時間あたりの注文の数などを測定するカウンターに基づいてスケーリング戦略を作成する場合は、これらの種類のカウンターからの結果と実際の計算容量の要件との関係を完全に理解することを確認します。1 つ以上のコンポーネントの拡張またはビジネス プロセス カウンターの変更への応答の単位を計算する必要があります。  
- 過度にスケール アウトし、何千ものインスタンスの実行に関連するコストを回避しようとするからシステムを防ぐため、自動的に追加できるインスタンスの最大数を制限することを検討してください。ほとんどの自動スケーリング メカニズムでは、ルールのインスタンスの最小および最大数を指定できます。さらに、まだオーバー ロードはインスタンスの最大数が展開されているかどうか、システムが提供する機能や、システムの低下正常に考慮します。
- 維持心負荷の突然のバーストを処理する最も適切なメカニズムをそのスケーリングが適さないことがあります。プロビジョニングしサービスの新しいインスタンスを起動またはシステムにリソースを追加する時間がかかるし、これらの追加リソースは利用可能ななされた時点でピークを過ぎた可能性があります。このシナリオより良いサービスを調整する場合があります。詳細についてを参照してください、 [抑制パターン](http://msdn.microsoft.com/library/dn589798.aspx).
- 逆に、ボリュームが急速に変動するコストが大きな要因ではないと、すべての要求を処理して、追加のインスタンスをすばやく開始する積極的な自動スケーリングの戦略の使用を検討する能力が必要か、最大値を満たすインスタンスに十分な数を開始するスケジュールされたポリシーを使用してロードする前に、その負荷が想定されます。
- The autoscaling mechanism should monitor the autoscaling process and log the details of each autoscaling event (what triggered it, what resources were added or removed, and when). If you create a custom autoscaling mechanism, ensure that it incorporates this capability. The information can be analyzed to help measure the effectiveness of the autoscaling strategy, and tune it if necessary—both in the short term as the usage patterns become more obvious, and over the long term as the business expands or the requirements of the application evolve. If an application reaches the upper limit defined for autoscaling, the mechanism might also alert an operator who could manually start additional resources if the situation warrants this. Note that, under these circumstances, the operator may also be responsible for manually removing these resources after the workload eases.

## Azure ソリューションの自動サイズ調整
Azure ソリューションのスケーリングを構成するためのいくつかのオプションがあります。

- **紺碧のスケーリング**.この機能は、最も一般的なスケーリング シナリオ スケジュールに基づくし、必要に応じて、トリガー実行時メトリックに基づいて操作をスケーリングをサポート (などのプロセッサ使用率、キューの長さ、またはビルドとカスタム カウンター)。簡単に Azure 管理ポータルを使用して、ライブラリを使用できます、Azure 監視サービス管理をより細かく制御の自動スケーリング ルールを構成するのにはおよびすぐに解決策を単純なスケーリング方針を構成できます。詳細については、のセクションを参照してください。 [Azure サービス管理ライブラリの監視](#the-azure-monitoring-services-management-library).
- **カスタム ソリューション** 診断、監視、および Azure のサービス管理機能に基づいています。たとえば、Azure 診断、カスタム コードを使用するか、 [Azure の system Center 管理パック](http://www.microsoft.com/download/details.aspx?id=38414) 継続的に、アプリケーションのパフォーマンスを監視するにはと、 [Azure サービス管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)、、 [Microsoft Azure 管理ライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Libraries)、または [自動スケール アプリケーション ブロック](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx) スケール アウトと。スケーリング操作をトリガーするためのメトリックは、任意の組み込みまたはカスタムのカウンターまたはアプリケーション内で実装するその他の計測をすることができます。ただし、カスタム ソリューションは、実装が単純ではないし、従来のアプローチのどれもがお客様の要件を満たすことができるかどうかのみ考慮する必要があります。自動スケールのアプリケーション ブロック オープン ソース フレームワークです、直接マイクロソフトでサポートされていないことに注意してください。
- **サードパーティ製のサービス** など、 [Paraleap AzureWatch](http://www.paraleap.com/AzureWatch) スケジュール、サービス負荷とシステム パフォーマンス指標、カスタム ルールとルールの種類の組み合わせに基づいてソリューションを拡張することができます。

自動スケール ソリューションを採用するを選択すると、次の点を考慮してください。

- お客様の要件を満たしている場合に、プラットフォームのスケーリング機能で内蔵を使います。いない場合は、あなたは本当に複雑なスケーリング機能を必要はかどうかを慎重に検討します。組み込みの自動スケーリング機能を提供していますそれらを超えて追加の要件のいくつかの例は、コントロール、スケーリング、スケーリング サブスクリプション間で、他の種類のリソースのスケーリングのトリガー イベントを検出する別の方法の詳細を含めることができます。
- Consider if you can predict the load on the application with sufficient accuracy to depend only on scheduled autoscaling (adding and removing instances to meet anticipated peaks in demand). Where this is not possible, use reactive autoscaling based on metrics collected at runtime to allow the application to handle unpredictable changes in demand. However, it is typically appropriate to combine these approaches. For example, create a strategy that adds resources such as compute, storage, and queues based on a schedule of the times when you know the application is most busy. This helps to ensure that capacity is available when required without the delay encountered when starting new instances. In addition, for each scheduled rule, define metrics that allow reactive autoscaling during that period to ensure that the application can handle sustained but unpredictable peaks in demand.
- メトリックと容量の要件、特にアプリケーションを最初に展開したときの関係を理解することは困難が多いです。初めに、少し余分な容量をプロビジョニング、監視し、実負荷に近い能力を持ってスケーリング ルールを調整することを好みます。

### 紺碧のスケーリングを使用してください。
紺碧のスケーリングでは、スケール アウトを構成して、ソリューションのオプションで拡張できます。紺碧のスケーリングは自動的に追加し、Azure クラウド サービス web、ワーカー ロール、Azure モバイル サービス、Azure Web サイト アプリケーションのインスタンスを削除できます。それはまたの開始と Azure の仮想マシンのインスタンスを停止、自動スケーリングを有効にできます。紺碧のスケーリング戦略要因の 2 つのセットで構成されます。

- 追加のインスタンスを確認することができますスケジュール ベースのスケーリングは使い方では、予想されるピーク時に合わせて利用でき、ピーク時間が経過した後でスケールすることができます。これは、十分なインスタンスが既に負荷に対応するためシステムを待つことがなく実行されていることを確認することができます。
- 最後の 1 時間、またはソリューションは、Azure ストレージまたはサービス バス キューに処理されているメッセージのバックログ平均 CPU 使用率などの要因に反応するメトリック ベースのスケーリング。これにより、予期しないまたは予期せぬ需要の変化に合わせてスケジュールされた自動スケーリング ルールから別々 に反応するアプリケーション。

Azure のスケーリングを使用する場合は、次の点を考慮してください。

- スケーリング戦略を組み合わせたスケジュールおよびメトリック ベースのスケーリングします。アプリケーションのスケーラビリティがスケジュールや負荷の変化に対応できるように、サービスのための規則の両方のタイプを指定できます。
- Azure スケーリング ルールを構成し、時間の経過と共に、アプリケーションのパフォーマンスを監視する必要があります。この監視の結果を使用すると、システムが必要ならばをスケーリングする方法を調整できます。ただし、念頭に置いてそのスケーリングの瞬時プロセスではない — 平均 CPU 使用率を超える (下回る) などの指標に反応する時間がかかる指定したしきい値。
- Autoscaling rules that use a detection mechanism based on a measured trigger attribute (such as CPU usage or queue length) use an aggregated value over time, rather than the instantaneous values, to trigger an autoscaling action. By default, the aggregate is an average of the values. This prevents the system from reacting too quickly, or causing rapid oscillation. It also allows time for new instances that are auto-started to settle into running mode, preventing additional autoscaling actions from occurring while the new instances are starting up. For Cloud Services and Virtual Machines, the default period for the aggregation is 45 minutes, so it can take up to this period of time for the metric to trigger autoscaling in response to spikes in demand. You can change the aggregation period by using the SDK, but be aware that periods of less than 25 minutes may cause unpredictable results (see [自動 CPU 使用率監視サービス管理ライブラリ azure のクラウド サービスをスケーリング](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/) 詳細については)。Azure Web サイトの平均期間は、平均トリガー測定する変更後約 5 分で使用する新しいインスタンスを許可する短い。
- SDK ではなく、web ポータルを使用してスケーリングを構成する場合は、ルールがアクティブな中に詳細スケジュールを指定できます。また、独自の指標を作成でき、またはスケーリング ルールで既存のもののいずれかなしでそれらを使用します。たとえば、1 秒または平均メモリ可用性要求数など代替カウンターを使用または特定のビジネス プロセスを測定するカスタム カウンターを使用したい場合があります。詳細については、のセクションを参照してください。 [Azure サービス管理ライブラリの監視](#the-azure-monitoring-services-management-library).
- ときに、Azure の仮想マシンの自動スケーリング数最大数に相当する仮想マシンのインスタンスを展開する必要がある自動スケーリングを許可します。これらのインスタンスは、同じ可用性設定の一部をする必要があります。仮想マシンの自動スケーリング メカニズムを作成または仮想マシンのインスタンスを削除しません。代わりに、自動スケーリング ルールの構成は開始し、これらのインスタンスの適切な数を停止します。詳細については、次を参照してください。 [自動的に Web ロール、ワーカー ロール、または仮想マシンを実行しているアプリケーションを拡張します。](cloud-services-how-to-scale.md#autoscale).
- 新しいインスタンスを開始することはできません、おそらく (コア仮想マシンのサービスを使用する場合の最大数) などのサブスクリプションの最大値に達しているか、起動中にエラーが発生したため、ポータルが表示スケーリング操作が成功したこと。しかし、その後 **ChangeDeploymentConfiguration** ポータルに表示されるイベントは、サービスのスタートアップが要求されましたが、それが正常に完了したことを示すイベントがなくなることのみ表示されます。
- 、自動スケーリング Azure コンピューティング サービス インスタンスに SQL データベース インスタンスやキューなどのリソースをリンクするのに web ポータル UI を使用できます。これより簡単に別の手動および自動スケーリングの各リンク先のリソースの構成オプションにアクセスすることができます。詳細については、次を参照してください。 [方法: リソースをクラウド サービスにリンク](cloud-services-how-to-manage.md#linkresources) ページのページに、クラウド サービスの管理方法 [アプリケーションをスケール アップする方法](cloud-services-how-to-scale.md).
- 複数のポリシーと規則を構成するとき彼らが相互に競合できる可能性があります。紺碧のスケーリングは、常に実行されているインスタンスに十分な数があることを確認する次の競合解決規則を使用してください。
  - スケール アウトの操作は、操作のスケールに常に優先します。
  - ときスケール アウト操作の競合は、インスタンスの数で最大の増加を開始するルールが優先されます。
  - とき操作競合でスケール、インスタンスの数が最小の減少を開始する規則が優先します。

<a name="the-azure-monitoring-services-management-library"></a>

### Azure サービス管理ライブラリの監視
サービス管理 API を使用して、詳細な制御と Azure のスケーリングを構成して、web ポータルを介して使用できない機能にアクセスします。この API は、REST Web API として直接または Azure 監視サービス管理ライブラリを介してアクセスされます。

紺碧のスケーリングを構成するには、クラウド サービスの役割、仮想マシンの可用性セット、(サーバー ファームをウェブ空間の) として Azure Web サイトまたは Azure 携帯サービスの自動スケール プロファイルを指定します。各プロファイルは、ターゲットを最大 20 を持つことができますを示します。

- (再発または固定の日付範囲を使用して) 適用される、だと
- (最小値、最大値、およびデフォルトの番号) のインスタンスの許可番号
- どのスケーリング ルールが有効

Web ポータルは、CPU 使用率またはキューの長さに基づいてスケール ルールの 1 つのペアで固定プロファイル、本質的に昼夜を区別および平日/週末プロファイルのセットを構成できます。代わりにサービス管理 API を使用して、プロファイルの適用性の細かい日付を構成するし、Azure 監視サービスに利用可能な任意のメトリックに基づくトリガーを最大 10 のルールを指定できます。

自動スケーリング ルールは、ルールが適用され、ターゲットの構成上の変更を示す尺度変更アクションを示すトリガーで構成されます。執筆時点で唯一サポートされているアクションは増加しましたが、インスタンス数の増減します。

自動スケーリング ルールのトリガーは、提供される統計情報に基づいています。構成されたメトリックの値は、スケーリングの構成で定義されている適切な情報源から定期的にサンプリングされます。アクティブなプロファイルから各ルールが評価されると、トリガーで指定されているメトリックの値を集計する時間のインスタンス (該当する場合)、およびこの集計は、規則が適用されるかどうかを示すしきい値と比較されます。時間をかけて有効な集計は、(既定値) の平均、最小値、最大値、最後に、合計、およびカウントします。(既定値) の平均、最大、および最小インスタンスに対する有効な集計です。

トリガーの使用可能なメトリックは、Azure ストレージとサービス バス キューの長さ、Azure 診断によって公開された標準的なパフォーマンス カウンターとカスタム パフォーマンス カウンターが各役割またはバーチャル マシンが公開します。既定以外の使用可能なパフォーマンス カウンターを扱うときのクラウド サービス ソリューションでは、サービスの「最小」から「詳細」の UI の監視レベルの設定を変更する必要があります。

詳細についてを参照してください。

- SDK を監視 [クラス ライブラリ](http://msdn.microsoft.com/library/azure/dn510414.aspx)
- [パフォーマンス カウンターを構成する方法](http://msdn.microsoft.com/library/azure/dn535595.aspx)
- [自動スケーリングの操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [自動サイズ調整の設定を追加します。](http://msdn.microsoft.com/library/azure/dn510372.aspx)
- [自動 CPU 使用率監視サービス管理ライブラリ azure のクラウド サービスをスケーリング](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/)
- [自動サイズ調整のルールを作成する Azure 監視サービス管理ライブラリを使用する方法](http://blogs.msdn.com/b/cie/archive/2014/02/20/how-to-use-windows-azure-monitoring-services-management-library-to-create-an-autoscale-rule.aspx)

## 関連するパターンと指導
次のパターンやガイダンスもあります、シナリオに関連する自動スケーリングを実装する場合。

- [抑制パターン](http://msdn.microsoft.com/library/dn589798.aspx).このパターンでは、アプリケーションが機能し、需要の増加は、リソースに極端な負荷を配置するときにサービス レベル契約を満たすために続けることができる方法について説明します。調整はシステムをスケール アウトしながら圧倒されてからシステムを防ぐための自動スケーリングと使用することができます。
- [競合する消費者パターン](http://msdn.microsoft.com/library/dn568101.aspx).このパターンでは、任意のアプリケーションのインスタンスからのメッセージを処理できるサービス インスタンスのプールを実装する方法について説明します。自動スケーリングは、開始および予想されるワークロードに合致するサービスのインスタンスを停止する使用ことができます。このアプローチにより、同時にスループットを最適化、スケーラビリティと可用性が向上し、負荷を複数のメッセージを処理するシステムです。
- [計装、遠隔指導](http://msdn.microsoft.com/library/dn589775.aspx).計装、テレメトリ スケーリング プロセスを駆動することができます情報を収集するために不可欠です。

## 詳細については
- [アプリケーションをスケール アップする方法](cloud-services-how-to-scale.md)
- [自動的に Web ロール、ワーカー ロール、または仮想マシンを実行しているアプリケーションを拡張します。](cloud-services-how-to-manage.md#linkresources)
- [方法: リソースをクラウド サービスにリンク](cloud-services-how-to-manage.md#linkresources)
- [リンク先のリソースをスケールします。](cloud-services-how-to-scale.md#scalelink)
- [Azure サービス管理ライブラリの監視](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring)
- [Azure サービス管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)
- [自動スケーリングの操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [Microsoft.WindowsAzure.Management.Monitoring.Autoscale Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.management.monitoring.autoscale.aspx)
- 、 [自動スケール アプリケーション ブロック](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx) MSDN のドキュメントとキーのシナリオ。
