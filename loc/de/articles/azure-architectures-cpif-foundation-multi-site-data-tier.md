<properties 
   pageTitle="Multi-Site Data Tier (Azure Architecture Patterns)" 
   description="The Multi-Site Data Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Multisite Datenebene (himmelblau Architektur Muster)

Die [Wolke Plattform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) Arbeitslast Integration-Anleitungen für Onboarding-Anwendungen in eine Cloud-Lösung von Microsoft. 

CPIF beschreibt, wie Organisationen, Kunden und Partnern sollten entwerfen und Bereitstellen von Cloud-bezogene Arbeitslasten, die Nutzung der Hybrid Wolke Plattform und Verwaltungsfunktionen von Azure, System Center und Windows Server. 

Die **Multisite Datenebene** Muster ist Teil der **Stiftung** Fläche, die im Dokument CPIF Architektur ausführlich beschrieben wird. 

## Multisite Datenebene

Die Multi-Site-Data-Tier design Muster Informationen die Azure Features und Dienste benötigt, um die Ebene Datendienste zu erschließen, die vorhersagbare Performance und hohe Verfügbarkeit über geografische Grenzen hinweg bieten können. Für die Zwecke dieses Entwurfsmuster ist eine Datenebene als Ebene des Dienstes die traditionelle Daten Plattform Dienstleistungen entweder eine isolierte Weise oder im Rahmen einer Multi-Tier-Anwendung definiert.  Innerhalb dieses Musters ist Load balancing von der Datenebene sowohl lokal innerhalb der Region und über die verschiedenen Regionen zur Verfügung gestellt.   

Mit SQL Server 2012 eingeführt, ist AlwaysOn Availability Groups eine Hochverfügbarkeit und Disaster-Recovery-Funktion, die vollständig auf Azure Infrastruktur-Services unterstützt wird.  Detaillierte Informationen und die offiziellen Support-Ankündigung für AlwaysOn Availability Groups auf Windows Azure Infrastruktur-Dienst finden Sie im Artikel AlwaysOn Availability Groups.   

Dieses Dokument enthält eine architektonische Übersicht über ein Multi-Site-Data-Tier in Azure SQL AlwaysOn Availability Groups nutzen. Mit ein optionaler schreibgeschützte sekundäre Knoten in eine zusätzliche Azure Datencenter für zusätzliche Funktionalität und Disaster Recovery. Mithilfe von SQL AlwaysOn in Azure bietet eine hohe Verfügbarkeit Datenebene, die von Web oder Anwendung Schichten verwendet werden kann.  

Während dieses Dokument konzentriert sich auf architektonische Patterns &amp; Practices, vollständige Bereitstellung Anleitungen in den offiziellen Tutorials finden Sie die die Konfiguration von AlwaysOn Availability Groups in Azure und die Konfiguration des Listeners AlwaysOn Verfügbarkeit Gruppe zu skizzieren. 

## Architekturmuster Überblick 

Dieses Dokument beschreibt ein Muster für den Zugriff auf Microsoft SQL Server-Inhalte über mehrere Regionen im Sinne der Verfügbarkeit und Redundanz.  Wichtige Dienste sind nachfolgend dargestellt, ohne Aufmerksamkeit auf die Anwendung oder Webebene, die die Daten selbst zugreifen.  Das folgende Diagramm ist eine einfache Darstellung der relevanten Dienste und deren Verwendung als Teil dieses Musters.   

Jeder der Hauptdienst Bereiche sind genauer nach dem Diagramm skizziert. 
 
![Markierungen-Teil auf Ressourcen- und Ressource Gruppe klingen](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Zusätzliche Ressourcen
[Laden Sie Balanced Datenebene (Pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Siehe auch
[CPIF Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Ausgewogene Webebene Global Load](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Hybrid-Vernetzung](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Himmelblau-Suche-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Batch-Verarbeitung-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
