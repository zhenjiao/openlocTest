<properties
	pageTitle="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio"
	description="Overview of using Python Tools for Visual Studio to create Azure cloud services including web roles and worker roles."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="get-started-article" 
	ms.date="02/09/2015"
	ms.author="huvalo"/>




# Python-Web und Worker-Funktionen mit Python Tools 2.1 für Visual Studio

Dieses Handbuch enthält eine Übersicht zur Verwendung von Python-Web und Worker-Rollen mit [Python-Tools für Visual Studio][].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [2.1 der Python-Tools für Visual Studio][]
 - [Azure SDK-Tools für VS 2013][] oder [Azure SDK-Tools für VS-2012][]
 - [Python 2.7 32-bit][] oder [Python 3.4 32-bit][]

[AZURBLAU. ENTHALTEN [Konto-und-Websites-Notiz](../includes/create-account-and-websites-note.md)]

## Was sind Python Web und Worker Rollen?

Azurblau bietet drei Modelle zum Ausführen von Anwendungen zu berechnen: [Himmelblau-Websites][execution model-web sites], [Azurblaue virtueller Maschinen][execution model-vms], und [Azure Cloud Services][execution model-cloud services]. All three models support Python. Cloud Services, which include web and worker roles, provide *Platform as a Service (PaaS)*. Innerhalb einer Cloud-Service bietet eine Webserverrolle ein dedizierten Webservers für Internet Information Services (IIS) von Host-Front-End-Web-Anwendungen, während eine Arbeiter-Rolle asynchrone, lang andauernde oder unbefristete Aufgaben unabhängig von Benutzerinteraktionen oder Eingabe ausgeführt werden kann.

Weitere Informationen finden Sie unter [Was ist ein Cloud-Service?].

> [AZURBLAU. HINWEIS] **Looking to build a simple website?**
If your scenario involves just a simple website front-end, consider using a lightweight Azure Website. You can easily upgrade to a Cloud Service as your website grows and your requirements change. See the <a href="/develop/python/">Python Developer Center</a> for articles that cover development of Azure Websites.
<br />


## Project creation

In Visual Studio, you can select **Azure Cloud Service** in the **New Project** dialog, under **Python**.

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

In the Azure Cloud Service wizard, you can select to create new web and worker roles.

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

The worker role template comes with boilerplate code to connect to a Azure storage account or service bus.

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

You can add web or worker roles to an existing cloud service at any time.  You can choose to add existing projects in your solution, or create new ones.

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Your cloud service can contain roles implemented in different languages.  For example, you can have a Python web role implemented using Django, with Python and C# worker roles.  You can easily communicate between your roles using service bus queues or storage queues.

## Run locally

If you set your cloud service project as the startup project and press F5, the cloud service will run in the local Azure emulator.

Although PTVS supports launching in the emulator, debugging (breakpoints, etc) will not work.

To debug your web and worker roles, you can set the role project as the startup project and debug that instead.  You can also set multiple startup projects.  Right-click on the solution and select **Set StartUp Projects**.

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

## Publish to Azure

To publish, right-click on the cloud service project in the solution and select **Publish**.

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

In the settings page, select the cloud service you want to publish to.

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

You can create a new cloud service if you don't already have one available.

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

It's also useful to enable remote desktop connections to the machine(s) for debugging failures.

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

When you are done configuring settings, click **Publish**.

Some progress will appear in the output window, then you'll see the Microsoft Azure Activity Log window.

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

Deployment will take several minutes to complete, then your web and/or worker roles will be running on Azure!

## Die nächsten Schritte

For more detailed information on working with web and worker roles in Python Tools for Visual Studio, see the PTVS documentation:

- [Cloud Service Projects][]

For more details on using Azure services from your web and worker roles, such as using Azure storage or service bus, see the following guides:

- [Blob Service][]
- [Table Service][]
- [Queue Service][]
- [Service Bus Queues][]
- [Service Bus Topics][]


<!--Link references-->

[What is a Cloud Service?]: /manage/services/cloud-services/what-is-a-cloud-service/
[execution model-web sites]: fundamentals-application-models.md#WebSites
[execution model-vms]: fundamentals-application-models.md#VMachine
[execution model-cloud services]: fundamentals-application-models.md#CloudServices
[Python Developer Center]: /develop/python/

[Blob Service]: storage-python-how-to-use-blob-storage.md
[Queue Service]: storage-python-how-to-use-queue-storage.md
[Table Service]: storage-python-how-to-use-table-storage.md
[Service Bus Queues]: service-bus-python-how-to-use-queues.md
[Service Bus Topics]: service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://pytools.codeplex.com/documentation
[Cloud Service Projects]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
