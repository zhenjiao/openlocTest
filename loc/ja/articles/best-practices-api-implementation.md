<properties
   pageTitle="API implementation guidance | Microsoft Azure"
   description="Guidance upon how to implement an API."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2015"
   ms.author="masashin"/>

# API 実装ガイダンス

![](media/best-practices-api-implementation/pnp-logo.png)


このガイドのいくつかのトピックが議論の下で、将来変更可能性があります。私たちはあなたのフィードバックを歓迎します。!

## 概要
慎重に設計の RESTful web API は、リソース、リレーションシップ、およびクライアント アプリケーションにアクセスできるナビゲーション スキームを定義します。Web API と方法をホスティング環境の物理的な条件を考慮する必要があります実装する web API を配置するで web API ではなく、データの論理構造を構築します。このガイドは web API を実装して、クライアント アプリケーションから利用できるように公開のベスト プラクティスに焦点を当てください。セキュリティ上の問題は、API セキュリティ ガイダンス文書で個別に説明します。API 設計ガイダンス文書の web API の設計に関する詳細情報を検索できます。

## RESTful web API を実装するための考慮事項
次のセクションでは、ASP.NET Web API テンプレートを使って RESTful web API を構築するためのベスト プラクティスについて説明します。Web API テンプレートの使用に関する詳細についてを参照してください、 [ASP.NET Web API について学ぶ](http://www.asp.net/web-api) Microsoft の web サイト上のページ。

## 要求のルーティングを実装するための考慮事項

ASP.NET Web API を使用して実装されたサービスでは、各要求のメソッドにルーティングは、 _コント ローラー_ クラスです。Web API のフレームワークを提供します。 ルーティングを実装するための 2 つの主なオプション _規則に基づく_ ルーティングと _属性ベース_ ルーティング。Web API に要求をルーティングする最善の方法を決定するときは、次の点を考慮してください。

- **制限および規則ベースのルーティングの要件を理解します。**.

	既定では、Web API のフレームワークは、規則ベースのルーティングを使用します。Web API のフレームワークでは、次のエントリが含まれている最初のルーティング テーブルを作成します。

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	ルートがジェネリック、リテラルで構成されるようになります。 _api_ などの変数 _{コント ローラー}_ と _{id}_.規則ベースのルーティング オプションへのルートのいくつかの要素をことができます。Web API のフレームワークは、api では、メソッド名の最初の部分への要求に HTTP メソッドを照合することによって、任意の省略可能なパラメーターを一致させることによって、コント ローラーで呼び出すメソッドを決定します。たとえば、という名前のコント ローラー _注文_ メソッドが含まれています _GetAllOrders()_ または _GetOrderByInt (int id)_ GET 要求では、 _http://www.adventure-works.com/api/orders/_ メソッドに指示されます。 _GetAlllOrders()_ GET 要求 _http://www.adventure-works.com/api/orders/99_ メソッドにルーティングされます。 _GetOrderByInt (int id)_.コント ローラーで Get プレフィックスで始まる方法が一致しない場合は、Web API フレームワークは HTTP 405 (メソッドは許可されていません) メッセージで応答します。さらに、ルーティング テーブルで指定されたパラメーター (id) の名前は、パラメーターの名前と同じをする必要があります、 _GetOrderById_ 法、それ以外の場合 Web API フレームワークは、HTTP 404 (見つかりません) 応答で返信されます。

	同じ規則は、ポスト、PUT、および削除の HTTP 要求に適用されます。注文 101 の詳細を更新する PUT 要求 URI に指示されます。 _http://www.adventure-works.com/api/orders/101_、メッセージの本文、順序の新しい詳細が含まれます、、この情報は、プレフィックスで始まる名前を持つ注文コント ローラーのメソッドにパラメーターとして渡されます _置く_、など _PutOrder_.

	既定のルーティング テーブルはよう RESTful web API の子リソースを参照する要求を一致しません _http://www.adventure-works.com/api/customers/1/orders_ (お客様 1 がすべての注文の詳細を見つける)。これらのケースを処理するためカスタム ルートをルーティング テーブルに追加できます。

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	このルートを指示するための URI に一致する要求、 _GetOrdersForCustomer_ 方法、 _利用のお客様_ コント ローラー。このメソッドは、という 1 つのパラメーターを取る必要があります。 _犬小屋_:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    public IEnumerable<Order> GetOrdersForCustomer(int custId)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	> [AZURE。ヒント] 可能な限り、既定のルーティングを活用し、多くの複雑なカスタムようにこれにより脆性 (それはあいまいなルートは、コント ローラーにメソッドを追加する非常に簡単です)、送ります、(ルーティング テーブルが大きく、Web API フレームワークはどのルートが指定された URI に一致するを動作するようにより多くの仕事) のパフォーマンスが低下を定義しないでください。API およびルートを簡単に保ちなさい。詳細については、「API 設計ガイダンスのリソースの Web API 周りの整理」を参照してください。カスタム ルートを定義する必要があります、好ましいアプローチはこのセクションで後述属性ベースのルーティングを使用することです。

	規則ベースのルーティングの詳細については、ページを参照してください。 [ASP.NET Web API でのルーティング](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) Microsoft の web サイト。

- **ルーティングのあいまいさを避ける**.

	規則ベースのルーティングは、コント ローラーで複数のメソッドが同じルートを一致させる場合にあいまいな経路で起因できます。これらの状況で、Web API フレームワークは「複数のアクションは、要求に一致するが発見された」テキストを含む HTTP 500 (内部サーバー エラー) 応答メッセージで応答します。

- **属性ベースのルーティングを好む**.

	属性ベースのルーティングは、コント ローラーのメソッドにルートを接続するための代替手段を提供します。ルーティング規則ベースのパターン マッチング機能に依存するのではなく明示的にする彼らは関連する必要がありますルートの詳細をコント ローラーのメソッドを付けることができます。このアプローチは可能なあいまいさを削除するヘルプ。さらに、明示的なルートはデザイン時に定義されている、このアプローチは実行時にルーティング規則に基づくよりも効率的です。次のコードを適用する方法を示しています、 _ルート_ お客様のコント ローラーのメソッドを属性します。これらのメソッドもに応答する必要がありますを示す HttpGet 属性を使用します。 _HTTP の GET_ 要求します。この属性では、規則ベースのルーティングによって期待されるよりもむしろ任意の便利な名前付けスキームを使用して、メソッドに名前を付けることができます。持つメソッドを付けることも、 _HttpPost_, _HttpPut_、と _HttpDelete_ 他の種類の HTTP 要求に応答する方法を定義する属性です。

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	    [Route("api/customers/{id}/orders")]
	    [HttpGet]
	    public IEnumerable<Order> FindOrdersForCustomer(int id)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	将来コードを保守する必要のある開発者のためのマニュアルとして有用な副作用は、属性ベースのルーティングもどのメソッドがどのルートに属するすぐに明らかだと _HttpGet_ 属性は、メソッドが応答する HTTP 要求の種類を明らかにします。

	属性ベースのルーティング パラメーターの照合方法を制限する制約を定義することができます。制約は、パラメーターの型を指定でき、いくつかのケースで彼らはまたパラメーター値の許容範囲を示すことができます。次の例では、id パラメーターで、 _FindCustomerByID_ メソッドは、非負の整数にする必要があります。アプリケーションでは、負の顧客数を持つ HTTP GET 要求を送信する場合、Web API フレームワークから HTTP 405 (メソッドは許可されていません) メッセージが応答として返されます。

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id:int:min(0)}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	}
	```

	属性ベースのルーティングの詳細については、ページを参照してください。 [Web API 2 のルーティング属性](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) Microsoft の web サイト。

- **ルートの Unicode 文字をサポートします。**.

	GET 要求でリソースを識別するために使用されるキーは、文字列可能性があります。したがって、グローバルなアプリケーションは、英語以外の文字を含む Uri をサポートする必要があります。

- **ルーティングされないメソッドを区別します。**.

	規則ベースのルーティングを使用する場合とそれらを飾ることによって HTTP アクションに対応していないメソッドを示す、 _NonAction_ 属性。これは通常、コント ローラー内の他のメソッドによってヘルパー メソッド定義に適用されます、この属性を防ぎますこれらのメソッドから一致し、誤った HTTP 要求によって呼び出されます。

- **利点とトレードオフのサブドメインで API を配置することを検討してください。**.

	既定では、ASP.NET web API に Api を整理、 _/api_ ドメインで、ディレクトリなど _http://www.adventure-works.com/api/orders_.このディレクトリは、同じホストによって公開される他のサービスと同じドメインに存在します。Web API をように Uri を別のホストで実行している独自のサブドメインに分割する有益なことがあります。 _http://api.adventure-works.com/orders_.この分離パーティションを作成し、他の web アプリケーションやサービス実行に影響を与えず、web API をより効果的にスケーリングすることができます、 _www.adventure-works.com_ ドメイン。

	ただし、セキュリティ上の懸念に異なるサブドメインの web API を配置することができる可能性も。任意のアプリケーションまたはサービスでホストされている web します。 _www.adventure-works.com_ それは多くの web ブラウザーの同一生成元ポリシーに違反する可能性があります他の場所を実行している web API を呼び出します。このような状況では、クロス オリジン リソース共有 (CORS) のホスト間を有効にする必要があります。詳細については、API セキュリティ ガイダンスのドキュメントを参照してください。

## 要求を処理するための考慮事項

クライアント アプリケーションからの要求が正常に web API のメソッドにルーティングされている、一度、できるだけ効率的な方法として要求で処理する必要があります。要求を処理するコードを実装する場合は、次の点を考慮してください。

- **取得、配置、削除、ヘッド、およびパッチの操作はアイデムポ テントなをする必要があります**.

	これらの要求を実装するコードに任意の副作用を課すことはできません。同じリソースを繰り返し同じ要求は、同じ状態になります。たとえば、同一の URI を複数の削除要求を送信する必要ですが同じ効果、応答メッセージの HTTP ステータス コードが異なる場合があります (最初の削除要求かもしれないステータスコードを返さなければ 204 (いいえコンテンツ) 以降の削除要求は、通計コード 404 (Not Found) を返すことがありますしながら)。

> [AZURE。メモ] 記事 [等羃性パターン](http://blog.jonathanoliver.com/idempotency-patterns/) Jonathan Oliver のブログ等羃性とデータ管理操作との関係の概要を示します。

- **新しいリソースを作成後操作すれば、無関係な副作用なし**.

	POST 要求は、新しいリソースを作成するものです、要求の効果は、新しいリソースに限定する必要があります (および多分直接関連するリソース リンケージのいくつかの並べ替えの場合) 場合たとえば、e コマース システムで顧客の新しい注文を作成する POST リクエスト可能性がありますも在庫レベルを修正、請求情報を生成、がない順序に直接関連しない情報を変更または副作用を持っている、他のシステム全体の状態にする必要があります。

- **おしゃべり投稿、PUT、および削除の操作を実装することを避けるため**.

	サポート記事、リソース コレクションを置くと削除要求。POST 要求して複数の新しいリソースの詳細情報が含まれて、同じコレクションにすべて追加できます、PUT 要求は、コレクション内のリソースのセット全体を置き換えることができます、削除要求は、コレクション全体を削除できます。

	ASP.NET Web API 2 に含まれる OData サポートがバッチ要求する機能を提供することに注意してください。クライアント アプリケーションは、いくつかの web API 要求をパッケージ化し単一の HTTP 要求でサーバーに送信し、各依頼への返信が含まれている 1 つの HTTP 応答を受信できます。詳細については、ページを参照してください。 [Web API と Web API OData バッチ サポートを導入](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) Microsoft の web サイト。

- **クライアント アプリケーションに応答を送信するときに HTTP プロトコルに従うこと**.

	Web API では、クライアントは、結果とその結果を解析するクライアントを有効にするのに適切に書式設定された体の性質を理解して、結果、適切な HTTP ヘッダーを処理する方法を決定するクライアントを有効に正しい HTTP ステータス コードを含むメッセージを返す必要があります。ASP.NET Web API テンプレートを使用する場合は、次の例に示すように HTTP POST 要求に応答するメソッドの実装のデフォルトの方法は新しく作成されたリソースのコピーを返す単に。

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public Customer CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        // This method returns a customer with a unique ID allocated
	        // by the repository
	        var newCust = repository.Add(customerDetails);
	        // Return the newly added customer
	        return newCust;
	    }
	    ...
	}
	```

	投稿操作が成功した場合、Web API フレームワークはメッセージ本体として HTTP 応答ステータス コード 200 (OK) と顧客の詳細を作成します。ただし、この場合、HTTP プロトコルによると POST 操作コードを返しますステータス 201 (Created) 応答メッセージは、応答メッセージのヘッダーに新しく作成されたリソースの URI を含める必要があります。

	これらの機能を提供するためを使用して HTTP 応答メッセージを返す、 `IHttpActionResult` インターフェイス。この方法次のコード例に示すように、応答メッセージの本文に HTTP のステータス コード、応答メッセージ、およびデータの形式ものヘッダーを細かく制御できます。このバージョンの `CreateNewCustomer` メソッドは、次の HTTP プロトコル クライアントの期待にもっと密接に準拠しています。、 `Created` 法、 `ApiController` クラスは指定されたデータからの応答メッセージを構築し、結果に Location ヘッダーを追加します。

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public IHttpActionResult CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        var newCust = repository.Add(customerDetails);

	        // Create a value for the Location header to be returned in the response
	        // The URI should be the location of the customer including its ID,
	        // such as http://adventure-works.com/api/customers/99
	        var location = new Uri(...);

            // Return the HTTP 201 response,
            // including the location and the newly added customer
	        return Created(location, newCust);
	    }
	    ...
	}
	```

- **コンテンツ ネゴシエーションをサポートします。**.

	応答メッセージの本文は、さまざまな形式のデータを含めることができます。HTTP GET 要求は、JSON のデータを返すことができますたとえば、または XML 形式。クライアントは、要求を送信する、それが扱うことができるデータ形式を指定します Accept ヘッダーを含めることができます。これらの形式は、メディアの種類として指定されます。たとえば、イメージを取得する GET 要求を発行するクライアントは、「画像/jpeg、gif/イメージ、イメージ/png」など、クライアントが処理できるメディア タイプを一覧表示する Accept ヘッダーを指定できます。 Web API に結果が返されるときこれらのメディアの種類のいずれかを使用して、データの書式を設定して応答のコンテンツ タイプのヘッダーの形式を指定します。

	クライアントが Accept ヘッダーを指定しない場合は、応答本体の賢明な既定の形式を使用します。例として ASP.NET Web API フレームワークのデータをテキスト ベースの JSON に既定値します。

	> [AZURE。メモ] ASP.NET Web API フレームワークは、Accept ヘッダーのいくつかの自動検出を実行し、自体は応答メッセージの本文内のデータの型に基づいてそれらを処理します。たとえば、応答メッセージの本文には、CLR (共通言語ランタイム) オブジェクトが含まれている場合、ASP.NET Web API 自動的にフォーマット応答 JSON として"アプリケーション/json"に設定されて場合 ASP.NET Web API フレームワークが応答を XML として書式を設定し、"テキストと xml"への応答の Content-type ヘッダーを設定、XML として結果を必要があることをクライアントが通知しない限り、応答のコンテンツ タイプ ヘッダーを持つ。ただし、操作の実装コードでさまざまなメディア タイプを明示的に指定する Accept ヘッダーを処理する必要があります。

- **HATEOAS スタイルのナビゲーションとリソースの検出をサポートするリンクを提供します。**.

	The API Design Guidance describes how following the HATEOAS approach enables a client to navigate and discover resources from an initial starting point. This is achieved by using links containing URIs; when a client issues an HTTP GET request to obtain a resource, the response should contain URIs that enable a client application to quickly locate any directly related resources. For example, in a web API that supports an e-commerce solution, a customer may have placed many orders. When a client application retrieves the details for a customer, the response should include links that enable the client application to send HTTP GET requests that can retrieve these orders. Additionally, HATEOAS-style links should describe the other operations (POST, PUT, DELETE, and so on) that each linked resource supports together with the corresponding URI to perform each request. This approach is described in more detail in the API Design Guidance document.

	現在、HATEOAS の実装を決定する基準はありませんが、次の例では、1 つの可能なアプローチです。この例では、顧客の詳細を検索する HTTP GET 要求は、その顧客の注文を参照する HATEOAS リンクを含む応答を返します。

	```HTTP
	GET http://adventure-works.com/customers/2 HTTP/1.1
	Accept: text/json
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Content-Type: application/json; charset=utf-8
	...
	Content-Length: ...
	{"CustomerID":2,"CustomerName":"Bert","Links":[
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"GET",
	   "LinkedResourceMIMETypes":["text/xml","application/json"]},
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"PUT",
	   "LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},
	  {"Relationship":"self",
	   "HRef":"http://adventure-works.com/customers/2",
	   "Action":"DELETE",
	   "LinkedResourceMIMETypes":[]},
	  {"Relationship":"orders",
	   "HRef":"http://adventure-works.com/customers/2/orders",
	   "Action":"GET",
	   "LinkedResourceMIMETypes":["text/xml","application/json"]},
	  {"Relationship":"orders",
	   "HRef":"http://adventure-works.com/customers/2/orders",
	   "Action":"POST",
	   "LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]}
	]}
	```

	この例では、顧客データが表されます、 `Customer` 次のコード スニペットに示すようにクラスです。HATEOAS リンクで開催されます、 `Links` コレクションのプロパティ:

	```C#
	public class Customer
	{
    	public int CustomerID { get; set; }
    	public string CustomerName { get; set; }
    	public List<Link> Links { get; set; }
    	...
	}

	public class Link
	{
    	public string Relationship { get; set; }
    	public string HRef { get; set; }
    	public string Action { get; set; }
    	public string [] LinkedResourceMIMETypes { get; set; }
	}
	```

	HTTP GET 操作はストレージおよび構造から顧客データを取得します。 `Customer` オブジェクトを作成し、 `Links` コレクションです。結果は、JSON 応答メッセージとしてフォーマットされています。各リンクには、次のフィールドが含まれます。

	- 返されるオブジェクトとのリンクが記述するオブジェクトの関係。ここでは「自己」リンクがオブジェクト自体への参照であることを示します (と同様、 `this` 多くのオブジェクト指向言語でのポインター)、「オーダー」は関連する注文情報を含むコレクションの名前。

	- ハイパーリンク (`HRef`) URI の形式内のリンクによって記述されているオブジェクト。

	- HTTP 要求 (の種類`Action`) この URI に送信できます。

	- 任意のデータ (の形式`LinkedResourceMIMETypes`) HTTP 要求またはを返すことができる要求の種類に応じて、応答で提供される必要があります。

	HTTP 応答の例に示すように HATEOAS リンクを示すクライアント アプリケーションが次の操作を実行できます。

	- URI に HTTP GET 要求 _http://adventure-works.com/customers/2_ (再び) 顧客の詳細を取得。データは、XML や JSON として返すことができます。

	- URI を HTTP PUT 要求 _http://adventure-works.com/customers/2_ 顧客の詳細を変更します。新しいデータは、x-ロード-したいだけの形式で要求メッセージで提供されなければなりません。

	- URI に HTTP DELETE 要求 _http://adventure-works.com/customers/2_ 顧客を削除します。要求はない任意の追加情報を期待または応答メッセージの本体のデータを返します。

	- URI に HTTP GET 要求 _http://adventure-works.com/customers/2/orders_ 顧客のすべての注文を見つけよう。データは、XML や JSON として返すことができます。

	- URI を HTTP PUT 要求 _http://adventure-works.com/customers/2/orders_ この顧客に対して新しい注文を作成します。データは、x-ロード-したいだけの形式で要求メッセージで提供する必要があります。

## 例外を処理するための考慮事項
既定では、ASP.NET Web API で操作フレームワークがキャッチされない例外をスローする場合、フレームワークは HTTP ステータス コード 500 (内部サーバー エラー) で応答メッセージを返します。多くの場合、この単純なアプローチの分離に有用ではないと困難な例外の原因を特定するになります。したがって、次の点を考慮した例外を処理するためのより包括的なアプローチを採用すべき。

- **例外をキャプチャし、クライアントに意味のある応答を返す**.

	The code that implements an HTTP operation should provide comprehensive exception handling rather than letting uncaught exceptions propagate to the Web API framework. If an exception makes it impossible to complete the operation successfully, the exception can be passed back in the response message, but it should include a meaningful description of the error that caused the exception. The exception should also include the appropriate HTTP status code rather than simply returning status code 500 for every situation. For example, if a user request causes a database update that violates a constraint (such as attempting to delete a customer that has outstanding orders), you should return status code 409 (Conflict) and a message body indicating the reason for the conflict. If some other condition renders the request unachievable, you can return status code 400 (Bad Request). You can find a full list of HTTP status codes on the [ステータス コードの定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) W3C サイト上にページ。

	次のコードは、さまざまな条件をトラップして適切な応答を返す例を示します。

	```C#
	[HttpDelete]
	[Route("customers/{id:int}")]
	public IHttpActionResult DeleteCustomer(int id)
	{
		try
		{
			// Find the customer to be deleted in the repository
			var customerToDelete = repository.GetCustomer(id);

			// If there is no such customer, return an error response
			// with status code 404 (Not Found)
			if (customerToDelete == null)
			{
					return NotFound();
			}

			// Remove the customer from the repository
			// The DeleteCustomer method returns true if the customer
			// was successfully deleted
			if (repository.DeleteCustomer(id))
			{
				// Return a response message with status code 204 (No Content)
				// To indicate that the operation was successful
				return StatusCode(HttpStatusCode.NoContent);
			}
			else
			{
				// Otherwise return a 400 (Bad Request) error response
				return BadRequest(Strings.CustomerNotDeleted);
			}
		}
		catch
		{
			// If an uncaught exception occurs, return an error response
			// with status code 500 (Internal Server Error)
			return InternalServerError();
		}
	}
	```

	> [AZURE。ヒント] Web API に侵入しようとする攻撃者にとって役に立つかもしれない情報は含めないでください。詳細についてを参照してください、 [ASP.NET Web API の例外処理](http://www.asp.net/web-api/overview/error-handling/exception-handling) Microsoft の web サイト上のページ。

	> [AZURE。メモ] 多くの web サーバーは、web API を到達する前に自身のエラー条件をトラップします。たとえば、web サイトの認証を構成して、ユーザーが適切な認証情報を提供できない場合、web サーバーは、ステータス コード 401 (Unauthorized) で応じるべきです。クライアントが認証されると、あなたのコードは、クライアントが要求されたリソースをアクセスできることを確認するチェックを実行できます。この認証が失敗した場合、ステータス コード 403 (Forbidden) を返す必要があります。

- **エラーの一貫した方法とログについての例外を処理します。**.

	一貫性のある方法で例外を処理するには、グローバルなエラー API ウェブ全体にわたって戦略を処理を実装することを検討してください。この部分を達成するコント ローラーが未処理の例外をスローするときに実行する例外フィルターを作成することによって、 `HttpResponseException` 例外。このアプローチで記述されている、 [ASP.NET Web API の例外処理](http://www.asp.net/web-api/overview/error-handling/exception-handling) Microsoft の web サイト上のページ。

	ただし、例外フィルターが例外をキャッチしないいくつかの状況があるを含みます。

	- コント ローラーのコンス トラクターからスローされた例外。

	- メッセージ ハンドラーからスローされた例外。

	- ルーティング中にスローされた例外。

	- 応答メッセージの内容をシリアル化するときにスローされる例外。

	これらのケースを処理するためよりカスタマイズされたアプローチを実装する必要があります。また各例外の完全な詳細をキャプチャするログ記録エラーを組み込む必要があります。このエラー ログは、限り、それはないアクセス web 上のクライアントに詳細な情報を含めることができます。記事 [Web API グローバル エラー処理](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) マイクロソフトのウェブサイトは、このタスクを実行する方法の 1 つを示しています。

- **クライアント側のエラーとサーバー側のエラーを区別します。**.

	HTTP プロトコル (HTTP 4 xx ステータス コード)、クライアント アプリケーションが原因で発生するエラーとサーバー (HTTP 5 xx ステータス コード) に事故によって引き起こされるエラーを区別します。任意のエラー応答のメッセージで、この条約を尊重することを確認します。

<a name="considerations-for-optimizing"></a>
## クライアント側データ アクセスを最適化するための考慮事項

Web サーバーやクライアント アプリケーションなどの分散環境での懸念の主な源の 1 つは、ネットワークです。これは、オブジェクトは、特に場合は、クライアント アプリケーションが頻繁要求を送信またはデータを受信にかなりボトルネックとして使用できます。そのためネットワーク経由のトラフィックの量を最小限に抑えることを目指してください。取得し、データを保持するコードを実装する場合は、次の点を考慮してください。

- **クライアント側のキャッシュをサポートします。**.

	HTTP 1.1 プロトコルは、キャッシュ コントロール ヘッダーを使用して、要求がルーティングされるクライアントと中間サーバにキャッシュをサポートします。応答はずっと前にどのように、クライアントまたは、要求がルーティングされている、中間サーバーによって応答の本体にデータできます安全にキャッシュかどうかを示すキャッシュ コントロール ヘッダーを含めることができます、web API に HTTP GET 要求を送信するクライアント アプリケーションは、有効期限が切れたし、古くなったと見なされます。次の例は、HTTP GET 要求とキャッシュ制御ヘッダーを含む対応する応答を示します。

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	この例ではキャッシュ制御ヘッダー 600 秒後期限切れする必要があります。 データが返されることを指定しますと、1 つのクライアントにのみ適してとない (それは他のクライアントによって使用される共有キャッシュに格納される必要があります。 _プライベート_).キャッシュ コントロール ヘッダーを指定することができます。 _公共_ ではなく _プライベート_ その場合共有キャッシュにデータを格納できるかを指定する _いいえストア_ その場合データにする必要があります。 **ない** クライアントによってキャッシュされます。次のコード例は、応答メッセージのキャッシュ コントロール ヘッダーを構築する方法を示しています。

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...
    		// Create a Cache-Control header for the response
    		var cacheControlHeader = new CacheControlHeaderValue();
    		cacheControlHeader.Private = true;
    		cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    		...

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			CacheControlHeader = cacheControlHeader
    		};
    		return response;
    	}
    	...
	}
	```

	このコードでは、カスタム `IHttpActionResult` という名前のクラス `OkResultWithCaching`.このクラスは、キャッシュ ヘッダーの内容を設定するコント ローラーを使用できます。

	```C#
	public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }

        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }

        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }

        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);

            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;

            return response;
        }
    }
	```

	> [AZURE。メモ] HTTP プロトコルをまた定義します、 _キャッシュなし_ キャッシュ コントロール ヘッダーのディレクティブ。むしろ紛らわしいこのディレクティブわけで「キャッシュしない」ではなく「再検証返す前にキャッシュされた情報をサーバー」; しかし、データをキャッシュまだことができますが、毎回チェックはまだ現在であることを確認に使用されます。

	キャッシュ管理はクライアント アプリケーションまたは中間サーバーの責任かどうか適切に実装することができます帯域幅を節約し、既に最近取得済みデータを取得する必要性を除去することによってパフォーマンスを向上させます。

	、 _マックス-時代_ キャッシュ コントロール ヘッダーの値はのみのガイドと対応するデータを指定した時間中に変更しないことを保証します。Web API は、データの予想される変動に応じて適切な値マックス-時代を設定必要があります。この期間が経過すると、クライアントはキャッシュからオブジェクトを破棄する必要があります。

	> [AZURE。メモ] 最新の web ブラウザーでは、前述の適切なキャッシュ コントロール ヘッダーを要求に追加して、結果のヘッダーを調べるでクライアント側のキャッシュをサポートします。ただし、一部の古いブラウザーでは、クエリ文字列を含む URL から返される値はキャッシュが。これは通常ここで説明したプロトコルに基づいて、独自のキャッシュ管理戦略を実装するカスタム クライアント アプリケーションのための問題ではありません。
	>
	> いくつかの古いプロキシは同じ現象が発生してクエリ文字列を Url に基づいた要求をキャッシュ可能性があります。これは、このようなプロキシを通じて web サーバーに接続するカスタム クライアント アプリケーションのための問題かもしれない。

