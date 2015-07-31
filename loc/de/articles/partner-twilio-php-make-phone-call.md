<properties 
	pageTitle="How to make a phone call from Twilio (PHP) - Azure" 
	description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." 
	documentationCenter="php" 
	services="" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Gewusst wie: Verwenden von Twilio in eine PHP-Anwendung auf Azure telefonieren 

Das folgende Beispiel zeigt Ihnen, wie Sie Twilio verwenden können, um einen Anruf von einer PHP-Webseite in Azure gehostet. Die resultierende Anwendung fordert den Benutzer für Anruf-Werte, wie in der folgenden Bildschirmabbildung dargestellt.

![Azurblaue Call Formular mit Twilio und PHP][twilio_php]

Sie müssen folgendermaßen vorgehen, um den Code zu verwenden, in diesem Thema:

1. Erwerben Sie eine Twilio Konto und Authentifizierung token. Informationen zum Einstieg mit Twilio bewerten Sie Preisberechnung auf [http://www.twilio.com/Pricing][twilio_pricing]. Sie können für einen Test-Account anmelden [https://www.twilio.com/try-twilio][try_twilio]. Informationen über die API von Twilio finden Sie [http://www.twilio.com/API][twilio_api].
2. Rufen Sie die Twilio-Bibliothek für PHP. Sie können es herunterladen von GitHub)[https://github.com/twilio/twilio-PHP][twilio_php_github]) oder als PEAR-Paket installieren. Weitere Informationen finden Sie unter [https://github.com/twilio/twilio-php/BLOB/Master/Readme.MD][twilio_github_readme].
3. Installieren Sie das Azure SDK für PHP. Eine Übersicht der SDK und Anweisungen zur Installation finden Sie [Richten Sie die Azure SDK für PHP][setup_php_sdk].

## Erstellen eines Webformulars für einen Anruf

The following HTML code shows how to build a web page (**callform.html**) that retrieves user data for making a call:

    <html>
	<head>
		<title>Automated call form</title>
	</head>
	<body>
	<h1>Automated Call Form</h1>
 	<p>Fill in all fields and click <b>Make this call</b>.</p>
  	<form action="makecall.php" method="post">
   	<table>
     	<tr>
       		<td>To:</td>
       		<td><input type="text" size=50 name="callTo" value=""></td>
     	</tr>
     	<tr>
       		<td>From:</td>
       		<td><input type="text" size=50 name="callFrom" value=""></td>
     	</tr>
     	<tr>
       		<td>Call message:</td>
       		<td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
     	</tr>
     	<tr>
       		<td colspan=2><input type="submit" value="Make this call"></td>
     	</tr>
   	</table>
 	</form>
 	<br/>
	</body>
	</html>

## Create the code to make the call
The following code shows how to build a web page (**makecall.php**) which is called when the user submits the form displayed by **callform.html**. The code shown below creates the call message and generates the call. (Use your Twilio account and authentication token instead of the placeholder values assigned to **$sid** und **$token** in the code below.)

    <html>
	<head><title>Making call...</title></head>
	<body>
	<p>Your call is being made.</p>

	<?php
	require_once 'Services/Twilio.php';

	$sid = "your_account_sid";
	$token = "your_authentication_token";

	$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
	$to_number = $_POST['callTo'];
	$message = $_POST['callText'];

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	$call = $client->account->calls->create(
		$from_number, 
		$to_number,
  		'http://twimlets.com/message?Message='.urlencode($message)
	);

	echo "Call status: ".$call->status."<br />";
	echo "URI resource: ".$call->uri."<br />";
	?>
	</body>
	</html>

In addition to making the call, **makecall.php** displays some call metadata (example shown in screenshot below). For more information about call metadata, see [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure Call Response Using Twilio and PHP][twilio_php_response]

## Run the application
The next step is to deploy your application to Azure Websites. The following articles contain the information for creating a website and deploying your code with Git, FTP, or WebMatrix (though not all information in each article is relevant):

* [Create a PHP-MySQL Azure Web Site and deploy using Git][website-git]
* [Create a PHP-MySQL Azure Web Site and Deploy Using FTP][website-ftp]
* [Create and deploy a PHP-MySQL Azure Web Site using WebMatrix][website-webmatrix]

## Die nächsten Schritte
This code was provided to show you basic functionality using Twilio in PHP on Azure. Before deploying to Azure in production, you may want to add more error handling or other features. For example:

* Instead of using a web form, you could use Azure storage blobs or SQL Database to store phone numbers and call text. For information about using Azure storage blobs in PHP, see [Using Azure Storage with PHP Applications][howto_blob_storage_php]. For information about using SQL Database in PHP, see [Using SQL Database with PHP Applications][howto_sql_azure_php].
* Die **makecall.php** code uses Twilio-provided URL ([http://twimlets.com/message][twimlet_message_url]) to provide a Twilio Markup Language (TwiML) response that informs Twilio how to proceed with the call. For example, the TwiML that is returned can contain a `<Say>` verb that results in text being spoken to the call recipient. Instead of using the Twilio-provided URL, you could build your own service to respond to Twilio's request; for more information, see [Verwenden Sie Twilio für Sprach- und SMS-Funktionen in PHP][howto_twilio_voice_sms_php]. More information about TwiML can be found at [http://www.twilio.com/docs/api/twiml][twiml], and more information about `<Say>` and other Twilio verbs can be found at [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Read the Twilio security guidelines at [https://www.twilio.com/docs/security][twilio_docs_security].

For additional information about Twilio, see [https://www.twilio.com/docs][twilio_docs].

## Siehe auch
* [Verwenden Sie Twilio für Sprach- und SMS-Funktionen in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php
