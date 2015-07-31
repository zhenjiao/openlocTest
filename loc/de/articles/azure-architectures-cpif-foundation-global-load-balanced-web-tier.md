<properties 
   pageTitle="Global Load Balanced Web Tier (Azure Architecture Patterns)" 
   description="The Global Load Balanced Web Tier pattern is part of the Foundation area, which is described extensively in the CPIF Architecture document." 
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

# Globale Belastung ausgeglichen Webebene (himmelblau Architektur Muster)

Die [Wolke Plattform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) Arbeitslast Integration-Anleitungen für Onboarding-Anwendungen in eine Cloud-Lösung von Microsoft. 

CPIF beschreibt, wie Organisationen, Kunden und Partnern sollten entwerfen und Bereitstellen von Cloud-bezogene Arbeitslasten, die Nutzung der Hybrid Wolke Plattform und Verwaltungsfunktionen von Azure, System Center und Windows Server. 

Die **Ausgewogene Webebene Global Load** Muster ist Teil der **Stiftung** Fläche, die im Dokument CPIF Architektur ausführlich beschrieben wird. 

##  Ausgewogene Webebene Global Load

Die Global Load Balanced Web-Tier design Muster Informationen die Azure Features und Dienste benötigt, um die Dienste von Web-Ebene zu erschließen, die vorhersagbare Performance und hohe Verfügbarkeit über geografische Grenzen hinweg bieten können. 

Für die Zwecke dieses Entwurfsmuster ein Web ist Ebene als Ebene des Dienstes die traditionelle HTTP/HTTPS Inhalte bereitstellen oder Anwendungsdienste entweder eine isolierte Weise oder als Teil einer mehrstufigen Webanwendung definiert.  Innerhalb dieses Musters ist Load balancing von der Webebene sowohl lokal innerhalb der Region und über die verschiedenen Regionen zur Verfügung gestellt. Aus Sicht des Compute können diese Dienste über Microsoft Azure virtuelle Maschinen, Websites oder einer Kombination aus beidem erfolgen.  Virtuelle Maschinen, die Bereitstellung von Web-Services können Inhalte über alle unterstützten Microsoft Windows oder Linux Verteilung Gastbetriebssystem innerhalb Microsoft Azure hosten. 


## Architekturmuster Überblick 

Dieses Dokument beschreibt ein Muster für den Zugriff auf Webservices oder Web-Server-Inhalte über mehrere Regionen im Sinne der Verfügbarkeit und Redundanz.  Wichtige Dienste sind im folgenden ohne Aufmerksamkeit zu Web-Plattform-Einschränkungen oder Entwicklungsmethodik innerhalb des Web-Dienstes selbst dargestellt.  Es gibt zwei Varianten dieses Muster – eine, die die Webinhalte oder Dienste auf virtuellen Computern gehostet (mit Azure unterstützten Betriebssysteme und Familien) und die Azure-Websites verwendet.  Das folgende Diagramm ist eine einfache Darstellung von den entsprechenden Diensten und wie sie im Rahmen dieses Musters am Beispiel der virtuellen Maschinen verwendet werden.   

##  Zusätzliche Ressourcen
[Globales Load Balanced Webebene (Pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

## Siehe auch
[CPIF Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Ausgewogene Datenebene zu laden](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid-Vernetzung](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Himmelblau-Suche-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Batch-Verarbeitung-Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

