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

# API 执行指导

![](media/best-practices-api-implementation/pnp-logo.png)


在本指南中的某些主题可能在将来会改变，并根据讨论。我们欢迎您的反馈!

## 概述
精心设计 rest 风格的 web API 定义资源、 关系和可访问的客户端应用程序的导航方案。当您实施和部署 web API 时，你应考虑环境承载 web API 和方式对物理需求在 web API 构造而不是数据的逻辑结构。本指南集中在执行 web API 和发布它，使其可用于客户端应用程序的最佳做法。安全问题是在 API 安全指导文件中分别介绍了。在 API 设计指导文件中，可以找到有关 web API 设计的详细的信息。

## 实现 rest 风格的 web API 的注意事项
以下各节说明了使用 ASP.NET Web API 模板生成 rest 风格的 web API 的最佳实践。关于使用 Web API 模板的详细信息，请访问 [了解 ASP.NET Web API](http://www.asp.net/web-api) 在 Microsoft 网站上的页面。

## 实现请求路由的注意事项

通过使用 ASP.NET Web API 实现的服务，在每个请求路由到一种方法 _控制器_ 类。Web API 框架提供了两个主要选项执行路由; _根据公约 》_ 路由和 _基于属性的_ 路由。当你确定将请求路由的最佳方式在您的 web API 时，请考虑以下几点:

- **了解的限制和要求的公约基于路由**.

	默认情况下，Web API 框架使用基于公约的路由。Web API 框架创建初始的路由表，其中包含以下项:

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	路线可以为泛型，如包括文本 _api_ 和变量如 _{控制器}_ 和 _{} id_.基于公约的路由允许一些元素路线是可选。Web API 框架可以确定要调用控制器中，通过匹配请求在 API 中，方法名称的开头部分中的 HTTP 方法，然后通过匹配任何可选参数的方法。例如，如果指定控制器 _订单_ 包含的方法 _GetAllOrders()_ 或 _GetOrderByInt (int id)_ 然后 GET 请求 _http://www.adventure-works.com/api/orders/_ 将被定向到的方法 _GetAlllOrders()_ 和 GET 请求 _http://www.adventure-works.com/api/orders/99_ 将路由到该方法 _GetOrderByInt (int id)_.如果没有匹配的方法始于 Get 前缀在控制器中，Web API 框架用 HTTP 405 (不允许使用此方法) 消息进行答复。另外，路由表中指定的参数 (id) 的名称必须为参数的名称相同 _GetOrderById_ 方法，否则 Web API 框架将回复 HTTP 404 (未找到) 响应。

	同样的规则适用于邮政、 付诸表决和删除 HTTP 请求;更新订单 101 的细节 PUT 请求将定向到的 URI _http://www.adventure-works.com/api/orders/101_邮件正文中的将包含的命令，新的详细信息，此信息将作为参数传递给命令控制器以前缀开头的名称与中的方法 _付诸表决_如 _PutOrder_.

	默认路由表将不匹配的请求，如引用子资源在 rest 风格的 web API， _http://www.adventure-works.com/api/customers/1/orders_ (找到客户 1 的所有订单的细节)。若要处理这种情况下，可以将自定义路由添加到路由表:

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	这条路线指示匹配到 URI 的请求 _GetOrdersForCustomer_ 中的方法 _客户_ 控制器。此方法必须采取一个名为的单个参数 _养狗场_:

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

	> [AZURE。小贴士] 利用默认路由，尽可能和避免定义许多复杂的自定义路由因为这可能导致的脆性 (它是很容易将方法添加到结果中含糊不清的路线控制器) 和性能 (越大的路由表，更多的工作 Web API 框架已做工作出哪条路线匹配给定的 URI) 降低。保持简单的 API 和路线。有关详细信息，请参阅 Web API 设计指导资源 API 周围组织。如果你必须定义自定义路由，较为可取的办法是使用基于属性的路由此部分后面所述。

	关于公约 》 基于路由的详细信息，请参阅页 [ASP.NET Web API 中的路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) 在微软的网站。

- **歧义的路由**.

	如果控制器中的多个方法匹配相同的路线，则将公约基于路由可以导致含糊不清的通路。在这些情况下，Web API 框架用包含文本"多个操作被找到相匹配的请求"HTTP 500 (内部服务器错误) 响应消息进行响应。

- **更喜欢基于属性的路由**.

	基于属性的路由提供一种方式连接到控制器中的方法的路线。而不是依赖公约基于路由的模式匹配功能，您可以显式注释中详细的路线，他们应该是相关联的控制器的方法。方法有助于清除可能含糊之处。此外，如在设计时定义显式路由这种方法是比公约基于路由在运行时效率更高。下面的代码演示如何将应用 _路线_ 归因于客户控制器中的方法。这些方法还使用公共属性来指示他们应对 _HTTP GET_ 请求。此属性使您可以命名你使用任何方便的命名方案，而不是所预期的公约基于路由的方法。你也可以注释方法 _HttpPost_, _HttpPut_和 _HttpDelete_ 属性定义响应其他类型的 HTTP 请求的方法。

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

	基于属性的路由还具有作为开发人员需要在未来; 维护代码的文档的有用的副作用它是立即清楚哪种方法属于哪条路线，和 _公共_ 属性澄清方法响应的 HTTP 请求的类型。

	基于属性的路由使您能够定义约束限制匹配参数的方式。约束可以指定参数的类型和在某些情况下，他们还可以指示参数值的可接受范围。在以下示例中的 id 参数 _FindCustomerByID_ 方法必须是一个非负整数。如果应用程序提交与负面的客户数量的 HTTP GET 请求，Web API 框架将以 HTTP 405 (不允许使用此方法) 消息响应:

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

	基于属性的路由的详细信息，请参阅页 [属性在 Web API 2 路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) 在微软的网站。

- **在路由支持 Unicode 字符**.

	用于标识资源在 GET 请求中的密钥可以是字符串。因此，在全球化应用程序，您可能需要支持包含非英文字符的 Uri。

- **区分应该不会被路由的方法**.

	如果您正在使用基于公约的路由，表明不对应的装饰他们与 HTTP 操作的方法 _侵权_ 属性。这通常适用于由其他控制器中的方法的帮助器方法用于定义，此属性将防止这些方法匹配和错误的 HTTP 请求所调用。

- **在利弊权衡的子域中放置的 API**.

	默认情况下，ASP.NET web API 组织成的 Api _/api_ 目录中的域，如 _http://www.adventure-works.com/api/orders_.此目录驻留在相同的域公开的同一主机的任何其他服务。它可能有利于拆分 web API 出来到其自身运行在单独的主机，如 Uri 的子域 _http://api.adventure-works.com/orders_.这种分离使您能够进行分区和更有效地扩展 web API，而不会影响任何其他 web 应用程序或服务在运行 _www.adventure-works.com_ 域。

	然而，不同的子域中放置 web API 也可以导致安全问题。任何 web 应用程序或在托管服务 _www.adventure-works.com_ 那调用运行在其他地方可能违反许多 web 浏览器的同源策略的 web API。在这种情况，它将需要启用跨源资源在主机之间共享 (CORS)。更多的信息，请参阅 API 安全指导文件。

## 处理请求时的注意事项

一旦请求从客户端应用程序中的 web API 的方法已成功传送，必须尽可能有效的方式在处理该请求。当您实现的代码来处理请求时，请考虑以下几点:

- **得到，放，删除、 头和修补程序的行动应该是幂等元**.

	实现这些要求的代码，不应施加任何的副作用。同样的要求，反复相同的资源应该导致相同的状态。例如，将删除的多个请求发送到相同的 URI 应该有同样的效果，虽然响应消息中的 HTTP 状态代码可能不同 (第一次的删除请求可能返回状态代码 204 (无内容)，而随后删除请求可能返回统计代码 404 (未找到))。

> [AZURE。注意] 这篇文章 [幂等性模式](http://blog.jonathanoliver.com/idempotency-patterns/) Jonathan Oliver 博客上概述的幂等性和它如何与数据管理操作。

- **创建新的资源的发布操作应该这样做，没有不相关的副作用**.

	如果一个 POST 请求打算创建一个新的资源，请求的影响应该是有限的新资源 (和可能任何直接相关资源如果某种形式的联系) 例如，在电子商务系统中，创建客户的新订单的 POST 请求可能也修订库存水平和生成计费信息但它不应该修改为订单不直接相关的信息或有任何其他副作用对系统的整体状态。

- **避免实施健谈的帖子，付诸表决和删除操作**.

	支持后，把和删除请求对资源集合。POST 请求可以包含多个新资源的详细信息并将它们添加到同一集合、 PUT 请求可以替换整个集的资源的集合，和删除请求可以删除整个集合。

	请注意，在 ASP.NET Web API 2 中所包括的 OData 支持提供批处理请求的能力。客户端应用程序可以打包几个 web API 请求并发送到一个单独的 HTTP 请求，在服务器和接收单一的 HTTP 响应，其中包含对每个请求的答复。有关详细信息，请参见页 [介绍 Web API 和 Web API OData 批处理支持](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) 在微软的网站。

- **遵守 HTTP 协议发送响应发送回客户端应用程序时**.

	Web API 必须返回包含正确的 HTTP 状态代码，使客户端来确定如何处理的结果，适当的 HTTP 标头，以便客户能够理解的结果和适当格式化的机构，以便使客户端能够解析结果性质的邮件。如果您使用的是资源的 ASP.NET Web API 模板，默认策略为实现响应 HTTP POST 请求的方法是资源的简单地返回新创建，一个拷贝下面的示例所示:

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

	如果 POST 操作成功，Web API 框架将创建与消息正文的 HTTP 响应状态代码 200 (OK) 和客户的详细信息。然而，在这种情况下，根据 HTTP 协议，术后应返回状态码 201 (Created) 响应消息应包括新创建的资源的 URI 位置的标头中的响应消息。

	为了提供这些功能，通过使用返回您自己的 HTTP 响应消息 `IHttpActionResult` 界面。这种方法给你精细控制的 HTTP 状态代码，响应消息，甚至数据和格式中的标题在答复邮件正文中，如下面的代码示例所示。此版本的 `CreateNewCustomer` 方法更密切地符合客户以下 HTTP 协议的期望。的 `Created` 方法 `ApiController` 类构造响应消息从指定的数据，并将位置标头添加到结果:

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

- **支持内容协商**.

	响应消息的正文可能包含各种格式的数据。例如，HTTP GET 请求在 JSON，就可以返回数据或 XML 格式。当客户端提交请求时，它可以包括 Accept 标头，指定它可以处理的数据格式。这些格式被指定的媒体类型。例如，一个问题检索图像的 GET 请求的客户端可以指定列出的媒体类型，客户端可以处理，如"图像/jpeg、 gif 图像/图像/png"Accept 标头。 当 web API 返回的结果时，它应该通过使用其中一种媒体类型设置数据的格式和响应的内容类型标头中指定的格式。

	如果客户端没有指定 Accept 标头，然后使用适当的默认格式为响应正文。例如，ASP.NET Web API 框架默认为 JSON 为基于文本的数据。

	> [AZURE。注意] ASP.NET Web API 框架执行 Accept 头一些自动检测和处理它们本身基于响应消息的正文中的数据的类型。例如，如果响应消息的正文包含 CLR (公共语言运行库) 对象，ASP.NET Web API 自动设置格式的响应以 json 格式与内容类型标头中设置为"应用程序/json"，除非客户端指示它需要结果作为 XML，案例的 ASP.NET Web API 框架格式为 XML 响应和设置为"text/xml"响应的内容类型标头的反应。然而，它可能需要处理操作的执行代码中显式指定不同的媒体类型的 Accept 头。

- **提供链接，以支持 HATEOAS 样式的导航和资源发现**.

	The API Design Guidance describes how following the HATEOAS approach enables a client to navigate and discover resources from an initial starting point. This is achieved by using links containing URIs; when a client issues an HTTP GET request to obtain a resource, the response should contain URIs that enable a client application to quickly locate any directly related resources. For example, in a web API that supports an e-commerce solution, a customer may have placed many orders. When a client application retrieves the details for a customer, the response should include links that enable the client application to send HTTP GET requests that can retrieve these orders. Additionally, HATEOAS-style links should describe the other operations (POST, PUT, DELETE, and so on) that each linked resource supports together with the corresponding URI to perform each request. This approach is described in more detail in the API Design Guidance document.

	目前有没有指导 HATEOAS，执行的标准，但下面的示例演示一种可能的方法。在此示例中，查找客户的详细信息的 HTTP GET 请求返回一个包括 HATEOAS 链接引用该客户订单的响应:

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

	在此示例中，客户数据由 `Customer` 下面的代码片断所示的类。HATEOAS 链接在举行 `Links` 集合属性:

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

	HTTP GET 操作检索客户数据从存储和构造 `Customer` 对象，并随后填充 `Links` 集合。结果格式化为 JSON 响应消息。每个环节包括以下字段:

	- 被返回的对象和链接所描述的对象之间的关系。在这种情况下，"自我"指示链接是回对象本身的引用 (类似于 `this` 许多面向对象语言中的指针)，和"订单"是一个集合，包含相关的订单信息的名称。

	- 超链接 (`HRef`) 对于被链接的 URI 形式所描述的对象。

	- 类型的 HTTP 请求 (`Action`)，可以被发送到此 URI。

	- 任何数据 (格式`LinkedResourceMIMETypes`)，应提供在 HTTP 请求或，可以将响应中返回，根据请求的类型。

	HTTP 响应示例中所示的 HATEOAS 链接指示客户端应用程序可以执行以下操作:

	- HTTP GET 请求的 uri _http://adventure-works.com/customers/2_ 获取客户 (再次) 的详细信息。可以为 XML 或 JSON 返回的数据。

	- HTTP 请求的 uri _http://adventure-works.com/customers/2_ 要修改的客户详细信息。新的数据必须在请求消息中 x-www-窗体-urlencoded 格式提供。

	- 删除 HTTP 请求的 uri _http://adventure-works.com/customers/2_ 若要删除客户。请求不期望任何额外的信息或不返回响应消息正文中的数据。

	- HTTP GET 请求的 uri _http://adventure-works.com/customers/2/orders_ 查找客户的所有订单。可以为 XML 或 JSON 返回的数据。

	- HTTP 请求的 uri _http://adventure-works.com/customers/2/orders_ 若要创建此客户的新订单。必须在 x-www-窗体-urlencoded 格式的请求消息中提供的数据。

## 处理异常时的注意事项
默认情况下，在 ASP.NET Web API 框架，如果操作引发未捕获的异常框架返回 HTTP 状态代码 500 (内部服务器错误) 的响应消息。在许多情况下，这种简单的方法不是孤立的有用和使确定异常困难的原因。因此你应该采取更全面的方法对处理异常，考虑以下各点:

- **捕获异常和有意义将响应返回到客户端**.

	The code that implements an HTTP operation should provide comprehensive exception handling rather than letting uncaught exceptions propagate to the Web API framework. If an exception makes it impossible to complete the operation successfully, the exception can be passed back in the response message, but it should include a meaningful description of the error that caused the exception. The exception should also include the appropriate HTTP status code rather than simply returning status code 500 for every situation. For example, if a user request causes a database update that violates a constraint (such as attempting to delete a customer that has outstanding orders), you should return status code 409 (Conflict) and a message body indicating the reason for the conflict. If some other condition renders the request unachievable, you can return status code 400 (Bad Request). You can find a full list of HTTP status codes on the [状态代码定义](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 在 W3C 网站上的页面。

	下面的代码演示一个示例，陷阱不同条件并返回适当的响应。

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

	> [AZURE。小贴士] 不包括对攻击者试图穿透你的 web API 可能有用的信息。有关进一步信息，请访问 [异常处理在 ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) 在 Microsoft 网站上的页面。

	> [AZURE。注意] 许多 web 服务器陷阱错误条件本身，在他们到达 web API 之前。例如，如果您配置一个 web 站点的身份验证和用户无法提供正确的身份验证信息，web 服务器应该响应状态代码 401 (未经授权)。一旦客户端已经过身份验证，您的代码可以执行自己的检查，以验证客户端应能够访问所请求的资源。如果这种授权失败，您应该返回状态代码 403 (禁止)。

- **在有关错误的一致方式和日志信息中处理异常**.

	以一致的方式处理异常，请考虑实现一个全局错误处理策略在整个网络 API。您可以通过创建运行每当控制器扔不是任何未处理的异常的异常筛选器实现的这一部分 `HttpResponseException` 异常。这种方法描述 [异常处理在 ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) 在 Microsoft 网站上的页面。

	然而，有几种情况下，在异常筛选器不会捕获异常，包括:

	- 从控制器的构造函数引发的异常。

	- 从消息处理程序引发的异常。

	- 在路由过程中引发的异常。

	- 序列化的响应消息的内容时引发的异常。

	若要处理这种情况下，你可能需要执行更多自定义的方法。你还应包含错误日志记录可捕获详细介绍每个异常;此错误日志可以包含详细的信息，只要它不是可通过 web 向客户端。这篇文章 [Web API 全局错误处理](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) 在微软网站显示表演这一任务的方式之一。

- **区分错误客户端和服务器端错误**.

	HTTP 协议 (HTTP 4xx 状态代码)，客户端应用程序所发生的错误和造成的事故 (HTTP 5xx 状态代码) 服务器上的错误进行了区分。请确保你尊重本公约中任何错误响应消息。

<a name="considerations-for-optimizing"></a>
## 优化客户端数据访问的注意事项

在分布式环境等，涉及 web 服务器和客户端应用程序中，关注的主要来源之一就是网络。这可以作为一个很大的瓶颈，尤其是如果一个客户端应用程序频繁地发送请求或接收数据。因此你应该旨在尽量减少流经网络的通信量。在实现检索和维护数据的代码时，请考虑以下几点:

- **支持客户端缓存**.

	HTTP 1.1 协议支持缓存在客户端和中间服务器通过请求将被路由，利用高速缓存控制标头。当一个客户端应用程序发送的 HTTP GET 请求 web api，反应可以在很久以前包括缓存控制标头，该值指示是否由客户端或中间服务器通过请求被路由，并为如何可以安全地缓存响应的正文中的数据时它应该过期并被认为已经过时。下面的示例演示一个 HTTP GET 请求和相应的响应包含一个缓存控制标头:

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

	在此示例中，缓存控制标头指定返回的数据应该在 600 秒后过期，只适用于单个客户端和不必须存储在其他客户端 (这是使用的共享缓存 _私人_).可以指定缓存控制标头 _公共_ 而不是 _私人_ 在这种情况下的数据可以存储在一个共享的缓存，或者它可以指定 _没有商店_ 在这种情况下的数据必须 **不** 由客户端缓存。下面的代码示例演示如何构建响应消息中的缓存控制标头:

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

	此代码使用的自定义 `IHttpActionResult` 名为的类 `OkResultWithCaching`.此类使控制器来设置高速缓存标头内容:

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

	> [AZURE。注意] 此外定义了 HTTP 协议 _无缓存_ 指令缓存控制标头。而令人困惑的是，该指令并不意味着"不缓存"但相当"重新验证与服务器之前返回它的缓存的信息";数据仍然可以被缓存，但它检查每次它用来确保它是目前仍然。

	高速缓存管理负责的客户端应用程序或中间服务器，但是如果正确执行它可以节省带宽，并且通过删除需要已经被最近的检索的获取数据来提高性能。

	的 _最大年龄_ 高速缓存控制标头中的值是仅指南并不是相应的数据在指定的时间不会改变的保证。Web API 应该设置为合适的值，具体取决于数据的预期波动的最大年龄。在此期限届满后，客户端应该丢弃缓存中的对象。

	> [AZURE。注意] 大多数现代 web 浏览器支持客户端缓存通过将适当的缓存控制标头添加到请求和检查的结果，头如所述。但是，一些较旧的浏览器会缓存返回从 URL 包含查询字符串的值。这通常不是自定义客户端应用程序执行基于议定书 》 在这里讨论自己缓存管理策略的一个问题。
	>
	> 某些较旧的代理表现出相同的行为和可能不缓存请求基于 Url 的查询字符串。这可能是自定义客户端应用程序连接到 web 服务器通过这类代理的一个问题。

- **提供 ETags 优化查询处理**.

	当一个客户端应用程序中检索对象时，响应消息还可以包括 _ETag_ (实体标签)。一个 ETag 是不透明的字符串，该值指示版本的资源;每次一个资源更改 Etag 时也会修改。这个 ETag 应由客户端应用程序缓存作为数据的一部分。下面的代码示例演示如何添加一个 ETag 作为 HTTP GET 请求的响应的一部分。此代码使用 `GetHashCode` 对象方法来生成一个数字值，标识对象 (您可以重写此方法，如有必要并生成您自己使用 MD5 算法的哈希值):

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

	由 web API 张贴的响应邮件看起来像这样:

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	> [AZURE。小贴士] 出于安全方面的考虑，不允许敏感数据或返回经过身份验证的 (HTTPS) 连接要缓存的数据。

	客户端应用程序可以发出后续的 GET 请求来检索相同的资源，在任何时候，如果资源已发生更改 (它有不同的 ETag) 应丢弃缓存的版本和新版本添加到缓存中。如果资源很大，需要大量的带宽传输回客户端，一再的要求取相同的数据可以变得低效。要解决这个问题，HTTP 协议定义优化你应该支持在 web API 的 GET 请求的以下过程:

	- 客户端构造包含如果没有匹配 HTTP 标头中引用的资源的当前缓存版本 ETag 的 GET 请求:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- GET 操作在 web API 获取当前 ETag 为所请求的数据 (在上面的示例订单 2)，并把它比作中如果没有匹配的标头的值。

	- 如果当前 ETag 为所请求的数据匹配所请求提供的 ETag，资源并没有改变和 web API 应该返回 HTTP 响应与空邮件正文和 304 (未修改) 状态代码。

	- 如果当前 ETag 为所请求的数据不匹配所请求提供的 ETag，然后数据已更改，web API 应该在邮件正文和 200 (OK) 状态代码返回 HTTP 响应的新数据。

	- 如果所请求的数据不再存在然后 web API 应该返回 HTTP 响应状态代码为 404 (未找到)。

	- 客户端使用的状态代码来维护缓存。如果数据不发生 (状态代码 304)，然后该对象可以保持缓存，客户端应用程序应该继续使用此对象的版本。如果数据已更改 (状态代码 200)，那么应该丢弃缓存的对象和新插入。如果不再有可用的数据 (状态代码 404) 然后应从缓存中移除该对象。

	> [AZURE。注意] 如果响应标头包含缓存控制标头不存储区，则对象应该总是会从缓存中删除无关的 HTTP 状态代码。

	下面的代码显示 `FindOrderByID` 扩展以支持如果没有匹配标题的方法。请注意，是否省略了如果没有匹配标题，总是检索指定的顺序:

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

	此示例包含一个附加的自定义 `IHttpActionResult` 名为的类 `EmptyResultWithCaching`.此类只是充当周围的包装 `HttpResponseMessage` 对象不包含响应正文:

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

	> [AZURE。小贴士] 在此示例中，数据 ETag 生成通过散列从基础数据源检索的数据。如果 ETag 可以以某种其他方式计算，那么可以进一步优化这个过程，只需要将数据从数据源提取，如果它已经改变了。 这种方法是特别有用的如果数据量很大，或访问数据源会导致明显的延迟 (例如，如果数据源是一个远程的数据库)。

- **使用 Etag 要支持开放式并发**.

	为了启用先前缓存的数据更新，HTTP 协议支持乐观并发策略。如果后获取和缓存的资源，客户端应用程序随后发送放或删除请求更改或删除的资源，它应该包括引用 ETag 的如果匹配标题中。然后，web API 可以使用此信息来确定是否资源已由另一个用户因为它被检索并适当将响应发送回客户端应用程序，如下所示:

	- 客户端构造包含该资源的新细节，如果匹配 HTTP 标头中引用的资源的当前缓存版本 ETag PUT 请求。下面的示例演示更新订单 PUT 请求:

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	ProductID=3&Quantity=5&OrderValue=250
	```

	- PUT 操作在 web API 获取当前 ETag 为所请求的数据 (在上面的示例订单 1)，并把它比作中如果比赛标头的值。

	- 如果当前 ETag 为所请求的数据匹配所请求提供的 ETag，资源并没有改变和 web API 应该执行更新，返回 HTTP 状态代码 204 (没有内容) 的消息，如果它是成功。响应可以包含缓存控制和资源的更新版本 etag。答复应始终包括引用新更新的资源的 URI 位置标头。

	- 如果当前 ETag 为所请求的数据不匹配所请求提供的 ETag，然后数据已被另一用户更改因为它取自和 web API 应该返回 HTTP 响应与空邮件正文和 412 (前提条件失败) 状态代码。

	- 如果要更新的资源不再存在然后 web API 应该返回 HTTP 响应状态代码为 404 (未找到)。

	- 客户端使用的状态代码和响应标头来维护缓存。如果数据已更新 (状态代码 204)，然后该对象可以保持缓存 (只要缓存控制标头不指定无店) 但是应该更新 ETag。如果数据已被另一个用户已更改 (状态代码 412) 或未找到 (状态代码 404)，那么应该丢弃缓存的对象。

	下面的代码示例演示订单控制器 PUT 操作实现:

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

	> [AZURE。小贴士] 如果匹配标题的使用是完全可选的如果它是省略的 web API 将始终尝试更新指定的顺序，可能会盲目地覆盖其他用户所做的更新。若要避免丢失更新问题，总是提供如果匹配标题。

<a name="considerations-for-handling-large"></a>
## 处理大量的请求和响应的注意事项

可能有的场合当一个客户端应用程序需要发出请求，发送或接收可能是几兆字节的数据 (或更大) 的大小。等待传输此数量的数据时，可能导致客户端应用程序停止响应。当您需要处理包含大量数据的请求时，请考虑以下几点:

- **优化的请求和响应涉及大型对象**.

	有些资源可能是大型对象或包括大的领域，如图形的图像或其他类型的二进制数据。Web API 应该支持流，使优化的上传和下载这些资源。

	HTTP 协议提供分块的传输编码机制以流式传输大数据对象返回到客户端。当客户端发送 HTTP GET 请求以获得一个庞大的物体时，web API 可以发送答复，回来在零敲碎打 _大块_ 通过 HTTP 连接。在答复中数据的长度可能不知道最初 (它可能生成)，所以服务器托管 web API 应该发送响应消息与每个指定的传输编码的块: 分块标题，而不是内容长度标头。客户端应用程序可以接收每个块反过来要建立完整的响应。在数据传输完成时该服务器发送回最后的块大小为零。	您可以实现在 ASP.NET Web API 使用分块 `PushStreamContent` 类。

	下面的示例显示了一个响应 HTTP GET 请求的产品图像的操作:

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

	在此示例中， `ConnectBlobToContainer` 在 Azure Blob 存储是一个连接到指定的容器 (不显示名称) 的帮助器方法。 `BlobExists` 是另一个 helper 方法，返回一个布尔值，该值指示 blob 存储容器中是否存在具有指定名称的 blob。

	每个产品都举行 blob 存储自身形象。的 `FileDownloadResult` 类是一个自定义 `IHttpActionResult` 使用的类 `PushStreamContent` 要从适当的 blob 中读取图像数据和异步传输它作为响应消息的内容的对象:

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

	您还可以应用流来上载操作，如果客户端需要发布一个新的资源，其中包括一个大型对象。下面的示例演示为 Post 方法 `ProductImages` 控制器。此方法允许客户端上传一个新的产品形象:

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

	此代码使用另一个自定义 `IHttpActionResult` 名为的类 `FileUploadResult`.此类包含用于异步上载数据的逻辑:

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

	> [AZURE。小贴士] 你可以上传到 web 服务的数据量不受流，和单个请求可以想象可能导致一个巨大的物体，耗费大量的资源。如果在流的过程中，web API 决定中请求的数据量已超过一些可接受的范围，它可以中止操作并返回状态代码 413 (请求实体太大) 的响应消息。

	你可以尽量减少使用 HTTP 压缩在网络上传输的大对象的大小。这种做法有助于减少网络通信量和关联的网络延迟，但需要额外的处理在客户端和服务器托管 web API。例如，一个客户端应用程序，预计接收压缩的数据可以包括接受编码: gzip 已请求标头 (算法还可以指定其他数据压缩)。如果服务器支持压缩应对举行 gzip 格式邮件正文和内容编码中的内容: gzip 已响应标头。

	> [AZURE。小贴士] 您可以将组合编码的压缩与流;压缩数据首先之前流，并指定 gzip 内容编码和分块的传输编码在邮件头中。此外注意到，一些 web 服务器 (如 Internet 信息服务器) 可以配置为自动压缩 HTTP 响应无论 web API 将不压缩的数据。

- **不支持异步操作的客户端执行部分响应**.

	As an alternative to asynchronous streaming, a client application can explicitly request data for large objects in chunks, known as partial responses. The client application sends an HTTP HEAD request to obtain information about the object. If the web API supports partial responses if should respond to the HEAD request with a response message that contains an Accept-Ranges header and a Content-Length header that indicates the total size of the object, but the body of the message should be empty. The client application can use this information to construct a series of GET requests that specify a range of bytes to receive. The web API should return a response message with HTTP status 206 (Partial Content), a Content-Length header that specifies the actual amount of data included in the body of the response message, and a Content-Range header that indicates which part (such as bytes 4000 to 8000) of the object this data represents.

	在 API 设计指导文件中详细介绍了 HTTP HEAD 请求和部分响应。

- **避免在客户端应用程序发送不必要继续状态消息**.

	是要将大量的数据发送到服务器的客户端应用程序可能会首先确定该服务器是否真的愿意接受这一要求。发送数据，在客户端应用程序可以提交 HTTP 请求与一个期望: 100-继续标题，内容长度标头指示的数据，但空消息正文的大小。如果该服务器是愿意处理该请求，它应以指定的 HTTP 状态 100 (继续) 的消息响应。客户端应用程序可以继续执行，然后发送完整的请求在邮件正文中包括的数据。

	如果您通过使用 IIS 承载服务，HTTP.sys 驱动程序会自动检测并处理期望: 100-继续之前将请求传递给 web 应用程序的标题。这意味着你很难看到这些头文件在应用程序代码中，您可以假定 IIS 技术已渗入它认为是不适宜或太大的任何消息。

	如果您正在构建客户端应用程序通过使用.NET Framework 中，然后开机自检和付诸表决的所有邮件将首先寄都发消息与期望: 100-默认情况下继续标头。与服务器端进程是由.NET 框架透明地处理。然而，这一过程结果在每个岗位和付诸表决的请求，2 往返过程导致的服务器，甚至小的请求。如果您的应用程序不发送请求处理大量的数据，您可以禁用此功能，通过使用 `ServicePointManager` 要创建类 `ServicePoint` 客户端应用程序中的对象。A `ServicePoint` 对象处理客户端到服务器的基础确定服务器上的资源的方案和主机 Uri 片段的连接。然后，您可以设置 `Expect100Continue` 属性的 `ServicePoint` 对象为 false。随后的所有员额和付诸表决请求由客户端通过一个 URI，它匹配的方案和主机的碎片 `ServicePoint` 没有预期的情况下将发送对象: 100-继续标头。下面的代码演示如何配置 `ServicePoint` 配置发送到 Uri 的计划的所有请求的对象 `http` 与主机的 `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	您还可以设置静态 `Expect100Continue` 属性的 `ServicePointManager` 类为所有随后创建指定此属性的默认值 `ServicePoint` 对象。有关更多信息，请参见 [ServicePoint 类](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) 在 Microsoft 网站上的页面。

- **支持分页的请求可能会返回大量的对象**.

	如果集合包含大量的资源，向相应的 URI 发出 GET 请求显著承载 web API 影响性能，服务器上的处理，也会造成生成大量的网络流量，从而增加延迟。

	若要处理这种情况下，web API 应该支持启用客户端应用程序来优化请求或获取数据更易于管理，离散块 (或页面) 中的查询字符串。ASP.NET Web API 框架分析查询字符串并分成一系列的参数/值对传递给适当的方法，按照前面所述的路由规则。应实现方法以接受使用相同的名称在查询字符串中指定这些参数。此外，这些参数应该是可选的 (在情况下客户端忽略来自一个请求的查询字符串) 并具有有意义的默认值。下面的代码显示 `GetAllOrders` 中的方法 `Orders` 控制器。此方法检索订单的详细信息。如果此方法不受约束，它可以想象返回大量数据。的 `limit` 和 `offset` 默认情况下，参数意欲以减少数据量较小的子集，在本例中只有第一次 10 的订单:

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

	客户端应用程序可以发出请求来检索 30 订单从偏移量 50 开始使用 URI _http://www.adventure-works.com/api/orders?limit=30&偏移量 = 50_.

	> [AZURE。小贴士] 避免使客户端应用程序可以指定结果中有超过 2000年个字符长的 URI 的查询字符串。很多 web 客户端和服务器不能处理这长的 Uri。

<a name="considerations-for-maintaining-responsiveness"></a>
## 维护响应性、 可扩展性和可用性的注意事项

相同的 web API 可能利用许多客户端应用程序在世界任何地方运行。它是重要的是确保 web API 实施，以确保负载很重，可缩放以支持高度不同的工作量，并保证可用性对于执行关键业务操作的客户端的响应能力。在确定如何满足这些要求时，请考虑以下几点:

- **提供异步支持长时间运行的请求**.

	应无阻塞，提交请求的客户端可能需要很长的时间来处理请求。Web API 可以执行一些初始的检查，为验证请求，启动一个单独的任务去执行的工作，然后返回响应消息与 HTTP 代码 202 (接受)。任务可以运行异步 web API 的一部分作为处理，或者它可以卸载到 Azure WebJob (如果 web API 由 Azure 网站) 或工作者角色 (如果 web API 作为 Azure 的云服务实现的)。

	> [AZURE。注意] 有关使用 WebJobs 与 Azure 网站的详细信息，请访问页面 [使用 WebJobs 在微软 Azure 网站运行后台任务](web-sites-create-web-jobs.md) 在微软的网站。

	Web API 还应提供一种机制，将处理的结果返回到客户端应用程序。你可以达到这目的，提供客户端轮询机制定期查询的应用是否处理完毕，取得结果，或启用 web API 来在操作完成时发送通知。

	您可以通过提供实现一种简单的轮询机制 _轮询_ 作为使用以下方法虚拟资源的 URI:

	1. 客户端应用程序将初始请求发送到 web API。

	2. Web API 在举行表存储或微软 Azure 缓存，在表中存储有关请求的信息并生成此条目的唯一键可能形式的 GUID。

	3. Web API 启动作为一个单独的任务处理。Web API 作为表中记录的任务的状态 _运行_.

	4. Web API 返回响应消息与 HTTP 状态代码 202 (接受) 和表条目的 GUID 在邮件的正文中。

	5. 当任务已经完成时，web API 将结果存储在表中，并设置该任务的状态 _完成_.注意是否任务失败，web API 都可以存储有关失败的信息并将状态设置为 _失败_.

	6. 在任务运行时，客户端可以继续执行它自己的处理。它可以定期发送请求的 uri _/polling/ {guid}_ 在哪里 _{} guid_ GUID 返回 202 响应消息中的 web API。

	7. Web API 在 _/polling {guid}_ URI 查询表中的相应任务的状态，并返回包含此状态 (HTTP 状态代码 200 (OK) 响应消息_运行_, _完成_或 _失败_).如果任务已完成，或失败，响应消息还可以包括处理或失败的原因有关的任何可用的信息的结果。

	如果您希望执行的通知，可用的选项包括:

	- 使用 Azure 通知集线器推异步响应到客户端应用程序。页面 [蔚蓝的通知中心通知用户](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) 在 Microsoft 网站提供进一步细节。

	- 用彗星模型保留在客户端与服务器托管 web API，持续的网络连接和使用此连接到推邮件从服务器向客户端。MSDN 杂志文章 [构建一个简单的彗星在 Microsoft.NET 框架应用程序](https://msdn.microsoft.com/magazine/jj891053.aspx) 介绍了一个示例解决方案。

	- 使用那么 SignalR 将数据推送实时从 web 服务器到客户端在持续的网络连接。作为一个 NuGet 包，那么 SignalR 是可供 ASP.NET web 应用程序。你可以找到更多的信息关于 [ASP.NET 那么 SignalR](http://signalr.net/) 网站。

	> [AZURE。注意] 彗星和那么 SignalR 都利用 web 服务器和客户端应用程序之间的持久网络连接。这会影响可伸缩性，因为大量的客户端可能需要同样大的并发连接数。

- **确保每个请求都无国籍**.

	每个请求应视为原子。应该有一个请求由客户端应用程序和提交相同的客户端的任何后续请求之间没有依赖关系。这种方法帮助可扩展性;web 服务的实例可以部署在多个服务器上。客户端请求可以针对任何这些实例，结果总是应该相同。它还提高了可用性出于类似的原因;如果 web 服务器出故障，请求可以被路由到另一个实例 (通过使用 Azure 流量管理器)，而服务器重新启动，客户端应用程序上没有不良影响。

- **跟踪客户和实施节流，以减低 DOS 攻击的机会**.

	如果特定客户端进行大量的请求在给定的时间内，它可能垄断服务和影响性能的其他客户端。为了减轻这一问题，web API 可以监视从客户端应用程序调用跟踪所有传入请求的 IP 地址或记录每个经过身份验证的访问。您可以使用此信息来限制资源的访问。如果客户端超过定义的限制，web API 可以返回响应消息与状态 503 (服务不可用)，并包括一个重试后标头，指定当客户端可以发送下一个请求没有它而被拒绝。这种策略可以帮助从一组客户机失速系统减少拒绝服务 (DOS) 攻击的机会。

- **仔细管理持久性的 HTTP 连接**.

	HTTP 协议支持他们在哪里，可用的持续 HTTP 连接。HTTP 1.0 规范添加连接: 保持-活着的头，使客户端应用程序向服务器表明它可以使用相同的连接来发送后续请求，而不是打开新的。如果客户端不会重用由宿主定义的期间内连接，该连接将自动关闭。此行为是默认的 HTTP 1.1 所用的 Azure 服务，因此您无需在邮件中包括保持页眉中。

	Keeping a connection open can can help to improve responsiveness by reducing latency and network congestion, but it can be detrimental to scalability by keeping unnecessary connections open for longer than required, limiting the ability of other concurrent clients to connect. It can also affect battery life if the client application is running on a mobile device; if the application only makes occassional requests to the server, maintaining an open connection can cause the battery to drain more quickly. To ensure that a connection is not made persistent with HTTP 1.1, the client can include a Connection:Close header with messages to override the default behavior. Similarly, if a server is handling a very large number of clients it can include a Connection:Close header in response messages which should close the connection and save server resources.

	> [AZURE。注意] 持续的 HTTP 连接是纯粹可选的功能来减少与反复建立一个通信通道相关的网络开销。既不是 web API，也不是客户端应用程序应取决于可用的持久 HTTP 连接。请不要使用持续的 HTTP 连接来执行彗星式通知系统;相反，你应该利用套接字 (或 websocket 如果可用) 在 TCP 层。最后，注意保持标题的有限使用如果代理; 通过与服务器进行通信的客户端应用程序只有与客户端和代理服务器的连接将会持续。

## 发布和管理 web API 时的注意事项

要使 web API 可用于客户端应用程序，web API 必须部署到一个主机环境。这种环境通常是 web 服务器，尽管它可能是一些其他类型的主机进程。在发布 web API 时，应考虑以下几点:

- 所有请求必须通过身份验证和授权，并必须执行适当级别的访问控制。
- 一个商业网络 API 可能会受到各种关于响应时间的质量保证。它是重要的是确保该主机环境是可扩展如果负载随着时间的推移有显著变化。
- 如果可能需要对米请求为盈利目的。
- 它可能需要调节到 web API，交通流量和实施节流为已用尽其配额的特定客户。
- 监管要求可能任务日志记录和审核的所有请求和响应。
- 为确保可用性，它可能需要监控服务器托管 web API 的健康并重新启动它，如果必要。

它是有用能够解耦这些问题从 web API 执行有关的技术问题。为此，请考虑创建 [立面](http://en.wikipedia.org/wiki/Facade_pattern)作为一个单独的进程运行和路线请求到 web API。立面可以提供管理操作和期待验证对 web API 的请求。使用一个门面也可以带来很多的功能优势，包括:

- 作为多个 web Api 集成点。
- 翻译为通过使用不同的技术生成的客户端的通信协议和转变为消息。
- 缓存的请求和响应，减少加载在服务器上承载的 web API。

## 测试 web API 时的注意事项
Web API 应该彻底作为任何其他的软件测试。你应该考虑创建单元测试来验证每个操作的功能，就像使用任何其他类型的应用程序。有关详细信息，请参见页 [使用单元测试验证代码](https://msdn.microsoft.com/library/dd264975.aspx) 在微软的网站。

> [AZURE。注意] 示例 web API 可用本指南包括一个演示如何执行单元测试所选操作的测试项目。

Web API 是天性使然自己额外的要求，以验证它正确运行。你应该特别注意以下几个方面:

- 测试所有的路线，以验证他们调用正确的操作。特别要注意被返回的 HTTP 状态代码 (不允许使用此方法) 405 竟然因为这可以表明路线和可以派遣到这条路线的 HTTP 方法 (GET，邮政，PUT，删除) 之间的不匹配。

	将 HTTP 请求发送到不支持它们，如提交 POST 请求对特定资源的路线 (POST 请求应只发送到资源集合)。在这些情况下，只有有效的响应 _应_ 将状态代码 405 (不允许)。

- 验证所有路线适当保护，并进行适当的身份验证和授权检查。

	> [AZURE。注意] 如用户身份验证安全的一些方面是最有可能的宿主环境，而不是 web API，责任却仍然有必要包括安全测试作为部署过程的一部分。

- 测试执行的每个操作的异常处理和验证适当的和有意义的 HTTP 响应被传递回客户端应用程序。
- 验证请求和响应消息格式良好。例如，如果一个 HTTP POST 请求中包含一个新的资源，在 x-www-窗体-urlencoded 格式的数据，确认相应操作正确解析数据、 创建资源，和返回的响应包含新的资源，包括正确的位置标头的详细信息。
- 在响应消息中验证所有链接和 Uri。例如，HTTP POST 消息应返回新创建的资源的 URI。所有 HATEOAS 链接应该都是有效的。

	> [AZURE。重要] 如果您发布 web API 通过 API 管理服务，这些 Uri 应反映管理服务的 URL 而不是承载 web API 的 web 服务器。

- 确保每个操作返回正确的状态代码的不同组合的输入。例如:
	- 如果查询是成功的它应该返回状态代码 200 (OK)
	- 如果未找到资源，操作应 returs HTTP 状态代码 404 (未找到)。
	- 如果客户端发送一个成功删除资源的请求，状态码应该是 204 (没有内容)。
	- 如果客户端发送一个请求，创建新的资源，状态码应该是 201 (创建)

提防 5xx 范围意外的响应状态代码。主机服务器通常报告这些消息以指示它是不能实现有效的请求。

- 测试不同的请求标头组合，客户端应用程序可以指定并确保 web API 响应消息中返回所期望的信息。

- 测试查询字符串。如果操作可以采取可选参数 (例如，分页请求)，测试了不同的组合和参数的顺序。

- 验证异步操作成功完成。如果 web API 支持流返回大型二进制对象 (如视频或音频) 的请求，请确保客户端请求都不阻止，则数据分流。如果 web API 实现轮询为长时间运行的数据修改操作，验证操作报告其状态正确，因为它们着手。

你还应该创建并运行性能测试，以检查 web API 操作令人满意地在胁迫之下。您可以通过使用 Visual Studio 最终生成 web 性能测试和负载测试项目。有关详细信息，请参见页 [在前一个版本的应用程序上运行性能测试](https://msdn.microsoft.com/library/dn250793.aspx) 在微软的网站。

## 发布和管理 web API 使用 Azure API 管理服务

Azure 提供 [API 管理服务](http://azure.microsoft.com/documentation/services/api-management/) 您可以使用发布和管理 web API。使用此功能，您可以生成行为立面为一个或多个 web Api 服务。服务本身就是一个可伸缩的 web 服务，您可以创建和配置通过使用 Azure 管理门户。您可以使用此服务发布和管理 web API，如下所示:

1. 将 web API 部署到网站、 Azure 的云服务或蔚蓝的虚拟机。

2. 连接到 web API API 管理服务。发送到管理 API 的 URL 的请求映射到 Uri 的 web API。相同的 API 管理服务可以将请求路由到多个 web API。这使您可以聚合到一个单一的管理服务的多个 web Api。同样，我们可以从多个 API 管理服务引用相同的 web API，如果你需要限制或分区到不同的应用程序可用的功能。

	> [AZURE。注意] 在 HATEOAS 链接作为 HTTP GET 请求的响应的一部分生成 Uri 应该参考 API 管理服务和不承载 web API 的 web 服务器的 URL。

3. 对于每个 web API，指定的 HTTP 操作的 web API 公开和操作可能需要的任何可选参数作为输入。您还可以配置是否 API 管理服务应缓存从 web API 来优化重复相同的数据请求对收到的答复。记录每个操作都可以生成的 HTTP 响应的详细信息。此信息用于生成文档的开发人员，所以很重要，它是准确、 完整。

	你也可以定义操作手动使用向导提供的 Azure 管理门户，或者你可以从一个包含 WADL 或大摇大摆的格式定义文件导入它们。

4. 配置 API 管理服务和承载 web API 的 web 服务器之间的通信的安全设置。API 管理服务目前支持基本身份验证和使用证书和 OAuth 2.0 用户授权的相互身份验证。

5. 创建一种产品。产品是出版物的单位;您将添加 web Api，你以前连接到该产品的管理服务。产品发布时，web Api 成为开发人员可用的。

	> [AZURE。注意] 之前发布的产品，您还可以定义用户组可以访问该产品，并将用户添加到这些组。这使您能够控制开发商和应用程序可以使用 web API。如果 web API 是批准，能够访问它之前开发人员必须向发送请求产品管理员。管理员可以授予或拒绝对开发人员的访问。如果情况有变，现有的开发人员也可以被阻止。

6.	为每个 web API 配置策略。政策管理方面是否跨域调用应允许，如何验证客户端，是否 XML 和 JSON 之间进行转换数据格式以透明的方式，是否限制调用从一个给定的 IP 范围，使用配额，以及是否限制通话率。可以在整个产品，为产品中的单个 web API 或 web API 中的各个工序全球应用策略。

你可以找到描述如何执行这些任务的全部细节 [API 管理](http://azure.microsoft.com/services/api-management/) 在 Microsoft 网站上的页面。Azure API 管理服务还提供其自己的 REST 接口，使您可以构建一个自定义的界面，用于简化配置 web API 的过程。有关详细信息，请访问 [蔚蓝的 API 管理其他 API 参考](https://msdn.microsoft.com/library/azure/dn776326.aspx) 在 Microsoft 网站上的页面。

> [AZURE。小贴士] Azure 提供 Azure 流量管理器使您能够执行故障转移和负载平衡，并降低延迟了整个 web 站点承载在不同地理位置的多个实例。你可以使用 Azure 交通管理器 API 管理服务; 结合API 管理服务可以将请求路由到的 web 站点通过 Azure 交通管理器实例。 有关详细信息，请访问 [关于交通经理负载平衡方法](https://msdn.microsoft.com/library/azure/dn339010.aspx) 在 Microsoft 网站上的页面。

> 在此结构中，如果你使用的自定义 DNS 名称为您的 web 站点，您应配置适当的 CNAME 记录为每个 web 站点指向 Azure 流量管理器 web 站点的 DNS 名称。

## 支持开发人员构建客户端应用程序
开发人员构建客户端应用程序通常要求信息有关如何访问 web API 和文件有关的参数、 数据类型、 返回类型和描述的不同请求和响应之间的 web 服务和客户端应用程序的返回代码。

### 记录的 web API 的其他操作
Azure API 管理服务包括一个描述了由 web API 公开的其他操作的开发人员门户。当已发布的产品出现在此门户上。开发人员可以使用此门户网站报名参加访问;然后，管理员可以批准或拒绝该请求。如果获得批准，开发人员，他们被分配订阅键，用来验证从客户端开发的应用程序他们的电话。此密钥必须提供与每个 web API 调用否则将被拒绝。

此门户还提供:

- 产品，列出它公开的操作，所需的参数和不同的反应，可以返回文档。请注意，此信息生成从提供的列表中的节中的步骤 3 中的详细信息 [通过使用微软 Azure API 管理服务发布 web API](#publishing-a-web-API).

- 演示如何调用操作从几种语言，包括 JavaScript、 C#、 Java、 Ruby、 Python 和 PHP 的代码片段。

- 使开发人员能够发送一个 HTTP 请求来在产品测试每个操作和查看结果的开发商的控制台。

- 网页开发人员可以在这里报告的任何问题或发现的问题。

Azure 管理门户使您能够自定义开发人员门户网站来更改的样式和布局，以匹配您的组织的品牌。

### 执行客户端 SDK
Building a client application that invokes REST requests to access a web API requires writing a significant amount of code to construct each request and format it appropriately, send the request to the server hosting the web service, and parse the response to work out whether the request succeeded or failed and extract any data returned. To insulate the client application from these concerns, you can provide an SDK that wraps the REST interface and abstracts these low-level details inside a more functional set of methods. A client application uses these methods, which transparently convert calls into REST requests and then convert the responses back into method return values. This is a common technique that is implemented by many services, including the Azure SDK.

创建客户端 SDK 是相当大的工程，因为它一贯执行并且经过仔细测试。然而，这个过程在很大可机械，和许多供应商提供的工具，可以使许多这些任务自动化。

## 监视 web API

取决于你有如何发布和部署你的 web API，您可以监视 web API 直接，或您可以通过分析通过 API 管理服务传递的通信收集使用率和运行状况的信息。

### 直接监测 web API
如果您已通过使用 ASP.NET Web API 模板 (或 Web API 项目作为在 Azure 的云服务的 Web 角色) 和视觉工作室 2013年实现您的 web API，你可以通过使用 ASP.NET 应用程序的见解来收集可用性、 性能和使用数据。应用程序的见解是一个包，透明地跟踪和记录的请求和响应信息，当 web API 部署到云;一旦该软件包的安装和配置，你不需要修改任何代码在您的 web API 来使用它。当您部署到 Azure 的 web 站点的 web API 时，检查所有交通和收集以下统计信息:

- 服务器的响应时间。

- 服务器请求数和每个请求的详细信息。

- 顶尖慢请求的平均响应时间。

- 任何失败的请求的详细信息。

- 由不同的浏览器和用户代理发起的会话的数量。

- 最频繁查看的网页 (主要用于 web 应用程序，而不是 web Api)。

- 不同的用户角色访问 web API。

在真正的时间从 Azure 管理门户，您可以查看此数据。您还可以创建监视 web API 的健康的 webtests。Web 测试中将定期请求发送到指定的 URI 的 web API 和捕获响应。您可以指定的定义 (如 HTTP 状态代码 200)，成功的响应，如果请求未返回此响应你可以安排要求向管理员发送警报。如果有必要，管理员可以重新启动承载 web API，如果它发生故障的服务器。

的 [应用的见解-开始监视您的应用程序的健康和用法](app-insights-start-monitoring-app-health-usage/) 微软网站的网页上提供更多的信息。

### 监视 web API 通过 API 管理服务

如果您已经通过使用 API 管理服务发布您的 web API，Azure 管理门户上的 API 管理页面包含的仪表板，使您可以查看服务的整体性能。分析页使您能够向下钻取到的产品如何使用的详细信息。此页包含下列选项卡:

- **使用**.此选项卡提供的 API 调用和处理这些调用，随着时间的推移而使用的带宽数量有关的信息。您可以筛选使用由产品、 API 和操作的细节。

- **健康**.此选项卡使您查看 API 请求 (返回的 HTTP 状态代码) 的成果，有效的缓存策略、 API 响应时间和服务响应时间。再次，您可以筛选健康产品、 API 和操作数据。

- **活动**.此选项卡提供呼叫数量的成功，失败叫通话阻塞、 平均响应时间和响应时间为每个产品、 web API 和操作文本的摘要。此页还列出了每个开发人员所作的调用次数。

- **在一瞥**.此选项卡显示的性能数据，包括开发人员负责大部分的 API 调用，和产品、 web Api 和收到这些调用的操作的摘要。

您可以使用此信息来确定是否特定 web API 或操作引起的瓶颈，如果必要的规模的主机环境和添加更多服务器。此外，您还可以确定是否一个或多个应用程序正在使用的资源不成比例的卷和应用适当的策略来设置配额和限制呼叫率。

> [AZURE。注意] 您可以更改已发布的产品，详细信息和所做的更改会立即应用。例如，您可以添加或删除操作从 web API 而不需要你重新发布包含 web API 的产品。

## 相关的模式
- 的 [立面](http://en.wikipedia.org/wiki/Facade_pattern) 模式描述如何提供对 web API 接口。

## 更多的信息
- 页面 [了解 ASP.NET Web API](http://www.asp.net/web-api) 在 Microsoft 网站提供详细的介绍了构建 rest 风格的 web 服务通过使用 Web API。
- 页面 [ASP.NET Web API 中的路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) 微软网站描述如何基于公约的路由作品在 ASP.NET Web API 框架。
- 基于属性的路由的详细信息，请参阅页 [属性在 Web API 2 路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) 在微软的网站。
- 的 [基本教程](http://www.odata.org/getting-started/basic-tutorial/) OData 网站的网页上提供的功能的 OData 协议介绍。
- 的 [ASP.NET Web API OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) 在 Microsoft 网站上的页面包含例子和进一步实施 OData web API 通过使用 ASP.NET 的信息。
- 页面 [介绍 Web API 和 Web API OData 批处理支持](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) 微软网站描述如何通过使用 OData 在 web API 执行批处理操作。
- 这篇文章 [幂等性模式](http://blog.jonathanoliver.com/idempotency-patterns/) Jonathan Oliver 博客上概述的幂等性和它如何与数据管理操作。
- 的 [状态代码定义](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) W3C 网站的网页上包含的 HTTP 状态代码的完整列表和它们的描述。
- 有关处理 HTTP 异常与 ASP.NET Web API 的详细信息，请访问 [异常处理在 ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) 在 Microsoft 网站上的页面。
- 这篇文章 [Web API 全局错误处理](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) 微软网站描述了如何实现一个全局错误处理和日志记录策略的 web API。
- 页面 [使用 WebJobs 在微软 Azure 网站运行后台任务](web-sites-create-web-jobs.md) 在 Microsoft 网站提供了有关使用 WebJobs 来执行后台操作在 Azure 网站信息和示例。
- 页面 [蔚蓝的通知中心通知用户](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) 微软网站显示如何使用 Azure 通知中心推异步响应到客户端应用程序。
- 的 [API 管理](http://azure.microsoft.com/services/api-management/) 在 Microsoft 网站上的页面介绍了如何发布产品，提供控制和保护的 web API 访问。
- 的 [蔚蓝的 API 管理其他 API 参考](https://msdn.microsoft.com/library/azure/dn776326.aspx) 在 Microsoft 网站上的页面介绍了如何使用 API 管理 REST API 来构建自定义管理应用程序。
- 的 [关于交通经理负载平衡方法](https://msdn.microsoft.com/library/azure/dn339010.aspx) 微软网站的网页上总结了如何 Azure 流量管理器可用于负载平衡请求 web API 的网站的多个实例。
- 的 [应用的见解-开始监视您的应用程序的健康和用法](app-insights-start-monitoring-app-health-usage.md) 微软网站的网页上提供有关安装和配置应用程序的见解在 ASP.NET Web API 项目的详细的信息。
- 页面 [使用单元测试验证代码](https://msdn.microsoft.com/library/dd264975.aspx) 在 Microsoft 网站提供了有关创建和管理单元测试通过使用 Visual Studio 的详细的信息。
- 页面 [在前一个版本的应用程序上运行性能测试](https://msdn.microsoft.com/library/dn250793.aspx) 微软网站描述了如何使用 Visual Studio 最终来创建一个 web 性能和负载测试项目。
