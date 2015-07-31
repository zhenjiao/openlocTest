<properties 
   pageTitle="Azure Search Tier (Azure Architecture Patterns)" 
   description="The Azure Search Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document" 
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

# Himmelblau-Suche-Tier (himmelblau Architektur Muster)

Die [Wolke Plattform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) Arbeitslast Integration-Anleitungen für Onboarding-Anwendungen in eine Cloud-Lösung von Microsoft.  

CPIF beschreibt, wie Organisationen, Kunden und Partnern sollten entwerfen und Bereitstellen von Cloud-bezogene Arbeitslasten, die Nutzung der Hybrid Wolke Plattform und Verwaltungsfunktionen von Azure, System Center und Windows Server. 

Die **Himmelblau-Suche-Tier** Muster ist Teil der **Stiftung** Fläche, die im Dokument CPIF Architektur ausführlich beschrieben wird. 

##  Himmelblau-Suche-Tier

Die Azure-Suche-Tier design Muster Informationen die Azure Funktionen und Dienstleistungen benötigt, um die Suchdienste zu liefern, die bieten vorhersagbare Performance und hohe Verfügbarkeit über geografische Grenzen hinweg und enthält ein Architekturmuster mit Azure Suche in eine Suche Lösung liefern.  Himmelblau-Suche ist eine "Suche-as-a-Service" gebaut in Microsoft Azure, die Entwicklern ermöglicht, Suchfunktionen in Anwendungen zu integrieren, ohne dass Sie bereitstellen, verwalten oder pflegen Infrastruktur-Services, um diese Fähigkeit zu Anwendungen bereitzustellen. Der Zweck dieses Musters ist eine wiederholbare Lösung zur Verwendung in unterschiedlichen Situationen und Design. 

## Architekturmuster Überblick 

Dieses Dokument beschreibt ein Kern-Muster für die Verwendung von Azure-Suche mit zwei Varianten des Kerns den architektonischen Bereich des Dienstes nachweisen.  Das Kern-Muster besteht aus Azure-Suche und die umliegenden Azure Dienste und Anleitungen zum Erstellen von End-to-End-Modelle bieten soll.  Variationen des Musters, speziell die Shared Service und Parallelität Muster, sind ebenfalls in diesem Abschnitt, basierend auf unterschiedliche Anforderungen, Service Level Agreements (SLA) und andere speziellen Auflagen Fragen enthalten. 

##  Zusätzliche Ressourcen
[Himmelblau-Suche-Tier (Pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

## Siehe auch
[CPIF Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Ausgewogene Webebene Global Load](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Ausgewogene Datenebene zu laden](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid-Vernetzung](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Batch-Verarbeitung-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
