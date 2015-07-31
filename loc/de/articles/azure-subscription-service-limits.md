<properties
	pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints"
	description="Provides a list of common Azure subscription and service limits, quotas, and constraints. This includes information on how to increase limits along with maximum values."
	services=""
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettem"
	editor="cgronlun"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# Himmelblau-Abo und Service-Begrenzungen, Quoten und Einschränkungen

## Übersicht

Dieses Dokument legt einige der am häufigsten verwendeten Microsoft Azure Grenzen. Beachten Sie, dass dies derzeit nicht alle Azure Dienste abdeckt. Diese Grenzwerte werden im Laufe der Zeit erweitert und aktualisiert, um mehr von der Plattform zu decken.

> [AZURBLAU. HINWEIS] Wenn Sie die oben genannten Grenze anheben wollen die **Standardlimit**Sie können [Öffnen Sie eine Support-Anfrage online-Kunden kostenlos](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Die Grenzen können nicht erhoben werden, über die **Höchstgrenze** Wert in den folgenden Tabellen. Wenn es gibt keine **Höchstgrenze** Spalte, dann die angegebene Ressource verfügt nicht über einstellbare Grenzwerte.


### Grenzen und den azurblauen Ressourcen-Manager

Es ist jetzt möglich, mehrere Azure Ressourcen in einer einzigen Azure-Ressourcengruppe zu kombinieren. Bei der Verwendung von Ressourcengruppen werden Grenzen, die einst global auf regionaler Ebene mit den Azure-Ressourcen-Manager verwaltet. Weitere Informationen über Azure Ressourcengruppen finden Sie [Verwendung von Ressourcengruppen Ihre Azure Ressourcen verwalten](resource-group-portal.md).

Im Rahmen unten wurde eine neue Tabelle hinzugefügt, um irgendwelche Unterschiede in Grenzen zu reflektieren, bei Verwendung der Azure-Ressourcen-Manager. Zum Beispiel gibt es eine **Abonnement-Grenzen** Tabelle und eine **Abonnement-Grenzen - himmelblau Ressourcenmanager** Tabelle. Wenn ein Grenzwert für beide Szenarien gilt, wird sie nur in der ersten Tabelle angezeigt. Sofern nicht anders angegeben, sind Grenzwerte global in allen Regionen.

> [AZURBLAU. HINWEIS] Es ist wichtig zu betonen, dass die Quoten für Ressourcen in Azure Ressourcengruppen pro-Region durch Ihr Abonnement zugänglich sind, und sind nicht pro-Abonnement, wie die Dienst Verwaltung Quoten sind. Kern-Quoten nutzen wir als Beispiel. Wenn Sie eine Quotenerhöhung mit Unterstützung für Kerne anfordern müssen, müssen Sie wie viele Kerne entscheiden, in welchen Regionen verwenden, und nehmen Sie dann eine bestimmte Anforderung Azure Ressourcengruppe Kern die Kontingente für die Beträge und die Regionen, die Sie möchten. Also, wenn du 30 Kerne in Westeuropa zu verwenden musst, um Ihre Anwendung auszuführen, da; Sie sollten speziell 30 Kerne in Westeuropa anfordern. Aber Sie müssen kein Kern-Kontingent in jeder anderen Region erhöhen--nur Westeuropa wird die Quote 30-Kern haben.
<!-- -->
As a result, you may find it useful to consider deciding what your Azure Resource Group quotas need to be for your workload in any one region, and request that amount in each region into which you are considering deployment. See [troubleshooting deployment issues](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) for more help discovering your current quotas for specific regions.

## Abonnement-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Abonnement-Grenzen](../includes/azure-subscription-limits.md)]

### Abonnement-Grenzen - himmelblau Ressourcenmanager

Die folgenden Einschränkungen gelten bei Verwendung des Ressourcen-Manager für Azure und Azure Ressourcengruppen. Grenzen, die nicht mit der Azure-Ressourcen-Manager geändert wurden sind nicht aufgeführt. Entnehmen Sie bitte der oben stehenden Tabelle genannten Grenzen.

[AZURBLAU. ENTHALTEN [Azure-Subscription-Limits-Azure-Resource-Manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Ressourcengruppe Grenzen

[AZURBLAU. ENTHALTEN [-Azurblau-Ressourcenbegrenzung-Gruppen](../includes/azure-resource-groups-limits.md)]


## Grenzen der virtuellen Maschinen

[AZURBLAU. ENTHALTEN [Azurblau-virtuelle-Maschinen-Grenzen](../includes/azure-virtual-machines-limits.md)]


### Virtuelle Maschinen Grenzen - himmelblau Ressourcenmanager

Die folgenden Einschränkungen gelten bei Verwendung des Ressourcen-Manager für Azure und Azure Ressourcengruppen. Grenzen, die nicht mit der Azure-Ressourcen-Manager geändert wurden sind nicht aufgeführt. Entnehmen Sie bitte der oben stehenden Tabelle genannten Grenzen.

[AZURBLAU. ENTHALTEN [Azure-Virtual-Machines-Limits-Azure-Resource-Manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Vernetzung von Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-virtuelle-Netzwerk-Grenzen](../includes/azure-virtual-network-limits.md)]


### Vernetzung von Grenzen – himmelblau Ressourcenmanager

Die folgenden Einschränkungen gelten bei Verwendung des Ressourcen-Manager für Azure und Azure Ressourcengruppen. Grenzen, die nicht mit der Azure-Ressourcen-Manager geändert wurden sind nicht aufgeführt. Entnehmen Sie bitte der oben stehenden Tabelle genannten Grenzen.

[AZURBLAU. ENTHALTEN [Azure-Virtual-Network-Limits-Azure-Resource-Manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Speicher-Limits

### Standard-Speicher-Limits 

[AZURBLAU. ENTHALTEN [Azurblau-Speichergrenzwerte](../includes/azure-storage-limits.md)]

Weitere Informationen über Konto Speicherbegrenzungen finden Sie unter [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](../articles/storage/storage-scalability-targets.md).


### Prämie von Speichergrenzwerten

[AZURBLAU. ENTHALTEN [Azure-Storage-Grenzen-Prämie-Speicher](../includes/azure-storage-limits-premium-storage.md)]


### Speicher-Limits - himmelblau Ressourcenmanager

[AZURBLAU. ENTHALTEN [Azure-Storage-Limits-Azure-Resource-Manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Cloud-Service-Grenzen

[AZURBLAU. ENTHALTEN [Azure-Cloud-Dienste-Grenzen](../includes/azure-cloud-services-limits.md)]


## App-Service-Begrenzungen - Web-Apps, Mobile Apps, API-Anwendungen, Logik-Apps

[AZURBLAU. ENTHALTEN [Azurblau-Websites-Grenzen](../includes/azure-websites-limits.md)]

## Batch-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Batch-Grenzen](../includes/azure-batch-limits.md)]


## DocumentDB Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Documentdb-Grenzen](../includes/azure-documentdb-limits.md)]


## Mobilen Einsatz Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Mobil-Engagement-Grenzen](../includes/azure-mobile-engagement-limits.md)]


## Suche-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Suche-Grenzen](../includes/azure-search-limits.md)]

Weitere Informationen über Azure Suche Grenzen sehen [Grenzen und Einschränkungen](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## SQL-Datenbank-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Sql-Datenbank-Grenzen](../includes/azure-sql-database-limits.md)]

Weitere Informationen über SQL-Datenbank finden Sie in den folgenden Themen:

 - [Azure SQL-Datenbank-Service-Tiers (Editionen)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Azure SQL-Datenbank-Service-Tiers und Leistungswerte](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Datenbank-Durchsatz-Einheit (DTU)-Quoten](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [SQL-Datenbank-Ressourcenbegrenzung](sql-database/sql-database-resource-limits.md)

## Media-Dienste-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Mediaservices-Grenzen](../includes/azure-mediaservices-limits.md)]

## Mobile Dienste Grenzen

[AZURBLAU. ENTHALTEN [Handy-Service-Bereiche](../includes/mobile-services-limits.md)]

## Service Bus Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Servicebus-Grenzen](../includes/service-bus-quotas-table.md)]

## Stream-Analytics-Grenzen

[AZURBLAU. ENTHALTEN [Stream-Analytik-Grenzen-table](../includes/stream-analytics-limits-table.md)]

## Active Directory-Grenzen

[AZURBLAU. ENTHALTEN [Service-AAD-Begrenzungen](../includes/active-directory-service-limits-include.md)]


## Azurblaue RemoteApp-Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Remoteapp-Grenzen](../includes/azure-remoteapp-limits.md)]

## StorSimple Grenzen

[AZURBLAU. ENTHALTEN [Storsimple-Grenzen-table](../includes/storsimple-limits-table.md)]


## Grenzen der operativen Einblicke

[AZURBLAU. ENTHALTEN [Einblicke-Grenzwerte](../includes/operational-insights-limits.md)]

## Sicherung der Grenzen

[AZURBLAU. ENTHALTEN [Azurblau-Backup-Grenzen](../includes/azure-backup-limits.md)]

## Website-Recovery-Grenzen

[AZURBLAU. ENTHALTEN [Website-Erholung-Grenzen](../includes/site-recovery-limits.md)]

## API-Management-Grenzen

[AZURBLAU. ENTHALTEN [API-Verwaltung-Dienst-Grenzen](../includes/api-management-service-limits.md)]

## Azurblaue Redis Cachelimits

[AZURBLAU. ENTHALTEN [Redis-Dienst-Cachelimits](../includes/redis-cache-service-limits.md)]

## Wesentliche Depot Grenzen

[AZURBLAU. ENTHALTEN [Schlüssel-Depot-Grenzen](../includes/key-vault-limits.md)]

## Multi-Faktor-Authentifizierung
[AZURBLAU. ENTHALTEN [Azurblau-Mfa-Dienst-Grenzen](../includes/azure-mfa-service-limits.md)]

## Siehe auch

[Himmelblau Grenzen und erhöht das Verständnis](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuelle Maschine und Cloud-Service-Größen für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)