- **クエリ処理を最適化する Etag を提供します。**.

	応答メッセージを含めることも、クライアント アプリケーションがオブジェクトを取得するとき、 _ETag_ (エンティティタグ)。ETag は、リソースのバージョンを示す不定形の文字列リソースは、Etag を変更するたびに、変更もできます。この ETag は、データの一部としてクライアント アプリケーションによってキャッシュする必要があります。次のコード例は、HTTP GET 要求への応答の一部として ETag を追加する方法を示しています。このコードを使用して、 `GetHashCode` (このメソッドを必要に応じてオーバーライドし、MD5 などのアルゴリズムを使用してハッシュを生成する) オブジェクトを識別する数値値を生成するオブジェクトのメソッド:

	```C#
	public class OrdersController : ApiController
	{
    	...
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...

    		var hashedOrder = order.GetHashCode();
    		string hashedOrderEtag = String.Format("\"{0}\"", hashedOrder);
    		var eTag = new EntityTagHeaderValue(hashedOrderEtag);

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			...,
    			ETag = eTag
    		};
    		return response;
    	}
    	...
    }
	```

	以下のような web API によって掲示される応答メッセージ

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	> [AZURE。ヒント] セキュリティ上の理由から、機密性の高いデータまたはキャッシュする認証された接続 (HTTPS) 経由で返されるデータを許可して。

	クライアント アプリケーションは、いつでも同じリソースを取得するために後続の GET 要求を発行できるリソースが変更された場合、(それが異なる ETag) キャッシュを破棄するか、キャッシュに新しいバージョンが追加。リソースが大きく、大量のクライアントに送信するための帯域幅を必要とする、同じデータをフェッチする繰り返された要求は非効率になることができます。これを戦うため、HTTP プロトコルは web API でサポートする必要があります取得要求を最適化するための以下のプロセスを定義します。

	- クライアントは、現在キャッシュされているバージョンならどれも一致 HTTP ヘッダーで参照されているリソースのための ETag を含む GET 要求を構築します。

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- Web API で取得操作は要求されたデータ (上の例の順序 2) に対する現在の ETag を取得し、場合どれも一致ヘッダーの値を比較します。

	- 現在の要求されたデータのための ETag には、要求によって提供される ETag が一致すると、リソースが変わっていないと web API は、空のメッセージ ボディと 304 (Not Modified) ステータス コードを HTTP 応答を返す必要があります。

	- 要求されたデータに対する現在の ETag が要求によって提供される ETag が一致しない場合、データが変更され、web API がメッセージのステータス コードは 200 (OK) 新しいデータを HTTP 応答を返す必要があります。

	- 要求されたデータが存在しない場合、web API は HTTP 応答ステータス コード 404 (Not Found) とを返す必要があります。

	- クライアントは、キャッシュを維持するために、ステータス コードを使用します。データが変更されていない場合 (ステータス コード 304) オブジェクトをキャッシュに保存することができ、クライアント アプリケーションがオブジェクトのこのバージョンを使用してください。場合は、データは、キャッシュされたオブジェクトを破棄する必要があり、新しいものが挿入されます (ステータス コード 200) に変わりました。データが利用できない場合 (ステータス コード 404) し、キャッシュからオブジェクトを削除する必要があります。

	> [AZURE。メモ] キャッシュ コントロール ヘッダーのないストアが応答ヘッダーに含まれている場合、オブジェクト常に HTTP ステータス コードに関係なくキャッシュから削除する必要があります。

	下のコード、 `FindOrderByID` 場合なし一致ヘッダーをサポートするための拡張メソッド。場合なし一致ヘッダーを省略すると、指定した順序は常に取得に注意してください。

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderById(int id)
        {
            try
            {
                // Find the matching order
    		    Order order = ...;

                // If there is no such order then return NotFound
                if (order == null)
                {
                    return NotFound();
                }

                // Generate the ETag for the order
                var hashedOrder = order.GetHashCode();
                string hashedOrderEtag = String.Format("\"{0}\"", hashedOrder);

                // Create the Cache-Control and ETag headers for the response
                IHttpActionResult response = null;
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Public = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                // Retrieve the If-None-Match header from the request (if it exists)
                var nonMatchEtags = Request.Headers.IfNoneMatch;

                // If there is an ETag in the If-None-Match header and
                // this ETag matches that of the order just retrieved,
                // then create a Not Modified response message
                if (nonMatchEtags.Count > 0 &&
                    String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
                {
                    response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NotModified,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }
                // Otherwise create a response message that contains the order details
                else
                {
                    response = new OkResultWithCaching<Order>(order, this)
                    {
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }

                return response;
            }
            catch
            {
                return InternalServerError();
            }
        }
    ...
    }
	```

	この例には、追加のカスタムが組み込まれています。 `IHttpActionResult` という名前のクラス `EmptyResultWithCaching`.このクラスは、単にラッパーとして動作します。 `HttpResponseMessage` 応答の本体が含まれていないオブジェクト:

	```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
        public HttpStatusCode StatusCode { get; set; }
		public Uri Location { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = new HttpResponseMessage(StatusCode);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = this.ETag;
            response.Headers.Location = this.Location;
            return response;
        }
    }
	```

	> [AZURE。ヒント] この例では、基になるデータ ソースから取得したデータをハッシュすることで、データのための ETag が生成されます。ETag は、いくつか他の方法で計算することができます、する場合は、プロセスをさらに最適化することができ、データのみが変更された場合、データ ソースからフェッチする必要があります。 このアプローチは、データが大きい場合 (たとえば、データ ソースがリモート データベース) 場合にかなりの遅延につながるデータ ソースへのアクセスに便利です。

- **Etag を使用してオプティミスティック同時実行制御をサポートするには**.

	以前にキャッシュされたデータの更新を有効にする、HTTP プロトコルでは、オプティミスティック同時実行戦略をサポートしています。フェッチとキャッシュ リソース、クライアント アプリケーションはその後変更またはリソースを削除する PUT または削除要求を送信した場合は、ETag を参照する If-match ヘッダーに含める必要があります。Web API は、別のユーザーによってリソースが変更されて既に取得されて以来かどうかを決定し、クライアント アプリケーションに適切な応答を次のように送信するこの情報を使用できます。

	- クライアントは、PUT 要求がリソースの新しい詳細は、If-マッチ ヘッダーで参照されているリソースのキャッシュされているバージョンのための ETag を含むを作成します。次の例は、注文を更新する PUT 要求を示しています。

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	ProductID=3&Quantity=5&OrderValue=250
	```

	- Web API の PUT 操作は、要求されたデータ (上の例の順序 1) に対する現在の ETag を取得し、If-match ヘッダーの値を比較します。

	- 現在の要求されたデータのための ETag には、要求によって提供される ETag が一致すると、リソースが変わっていないと web API は、それが成功した場合、HTTP ステータス コード 204 (いいえコンテンツ) でメッセージを返す更新を実行する必要があります。応答は、キャッシュ制御、更新されたバージョンのリソースの ETag ヘッダーを含めることができます。応答は常に新しく更新されたリソースの URI を参照する Location ヘッダーを含める必要があります。

	- 要求されたデータに対する現在の ETag が要求によって提供される ETag が一致しない場合、データ変わりました別のユーザーがフェッチされて、web API が空のメッセージ ボディと 412 (前提条件の失敗) のステータス コード、HTTP 応答を返す必要があります。

	- 更新されるリソースが存在しない場合、web API は HTTP 応答ステータス コード 404 (Not Found) とを返す必要があります。

	- クライアントは、キャッシュを維持するために、ステータス コード、応答ヘッダーを使用します。データがされている場合 (限り、キャッシュ制御ヘッダーは、ストアを指定しない)、オブジェクトがキャッシュされた維持できるが、ETag を更新する (ステータス コード 204) を更新します。(ステータス コード 412) 変更別のユーザーによってデータが変更された場合、またはキャッシュされたオブジェクトを破棄する必要がありますし、(ステータス コード 404) が見つからなかった。

	次のコード例は、注文のコント ローラーのための PUT 操作の実装を示しています。

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[HttpPut]
    	[Route("api/orders/{id:int}")]
    	        public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
        {
            try
            {
                var baseUri = Constants.GetUriFromConfig();
                var orderToUpdate = this.ordersRepository.GetOrder(id);
                if (orderToUpdate == null)
                {
                    return NotFound();
                }

                var hashedOrder = orderToUpdate.GetHashCode();
                string hashedOrderEtag = String.Format("\"{0}\"", hashedOrder);

                // Retrieve the If-Match header from the request (if it exists)
                var matchEtags = Request.Headers.IfMatch;

                // If there is an Etag in the If-Match header and
                // this etag matches that of the order just retrieved,
                // or if there is no etag, then update the Order
                if (((matchEtags.Count > 0 &&
                     String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                     matchEtags.Count == 0)
                {
                    // Modify the order
                    orderToUpdate.OrderValue = order.OrderValue;
                    orderToUpdate.ProductID = order.ProductID;
                    orderToUpdate.Quantity = order.Quantity;

                    // Save the order back to the data store
                    // ...

                    // Create the No Content response with Cache-Control, ETag, and Location headers
                    var cacheControlHeader = new CacheControlHeaderValue();
                    cacheControlHeader.Private = true;
                    cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                    hashedOrder = order.GetHashCode();
                    hashedOrderEtag = String.Format("\"{0}\"", hashedOrder);
                    var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                    var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                    var response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NoContent,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag,
                        Location = location
                    };

                    return response;
                }

                // Otherwise return a Precondition Failed response
                return StatusCode(HttpStatusCode.PreconditionFailed);
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
	```

	> [AZURE。ヒント] If-match ヘッダーの使用は完全にオプションだと省略 web API が常に更新しようと指定された順序盲目的に上書き更新が別のユーザーによって行われます。更新内容の消失に起因する問題を避けるためには、常に If-match ヘッダーを提供します。

<a name="considerations-for-handling-large"></a>
## 大規模な要求と応答を処理するための考慮事項

クライアント アプリケーション可能性がありますいくつかのメガバイト データの送受信要求を発行する必要がある場合場合がある (またはより大きい) サイズ。待機し、この量のデータが転送されると、クライアント アプリケーションが応答しなくなる可能性があります。大量のデータを含む要求を処理する場合に、次の点を考慮してください。

- **要求と大きなオブジェクトを含む応答を最適化します。**.

	いくつかのリソースはラージ オブジェクトをされたり、画像やその他の種類のバイナリ データなど、大規模なフィールドを含める可能性があります。Web API はアップロードを最適化し、これらのリソースのダウンロードを有効にするストリーミングをサポートする必要があります。

	HTTP プロトコルでは、クライアントに大きなデータ オブジェクトをストリームにチャンク処理された転送エンコーディング メカニズムを提供します。Web API が断片的に戻って返信を送信できるクライアントは、ラージ オブジェクトの HTTP GET 要求を送信するとき _チャンク_ HTTP 経由。応答内のデータの長さが当初知られていない可能性があります (それが生じる)、web API をホストするサーバーは、各チャンク転送エンコードを指定すると応答メッセージを送信する必要があります: チャンク ヘッダーではなく、コンテンツ長のヘッダー。クライアント アプリケーションは、完全な応答を構築するために各チャンクを受信できます。サーバーを送信戻って、サイズが 0 の最後のチャンク データ転送を完了します。	使用して ASP.NET Web API のチャンクを実装することができます、 `PushStreamContent` クラスです。

	商品画像の HTTP GET 要求に応答する操作を次の例に示します。

	```C#
	public class ProductImagesController : ApiController
	{
    	...
    	[HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);

                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	この例では、 `ConnectBlobToContainer` Azure Blob ストレージは、指定されたコンテナー (名は示されていない) に接続するヘルパー メソッドです。 `BlobExists` blob ストレージ コンテナーに指定した名前を持つ blob が存在するかどうかを示すブール値を返す別のヘルパー メソッドです。

	各製品は、独自の画像を blob ストレージで開催です。、 `FileDownloadResult` クラスは、カスタム `IHttpActionResult` 使用するクラス、 `PushStreamContent` 適切な blob からイメージ データを読み込み、非同期的に応答メッセージのコンテンツとして送信するオブジェクト:

	```C#
	public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });

            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
	```

	クライアントは、大規模なオブジェクトを含む新しいリソースを投稿する必要がある場合、操作をアップロードするストリーミングを適用できます。次の例は、Post メソッドのため、 `ProductImages` コント ローラー。このメソッドは、新しい製品の画像をアップロードするクライアントを使用できます。

	```C#
	public class ProductImagesController : ApiController
	{
    	...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	このコードを使用して、別のカスタム `IHttpActionResult` 呼ばれるクラス `FileUploadResult`.このクラスには、非同期的にデータをアップロードするためのロジックが含まれています。

	```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
	```

	> [AZURE。ヒント] Web サービスにアップロードできるデータ量、ストリーミング、とらわれることがなく、1 つの要求可能性がありますおそらくかなりのリソースを消費する大規模なオブジェクト。ストリーミング プロセス中には、web API は、要求内のデータ量がいくつかの許容範囲を超えていることを判断した場合は、操作を中止し、413 (要求エンティティも大) のステータス コードを含む応答メッセージを返すことができますそれ。

	HTTP 圧縮を使用してネットワーク経由で送信するラージ オブジェクトのサイズを最小限に抑えることができます。このアプローチは、クライアントと web API をホストするサーバーで追加の処理を必要とする犠牲にしてネットワーク トラフィックおよび関連付けられたネットワークの待ち時間の量を減らすのに役立ちます。たとえば、圧縮されたデータを受信するクライアント アプリケーションが受け入れる-エンコードを含めることができます: gzip リクエスト ヘッダー (他のデータ圧縮アルゴリズムを指定することも)。サーバーは、圧縮をサポートしている場合それはメッセージ本文のコンテンツ エンコーディング gzip 形式で開催されたコンテンツで応じるべき: gzip 応答ヘッダー。

	> [AZURE。ヒント] ストリーミングのエンコードされた圧縮を組み合わせることができます。まず前にストリーミング データを圧縮し、gzip コンテンツ エンコーディングおよびメッセージ ヘッダーでチャンク転送エンコードを指定します。なお、web API がデータを圧縮するかどうかどうかに関係なく、HTTP 応答を自動的に圧縮する、いくつかの web サーバー (インターネット インフォメーション サービス) などを構成できます。

- **非同期操作をサポートしていないクライアントに部分的な応答を導入します。**.

	As an alternative to asynchronous streaming, a client application can explicitly request data for large objects in chunks, known as partial responses. The client application sends an HTTP HEAD request to obtain information about the object. If the web API supports partial responses if should respond to the HEAD request with a response message that contains an Accept-Ranges header and a Content-Length header that indicates the total size of the object, but the body of the message should be empty. The client application can use this information to construct a series of GET requests that specify a range of bytes to receive. The web API should return a response message with HTTP status 206 (Partial Content), a Content-Length header that specifies the actual amount of data included in the body of the response message, and a Content-Range header that indicates which part (such as bytes 4000 to 8000) of the object this data represents.

	HTTP HEAD 要求と部分的な応答は、API デザイン ガイダンス文書で詳細に説明します。

- **クライアント アプリケーションで不必要な続行ステータス メッセージを送信しません。**.

	大量のデータをサーバーに送信されるクライアント アプリケーションは、サーバーが実際に要求を受け入れるように喜んでであるかどうか最初決定可能性があります。データを送信する前にクライアント アプリケーションが期待に HTTP 要求を送信できます: 100-ヘッダー、空のメッセージは、データのサイズを示すコンテンツ長ヘッダーを続けます。サーバーが要求を処理するために喜んでいる場合は、HTTP ステータス 100 (Continue) を示すメッセージに応じるべきです。クライアント アプリケーションは、続行、メッセージ本文のデータを含む完全な要求を送信できます。

	IIS を使用してサービスをホストする場合、HTTP.sys ドライバーが自動的に検出し、期待を処理: 100-web アプリケーションに要求を渡す前にヘッダーを続けます。つまり、あなたはアプリケーション コードでこれらのヘッダーを参照してくださいする可能性が高いではなく、IIS が既に不適当または大きすぎると判断した任意のメッセージをフィルター処理されていることがわかります。

	.NET Framework を使用してクライアント アプリケーションを作成するかどうかは、すべての POST や PUT メッセージは最初期待とメッセージを送信: 100-既定ではヘッダーを続けます。サーバー側と同様、プロセスは、.NET Framework によって透過的に処理されます。しかし、このプロセスは、小さな要求もサーバーに 2 のラウンドト リップを引き起こす各 POST や PUT 要求の結果します。使用してこの機能を無効にできます、アプリケーションが大量のデータ要求を送信しない場合、 `ServicePointManager` クラスを作成するには `ServicePoint` クライアント アプリケーション内のオブジェクト。A `ServicePoint` オブジェクトは、クライアントが、サーバー上のリソースを識別する Uri のスキームとホスト フラグメントに基づいて、サーバーへの接続を処理します。設定できます、 `Expect100Continue` プロパティ、 `ServicePoint` false オブジェクト。後続のすべての POST や PUT 要求のスキームとホストのフラグメントに一致する URI を通じてクライアントによって行われる、 `ServicePoint` オブジェクトが予期せず送信されます: 100-ヘッダーを続けます。次のコードを構成する方法を示しています、 `ServicePoint` スキームを持つ Uri に送信されるすべての要求を構成するオブジェクト `http` ホスト `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	静的に設定することもできます。 `Expect100Continue` プロパティ、 `ServicePointManager` その後に作成されたすべてのこのプロパティの既定値を指定するクラス `ServicePoint` オブジェクト。詳細についてを参照してください、 [ServicePoint クラス](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) Microsoft の web サイト上のページ。

- **多数のオブジェクトを返すことがあります要求の改ページ調整をサポートします。**.

	コレクションには、多数のリソースが含まれている場合を重要な処理のパフォーマンスに影響を与える web API をホストするサーバー上で起因し、待ち時間の増加の結果としてネットワーク トラフィックのかなりの量を生成可能性があります対応する URI に GET 要求を発行します。

	これらのケースを処理するため web API は要求を絞り込むかより管理しやすい、離散のブロック (またはページ) のデータを取得するクライアント アプリケーションを有効にするクエリ文字列をサポートする必要があります。ASP.NET Web API フレームワークは、クエリ文字列を解析し、前に説明したルーティング ルールに従って適切なメソッドに渡されるパラメーターと値のペアのシリーズにそれらを分割します。メソッドは、クエリ文字列で指定された同じ名前を使用してこれらのパラメーターを受け入れるように実装する必要があります。さらに、これらのパラメーターは、(クライアント要求からクエリ文字列を省略する) 場合に省略可能であるし、意味のある既定値を持つ必要があります。下のコード、 `GetAllOrders` 方法、 `Orders` コント ローラー。このメソッドは、注文の詳細を取得します。この方法は制約された場合それは多分大量のデータを返すことができます。、 `limit` と `offset` パラメーターは既定では小さなサブセットにデータの量を減らす、この場合最初の 10 命令だけします。

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders")]
    	[HttpGet]
    	public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    	{
    	    // Find the number of orders specified by the limit parameter
    	    // starting with the order specified by the offset parameter
    	    var orders = ...
    	    return orders;
    	}
    	...
	}
	```

	クライアント アプリケーションは、URI を使用してオフセット 50 から始まる 30 の注文を取得する要求を発行できます。 _http://www.adventure-works.com/api/orders?limit=30&オフセット = 50_.

	> [AZURE。ヒント] 長さ 2000 以上の文字は URI にクエリ文字列を指定するクライアント アプリケーションを有効にすることは避けてください。多くの web クライアントとサーバーは、この長い Uri を処理できません。

<a name="considerations-for-maintaining-responsiveness"></a>
## 応答性、スケーラビリティ、および可用性を維持するための考慮事項

世界中どこでも多くのクライアント アプリケーションによって同じ web API を利用する可能性があります。高いさまざまなワークロードをサポートし、ビジネス ・ クリティカルな操作を実行するクライアントの可用性を保証するために拡張できるように、重い負荷の下での応答性を維持するために web API が実装されていることを確認することが重要です。これらの要件を満たす方法を決定するときは、次の点を考慮してください。

- **実行時間の長い要求の非同期サポートを提供します。**.

	要求を送信したクライアントをブロックせず、処理に長い時間がかかる場合があります要求を実行する必要があります。Web API は、要求を検証し、作業を実行する別のタスクを開始、[HTTP コード 202 (Accepted) が応答メッセージを返すいくつかの初期チェックを実行できます。タスクを処理、web API の一部として非同期的に実行できますか (web API は、Azure クラウド サービスとして実装) 場合 (web API は、Azure ウェブサイトでホストされます) 場合 Azure WebJob またはワーカー ロールにオフロードできます。

	> [AZURE。メモ] Azure のウェブサイト WebJobs を使用の詳細については、ページを参照してください。 [WebJobs を使用して、Microsoft Azure ウェブサイトでバック グラウンド タスクを実行するには](web-sites-create-web-jobs.md) Microsoft の web サイト。

	Web API は、処理結果をクライアント アプリケーションに返すメカニズムを提供する必要がありますも。処理が終わったかどうかクライアントのポーリング機構定期的にクエリするアプリケーションを提供することによってこれを達成して、その結果、または web の操作が完了したときに通知を送信する API を有効にするを取得できます。

	単純なポーリング機構を実装するを提供することによって、 _ポーリング_ 次の方法を使用して仮想リソースとして機能する URI:

	1. クライアント アプリケーションは、web API に最初の要求を送信します。

	2. Web API テーブル ストレージまたは Microsoft Azure キャッシュに保持されてテーブルの要求に関する情報を格納し、GUID の形式でおそらくこのエントリの一意のキーを生成します。

	3. Web API は、別のタスクとして処理を開始します。Web API としてテーブルにタスクの状態を記録します。 _実行しています。_.

	4. Web API は、メッセージの本文に HTTP ステータス コード 202 (Accepted)、応答メッセージとテーブル エントリの GUID を返します。

	5. タスクが完了したら、web API テーブルに結果を格納およびタスクの状態を設定します _完了します。_.タスクが失敗した場合、web API 可能性がありますもエラーに関する情報を格納され状態に設定 _失敗しました_.

	6. タスクの実行中、クライアントは、独自の処理を実行する続行できます。それは定期的に URI に要求を送信することができます。 _/polling/{guid}_ どこ _{guid}_ GUID が 202 応答メッセージで web API によって返されます。

	7. Web API で、 _/polling {guid}_ URI] テーブルの対応するタスクの状態を照会し、この状態 (を含む HTTP 状態コード 200 (OK) 応答メッセージを返します_実行しています。_, _完了します。_、または _失敗しました_).タスクが完了または失敗した、応答メッセージは処理、または失敗の理由について情報の結果を含めることも。

	通知機能を実装する場合は、使用可能なオプションが含まれます。

	- Azure 通知ハブを使用した、クライアント アプリケーションに非同期の応答をプッシュします。ページ [紺碧通知ハブをユーザーに通知します。](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) マイクロソフトのウェブサイトがさらに詳細に提供します。

	- Web API を提供するサーバーとクライアントの間の永続的なネットワーク接続を保持する彗星モデルを使用して、サーバからメッセージをプッシュするこの接続の使用をクライアントに返送します。MSDN の雑誌の記事 [Microsoft .NET Framework で簡単な彗星アプリケーションの構築](https://msdn.microsoft.com/magazine/jj891053.aspx) 例のソリューションについて説明します。

	- SignalR を使用してデータをプッシュするリアルタイム web サーバーからクライアントに永続的なネットワーク接続を介して。SignalR は NuGet パッケージとして ASP.NET web アプリケーションで使用できます。詳細についてを検索できます、 [ASP.NET SignalR](http://signalr.net/) ウェブサイト。

	> [AZURE。メモ] 彗星と SignalR 両方は、web サーバーとクライアント アプリケーション間の永続的なネットワーク接続を使用します。多数のクライアントが同様に多数の同時接続を必要がありますので拡張性に影響が。

- **各要求がステートレスであることを確認します。**.

	各要求は、原子見なす必要があります。クライアント アプリケーションで 1 つの要求と同じクライアントによって送信された後続の要求間の依存関係はないはずです。このアプローチを支援スケーラビリティです。web サービスのインスタンスは、多数のサーバーに展開できます。クライアントの要求は、これらのインスタンスのいずれかに向けることができるし、結果は常に同じをする必要があります。同様の理由で可用性も向上します。クライアント アプリケーションに悪影響を与えることなしで場合 (Azure トラフィック マネージャーを使用して)、別のインスタンスに要求をルーティングできます web サーバーが失敗しながら、サーバを再起動すると。

- **クライアントと DOS 攻撃の可能性を減らすために実装の調整を追跡します。**.

	特定のクライアント要求の数が多い場合時間の指定された期間内サービスを独占して他のクライアントのパフォーマンスに影響を与えるでしょう。すべての着信要求の IP アドレスを追跡することによって、または各アクセスの認証をログインすることによりこの問題を軽減するには、web API はクライアント アプリケーションから呼び出しを監視できます。この情報を使用すると、リソースへのアクセスを制限することができます。クライアントは、定義されている制限を超えた場合、web API ステータス 503 (サービスを使用できません) で応答メッセージを戻り、クライアントが却下されることがなく次の要求を送信するときを指定する Retry-after 後ヘッダーが含まれます。この戦略は、一連のシステムを停止クライアントからサービス拒否 (DOS) 攻撃の可能性を減らすために助けることができます。

- **慎重に永続的な HTTP 接続を管理します。**.

	HTTP プロトコルでは、利用可能な永続的な HTTP 接続をサポートしています。HTTP 1.0 準拠追加接続: 維持-クライアント アプリケーションは、新しいものを開くのではなく、それ以降の要求を送信する同じ接続を使用することができますをサーバーに示すことができる生きているヘッダー。クライアントにホストで定義された期間内で接続が再利用できない場合、接続は自動的に閉じられます。この動作は、デフォルトの HTTP 1.1 キープア ライブ ヘッダーをメッセージに含める必要はありませんので、Azure サービスで使用されているのです。

	Keeping a connection open can can help to improve responsiveness by reducing latency and network congestion, but it can be detrimental to scalability by keeping unnecessary connections open for longer than required, limiting the ability of other concurrent clients to connect. It can also affect battery life if the client application is running on a mobile device; if the application only makes occassional requests to the server, maintaining an open connection can cause the battery to drain more quickly. To ensure that a connection is not made persistent with HTTP 1.1, the client can include a Connection:Close header with messages to override the default behavior. Similarly, if a server is handling a very large number of clients it can include a Connection:Close header in response messages which should close the connection and save server resources.

	> [AZURE。メモ] 持続的 HTTP 接続は、繰り返し通信チャネルを確立するのに関連付けられているネットワーク オーバーヘッドを軽減する純粋なオプション機能です。Web API も、クライアント アプリケーションは、利用可能である、永続的な HTTP 接続に依存します。彗星型の通知システムを実装するのに永続的な HTTP 接続を使用しません。ソケット (または websocket を使用可能な場合) を利用する必要があります代わりに TCP 層で。最後に、キープア ライブ ヘッダーが限定使用クライアント アプリケーションは、プロキシ経由でサーバーと通信する場合に注意してください。だけで、クライアントとプロキシ接続は永続的ななります。

## 出版および web API の管理に関する考慮事項

Web API をクライアント アプリケーションで使用できるように、web API をホスト環境に展開する必要があります。ホスト プロセスのいくつかの他の種類がありますが、この環境は、通常 web サーバーです。Web API を公開するときは、次の点を考慮してください。

- すべての要求を認証および承認する必要があります、適切なレベルのアクセス制御を適用する必要があります。
- 商業 web API は、応答時間に関し様々 な品質保証されるおそれがあります。負荷は時間の経過とともに大幅に増減する場合そのホスト環境がスケーラブルなことを確認することが重要です。
- 場合の収益目的のためメーター要求する必要があります。
- Web API へのトラフィックの流れを規制し、そのクォータに達しました特定のクライアント調整を実装する必要があります。
- ログおよび監査のすべての要求と応答の規制要件が必須で。
- 可用性を確保するのには、web API をホストするサーバーの状態を監視し、必要に応じてそれを再起動する必要があります。

Web API の実施に関し技術的な問題からこれらの問題を分離することができると便利です。このため、作成を検討、 [ファサード](http://en.wikipedia.org/wiki/Facade_pattern)、個別のプロセスとして実行して web API に要求をルーティングします。ファサードは、管理操作を提供することができます、楽しみにして web API への要求を検証します。ファサードも含めて多くの機能的な利点をもたらすことができます。

- 複数の web Api のための統合ポイントとして機能します。
- メッセージの変換およびさまざまなテクノロジを使用して構築されたクライアントの通信プロトコルの変換します。
- キャッシュの要求と応答を抑える web API をホストするサーバーの負荷します。

## Web API をテストするための考慮事項
Web API は、ソフトウェアの他の作品として徹底的にテスト必要があります。アプリケーションの他のタイプと同様に、各操作の機能を検証するための単体テストの作成を検討してください。詳細については、ページを参照してください。 [単体テストを使用したコードの検証](https://msdn.microsoft.com/library/dd264975.aspx) Microsoft の web サイト。

> [AZURE。メモ] サンプル web このガイドで使用可能な API には、ユニット選択した操作上のテストを実行する方法を示すテスト プロジェクトが含まれています。

Web API の性質は、それが正しく動作することを確認する独自の追加の要件をもたらします。以下の点に特に注意を払う必要があります。

- 彼らが正しい操作を呼び出すことを確認するすべてのルートをテストします。返される HTTP ステータス コード 405 (メソッドは許可されていません) に特に注意してくださいこれはルートとそのルートに派遣することができます HTTP メソッド (GET、POST、PUT、DELETE) の不一致を示すことができますが予期せず。

	特定のリソースに POST リクエストを送信する場合など、それらをサポートしていないルートに HTTP 要求を送信 (POST 要求する必要がありますリソース コレクションに送信のみ)。これらの場合は、唯一の有効な応答 _必要があります。_ ステータス コード 405 (不可) であります。

- すべてのルートが適切に保護されているし、適切な認証と承認チェックの対象を確認します。

	> [AZURE。メモ] ユーザー認証などのセキュリティのいくつかの側面は、web API ではなく、ホスト環境の責任にする可能性が最も高いが、まだ展開プロセスの一環としてセキュリティ テストを含める必要があります。

- 各操作によって実行される例外処理をテストし、適切で有意義な HTTP 応答がクライアント アプリケーションに返されることを確認します。
- 要求および応答メッセージが整形式ことを確認します。たとえば、HTTP POST 要求には、x-ロード-したいだけの形式で新しいリソースのデータが含まれている場合、ことを確認対応する操作正しくデータを解析し、リソースを作成します正しい場所ヘッダーを含む新しいリソースの詳細を含む応答を返します。
- 応答メッセージですべてのリンクや Uri を確認します。たとえば、HTTP POST メッセージには、新しく作成されたリソースの URI が返されます。HATEOAS のすべてのリンクを無効にする必要があります。

	> [AZURE。重要です] サービス管理 API を介して web API を公開する場合、管理サービスの URL と web API をホストしている web サーバーのこれらの Uri は反映されます。

- 各操作入力のさまざまな組み合わせの正しいステータス コードを返すことを確認します。たとえば。
	- クエリが成功した場合、それは 200 (OK) ステータス コードを返す必要があります。
	- リソースが見つからない場合、操作において、ポストバックするために HTTP ステータス コード 404 (Not Found) を実行する必要があります。
	- クライアントは、正常にリソースを削除する要求を送信した場合、ステータス コードは 204 (いいえコンテンツ) をする必要があります。
	- ステータス コードは 201 (Created) をする必要があります、クライアントでは、新しいリソースを作成するリクエストを送信した場合

予期しない応答ステータス コード 5 xx の範囲に気を付けろ。これらのメッセージは通常、有効な要求を達成できなかったことを示すホスト サーバーによって報告されます。

- クライアント アプリケーションが指定し、web API は応答メッセージで必要な情報を返すことを確認できます要求ヘッダーの組み合わせをテストします。

- クエリ文字列をテストします。操作は、省略可能なパラメーター (改ページ要求など) を取ることができる、さまざまな組み合わせやパラメーターの順序をテストします。

- 非同期操作が正常に完了することを確認します。Web API は、大きなバイナリ オブジェクト (ビデオ、オーディオなど) を返す要求のためのストリーミングをサポートする場合は、クライアントからの要求データをストリーミングしながらブロックされていないことを確認します。Web API は、実行時間の長いデータ変更操作のためのポーリングを実装する場合操作が状態を報告することを確認正しく進行。

作成し、web API が監禁の下で十分に動作することを確認するテストを実行する必要がありますもします。Visual Studio Ultimate を使用して web パフォーマンス テストおよびロード テスト プロジェクトを作成できます。詳細については、ページを参照してください。 [リリースする前にアプリケーションのパフォーマンス テストを実行します。](https://msdn.microsoft.com/library/dn250793.aspx) Microsoft の web サイト。

## 公開と Azure API 管理サービスを使用して web API を管理

Azure を提供します、 [API 管理サービス](http://azure.microsoft.com/documentation/services/api-management/) 発行し、web API を管理する使用ことができます。この機能を使用すると、1 つまたは複数の web Api のファサードの役割を果たすサービスを生成できます。サービス自体は作成して Azure 管理ポータルを使用して構成することができますスケーラブルな web サービスです。Web API を次のように管理し、このサービスを使用できます。

1. Web API をウェブサイト、Azure クラウド サービス、または Azure の仮想マシンに展開します。

2. API の管理サービスを web API に接続します。管理 API の URL に送信された要求は、web API の Uri にマップされます。同じ API の管理サービスは、1 つ以上の web API に要求をルーティングできます。これは、1 つの管理サービスに複数の web Api を集約することができます。同様に、またはパーティションの異なるアプリケーションで使用できる機能を制限する必要がある場合は、同じ web API を 1 つ以上の API の管理サービスから参照できます。

	> [AZURE。メモ] HTTP GET 要求に対する応答の一部として生成されたリンクは HATEOAS の Uri は、API の管理サービスの web API をホストしている web サーバーではなく URL を参照ください。

3. 各 web API、web API 公開操作を取ることができるすべての省略可能なパラメーターと共に HTTP 操作を指定入力として。API 管理サービスは同じデータを繰り返し要求を最適化する web API から受信した応答をキャッシュすべきかどうかも構成できます。各操作が発生する HTTP 応答の詳細を記録します。この情報は、正確かつ完全であることが重要ですので、開発者は、ドキュメントを生成する使用されます。

	Azure 管理ポータルで提供されるウィザードを使用して手動での操作を設定できます、または WADL または闊歩形式の定義を含むファイルからインポートすることができます。

4. API の管理サービスと web API をホストしている web サーバー間の通信のセキュリティ設定を構成します。API 管理サービスは現在、基本認証と証明書、および OAuth 2.0 のユーザー認証を使用して相互認証をサポートしています。

5. 製品を作成します。製品はパブリケーションの単位web Api に製品管理サービスに接続したことを追加します。製品の発行時に web Api なる開発者が利用できます。

	> [AZURE。メモ] 製品を公開する前に製品にアクセスでき、これらのグループにユーザーを追加するユーザー グループを定義できます。この開発者を制御できます、web API を使用できるアプリケーション。場合承認 API は、web、それにアクセスする前に開発者に送信要求製品管理者。管理者は、許可または、開発者へのアクセスを拒否できます。既存の開発者は、状況が変わった場合にもブロックできます。

6.	それぞれの web API のポリシーを構成します。ポリシーはドメイン間呼び出し許可かどうかが、JSON と XML 間の変換をデータ形式に透過的に、指定した IP の範囲からの呼び出しを制限するかどうかどうかは、クライアントを認証する方法など、管理使用量クォータ、および呼び出しの速度を制限するかどうか。全体製品、製品の単一の web API、web API の個々 の操作全体にわたるポリシーをグローバルに適用することができます。

これらのタスクを実行する方法についての完全な詳細を見つけることができます、 [API の管理](http://azure.microsoft.com/services/api-management/) Microsoft の web サイト上のページ。Azure API の管理サービスも web API を構成するプロセスを簡略化するためのカスタム インターフェイスを作成することができます独自の REST インターフェイスを提供します。詳細についてを参照してください、 [紺碧 API 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn776326.aspx) Microsoft の web サイト上のページ。

> [AZURE。ヒント] Azure では、フェイルオーバとロード バランシングを実装し、異なる地理的な場所にホストされている web サイトの複数のインスタンス間での待ち時間を減らすことができます Azure トラフィック マネージャーを提供します。Azure トラフィック マネージャーを使用すると、API の管理サービスと組み合わせてAPI の管理サービスは、web サイトを通じて Azure トラフィック マネージャーのインスタンスに要求をルーティングできます。 詳細についてを参照してください、 [トラフィック マネージャーの負荷分散方式について](https://msdn.microsoft.com/library/azure/dn339010.aspx) Microsoft の web サイト上のページ。

> あなたのウェブサイトのためのカスタムの DNS 名を使用している場合この構造で Azure トラフィック マネージャー web サイトの DNS 名をポイントする web サイトごとに適切な CNAME レコードを構成する必要があります。

## クライアント アプリケーションを構築する開発者をサポート
通常クライアント アプリケーションを構築する開発者には、web API、およびパラメーター、データ型、戻り値の型と異なる要求と web サービスとクライアント アプリケーションの間の応答を示すリターン コードに関するドキュメントにアクセスする方法に関する情報が必要です。

### Web API、REST 操作を文書化します。
Azure API 管理サービスには、web API によって公開される残りの操作を記述した開発者ポータルが含まれています。製品が公開されているこのポータルに表示されます。開発者は、このポータルを使用してへのアクセスにサインアップ管理者は、承認し、または要求を拒否できます。開発者が承認された場合、その開発するクライアント アプリケーションからの呼び出しの認証に使用されるサブスクリプション キーが割り当てられます。このキーは、それは拒否されますそれ以外の場合 web API 呼び出しごとに提供されなければなりません。

このポータルは、次のようにいます。

- 公開する操作、必要なパラメーター、返すことができる別の応答の一覧、製品のドキュメント。この情報は、セクションのリストの手順 3 で提供される詳細から生成されます。 [Microsoft Azure API 管理サービスを使用して web API を公開](#publishing-a-web-API).

- JavaScript、c#、Java、Ruby、Python、および PHP を含むいくつかの言語から操作を呼び出す方法を示すコード スニペット。

- 開発者が製品の各操作をテストし、結果を表示する HTTP リクエストを送信することができる開発者のコンソール。

- 開発者が任意の問題や問題を報告できるページ。

Azure 管理ポータルでは、スタイリングと、組織のブランドに合わせてレイアウトを変更する開発者ポータルをカスタマイズすることができます。

### クライアント SDK の実装
Building a client application that invokes REST requests to access a web API requires writing a significant amount of code to construct each request and format it appropriately, send the request to the server hosting the web service, and parse the response to work out whether the request succeeded or failed and extract any data returned. To insulate the client application from these concerns, you can provide an SDK that wraps the REST interface and abstracts these low-level details inside a more functional set of methods. A client application uses these methods, which transparently convert calls into REST requests and then convert the responses back into method return values. This is a common technique that is implemented by many services, including the Azure SDK.

それは一貫して実装する必要があるし、慎重にテストかなり事業は、クライアント側の SDK を作成します。このプロセスの多くは機械的に行うことができ、多くのベンダーは、これらのタスクの多くを自動化できるツールを提供します。

## Web API の監視

公開して web API を監視することができますを展開した方法に応じて web API を直接、またはあなたは、API 管理サービスを通過するトラフィックを分析することによって使用状況と状態の情報を収集できます。

### Web API を直接監視
ASP.NET Web API テンプレート (Web API プロジェクトまたは Azure クラウド サービスで Web ロールとして) と Visual Studio 2013 を使用して、あなたの web API を実装した場合は、ASP.NET アプリケーションのインサイトを使用して、可用性、パフォーマンス、および使用状況データを収集できます。アプリケーションのインサイトは透過的に追跡し、web API がクラウドに展開される要求と応答に関する情報を記録するパッケージパッケージをインストールし、構成したら、それを使用するための API、web 内のコードを修正する必要はありません。Azure の web サイトへ web API を展開するときすべてのトラフィックの検査し、次の統計情報が収集されます。

- サーバーの応答の時間。

- サーバーへの要求と各要求の詳細の数。

- 平均応答時間の面でトップの遅い要求。

- いずれかの詳細には、要求が失敗しました。

- 異なるブラウザーやユーザ エージェントで開始されたセッションの数です。

- 最もよく表示されるページ (主に有用な web アプリケーションの web Api ではなく)。

- Web API にアクセスする別のユーザーの役割。

このデータは、Azure 管理ポータルからリアルタイムで表示できます。Web API のヘルスを監視 webtests を作成することも。Webtest は、web API に指定された URI に定期的な要求を送信し、応答をキャプチャします。正常な応答 (HTTP ステータス コード 200) などの定義を指定することができます、要求がこの応答を返さない場合は、管理者に送信される通知の手配します。必要に応じて、管理者はそれが失敗した場合は、web API をホストするサーバーを再起動できます。

、 [アプリケーションの洞察力 - 起動アプリの状態と使用状況を監視](app-insights-start-monitoring-app-health-usage/) マイクロソフト web サイト上のページより多くの情報を提供します。

### API 管理サービスを通じて web API の監視

API の管理サービスを使用して、あなたの web API を公開している、Azure 管理ポータルの API の管理] ページに、サービスの全体的なパフォーマンスを表示することができますダッシュ ボードが含まれます。解析ページでは、製品を使用する方法の詳細にドリル ダウンできます。このページには、次のタブが含まれています。

- **使い方**.このタブでは、API の呼び出しと時間をかけてこれらの呼び出しを処理するために使用する帯域幅の数に関する情報を提供します。製品、API、および操作の使用方法の詳細をフィルター処理できます。

- **健康**.このタブにより、API リクエスト (返される HTTP ステータス コード) の結果、キャッシュ ポリシー、API の応答時間およびサービス応答時間の有効性を表示します。もう一度、健康製品、API、および操作によってデータをフィルターできます。

- **アクティビティ**.このタブでは、ブロックされたコール、平均応答時間、および応答時間各製品、web API、および操作の呼ばれる失敗した成功した呼び出しの数のテキストの要約を提供します。このページには、それぞれの開発者によって行われた呼び出しの数も表示されます。

- **一目で**.このタブには、ほとんどの API 呼び出しと製品、web Api、およびこれらの呼び出しを受信する操作を作るための責任の開発者を含むパフォーマンス データの概要が表示されます。

この情報を使用すると、必要なホスト環境を拡張しより多くのサーバーを追加する場合、特定の web API または操作がボトルネックの原因であるかどうかを決定することができます。1 つまたは複数のアプリケーション リソースの不均衡な量を使用しているし、クォータを設定し、通話料金を制限する適切なポリシーを適用するかどうかも確かめることができます。

> [AZURE。メモ] 公開された製品の詳細を変更することができます、変更がすぐに適用されます。たとえば、追加または、web API が含まれている製品を再発行することを必要とせず web API から操作を削除できます。

## 関連パターン
- 、 [ファサード](http://en.wikipedia.org/wiki/Facade_pattern) パターンでは、web API へのインターフェイスを提供する方法について説明します。

## 詳細については
- ページ [ASP.NET Web API について学ぶ](http://www.asp.net/web-api) マイクロソフトの web サイトは、Web API を使用して、RESTful web サービスを構築するための詳細な導入を提供します。
- ページ [ASP.NET Web API でのルーティング](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) マイクロソフトの web サイトは ASP.NET Web API のフレームワークにどのように規則に基づくルーティングの動作について説明します。
- 属性ベースのルーティングの詳細については、ページを参照してください。 [Web API 2 のルーティング属性](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) Microsoft の web サイト。
- 、 [基本的なチュートリアル](http://www.odata.org/getting-started/basic-tutorial/) OData web サイトのページは、OData プロトコルの機能について紹介を提供します。
- 、 [ASP.NET Web API OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) Microsoft の web サイト上のページには、例と ASP.NET を使用して OData web API の実装に関する詳細情報が含まれています。
- ページ [Web API と Web API OData バッチ サポートを導入](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) マイクロソフトの web サイトは web API で OData を使用してバッチ処理を実装する方法について説明します。
- 記事 [等羃性パターン](http://blog.jonathanoliver.com/idempotency-patterns/) Jonathan Oliver のブログ等羃性とデータ管理操作との関係の概要を示します。
- 、 [ステータス コードの定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) W3C の web サイト上のページには、HTTP ステータス コードの完全なリストとその説明が含まれます。
- ASP.NET Web API による HTTP 例外の処理の詳細についてを参照してください、 [ASP.NET Web API の例外処理](http://www.asp.net/web-api/overview/error-handling/exception-handling) Microsoft の web サイト上のページ。
- 記事 [Web API グローバル エラー処理](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) マイクロソフトの web サイトはグローバル エラー処理とログの web API のための戦略を実装する方法について説明します。
- ページ [WebJobs を使用して、Microsoft Azure ウェブサイトでバック グラウンド タスクを実行するには](web-sites-create-web-jobs.md) マイクロソフトの web サイトは、情報と Azure ウェブサイトのバック グラウンド処理を実行する WebJobs を使用する例を示します。
- ページ [紺碧通知ハブをユーザーに通知します。](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) マイクロソフトのウェブサイトは、Azure 通知ハブを使用して、クライアント アプリケーションに非同期の応答をプッシュする方法を示しています。
- 、 [API の管理](http://azure.microsoft.com/services/api-management/) Microsoft の web サイト上のページでは、制御を提供する製品を発行し、web API へのアクセスをセキュリティで保護する方法について説明します。
- 、 [紺碧 API 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn776326.aspx) Microsoft の web サイト上のページでは、API 管理 REST API を使用してカスタム管理アプリケーションを構築する方法について説明します。
- 、 [トラフィック マネージャーの負荷分散方式について](https://msdn.microsoft.com/library/azure/dn339010.aspx) Microsoft の web サイト上のページは、web API をホストする web サイトの複数のインスタンス間で要求を負荷分散する Azure トラフィック マネージャーを使用できる方法をまとめたものです。
- 、 [アプリケーションの洞察力 - 起動アプリの状態と使用状況を監視](app-insights-start-monitoring-app-health-usage.md) Microsoft の web サイト上のページでは、インストールおよび ASP.NET Web API プロジェクトでアプリケーション洞察力を構成する詳細について説明します。
- ページ [単体テストを使用したコードの検証](https://msdn.microsoft.com/library/dd264975.aspx) マイクロソフトの web サイトを作成して、Visual Studio を使用した単体テストの管理の詳細についてを提供します。
- ページ [リリースする前にアプリケーションのパフォーマンス テストを実行します。](https://msdn.microsoft.com/library/dn250793.aspx) マイクロソフトの web サイトは、web パフォーマンス テストを作成およびロード テストのプロジェクトに Visual Studio Ultimate を使用する方法をについて説明します。
