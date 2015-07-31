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

# Leitfaden für die Umsetzung von API

![](media/best-practices-api-implementation/pnp-logo.png)


Einige Themen in diesem Handbuch werden diskutiert und möglicherweise in Zukunft ändern. Wir freuen uns über Ihr feedback!

## Übersicht
Eine sorgfältig gestaltete RESTful Web API definiert die Ressourcen, Beziehungen und Navigations-Systeme, die für Client-Anwendungen zugänglich sind. Beim Implementieren und eine Web API bereitstellen, berücksichtigen Sie die physikalischen Anforderungen der Umwelt hosting Web API und den Weg in die Web API anstatt die logische Struktur der Daten erstellt wird. Diese Anleitung setzt auf bewährte Methoden für die Implementierung einer Web-API und veröffentlichen es Client-Anwendungen zur Verfügung stellen. Sicherheitsbedenken werden separat im API Security Guidance Dokument beschrieben. Detaillierte Informationen zur Web API-Entwurf finden Sie in der API-Design-Leitfaden.

## Überlegungen zum Implementieren einer RESTful Web API
Den folgenden Abschnitten wird die bewährte Methode für die Verwendung der ASP.NET Web API-Vorlage eine RESTful Web-API erstellen. Besuchen Sie für detaillierte Informationen zur Verwendung der Web API-Vorlage, die [Erfahren Sie mehr über ASP.NET Web API](http://www.asp.net/web-api) Seite auf der Microsoft-Website.

## Überlegungen zum Implementieren des Routings Anfrage

In einen Dienst, mit dem ASP.NET Web API implementiert, jede Anfrage wird weitergeleitet auf eine Methode in einer _Controller_ Klasse. Das Web-API-Framework bietet zwei primäre Optionen zum Implementieren des Routings; _auf der Grundlage von Übereinkommen_ Weiterleitung und _Attribut-basierten_ Weiterleitung. Beachten Sie die folgenden Punkte, wenn Sie der beste Weg zum Weiterleiten von Anforderungen in Ihrem Web API bestimmen:

- **Verstehen Sie die Einschränkungen und Anforderungen des Übereinkommens-basiertem routing**.

	Standardmäßig verwendet das Web-API-Framework Konvention-basiertem routing. Das Web-API-Framework erstellt eine anfängliche routing-Tabelle, die den folgenden Eintrag enthält:

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	Routen können generisch, bestehend aus wie Literale sein. _API_ und Variablen wie _{Controller}_ und _{Id}_. Konvention-basiertem routing kann einige Elemente der Route optional sein. Das Web-API-Framework bestimmt, welche Methode in der Steuerung durch den Abgleich der HTTP-Methode in der Anforderung an den Anfangsteil der Methodenname in der API und dann nach passenden optionalen Parameter aufrufen. Beispielsweise, wenn ein Controller mit dem Namen _Bestellungen_ enthält die Methoden _GetAllOrders()_ oder _GetOrderByInt (Int Id)_ dann die GET-Anfrage _http://www.Adventure-Works.com/API/Orders/_ wird an die Methode zu richten _GetAlllOrders()_ und die GET-Anfrage _http://www.Adventure-Works.com/API/Orders/99_ werden weitergeleitet an die Methode _GetOrderByInt (Int Id)_. Existiert keine entsprechende Methode zur Verfügung, die mit dem Präfix Get im Controller beginnt, antwortet das Web-API-Framework mit Fehlermeldung HTTP 405 (Methode nicht erlaubt). Darüber hinaus muss Name des Parameters (Id) in der Routingtabelle angegeben sein, dasselbe wie der Name des Parameters für die _GetOrderById_ Methode, wird sonst das Web-API-Framework mit einer HTTP 404 (Not Found) Antwort Antworten.

	Es gelten die gleichen Regeln, POST, PUT und DELETE HTTP-Anforderungen; eine PUT-Anforderung, die die Details der Bestellung 101 aktualisiert würde an den URI zu richten _http://www.Adventure-Works.com/API/Orders/101_, der Hauptteil der Nachricht wird die neuen Einzelheiten der Bestellung enthalten, und diese Information wird als Parameter übergeben werden, auf eine Methode in der Bestellungen-Controller mit einem Namen, die mit dem Präfix beginnt _Setzen_, wie z.B. _PutOrder_.

	Die Standard-routing-Tabelle entsprechen keine Anforderung, die untergeordneten Ressourcen in einem RESTful Web-API, wie z. B. verweist _http://www.Adventure-Works.com/API/Customers/1/Orders_ (finden Sie die Informationen über alle Bestellungen Kunden 1). Um diese Fälle zu behandeln, können Sie benutzerdefinierte Routen in der Routingtabelle hinzufügen:

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	Diese Route leitet Anforderungen, die den URI zu entsprechen den _GetOrdersForCustomer_ Methode in der _Kunden, die_ Controller. Diese Methode muss einen einzelnen Parameter mit dem Namen nehmen. _Zuchtstätte_:

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

	> [AZURBLAU. TIPP] Nutzen Sie Standardrouting möglichst zu und zu vermeiden Sie, definieren viele komplizierte Custom leitet wie dies Sprödigkeit führen kann (es ist sehr einfach, einen Controller, der mehrdeutigen Routen führen Methoden hinzufügen) und Leistung (je größer die routing-Tabelle, die weitere Arbeit, die das Web-API-Framework hat zu tun, um herauszufinden welche Route mit eine angegebene URI übereinstimmt) reduziert. Halten Sie die API und Routen einfach. Weitere Informationen finden Sie im Abschnitt Web-API um Ressourcen in der API-Design-Führung zu organisieren. Wenn Sie benutzerdefinierte Routen definieren müssen, wird ein vorzuziehen attributbasierten weiter unten in diesem Abschnitt beschriebene routing verwenden.

	Weitere Informationen zum Übereinkommen basierende routing finden Sie auf der Seite [Weiterleitung in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) auf der Microsoft-Website.

- **Vermeiden von Mehrdeutigkeiten beim routing**.

	Konvention-basiertem routing kann mehrdeutige Wege führen, wenn mehrere Methoden in einem Domänencontroller dieselbe Route übereinstimmen. In diesen Situationen reagiert das Web-API-Framework mit einem HTTP 500 (Internal Server Error)-Response-Nachricht mit dem Text "mehrere Aktionen wurden gefunden, die die Anforderung entsprechen".

- **Attribut-basiertem routing bevorzugen**.

	Attribut-basiertem routing bietet eine Alternative für Methoden in einem Controller Routen connectin bedeutet. Anstatt auf den Mustervergleich Features des Übereinkommens-basiertem routing können Sie explizit Methoden in einem Controller mit den Details der Route versehen, die sie zugeordnet sein sollten. Dieser Ansatz-Hilfe um mögliche Unklarheiten zu entfernen. Darüber hinaus wie explizite Routen zur Entwurfszeit definiert sind, ist diese Vorgehensweise effizienter als Konvention-basiertem routing zur Laufzeit. Der folgende Code veranschaulicht das Anwenden der _Strecke_ Methoden in der Kunden-Controller zuschreiben. Diese Methoden verwenden auch das HttpGet-Attribut an, dass sie, um reagieren sollten _HTTP-GET_ Zugriffe. Dieses Attribut ermöglicht Ihnen, Ihre Methoden, die mit jeder bequem Namensschema statt, erwartet vom Konvent-basierte routing zu nennen. Sie können auch versehen mit Methoden der _HttpPost_, _HttpPut_, und _HttpDelete_ Attribute und Methoden definieren, die auf andere Arten von HTTP-Anforderungen reagieren.

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

	Attribut-basiertem routing auch hat den nützlichen Nebeneffekt handeln als Dokumentation für Entwickler, die den Code in der Zukunft pflegen müssen; Es ist sofort klar, welchen Weg die Methode gehört und die _HttpGet_ Attribut klärt den Typ der HTTP-Anforderung, zu dem die Methode antwortet.

	Attribut-basierte routing können Sie Einschränkungen definieren, die einschränken, wie die Parameter abgeglichen werden. Einschränkungen können den Typ des Parameters angeben, und in einigen Fällen können sie auch den zulässigen Wertebereich Parameter angeben. Im folgenden Beispiel der Id-Parameter für die _FindCustomerByID_ Methode muss eine nicht Negative Ganzzahl sein. Wenn eine Anwendung eine HTTP GET-Anforderung mit einer negativen Kundennummer übermittelt, antwortet das Web-API-Framework mit Fehlermeldung HTTP 405 (Methode nicht erlaubt):

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

	Weitere Informationen zum Attribut-basierte routing finden Sie auf der Seite [Attribut-Routing auf Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) auf der Microsoft-Website.

- **Unterstützung von Unicode-Zeichen in Routen**.

	Die Schlüssel verwendet, um Ressourcen in GET-Anforderungen identifizieren könnte Zeichenfolgen. Daher müssen Sie in einer globalen Anwendung unterstützen URIs, die nicht-englischen Zeichen enthalten.

- **Methoden, die nicht geroutet werden soll zu unterscheiden**.

	Wenn Sie Übereinkommen-basiertem routing verwenden, geben Sie Methoden, die nicht auf HTTP-Aktionen entsprechen, durch dekorieren sie mit den _Nichthandeln_ Attribut. Dies gilt normalerweise für Hilfsmethoden für die Verwendung definiert durch andere Methoden innerhalb eines Controllers, und dieses Attribut verhindert werden diese Methoden wird abgestimmt und durch eine fehlerhafte HTTP-Anforderung aufgerufen.

- **Betrachten Sie die Vorteile und Nachteile der Einlagerung der API einer subdomain**.

	Standardmäßig organisiert das ASP.NET Web API APIs in der _/ API_ Verzeichnis in einer Domäne, wie _http://www.Adventure-Works.com/API/Orders_. Dieses Verzeichnis befindet sich in derselben Domäne wie andere Dienstleistungen, die von demselben Host verfügbar gemacht werden. Es kann von Vorteil im Web API heraus in eine eigene Sub-Domain läuft auf einem separaten Host mit URIs wie aufteilen _http://API.Adventure-Works.com/Orders_. Diese Trennung ermöglicht es Ihnen, partitionieren und effektiver im Web API zu skalieren, ohne Auswirkungen auf andere Webanwendungen oder Diensten den _www.Adventure-Works.com_ Domäne.

	Platzieren eine Web-API in eine andere Subdomain kann jedoch zu Sicherheitsbedenken auch führen. Alle Webanwendungen oder Dienste gehostet bei _www.Adventure-Works.com_ das Aufrufen eine Webdienst-API ausgeführt, dass andernorts die same-Origin-Policy von vielen Webbrowsern verletzen kann. In diesem Fall werden notwendig, um Kreuz-Herkunft Ressourcenfreigabe (COR) zwischen den Hosts ermöglichen. Weitere Informationen finden Sie im Sicherheitsleitfaden für API-Dokument.

## Überlegungen zum Verarbeiten von Anforderungen

Sobald eine Anforderung von einem Client-Anwendung auf eine Methode in eine Web-API erfolgreich weitergeleitet wurde, muss so effizient wie möglich in die Anforderung verarbeitet werden. Beachten Sie die folgenden Punkte, wenn Sie den Code-Anforderungen implementieren:

- **Zu erhalten, setzen, löschen, Kopf und PATCH-Aktionen sollte Idempotent**.

	Der Code, der diese Anforderungen implementiert sollten keine Nebenwirkungen nicht aufzwingen. Die gleiche Anforderung wiederholt über dieselbe Ressource sollte im gleichen Zustand führen. Beispielsweise senden mehrere DELETE-Anforderungen an den gleichen URI sollte haben den gleichen Effekt, allerdings kann der HTTP-Statuscode in der Antwortnachrichten unterschiedlich sein (die erste DELETE-Anforderung möglicherweise zurück Statuscode 204 (No Content) während eines nachfolgenden Löschanfrage Statis code 404 (Not Found) zurückgeben könnte).

> [AZURBLAU. HINWEIS] Der Artikel [Idempotency Muster](http://blog.jonathanoliver.com/idempotency-patterns/) auf Jonathan Oliver Blog Übersicht über Idempotency und wie sie auf Datenverwaltungsoperationen bezieht.

- **POST-Aktionen, die neue Ressourcen erstellen sollten unabhängige Nebenwirkungen tun, ohne**.

	Wenn eine POST-Anforderung eine neue Ressource erstellen soll, die Auswirkungen der Anforderung auf die neue Ressource beschränkt bleiben sollte (und möglicherweise alle direkt Ressourcen zugehörigen wenn es irgendeine Art von Bindung beteiligten) z. B. in einem e-Commerce-System, eine POST-Anforderung, die einen neuen Auftrag für einen Kunden erstellt möglicherweise auch ändern Lagerbestände und Rechnungsinformationen zu generieren , aber es sollte nicht Informationen, die nicht unmittelbar auf die Reihenfolge ändern oder haben Sie irgendwelche anderen Nebenwirkungen auf den allgemeinen Zustand des Systems.

- **Vermeiden Sie Implementierung "geschwätzigen" POST, PUT und DELETE-Operationen**.

	Unterstützen, POST, PUT und DELETE-Anforderungen über den Ressource-Sammlungen. Eine POST-Anforderung enthalten die Angaben für mehrere neue Ressourcen und sie alle in die gleiche Sammlung hinzufügen kann, eine PUT-Anforderung kann den gesamten Satz von Ressourcen in einer Auflistung ersetzen und eine Löschanfrage kann eine ganze Sammlung entfernen.

	Beachten Sie, dass die promoveaza Unterstützung in ASP.NET Web API 2 enthalten die Möglichkeit Batchanforderungen bietet. Eine Client-Anwendung kann mehrere Webanforderungen API-Paket und in einen einzelnen HTTP-Anforderung an den Server zu senden und erhalten eine einzelne HTTP-Antwort, die die Antworten auf jede Anforderung enthält. Weitere Informationen finden Sie auf der Seite [Die Batch-Unterstützung in Web-API und Web API promoveaza](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) auf der Microsoft-Website.

- **Halten Sie sich an das HTTP-Protokoll beim Senden einer Antwort zurück an die Clientanwendung**.

	Eine Web-API muss Meldungen zurück, die enthalten den richtigen HTTP-Statuscode zum Aktivieren des Clients feststellen, wie das Ergebnis der entsprechenden HTTP-Header verarbeiten, so dass der Client die Natur das Ergebnis und eine entsprechend formatierte Körper aktivieren Sie den Client an, das Ergebnis zu analysieren versteht. Wenn Sie die ASP.NET Web API-Vorlage verwenden, ist die Standardstrategie zur Umsetzung der Methoden, die auf HTTP POST-Anforderungen reagieren einfach eine Kopie von der neu erstellten Ressource zurückgegeben wie im folgenden Beispiel dargestellt:

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

	Wenn der POST-Vorgang erfolgreich ist, erstellt das Web-API-Framework eine HTTP-Antwort mit dem Statuscode 200 (OK) und die Details des Kunden als Nachrichtentext. Jedoch in diesem Fall gemäß dem HTTP-Protokoll eine POST-Operation sollte zurück Statuscode 201 (hergestellt) und die Response-Nachricht sollte den URI der neu erstellten Ressource im Location-Header der Antwort enthalten.

	Zurück, um diese Features zu bieten, eigene HTTP-Response-Nachricht mithilfe der `IHttpActionResult` Schnittstelle. Dieser Ansatz gibt Ihnen Feinsteuerung HTTP-Statuscodes, die Kopfzeilen in der Antwortnachricht und sogar das Format der Daten in den Hauptteil der Antwort-Nachricht, wie im folgenden Codebeispiel gezeigt. Diese Version von der `CreateNewCustomer` stärker entspricht Methode den Erwartungen der Client nach dem HTTP-Protokoll. Die `Created` Methode der `ApiController` Klasse erstellt die Response-Nachricht aus den angegebenen Daten und die Ergebnisse den Location-Header hinzugefügt:

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

- **Content-Negotiation zu unterstützen**.

	Der Hauptteil einer Antwortnachricht kann Daten in verschiedenen Formaten enthalten. Beispielsweise konnte eine HTTP GET-Anforderung zurück Daten in JSON oder XML-Format. Wenn der Client eine Anforderung sendet, kann es einen Accept-Header enthalten, der die Datenformate angibt, die damit umgehen kann. Diese Formate werden als Medientypen angegeben. Beispielsweise kann ein Client, der eine GET-Anforderung ausgibt, die ein Bild Ruft einen Accept-Header angeben, der die Medientypen, die der Client kann auflistet, z. B. "Image/Jpeg, Image/Gif, Image/Png verarbeiten".  Wenn das Web API als Ergebnis zurückgegeben wird, sollte es formatieren die Daten mithilfe einer der folgenden Medientypen und geben Sie das Format in der Content-Type-Header der Antwort.

	Wenn der Client kein Accept-Header angegeben ist, verwenden Sie eine vernünftige Standardformat für den Antworttext. Für textbasierte Daten standardmaessig ASP.NET Web API Framework beispielsweise JSON.

	> [AZURBLAU. HINWEIS] ASP.NET Web API Framework führt einige automatische Erkennung der Accept-Header und behandelt sie selbst basiert auf dem Typ der Daten in den Körper der Antwortnachricht. Beispielsweise enthält der Hauptteil einer Antwortnachricht ein CLR (CLR)-Objekt, formatiert das ASP.NET Web API automatisch die Antwort als JSON mit dem Content-Type-Header der Antwort auf "Application/Json" festgelegt, es sei denn, der Kunde zeigt, dass es die Ergebnisse als XML erfordert, in dem Fall das ASP.NET Web API Framework formatiert die Antwort im XML-Format und legt den Content-Type Header der Antwort auf "Text/Xml". Allerdings kann es notwendig sein behandeln Accept-Header, die verschiedene Medientypen im Implementierungscode für einen Vorgang explizit angeben.

- **Links zur Unterstützung HATEOAS-Stil Navigation und Entdeckung der Ressourcen bereitgestellt**.

	The API Design Guidance describes how following the HATEOAS approach enables a client to navigate and discover resources from an initial starting point. This is achieved by using links containing URIs; when a client issues an HTTP GET request to obtain a resource, the response should contain URIs that enable a client application to quickly locate any directly related resources. For example, in a web API that supports an e-commerce solution, a customer may have placed many orders. When a client application retrieves the details for a customer, the response should include links that enable the client application to send HTTP GET requests that can retrieve these orders. Additionally, HATEOAS-style links should describe the other operations (POST, PUT, DELETE, and so on) that each linked resource supports together with the corresponding URI to perform each request. This approach is described in more detail in the API Design Guidance document.

	Derzeit gibt es keine Standards, die die Umsetzung der HATEOAS Regeln, aber das folgende Beispiel veranschaulicht ein möglichen Ansatz. In diesem Beispiel gibt eine HTTP GET-Anforderung, die die Details für einen Kunden findet eine Antwort, die HATEOAS Links enthalten, die die Bestellungen für diesen Kunden verweisen:

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

	In diesem Beispiel die Kundendaten ist vertreten durch die `Customer` Klasse im folgenden Codeausschnitt gezeigt. Die HATEOAS Links befinden sich der `Links` Eigenschaft der Auflistung:

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

	Der HTTP-GET-Vorgang ruft die Kundendaten aus Speicher und Konstrukte ein `Customer` Objekt und füllt anschließend die `Links` -Auflistung. Das Ergebnis wird als eine JSON-Response-Nachricht formatiert. Jeder Link umfasst folgende Felder:

	- Die Beziehung zwischen das zurückgegebene Objekt und das Objekt durch den Link beschrieben. In diesem Fall "selbst" bedeutet, dass der Link ein Verweis auf das Objekt selbst zurück ist (ähnlich wie ein `this` Zeiger in vielen objektorientierten Sprachen), und "Bestellungen" ist der Name einer Sammlung, enthält die zugehörige Bestellinformationen.

	- Der Hyperlink (`HRef`) für das Objekt durch den Link in der Form eines URIs beschrieben wird.

	- Die Art der HTTP-Anforderung)`Action`), die an diesen URI gesendet werden können.

	- Das Format der Daten (alle)`LinkedResourceMIMETypes`), die vorzusehen im HTTP-Anforderung oder dass in der Antwort, abhängig von der Art der Anforderung zurückgegeben werden kann.

	Die HATEOAS Links angezeigt, im Beispiel HTTP-Antwort angeben, dass eine Client-Anwendung die folgenden Vorgänge durchführen kann:

	- Eine HTTP GET-Anforderung an den URI _http://Adventure-Works.com/Customers/2_ um die Details des Kunden (wieder) zu holen. Die Daten können als XML oder JSON zurückgegeben werden.

	- Eine HTTP PUT-Anforderung an den URI _http://Adventure-Works.com/Customers/2_ die Details des Kunden zu ändern. Die neuen Daten die Request-Nachricht im Format X-www-form-urlencoded beizufügen.

	- Eine HTTP DELETE-Anforderung an den URI _http://Adventure-Works.com/Customers/2_ um den Kunden zu löschen. Die Anforderung nicht erwarten zusätzliche Unterlagen oder Daten im Hauptteil Antwort-Nachricht zurück.

	- Eine HTTP GET-Anforderung an den URI _http://Adventure-Works.com/Customers/2/Orders_ Alle Aufträge für den Kunden zu finden. Die Daten können als XML oder JSON zurückgegeben werden.

	- Eine HTTP PUT-Anforderung an den URI _http://Adventure-Works.com/Customers/2/Orders_ Erstellen Sie eine neue Bestellung für diesen Kunden. Die Daten sind in die Request-Nachricht im Format X-www-form-urlencoded vorzulegen.

