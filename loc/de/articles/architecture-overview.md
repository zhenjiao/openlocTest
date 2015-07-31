<properties 
	pageTitle="Application Architecture on Microsoft Azure" 
	description="Architecture overview that covers common design patterns" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Application Architektur auf Microsoft Azure
Ressourcen zum Erstellen von Anwendungen, die Microsoft Azure zu verwenden.

##Azure-architektonisches Design-Patterns
Microsoft veröffentlicht Serie architektonischen Design Patterns helfen Ihnen Ihre eigenen benutzerdefinierten Designs zu komponieren. Die Muster sollen prägnant sein architektonische Guides, die zusammen in der Reihenfolge zusammengesetzt werden können geben Hinweise, wie man am besten nutzen die Microsoft Azure-Plattform, um die Geschäftsanforderungen Ihres Unternehmens zu lösen.


[Übersicht](../azure-architectures-cpif-overview/) - 
[Hybrid-Vernetzung](../azure-architectures-cpif-infrastructure-hybrid-networking/) - 
[Offsite-Batch-Verarbeitung](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) -
[Multisite Datenebene](../azure-architectures-cpif-foundation-multi-site-data-tier/) -
[Globale Belastung ausgeglichen Webschicht](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) -
[Himmelblau-Suche-tier](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Jedes Muster enthält
 
- Eine Dienstbeschreibung
- Eine Liste der Azure Services benötigen, um das Muster zu profitieren
- Blockdiagrammen
- Architektonische Abhängigkeiten
- Design-Einschränkungen oder Überlegungen, die das Muster auswirken können
- Schnittstellen und Endpunkte
- -Anti-patterns
- Wichtige allgemeine Architektur Überlegungen einschließlich der Verfügbarkeit und Ausfallsicherheit, zusammengesetzte SLAs für bezogene Leistungen, Umfang und Leistung, Kosten und operativen Überlegungen.

##Microsoft Architecture Blueprints

Microsoft veröffentlicht eine Reihe von hohen Level-Architektur-Entwürfe zeigen, wie bestimmte Arten von Systemen zu bauen, die Microsoft-Produkte und Microsoft Azure Services umfassen. Jeder Entwurf umfasst eine 2D Visio-basierte Datei, die Sie herunterladen und bearbeiten können, ein bunter 3D PDF-Datei zum einführen, die Blaupause und ein Video, die durch die 3D-Version geht. Finden Sie unter 
[Microsoft Architecture Blueprints](http://msdn.microsoft.com/dn630664). 

Die 2D Blueprint-Diagramme benutzen die Wolke und unten genannten Unternehmen Symbol festgelegt.  

Die 3D Blueprint-PDFs werden in einem nicht-Microsoft-Tool erstellt, aber eine Visio-Vorlage ist in der Entwicklung. Finden Sie ein [BETA-Schulungsvideo der Vorlage hier.](http://aka.ms/3dBlueprintTemplate). Die Visio-3d Blueprint Vorlage Beta, e-Mail abrufen [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). 

![Microsoft Architecture Blueprint 3D Diagramm](./media/architecture-overview/BluePrintThumb.jpg)

##Cloud und Enterprise Symbol/Icon set

[Herunterladen Sie das Wolke und Enterprise Symbol/Icon set](http://aka.ms/CnESymbols) technische Materialien erstellen, die beschreiben, Azure, Windows Server, SQL Server und andere Microsoft-Produkte. Sie können sie in Architektur-Diagramme, Schulungsmaterialien, Präsentationen, Datenblätter, Infografiken, Whitepapers und sogar dritte Partei Bücher verwenden, wenn das Buch Menschen schult zu Microsoft-Produkten zu verwenden. Die Symbole sind in Visio und PNG Formate. Anweisungen zur Verwendung die PNG-Dateien in PowerPoint sind eingeschlossen. 

Die Symbolsatz Schiffe vierteljährlich und wird aktualisiert, sobald neue Dienstleistungen freigegeben werden. Wenn Sie eine Vorschau der neuesten Version, die Azure Zusatzleistungen umfassen können wollen, email [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).  

Wir würden gerne wissen, was Sie denken, so Anweisungen zum Bereitstellen von Feedback im Download gibt. Wenn Sie die Symbole verwendet haben, füllen Sie die kurze 5-Frage [Umfrage](http://aka.ms/azuresymbolssurveyv2) oder schicken Sie eine e-Mail an die Adresse oben, lassen uns wissen, wenn sie nützlich sind und wie Sie sie benutzen.  

Zusätzliche Symbole stehen in der [Microsoft Office Visio-Schablone](http://www.microsoft.com/en-us/download/details.aspx?id=35772), obwohl sie nicht für Blockdiagrammen wie der CnE ist optimiert sind.  

![Wolke und Enterprise Symbol/Icon set](./media/architecture-overview/CnESymbols.png)

##Design Muster
Microsoft Patterns &amp; Practices hat das Buch veröffentlicht [Wolke-Entwurfsmuster](http://msdn.microsoft.com/library/dn568099.aspx) auf MSDN und im PDF-Download verfügbar ist. Es gibt auch eine großformatige Poster der die Muster aufgelistet sind. 

![Patterns und Practices Wolke Muster Poster](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Architecture Infografiken
Microsoft veröffentlicht mehrere Architektur ähnliche Plakate/Infografiken. Dazu gehören [Gebäude-reale Cloud-Anwendungen](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) und [Skalierung mit Cloud-Services](http://azure.microsoft.com/documentation/infographics/cloud-services/) . 

![Azurblaue Architektur Infografiken](./media/architecture-overview/AzureArchInfographicThumb.jpg)
