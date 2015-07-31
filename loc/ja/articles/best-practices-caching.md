<properties
   pageTitle="Caching guidance | Microsoft Azure"
   description="Guidance on caching to improve performance and scalability."
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

# キャッシング ガイド

![](media/best-practices-caching/pnp-logo.png)

キャッシュは、パフォーマンスを向上させることを目的とする一般的な手法、
アクセスされるデータを一時的に頻繁にコピーすることによってシステムのスケーラビリティ
高速なストレージ アプリケーションの近くに位置します。もしこの高速データ ストレージ
元のキャッシュよりアプリケーションに近い方には、します。
提供することによってクライアント アプリケーションの応答時間を改善することができます。
データより迅速に。キャッシュは、クライアントが繰り返しインスタンスする場合最も効果的です
データが比較的静的なまま場合は特に同じデータを読み取り、
元のデータ ストア キャッシュの速度に比べて遅いが、
や遠く離れたときネットワーク ・ レーテンシーは、高レベルの競合の対象
遅くなる可能性があります。

## 分散アプリケーションでキャッシュ

分散アプリケーションは、通常の一方または両方を実装、
次の戦略データをキャッシュする場合:

- アプリケーションまたはサービスのインスタンスを実行しているコンピューター上でのデータのローカルの開催するプライベート キャッシュを使用してください。
- 複数のプロセスまたは複数のコンピューターでアクセスできる一般的なソースとして、共有キャッシュを使用します。

両方のケースでキャッシングすることができます (プロセスを提供することによって実行されるクライアント側
web ブラウザーやデスクトップ アプリケーションなど、システムのユーザインターフェイス)
サーバー側 (ビジネス サービスの提供プロセスによっておよび/または
リモートで実行する)。

### プライベート キャッシュ

キャッシュの最も基本的なタイプはアドレスで開催されたメモリ内ストアです。
1 つのプロセスの空間、実行するコードで直接アクセス
そのプロセス。このタイプのキャッシュは非常に迅速にアクセスでき
適度な量を格納するための非常に効果的な戦略を提供します。
静的なデータをキャッシュのサイズとして通常拘束されて、
プロセスをホストするコンピューターで利用できるメモリの量。場合します。
メモリで物理的に可能であるより多くの情報をキャッシュする必要があります。
ローカル ファイル システムにキャッシュされているデータを記述できます。これは、します。
必ずしもデータ メモリ内に保持する必要がありますよりアクセスが遅くなります。
速くより信頼性の高いネットワーク経由でデータを取得するがあります。

このモデルを使用してアプリケーションの複数のインスタンスがある場合
同時に実行して、各アプリケーション インスタンスがあります独自
独立したキャッシュ データの独自のコピーを保持します。

いくつかの元のデータのスナップショットとしてキャッシュの考える必要があります。
過去にポイントします。そうだこのデータは静的ではない場合、
別のアプリケーションのインスタンスの異なるバージョンを保持する、
それらのキャッシュ内のデータ。これらによって実行されるため、同じクエリ
図 1 に示すように、インスタンスは、異なる結果を返すことができます。

![アプリケーションの別のインスタンスのメモリ内キャッシュを使用してください。](media/best-practices-caching/Figure1.png)

_図 1: アプリケーションの別のインスタンスのメモリ内キャッシュを使用してください。_

### 共有キャッシュ

共有キャッシュを使用して、データ可能性があります懸念を軽減するために助けることができます。
各キャッシュに異なるメモリ内キャッシュで発生することができます。共有
キャッシュにより別のアプリケーション インスタンスが、同じを参照してください。
別の場所にキャッシュを配置することによってキャッシュされたデータのビュー
図 2 に示すように、別のサービスの一部としてホスト通常。

![共有キャッシュを使用_](media/best-practices-caching/Figure2.png)

_図 2: 共有キャッシュを使用_

共有キャッシュのアプローチを使用しての重要な利点は、
スケーラビリティを提供することができます。多くの共有キャッシュ サービスします。
サーバーのクラスターを使用してによって実装されており、ソフトウェアを利用します。
透過的な方法でクラスターにデータを分散します。、
アプリケーション インスタンスは、単にキャッシュ サービスに要求を送信します。
基盤となるインフラストラクチャが判断して、
クラスター内のキャッシュされたデータの場所です。容易に拡張できる、
多くのサーバーを追加してキャッシュします。

共有キャッシュの不利な点に近づくキャッシュは、
それもはやローカル各を開催ですので、アクセスに時間がかかります
アプリケーション インスタンス、および別を実装するための要件
キャッシュ サービスは、複雑さをソリューションに追加できます。

## キャッシュの使用に関する考慮事項

以下は考慮事項の詳細について説明します。
設計およびキャッシュを使用しています。

### データがキャッシュされるとき

キャッシュは、パフォーマンス、スケーラビリティ、および可用性を飛躍的に向上することができます。多くのデータ
なとより大きい、このデータにアクセスする必要があるユーザー数が大きい
なる遅延や処理に関連する競合を減らすことによってキャッシュの利点
大量の元データの同時実行要求を格納します。たとえば、データベース
同時接続が、共有から取得するデータの限られた数をサポート可能性があります。
基になるデータベースではなくキャッシュ データにアクセスするクライアント アプリケーションをことができます。
場合でも、利用可能な接続数は現在使い果たされます。さらに、もし、
データベースが使用できなくなると、クライアント アプリケーションを使用して続行することができるかもしれない、
データがキャッシュに保持されます。

よく、それは頻繁に変更が読み取られるデータのキャッシュを検討してください。
(データ書き込み操作と読み取り操作の割合が高いを持っている)。ただし、
重要な情報の信頼できるストアとして、キャッシュを使用しないでください。必要があります。
あなたのアプリケーションを失うことができないすべての変更に常に保存されることを確認します。
永続的なデータ ストア。これで、キャッシュが利用できない場合、アプリケーションことができます。
データ ストアを使用して動作するように引き続き、重要な失われません
情報。

### データ ・ キャッシュの人口戦略の種類

キャッシュを効果的に使用する鍵は最も適切なデータを決定します。
キャッシュ、およびキャッシュを適切なタイミングで。キャッシュにデータを追加することがあります。
アプリケーションによって取得された最初の時間を要求するアプリケーションに必要なので、
データ ストアとそれ以降のアクセスが満たされる 1 回だけデータをフェッチします。
キャッシュを使用します。

また、キャッシュが部分的または完全にデータを設定する、事前に
通常、アプリケーションの起動時 (シードと呼ばれるアプローチ)。しかし、それは可能性があります。
このアプローチが課すことができる、大容量のキャッシュのシードを実装することをお勧めではないです。
元のデータ ストア、アプリケーションの実行開始時に突然、高負荷。

使用パターンの分析が決定する助けることができる多くの場合するかどうか完全にまたは部分的に
あらかじめしておくキャッシュとキャッシュすべきデータを選択します。たとえば、
静的なユーザー プロファイル データとキャッシュをシードする有用でしょう
(多分毎日)、定期的にアプリケーションをご利用のお客様が、
週に 1 回だけアプリケーションをご使用のお客様。

データには変更または変更されるとうまく動作通常キャッシュ
頻度が低い。リファレンス情報については、製品と価格設定などがあります。
e コマース アプリケーション、または高価な共有の静的リソースの情報
構築します。このデータの一部またはすべてのアプリケーションをキャッシュに読み込むことができます。
起動時のリソースに対する要求を最小限に抑えるため、パフォーマンスを向上させる。あります。
参照データを定期的に更新するバック グラウンド プロセスを持つことが適当
それは日付、またはキャッシュを更新するようにキャッシュを参照する場合
データの変更。

キャッシュは動的データのためあまり有用ことがありますいくつかの例外がありますが
この考察 (後述の非常に動的なデータのキャッシュ」を参照してください。
詳細についてはガイダンス)。時元のデータを定期的に変更、どちらか
キャッシュされた情報はすぐに古くなることができるまたは維持のオーバーヘッド
元のデータ ストアと同期してキャッシュの有効性が低下します。
キャッシュ。キャッシュがへの完全なデータが含まれていないことに注意してください、
エンティティ。たとえば、データ項目は、銀行などの複数値を持つオブジェクトを表します
名前、アドレス、およびアカウントの残高を持つ顧客、これらの要素のいくつかの可能性があります。
まま静的 (名前とアドレス)、他 (口座残高) など
ダイナミックながあります。これらの状況では、静的をキャッシュすると便利かもしれません
データの一部のみを取得 (または計算) として残りの情報と
それが必要な場合。