## Überlegungen zum Behandeln von Ausnahmen
Standardmäßig im ASP.NET Web API gibt Rahmen, wenn eine Operation nicht abgefangene Ausnahme Rahmen löst eine Antwortnachricht mit HTTP-Statuscode 500 (Internal Server Error). In vielen Fällen dieser vereinfachenden Ansatz eignet sich nicht isoliert, und die Ursache der Ausnahme schwierig macht. Daher sollten Sie einen umfassenderen Ansatz zur Behandlung von Ausnahmen, erlassen, unter Berücksichtigung der folgenden Punkte:

- **Ausnahmen zu erfassen und eine sinnvolle Antwort an Clients zurück**.

	The code that implements an HTTP operation should provide comprehensive exception handling rather than letting uncaught exceptions propagate to the Web API framework. If an exception makes it impossible to complete the operation successfully, the exception can be passed back in the response message, but it should include a meaningful description of the error that caused the exception. The exception should also include the appropriate HTTP status code rather than simply returning status code 500 for every situation. For example, if a user request causes a database update that violates a constraint (such as attempting to delete a customer that has outstanding orders), you should return status code 409 (Conflict) and a message body indicating the reason for the conflict. If some other condition renders the request unachievable, you can return status code 400 (Bad Request). You can find a full list of HTTP status codes on the [Statuscodes](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) Seite der W3C-Website.

	Der folgende Code zeigt ein Beispiel, fallen unterschiedliche Bedingungen und gibt eine entsprechende Antwort zurück.

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

	> [AZURBLAU. TIPP] Enthalten Sie keine Informationen, die ein Angreifer versucht, Ihre Web-API zu durchdringen nützlich sein könnte. Weitere Informationen finden Sie auf der [Ausnahmebehandlung in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) Seite auf der Microsoft-Website.

	> [AZURBLAU. HINWEIS] Viele Webserver trap Fehlerbedingungen selbst, bevor sie im Web API erreichen. Beispielsweise wenn Sie Konfigurieren der Authentifizierung für eine Website, und der Benutzer nicht die richtigen Authentifizierungsinformationen bereitstellen, sollte der Webserver mit dem Statuscode 401 (nicht autorisiert) reagieren. Sobald ein Client authentifiziert wurde, kann Code ausführen, eine eigene durchführen, um sicherzustellen, dass der Client die angeforderte Ressource zugreifen sollte. Wenn diese Autorisierung fehlschlägt, sollten Sie den Statuscode 403 (Forbidden) zurückgeben.

