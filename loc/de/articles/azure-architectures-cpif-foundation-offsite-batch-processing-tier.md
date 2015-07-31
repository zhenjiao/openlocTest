<properties 
   pageTitle="Offsite Batch Processing Tier (Azure Architecture Patterns)" 
   description="The Offsite Batch Processing Tier pattern is part of the Infrastructure area, which is described extensively in the CPIF Architecture document." 
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

# Offsite-Batch-Verarbeitung Tier (himmelblau Architektur Muster)

Die [Wolke Plattform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) Arbeitslast Integration-Anleitungen für Onboarding-Anwendungen in eine Cloud-Lösung von Microsoft.  

CPIF beschreibt, wie Organisationen, Kunden und Partnern sollten entwerfen und Bereitstellen von Cloud-bezogene Arbeitslasten, die Nutzung der Hybrid Wolke Plattform und Verwaltungsfunktionen von Azure, System Center und Windows Server. 

Die **Offsite-Batch-Verarbeitung-Tier** Muster ist Teil der **Infrastruktur** Fläche, die im Dokument CPIF Architektur ausführlich beschrieben wird. 

##  Offsite-Batch-Verarbeitung-Tier

Die Offsite Batch-Verarbeitung-Tier design Muster Informationen die Azure Features und Dienste benötigt, um die Verarbeitung von Backend-Daten zu liefern, die beide Schuld, tolerant und skalierbar ist.  Diese Dienste werden als Worker-Funktionen in Cloud-Services auf Azure, realisiert, die derzeit für jede Azure Rechenzentrum bereitgestellt werden können.   

Batch-Verarbeitung-Arbeitslasten sind einzigartig, dass sie in der Regel wenig oder gar keine Benutzeroberfläche bereitzustellen.  Ein Beispiel für diese Art von Belastung Räumlichkeiten wäre ein Windows-Dienst unter Windows Server ausgeführt.  Wenn diese Art der Arbeitsbelastung in einer Cloud-Umgebung in Betracht, wäre es Verschwendung, einen ganzen Server um eine Arbeitslast ausgeführt werden, wenn was wirklich erforderlich ist, zu berechnen ist, Speicher- und Netzwerk-Konnektivität bereitstellen.  Die Arbeiter-Rolle ist die Umsetzung dieser auf Azure. 

Per Definition ist eine Batchverarbeitungsauftrag, die in Azure ausgeführt ist eine Arbeitsauslastung, die auf eine Ressource verbindet, bietet einige Geschäftslogik (Datenverarbeitung) und bietet einige Ausgabe.  Die ein- und Ausgabe-Ressourcen werden vom Benutzer definiert und können reichen von Flatfiles, Blobs im Azure Blob-Speicher, eine NoSQL Datenbank oder relationale Datenbanken.   

Die Geschäftslogik wird in eine Rolle himmelblau Arbeitnehmer in der Regel durch die Definition der erforderlichen Business-Logik in einer .NET-Bibliothek implementiert.  Während Bereitstellung einer Arbeitnehmer-Rolle auf Azure ein einfacher Vorgang ist, erfordert die Bereitstellung einer Arbeitnehmer-Rolle, die Schuld, tolerant und skalierbar ist eine Design, die berücksichtigt, wie der Dienst ausgeführt und innerhalb Azure.  Dieses Muster wird das Design ausführlich das hält diese Anforderungen und beschreibt, wie diese umgesetzt werden können. 

## Architekturmuster Überblick 

Dieses Dokument beschreibt ein Muster für die Offsite-Stapelverarbeitung nutzen Arbeitnehmer Rolleninstanzen enthaltenen ein Cloud-Service in Azure.  Die wichtigen Komponenten für diesen Entwurf sind unten abgebildet.  Dieses Diagramm zeigt die minimalen erforderlichen Instanzen um eine höhere Fehlertoleranz zu erreichen.  Zusätzliche Instanzen können bereitgestellt werden, um die Leistung des Dienstes zu erhöhen.  Zudem kann automatische Skalierung aktiviert sein, um Unterstützung bei der Skalierung der Instanzen durch Zeit oder zusätzliche Server-Metriken. 

##  Zusätzliche Ressourcen
[Batch-Verarbeitung-Tier (Pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Siehe auch
[CPIF Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Ausgewogene Webebene Global Load](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Ausgewogene Datenebene zu laden](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid-Vernetzung](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Himmelblau-Suche-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

