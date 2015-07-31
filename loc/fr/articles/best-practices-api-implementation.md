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

# Guide d'implémentation API

![](media/best-practices-api-implementation/pnp-logo.png)


Certaines rubriques de ce guide sont en discussion et peuvent changer à l'avenir. Nous apprécions vos commentaires!

## Vue d'ensemble
Un web RESTful API soigneusement conçus définit les ressources, les relations et les plans de navigation qui sont accessibles aux applications clientes. Lorsque vous implémentez et déployez une web API, vous devriez considérer les exigences physiques de l'environnement d'hébergement web API et la façon dans laquelle le web API est construit plutôt que de la structure logique des données. Ce guide met l'accent sur les meilleures pratiques pour la mise en œuvre d'une API web ainsi que sa publication pour le rendre disponible aux applications clientes. Problèmes de sécurité sont décrits séparément dans le document d'orientation sécurité API. Vous pouvez trouver des informations détaillées sur le web conception d'API dans le document d'orientation de conception API.

## Considérations relatives à la mise en œuvre d'un web RESTful API
Les sections suivantes illustrent les meilleures pratiques pour l'utilisation du modèle ASP.NET Web API pour construire un web RESTful API. Pour plus d'informations sur l'utilisation du modèle de l'API Web, visitez le [En savoir plus sur ASP.NET Web API](http://www.asp.net/web-api) page sur le site Web de Microsoft.

## Considérations relatives à l'application demande routage

Dans un service implémenté à l'aide de l'API de Web ASP.NET, chaque demande est acheminée à une méthode dans un _contrôleur de_ classe. Le cadre de l'API Web offre deux options principales pour la mise en œuvre du routage ; _axée sur la Convention_ routage et _basé sur les attributs_ routage. Tenez compte des points suivants lorsque vous déterminez la meilleure façon d'acheminer les demandes dans votre site web API :

- **Comprendre les limitations et les exigences du routage basé sur la convention**.

	Par défaut, le cadre de l'API Web utilise routage basé sur la convention. Le cadre de l'API Web crée une table de routage initiale qui contient l'entrée suivante :

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	Itinéraires peuvent être génériques, comprenant des littéraux tels que _API_ et variables telles que _contrôleur de {}_ et _{id}_. Routage par convention permet à certains éléments de la route et être facultatif. Le cadre de l'API Web détermine la méthode à appeler dans le contrôleur, en faisant correspondre la méthode HTTP de la demande de la partie initiale de la nom de la méthode de l'API, puis en faisant correspondre les paramètres facultatifs. Par exemple, si un contrôleur nommé _commandes_ contient les méthodes _GetAllOrders()_ ou _GetOrderByInt (int id)_ puis la requête GET _http://www.Adventure-Works.com/API/Orders/_ sera dirigé vers la méthode _GetAlllOrders()_ et la requête GET _http://www.Adventure-Works.com/API/Orders/99_ seront acheminés à la méthode _GetOrderByInt (int id)_. Si il n'y a aucune méthode correspondante disponible qui commence par le préfixe Get dans le contrôleur, le cadre de l'API Web répond avec un message HTTP 405 (méthode non autorisée). En outre, le nom du paramètre (id) spécifié dans la table de routage doit être le même que le nom du paramètre pour le _GetOrderById_ méthode, sinon le framework Web API répondra avec une réponse HTTP 404 (introuvable).

	Les mêmes règles s'appliquent aux demandes POST, PUT et DELETE HTTP ; une demande PUT qui met à jour les détails de commande 101 s'adresserait à l'URI _http://www.Adventure-Works.com/API/Orders/101_, le corps du message doit contenir les nouveaux détails de l'ordre, et cette information sera passée comme un paramètre à une méthode dans le contrôleur de commandes dont le nom commence par le préfixe _Mettre_, tels que _PutOrder_.

	La table de routage par défaut ne correspond pas à une requête qui fait référence à des ressources d'enfant dans un web RESTful API, telles que _http://www.Adventure-Works.com/API/Customers/1/Orders_ (consulter les détails de toutes les commandes passées par le client 1). Pour gérer ces cas, vous pouvez ajouter des itinéraires personnalisés à la table de routage :

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	Cet itinéraire redirige les requêtes qui correspondent à l'URI à la _GetOrdersForCustomer_ méthode dans le _Visiteurs_ contrôleur. Cette méthode doit accepter un seul paramètre nommé _Elevages_:

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

	> [AZURE. ASTUCE] Utiliser le routage par défaut chaque fois que possible et évitez de définir beaucoup compliqué custom routes car cela pourrait provoquer dans la fragilité (il est très facile d'ajouter des méthodes à un contrôleur qui donnent lieu à des parcours ambigus) et réduit les performances (plus la table de routage, le travail plus que le cadre de l'API Web doit faire pour déterminer quel itinéraire correspond à un URI donné). Garder les API et les itinéraires simples. Pour plus d'informations, consultez la section organise les API Web autour de ressources dans le Guide de conception d'API. Si vous devez définir des itinéraires personnalisés, une approche préférable consiste à utiliser le routage basé sur les attributs décrits plus loin dans cette section.

	Pour plus d'informations sur le routage basé sur convention, consultez la page [Routage dans ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sur le site Web de Microsoft.

- **Éviter toute ambiguïté dans le routage**.

	Routage par convention peut entraîner dans les voies de l'ambigu si plusieurs méthodes dans un contrôleur correspondent à la même route. Dans ces situations, le cadre de l'API Web répond avec un message de réponse HTTP 500 (Internal Server Error) contenant le texte « des actions multiples ont été trouvées qui correspondent à la demande ».

- **Préférez le routage basé sur l'attribut**.

	Basé sur les attributs routage fournit qu'un autre moyen pour relier les routes aux méthodes dans un contrôleur. Plutôt que d'utiliser les fonctionnalités de mise en correspondance du routage basé sur la convention, vous pouvez annoter explicitement les méthodes d'un contrôleur avec les détails de la route à laquelle ils doivent être associés. Cette aide d'approche pour lever les ambiguïtés possibles. En outre, itinéraires explicites sont définis au moment du design cette approche est plus efficace que routage basé sur convention lors de l'exécution. Le code suivant montre comment appliquer la _Itinéraire_ attribuent aux méthodes du contrôleur de clients. Ces méthodes utilisent également l'attribut HttpGet pour indiquer qu'ils doivent répondre aux _HTTP GET_ demandes. Cet attribut vous permet de nommer vos méthodes à l'aide de n'importe quel schéma de nommage pratique plutôt que celle attendue par routage basé sur la convention. Vous pouvez également annoter des méthodes avec le _HttpPost_, _HttpPut_, et _HttpDelete_ attributs de définir des méthodes qui répondent à d'autres types de requêtes HTTP.

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

	Basé sur les attributs routage aussi a l'effet secondaire utile d'agir comme documentation pour les développeurs qui ont besoin de conserver le code à l'avenir ; Il est immédiatement évident quelle méthode appartient à quel itinéraire et la _HttpGet_ attribut précise le type de requête HTTP à laquelle répond la méthode.

	Basé sur les attributs de routage permet de définir des contraintes qui limitent la façon dont les paramètres sont mis en correspondance. Contraintes peuvent spécifier le type du paramètre, et dans certains cas, ils peuvent également indiquer la plage acceptable des valeurs de paramètre. Dans l'exemple suivant, le paramètre id à la _FindCustomerByID_ méthode doit être un entier non négatif. Si une application envoie une demande HTTP GET, avec un numéro de client négatif, le cadre de l'API Web répondra par un message HTTP 405 (méthode non autorisée) :

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

	Pour plus d'informations sur le routage basé sur l'attribut, consultez la page [Attribut routage dans Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) sur le site Web de Microsoft.

- **Prend en charge les caractères Unicode dans itinéraires**.

	Les clés utilisées pour identifier des ressources dans les requêtes GET pourraient être des chaînes. Par conséquent, dans une application globale, vous devrez peut-être appuyer URI qui contiennent des caractères non anglais.

- **Distinguer les méthodes qui ne doivent pas être acheminés**.

	Si vous utilisez routage basé sur convention, indiquer les méthodes qui ne correspondent pas aux actions HTTP en décorant avec le _NonAction_ attribut. Cela s'applique généralement aux méthodes d'assistance définis pour être utilisés par d'autres méthodes au sein d'un contrôleur, et cet attribut empêche ces méthodes de mise en correspondance et invoquée par une requête HTTP errante.

- **Examiner les avantages et les inconvénients des choix conceptuels de placer l'API dans un sous-domaine**.

	Par défaut, ASP.NET web API organise des API dans le _/API_ annuaire dans un domaine, tel que _http://www.Adventure-Works.com/API/Orders_. Ce répertoire se trouve dans le même domaine que d'autres services exposés par le même hôte. Il peut être avantageux de diviser le web API dehors dans sa propre sous-domaine s'exécutant sur un hôte distinct, avec URIs telles que _http://API.Adventure-Works.com/Orders_. Cette séparation permet de partitionner et évoluer le web API plus efficacement sans affecter d'autres applications web ou services en cours d'exécution le _www.Adventure-Works.com_ domaine.

	Cependant, les plaçant une API web dans un sous-domaine différent peut aussi conduire à des problèmes de sécurité. Des applications web ou des services hébergés à _www.Adventure-Works.com_ qui appeler une API web exécutant qu'ailleurs peuvent violer la politique de la même origine de nombreux navigateurs web. Dans ce cas, il sera nécessaire permettre le partage (SCRO) entre les hôtes de la Croix-origine ressource. Pour plus d'informations, consultez le document d'orientation sécurité API.

## Considérations pour le traitement des requêtes

Une fois qu'une demande d'une application cliente a été acheminée avec succès à une méthode dans une API web, la demande doit être traitée en tant qu'efficacement que possible. Tenez compte des points suivants lorsque vous implémentez le code pour gérer les requêtes :

- **OBTENIR, mettre, DELETE, HEAD et PATCH les actions devraient être idempotente**.

	Le code qui implémente ces demandes ne devrait pas imposer aucun effet secondaire. La même demande répétée sur la même ressource devrait se traduire dans le même État. Par exemple, envoyer plusieurs requêtes de suppression pour le même URI devrait avoir le même effet, même si le code d'état HTTP dans les messages de réponse peut être différent (la première requête DELETE peut renvoyer le code d'État 204 (sans contenu) alors qu'une demande de suppression ultérieure peut retourner statis code 404 (introuvable)).

> [AZURE. REMARQUE] L'article [Idempotence Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) le blog de Jonathan Oliver donne un aperçu de l'idempotence, et comment il se rapporte aux opérations de gestion de données.

- **Les actions POST qui créent de nouvelles ressources devraient le faire sans effets secondaires non apparentés**.

	Si une demande POST est destinée à créer une nouvelle ressource, les effets de la demande devraient être limitées à la nouvelle ressource (et éventuellement un directement liés ressources s'il y a une sorte de lien impliqués) par exemple, dans un système de commerce électronique, une demande POST qui crée une nouvelle commande pour un client peut également modifier les niveaux des stocks et génèrent des informations de facturation , mais il ne devrait pas modifier les informations non directement liées à la commande ou ont d'autres effets secondaires sur l'état général du système.

- **Éviter de mettre en œuvre des opérations POST, PUT et DELETE bavardes**.

	POTEAU, support les demandes PUT et DELETE sur les collections de ressources. Une requête POST pouvant contenir les détails de plusieurs nouvelles ressources et ajoutez-les tous à la même collection, une demande PUT peut remplacer l'ensemble des ressources dans une collection et une demande de suppression peut supprimer toute une collection.

	Notez que le support de OData inclus dans ASP.NET Web API 2 fournit la possibilité de requêtes de lots. Une application cliente peut emballer plusieurs requêtes d'API web et les envoyer au serveur en une seule requête HTTP et recevoir une réponse HTTP unique qui contient les réponses à chaque demande. Pour plus d'informations, consultez la page [Lance le Batch Support API Web et Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) sur le site Web de Microsoft.

- **Respecter le protocole HTTP lors de l'envoi d'une réponse à une application cliente**.

	Une API web doit retourner des messages qui contiennent le code d'état HTTP correct pour permettre au client de déterminer comment gérer le résultat, les en-têtes HTTP appropriés afin que le client comprenne la nature du résultat et un corps convenablement formaté pour permettre au client d'analyser le résultat. Si vous utilisez le modèle ASP.NET Web API, la stratégie par défaut pour l'application des méthodes qui répondent aux requêtes HTTP POST est tout simplement de retourner une copie de la ressource nouvellement créée, comme l'illustre l'exemple suivant :

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

	Si l'opération POST est réussie, le cadre de l'API Web crée une réponse HTTP avec le code d'état 200 (OK) et les détails du client comme le corps du message. Toutefois, dans ce cas, selon le protocole HTTP, une opération POST doit retourner le code de statut 201 (création) et le message de réponse doit inclure l'URI de la ressource nouvellement créée dans l'en-tête Location de message de réponse.

	Pour fournir ces fonctionnalités, retourner votre propre message de réponse HTTP en utilisant le `IHttpActionResult` interface. Cette approche vous donne un contrôle précis du code d'état HTTP, les en-têtes dans le message de réponse et même le format des données dans le corps de message de réponse, comme illustré dans l'exemple de code suivant. Cette version de la `CreateNewCustomer` méthode est davantage conforme aux attentes du client suivant le protocole HTTP. Le `Created` méthode de la `ApiController` classe construit le message de réponse de données spécifiées et ajoute l'en-tête Location aux résultats :

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

- **Soutenir la négociation de contenu**.

	Le corps d'un message de réponse peut contenir des données dans divers formats. Par exemple, une requête HTTP GET pourrait retourner des données en JSON, ou format XML. Lorsque le client envoie une demande, il peut inclure un en-tête Accept qui spécifie les formats de données qu'il peut gérer. Ces formats sont spécifiés comme des types de médias. Par exemple, un client qui émet une demande GET qui récupère une image peut spécifier un en-tête Accept qui répertorie les types de médias que le client peut gérer, par exemple "image/jpeg, image/gif, image/png".  Quand le web API retourne le résultat, il doit formater les données en utilisant l'un de ces types de médias et spécifier le format de l'en-tête Content-Type de la réponse.

	Si le client ne spécifie pas un en-tête Accept, puis utiliser un format par défaut sensible pour le corps de réponse. À titre d'exemple, l'infrastructure ASP.NET Web API par défaut est JSON pour les données textuelles.

	> [AZURE. REMARQUE] L'infrastructure ASP.NET Web API effectue une détection automatique des en-têtes Accept et gère leur lui-même basé sur le type de données dans le corps du message de réponse. Par exemple, si le corps d'un message de réponse contient un objet CLR (common language runtime), ASP.NET Web API automatiquement formate la réponse JSON avec l'en-tête Content-Type de la réponse, la valeur "application/json" à moins que le client indique qu'il faut les résultats au format XML, auquel cas l'infrastructure ASP.NET Web API met en forme la réponse au format XML et définit l'en-tête Content-Type de la réponse à « text/xml ». Toutefois, il peut être nécessaire gérer les en-têtes Accept qui spécifient explicitement les différents types de supports dans le code d'implémentation pour une opération.

- **Fournir des liens pour prendre en charge la navigation du style HATEOAS et découverte des ressources**.

	The API Design Guidance describes how following the HATEOAS approach enables a client to navigate and discover resources from an initial starting point. This is achieved by using links containing URIs; when a client issues an HTTP GET request to obtain a resource, the response should contain URIs that enable a client application to quickly locate any directly related resources. For example, in a web API that supports an e-commerce solution, a customer may have placed many orders. When a client application retrieves the details for a customer, the response should include links that enable the client application to send HTTP GET requests that can retrieve these orders. Additionally, HATEOAS-style links should describe the other operations (POST, PUT, DELETE, and so on) that each linked resource supports together with the corresponding URI to perform each request. This approach is described in more detail in the API Design Guidance document.

	Actuellement il n'y a pas de normes qui régissent la mise en œuvre de HATEOAS, mais l'exemple suivant illustre une approche possible. Dans cet exemple, une requête HTTP GET qui trouve les détails pour un client renvoie une réponse qui contient des liens HATEOAS qui référencent les commandes de ce client :

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

	Dans cet exemple, les données du client sont représentées par le `Customer` classe illustré dans l'extrait de code suivant. Les liens HATEOAS ont lieu dans le `Links` propriété de la collection :

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

	L'opération HTTP GET récupère les données des clients de stockage et des constructions une `Customer` objet et remplit ensuite la `Links` collection. Le résultat est mis en forme comme un message de réponse JSON. Chaque lien comporte les champs suivants :

	- La relation entre l'objet renvoyé et l'objet décrit par le lien. Dans ce cas, « soi » indique que le lien est une référence à l'objet lui-même (semblable à une `this` le pointeur dans de nombreux langages orientés objet), et « ordres » sont le nom d'une collection qui contient les informations de commande associée.

	- Le lien hypertexte (`HRef`) pour l'objet décrit par le lien sous la forme d'un URI.

	- Le type de demande () HTTP`Action`) qui peuvent être envoyés à cet URI.

	- Le format de tout (des données`LinkedResourceMIMETypes`) qui devrait être fourni dans le HTTP demande ou qui peuvent être retournées dans la réponse, selon le type de la demande.

	Les liens HATEOAS indiqués dans l'exemple de réponse HTTP indiquent qu'une application cliente peut effectuer les opérations suivantes :

	- Une demande HTTP GET à l'URI _http://Adventure-Works.com/customers/2_ pour extraire les détails du client (à nouveau). Les données peuvent être retournées comme XML ou JSON.

	- Une requête HTTP PUT à l'URI _http://Adventure-Works.com/customers/2_ pour modifier les détails du client. Les nouvelles données doivent être fournies dans le message de demande au format x-www-formulaires-urlencoded.

	- Une demande HTTP DELETE à l'URI _http://Adventure-Works.com/customers/2_ pour supprimer le client. La demande ne pas attendre des renseignements supplémentaires ou retourner des données dans le corps de message de réponse.

	- Une demande HTTP GET à l'URI _http://Adventure-Works.com/customers/2/Orders_ pour trouver toutes les commandes pour le client. Les données peuvent être retournées comme XML ou JSON.

	- Une requête HTTP PUT à l'URI _http://Adventure-Works.com/customers/2/Orders_ pour créer un nouvel ordre pour ce client. Les données doivent être fournies dans le message de demande au format x-www-formulaires-urlencoded.

## Considérations relatives à la gestion des exceptions
Par défaut, dans l'API de Web ASP.NET framework, si une opération lève une exception non interceptée le cadre renvoie un message de réponse avec le code d'état HTTP 500 (Internal Server Error). Dans de nombreux cas, cette approche simpliste n'est pas utile dans l'isolement et permet de déterminer la cause de l'exception difficile. Par conséquent, vous devez adopter une approche plus globale de gestion des exceptions, tenant compte des points suivants :

- **Capturer des exceptions et renvoient une réponse significative aux clients**.

	The code that implements an HTTP operation should provide comprehensive exception handling rather than letting uncaught exceptions propagate to the Web API framework. If an exception makes it impossible to complete the operation successfully, the exception can be passed back in the response message, but it should include a meaningful description of the error that caused the exception. The exception should also include the appropriate HTTP status code rather than simply returning status code 500 for every situation. For example, if a user request causes a database update that violates a constraint (such as attempting to delete a customer that has outstanding orders), you should return status code 409 (Conflict) and a message body indicating the reason for the conflict. If some other condition renders the request unachievable, you can return status code 400 (Bad Request). You can find a full list of HTTP status codes on the [Définitions des codes d'État](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) page sur le site Web du W3C.

	Le code suivant montre un exemple qui emprisonne les différentes conditions et retourne une réponse appropriée.

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

	> [AZURE. ASTUCE] N'incluez pas d'informations qui pourraient être utiles à un pirate tente de pénétrer votre web API. Pour plus amples renseignements, visitez le [Gestion des exceptions dans ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) page sur le site Web de Microsoft.

	> [AZURE. REMARQUE] De nombreux serveurs web piéger les conditions d'erreur eux-mêmes avant qu'ils atteignent le web API. Par exemple, si vous configurez l'authentification pour un site web et l'utilisateur ne parvient pas à fournir les informations d'authentification correcte, le serveur web doit répondre avec code d'état 401 (non autorisé). Une fois qu'un client a été authentifié, votre code peut effectuer ses propres contrôles pour vérifier que le client doit être en mesure accès la ressource demandée. Si cette autorisation échoue, vous devez retourner le code d'état 403 (refusé).

- **Gestion des exceptions dans une information cohérente de manière et ouvrez une session sur les erreurs**.

	Pour gérer les exceptions de façon cohérente, envisagez d'implémenter une gestion stratégie à travers l'ensemble du web API d'erreur globale. Vous pouvez réaliser ce cadre en créant un filtre d'exception qui s'exécute chaque fois qu'un contrôleur lève une exception non gérée qui n'est pas une `HttpResponseException` exception. Cette approche est décrite sur le [Gestion des exceptions dans ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) page sur le site Web de Microsoft.

	Cependant, il y a plusieurs situations où un filtre d'exception n'intercepte pas une exception, y compris :

	- Exceptions levées par des constructeurs de contrôleur.

	- Exceptions levées à partir des gestionnaires de messages.

	- Exceptions levées lors de l'acheminement.

	- Exceptions levées lors de la sérialisation du contenu d'un message de réponse.

	Pour gérer ces cas, vous devrez peut-être mettre en œuvre une approche plus personnalisée. Vous devriez également intégrer error logging qui capture tous les détails de chaque exception ; ce journal des erreurs peut contenir des informations détaillées tant qu'il ne fait pas accessible sur le web aux clients. L'article [Gestion d'erreur Global Web API](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) sur Microsoft site Web illustre une façon d'accomplir cette tâche.

- **Faire la distinction entre les erreurs côté client et côté serveur**.

	Le protocole HTTP établit une distinction entre les erreurs qui se produisent en raison de l'application cliente (codes d'état HTTP de 4xx) et des erreurs qui sont causés par un accident sur le serveur (les codes d'État 5xx HTTP). Assurez-vous que vous respectez cette convention dans les messages de réponse d'erreur.

<a name="considerations-for-optimizing"></a>
## Considérations relatives à l'optimisation de l'accès aux données côté client

Dans un environnement distribué telles que celle portant sur un serveur web et les applications clientes, une des principales sources de préoccupation est le réseau. Cela peut agir comme un goulot d'étranglement considérable, surtout si une application cliente est souvent envoyer des requêtes ou reçoit des données. Par conséquent, vous devriez viser à réduire au minimum la quantité de trafic qui circule sur le réseau. Considérez les points suivants lorsque vous implémentez le code pour récupérer et gérer les données :

- **Soutenir la mise en cache côté client**.

	Le protocole HTTP 1.1 prend en charge la mise en cache dans les clients et les serveurs intermédiaires grâce auquel une demande soit acheminée par l'utilisation de l'en-tête Cache-Control. Lorsqu'une application cliente envoie un HTTP GET request à l'API web, la réponse peut inclure un en-tête Cache-Control qui indique si les données dans le corps de la réponse peuvent être en toute sécurité mis en cache par le client ou un serveur intermédiaire par lequel la demande a été acheminée et Pendant combien de temps elle doit expirer et être considéré comme obsolète. L'exemple suivant montre une requête HTTP GET et la réponse correspondante qui inclut un en-tête Cache-Control :

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

	Dans cet exemple, l'en-tête Cache-Control spécifie que les données retournées doit expirer après 600 secondes et est conçu uniquement pour un seul client et ne doit pas être stockée dans un cache partagé utilisé par les autres clients (c'est _privé_). Pourrait spécifier l'en-tête Cache-Control _public_ au lieu de _privé_ auquel cas les données peuvent être stockées dans un cache partagé, ou il pourrait spécifier _non-magasin_ dans ce cas, les données doivent **pas** mise en cache par le client. L'exemple de code suivant montre comment construire un en-tête Cache-Control dans un message de réponse :

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

	Ce code fait appel à une coutume `IHttpActionResult` classe nommée `OkResultWithCaching`. Cette classe permet au contrôleur de définir le cache de contenu de l'en-tête :

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

	> [AZURE. REMARQUE] Le protocole HTTP définit également la _non-cache_ directive relative à l'en-tête Cache-Control. Plutôt confuse, cette directive ne signifie pas « ne pas mettre cache » mais plutôt « revalider les informations mises en cache avec le serveur avant de le retourner » ; les données peuvent toujours être mis en cache, mais il est vérifié chaque fois qu'il est utilisé pour s'assurer qu'il est toujours d'actualité.

	Gestion du cache est la responsabilité de l'application client ou serveur intermédiaire, mais si correctement mis en oeuvre il peut économiser la bande passante et améliorer les performances en éliminant le besoin de récupérer les données qui a déjà été récemment récupérés.

	Le _max-age_ valeur à l'en-tête Cache-Control est seulement un guide et non une garantie que les données correspondantes ne changeront pas durant la période visée. Le web API doit, sur une valeur appropriée selon la volatilité des données, le max-age. L'expiration de cette période, le client doit jeter l'objet du cache.

	> [AZURE. REMARQUE] Navigateurs web modernes plus prend en charge la mise en cache côté client en ajoutant les en-têtes de contrôle du cache appropriée aux demandes et en examinant les en-têtes des résultats, tel que décrit. Cependant, certains navigateurs plus anciens ne met pas en cache les valeurs retournées à partir d'une URL qui comprend une chaîne de requête. Ce n'est généralement pas un problème pour les applications clientes personnalisées qui mettent en œuvre leur propre stratégie de gestion de cache basée sur le protocole que nous discuté.
	>
	> Certains proxies plus âgés présentent le même comportement et pourraient met pas en cache les demandes fondées sur des URL avec des chaînes de requête. Cela pourrait être un problème pour les applications clientes personnalisées qui se connectent à un serveur web par l'intermédiaire de laquelle une procuration.

- **Fournir les ETags afin d'optimiser le traitement des requêtes**.

	Lorsqu'une application cliente récupère un objet, le message de réponse peut également inclure une _ETag_ (Balise d'entité). Un ETag est une chaîne opaque qui indique la version d'une ressource ; chaque fois qu'une ressource modifie l'Etag est également modifiée. Cette ETag doit être mises en cache dans le cadre des données par l'application cliente. L'exemple de code suivant montre comment ajouter un ETag dans le cadre de la réponse à une requête HTTP GET. Ce code utilise le `GetHashCode` méthode d'un objet pour générer une valeur numérique qui identifie l'objet (vous pouvez substituer cette méthode si nécessaire et générer votre propre table de hachage à l'aide d'un algorithme tel que MD5) :

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

	Le message de réponse Posté par l'API web ressemble à ceci :

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
	```

	> [AZURE. ASTUCE] Pour des raisons de sécurité, ne permettent pas de données sensibles ou des données renvoyées via une connexion authentifiée de (HTTPS) à mettre en cache.

	Une application cliente peut émettre une demande subséquente de GET pour récupérer la ressource même à tout moment, et si la ressource a changé (il a un ETag différent) la version mise en cache doit être jetée et la nouvelle version ajouté au cache. Si une ressource est grande et nécessite une quantité importante de bande passante pour transmettre au client, les demandes répétées de récupérer les données mêmes peuvent devenir inefficaces. Pour lutter contre cela, le protocole HTTP définit le processus suivant pour l'optimisation des requêtes GET qui vous soutienne dans une API web :

	- Le client génère une requête GET contenant l'ETag pour la version actuellement mises en cache de la ressource référencée dans un en-tête HTTP If-None-Match :

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- L'opération d'obtention dans l'API web obtient l'ETag actuel pour les données demandées (ordre 2 dans l'exemple ci-dessus) et le compare à la valeur dans l'en-tête If-None-Match.

	- Si le courant ETag pour les données demandées correspond à l'ETag fourni par la demande, la ressource n'a pas changé et l'API web doit retourner une réponse HTTP avec un corps de message vide et un code statut 304 (non modifié).

	- Si l'ETag actuel pour les données demandées ne correspond pas à l'ETag fourni par la demande, puis les données ont changé et le API web doit retourner une réponse HTTP avec les nouvelles données dans le corps du message et un code d'état 200 (OK).

	- Si les données demandées n'existent plus le web API doit alors retourner une réponse HTTP avec le code d'État 404 (introuvable).

	- Le client utilise le code d'État pour maintenir le cache. Si les données n'a pas changé (code statut 304) puis l'objet peut rester en mémoire cache et l'application cliente doit continuer à utiliser cette version de l'objet. Si les données ont changé (code d'état 200) puis l'objet mis en cache doit être jeté et inséré une nouvelle. Si les données ne soient plus disponibles (code d'État 404) puis l'objet doit être supprimé du cache.

	> [AZURE. REMARQUE] Si l'en-tête de la réponse ne contient l'en-tête Cache-Control-store, puis l'objet devrait toujours être supprimé du cache quel que soit le code d'état HTTP.

	Le code ci-dessous montre la `FindOrderByID` méthode étendu pour prendre en charge l'en-tête If-None-Match. Notez que, si l'en-tête If-None-Match est omis, l'ordre spécifié est toujours Récupérée :

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

	Cet exemple comprend une coutume supplémentaire `IHttpActionResult` classe nommée `EmptyResultWithCaching`. Cette classe agit simplement comme un wrapper autour d'un `HttpResponseMessage` objet qui ne contient-elle pas de corps de réponse :

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

	> [AZURE. ASTUCE] Dans cet exemple, l'ETag pour les données est générée en hachant les données extraites de la source de données sous-jacente. Si l'ETag peut être calculée d'une autre manière, puis optimiser davantage le processus et les données ne doivent être extrait de la source de données, si elle a changé.  Cette approche est particulièrement utile si les données sont grandes ou accéder à la source de données peut entraîner une latence importante (par exemple, si la source de données est une base de données distante).

- **Utiliser les ETags pour soutenir l'accès concurrentiel optimiste**.

	Pour activer les mises à jour sur données précédemment mises en cache, le protocole HTTP prend en charge une stratégie d'accès concurrentiel optimiste. Si, après extraction et mise en cache d'une ressource, l'application cliente envoie ensuite une requête PUT ou DELETE pour modifier ou supprimer la ressource, il doit inclure dans l'en-tête If-Match qui fait référence à l'ETag. L'API web peut ensuite utiliser ces informations pour déterminer si la ressource déjà a été modifiée par un autre utilisateur, puisqu'il a été récupéré et envoyer une réponse appropriée à l'application cliente comme suit :

	- Le client génère une demande PUT contenant les nouveaux détails pour la ressource et l'ETag pour la version actuellement mises en cache de la ressource référencée dans un en-tête HTTP If-Match. L'exemple suivant montre une requête PUT qui met à jour une ordonnance :

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	ProductID=3&Quantity=5&OrderValue=250
	```

	- L'opération PUT dans l'API web obtient l'ETag actuel pour les données demandées (ordre 1 dans l'exemple ci-dessus) et la compare à la valeur de l'en-tête If-Match.

	- Si le courant ETag pour les données demandées correspond à l'ETag fourni par la demande, la ressource n'a pas changé et l'API web doit exécuter la mise à jour, en retournant un message avec le code d'état HTTP 204 (sans contenu) si elle est réussie. La réponse peut inclure des Cache-Control et en-têtes ETag pour la version mise à jour de la ressource. La réponse doit toujours inclure l'en-tête Location qui fait référence à l'URI de la ressource nouvellement mis à jour.

	- Si l'ETag actuel pour les données demandées ne correspond pas à l'ETag fourni par la demande, les données ont été modifiées par un autre utilisateur puisqu'elle a été extraite et le web API doit retourner une réponse HTTP avec un corps de message vide et un code d'état de 412 (échec de la condition préalable).

	- Si la ressource à mettre à jour n'existe plus le web API doit alors retourner une réponse HTTP avec le code d'État 404 (introuvable).

	- Le client utilise les en-têtes de code et de la réponse de statut pour maintenir le cache. Si les données ont été mis à jour (code d'État 204) puis l'objet peut rester en mémoire cache (aussi longtemps que l'en-tête Cache-Control ne spécifie pas non-magasin), mais il convient d'actualiser l'ETag. Si les données a été modifiées par un autre utilisateur a changé (code d'État 412) ou introuvable (code d'État 404), puis l'objet mis en cache doit être jeté.

	L'exemple de code suivant illustre une implémentation de l'opération PUT pour le contrôleur de commandes :

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

	> [AZURE. ASTUCE] Utilisation de l'en-tête If-Match est entièrement facultative, et s'il est omis le web API tentera toujours de mettre à jour l'ordre spécifié, éventuellement aveuglément écrasant une mise à jour faite par un autre utilisateur. Pour éviter les problèmes dus à des mises à jour perdues, toujours fournir un en-tête If-Match.

<a name="considerations-for-handling-large"></a>
## Considérations pour le traitement des réponses et les grandes demandes

Il peut y avoir des reprises lorsqu'une application cliente doit émettre des requêtes qui envoient ou reçoivent des données qui peuvent être de plusieurs millions d'octets (ou plus) dans la taille. Alors que cette quantité de données est transmise en attente pourrait provoquer l'application cliente à se bloquer. Considérez les points suivants lorsque vous avez besoin gérer les requêtes qui incluent des données substantielles :

- **Optimiser les requêtes et les réponses qui impliquent des objets de grande taille**.

	Certaines ressources peuvent être des objets de grande taille ou comprennent des grands champs, tels que les images graphiques ou d'autres types de données binaires. Une API web devrait soutenir le streaming pour permettre d'optimiser le téléchargement et le téléchargement de ces ressources.

	Le protocole HTTP fournit le mécanisme de codage de transfert segmenté pour couler des objets de données de grande taille à un client. Lorsque le client envoie une demande HTTP GET pour un objet de grande taille, l'API web peut envoyer la réponse au coup par coup _gros morceaux_ sur une connexion HTTP. La longueur des données dans la réponse ne peut pas être connue au départ (il peut être généré), donc le serveur qui héberge le web API devrait envoyer un message de réponse avec chaque morceau qui spécifie le codage de transfert : mémorisé en bloc en-tête plutôt qu'un en-tête Content-Length. L'application cliente peut recevoir chaque segment à son tour à mettre en place la réponse complète. Le transfert de données se termine lorsque le serveur renvoie un morceau final avec taille nulle.	Vous pouvez implémenter de segmentation dans l'API de Web ASP.NET à l'aide de la `PushStreamContent` classe.

	L'exemple suivant montre une opération qui répond aux requêtes HTTP GET pour des images de produit :

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

	Dans cet exemple, `ConnectBlobToContainer` est une méthode d'assistance qui se connecte à un conteneur spécifié (nom non illustré) stockage Blob Azure. `BlobExists` est une autre méthode d'assistance qui retourne une valeur booléenne qui indique si un objet blob portant le nom spécifié existe dans le conteneur de stockage blob.

	Chaque produit a sa propre image blob stocké. Le `FileDownloadResult` classe est une coutume `IHttpActionResult` classe qui utilise un `PushStreamContent` objet pour lire les données d'image blob approprié et transmettre de façon asynchrone le contenu du message de réponse :

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

	Vous pouvez également appliquer le streaming pour téléverser les opérations si un client a besoin d'afficher une nouvelle ressource qui inclut un objet volumineux. L'exemple suivant illustre la méthode Post pour le `ProductImages` contrôleur. Cette méthode permet au client de télécharger une nouvelle image produit :

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

	Ce code utilise un autre personnalisé `IHttpActionResult` classe appelée `FileUploadResult`. Cette classe contient la logique pour le téléchargement des données de manière asynchrone :

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

	> [AZURE. ASTUCE] Le volume de données que vous pouvez télécharger vers un service web n'est pas limité par le streaming, et une demande unique pourrait en théorie un objet massif qui consomme des ressources considérables. Si, pendant le processus de diffusion en continu, le API web détermine que la quantité de données dans une requête a dépassé certaines limites acceptables, elle peut abandonner l'opération et retourner un message de réponse avec le code d'État 413 (requête entité trop gros).

	Vous pouvez réduire la taille des objets volumineux transmis sur le réseau à l'aide de la compression HTTP. Cette approche aide à réduire la quantité de trafic réseau et la latence du réseau associées, mais au prix de nécessitant un traitement supplémentaire sur le client et le serveur qui héberge le web API. Par exemple, une application cliente qui s'attend à recevoir les données compressées peut inclure un Accept-Encoding : gzip en-tête de requête (autre algorithmes peuvent également être spécifiées de compression de données). Si le serveur prend en charge la compression il doit répondre avec le contenu qui s'est tenu au format gzip dans le corps du message et le Content-Encoding : gzip en-tête de réponse.

	> [AZURE. ASTUCE] Vous pouvez combiner compression codée avec diffusion en continu. compresser les données d'abord avant le streaming il et spécifier l'encodage gzip et le codage dans les en-têtes de message de transfert mémorisé en bloc. Notez également que certains serveurs web (par exemple Internet Information Server) peuvent être configurés pour compresser automatiquement les réponses HTTP indépendamment de savoir si l'API web compresse les données ou non.

- **Mettre en œuvre des réponses partielles pour les clients qui ne supportent pas les opérations asynchrones**.

	As an alternative to asynchronous streaming, a client application can explicitly request data for large objects in chunks, known as partial responses. The client application sends an HTTP HEAD request to obtain information about the object. If the web API supports partial responses if should respond to the HEAD request with a response message that contains an Accept-Ranges header and a Content-Length header that indicates the total size of the object, but the body of the message should be empty. The client application can use this information to construct a series of GET requests that specify a range of bytes to receive. The web API should return a response message with HTTP status 206 (Partial Content), a Content-Length header that specifies the actual amount of data included in the body of the response message, and a Content-Range header that indicates which part (such as bytes 4000 to 8000) of the object this data represents.

	Requêtes HTTP HEAD et réponses partielles sont décrites plus en détail dans le document d'orientation de conception API.

- **Évitez d'envoyer des messages de statut Continue inutiles dans les applications clientes**.

	Une application cliente qui s'apprête à envoyer une grande quantité de données à un serveur peut déterminer d'abord si le serveur est en fait prêt à accepter la demande. Avant d'envoyer les données, l'application cliente peut soumettre une requête HTTP avec un Expect : 100-Continue d'en-tête, un en-tête Content-Length qui indique la taille des données, mais un corps de message vide. Si le serveur est prêt à gérer la demande, il doit répondre avec un message qui spécifie l'état HTTP 100 (Continue). L'application cliente peut s'engager et envoyer la demande complète y compris les données dans le corps du message.

	Si vous hébergez un service à l'aide d'IIS, le pilote HTTP.sys automatiquement détecte et gère Expect : 100-continuer les en-têtes avant de transmettre les demandes à votre application web. Cela signifie que vous êtes susceptibles de voir ces en-têtes dans le code de votre application, et vous pouvez supposer que IIS a déjà filtré les messages qu'il juge inapte ou trop grand.

	Si vous générez des applications clientes à l'aide du .NET Framework, tous les messages POST et PUT seront tout d'abord envoyer des messages avec Expect : 100-Continue d'en-tête par défaut. Comme pour le côté serveur, le processus est géré de façon transparente par le .NET Framework. Toutefois, ce processus aboutit à chaque requête POST et PUT causer 2 allers-retours vers le serveur, même pour petites requêtes. Si votre application n'envoie pas de requêtes avec de grandes quantités de données, vous pouvez désactiver cette fonctionnalité en utilisant le `ServicePointManager` classe pour créer `ServicePoint` objets dans l'application cliente. A `ServicePoint` l'objet gère les connexions que le client effectue vers un serveur basé sur les régime et hôte des fragments d'URI qui identifient les ressources sur le serveur. Vous pouvez ensuite définir la `Expect100Continue` propriété de la `ServicePoint` objet à false. Toutes les requêtes POST et PUT commises par le client via un URI correspondant à des fragments de schéma et hôte de la `ServicePoint` objet sera envoyé sans attendre : 100-Continue d'en-têtes. Le code suivant montre comment configurer un `ServicePoint` objet qui configure toutes les demandes envoyées aux URI avec un schéma de `http` et une foule de `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	Vous pouvez également définir la méthode statique `Expect100Continue` propriété de la `ServicePointManager` classe pour spécifier la valeur par défaut de cette propriété pour tous créés ultérieurement `ServicePoint` objets. Pour plus d'informations, consultez le [Classe ServicePoint](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) page sur le site Web de Microsoft.

- **Prise en charge la pagination pour les requêtes qui peut retourner un grand nombre d'objets**.

	Si la collection contient un grand nombre de ressources, émission d'une requête GET à l'URI correspondant pourrait donner lieu à une transformation significative sur le serveur hébergeant l'API web influant sur le rendement et génèrent une quantité importante de trafic réseau, ce qui entraîne une latence accrue.

	Pour gérer ces cas, l'API web devrait soutenir des chaînes de requête qui permettent l'application cliente extraire les données en blocs plus maniable, discret (ou pages) ou d'affiner les requêtes. L'infrastructure ASP.NET Web API analyse des chaînes de requête et eux scinde en une série de paires de paramètre/valeur qui sont passés à la méthode appropriée, suivant les règles de routage décrits précédemment. La méthode doit être implémentée pour accepter ces paramètres en utilisant le même nom spécifié dans la chaîne de requête. En outre, ces paramètres doivent être facultatif (dans le cas où le client omet de la chaîne de requête d'une demande) et ont des valeurs par défaut significatif. Le code ci-dessous montre la `GetAllOrders` méthode dans le `Orders` contrôleur. Cette méthode récupère les détails de commandes. Si cette méthode était sans contrainte, il pourrait éventuellement revenir une grande quantité de données. Le `limit` et `offset` paramètres sont destinées à réduire le volume de données à un sous-ensemble plus petit, dans ce cas, seules les 10 premières commandes par défaut :

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

	Une application cliente peut émettre une requête d'extraction de 30 commandes commençant à offset 50 à l'aide de l'URI _http://www.Adventure-Works.com/API/Orders?Limit=30&offset = 50_.

	> [AZURE. ASTUCE] Éviter d'activer les applications clientes de spécifier des chaînes de requête qui donnent lieu à un URI qui est plus de 2 000 caractères de long. Beaucoup web clients et serveurs ne peut pas gérer les URI auxquels sont longtemps.

<a name="considerations-for-maintaining-responsiveness"></a>
## Considérations pour le maintien de la réactivité, l'évolutivité et disponibilité

La même API web pourrait être utilisée par plusieurs applications clientes exécutant n'importe où dans le monde. Il est important de veiller à ce que le web API est mis en œuvre pour maintenir la réactivité sous une charge lourde, pour être évolutive pour soutenir une charge de travail très variée et de garantir la disponibilité pour les clients qui effectuent des opérations stratégiques. Considérer les points suivants pour déterminer comment répondre à ces exigences :

- **Fournir une prise en charge asynchrone des requêtes longues**.

	Une demande qui pourrait prendre un certain temps à traiter doit être effectuée sans bloquer le client qui a présenté la demande. Le web API peut effectuer des vérifications initiales pour valider la demande, lancer une tâche distincte pour exécuter les travaux et puis retourner un message de réponse avec le code HTTP 202 (Accepted). La tâche pourrait exécuter de façon asynchrone dans le cadre de l'API web de traitement, ou il pourrait être déchargée à un WebJob Azure (si l'API web est hébergé par un site Web d'Azur) ou à un rôle de travail (si l'API web est implémenté comme un service de cloud Azure).

	> [AZURE. REMARQUE] Pour plus d'informations sur l'utilisation de WebJobs avec Azure site, visitez la page [Utilisez WebJobs pour exécuter des tâches en arrière-plan dans Microsoft Azure Websites](web-sites-create-web-jobs.md) sur le site Web de Microsoft.

	Le web API devrait également fournir un mécanisme permettant de renvoyer les résultats du traitement à l'application cliente. Vous pouvez atteindre cet objectif en fournissant un mécanisme d'interrogation pour client applications pour interroger périodiquement si le traitement est terminé et obtenir le résultat ou activer le web API pour envoyer une notification lorsque l'opération est terminée.

	Vous pouvez implémenter un mécanisme d'interrogation simple en fournissant un _du scrutin_ URI qui agit comme une ressource virtuelle utilisant l'approche suivante :

	1. L'application cliente envoie la requête initiale à l'API web.

	2. L'API web stocke des informations sur la requête dans une table qui s'est tenue en stock de la table ou Microsoft Azure Cache et génère une clé unique pour cette entrée, éventuellement sous la forme d'un GUID.

	3. L'API web initie le traitement comme une tâche distincte. L'API web enregistre l'état de la tâche dans la table comme _En cours d'exécution_.

	4. Le web API renvoie un message de réponse avec le code d'état HTTP 202 (accepté) et le GUID de l'entrée de la table dans le corps du message.

	5. Une fois la tâche terminée, l'API web stocke les résultats dans le tableau et définit l'état de la tâche à _Toutes les_. Notez que si la tâche échoue, le API web peut également stocker des informations sur l'échec et de définir l'État sur _A échoué_.

	6. Alors que la tâche est en cours d'exécution, le client peut continuer d'exercer son propre traitement. Il peut périodiquement envoyer une requête à l'URI _/polling/ {guid}_ où _{guid}_ est le GUID renvoyé dans le message de 202 réponse par le web API.

	7. Le web API à la _/polling {guid}_ URI interroge l'état de la tâche correspondante dans la table et retourne un message de réponse contenant cette État (avec le code d'état HTTP 200 (OK)_En cours d'exécution_, _Toutes les_, ou _A échoué_). Si la tâche a accompli ou omis, le message de réponse peut également inclure les résultats de la transformation ou de toute information disponible sur les raisons de l'échec.

	Si vous souhaitez implémenter des notifications, les options disponibles incluent :

	- En utilisant un Hub de Notification Azure pour pousser les réponses asynchrones aux applications clientes. La page [Moyeux d'Azur Notification notifier les utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) sur Microsoft site Web fournit plus de détails.

	- En utilisant le modèle Comet de conserver une connexion persistante entre le client et le serveur qui héberge le web API et cette connexion messages push depuis le serveur de retour au client. L'article du magazine MSDN [Création d'une Application Simple de la comète dans le Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) décrit un exemple de solution.

	- L'utilisation de SignalR pour pousser données en temps réel sur le serveur web au client via une connexion réseau persistantes. SignalR est disponible pour les applications web ASP.NET comme un package NuGet. Vous pouvez trouver plus d'informations sur le [SignalR ASP.NET](http://signalr.net/) site Web.

	> [AZURE. REMARQUE] Comète et SignalR les deux utilisent des connexions persistantes réseau entre le serveur web et l'application cliente. Cela peut affecter une évolutivité dont un grand nombre de clients peut avoir besoin d'un aussi grand nombre de connexions simultanées.

- **S'assurer que chaque demande est apatride**.

	Chaque demande doit être considérée atomique. Il ne devrait y avoir aucune dépendance entre une demande faite par une application cliente et toute les demandes suivantes du même client. Cette approche aide à évolutivité ; les instances du service web peuvent être déployés sur plusieurs serveurs. Les demandes des clients, adressez-vous à l'un de ces cas et les résultats devraient toujours être les mêmes. Il améliore également la disponibilité pour une raison similaire ; Si un échec de serveur web requêtes peuvent être acheminés vers une autre instance (à l'aide d'Azur Traffic Manager) tandis que le serveur est redémarré sans aucune séquelle sur les applications clientes.

- **Suivi des clients et la limitation de la mise en oeuvre pour réduire les risques d'attaques DOS**.

	Si un client spécifique établit un grand nombre de demandes dans un délai donné, elle peut monopoliser le service et affecter les performances des autres clients. Pour minimiser ce problème, une API web peut surveiller des appels à partir d'applications clientes grâce au suivi de l'adresse IP de toutes les demandes entrantes ou en vous connectant chaque accès authentifié. Vous pouvez utiliser ces informations pour limiter l'accès aux ressources. Si un client dépasse une certaine limite, l'API web peut renvoyer un message de réponse avec état 503 (Service indisponible) et inclure un en-tête Retry-After qui spécifie quand le client peut envoyer la demande suivante sans qu'il soit diminué. Cette stratégie peut aider à réduire les risques d'une attaque par déni de Service (DOS) d'un ensemble de clients, le système de décrochage.

- **Gérer les connexions HTTP persistantes avec soin**.

	Le protocole HTTP prend en charge les connexions HTTP persistantes où elles sont disponibles. Les caractéristiques de HTTP 1.0 ajouté le Connection : Keep-Alive en-tête qui permet à une application cliente indiquer au serveur qu'il peut utiliser la même connexion pour envoyer les demandes suivantes plutôt que d'ouvrir de nouveaux. La connexion se ferme automatiquement si le client ne réutilise pas la connexion dans un délai défini par l'hôte. Ce comportement est par défaut dans HTTP 1.1, tel qu'utilisé par les services Azure, donc il est inutile d'inclure les messages keepalive dans les messages.

	Keeping a connection open can can help to improve responsiveness by reducing latency and network congestion, but it can be detrimental to scalability by keeping unnecessary connections open for longer than required, limiting the ability of other concurrent clients to connect. It can also affect battery life if the client application is running on a mobile device; if the application only makes occassional requests to the server, maintaining an open connection can cause the battery to drain more quickly. To ensure that a connection is not made persistent with HTTP 1.1, the client can include a Connection:Close header with messages to override the default behavior. Similarly, if a server is handling a very large number of clients it can include a Connection:Close header in response messages which should close the connection and save server resources.

	> [AZURE. REMARQUE] Les connexions HTTP persistantes sont une fonction purement facultative pour réduire la surcharge réseau associée à plusieurs reprises établir un canal de communications. L'API web ni l'application cliente doit dépendre d'une connexion HTTP persistante étant disponibles. Ne pas utiliser les connexions HTTP persistantes à mettre en œuvre des systèmes de notification Comet-style ; au lieu de cela vous est conseillé d'utiliser des prises (ou websockets si disponible) à la couche TCP. Enfin, notez les messages keepalive sont d'une utilité limitée, si une application cliente communique avec un serveur via un serveur proxy ; seule la connexion avec le client et le serveur proxy sera persistante.

## Considérations relatives à la publication et la gestion d'une API web

Pour rendre une API web disponibles pour les applications clientes, l'API web doit être déployée sur un environnement hôte. Cet environnement est en général un serveur web, mais ce peut être un autre type de processus hôte. Vous devez considérer les points suivants lorsque vous publiez un site web API :

- Toutes les demandes doivent être authentifiés et autorisés, et le niveau de contrôle d'accès doit être appliqué.
- Un commercial web API peut être soumise à des garanties de qualité diverses concernant les temps de réponse. Il est important de s'assurer que l'environnement hôte est évolutif, si la charge peut varier considérablement au fil du temps.
- Si peut être nécessaire de compteur demandes à des fins de monétisation.
- Il peut être nécessaire de réguler le flux du trafic sur le web, API et mettre en œuvre pour des clients particuliers qui ont épuisé leurs quotas de limitation.
- Exigences réglementaires pourraient mandater journalisation et l'audit de toutes les demandes et les réponses.
- Pour assurer la disponibilité, il peut être nécessaire de surveiller l'intégrité du serveur qui héberge le web API et le redémarrer si nécessaire.

Il est utile d'être capable de dissocier ces questions des questions techniques concernant la mise en oeuvre de l'API web. Pour cette raison, envisager la création d'un [façade](http://en.wikipedia.org/wiki/Facade_pattern), en tant qu'un processus distinct et que les demandes de routes à l'API web. La façade peut fournir les opérations de gestion et validés avant requêtes sur le web API. À l'aide d'une façade peut aussi apporter de nombreux avantages fonctionnels, y compris :

- Agissant comme un point d'intégration pour plusieurs web APIs.
- Transformer des messages et traduction des protocoles de communication pour les clients créés à l'aide de différentes technologies.
- Mise en cache les requêtes et les réponses pour réduire les chargement sur le serveur hébergeant le web API.

## Considérations relatives à l'essai une API web
Une API web doit être testée comme soigneusement comme n'importe quel autre logiciel. Vous devez envisager de créer des tests unitaires pour valider les fonctionnalités de chaque opération, comme vous le feriez avec n'importe quel autre type d'application. Pour plus d'informations, consultez la page [Vérifier le Code à l'aide de Tests unitaires](https://msdn.microsoft.com/library/dd264975.aspx) sur le site Web de Microsoft.

> [AZURE. REMARQUE] Le web d'échantillon API disponible avec ce guide comprend un projet de test qui indique comment exécuter des tests sur certaines opérations unitaires.

La nature d'une API web apporte ses propres exigences supplémentaires afin de vérifier qu'il fonctionne correctement. Vous devez payer une attention particulière aux aspects suivants :

- Testez toutes les routes pour vérifier qu'ils invoquent les opérations correctes. Être particulièrement conscients de code d'état HTTP 405 (méthode non autorisée) étant retourné inopinément, car cela peut indiquer une incompatibilité entre une route et les méthodes HTTP (GET, POST, PUT, DELETE) qui peuvent être expédiées vers cette voie.

	Envoyer des requêtes HTTP aux liaisons qui ne gèrent pas eux, tels que la présentation d'une demande de poste à une ressource spécifique (requêtes POST ne devraient être envoyés aux collections de ressources). Dans ces cas, la réponse n'est valide que _devrait_ être le code d'État 405 (non autorisé).

- Vérifiez que toutes les voies sont protégés correctement et sont soumis aux contrôles d'authentification et d'autorisation appropriées.

	> [AZURE. REMARQUE] Certains aspects de la sécurité tels que l'authentification des utilisateurs sont susceptibles d'assumer la responsabilité de l'environnement de l'hôte plutôt que le API web, mais il est encore nécessaire d'inclure des tests de sécurité dans le cadre du processus de déploiement.

- Tester la gestion des exceptions effectuée par chaque opération et de vérifier qu'une réponse appropriée et significative de HTTP est repassée à l'application cliente.
- Vérifiez que les messages de demande et de réponse sont bien formées. Par exemple, si une demande HTTP POST contient les données pour une nouvelle ressource au format x-www-formulaires-urlencoded, confirmer que l'opération correspondante correctement analyse les données, crée les ressources et renvoie une réponse contenant les détails de la nouvelle ressource, y compris l'en-tête d'emplacement correct.
- Vérifier tous les liens et les URIs dans les messages de réponse. Par exemple, un message HTTP POST doit retourner l'URI de la ressource nouvellement créé. Tous les liens HATEOAS doivent être valides.

	> [AZURE. IMPORTANT] Si vous publiez le API web grâce à un Service de gestion API, puis ces URIs devrait refléter l'URL du service de gestion et non celui du serveur web qui héberge le web API.

- S'assurer que chaque opération retourne les codes d'état correct pour différentes combinaisons d'entrée. Par exemple :
	- Si une requête est acceptée, elle doit retourner le code d'état 200 (OK)
	- Si une ressource n'est trouvée, l'opération devrait returs code d'état HTTP 404 (introuvable).
	- Si le client envoie une requête qui supprime une ressource, le code d'État doit être 204 (sans contenu).
	- Si le client envoie une requête qui crée une nouvelle ressource, le code d'État devrait être 201 (création)

Méfiez-vous des codes d'état de réponse inattendue dans la gamme 5xx. Ces messages sont souvent signalés par le centre serveur pour indiquer qu'il était incapable de remplir une demande valide.

- Testez les combinaisons d'en-tête de demande différente qu'une application cliente peut spécifier et veiller à ce que le web API renvoie les informations attendues dans les messages de réponse.

- Tester les chaînes de requête. Si une opération peut prendre des paramètres optionnels (tels que les requêtes de pagination), tester les différentes combinaisons et l'ordre des paramètres.

- Vérifier que les opérations asynchrones se terminent avec succès. Si le web API prend en charge le streaming pour les requêtes qui retournent des objets binaires volumineux (tels que vidéo ou audio), s'assurer que les demandes des clients ne sont pas bloqués, tandis que les données sont transmis en continu. Si le web API implémente interrogation pour les opérations de modification de données longues, vérifiez que que les opérations de signaler leur état correctement car ils procèdent.

Vous devez également créer et exécuter des tests de performance pour vérifier que l'API web fonctionne de manière satisfaisante sous la contrainte. Vous pouvez construire une performance web et un projet de test de charge à l'aide de Visual Studio Ultimate. Pour plus d'informations, consultez la page [Exécuter des tests de performance sur une demande avant une sortie](https://msdn.microsoft.com/library/dn250793.aspx) sur le site Web de Microsoft.

## Publication et la gestion d'une API web en utilisant le Service de gestion des API d'Azur

Azure fournit le [Service Management API](http://azure.microsoft.com/documentation/services/api-management/) qui vous permet de publier et de gérer une API web. En utilisant cette installation, vous pouvez générer un service qui agit d'une façade pour un ou plusieurs web APIs. Le service lui-même est un service web évolutif que vous pouvez créer et configurer en utilisant le portail Azure Management. Ce service vous permet de publier et de gérer une API web comme suit :

1. Déployer le web API sur un site Web, service nuage bleu azur ou Azur machine virtuelle.

2. Connectez le service de gestion API au web API. Les requêtes envoyées à l'URL de l'API de gestion sont mappés aux URI dans l'API web. Le même service de gestion API peut router les demandes vers plus d'un web API. Cela vous permet d'agréger plusieurs web API dans un service de gestion unique. De même, la même API web peut être référencée depuis plus d'un service de gestion API si vous devez restreindre ou partitionner les fonctionnalités disponibles pour différentes applications.

	> [AZURE. REMARQUE] Les URI en HATEOAS liens générés dans le cadre de la réponse pour les requêtes HTTP GET doivent référencer l'URL du service de gestion de la API et pas le serveur web qui héberge l'API web.

3. Pour chaque API web, spécifier les opérations HTTP que le web API expose ainsi que tous les paramètres facultatifs qu'une opération peut prendre comme entrée. Vous pouvez également configurer si le service de gestion API doit mettre en cache la réponse reçue de l'API web afin d'optimiser ses demandes répétées pour les mêmes données. Enregistrer les détails des réponses HTTP qui peut produire de chaque opération. Ces informations sont utilisées pour générer la documentation pour les développeurs, il est donc important qu'elle est exacte et complète.

	Vous pouvez soit définir des opérations manuellement en utilisant les assistants fournis par le portail de gestion Azure, ou vous pouvez les importer depuis un fichier contenant les définitions au format WAFDI ou Swagger.

4. Configurer les paramètres de sécurité pour les communications entre le service de gestion API et le serveur web qui héberge le web API. Le service de gestion API prend actuellement en charge l'authentification de base et l'authentification mutuelle à l'aide de certificats et autorisation de l'utilisateur OAuth 2.0.

5. Créer un produit. Un produit est l'unité de publication ; vous ajoutez le web API qui vous connecté auparavant au service de gestion du produit. Lorsque le produit est publié, le web API devenue disponibles pour les développeurs.

	> [AZURE. REMARQUE] Avant de publier un produit, vous pouvez également définir des groupes d'utilisateurs qui peuvent accéder au produit et ajouter des utilisateurs à ces groupes. Ce vous donne le que contrôle sur les développeurs et les applications qui peuvent utiliser le API web. Si un web QU'API est soumise à approbation, avant d'être en mesure d'y accéder, un développeur doit envoyer une demande à l'administrateur de produit. L'administrateur peut accorder ou refuser l'accès au développeur. Des développeurs existant peuvent aussi être bloqués si les circonstances changent.

6.	Configurer des stratégies pour chaque API web. Politiques régissant des aspects tels que la question de savoir si les appels interdomaines doivent être accueillis, comment faire pour authentifier les clients, si pour convertir entre JSON et XML formats de données transparente, s'il faut restreindre les appels depuis une plage IP donnée, quotas d'utilisation et s'il faut limiter le taux d'appel. Stratégies peuvent être appliquées à l'échelle mondiale à travers l'ensemble du produit, pour un seul web API dans un produit, ou pour l'utilisation d'un site web API.

Vous trouverez tous les détails décrivant la façon d'effectuer ces tâches sur le [Gestion de l'API](http://azure.microsoft.com/services/api-management/) page sur le site Web de Microsoft. Le Service de gestion des API Azure fournit également sa propre interface REST, ce qui vous permet de construire une interface personnalisée pour simplifier le processus de configuration un web API. Pour plus d'informations, visitez le [Azur gestion REST API QAnywhere](https://msdn.microsoft.com/library/azure/dn776326.aspx) page sur le site Web de Microsoft.

> [AZURE. ASTUCE] Azure fournit l'Azure Traffic Manager qui vous permet de mettre en œuvre le basculement et l'équilibrage de la charge et réduire la latence sur plusieurs instances d'un site web hébergé dans différents endroits géographiques. Vous pouvez utiliser Azure Traffic Manager en collaboration avec le Service de gestion API ; le Service de gestion API peut router les demandes vers les instances d'un site web via le gestionnaire de trafic Azure.  Pour plus d'informations, visitez le [Sur les méthodes d'équilibrage de charge de Traffic Manager](https://msdn.microsoft.com/library/azure/dn339010.aspx) page sur le site Web de Microsoft.

> Dans cette structure, si vous utilisez des noms DNS personnalisés pour vos sites web, vous devez configurer l'enregistrement CNAME approprié pour chaque site web pointer vers le nom DNS du site web d'Azur Traffic Manager.

## Soutien aux développeurs de créer des applications client
Développeurs de construire des applications clientes en général besoin de renseignements sur la façon d'accéder à l'API web et la documentation concernant les paramètres des types de données, types de retour et des codes de retour qui décrivent les différentes demandes et réponses entre le service web et l'application cliente.

### Documenter les activités de repos pour une API web
Le Service de gestion des API d'Azur comprend un portail des développeurs qui décrit les opérations reste exposées par un web API. Lorsqu'un produit a été publié, elle apparaît sur ce portail. Les développeurs peuvent utiliser ce portail pour vous inscrire pour l'accès ; l'administrateur peut ensuite approuver ou refuser la demande. Si le développeur est approuvé, ils reçoivent un abonnement clé qui est utilisée pour authentifier les appels depuis les applications qu'ils développent. Cette clé doit être fournie avec chaque appel API web dans le cas contraire, elle sera rejetée.

Ce portail fournit également :

- Documentation du produit, liste des opérations qu'il expose, les paramètres requis et les différentes réactions qui peuvent être retournées. Notez que cette information est générée à partir les détails fournis à l'étape 3 dans la liste dans la section [Une API web de publication en utilisant le Service de gestion API Microsoft Azure](#publishing-a-web-API).

- Extraits de code qui montrent comment faire pour appeler des opérations de plusieurs langues, y compris le JavaScript, c#, Java, Ruby, Python et PHP.

- Console de développeurs qui permet à un développeur d'envoyer une requête HTTP à tester chaque opération du produit et d'afficher les résultats.

- Une page où le développeur peut signaler des problèmes ou des problèmes trouvés.

Le portail Azure Management vous permet de personnaliser le portail des développeurs pour modifier le style et la disposition pour correspondre à l'image de marque de votre organisation.

### Implémentation d'un client SDK
Building a client application that invokes REST requests to access a web API requires writing a significant amount of code to construct each request and format it appropriately, send the request to the server hosting the web service, and parse the response to work out whether the request succeeded or failed and extract any data returned. To insulate the client application from these concerns, you can provide an SDK that wraps the REST interface and abstracts these low-level details inside a more functional set of methods. A client application uses these methods, which transparently convert calls into REST requests and then convert the responses back into method return values. This is a common technique that is implemented by many services, including the Azure SDK.

Création d'un kit de développement logiciel côté client est une entreprise considérable car elle a été appliquée de manière cohérente et testés avec soin. Cependant, une grande partie de ce processus peut être faite à la mécanique, et de nombreux fournisseurs fournissent des outils qui peuvent d'automatiser bon nombre de ces tâches.

## Une API web de surveillance

Selon comment vous avez publié et déployé votre web API, vous pouvez surveiller le web API directement, ou vous pouvez rassembler les informations de son utilisation et de la santé en analysant le trafic qui passe par le service de gestion de l'API.

### Une API web de surveillance directement
Si vous avez implémenté votre web API en utilisant le modèle ASP.NET Web API (que ce soit comme un projet Web API ou un rôle Web dans un service de cloud Azure) et Visual Studio 2013, vous pouvez collecter la disponibilité, les performances et les données d'utilisation à l'aide de ASP.NET Application Insights. Aperçus de l'application est un paquet qui transparente suit et enregistre des informations sur les requêtes et les réponses lorsque l'API web est déployé dans le nuage ; une fois que le paquet est installé et configuré, vous n'avez pas besoin de modifier n'importe quel code dans votre site web API pour l'utiliser. Lorsque vous déployez l'API web vers un site web d'Azur, tout le trafic est examiné et les statistiques suivantes sont réunies :

- Temps de réponse de serveur.

- Nombre de requêtes du serveur et les détails de chaque demande.

- Le haut de la page demande plus lent en termes de temps de réponse moyen.

- Les détails de toutes les demandes ayant échouées.

- Le nombre de sessions ouvertes par les différents navigateurs et les agents utilisateurs.

- Le plus souvent vu pages (principalement utiles pour les applications web et non web API).

- Les rôles d'utilisateur différente qui accèdent au web API.

Vous pouvez afficher ces données en temps réel à partir du portail Azure Management. Vous pouvez également créer des webtests qui surveillent la santé de l'API web. Un webtest envoie une demande périodique à un URI spécifié dans l'API web et capture la réponse. Vous pouvez spécifier la définition d'une réponse réussie (par exemple, code d'état HTTP 200), et si la requête ne retourne pas de cette réponse, vous pouvez organiser pour une alerte être envoyé à un administrateur. Si nécessaire, l'administrateur peut redémarrer le serveur qui héberge le web API si elle a échoué.

Le [Demande Insights - commencer à surveiller la santé de votre application et d'utilisation](app-insights-start-monitoring-app-health-usage/) page sur le site Web Microsoft fournit plus d'informations.

### Suivi d'une API web via le Service de gestion API

Si vous avez publié votre site web API en utilisant le service de gestion de l'API, la page de gestion de l'API sur le portail Azure Management contient un tableau de bord qui vous permet d'afficher la performance globale du service. La page Analytique permet de descendre dans les détails de comment le produit est utilisé. Cette page contient les onglets suivants :

- **Son utilisation**. Cet onglet fournit des informations sur le nombre d'appels API et de la bande passante utilisée pour traiter ces appels au fil du temps. Vous pouvez filtrer les détails sur l'utilisation de produit, API et le fonctionnement.

- **Santé**. Ceci permet d'onglet vous afficher les résultats des requêtes de l'API (les codes d'état HTTP retournés), l'efficacité de la politique de mise en cache, le temps de réponse d'API et le temps de réponse du service. Encore une fois, vous pouvez filtrer les données de santé par produit, API et le fonctionnement.

- **Activité**. Cet onglet fournit un résumé du texte des numéros d'appels réussis, n'a pas appelés, bloqué les appels, temps de réponse moyen et temps de réponse pour chaque produit, API web et le fonctionnement. Cette page répertorie également le nombre d'appels effectués par chaque développeur.

- **En un clin d'oeil**. Cet onglet affiche un résumé des données performance, y compris les développeurs responsables des plupart des appels API et les produits, web API, et des opérations qui ont reçu ces appels.

Vous pouvez utiliser ces informations pour déterminer si une opération ou un particulier web API provoque un engorgement, et si nécessaire, ajoutez d'autres serveurs et adapte l'environnement hôte. Vous pouvez également déterminer si une ou plusieurs applications utilisent un volume disproportionné des ressources et d'appliquent les politiques appropriées pour définir des quotas et limiter le taux d'appel.

> [AZURE. REMARQUE] Vous pouvez modifier les détails d'un produit publié, et les modifications sont appliquées immédiatement. Par exemple, vous pouvez ajouter ou supprimer une opération d'une API web sans nécessiter que vous republiez le produit qui contient l'API web.

## Modèles liés
- Le [façade](http://en.wikipedia.org/wiki/Facade_pattern) modèle décrit comment fournir une interface à une API web.

## Plus d'informations
- La page [En savoir plus sur ASP.NET Web API](http://www.asp.net/web-api) sur Microsoft site Web fournit une introduction détaillée à la création de services web RESTful en utilisant l'API de Web.
- La page [Routage dans ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sur Microsoft site Web décrit comment axée sur la convention de routage fonctionne dans le cadre de l'API Web ASP.NET.
- Pour plus d'informations sur le routage basé sur l'attribut, consultez la page [Attribut routage dans Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) sur le site Web de Microsoft.
- Le [Didacticiel de base](http://www.odata.org/getting-started/basic-tutorial/) page sur le site OData fournit une introduction aux fonctionnalités du protocole OData.
- Le [ASP.NET Web API OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) page sur le site Web de Microsoft contient des exemples et plus d'informations sur l'implémentation d'une API web de OData en utilisant ASP.NET.
- La page [Lance le Batch Support API Web et Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) sur Microsoft site Web décrit comment implémenter des opérations par lots dans une API web à l'aide de OData.
- L'article [Idempotence Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) le blog de Jonathan Oliver donne un aperçu de l'idempotence, et comment il se rapporte aux opérations de gestion de données.
- Le [Définitions des codes d'État](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) page sur le site Web W3C contient une liste complète des codes d'état HTTP et leurs descriptions.
- Pour plus d'informations sur la gestion des exceptions avec les ASP.NET Web API HTTP, visitez le [Gestion des exceptions dans ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) page sur le site Web de Microsoft.
- L'article [Gestion d'erreur Global Web API](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) sur Microsoft site Web explique comment implémenter une erreur globale de gestion et stratégie pour un web API de journalisation.
- La page [Utilisez WebJobs pour exécuter des tâches en arrière-plan dans Microsoft Azure Websites](web-sites-create-web-jobs.md) sur Microsoft site Web fournit des informations et des exemples d'utilisation de WebJobs pour effectuer des opérations en arrière-plan sur un site Internet d'Azur.
- La page [Moyeux d'Azur Notification notifier les utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) sur Microsoft site Web montre comment vous pouvez utiliser un Hub de Notification Azure pour pousser les réponses asynchrones aux applications clientes.
- Le [Gestion de l'API](http://azure.microsoft.com/services/api-management/) page sur le site de Microsoft explique comment publier un produit qui fournit contrôlé et sécuriser l'accès à une API web.
- Le [Azur gestion REST API QAnywhere](https://msdn.microsoft.com/library/azure/dn776326.aspx) page sur le site de Microsoft explique comment utiliser l'API REST de gestion API pour créer des applications de gestion personnalisées.
- Le [Sur les méthodes d'équilibrage de charge de Traffic Manager](https://msdn.microsoft.com/library/azure/dn339010.aspx) page sur le site Web Microsoft résume comment Azure Traffic Manager peut être utilisé pour équilibrer la charge des demandes sur plusieurs instances d'un site Web, hébergement web API.
- Le [Demande Insights - commencer à surveiller la santé de votre application et d'utilisation](app-insights-start-monitoring-app-health-usage.md) page sur le site Web de Microsoft fournit des informations détaillées sur l'installation et la configuration des aperçus de l'Application dans un projet ASP.NET Web API.
- La page [Vérifier le Code à l'aide de Tests unitaires](https://msdn.microsoft.com/library/dd264975.aspx) sur Microsoft site Web fournit des informations détaillées sur la création et la gestion des tests unitaires à l'aide de Visual Studio.
- La page [Exécuter des tests de performance sur une demande avant une sortie](https://msdn.microsoft.com/library/dn250793.aspx) sur Microsoft site Web décrit comment utiliser Visual Studio Ultimate pour créer une performance web et projet de test de charge.