- **Behandeln von Ausnahmen in eine konsistente Weise und Log-Informationen zu Fehlern**.

	Behandeln von Ausnahmen in konsistenter Weise erwägen Sie eine globale Fehlerbehandlung Strategie über das gesamte Web API zu implementieren. Erreichen Sie durch Erstellen eines Ausnahmefilters, der ausgeführt wird, wenn ein Controller eine nicht behandelte Ausnahme auslöst, die nicht Teil dieser ein `HttpResponseException` Ausnahme. Diese Vorgehensweise wird beschrieben, auf die [Ausnahmebehandlung in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) Seite auf der Microsoft-Website.

	Es gibt jedoch einige Situationen, wo ein Ausnahmefilter keine Ausnahme fängt, einschließlich:

	- Ausnahmen, die von Controller-Konstruktoren.

	- Ausnahmen, die von Message Handlern.

	- Ausnahmen, die während des Routings.

	- Ausnahmen, die während der Serialisierung des Inhalts für eine Antwortnachricht.

	Um diese Fälle zu behandeln, müssen Sie möglicherweise ein mehr angepassten Konzept zu verwirklichen. Sie sollten auch integrieren Fehlerprotokollierung, die alle Details der einzelnen Ausnahme erfasst; Dieses Fehlerprotokoll kann detaillierte Informationen enthalten, solange es nicht über das Web Clients zugänglich ist. Der Artikel [Web API globale Fehlerbehandlung](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) Microsoft zeigt die Webseite eine Möglichkeit, diese Aufgabe.

- **Unterscheiden Sie zwischen clientseitigen und serverseitigen Fehlern**.

	Das HTTP-Protokoll unterscheidet zwischen aufgrund der Clientanwendung (die HTTP-4xx-Statuscodes) auftretenden Fehler und Störungen, die durch ein Missgeschick auf dem Server (die HTTP-5xx-Statuscodes) entstehen. Stellen Sie sicher, dass dieses Übereinkommen in alle Fehlermeldungen zu respektieren.

<a name="considerations-for-optimizing"></a>
## Überlegungen zur Optimierung von clientseitigen Datenzugriff

In einer verteilten Umgebung wie Sie mit einem Web-Server und Client-Anwendungen ist eine der primären Quellen betreffen das Netzwerk. Dies kann als einen beträchtlichen Engpass auftreten, vor allem, wenn eine Clientanwendung ist häufig Anfragen Daten sendet oder empfängt. Daher sollten Sie anstreben, den Datenverkehr zu minimieren, der über das Netzwerk fließt. Beachten Sie die folgenden Punkte, wenn Sie implementieren Sie den Code zum Abrufen und Verwalten von Daten:

- **Unterstützung für das clientseitige Zwischenspeichern**.

	Das HTTP 1.1-Protokoll unterstützt das Zwischenspeichern in Clients und Fortgeschrittene Servern, über die eine Anforderung durch den Einsatz von der Cache-Control Header geroutet wird. Wenn eine Clientanwendung eine HTTP GET anfordern für die Web-API sendet, die Antwort enthalten kann einen Cache-Control-Header, der angibt, ob die Daten in den Hauptteil der Antwort sicher zwischengespeichert werden können, von dem Client oder Zwischenserver, über die die Anfrage weitergeleitet wurden, und für wie lange vorher es ablaufen sollte und veraltet angesehen werden. Das folgende Beispiel zeigt eine HTTP GET-Anforderung und die entsprechende Antwort, in der einen Cache-Control-Header enthalten:

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

	In diesem Beispiel der Cache-Control-Header gibt an, dass die zurückgegebenen Daten nach 600 Sekunden abgelaufen sein sollte und eignet sich nur für einen einzelnen Client und muss nicht in einem gemeinsamen Cache verwendet von anderen Clients (es ist gespeichert werden _Private_). Der Cache-Control-Header konnte angeben _öffentliche_ statt _Private_ in diesem Fall können die Daten in einem gemeinsamen Cache gespeichert werden, oder es könnten angeben _keine-Shop_ in diesem Fall müssen die Daten **nicht** vom Client zwischengespeichert werden. Im folgenden Codebeispiel wird veranschaulicht, wie einen Cache-Control-Header in einer Antwortnachricht zu konstruieren:

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

	Dieser Code nutzt eine benutzerdefinierte `IHttpActionResult` Klasse mit dem Namen `OkResultWithCaching`. Diese Klasse ermöglicht den Controller Cache Headerinhalt festlegen:

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

	> [AZURBLAU. HINWEIS] Auch das HTTP-Protokoll definiert die _keine-cache_ Richtlinie für den Cache-Control-Header. Ziemlich verwirrend, bedeutet diese Richtlinie nicht "nicht zwischenspeichern" aber "erneut überprüfen vielmehr die zwischengespeicherte Informationen mit dem Server, bevor es wieder"; noch die Daten zwischengespeichert werden können, aber jedes Mal ausgecheckt wird verwendet, um sicherzustellen, dass es immer noch aktuell ist.

	Cache-Verwaltung liegt in der Verantwortung der Clientanwendung oder Zwischenserver, aber wenn ordnungsgemäß umgesetzt können sie Bandbreite sparen und verbessern der Leistung durch die Beseitigung der Notwendigkeit auf Abruf-Daten, die vor kurzem bereits abgerufen wurden.

	Die _Max-Alter_ Wert in den Cache-Control Header ist nur ein Leitfaden und keine Garantie, die die entsprechenden Daten in der angegebenen Zeit nicht zu ändern. Das Web API sollte Max-Alter auf einen geeigneten Wert abhängig von der erwarteten Volatilität der Daten festgelegt. Nach Ablauf dieser Zeit sollte der Client das Objekt aus dem Cache verwerfen.

	> [AZURBLAU. HINWEIS] Die meisten moderne Webbrowsern unterstützt clientseitiges Zwischenspeichern, indem du den entsprechenden Cache-Control-Header auf Anfragen und untersuchen die Header der Ergebnisse, wie beschrieben. Einige ältere Browser Zwischenspeichern nicht von einer URL, die enthält eine Abfragezeichenfolge zurückgegebenen Werte. Dies ist nicht normalerweise ein Problem für benutzerdefinierten Clientanwendungen, die eigene Cache-Management-Strategie, basierend auf dem Protokoll, die hier erörterten implementieren.
	>
	> Einige ältere Proxies zeigen das gleiche Verhalten und Anforderungen anhand des URLs Abfragezeichenfolgen nicht zwischengespeichert werden können. Dies könnte ein Problem für benutzerdefinierte Clientanwendungen, die auf einem Webserver über einen solchen Proxy verbinden.

