<properties 
   pageTitle="Cloud Platform Integration Framework - Azure Architecture Patterns" 
   description="The Cloud Platform Integration Framework provides workload integration guidance for onboarding applications into a Microsoft Cloud Solution consisting of architectural patterns for Microsoft Azure" 
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


# Wolke Plattform Integration Framework (himmelblau Architektur Muster)

Die Cloud Platform Integration Framework (CPIF) Anleitungen Arbeitsauslastung Integration für Onboarding-Anwendungen in eine Cloud-Lösung von Microsoft. 

CPIF beschreibt, wie Organisationen, Kunden und Partnern sollten entwerfen und Bereitstellen von Cloud-bezogene Arbeitslasten, die Nutzung der Hybrid Wolke Plattform und Verwaltungsfunktionen von Azure, System Center und Windows Server. Die CPIF Domänen haben in folgenden Funktionen zerlegt worden:

![Markierungen-Teil auf Ressourcen- und Ressource Gruppe klingen](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF Architektur

Sowohl öffentliche und private Cloud-Umgebungen bieten gemeinsame Elemente um den Betrieb von komplexen Arbeitsauslastungen unterstützen. Während diese Architekturen in traditionellen lokalen physische und virtualisierte Umgebungen relativ gut verstanden sind, erfordern die Konstrukte, die innerhalb der Microsoft Azure zusätzliche Planung zu rationalisieren, die Infrastruktur und Plattform-Funktionen innerhalb der öffentlichen Cloud Umgebungen gefunden. Um die Entwicklung eines gehosteten Anwendung oder Dienst in Azure unterstützen, sind eine Reihe von Mustern erforderlich gliedern die verschiedenen Komponenten benötigt, um eine bestimmte Arbeitsauslastung Lösung zu komponieren.  

Diese architektonische Muster fallen in die folgenden Kategorien:

- **Infrastruktur**– Microsoft Azure ist ein Infrastruktur - und Plattform-as-a-Service Lösung, die aus mehreren zugrunde liegenden Dienste und Funktionen besteht.  Diese Dienste können weitgehend zerlegt werden, in Rechen-, Speicher- und Netzwerk-Dienste, aber es gibt mehrere Möglichkeiten, die außerhalb dieser Definitionen fallen können.  Infrastruktur-Muster ausführlich einen bestimmten Funktionsbereich von Microsoft Azure, die erforderlich ist, um eine gegebene Dienstleistung zu einem oder mehreren Lösungen innerhalb eines gegebenen Azure Abonnements gehostet. 
- **Stiftung** – Wenn eine mehrschichtige Anwendung oder ein Dienst innerhalb von Azure zu komponieren, müssen mehrere Komponenten in Kombination verwendet werden, um eine geeignete hosting-Umgebung bereitzustellen.  Stiftung-Muster bilden einen oder mehrere Dienste von Microsoft Azure, eine Ebene der Funktionalität in einer Anwendung zu unterstützen. Dies erfordert möglicherweise die Verwendung von einem oder mehreren Bestandteilen, die in der oben beschriebenen Infrastruktur-Muster beschrieben. Beispielsweise erfordert die Darstellungsschicht einer mehrschichtigen Anwendung Compute, Netzwerk- und Storage-Funktionen in Azure funktionsfähig zu werden.  Stiftung Muster sollen im Rahmen einer bestimmten Lösung mit anderen Mustern zusammengesetzt werden.
- **Lösung** – Lösungsmuster bestehen aus Infrastruktur und/oder Stiftung Muster darstellen eine End-Anwendung oder eine Reparatur entwickelt.  Es ist geplant, dass komplexe Lösungen nicht unabhängig von anderen Mustern entwickelt werden würde.  Vielmehr sollten sie nutzen, Komponenten und Schnittstellen, die in den oben beschriebenen Muster-Kategorien definiert.    

## Azurblaue Architekturmuster Konzepte

Um die Entwicklung von Lösungsarchitekturen in Azure unterstützen, werden eine Reihe von Muster-Leitfäden für häufige Szenarien bereitgestellt.   Während diese Szenario Handbücher im Laufe der Zeit aufgebaut werden, gehören die anvisierten Muster:

- Ausgewogene Webebene Global Load 
- Ausgewogene Datenebene zu laden
- Batch-Verarbeitung-Tier
- Hybrid-Vernetzung
- Himmelblau-Suche 

##  Zusätzliche Ressourcen
[CPIF Architektur (Pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

## Siehe auch
[Ausgewogene Webebene Global Load](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Ausgewogene Datenebene zu laden](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Batch-Verarbeitung-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Himmelblau-Vernetzung](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Himmelblau-Suche](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)
