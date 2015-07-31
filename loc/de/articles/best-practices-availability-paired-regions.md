<properties
	pageTitle="Improve Business Continuity with Azure Regional Pairs"
	description="Use Regional pairs to keep applications resilient during data center failures."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Verbesserung der Verfügbarkeit über Azure regionale Paare

## Azurblaue gepaarten Regionen erklärt

Azurblau ist in mehrere Regionen auf der ganzen Welt tätig. Eine Azure Geographie ist ein Gebiet der Welt, die mindestens eine Azure Region enthält. Ein himmelblau-Gebiet ist ein Gebiet innerhalb einer Geographie mit einem oder mehreren Rechenzentren.

Jede Azure Region ist gepaart mit einer anderen Region innerhalb der gleichen Geographie (mit Ausnahme von Brasilien Süden die mit einer Region außerhalb seiner Geografie gepaart ist), zusammen eine regionale Paar.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Abbildung 1 – Azure regionale paar Diagramm



| Geographie |  Gepaart Regionen |                  |
| :-------------| :-------------   | :-------------   |
| Nordamerika | North Central USA | South Central USA |
| Nordamerika | Ost USA | Nordwest-USA |
| Nordamerika | US-Osten 2 | USA-Mitte |
| Europa | Nord Europa | Westeuropa |
| Asien | Südost-Asien | Ost-Asien |
| China | Ostchina | Nord China |
| Japan | Osten Japan | West-Japan |
| Brasilien | Brasilien-Süd (1) | South Central USA |
| Australia | Australien Ost | Australien-Southeast|
| US-Regierung | US-Gov-Iowa | US-Gov-Virginia |

Tabelle 1: Zuordnung von himmelblau regionale Paaren

> (1) Brasilien Süden ist einzigartig, weil es mit einer Region außerhalb seiner eigenen Geografie gekoppelt ist. Beachten Sie, dass Brasilien Süden sekundäre Region South Central US ist aber sekundäre South Central US-Region nicht Brasilien Süden ist.

Wir empfehlen Sie replizieren Arbeitslasten über regionale Paare von Azure Isolation und Verfügbarkeit des Hotels profitieren. Beispielsweise geplante Azure Systemupdates werden sequenziell bereitgestellt (nicht gleichzeitig) zwischen den gepaarten Regionen. Das bedeutet, dass auch in dem seltenen Fall eines fehlerhaften Updates, beide Bereiche nicht gleichzeitig betroffen sein werden. Darüber hinaus wird in dem unwahrscheinlichen Fall einer breiten Ausfalls, Verwertung von mindestens eine Region aus jedem Paar priorisiert.

## Regionale paar Beispiel
Abbildung 2 unten zeigt eine hypothetische Anwendung, die das regionale Paar für Disaster Recovery verwendet. Die grünen Zahlen markieren die Kreuz-Region-Aktivitäten der drei Azure Dienste (Azure Compute, Speicher- und Datenbank) und wie sie konfiguriert sind, um über die verschiedenen Regionen zu replizieren. Die einzigartigen Vorteile der Bereitstellung von gepaarten Regionen werden durch die Orangen Zahlen hervorgehoben.


![Gepaarte Region Vorteile im Überblick](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Abbildung 2 – hypothetische Azure regionale paar

## Cross-Region-Aktivitäten
Wie in Abbildung 2, bezeichnet.

![1Green](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – Sie müssen zusätzliche Datenverarbeitungsressourcen im voraus, um sicherzustellen, dass Ressourcen in einer anderen Region während einer Katastrophe verfügbar sind, bereitstellen. Weitere Informationen finden Sie unter [Azurblaue Business Continuity technische Anleitungen](https://msdn.microsoft.com/library/azure/hh873027.aspx)

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Himmelblau-Speicher** -Geo-redundante Speicherung (GRS) ist standardmäßig so konfiguriert, wenn ein Azure Storage-Konto erstellt wird. Mit GRS werden Ihre Daten automatisch dreimal innerhalb der primären Region und drei Mal in der gepaarten Region repliziert. Weitere Informationen finden Sie unter [Azurblaue Speicheroptionen Redundanz](../storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL-Datenbanken** – Mit Azure SQL Standard Geo-Replikation können Sie Transaktionen zu einer gepaarten Region, asynchrone Replikation konfigurieren. Mit Premium-Geo-Replikation können Sie in jeder Region der Welt Datenbankreplikation; Wir empfehlen jedoch, dass Sie diese Ressourcen in einer Region, die für die meisten Disaster-Recovery-Szenarien gepaarten bereitstellen. Weitere Informationen finden Sie unter  [Geo-Replikation in SQL Azure-Datenbank](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Green](./media/best-practices-availability-paired-regions/4Green.png) **Himmelblau-Ressourcen-Manager (ARM)** -ARM bietet grundsätzlich logische Isolation von Service-Management-Komponenten in den Regionen. Dies bedeutet, dass logische Fehler in einer Region sind weniger wahrscheinlich zu einem anderen auswirken.

## Vorteile einer gepaarten Region
Wie in Abbildung 2, bezeichnet.  

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png)
**Physische Isolierung** – Wenn möglich, bevorzugt Azure mindestens 300 Meilen von Trennung zwischen Rechenzentren in einem regionalen Paar, obwohl dies nicht praktisch oder möglich in allen Regionen ist. Physische Datacenter Trennung reduziert die Wahrscheinlichkeit von Naturkatastrophen, Unruhen, Stromausfälle oder physischen Netzwerkausfälle, die die beiden Regionen gleichzeitig. Isolation ist vorbehaltlich der Einschränkungen in der Geografie (Geografie, Größe, macht/Netzwerk-Infrastruktur-Verfügbarkeit, Verordnungen usw..).  

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)
**Plattform bereitgestellten Replikation** -Einige Dienste wie Geo-redundante Speicherung bieten automatische Replikation der gepaarten Region.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png)
**Gebiet Einziehungsanordnung** – Bei einem breiten Ausfall wird die Wiederherstellung von einer Region aus jedem Paar priorisiert. Zwischen den gepaarten Regionen bereitgestellte Anwendungen werden garantiert eine der Regionen mit Priorität erholt haben. Wenn eine Anwendung über Regionen hinweg bereitgestellt wird, die nicht gekoppelt sind, kann Erholung – im schlimmsten Fall verzögert werden, die die gewählten Regionen möglicherweise die letzten beiden wiederhergestellt werden.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png)
**Sequenzielle Aktualisierungen** -Geplante Azure Systemupdates sind gepaarten Regionen nacheinander ausgerollt (nicht gleichzeitig) zur Minimierung von Ausfallzeiten, die Wirkung von Fehlern und logische Fehler in dem seltenen Fall eines schlecht-Updates.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png)
**Daten vor-Ort** – Eine Region befindet sich innerhalb der gleichen Geographie als seine Paar (mit Ausnahme von Brasilien Süden) um Daten Wohnsitz Anforderungen für Steuer und Recht Vollzugszwecken zuständig.