- **Bieten Sie ETags zur Optimierung der Abfrageverarbeitung**.

	Wenn eine Clientanwendung ein Objekt abruft, kann auch die Antwortnachricht enthalten eine _ETag_ (Entity-Tag). Ein ETag ist eine undurchsichtige Zeichenfolge, die die Version einer Ressource angibt; jedes Mal, wenn eine Ressource das Etag ändert wird auch geändert. Diese ETag sollten als Teil der Daten von der Clientanwendung zwischengespeichert werden. Im folgenden Codebeispiel wird veranschaulicht, wie ein ETag als Teil der Antwort auf eine HTTP GET-Anforderung hinzu. Dieser Code verwendet die `GetHashCode` -Methode eines Objekts einen numerischen Wert zu generieren, der identifiziert das Objekt (Sie können diese Methode überschreiben, falls erforderlich und eigene mithilfe eines Algorithmus wie MD5 Hash generieren):

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

	Geschrieben von Web API Response-Nachricht sieht so aus:

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	> [AZURBLAU. TIPP] Erlauben Sie aus Sicherheitsgründen keine vertrauliche Daten oder Daten, die über eine authentifizierte Verbindung (HTTPS) zwischengespeichert werden zurückgegeben.

	Eine Client-Anwendung kann eine nachfolgende GET-Anforderung an dieselbe Ressource jederzeit abrufen ausstellen und wenn die Ressource geändert hat (es hat eine unterschiedliche ETag) die zwischengespeicherte Version verworfen werden sollte und die neue Version zum Cache hinzugefügt. Wenn eine Ressource groß ist und eine erhebliche Menge an Bandbreite erfordert, zurück an den Client zu übertragen, können wiederholte Anfragen an dieselben Daten zu holen ineffizient geworden. Um dies zu bekämpfen, definiert das HTTP-Protokoll den folgenden Prozess zur Optimierung von GET-Anforderungen, die Sie in einem Web API unterstützen soll:

	- Der Client erstellt eine GET-Anforderung, die das ETag für die aktuell zwischengespeicherten Version der Ressource verwiesen in einem If-None-Match-HTTP-Header enthält:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- Der GET-Vorgang im Web API erhält das aktuelle ETag für die angeforderten Daten (Bestellung 2 im obigen Beispiel), und vergleicht ihn mit dem Wert in den Header If-None-Match.

	- Wenn die aktuelle ETag für die angeforderten Daten das ETag, bereitgestellt durch die Anforderung übereinstimmt, die Ressource hat sich nicht geändert und das Web API sollte eine HTTP-Antwort mit einem leeren Nachrichtentext und dem Statuscode 304 (nicht geändert) zurückgeben.

	- Wenn das aktuelle ETag für die angeforderten Daten nicht das ETag, bereitgestellt durch die Anforderung übereinstimmt, dann hat die Daten geändert und im Web API sollte eine HTTP-Antwort mit den neuen Daten in den Nachrichtentext und einen Statuscode 200 (OK) zurückgeben.

	- Wenn die angeforderten Daten nicht mehr vorhanden ist, sollte das Web API eine HTTP-Antwort mit dem Statuscode 404 (Not Found) zurückgeben.

	- Der Client verwendet den Statuscode, um den Zwischenspeicher zu verwalten. Wenn die Daten nicht (Statuscode 304) geändert hat, dann, das Objekt bleiben kann zwischengespeichert und die Client-Anwendung sollte weiterhin diese Version des Objekts verwenden. Wenn die Daten (Statuscode 200) geändert hat, dann das zwischengespeicherte Objekt verworfen sollte und neue eingefügt. Wenn die Daten nicht mehr verfügbar ist (Statuscode 404) und dann das Objekt aus dem Cache entfernt werden soll.

	> [AZURBLAU. HINWEIS] Wenn der Antwort-Header Cache-Control Header Nein-Shop enthält dann das Objekt aus dem Cache unabhängig von der HTTP-Statuscode immer entfernt werden soll.

	Der Code unten zeigt die `FindOrderByID` -Methode erweitert, um den Header If-None-Match zu unterstützen. Beachten Sie, dass der If-None-Match-Header fehlt, wird die angegebene Reihenfolge immer abgerufen:

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

	Dieses Beispiel enthält eine zusätzliche benutzerdefinierte `IHttpActionResult` Klasse mit dem Namen `EmptyResultWithCaching`. Diese Klasse fungiert lediglich als Wrapper um ein `HttpResponseMessage` Objekt, das einen Antworttext nicht enthält:

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

	> [AZURBLAU. TIPP] In diesem Beispiel wird das ETag für die Daten generiert, indem Daten aus der zugrunde liegenden Datenquelle aus. Wenn das ETag in anderer Weise berechnet werden kann, dann der Prozess weiter optimiert werden kann und müssen nur die Daten aus der Datenquelle abgerufen werden, wenn sie sich geändert hat.  Dieser Ansatz ist besonders nützlich, wenn die Daten groß ist oder den Zugriff auf die Datenquelle in bedeutende Latenz führen kann (beispielsweise, wenn die Datenquelle einer Remotedatenbank ist).

- **Verwenden Sie ETags zur Unterstützung der vollständigen Parallelität**.

	Um Updates über zuvor zwischengespeicherte Daten zu ermöglichen, unterstützt das HTTP-Protokoll eine vollständige Parallelität-Strategie. Wenn nach dem Abrufen und Zwischenspeichern einer Ressource, die Clientanwendung anschließend eine PUT oder DELETE Anforderung zum Ändern oder entfernen die Ressource sendet, sollte im If-Match-Header enthalten, die das ETag verweist. Das Web-API können dann diese Informationen bestimmen, ob die Ressource bereits von einem anderen Benutzer geändert wurde, seit es abgerufen wurde und eine angemessene Reaktion an die Clientanwendung wie folgt senden:

	- Der Client erstellt eine PUT-Anforderung mit den neuen Details für die Ressource und das ETag für die aktuell zwischengespeicherten Version der Ressource in einem If-Match-HTTP-Header verwiesen wird. Das folgende Beispiel zeigt eine PUT-Anforderung, die eine Bestellung aktualisiert:

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	ProductID=3&Quantity=5&OrderValue=250
	```

	- Die PUT-Operation im Web API erhält das aktuelle ETag für die angeforderten Daten (Bestellung 1 im obigen Beispiel), und vergleicht ihn mit dem Wert in der If-Match-Header.

	- Wenn die aktuelle ETag für die angeforderten Daten das ETag, bereitgestellt durch die Anforderung übereinstimmt, die Ressource nicht geändert hat, und im Web API sollte Untersuchung erfolgt, eine Nachricht mit HTTP-Statuscode 204 (No Content) zurückgeben, wenn es erfolgreich ist. Die Antwort kann Cache-Control und ETag Header für die aktualisierte Version der Ressource enthalten. Die Antwort sollte immer den Location-Header enthalten, der den URI der aktualisierte Ressource verweist.

	- Wenn das aktuelle ETag für die angeforderten Daten nicht das ETag, bereitgestellt durch die Anforderung übereinstimmt, hat dann die Daten geändert wurden von einem anderen Benutzer da es geholt wurde und im Web API sollte eine HTTP-Antwort mit einem leeren Nachrichtentext und einen Statuscode von 412 (Precondition Failed) zurückgeben.

	- Wenn die Ressource aktualisiert werden nicht mehr existiert, sollte im Web API eine HTTP-Antwort mit dem Statuscode 404 (Not Found) zurückgeben.

	- Der Client verwendet die Status-Code und Response-Header, um den Zwischenspeicher zu verwalten. Wenn die Daten wurden aktualisiert (Statuscode 204), dann das Objekt zwischengespeicherte bleiben kann, (solange der Cache-Control Header keine Nein-Shop angegeben ist), aber das ETag aktualisiert werden soll. Wenn die Daten von einem anderen Benutzer geändert (Statuscode 412) geändert wurden oder nicht gefunden (Statuscode 404), dann das zwischengespeicherte Objekt verworfen werden sollte.

	Im folgenden Codebeispiel wird eine Implementierung der PUT-Operation für die Orders-Controller:

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

	> [AZURBLAU. TIPP] Verwendung des If-Match-Headers ist freiwillig, und wenn es ausgelassen im Web API immer versucht wird, die angegebene Reihenfolge aktualisieren möglicherweise Blind ein Update von einem anderen Benutzer überschreiben. Zur Vermeidung von Problemen durch verlorene Aktualisierungen immer bereit einen If-Match-Header.

<a name="considerations-for-handling-large"></a>
## Überlegungen zum Umgang mit großen Anfragen und Antworten

Möglicherweise gibt es Gelegenheiten, wenn eine Clientanwendung muss Anforderungen ausgeben, das Senden oder empfangen von Daten, die möglicherweise mehrere Megabyte (oder größer) in der Größe. Personen, die während der Übertragung dieser Datenmenge auf bewirken, dass die Client-Anwendung reagiert. Beachten Sie die folgenden Punkte, wenn Sie Anfragen zu behandeln, die große Datenmengen enthalten müssen:

- **Optimierung von Anfragen und Antworten, die große Objekte betreffen**.

	Einige Ressourcen möglicherweise werden große Objekte oder große Felder, z. B. Grafiken oder andere Arten von binären Daten enthalten. Eine Web-API sollte unterstützen streaming um optimierte hoch- und Herunterladen dieser Ressourcen zu aktivieren.

	Das HTTP-Protokoll bietet die segmentierte Übertragung Codierung Mechanismus um große Datenobjekte zurück an einen Client zu streamen. Sendet der Client eine HTTP GET-Anforderung für ein großes Objekt, kann im Web API die Antwort zurück in die schrittweise senden _Brocken_ über eine HTTP-Verbindung. Die Länge der Daten in der Antwort kann zunächst nicht bekannt (es möglicherweise generiert werden), so dass der Server hosting Web-API eine Antwortnachricht mit jedem Stück senden soll, der angibt, die Transfer-Encoding: Chunked Header, anstatt einen Content-Length-Header. Die Client-Anwendung kann jeder Chunk wiederum zum Aufbau von der vollständigen Antwort erhalten. Der Datentransfer abgeschlossen ist, sendet der Server wieder ein letzte Stück Größe 0.	Sie können implementieren, Segmentierung im ASP.NET Web API mithilfe der `PushStreamContent` Klasse.

	Das folgende Beispiel zeigt eine Operation, die auf HTTP GET-Anforderungen für Produktbilder antwortet:

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

	In diesem Beispiel `ConnectBlobToContainer` Azure Blob-Speicher ist eine Hilfsmethode, die mit einem angegebenen Container (Name nicht gezeigt) verbunden wird. `BlobExists` ist ein weiteres Hilfsmethode, die einen booleschen Wert, der anzeigt zurückgibt, ob ein Blob mit dem angegebenen Namen in das Blob-Storage-Container vorhanden ist.

	Jedes Produkt hat sein eigenes Bild in Blob-Speicher statt. Die `FileDownloadResult` Klasse ist eine benutzerdefinierte `IHttpActionResult` Klasse, die verwendet eine `PushStreamContent` Objekt die Bilddaten aus entsprechenden Blob gelesen und asynchron zu übertragen, wie des Inhalts der Antwort:

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

	Sie können auch anwenden, um Vorgänge zu uploaden, wenn ein Client eine neue Ressource buchen, die ein large Object enthält streaming. Das nächste Beispiel zeigt die Post-Methode für die `ProductImages` Controller. Diese Methode ermöglicht dem Client, ein neues Produktbild hochladen:

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

	Dieser Code verwendet eine andere benutzerdefinierte `IHttpActionResult` Klasse mit dem Namen `FileUploadResult`. Diese Klasse enthält die Logik für das Hochladen der Daten asynchron:

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

	> [AZURBLAU. TIPP] Das Volumen der Daten, die Sie, um einen Webdienst hochladen können wird nicht eingeschränkt durch streamen, und eine einzelne Anforderung denkbar massive-Objekt, das beträchtliche Ressourcen verbraucht führen könnte. Wenn während des streaming-Vorgangs im Web API feststellt, dass die Menge der Daten in einer Anforderung einige akzeptablen Grenzen überschritten hat, können den Vorgang abbrechen und eine Response-Nachricht mit dem Statuscode 413 (Request Entity zu groß) zurückgegeben.

	Sie können die Größe der großen Objekte über das Netzwerk mithilfe von HTTP-Komprimierung übertragen minimieren. Dieser Ansatz hilft, um den Netzwerkverkehr und die damit verbundenen Netzwerklatenz, aber auf Kosten der erfordern zusätzliche Verarbeitung auf dem Client und dem Server hosting Web-API zu verringern. Eine Clientanwendung, die erwartet, komprimierte Daten empfangen kann beispielsweise ein Accept-Encoding: Gzip-Anfrage-Header (andere Datenkompression Algorithmen auch angegeben werden können). Wenn der Server Komprimierung unterstützt sollte es reagieren mit dem Inhalt statt im Gzip-Format in den Nachrichtentext und Content-Encoding: Gzip-Antwort-Header.

	> [AZURBLAU. TIPP] Sie können codierte Kompression mit streaming kombinieren; Komprimieren Sie die Daten zuerst bevor es streaming und geben Sie Inhaltskodierung, Gzip und segmentierter Übertragungscodierung im Nachrichten-Header an. Beachten Sie, dass einige Webserver (z. B. Internet Information Server) konfiguriert werden können, um automatisch komprimieren HTTP-Antworten, unabhängig davon, ob das Web API Daten komprimiert.

- **Implementieren Sie teilweise Antworten für Clients, die keine asynchrone Operationen unterstützen**.

	As an alternative to asynchronous streaming, a client application can explicitly request data for large objects in chunks, known as partial responses. The client application sends an HTTP HEAD request to obtain information about the object. If the web API supports partial responses if should respond to the HEAD request with a response message that contains an Accept-Ranges header and a Content-Length header that indicates the total size of the object, but the body of the message should be empty. The client application can use this information to construct a series of GET requests that specify a range of bytes to receive. The web API should return a response message with HTTP status 206 (Partial Content), a Content-Length header that specifies the actual amount of data included in the body of the response message, and a Content-Range header that indicates which part (such as bytes 4000 to 8000) of the object this data represents.

	HTTP-HEAD-Anfragen und partielle Antworten werden in der API-Design-Leitfaden näher beschrieben.

- **Vermeiden Sie unnötige weiter Statusmeldungen in Clientanwendungen senden**.

	Eine Clientanwendung, die eine große Menge von Daten an einen Server senden entscheiden zuerst, ob der Server tatsächlich bereit, die Anfrage zu akzeptieren ist. Vor dem Senden der Daten, kann die Client-Anwendung senden eine HTTP-Anforderung mit einem erwarten: 100-Header, einen Content-Length-Header, der angibt, die Größe der Daten, sondern eine leere Nachricht Körper weiter. Wenn der Server bereit, die Anforderung zu behandeln ist, sollte es mit einer Meldung reagieren, die den HTTP-Status 100 (Fortfahren) angibt. Die Client-Anwendung kann dann fortfahren und die vollständige Anforderung, einschließlich der Daten in den Nachrichtentext senden.

	Wenn Sie einen Dienst mithilfe von IIS hosten, der HTTP.sys-Treiber automatisch erkennt und behandelt erwarten: 100-Header vor der Übergabe der Zugriffe auf Ihre Webanwendung weiter. Dies bedeutet, dass Sie unwahrscheinlich, dass diese Header im Anwendungscode zu sehen, und Sie davon ausgehen können, dass IIS bereits alle Nachrichten gefiltert hat, dass es zu groß oder ungeeignet erachtet.

	Wenn Sie Client-Anwendungen mit dem .NET Framework erstellen, dann alle POST und PUT Nachrichten zuerst Nachrichten mit Expect senden werden: 100-Header standardmäßig weiter. Wie bei der Server-Seite, wird der Prozess transparent von .NET Framework behandelt. Dieser Vorgang führt jedoch jede POST und PUT-Anforderung verursacht 2 Roundtrips zum Server, auch für kleine Anfragen. Wenn Ihre Anwendung keine Anfragen mit großen Datenmengen versendet, können Sie das Feature deaktivieren, mit der `ServicePointManager` Klasse erstellen `ServicePoint` Objekte in der Clientanwendung. A `ServicePoint` -Objekt behandelt die Verbindungen, die der Client an einen Server stellt, auf der Grundlage der Regelung und Host Fragmente von URIs, die Ressourcen auf dem Server zu identifizieren. Sie können dann festlegen, die `Expect100Continue` Eigenschaft der `ServicePoint` Objekts auf False. Alle nachfolgenden POST und PUT Anforderungen vom Client über einen URI, der die Regelung und Host Fragmente der entspricht der `ServicePoint` Objekt ohne Expect versendet werden: 100-Header fortfahren. Der folgende Code veranschaulicht das Konfigurieren einer `ServicePoint` Objekt, das alle URIs mit einer Regelung der gesendeten Anforderungen konfiguriert `http` und eine Vielzahl von `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	Sie können auch die statische festlegen. `Expect100Continue` Eigenschaft der `ServicePointManager` Klasse an der Standardwert dieser Eigenschaft für alle erstellt `ServicePoint` Objekte. Weitere Informationen finden Sie unter der [ServicePoint-Klasse](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) Seite auf der Microsoft-Website.