決定するためのパフォーマンス テストと利用状況分析が行われるべきかどうか
キャッシュ、またはその両方の組み合わせの前の人口やオンデマンドの読み込みが
適切です。ボラティリティの組み合わせに基づいて決定をする必要がありますが、
データの使用パターン。キャッシュの使用率とパフォーマンスの分析は
重い負荷が発生しする必要がありますアプリケーションでは特に重要
拡張性の高い。たとえば、拡張性の高いシナリオでそれに意味をなさない可能性があります。
ピーク時にデータ ストアの負荷を減らすためにキャッシュをシードします。

キャッシュも、アプリケーションが計算を繰り返しを避けるために使用できます。
実行しています。操作データを変換または複雑な計算を実行する場合
それは、キャッシュの操作の結果を保存できます。もし同じ計算
アプリケーションことができますから結果を取得して単にその後、必要です。
キャッシュ。

アプリケーションは、キャッシュに保持されているデータを変更できますが、考慮する必要があります、
いつでも消えることができる一時的なデータ ストアとしてキャッシュします。保存しません。
貴重なデータ、キャッシュでのみが、情報を維持することを確認します。
オリジナルのデータ ストアにも。これで、キャッシュする必要がありますになる場合
利用できない場合、あなたはデータを失う可能性を最小限に抑えます。

### 非常に動的なデータのキャッシュ

永続的なデータ ストアで急速に変更を課すことができる情報を格納します。
システムのオーバーヘッドです。たとえば、常にレポートされるデバイス
ステータスまたはいくつかの他の測定。これをキャッシュにしないアプリケーションを選択した場合
データに基づいて、キャッシュされた情報がほぼ常に時代遅れになる、
同じ意識を保存して、この情報を取得する場合は true かもしれない
データ ストアから保存し、このデータを取得するのにかかる時間にそれがあります。
変更。このような動的に格納する利点を考慮します。
永続的なデータ ストアではなくキャッシュに直接情報です。場合は、
データが重要ではない、監査することを必要としないし、それは問題ではないです。
場合は、時折変更失われます。

### キャッシュ データの有効期限を管理します。

ほとんどの場合、キャッシュに保持されているデータは元のデータのデータのコピー
格納します。引き起こして、それがキャッシュされた後に元のデータ ストア内のデータを変更可能性があります。
キャッシュされたデータが古くなるを。多くのキャッシング システムを構成することを有効にする、
キャッシュ データを有効期限が切れるし、データが期限切れである期間を短縮します。