- **Unterstützen Sie Paginierung, für Anforderungen, die große Anzahl von Objekten zurückgeben kann**.

	Enthält eine Auflistung eine große Anzahl von Ressourcen, könnte eine GET-Anforderung an die entsprechende URI ausgegeben führen zu erheblichen Verarbeitung auf dem Server hostet die Web-API, die die Leistung und generieren eine erhebliche Menge an Netzwerkverkehr, wodurch erhöhte Wartezeit.

	Um diese Fälle zu behandeln, sollte das Web API Abfragezeichenfolgen unterstützen, mit denen die Client-Anwendung optimieren Anfragen oder Daten in handlicher, diskrete Blöcke (oder Seiten) zu holen. ASP.NET Web API Framework analysiert Abfragezeichenfolgen und teilt sie sich in eine Reihe von Parameter-Wert-Paaren, die für die entsprechende Methode übergeben werden nach der routing-Regeln wie oben beschrieben. Die Methode sollte implementiert werden, um diese mit den gleichen Namen, die in der Abfragezeichenfolge angegebenen Parameter akzeptieren. Darüber hinaus sollten diese Parameter optional sein (falls der Client den Query-String aus einer Anforderung weggelassen) und sinnvolle Standardwerte haben. Der Code unten zeigt die `GetAllOrders` Methode in der `Orders` Controller. Diese Methode ruft die Details Bestellungen. Wenn diese Methode zwanglose, konnte es denkbar eine große Menge von Daten zurückgeben. Die `limit` und `offset` Parameter dienen zur Reduzierung der Datenmenge auf eine kleinere Teilmenge, in diesem Fall nur die ersten 10 Bestellungen standardmäßig:

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

	Eine Client-Anwendung kann eine Anforderung zum Abrufen von 30 Bestellungen ab Offset 50 mithilfe des URIs ausstellen. _http://www.Adventure-Works.com/API/Orders?Limit=30&Versatz = 50_.

	> [AZURBLAU. TIPP] Zu vermeiden, Aktivieren von Client-Anwendungen Abfrage-Strings angeben, die in einem URI zu führen, die mehr als 2000 Zeichen lang ist. Viele web-Clients und Servern können nicht verarbeiten URIs, die diese lange sind.

<a name="considerations-for-maintaining-responsiveness"></a>
## Überlegungen für die Aufrechterhaltung der Reaktionsfähigkeit, Skalierbarkeit und Verfügbarkeit

Die gleiche Web-API kann von vielen Clientanwendungen laufen überall in der Welt genutzt werden. Es ist wichtig, um sicherzustellen, dass das Web API implementiert wird, um beizubehalten Reaktionsfähigkeit stark ausgelastet, werden skalierbare, höchst unterschiedliche Arbeitsauslastung zu unterstützen und Verfügbarkeit für Kunden zu garantieren, die geschäftskritische Vorgänge ausführen. Bestimmen, wie Sie diese Anforderungen erfüllen die folgenden Punkte berücksichtigen:

- **Asynchrone Unterstützung für lang andauernde Anfragen**.

	Eine Anforderung, die lange Zeit verarbeiten dauern sollte durchgeführt werden, ohne Blockierung des Clients, der die Anforderung gesendet. Das Web-API kann einige anfängliche überprüfen, überprüfen Sie die Anforderung, einen separaten Task zur Ausführung der Arbeit zu initiieren und dann zurückgeben einer Antwortnachricht mit HTTP-Code 202 (Accepted) durchführen. Die Aufgabe könnte als Teil der Web API asynchron ausführen, Verarbeitung, oder es könnte eine Azure WebJob (wenn im Web API von einer Azure-Website gehostet wird) oder ein Arbeitnehmer Rolle abgeladen, (wenn im Web API als eine Azure Cloud-Service implementiert ist).

	> [AZURBLAU. HINWEIS] Weitere Informationen zur Verwendung von WebJobs mit Azure Website besuchen Sie die Seite [Verwenden Sie WebJobs zum Ausführen von Tasks im Hintergrund in Microsoft Azure-Websites](web-sites-create-web-jobs.md) auf der Microsoft-Website.

	Das Web API soll auch einen Mechanismus, um die Ergebnisse der Verarbeitung an die Clientanwendung zurückgegeben. Sie erreichen dies durch die Bereitstellung einer Abrufmechanismus für Client Anwendungen regelmäßig Abfrage, ob die Verarbeitung abgeschlossen hat und erhalten das Ergebnis, oder aktivieren das Web-API, um eine Benachrichtigung zu senden, wenn der Vorgang abgeschlossen ist.

	Sie können einen einfachen Abrufmechanismus implementieren, indem eine _Polling_ URI, die fungiert als virtuelle Ressource mithilfe des folgenden Ansatzes:

	1. Die Clientanwendung sendet die ursprüngliche Anforderung im Web API.

	2. Das Web API speichert Informationen über die Anforderung in einer Tabelle in Tabelle Speicher oder Microsoft Azure Cache gehalten und einen eindeutigen Schlüssel für diesen Eintrag, möglicherweise in Form einer GUID generiert.

	3. Das Web API initiiert die Verarbeitung als separaten Task. Das Web API zeichnet den Zustand der Aufgabe in der Tabelle als _Ausführen_.

	4. Im Web API gibt eine Antwortnachricht mit HTTP-Statuscode 202 (akzeptiert), und die GUID der Eintrag in der Tabelle in den Textkörper der Nachricht zurück.

	5. Wenn der Vorgang abgeschlossen hat, im Web API speichert die Ergebnisse in der Tabelle, und legt den Zustand des Vorgangs zu _Komplette_. Beachten Sie, dass wenn der Vorgang fehlschlägt, könnte das Web API auch Informationen über den Fehler speichern und setzen Sie den Status auf _Ist fehlgeschlagen_.

	6. Während die Aufgabe ausgeführt wird, kann der Client eine eigene Verarbeitung fortsetzen. Es kann in regelmäßigen Abständen eine Anforderung an den URI senden. _/Polling/ {Guid}_ wo _{Guid}_ die GUID wird in der 202-Antwort-Nachricht von der Web-API zurückgegeben werden.

	7. Die Web-API unter der _1000Hz {Guid}_ URI fragt den Status der entsprechenden Aufgabe in der Tabelle und sendet eine Antwortnachricht mit HTTP-Statuscode 200 (OK), enthält dieses Staates)_Ausführen_, _Komplette_, oder _Ist fehlgeschlagen_). Wenn die Aufgabe abgeschlossen hat oder fehlgeschlagen, kann die Antwortnachricht auch die Ergebnisse der Verarbeitung oder alle verfügbaren Informationen über die Ursache des Fehlers enthalten.

	Bevorzugen Sie Benachrichtigungen zu implementieren, sind die Optionen zur Verfügung:

	- Mithilfe einer Azure-Notification-Hub, um asynchrone Rückmeldungen für Clientanwendungen zu drücken. Die Seite [Himmelblau-Benachrichtigung Hubs benachrichtigen Benutzer](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) auf der Microsoft-Website bietet weitere Informationen.

	- Mit dem Comet-Modell, um eine permanente Netzwerkverbindung zwischen dem Client und dem Server hosting Web-API zu behalten, und über diese Verbindung zu Push-Mitteilungen vom Server zurück an den Client. Die MSDN Magazin-Artikel [Erstellen einer einfachen Comet-Anwendung in das Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) Beschreibt eine Beispiellösung.

	- Verwenden SignalR, um Daten in Echtzeit vom Webserver an den Client über eine permanente Netzwerkverbindung hineindrücken. SignalR steht für ASP.NET Web-Anwendungen als NuGet Paket zur Verfügung. Weitere Informationen finden Sie auf der [ASP.NET SignalR](http://signalr.net/) Webseite.

	> [AZURBLAU. HINWEIS] Kometen und SignalR nutzen sowohl permanente Netzwerkverbindungen zwischen dem Webserver und die Client-Anwendung. Dies kann Skalierbarkeit auswirken, da eine große Anzahl von Clients eine gleich große Anzahl von gleichzeitigen Verbindungen erfordern kann.

- **Sicherzustellen Sie, dass jede Anforderung statusfrei ist**.

	Jede Anforderung sollte atomare betrachtet werden. Es sollte keine Abhängigkeiten zwischen einem Antrag von einer Clientanwendung und alle nachfolgenden Anforderungen vom gleichen Kunden. Diese Vorgehensweise hilft bei der Skalierbarkeit; Instanzen des Web Service können auf eine Anzahl von Servern bereitgestellt werden. Client-Anfragen können auf jeder dieser Instanzen gerichtet werden und die Ergebnisse sollten immer gleich sein. Es verbessert auch die Verfügbarkeit aus einem ähnlichen Grund; Wenn ein Web-Server schlägt fehl, die Anforderungen an eine andere Instanz weitergeleitet werden können (mithilfe von Azure Traffic Manager) den Server zwar ist ohne negative Auswirkungen auf den Client-Anwendungen neu gestartet.

- **Verfolgen von Clients und implementieren Drosselung reduzieren die Möglichkeiten von DOS-Attacken**.

	Wenn ein bestimmter Client eine große Anzahl von Anfragen macht innerhalb eines bestimmten Zeitraums könnte es den Dienst zu monopolisieren und beeinträchtigt die Leistung von anderen Clients. Um dieses Problem zu verringern, kann eine Web-API von Clientanwendungen überwachen, verfolgen die IP-Adresse aller eingehenden Anfragen oder durch Protokollierung jeder authentifizierten Zugriff. Sie können diese Informationen verwenden, Zugriff auf Ressourcen zu begrenzen. Wenn ein Client eine definierte Grenze überschreitet, kann das Web API zurückgeben eine Antwortnachricht mit Status 503 (Service nicht verfügbar) und beinhalten einen Retry-After-Header, der angibt, wann der Client die nächste Anforderung ohne es abgelehnt wird senden kann. Diese Strategie kann helfen, die Chancen für einen Denial Of Service (DOS) Angriff von einer Gruppe von Clients abwürgen des Systems zu reduzieren.

- **Ständige HTTP-Verbindungen sorgfältig verwalten**.

	Das HTTP-Protokoll unterstützt persistente HTTP-Verbindungen, in denen sie zur Verfügung stehen. HTTP 1.0-Initialisierungszeichenfolge hinzugefügt den Verbindung: Keep-Alive-Header, das ermöglicht eine Client-Anwendung, um den Server anzuzeigen, dass es dieselbe Verbindung verwenden kann, um nachfolgende Anforderungen, anstatt neue zu öffnen senden. Die Verbindung wird automatisch geschlossen, wenn der Client die Verbindung innerhalb einer Frist, die vom Host definiert nicht wiederverwendet wird. Dieses Verhalten ist die Standardeinstellung in HTTP 1.1 wie von Azure Services, verwendet, so gibt es keine Notwendigkeit, Keep-Alive-Header in Nachrichten enthalten.

	Keeping a connection open can can help to improve responsiveness by reducing latency and network congestion, but it can be detrimental to scalability by keeping unnecessary connections open for longer than required, limiting the ability of other concurrent clients to connect. It can also affect battery life if the client application is running on a mobile device; if the application only makes occassional requests to the server, maintaining an open connection can cause the battery to drain more quickly. To ensure that a connection is not made persistent with HTTP 1.1, the client can include a Connection:Close header with messages to override the default behavior. Similarly, if a server is handling a very large number of clients it can include a Connection:Close header in response messages which should close the connection and save server resources.

	> [AZURBLAU. HINWEIS] Ständige HTTP-Verbindungen sind ein rein optionales Feature zugeordneten wiederholt Einrichten eines Netzwerk-Overhead reduzieren. Einer persistenten HTTP-Verbindung zur Verfügung stehen sollten weder im Web API als auch die Client-Anwendung abhängig. Verwenden Sie keine ständige HTTP-Verbindungen, Comet-Stil Benachrichtigungssysteme implementieren; Stattdessen sollten Sie nutzen, Sockets (oder Websockets, falls vorhanden) auf der TCP-Schicht. Beachten Sie schließlich, Keep-Alive-Header nur begrenzt von Nutzen sind, wenn eine Clientanwendung, die mit einem Server über einen Proxy kommuniziert; nur die Verbindung mit dem Client und dem Proxy wird persistent sein.

## Überlegungen zum Veröffentlichen und Verwalten von einem Web API

Um eine Web-API für Client-Anwendungen verfügbar zu machen, muss die Web-API zu einer Hostumgebung bereitgestellt werden. Diese Umgebung ist in der Regel einen Webserver, obwohl es vielleicht eine andere Art des Hostprozesses. Wenn Sie eine Web-API zu veröffentlichen, sollten Sie folgende Punkte beachten:

- Alle Anfragen müssen authentifiziert und autorisiert und die entsprechende Ebene der Zugangskontrolle durchgesetzt werden muss.
- Ein kommerzielles Web API möglicherweise unterliegen verschiedenen Qualitätsgarantien über Reaktionszeiten. Es ist wichtig, um sicherzustellen, dass die Hostumgebung skalierbar ist, wenn die Belastung im Laufe der Zeit erheblich variieren kann.
- Wenn möglicherweise erforderlich, Zähler Anträge zwecks Monetarisierung.
- Es möglicherweise notwendig, regulieren den Fluss des Datenverkehrs im Web API, und implementieren Sie Einschränkungen für bestimmte Kunden, die ihre Kontingente erschöpft sind.
- Behördlicher Auflagen könnten Mandat, Protokollierung und Überwachung aller Anfragen und Antworten.
- Um die Verfügbarkeit zu gewährleisten, ist es möglicherweise notwendig, überwachen Sie den Status des Servers mit das Web API und starten Sie ihn neu, falls erforderlich.

Es ist sinnvoll, diese Fragen aus den technischen Problemen, über die Durchführung der Web API zu entkoppeln können. Aus diesem Grund sollten Sie erstellen eine [Fassade](http://en.wikipedia.org/wiki/Facade_pattern), als separater Prozess ausgeführt und die Routen im Web API anfordert. Die Fassade bieten den Leitungsbereich und vorwärts Zugriffe auf das Web API überprüft. Mit einer Fassade bringen auch viele funktionale Vorteile, wie:

- Als ein Integrationspunkt für mehrere Web APIs fungiert.
- Transformieren von Nachrichten und Übersetzen von Kommunikationsprotokollen für Clients mit unterschiedlichen Technologien erstellt.
- Zwischenspeichern-Anforderungen und-Antworten zu reduzieren auf dem Hostserver im Web API geladen werden.

## Überlegungen zum Testen eines Web API
Eine Web-API sind genau wie jedes andere Stück Software zu prüfen. Sie sollten erwägen, Erstellen von Unit-Tests zur Überprüfung der Funktionalität der einzelnen Vorgänge, wie bei jeder anderen Art von Anwendung. Weitere Informationen finden Sie auf der Seite [Überprüfen von Code mithilfe von Komponententests](https://msdn.microsoft.com/library/dd264975.aspx) auf der Microsoft-Website.

> [AZURBLAU. HINWEIS] Im Beispiel Web API zur Verfügung, mit dieser Anleitung enthält ein Testprojekt, das Ausführen von Komponententests über ausgewählte Vorgänge veranschaulicht.

Die Natur eines Web-API bringt eine eigene zusätzlichen Anforderungen zu überprüfen, ob es ordnungsgemäß funktioniert. Sie sollte besonders auf folgende Aspekte achten:

- Testen Sie alle Routen um sicherzustellen, dass sie die richtigen Operationen aufrufen. Achten Sie vor allem zurückgegebenen HTTP-Statuscode 405 (Methode nicht erlaubt) unerwartet, wie dies angeben, kann einen Konflikt zwischen einer Route und die HTTP-Methoden (GET, POST, PUT, DELETE), die an dieser Strecke verteilt werden können.

	Senden von HTTP-Anforderungen zu Strecken, die nicht unterstützen, z. B. eine POST-Anfrage an eine bestimmte Ressource (POST-Anforderungen sollten nur an Ressourcengruppen gesendet). In diesen Fällen die einzige gültige Antwort _sollte_ sein Statuscode 405 (nicht zulässig).

- Überprüfen Sie, ob alle Routen ordnungsgemäß geschützt sind und die entsprechenden Authentifizierung und Autorisierung überprüft werden.

	> [AZURBLAU. HINWEIS] Einige Aspekte der Sicherheit wie die Benutzerauthentifizierung sind am ehesten der Hostumgebung anstatt im Web API zuständig, aber es ist immer noch notwendig, Sicherheitstests als Bestandteil der Bereitstellung-Prozess zu berücksichtigen.

- Testen Sie die Ausnahmebehandlung von jedem Vorgang durchgeführt und überprüfen Sie, ob eine geeignete und sinnvolle HTTP-Antwort an die Clientanwendung zurückgegeben werden.
- Stellen Sie sicher, dass Anforderungs-und Antwortnachrichten wohlgeformt sind. Beispielsweise enthält eine HTTP POST-Anforderung die Daten für eine neue Ressource in X-www-form-urlencoded Format, bestätigen Sie, dass die entsprechende Operation richtig die Daten analysiert, werden die Ressourcen erstellt und gibt eine Antwort enthält die Einzelheiten der neuen Ressource, einschließlich des richtigen Location-Headers zurück.
- Überprüfen Sie alle Links und URIs in Antwortnachrichten. Beispielsweise sollte eine HTTP POST-Nachricht den URI der neu erstellten Ressource zurück. Alle HATEOAS Links sollten gültig sein.

	> [AZURBLAU. WICHTIG] Wenn Sie das Web-API über eine API-Management-Service veröffentlichen, sollte diese URIs die URL von der Verwaltungsdienst und nicht mit dem Webserver hosting Web-API widerspiegeln.

- Sicherstellen Sie, dass jede Operation die richtige Statuscodes für verschiedene Kombinationen von Input zurückgibt. Zum Beispiel:
	- Wenn eine Abfrage erfolgreich ist, muss er Statuscode 200 (OK) zurückgeben.
	- Wenn eine Ressource nicht gefunden wird, sollte die Operation Returs HTTP-Statuscode 404 (Not Found).
	- Wenn der Client eine Anforderung, die eine Ressource erfolgreich löscht sendet, sollte der Statuscode 204 (No Content).
	- Wenn der Client eine Anforderung, die eine neue Ressource erstellt sendet, sollte der Statuscode 201 (Created) sein.

Achten Sie auf unerwartete Antwortstatuscodes im Bereich 5xx. Diese Meldungen werden in der Regel vom Host-Server gemeldet, um anzugeben, dass es eine gültige Anforderung erfüllen konnte.

- Testen Sie die andere Anforderung Header-Kombinationen, die eine Clientanwendung kann angeben und sicherstellen, dass das Web-API die erwartete Informationen in Antwortnachrichten zurückgibt.

- Testen Sie Abfrage-Strings. Wenn eine Operation optionale Parameter (z. B. Paginierung Anfragen) nehmen kann, testen Sie die verschiedenen Kombinationen und die Reihenfolge der Parameter.

- Überprüfen Sie, dass asynchrone Operationen erfolgreich abgeschlossen. Wenn das Web API streaming für Anforderungen, die große binäre Objekte (z.B. Video oder Audio) zurückgeben unterstützt, sicherstellen Sie, dass Clientanforderungen nicht blockiert werden, während die Daten gestreamt werden. Wenn das Web API Abrufintervall für lang andauernde Datenänderungsvorgänge implementiert, überprüfen, dass die Vorgänge ihres Status Bericht richtig, wenn sie den Vorgang fortsetzen.

Sie sollten auch erstellen und Ausführen von Performance-Tests zur Überprüfung, ob das Web API unter Zwang zufrieden stellend funktioniert. Sie können eine Web-Performance und Test-Projekt laden erstellen, mithilfe von Visual Studio Ultimate. Weitere Informationen finden Sie auf der Seite [Performance-Tests auf eine Anwendung vor einer Freigabe ausführen](https://msdn.microsoft.com/library/dn250793.aspx) auf der Microsoft-Website.

## Veröffentlichen und Verwalten von einer Web-API mit der Azure-API-Verwaltungsdienst

Azurblau bietet die [API-Management-Service](http://azure.microsoft.com/documentation/services/api-management/) die Sie verwenden können, zu veröffentlichen und verwalten eine Web-API. Mit dieser Einrichtung, generieren Sie einen Dienst, der eine Fassade für eine oder mehrere Web APIs fungiert. Der Dienst ist selbst ein skalierbare Web-Dienst, den Sie können erstellen und konfigurieren Sie mithilfe der Azure-Verwaltungsportal. Sie können diesen Dienst veröffentlichen und verwalten eine Web-API wie folgt:

1. Bereitstellen Sie das Web API für eine Website, Azure Cloud-Service oder Azure virtuelle Maschine.

2. Verbinden Sie den API-Verwaltungsdienst im Web API. Anfragen an die URL der-API werden URIs im Web API zugeordnet. Der gleiche API-Verwaltungsdienst kann Anfragen an mehrere Web API weiterleiten. Dadurch können Sie mehrere Web-APIs in einen einzigen Management-Dienst zu aggregieren. Ebenso kann die gleiche Web-API von mehr als einem API-Management-Service verwiesen werden, wenn Sie benötigen, zu beschränken oder zu partitionieren, die Funktionalität für unterschiedliche Anwendungen zur Verfügung.

	> [AZURBLAU. HINWEIS] Die URIs in HATEOAS-Links, die als Teil der Antwort für HTTP GET-Anforderungen generiert, sollte die URL der API-Verwaltungsdienst und nicht auf dem Webserver hosting Web-API verweisen.

3. Für jede Web-API, geben Sie die HTTP-Vorgänge, die im Web API zusammen mit optionalen Parameter verfügbar macht, die eine Operation durchführen können als Eingabe. Sie können auch konfigurieren, ob der API-Verwaltungsdienst vom Web API wiederholte Anfragen für die gleichen Daten optimieren empfangene Antwort zwischenspeichern soll. Notieren Sie die Informationen über die HTTP-Antworten, die jeden Vorgang generieren kann. Diese Information dient zum Generieren von Dokumentation für Entwickler, weshalb es wichtig ist, dass es richtig und vollständig sind.

	Sie können entweder Vorgänge manuell mithilfe der Assistenten bereitgestellt durch das Azure Management Portal, oder Sie können sie aus einer Datei mit den Definitionen im WADL oder Prahlerei-Format importieren.

4. Konfigurieren Sie die Sicherheitseinstellungen für die Kommunikation zwischen der API-Verwaltungsdienst und dem Webserver hosting Web-API. Der API-Verwaltungsdienst unterstützt derzeit Standardauthentifizierung und gegenseitige Authentifizierung mit Zertifikaten und OAuth 2.0 Benutzerautorisierung.

5. Erstellen eines Produkts. Ein Produkt ist die Publikation; Sie fügen das Web APIs, die Sie zuvor mit der Verwaltungsdienst des Produkts verbunden. Wenn das Produkt veröffentlicht wird, werden im Web APIs für Entwickler verfügbar.

	> [AZURBLAU. HINWEIS] Vor der Veröffentlichung eines Produktes, können Sie auch Benutzergruppen definieren, die das Produkt zugreifen und diesen Gruppen Benutzer hinzufügen können. Dieses gibt, die Ihnen über den Entwicklern Kontrolle, und Anwendungen, die das Web API nutzen können. Wenn eine Webanwendung für die API vorbehaltlich der Zustimmung ist, muss bevor er darauf zugreifen ein Entwickler eine Anfrage an den Produkt-Administrator senden. Der Administrator erteilen oder Verweigern des Zugriffs für den Entwickler. Vorhandene Entwickler können auch blockiert werden, wenn Umstände ändern.

6.	Konfigurieren von Richtlinien für jedes Web API. Aspekte wie ob domänenübergreifende Aufrufe wie zur Authentifizierung von Clients darf werden, ob zwischen XML und JSON konvertieren transparent, ob Anrufe aus einem bestimmten IP-Bereich beschränkt Datenformate, bestimmen Nutzung Quoten, und ob der Abrufsatz beschränkt. Richtlinien können weltweit über das gesamte Produkt, für eine einzelne Web-API in einem Produkt oder für einzelne Vorgänge in einem Web API angewendet werden.

Finden Sie alle Details beschreiben, wie diese Aufgaben auf die [API-Management](http://azure.microsoft.com/services/api-management/) Seite auf der Microsoft-Website. Der Azure-API-Verwaltungsdienst bietet auch eine eigene REST-Schnittstelle ermöglicht Ihnen eine benutzerdefinierte Schnittstelle für die Vereinfachung der Konfiguration eines Web-API erstellen. Weitere Informationen finden Sie auf der [Himmelblau-API Management REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn776326.aspx) Seite auf der Microsoft-Website.

> [AZURBLAU. TIPP] Azurblau bietet die Azure-Traffic-Manager können Sie implementieren, Failover und Load-balancing und verringern Latenz über mehrere Instanzen einer Website in verschiedenen geografischen Standorten gehostet. Azure Traffic Manager können Sie in Verbindung mit der API-Verwaltungsdienst; der API-Verwaltungsdienst können Anfragen an Instanzen einer Website über Azure Traffic Manager weiterleiten.  Weitere Informationen finden Sie auf der [Über Traffic Manager-Load Balancing-Methoden](https://msdn.microsoft.com/library/azure/dn339010.aspx) Seite auf der Microsoft-Website.

> In dieser Struktur Wenn Sie benutzerdefinierte DNS-Namen für Ihre Web-Sites verwenden, sollten Sie den entsprechenden CNAME-Eintrag für jede Website auf den DNS-Namen der Website Azure Traffic Manager hinzu konfigurieren.

## Unterstützung von Entwicklern, die Clientanwendungen erstellen
Entwickler, die Konstruktion von Client-Anwendungen in der Regel benötigen Informationen zum Zugriff auf die Web-API und Unterlagen über die Parameter, Datentypen, Rückgabetypen und return-Codes, die den unterschiedlichen Anforderungen und Antworten zwischen dem Webdienst und der Client-Anwendung zu beschreiben.

### Die REST-Operationen für eine Web-API Dokumentation
Die Azure-API-Management-Service umfasst ein Entwicklerportal, das die REST-Operationen eine Web-API verfügbar gemachten beschreibt. Wenn ein Produkt veröffentlicht wurde, scheint es auf diesem Portal. Entwickler können dieses Portal für Zugang anmelden; der Administrator kann dann genehmigen oder verweigern die Anforderung. Wenn der Entwickler genehmigt wird, werden sie einen Abonnementschlüssel zugewiesen, die verwendet wird, um von den Clientanwendungen zu authentifizieren, die sie entwickeln. Dieser Schlüssel muss sonst mit jedem Web API-Aufruf bereitgestellt werden, dass es abgelehnt wird.

Dieses Portal bietet sich auch für:

- Dokumentation des Produkts angezeigt, die Vorgänge, die ihm verfügbar, die Parameter erforderlich sind und die unterschiedlichen Reaktionen, die zurückgegeben werden können. Beachten Sie, dass diese Informationen aus den Angaben des in Schritt 3 in der Liste im Abschnitt generiert wird [Veröffentlichen einer Web API mithilfe der Microsoft Azure API-Verwaltungsdienst](#publishing-a-web-API).

- Code-Schnipsel, die zeigen, wie Vorgänge aus mehreren Sprachen, einschließlich JavaScript, c#, Java, Ruby, Python und PHP aufrufen.

- Ein Entwickler-Konsole, kann einen Entwickler senden eine HTTP-Anforderung an jeden Vorgang im Produkt zu testen und die Ergebnisse anzuzeigen.

- Eine Seite, wo der Entwickler irgendwelche Fragen oder Probleme gefunden melden kann.

Das Azure Management-Portal können Sie das Entwicklerportal zum Ändern von Design und Layout entsprechen, das branding Ihres Unternehmens anpassen.

### Implementieren einen Client SDK
Building a client application that invokes REST requests to access a web API requires writing a significant amount of code to construct each request and format it appropriately, send the request to the server hosting the web service, and parse the response to work out whether the request succeeded or failed and extract any data returned. To insulate the client application from these concerns, you can provide an SDK that wraps the REST interface and abstracts these low-level details inside a more functional set of methods. A client application uses these methods, which transparently convert calls into REST requests and then convert the responses back into method return values. This is a common technique that is implemented by many services, including the Azure SDK.

Erstellen einer Client-seitigen SDK ist ein erheblicher Unterfangen wie es konsequent umgesetzt werden und sorgfältig getestet. Jedoch viel dieses Prozesses kann mechanische gemacht werden, und viele Anbieter liefern Tools, die viele dieser Aufgaben automatisieren können.

## Überwachung einer Web API

Je nachdem, wie Sie veröffentlicht haben und Ihre Web-API, die Sie überwachen können bereitgestellt können direkt im Web-API oder Sie sammeln Informationen über Verwendung und Gesundheit analysiert den Datenverkehr, der durch den API-Verwaltungsdienst verläuft.

### Überwachung einer Web API direkt
Wenn Sie Ihre Web-API implementiert haben, mithilfe des ASP.NET Web API Vorlage (entweder als Projekt Web API oder wie ein Webserverrolle in einer Azure Cloud-Service) und Visual Studio-2013, können Sie mithilfe von ASP.NET Anwendung Einblicke Verfügbarkeit, Performance und Nutzungsdaten sammeln. Anwendung-Insights ist ein Paket, die transparent verfolgt und zeichnet Informationen über Anfragen und Antworten, wenn das Web API in der Cloud bereitgestellt wird; Sobald das Paket installiert und konfiguriert ist, müssen Sie nicht Code in Ihre Web-API, um es verwenden zu ändern. Wenn Sie im Web-API in einer Azure-Website bereitstellen, Gesamtverkehr wird untersucht und die folgenden Statistiken werden gesammelt:

- Antwortzeit des Servers.

- Anzahl der Serveranforderungen und die Details der einzelnen Anforderungen.

- Die oberen langsamsten Anfragen in Bezug auf die durchschnittliche Antwortzeit.

- Die Details jeder fehlgeschlagenen Anforderungen.

- Die Anzahl der Sitzungen, die von verschiedenen Browsern und User-Agents initiiert.

- Die meist gesehene häufig Seiten (vor allem nützlich für Web-Anwendungen eher als web-APIs).

- Die verschiedene Benutzerrollen, die Zugriff auf das Web API.

Sie können diese Daten in Echtzeit aus dem Azure Management Portal anzeigen. Sie können auch Webtests erstellen, die die Integrität des Web API überwachen. Ein Webtest sendet eine regelmäßige Anforderung an einem angegebenen URI im Web API und fängt die Antwort. Sie können die Definition einer erfolgreichen Antwort (z. B. HTTP-Statuscode 200) angeben, und wenn die Anforderung nicht diese Antwort erhält Sie arrangieren für eine Warnung an den Administrator gesendet werden. Falls erforderlich, kann der Administrator starten Sie den Server hosting Web API, wenn es versäumt hat.

Die [Anwendung-Insights - starten Sie die Überwachung der Gesundheit und die Nutzung Ihrer app](app-insights-start-monitoring-app-health-usage/) Seite auf der Microsoft-Website enthält weitere Informationen.

### Überwachung einer Web-API durch die API-Management-Service

Wenn Sie Ihre Web-API veröffentlicht haben, mithilfe des API-Verwaltungsdiensts, enthält die API-Verwaltungsseite im Azure Management-Portal ein Dashboard, mit dem Sie die Gesamtleistung des Dienstes anzeigen kann. Die Analytics-Seite können Sie einen Drilldown in die Details wie das Produkt verwendet wird. Diese Seite enthält die folgenden Registerkarten:

- **Verwendung**. Diese Registerkarte enthält Informationen über die Anzahl der API-Aufrufe und die Bandbreite verwendet, um diese Anrufe im Laufe der Zeit zu behandeln. Sie können Filtern Nutzungsdetails von Produkt, API und Betrieb.

- **Gesundheit**. Dieser Registerkarte können Sie anzeigen, das Ergebnis der API-Anfragen (die HTTP-Statuscodes zurückgegeben), die Wirksamkeit der Cachingrichtlinie, die API-Reaktionszeit und die Dienst-Reaktionszeit. Auch hier können Sie die Gesundheitsdaten von Produkt, API und Betrieb filtern.

- **Aktivität**. Diese Registerkarte enthält eine Zusammenfassung der Text über die Zahl der erfolgreichen fehlerhafte Aufrufe aufgerufen, blockierte Anrufe, durchschnittliche Antwortzeit und Reaktionszeiten für jedes Produkt, die Web-API und den Betrieb. Diese Seite listet auch die Anzahl der Aufrufe von jedem Entwickler.

- **Auf einen Blick**. Diese Registerkarte zeigt eine Zusammenfassung der Performance-Daten, einschließlich die Entwickler dafür verantwortlich, die meisten API-Aufrufe und die Produkte, Web APIs und Operationen, die diese Anrufe empfangen.

Sie können diese Informationen verwenden, um festzustellen, ob ein bestimmtes Web API oder Betrieb einen Engpass ist und ggf. skaliert die Hostumgebung und weitere Server hinzugefügt. Sie können auch prüfen, ob eine oder mehrere Anwendungen einen unverhältnismäßigen Umfang der Mittel verwenden und die entsprechenden Richtlinien wenden um Quoten festzulegen und Tarife zu beschränken.

> [AZURBLAU. HINWEIS] Sie können die Details für eine veröffentlichte Produkt ändern und die Änderungen werden sofort übernommen. Beispielsweise können Sie hinzufügen oder entfernen ein Vorgangs aus einer Web-API ohne dass Sie das Produkt erneut veröffentlichen, das das Web API enthält.

## Verwandte Entwurfsmuster
- Die [Fassade](http://en.wikipedia.org/wiki/Facade_pattern) Muster beschreibt eine Schnittstelle zu einer Web API bereitstellen.

## Weitere Informationen
- Die Seite [Erfahren Sie mehr über ASP.NET Web API](http://www.asp.net/web-api) Microsoft bietet die Website eine ausführliche Einführung in das Erstellen von RESTful Webservices mithilfe der Web-API.
- Die Seite [Weiterleitung in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) Microsoft beschreibt Webseite wie Konvention-basierte routing funktioniert in ASP.NET Web API Framework.
- Weitere Informationen zum Attribut-basierte routing finden Sie auf der Seite [Attribut-Routing auf Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) auf der Microsoft-Website.
- Die [Lernprogramm](http://www.odata.org/getting-started/basic-tutorial/) Seite der promoveaza-Website bietet eine Einführung in die Features des promoveaza-Protokolls.
- Die [ASP.NET Web API promoveaza](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) Seite auf der Microsoft-Website enthält Beispiele und weitere Informationen zum Implementieren einer promoveaza Web API mit ASP.NET.
- Die Seite [Die Batch-Unterstützung in Web-API und Web API promoveaza](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) auf der Microsoft beschreibt Webseite, wie Batch-Operationen in einem Web API implementieren, mithilfe von promoveaza.
- Der Artikel [Idempotency Muster](http://blog.jonathanoliver.com/idempotency-patterns/) auf Jonathan Oliver Blog Übersicht über Idempotency und wie sie auf Datenverwaltungsoperationen bezieht.
- Die [Statuscodes](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) Seite auf der W3C-Website enthält eine vollständige Liste der HTTP-Statuscodes und ihre Beschreibungen.
- Ausführliche Informationen zur Behandlung von HTTP-Ausnahmen mit dem ASP.NET Web API, besuchen Sie die [Ausnahmebehandlung in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) Seite auf der Microsoft-Website.
- Der Artikel [Web API globale Fehlerbehandlung](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) auf der Microsoft beschreibt Webseite einen globalen Fehler Fehlerbehandlung und Protokollierung der Strategie für eine Web-API implementiert.
- Die Seite [Verwenden Sie WebJobs zum Ausführen von Tasks im Hintergrund in Microsoft Azure-Websites](web-sites-create-web-jobs.md) Microsoft bietet die Website Informationen und Beispiele zur Verwendung von WebJobs, Hintergrund für eine Azure Website durchzuführen.
- Die Seite [Himmelblau-Benachrichtigung Hubs benachrichtigen Benutzer](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) auf der Microsoft veranschaulicht Webseite die Verwendung einer Azure-Notification-Hub, um asynchrone Rückmeldungen für Clientanwendungen zu drücken.
- Die [API-Management](http://azure.microsoft.com/services/api-management/) Seite auf der Microsoft-Website beschreibt veröffentlichen ein Produkt, das bietet kontrollierten und sicheren Zugriff auf eine Web-API.
- Die [Himmelblau-API Management REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn776326.aspx) Seite auf der Microsoft-Website beschreibt die API-REST-API verwenden, um benutzerdefinierte Anwendungen erstellen.
- Die [Über Traffic Manager-Load Balancing-Methoden](https://msdn.microsoft.com/library/azure/dn339010.aspx) Seite auf der Microsoft-Website fasst zusammen, wie Azure Traffic Manager zum Lastenausgleich Anfragen über mehrere Instanzen einer Website hosting Web-API verwendet werden kann.
- Die [Anwendung-Insights - starten Sie die Überwachung der Gesundheit und die Nutzung Ihrer app](app-insights-start-monitoring-app-health-usage.md) Seite auf der Microsoft-Website enthält detaillierte Informationen zur Installation und Konfiguration der Anwendung Einblicke in einem Projekt auf ASP.NET Web API.
- Die Seite [Überprüfen von Code mithilfe von Komponententests](https://msdn.microsoft.com/library/dd264975.aspx) Microsoft bietet die Website ausführliche Informationen zum Erstellen und Verwalten von Komponententests mithilfe von Visual Studio.
- Die Seite [Performance-Tests auf eine Anwendung vor einer Freigabe ausführen](https://msdn.microsoft.com/library/dn250793.aspx) auf der Microsoft beschreibt Webseite mithilfe von Visual Studio Ultimate Erstellen einer Web-Performance und Testprojekt zu laden.