キャッシュされたデータの有効期限が切れるキャッシュから削除され、アプリケーションにする必要があります。
(置くことが新しくフェッチ元のデータ ストアからデータを取得します。
情報キャッシュに戻す)。既定の有効期限ポリシーを設定することができますとあなた
キャッシュを構成します。多くのキャッシュ サービスで、有効期限を決めることも
プログラムによってキャッシュにそれらを格納する個々 のオブジェクトの期間
(いくつかのキャッシュを有効にすると、絶対値として有効期限を指定すること、または
スライド値として原因じゃない場合、キャッシュから削除するアイテム
指定した時間内にアクセス。この設定は、任意のキャッシュ全体
指定したオブジェクトに対してだけ有効期限ポリシー。

> [AZURE。メモ] キャッシュと慎重に含まれているオブジェクトの有効期間を検討します。短すぎるとして、オブジェクトが余りにすぐに切れるし、キャッシュを使用する利点が減少します。あまりにも長い期間を加えると、データが古くなって危険があります。

可能維持するデータが許可されている場合は、キャッシュがいっぱいです。
長い時間の居住者。この場合、いずれかをする新しい項目を追加する要求、
キャッシュと呼ばれるプロセスで、強制的に削除されるいくつかの項目があります。
立ち退き。キャッシュ サービスは、通常最も最近使用 (LRU) 上のデータを削除します。
基礎が通常このポリシーをオーバーライドして、できます項目を防ぐ
削除。しかし、このアプローチを採用する場合、あなたはキャッシュを超える危険性、
メモリには項目を追加しようと、使用可能なアプリケーションがあります。
キャッシュには、例外で失敗します。

一部のキャッシュの実装は、追加削除ポリシーにあります。これら
通常最も最近使用されたポリシーを含める (期待する、
データ必要はありませんもう一度)、(最も古いデータは、先入れ先出しのポリシー
削除最初)、またはトリガーされたイベントに基づいて明示的な削除 (など、
変更されるデータ)。

### クライアント側キャッシュ内のデータを無効化

クライアント側のキャッシュに保持されたデータはおおむねの外
クライアントにデータを提供するサービスの後援サービス
情報を追加、削除するクライアントを強制的ことはできません直接、
クライアント側のキャッシュ。つまり、それはクライアントを使用することが可能
構成が不十分なキャッシュ (たとえば、有効期限ポリシーがないです。
キャッシュされた古い情報を使用し続けるに適切に実装)
ローカル元のデータ ソース内の情報が変更されたとします。

HTTP を介してデータを提供する web アプリケーションを構築する場合
接続、暗黙的に web クライアントを強制的ことができます (ブラウザーなどや
web プロキシ) リソースが更新された場合、最新の情報を取得するには
そのリソースの URI を変更します。Web クライアントは、通常、URI を使用してください。
クライアント側のキャッシュのキーとしてリソースの URI を変更するので
により、web クライアントのすべてのキャッシュ済みバージョンを無視する、
リソース、代わりに新しいバージョンを取得します。

## キャッシュ内の同時実行の管理

キャッシュは、頻繁の複数のインスタンスで共有するために設計されている、
アプリケーション。各アプリケーション インスタンスが読み書きのデータの変更
キャッシュ。その結果、同じ同時実行の問題が発生します。
任意の共有データ ストアのキャッシュにも当てはまります。状況で
アプリケーションがキャッシュに保持されているデータを変更する必要がある場合があります。
アプリケーションの 1 つのインスタンスで更新が行われるようにする必要があります。
盲目的に別のインスタンスで行った変更を上書きしません。

データの性質との衝突の可能性に応じてください。
同時実行する 2 つの方法のいずれかを採用することができます。

- __楽観的。__ アプリケーションは、キャッシュ内のデータはすぐにそれを更新する前に、それが取得されてから変わったかどうかを確認します。データが同じでも変更は可能です。それ以外の場合、アプリケーションが (この決定を行うビジネス ロジックは特定のアプリケーションになります) それを更新するかどうかを決定します。この方法は、更新が頻繁に、または衝突が発生する可能性が状況に適しています。
- __悲観的。__ アプリケーションは、別のインスタンスがデータを変更するを防ぐためにそれを取得するときに、キャッシュ内のデータをロックします。このプロセスにより、衝突は発生することはできませんが、同じデータを処理する必要があります他のインスタンスをブロック可能性があります。ペシミスティック同時実行制御ソリューションのスケーラビリティに影響を与えることができるし、短時間の操作にのみ使用する必要があります。このアプローチは、アプリケーション キャッシュ内の複数の項目を更新し、これらの変更が一貫して適用されていることを確認する必要がある場合は特に、衝突が本当らしい、状況に適した可能性があります。

### 高い可用性とスケーラビリティを実装してパフォーマンスを向上させる

キャッシュは、データのプライマリ ・ リポジトリをしないでください。これはロールです。
キャッシュの設定を元のデータ ストア。、
元のデータ ストアの永続性を確保するための責任は、
データ。

可用性の重要な依存関係を導入することに注意してください。
ソリューションに共有キャッシュ サービス。アプリケーションする必要があります。
場合は、機能を継続することができる共有のキャッシュを提供するサービス
無効です。アプリケーションがハングするか待っている間失敗しません。
キャッシュ サービスの再開します。したがって、アプリケーション必要があります。
キャッシュ サービスの可用性を検出し、フォールバックする準備
元のデータの保存、キャッシュにアクセスできない場合。、
[回路ブレーカーのパターン](http://msdn.microsoft.com/library/dn589784.aspx) このシナリオを処理するために便利です。、
キャッシュを回復することができますと一度それになりますを提供するサービスします。
利用可能なキャッシュ再作成できます読み取りフォーム、
元のデータ ストアは、次の戦略など、 [キャッシュ アサイド パターン](http://msdn.microsoft.com/library/dn589799.aspx).

ただし、フォールバック元のデータ ストアにキャッシュがある場合
一時的にはシステムのスケーラビリティの影響を持つかもしれない
データ ストアがされて回収元のデータ ストア
タイムアウトの結果データの要求が殺到することが、
接続に失敗しました。考慮すべき戦略は、します。
アプリケーションの各インスタンスでローカルのプライベート キャッシュを実装します。
一緒に共有キャッシュがアプリケーションのすべてのインスタンス
アクセスします。それが最初にチェックすることができますアプリケーションでは、アイテムを取得するとき
で、ローカル キャッシュ、共有キャッシュ、最終的に元
データ ストア。データを使用してローカル キャッシュを設定できます、
共有キャッシュまたはデータベース共有キャッシュが利用できない場合。
ローカルを防ぐために構成に注意が必要です。
共有キャッシュが、それに関してあまりにも古くなってキャッシュ
共有キャッシュに到達できない場合は、バッファーとして機能します。図 3
この構造を示しています。

![ローカル、プライベート キャッシュを用いた共有キャッシュ_](media/best-practices-caching/Caching3.png)
_図 3: 共有キャッシュとローカルの専用のキャッシュを使用してください。_

いくつか比較的長命のデータを保持する大規模なキャッシュをサポートするには
キャッシュ サービスを実装する高可用性オプションを提供します。
自動フェールオーバー キャッシュができなくなった場合。このアプローチ
通常は、主に保存されているキャッシュのデータをレプリケートします。
キャッシュ サーバーはセカンダリ キャッシュ サーバー、およびへの切り替え、
プライマリ サーバーが失敗した場合はセカンダリ サーバーまたは接続が
失った。複数への書き込みに伴う待ち時間を減らすために
目的地、プライマリ キャッシュにデータが書き込まれる
サーバー、セカンダリ サーバーへのレプリケーションが発生します。
非同期的にします。 このアプローチは、いくつかの可能性につながる
キャッシュされた情報が、障害時に失われるが、
このデータの割合は、全体と比較して小さくなるはず
キャッシュのサイズ。

パーティションに有益な共有キャッシュが大きい場合があります、
キャッシュされたデータの競合の可能性を減らすためにノード間で、
スケーラビリティが向上します。多くの共有キャッシュ機能をサポートします。
動的に追加 (および削除) ノード間でデータを再配分と
パーティション。このアプローチは、それによってクラスタ リングを伴うことがあります、
ノードのコレクションとしてクライアント アプリケーションに提示します。
シームレスに 1 つのキャッシュが内部的にデータを分散します。
次のいくつかのノード間流通戦略を定義済み
これは均等に負荷を分散させます。、 [データのパーティション分割のガイダンス文書](http://msdn.microsoft.com/library/dn589795.aspx)
マイクロソフトのウェブサイトはできるだけの詳細についてを提供します。
パーティション分割の方法。

クラスタ リング追加することも一層キャッシュの可用性場合は、
ノードが失敗した場合、キャッシュの残りの部分は、まだアクセス可能です。
レプリケーションと連動で多用されるクラスタ リング
フェイル オーバー。各ノードを複製することができますとレプリカ
迅速にオンラインにノードが失敗する場合。

多くと書き込み操作が 1 つのデータを含む可能性が高い
値またはオブジェクト。ただしがある場合があるかもしれません
必要に応じて保存または大量のデータを迅速に取得します。
たとえば、何百もの書き込み含むことができるキャッシュをシードまたは
キャッシュ、またはアプリケーションへのアイテムの何千もする必要があります。
としてキャッシュから多数の関連アイテムを取得します。
同じ要求の一部です。多くの大規模なキャッシュを提供するバッチ
これらの目的は、クライアント アプリケーションの操作
大量の 1 つの要求にアイテムをパッケージ化し、
多数の実行に伴うオーバーヘッドを削減
小さな要求。

## キャッシュおよび最終的な一貫性

キャッシュ アサイド パターンは、アプリケーションのインスタンスに依存します。
最も最近にアクセス キャッシュへの読み込みと
データの一貫性のあるバージョンです。実装するシステムで
イベンチュアル ・ コンシステンシー (レプリケートされたデータ ストア) などが
ケースではないです。アプリケーションの 1 つのインスタンスを変更します。
データ項目し、その項目のキャッシュされたバージョンを無効にします。もう一つ
アプリケーションのインスタンスからこの項目を読むしようとする可能性があります。
キャッシュはキャッシュ ・ ミスが発生するからデータを読み取るので、
データ保存し、それをキャッシュに追加します。ただし、データ ストアの場合
他のレプリカと完全に同期されていない、
アプリケーション インスタンスが読み取りできをキャッシュに設定、
古い値。

データの整合性の処理の詳細についてを参照してください、
Microsoft の web サイト上のデータの一貫性指導のページ。

### キャッシュされたデータの保護

関係なく、キャッシュ サービスを使用して、検討してください。
不正からキャッシュで保持されるデータを保護する方法
アクセスします。2 つの主な懸念があります。

- キャッシュ内のデータのプライバシー。
- キャッシュ間のデータのプライバシーの流れと、
  アプリケーション キャッシュを使用します。

キャッシュ内のデータを保護するためにキャッシュ サービスを実装する場合があります。
要求する認証メカニズムのアプリケーション
、自分自身を識別し、承認スキーム
どの id が、キャッシュ内のデータにアクセスできるを指定しますと
これらの id は操作 (読み取りと書き込み)
実行を許可します。関連するオーバーヘッドを削減するには
一度 id が付与されたデータの読み書き
書き込みや識別に使用できるキャッシュへの読み取りアクセス
キャッシュ内のデータ。アクセスを制限する必要がある場合
キャッシュされたデータのサブセットは、次の操作を実行できます。

- (別のキャッシュを使用して、キャッシュをパーティション分割します。
  サーバー) の id にアクセスのみを許可し、
  パーティションを使用して、許可する必要がありますか
- 別のキーを使用して、各サブセットのデータを暗号化します。
  のみ id に暗号化キーを提供して
  各サブセットへのアクセスが必要です。クライアント アプリケーション
  キャッシュ内のデータのすべてを取得することができます。
  だけそれのデータを復号化できるようになりますが、
  鍵を持っています。

それはデータを保護するためにフロー キャッシュに出入りします。
ネットワークで提供されるセキュリティ機能に依存しています。
インフラストラクチャに接続するクライアント アプリケーションを使用している、
キャッシュ。敷地内のサーバーを使用して、キャッシュを実装する場合
クライアント アプリケーションをホストする同じ組織内
その後、ネットワーク自体の分離をしないする必要があります。
任意の追加の手順を実行します。キャッシュがリモートにある場合、
(このようなパブリック ネットワークを介して TCP または HTTP 接続が必要です。
・ インターネットとして SSL を実装することを検討してください。

## Microsoft Azure のキャッシュを実装するための考慮事項

Azure では、Azure Redis キャッシュを提供します。これは実装です。
オープン ソースのサービスとして実行される Redis のキャッシュ、
Azure のデータ センターです。それはすることができますキャッシュ サービスを提供します
かどうか、任意の Azure アプリケーションからアクセス アプリケーション
クラウド サービス、web サイト、または内部として実装は、
Azure の仮想マシンです。キャッシュは、クライアントによって共有することができます。
適切なアクセス キーを持つアプリケーション。

Redis は高性能のキャッシュ ソリューションを提供します。
可用性、スケーラビリティおよびセキュリティ。それは通常実行します。
1 つまたは複数の専用マシンに分散サービスとして、
メモリにできるだけ多くの情報を格納しようとしました。
高速アクセスを確保します。このアーキテクチャを使用すると、
低レーテンシーとする必要性を減らすことによる高スループット
遅い I/O 操作を実行します。

Azure Redis キャッシュは多くの様々 な互換性のあります。
クライアント アプリケーションで使用する Api。既存がある場合
Redis 社内の実行を既に使用しているアプリケーション、
紺碧 Redis キャッシュ キャッシュへの迅速な移行パスを提供します。
クラウドで。

> [AZURE。メモ] Azure には、キャッシュの管理サービスも提供します。これ
  サービスは、Microsoft の AppFabric キャッシュ エンジンに基づきます。それ
  共有できる分散キャッシュを作成することができます。
  疎結合アプリケーション。キャッシュ上でホストされて
  Azure のデータ センターで実行されている高性能サーバー。
  ただし、このオプションは推奨されません、だけ
  構築されている既存のアプリケーションをサポートします。
  それを使用します。すべての新しい開発のため使用する Azure Redis
  代わりにキャッシュします。
>
> さらに、Azure は、ロールのキャッシュをサポートしています。この機能
  クラウド サービスに固有のキャッシュを作成できます。
  キャッシュが web またはワーカー ロールのインスタンスによってホストされていると
  同じの一部として操作の役割によってのみアクセスできます。
  クラウド サービス展開単位 (デプロイメント ユニットがセット
  ロール インスタンスの特定の提供するクラウド サービスとして展開
  地域)。キャッシュがクラスター化されると、すべてのインスタンスの
  キャッシュをホストする同一の配置単位内の役割
  同じキャッシュ クラスターの一部となります。既存のアプリケーション
  、そうし続ける役割でキャッシュを使用することができますが、
  Azure Redis キャッシュへの移行より多くの利益をもたらす可能性があります。
  Azure Redis キャッシュを使用するかどうかの詳細について
  または、ロール内の-キャッシュ、ページをご覧ください
  [Azure キャッシュ サービスは私にとって正しいですか。](http://msdn.microsoft.com/library/azure/dn766201.aspx) Microsoft の web サイト。


### Redis の特徴

Redis は簡単なキャッシュ サーバー; より分散メモリを提供します。
多くの一般的なシナリオをサポートする豊富なコマンド セットを備えたデータベース
使用例で説明したよう Redis 後述のキャッシュ
ドキュメント。このセクションは Redis 主要な機能のいくつかをまとめたもの
提供します。

### メモリ内データベースとして redis します。

Redis では、読み取りと書き込み操作の両方をサポートしています。(これは一時的なデータ ストアとして考慮されるべき) 多くのキャッシュとは異なり書き込みはいずれかに定期的にローカル スナップショット ファイルまたは追加のみのログ ファイルに格納される、システム障害から保護できます。すべての書き込みは非同期であり、クライアントの読み取りとデータの書き込みをブロックしません。とき Redis 実行を開始、スナップショットまたはログ ファイルからデータを読み取り、使用してメモリ内にキャッシュを構築します。詳細については、次を参照してください。 [REDIS の永続性](http://redis.io/topics/persistence) Redis のウェブサイト。

> [AZURE。メモ] Redis はすべての書き込みがイベントログに保存されることを保証しません
  のみいくつかの秒を失う必要があります最悪の時が、致命的な障害の
  データの価値があります。キャッシュとして機能する意図しないことを覚えて、
  権限のあるデータ ソース、およびそれがアプリケーションの責任
  正常に保存が重要なデータを確実にキャッシュを使用して、
  適切なデータ ストア。詳細については、キャッシュ アサイド パターンを参照してください。

#### Redis データ型

Redis 値が単純型またはリスト、ハッシュなどの複雑なデータ構造を含めることができますキーおよび値のストア、および設定します。これらのデータ型の分割不可能な操作のセットをサポートしています。キーは永続的またはその時点でキーと対応する値は自動的にキャッシュから削除されますに住む限られた時間でタグ付けすることができます。Redis キーと値の詳細については、ページを参照してください。 [Redis データ型および抽象化の導入](http://redis.io/topics/data-types-intro) Redis のウェブサイト。

#### Redis レプリケーションとクラスタ リング

Redis は、マスターと下位の可用性を確保し、スループットを維持するためにレプリケーションをサポートしていますRedis マスター ノードへの書き込み操作が 1 つにレプリケートまたはより多くの従属ノード、および読み取り操作は、マスターまたは下位図形のいずれかによって提供できます。ネットワーク パーティションが発生した場合は、部下がデータを提供して、透過的に再同期マスターとの接続が再確立されたときに続行できます。詳細についてを参照してください、 [レプリケーション](http://redis.io/topics/replication) Redis ウェブサイトのページ。

Redis では、クラスタ リング、透過的に破片にサーバー間でデータを分割し、負荷分散することができる提供しています。この機能は、Redis の新しいサーバーを追加することができ、データ キャッシュのサイズに従って増加のスケーラビリティを向上させます。さらに、クラスター内の各サーバーは、クラスター内の各ノード間で可用性を確保するマスターと下位のレプリケーションを使用してレプリケートできます。クラスタ リングと分割の詳細についてを参照してください、 [Redis クラスター チュートリアル ページ](http://redis.io/topics/cluster-tutorial) Redis のウェブサイト。

> [AZURE。メモ] 紺碧 Redis キャッシュは現在クラスタ リングをサポートしていません。Redis クラスターを作成する場合は、カスタム Redis サーバーを構築できます。詳細については、このドキュメントの後半のカスタム Redis キャッシュを構築を参照してください。

### メモリの使用量を redis します。

Redis キャッシュは、ホスト コンピューターで利用可能なリソースに応じて有限サイズを持ちます。Redis サーバーを構成するときは、使用できるメモリの最大量を指定できます。Redis キャッシュ内のキーは、有効期間、その後自動的にキャッシュから削除で構成できます。この機能は、メモリ内キャッシュが古いまたは古いデータで満たされているを防ぐことができます。

メモリがいっぱいになりました、Redis できます自動的にポリシーの数に従って、キーとその値を削除します。既定では (少なくとも最近使用)、LRU が選択することも、ランダムにキーを削除または削除をオフにするなど他の政策全体で (その場合、項目をキャッシュに追加するのには失敗しますがいっぱいの場合)。ページ [LRU キャッシュとして Redis を使用してください。](http://redis.io/topics/lru-cache) 多くの情報を提供します。

### Redis トランザクションとバッチ

Redis enables a client application to submit a series of operations that read and write data in the cache as an atomic transaction. All of the commands in the transaction are guaranteed to be executed sequentially and no commands issued by other concurrent clients will be interwoven between them. However, these are not true transactions as a relational database would perform them. Transaction processing consists of two stages; command queuing and command execution. During the command queuing stage, the commands that comprise the transaction are submitted by the client. If some sort of error occurs at this point (such as a syntax error, or the wrong number of parameters) then Redis will refuse to process the entire transaction and discard it. During the execution phase, Redis performs each queued command in sequence. If a command fails during this phase Redis will continue with the next queued command and it does not roll back the effects of any commands that have already been executed. This simplified form of transaction helps to maintain performance and avoid performance problems caused by contention. Redis does implement a form of optimistic locking to assist in maintaining consistency. For detailed information about transactions and locking with Redis, visit the [取引ページ](http://redis.io/topics/transactions) Redis のウェブサイト。

Redis では、要求の非トランザクション バッチ処理もサポートしています。Redis サーバーにコマンドを送信するクライアントを使用して Redis プロトコルでは、同じ要求の一部として一連の操作を送信するクライアントをできます。これは、ネットワーク上のパケットの断片化を減らすために助けることができます。バッチを処理すると、各コマンドが実行されます。トランザクションとは異なり、これらのコマンドのいずれかが不正な場合拒否されますが、残りのコマンドが実行されます。また、バッチ内のコマンドが処理される順序の保証はありません。

### Redis セキュリティ

Redis は、データへの高速アクセスを提供することに取り組むし、信頼された環境内で実行し、信頼されたクライアントのみがアクセスするように設計。Redis (これは推奨されませんが認証を完全に削除することが可能です) パスワード認証に基づいて制限されたセキュリティ モデルのみをサポートします。すべての認証されたクライアントは、同じグローバル パスワードを共有し、同じリソースへのアクセスがあります。包括的なログインのセキュリティが必要な場合 Redis サーバーの前にあなた自身のセキュリティ レイヤーを実装する必要があります、すべてのクライアント要求が通ればこのレイヤーを追加。Redis いない、信頼されていないまたは認証されていないクライアントに直接公開するべき。

コマンドを無効にすることまたは名前を変更して (とだけ新しい名前と特権クライアントを提供する) でアクセスを制限できます。

Redis は何らかのデータの暗号化、クライアント アプリケーションによってすべてのエンコードを行う必要がありますので直接はサポートしていません。さらに、SSL プロキシを実装する必要がある場合はネットワークを経由するデータを保護する必要がありますので、Redis ではトランスポートのセキュリティの任意のフォームを提供しません。

詳細についてを参照してください、 [Redis セキュリティ](http://redis.io/topics/security) Redis ウェブサイトのページ。

> [AZURE。メモ] 紺碧 Redis キャッシュ クライアントが接続する際、独自のセキュリティ層を提供します。基になる Redis
  サーバーはパブリック ネットワークに公開されません。

### Azure Redis キャッシュを使用

Azure Redis キャッシュで Azure のデータ センターにホスト サーバーで実行されている Redis サーバーにアクセスを提供します。それは、アクセス制御とセキュリティ ファサードとして機能します。Azure 管理ポータルを使用して、キャッシュをプロビジョニングすることができます。ポータルは、(プライバシー) の SSL 通信と 250 MB まで、99.9% の可用性の SLA を持つマスターと下位レプリケーション レプリケーション (可用性保証のない) 共有ハードウェア上で実行することがなくキャッシュをサポートする専用のサービスとして実行されている 53 GB キャッシュに至る、定義済みの構成の数を提供します。

Azure 管理ポータルを使用しても、キャッシュの削除ポリシーを構成して提供ロールにユーザーを追加するキャッシュへのアクセスを制御所有者、寄稿者、読者。これらのロールのメンバーが実行できる操作を定義します。たとえば、所有者ロールのメンバー (セキュリティを含む) キャッシュとその内容を完全に制御がある、共同作成者ロールのメンバーが、キャッシュ内で情報を読み書きできるし、読者の役割のメンバーのみ、キャッシュからデータを取得できます。

ほとんどの管理タスクは Azure 管理ポータルを通じて実行され、Redis の標準バージョンで使用できる管理コマンドの多くが利用できない場合は、プログラムを使用して、構成を変更する機能を含むため Redis サーバーをシャット ダウン構成追加の奴隷、または強制的にデータをディスクに保存します。

The Azure management portal includes a convenient graphical display that enables you to monitor the performance of the cache. For example, you can view the number of connections being made, the number of requests performed, the volume of reads and writes, and the number of cache hits versus cache misses. Using this information you can determine the effectiveness of the cache and if necessary switch to a different configuration or change the eviction policy. Additionally, you can create alerts that send email messages to an administrator if one or more critical metrics fall outside of an expected range. For example, if the number of cache misses exceeds a specified value in the last hour, an administrator could be alerted as the cache may be too small or data may be being evicted too quickly.

CPU、メモリ、およびキャッシュのネットワーク使用率を監視することも。

> [AZURE。メモ] 紺碧 Redis キャッシュのデータベースではなくキャッシュとして純粋に行動するものです。その結果、現在 Redis の永続化を実装しません。

詳細については、作成し、Azure Redis のキャッシュを構成する方法を示す例を参照してくださいページ [紺碧 Redis キャッシュの周りにラップ](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) 紺碧のブログ。

## キャッシュ セッション状態と HTML 出力

Azure の web ロールを使用して実行すると、保存できることセッション状態情報と HTML 出力の Azure Redis キャッシュ内のアプリケーションを web ASP.NET を構築するかどうか。Azure Redis キャッシュ セッション状態プロバイダーは、ASP.NET web アプリケーションの異なるインスタンス間でセッション情報を共有することができます、クライアント-サーバ アフィニティが使用できないし、セッション データをメモリ内のキャッシュは適切でない web ファーム場合に非常に便利です。

Azure Redis キャッシュ提供のセッション状態プロバイダーを使用してを含むいくつかの利点。

- それは多数のスケーラビリティの向上を提供する、ASP.NET web アプリケーションのインスタンス間でセッション状態を共有できます。
- 複数のリーダーと単一ライターの同じセッション状態データへの制御、同時アクセスをサポートしていて、
- 圧縮を使用してメモリを節約し、ネットワーク パフォーマンスを向上させることできます。

詳細については"を参照してください、 [Azure の ASP.NET のセッション状態プロバイダーは、キャッシュを Redis します。](http://msdn.microsoft.com/library/azure/dn690522.aspx) Microsoft の web サイト上のページ。

> [AZURE。メモ] Azure 環境の外部で実行する ASP.NET アプリケーションに、Azure Redis キャッシュ セッション状態プロバイダーを使わない。Azure の外部からキャッシュへのアクセスの待機時間は、データ キャッシュのパフォーマンス上の利点をなくすことができます。

同様に、Azure Redis キャッシュに出力キャッシュ プロバイダーは ASP.NET web アプリケーションによって生成される HTTP 応答を保存することができます。複雑な HTML 出力をレンダリングするアプリケーションの応答時間を短縮することができます Azure Redis キャッシュと出力キャッシュ プロバイダーを使用してあらためて出力この HTML を生成するのではなく、キャッシュに共有出力のフラグメントのアプリケーション インスタンスを生成するような反応をすることができますを使用します。 詳細については"を参照してください、 [Azure の ASP.NET 出力キャッシュ プロバイダーは、キャッシュを Redis します。](http://msdn.microsoft.com/library/azure/dn798898.aspx) Microsoft の web サイト上のページ。

## カスタム Redis キャッシュを構築

The Azure Redis cache acts as a façade to the underlying Redis servers. Currently it supports a fixed set of configurations but does not provide for Redis clustering. If you require an advanced configuration that is not covered by the Azure Redis cache (such as a cache bigger than 53GB) you can build and host your own Redis servers by using Azure virtual machines. This is a potentially complex process as you may need to create several VMs to act as master and subordinate nodes if you want to implement replication. Furthermore, if you wish to create a cluster, then you will need multiple masters and subordinate servers; a minimal clustered, replication topology that provides a high degree of availability and scalability comprises at least 6 VMs organized as 3 pairs of master/subordinate servers (a cluster must contain at least 3 master nodes). Each master/subordinate pair should be located close together to minimize latency, but each set of pairs can be running in different Azure datacenters located in different regions if you wish to locate cached data close to the applications that are most likely to use it. The page [Azure の CentOS Linux VM 上 Redis で実行](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) マイクロソフトの web サイトの構築し、Azure の VM として実行している Redis ノードを構成する方法を示す例について説明します。

この方法で Redis キャッシュを実装する場合あなたが監視、管理、およびサービスの保護を担当に注意してください。

## Redis キャッシュのパーティション分割

キャッシュをパーティションでは、複数のコンピューターにわたってキャッシュを分割します。この構造体は、いくつかの利点を含む、1 つのキャッシュ サーバーを使用しています。

- キャッシュを作成する 1 つのサーバーに格納できるよりもはるかに大きいです。
- 可用性を向上させるサーバ間でデータを配布します。保持するデータのみが利用可能なは 1 つのサーバーが失敗したまたはアクセスできなくなったり、残りのサーバー上のデータをアクセスもできます。キャッシュこのキャッシュされたデータとして重要ななっていないデータの一時的なコピーのみ、データベースに保持されて、アクセスできなくなったサーバー上のキャッシュされたデータを別のサーバーに代わりにキャッシュできます。
- パフォーマンスとスケーラビリティが向上、サーバーに負荷を分散します。
- Geolocating データ遅延を削減でき、それにアクセスするユーザーの近くに配置。

パーティション分割の最も一般的な形式は、キャッシュ、シャーディングです。この戦略では、各パーティション (または破片) は、独自の権利で Redis キャッシュです。データは、特定のパーティションにデータを分散するさまざまなアプローチを使用することができます分割ロジックを使用してによって監督されます。、 [分割パターン](http://msdn.microsoft.com/library/dn589797.aspx) 分割の実装の詳細を提供します。

Redis キャッシュのパーティション分割を実装するには、次の方法のいずれかを採用します。

- _サーバー側のクエリをルーティングします。_ この手法では、クライアント アプリケーションのいずれかに要求を送信、
  Redis サーバー キャッシュ (おそらく最も近いサーバー) を構成します。各 Redis サーバー格納します。
  それを保持していると、これに関する情報も格納パーティションを記述するメタデータ
  パーティションは、他のサーバーに配置されます。Redis サーバー クライアント要求を調べ、そして、もしそれ
  それは要求された操作、それ以外の場合、転送を実行をローカルで解決することができます、
  適切なサーバーに要求します。このモデルは Redis クラスタ リングによって実装され、
  詳細について、 [Redis クラスター チュートリアル](http://redis.io/topics/cluster-tutorial) Redis ウェブサイトのページ。Redis クラスタ リング
  クライアント アプリケーションは、サーバーをクラスターに追加することができます追加 Redis に対して透過的です。
  (と再分割データ) クライアントを再構成することを必要とせず。

  > [AZURE。重要です] 紺碧 Redis キャッシュは現在 Redis のクラスタ リングをサポートしていません。たい場合
  このアプローチを実装し、前述のようにカスタム Redis キャッシュを構築する必要があります。

- _クライアント側のパーティション分割。_ このモデルでは、クライアント アプリケーションには (おそらくのロジックが含まれています
  ライブラリのフォーム) Redis の適切なサーバーに要求をルーティングします。このアプローチ
  Azure Redis キャッシュで使用できます。複数 Azure Redis のキャッシュ (1 つ各データの作成します。
  パーティション)、要求を適切にルーティング クライアント側ロジックを実装
  キャッシュ。(追加 Azure Redis キャッシュを作成する場合、パーティション構成が変更された場合
  たとえば)、クライアント アプリケーションを再構成する必要があります。

- _プロキシによる分割します。_ この方式では、クライアント アプリケーションは、要求を送信します。
  仲介プロキシ サービス データのパーティション分割方法を理解して、ルート
  Redis の適切なサーバーに要求します。このアプローチは、Azure で使用もできます。
  Redis キャッシュ;プロキシ サービスは、Azure クラウド サービスとして実装することができます。これ
  アプローチはサービスの導入における複雑さの追加レベルを必要とし、
  要求は、クライアント側のパーティション分割を使用するよりも実行に時間がかかります。

ページ [Redis の複数のインスタンス間でデータを分割する方法の分割。](http://redis.io/topics/partitioning)
Redis のウェブサイトは Redis でパーティショニングの実装に関する詳細を提供します。

### Redis キャッシュ クライアント アプリケーションを実装します。

Redis 多数のプログラミング言語で記述されたクライアント アプリケーションをサポートします。.NET Framework を使用して、新しいアプリケーションを構築する場合は、StackExchange.Redis クライアント ライブラリを使用することをお勧めします。このライブラリは、Redis サーバーへの接続、コマンドの送信と応答の受信の詳細を抽象化する .NET Framework オブジェクト モデルを提供します。NuGet パッケージと Visual Studio で利用可能です。この同じライブラリを使用すると、Azure Redis キャッシュまたは Redis のカスタム キャッシュの vm ホストに接続できます。

静的に使用する Redis サーバーに接続するには `Connect` 法、 `ConnectionMultiplexer` クラスです。このメソッドが作成する接続は、クライアント アプリケーションの有効期間全体にわたる使用設計されていますおよび複数の並行スレッドで同じ接続を使用することができます。再接続しないし、切断するたび Redis 操作を実行すると、パフォーマンスが低下することができます。アドレス Redis ホストおよび、パスワードなどの接続パラメーターを指定できます。Azure Redis キャッシュを使用する場合はこれがどちらかはパスワード プライマリまたはセカンダリ キーを生成 Azure Redis キャッシュの Azure 管理ポータルを使用しています。

Redis サーバーに接続すると、キャッシュとして機能する Redis データベースのハンドルを取得できます。Redis 接続を提供します、 `GetDatabase` これを行う方法。キャッシュから項目を取得し、キャッシュを使用してデータを格納、 `StringGet` と `StringSet` メソッド。これらのメソッドが要求するパラメーターとしてキーと一致する値 (キャッシュで項目を返すか`StringGet`) このキー (キャッシュに項目を追加または`StringSet`).

Redis サーバーの場所、によって多くの操作がサーバーに要求を送信し、応答がクライアントに返される、いくつかの遅延を負うかもしれない。StackExchange ライブラリは、多くのクライアント アプリケーションの応答性を維持に役立つに公開するメソッドの非同期バージョンを提供します。これらのメソッドをサポートします [タスク ベースの非同期パターン](http://msdn.microsoft.com/library/hh873175.aspx) .NET framework。

次のコード スニペットは、という名前のメソッドを示しています。 `RetrieveItem` Redis と StackExchange ライブラリに基づくキャッシュ アサイド パターンの実装例を示します。メソッドは文字列キー値を受け取り、呼び出して Redis キャッシュから対応する項目を取得しようとすると、 `StringGetAsync` メソッド (非同期バージョン `StringGet`).使用して基になるデータ ソースからフェッチされる項目がない場合、 `GetItemFromDataSourceAsync` (これはローカル メソッドと StackExchange ライブラリの一部ではない) メソッドを使用してキャッシュに追加した、 `StringSetAsync` メソッドをそれを取得できますより迅速に次の時間。

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

、 `StringGet` と `StringSet` メソッドで取得または文字列値を格納に制限はありません。任意の項目のバイトの配列としてシリアル化をとることができます。.NET オブジェクトを保存する必要がある場合は、バイト ストリームとしてシリアル化し、キャッシュへの書き込みに StringSet メソッドを使用できます。同様に、StringGet メソッドを使用して、キャッシュからオブジェクトを読み取るし、.NET オブジェクトとしてシリアル化できます。次のコードは、IDatabase インターフェイスの拡張メソッドのセットを示しています (Redis 接続の GetDatabase メソッドを返します、 `IDatabase` オブジェクト) とキャッシュに BlogPost オブジェクトを読み書きするこれらのメソッドを使用していくつかのサンプル コード。

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

次のコードは、という名前のメソッドを示しています。 `RetrieveBlogPost` 読み取りと書き込み、シリアル化可能なこれらの拡張メソッドを使用します。 `BlogPost` キャッシュ アサイド パターンに従ってキャッシュするオブジェクト:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis サポート コマンド パイプ ラインの場合、クライアント アプリケーションは、複数の非同期要求を送信します。Redis は、同じ接続を使用してよりもむしろ受信および厳密な順序でコマンドに応答して要求を多重することができます。この方法は、ネットワークをより効率的に使用することによって遅延を減らすのに役立ちます。次のコード スニペットは、同時に 2 人の顧客の詳細を取得する例を示します。このコードは 2 つの要求を送信し、いくつか他の処理を実行 (表示されていません) 結果の受信を待機する前に。キャッシュ オブジェクトの待機メソッドは .NET Framework Task.Wait メソッドに似ています。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

ページ [紺碧 Redis キャッシュのための開発します。](http://msdn.microsoft.com/library/azure/dn690520.aspx) マイクロソフトでは、ウェブサイトは、Azure Redis キャッシュを使用できるクライアント アプリケーションを作成する方法の詳細についてを提供します。追加情報は、 [基本的な使い方のページ](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) StackExchange.Redis のウェブサイトのページ [パイプラインおよびマルチプレクサー](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) 同じ web サイトに非同期操作および Redis StackExchange ライブラリとパイプライン処理の詳細についてを提供します。 このガイダンスで後キャッシュ Redis でユース ケースは、Redis キャッシュで保持されるデータに適用することができますより高度な技術のいくつかの例を説明します。

## Redis キャッシュの使用例

The simplest use of Redis for caching concerns storing key/value pairs where the value is an uninterpreted string of arbitrary length that can contain any binary data (it is essentially  an array of bytes that can be treated as a string). This scenario was illustrated in the section Implementing Redis Cache Client Applications earlier in this guidance. You should note that keys also contain uninterpreted data, so you can use any binary information as the key, although the longer the key is the more space it will take to store, and the longer it will take to perform lookup operations. For usability and ease of maintenance design your keyspace carefully and use meaningful (but not verbose) keys. For example, use structured keys such as "customer:100" to represent the key for the customer with ID 100 rather than simply "100". This scheme enables you to easily distinguish between values that store different data types. For example, you could also use the key "orders:100" to represent the key for the order with ID 100.

バイナリ文字列は 1 次元から離れて Redis のキー/値ペア内の値はより構造化されたリストを含む情報 (ソートし、ソートなし) を設定しますとハッシュも保持できます。これらのコマンドの多くは redis これらのタイプを操作することができます包括的なコマンド セットを提供します、StackExchange などのクライアント ライブラリを .NET Framework アプリケーションで使用できます。ページ [Redis データ型および抽象化の導入](http://redis.io/topics/data-types-intro) Redis のウェブサイトは、これらの種類とそれらを操作するために使用できるコマンドの詳細な概要を提供します。

このセクションでは、これらのデータ型やコマンドのいくつかの一般的な使用例をまとめたものです。

### アトミック実行とバッチ処理

Redis では、文字列の値を取得および設定しますアトミックオペレーションのシリーズをサポートしています。これらの操作は別を使用するときに発生する可能性があります可能なレースの危険性を削除します。 `GET` と `SET` コマンド。使用可能な操作が含まれます。

- `INCR`, `INCRBY`, `DECR`、と `DECRBY` インクリメントを実行し操作をデクリメント
  整数の数値データの値。StackExchange ライブラリのオーバー ロードされたバージョンを提供します、
  `IDatabase.StringIncrementAsync` と `IDatabase.StringDecrementAsync` 実行するメソッド
  これらの操作と、キャッシュに格納されている結果値を返します。次のコード
  スニペットは、これらのメソッドを使用する方法を示しています。

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET` キーに関連付けられている値を取得し、新しい値に変更します。、
  StackExchange ライブラリ、この操作を利用できるように、 `IDatabase.StringGetSetAsync`
  メソッドです。次のコード スニペットでは、このメソッドの例を示します。このコードを返します現在
  値「データ: カウンター」は、前の例のキーに関連付けられている、値をリセットします
  同じ操作の一部としてすべてのゼロに戻ってこのキー。

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` と `MSET`、返すまたは単一の操作として文字列値のセットを変更できます。、
  `IDatabase.StringGetAsync` と `IDatabase.StringSetAsync` メソッドはオーバー ロードをサポートするには
  この機能を使用すると、次の例に示すように:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

このガイドで Redis トランザクションとバッチのセクションの説明に従っても単一 Redis トランザクションに複数の操作を組み合わせることができます。StackExchange ライブラリは、取引をサポートします `ITransaction` インターフェイス。IDatabase.CreateTransaction メソッドを使用して ITransaction オブジェクトを作成でき、提供されるメソッドを使用して、トランザクションにコマンドを呼び出す `ITransaction` オブジェクト。、 `ITransaction` インターフェイスは、メソッドとしての同じようなセットへのアクセスを提供します、 `IDatabase` インターフェイスのすべてのメソッドが非同期です。彼らは、のみ実行、 `ITransaction.Execute` メソッドが呼び出されます。Execute メソッドによって返される値は、トランザクションが正常に作成されたかどうかを示す (true) または失敗 (false)。

次のコード スニペットは同じトランザクションの一部としてインクリメントおよびデクリメントします 2 つのカウンターの例を示します。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Redis トランザクションがリレーショナル データベースでのトランザクションとは異なりことを覚えてください。Execute メソッドは単に、実行するためのトランザクションを構成するすべてのコマンドをキューし、それらのいずれかが正しくない場合は、トランザクションは中止されます。すべてのコマンドは正常にキューに置かれたが、各コマンドは非同期的に実行されます。任意のコマンドが失敗した場合、他の人はまだ、処理を続行しますコマンドが正常に完了したことを確認する必要がある場合は、上記の例で示すように、対応するタスクの Result プロパティを使用してコマンドの結果をフェッチしなければなりません。プロパティは、タスクが完了するまでブロックされます結果を読み取る。

詳細についてを参照してください、 [Redis のトランザクション](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) StackExchange.Redis のウェブサイトのページ。

バッチ操作を実行すると、StackExchange ライブラリの IBatch インタ フェースを使用できます。このインターフェイスは、ことを除いてすべてのメソッドが非同期 IDatabase インターフェイスとして同じような一連のメソッドへのアクセスを提供します。IDatabase.CreateBatch メソッドを使用して、IBatch オブジェクトを作成し、次の例に示すように、IBatch.Execute メソッドを使用して、バッチを実行します。このコードは、単に文字列値とインクリメントおよびデクリメントします前の例で使用される同じカウンターを設定し、結果が表示されます。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

それは、トランザクションとは異なり不正な形式だから、バッチ内のコマンドが失敗した場合は他のコマンドがまだ実行を理解することが重要IBatch.Execute メソッドは、成功または失敗の兆候を返しません。

### 火災を忘れてキャッシュ操作を実行します。

Redis は、コマンドのフラグを使用して、火災を忘れて操作をサポートしています。このような状況でクライアントだけ操作を開始、結果に興味を持たないし、コマンドの完了を待機しません。次の例は、火災を忘れて操作として INCR コマンドを実行する方法を示しています。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### 自動的にキーの有効期限

Redis キャッシュに項目を保存すると、アイテムを後キャッシュから自動的に削除されますがタイムアウトを指定できます。どのように多くの時間、キーを使用して期限切れになる前に検索することも、 `TTL` コマンド;このコマンドは、IDatabase.KeyTimeToLive メソッドを使用して StackExchange アプリケーションで利用可能です。

次のコード スニペットは、キーに 20 秒の有効期限を設定し、キーの残りの有効期間を照会する例を示します。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

KeyExpireAsync メソッドと StackExchange ライブラリで利用できる有効期限コマンドを使用して特定の日付と時刻に有効期限を設定することもできます。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _ヒント:_ IDatabase.KeyDeleteAsync メソッドと StackExchange ライブラリを介して入手可能です・ デル ・ コマンドを使用してキャッシュからアイテムを手動で削除することができます。

### タグを使用して、キャッシュされたアイテムを相互に関連付け

Redis セットは、1 つのキーを共有する複数の項目のコレクションです。・ サッド コマンドを使用してセットを作成できます。SMEMBERS コマンドを使用して、セット内の項目を取得できます。StackExchange ライブラリは、IDatabase.SetMembersAsync メソッドと IDatabase.SetAddAsync メソッドを介してサッド コマンドと SMEMBERS コマンドを実装します。また SDIFF (差集合)、焼結 (積集合)、および SUNION (和集合) コマンドを使用して新しいセットを作成する既存のセットを組み合わせることができます。StackExchange ライブラリは、IDatabase.SetCombineAsync メソッドでこれらの操作を統一します。このメソッドの最初のパラメーターは、設定操作を実行するを指定します。

次のコード スニペットは、セットが迅速に格納および関連項目のコレクションを取得する便利なことができる方法を表示します。このコードは、セクション Redis キャッシュ クライアント アプリケーションの実装に記述された BlogPost タイプを使用します。BlogPost オブジェクトには、4 つのフィールドが含まれています-ID、タイトル、ランキング スコア、およびタグのコレクション。次の最初のコード スニペットは、BlogPost オブジェクトの c# 一覧の作成に使用するサンプル データを示しています。

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Redis キャッシュのセットとして BlogPost オブジェクトごとにタグを格納し、BlogPost の ID 各セットに関連付けることができます。これにより、迅速に特定のブログ記事に属するすべてのタグを検索するアプリケーション。逆方向で検索を有効にして、特定のタグを共有しているすべてのブログの記事を検索、ブログの記事を参照キーにタグ ID を保持している別のセットを作成します。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

これらの構造は、多くの一般的なクエリを非常に効率的に実行することができます。たとえば、検索およびすべてのこのようなブログの記事 1 のタグを表示できます。

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

積集合演算を次のように実行することによって 1、ブログ記事 2 を投稿ブログに共通するすべてのタグを検索できます。

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

特定のタグが含まれているすべてのブログの記事を見つけることができます。

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### 最近アクセスしたアイテム

A common problem required by many applications is to find the most recently accessed items. For example, a blogging site might want to display information about the most recently read blog posts. You can implement this functionality by using a Redis list. A Redis list contains multiple items that share the same key, but the list acts as a double-ended queue. You can push items on to either end of the list by using the LPUSH (left push) and RPUSH (right push) commands. You can retrieve items from either end of the list by using the LPOP and RPOP commands.  You can also return a set of elements by using the LRANGE and RRANGE commands. The code snippets below show how you can perform these operations by using the StackExchange library. This code uses the BlogPost type from the previous examples. As a blog post is read by a user, the title of the blog post is pushed onto a list associated with the key "blog:recent_IDatabase.ListLeftPushAsync 法を用いた Redis キャッシュ"を投稿します。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

多くのブログの記事を読んで、そのタイトルが同じリストにプッシュされます。リストの順序は、追加されていない; 順最も最近のブログ投稿は、リストの左の端の方 (1 回、それ以上は一覧に複数のエントリを持っている同じブログの記事を読む) を読みます。IDatabase.ListRange メソッドを使用して、最も最近読まれた記事のタイトルを表示できます。このメソッドは、リスト、開始点と終了点を含むキーを受け取ります。次のコードは、リストの一番左の端に 10 のブログ記事 (項目 0 から 9 に) のタイトルを取得します。

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

ListRangeAsync はリストから項目を削除することに注意してください。これを行うには、IDatabase.ListLeftPopAsync および IDatabase.ListRightPopAsync メソッドを使用できます。

一覧が無限に増大するを防ぐため一覧をトリミングすることによってアイテムを定期的に処分することができます。、下記のコード スニペットは、すべてを削除しますが、5 の一番左のアイテム一覧から。

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### リーダー ボードの実装

既定では、セット内のアイテムは任意の特定の順序で保持されません。ZADD コマンド (StackExchange ライブラリ内の IDatabase.SortedSetAdd メソッド) を使用して、順序付けされたセットを作成できます。コマンドへのパラメーターとして提供されるスコアと呼ばれる数値を使用してのオーダーです。次のコード スニペットは、順序付きリストにブログ記事のタイトルを追加します。例では、各ブログの記事は、ブログの記事のランキングを含むスコア フィールドに記入しています。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

ブログ記事のタイトルと IDatabase.SortedSetRangeByRankWithScores メソッドを使用してスコアを昇順でスコアを取得できます。

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE。メモ] StackExchange ライブラリには、スコア順にデータを返しますが、スコアを返さない IDatabase.SortedSetRangeByRankAsync メソッドも提供します。

またスコアの降順でアイテムを取得し、IDatabase.SortedSetRangeByRankWithScoresAsync メソッドに追加のパラメーターを提供することによって返されるアイテムの数を制限できます。次の例は、タイトルとスコアのトップ 10 ランク付けされたブログの記事を表示します。

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

次の例は、与えられたスコア内に返される項目数を制限するために使用できる IDatabase.SortedSetRangeByScoreWithScoresAsync メソッドを使用して範囲。

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### チャネルを使用するメッセージング

データ キャッシュとして機能する高性能パブリッシャー/サブスクライバー メカニズムを介したメッセージング Redis サーバーを提供します。クライアント アプリケーションが、チャネルをサブスクライブすることができ、他のアプリケーションまたはサービスがチャネルにメッセージをパブリッシュできます。サブスクライブしているアプリケーションは、これらのメッセージを受信し、それらを処理できます。

Redis のチャネルにサブスクライブ SUBSCRIBE コマンドを提供します。このコマンドは、1 つ以上のチャネルをアプリケーションで受け入れるメッセージの名を見込んでいます。StackExchange ライブラリには、購読してチャネルにパブリッシュする .NET Framework アプリケーションを可能にする ISubscription インターフェイスが含まれています。Redis サーバーへの接続の GetSubscriber メソッドを使用して、ISubscription オブジェクトを作成し、このオブジェクトの SubscribeAsync メソッドを使用してチャネル メッセージをリッスンし。次のコード例は、「メッセージ: ブログ記事」という名前のチャネルにサブスクライブする方法を示しています。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Subscribe メソッドの最初のパラメーターは、チャネルの名前です。この名前は、キャッシュにあるキーで使用されるのと同じ規則に従います、良好なパフォーマンスと保守性を確保するために比較的短い、意味のある文字列を使用することをお勧めは、任意のバイナリ データを含めることができます。また、チャネルによって使用される名前空間は別に維持するために難しく、アプリケーション コードになるが、チャネルと同じ名前を持つキーを持つことができますので、キー、によって使用されるあることを注意してください。

2 番目のパラメーターは、アクション デリゲートです。このデリゲートは、チャネルに新しいメッセージが表示されるたびに非同期的に実行されます。この例は単に (メッセージのブログ投稿のタイトルが含まれます) コンソールにメッセージを表示します。

チャネルにパブリッシュするには、アプリケーションは Redis の発行コマンドを使用できます。StackExchange ライブラリは、この操作を実行するには、IServer.PublishAsync メソッドを提供します。次のコード スニペットは、「メッセージ: ブログ記事"チャネル メッセージをパブリッシュする方法を示しています。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

パブリッシュ/サブスクライブ メカニズムについて理解する必要がありますいくつかのポイントがあります。

- 複数のサブスクライバーが同じチャネルに購読することができます、彼らはすべてのチャネルにパブリッシュされたメッセージを受け取ります。
- 加入者は、彼らを購読するが後に公開されているメッセージのみを受信します。チャンネルは、バッファリングされていないと Redis インフラストラクチャ メッセージを各サブスクライバーにプッシュし、削除するメッセージを発行します。
- 既定では、メッセージは送信される順序でサブスクライバーによって受信されます。数が多いと非常にアクティブなシステムで
  メッセージ、多くのサブスクライバーとパブリッシャーの保証されたシーケンシャル メッセージ配信システムのパフォーマンスが低下することができます。場合
  各メッセージは独立しており、順序が材料 Redis システムを助けることができる並行処理を有効にできます
  応答性を向上させます。StackExchange クライアントによって使用される接続の PreserveAsyncOrder を設定することによってこれを実現できます。
  false にサブスクライバー:
  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## 関連するパターンと指導

次のパターンは、アプリケーションでキャッシュを実装する際のシナリオに関連あります。

- [キャッシュ アサイド パターン](http://msdn.microsoft.com/library/dn589799.aspx): このパターンでは、データ ストアからキャッシュに読み込んで、データを要求する方法について説明します。このパターンは、キャッシュに保持されているデータと元のデータ ストア内のデータ間の一貫性を維持するためにも役立ちます。
- 、 [分割パターン](http://msdn.microsoft.com/library/dn589797.aspx) 格納して、大量のデータにアクセスするときのスケーラビリティを向上させる水平パーティション分割の実装について説明します。

## 詳細については

- 、 [MemoryCache クラス](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) Microsoft の web サイト上のページ。
- 、 [Microsoft Azure キャッシュ](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) Microsoft の web サイト上のページ。
- 、 [Azure キャッシュ サービスは私にとって正しいですか。](http://msdn.microsoft.com/library/azure/dn766201.aspx) Microsoft の web サイト上のページ。
- 、 [構成モデル](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) Microsoft の web サイト上のページ。
- 、 [タスク ベースの非同期パターン](http://msdn.microsoft.com/library/hh873175.aspx) Microsoft の web サイト上のページ。
- 、 [パイプラインおよびマルチプレクサー](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) StackExchange.Redis GitHub レポのページ。
- 、 [REDIS の永続性](http://redis.io/topics/persistence) Redis ウェブサイトのページ。
- 、 [レプリケーション ページ](http://redis.io/topics/replication) Redis のウェブサイト。
- 、 [Redis クラスター チュートリアル](http://redis.io/topics/cluster-tutorial) Redis ウェブサイトのページ。
- 、 [Redis の複数のインスタンス間でデータを分割する方法の分割。](http://redis.io/topics/partitioning) Redis ウェブサイトのページ。
- ページ [LRU キャッシュとして Redis を使用してください。](http://redis.io/topics/lru-cache) Redis のウェブサイト。
- 、 [取引ページ](http://redis.io/topics/transactions) Redis のウェブサイト。
- 、 [Redis セキュリティ](http://redis.io/topics/security) Redis ウェブサイトのページ。
- ページ [紺碧 Redis キャッシュの周りにラップ](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) 紺碧のブログ。
- ページ [Redis CentOS Linux VM 上で実行](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) Microsoft の web サイトに azure。
- 、 [Azure の ASP.NET のセッション状態プロバイダーは、キャッシュを Redis します。](http://msdn.microsoft.com/library/azure/dn690522.aspx) Microsoft の web サイト上のページ。
- 、 [Azure の ASP.NET 出力キャッシュ プロバイダーは、キャッシュを Redis します。](http://msdn.microsoft.com/library/azure/dn798898.aspx) Microsoft の web サイト上のページ。
- ページ [紺碧 Redis キャッシュのための開発します。](http://msdn.microsoft.com/library/azure/dn690520.aspx) 紺碧のサイトです。
- ページ [Redis データ型および抽象化の導入](http://redis.io/topics/data-types-intro) Redis のウェブサイト。
- 、 [基本的な使用法](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) StackExchange.Redis のウェブサイトのページ。
- 、 [Redis のトランザクション](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) StackExchange.Redis レポのページ。
- 、 [データ パーティション分割ガイド](http://msdn.microsoft.com/library/dn589795.aspx) Microsoft の web サイト。
