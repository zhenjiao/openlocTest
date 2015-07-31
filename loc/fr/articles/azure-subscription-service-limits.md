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

# Contraintes et limites du Service, Quotas et abonnement Azur

## Vue d'ensemble

Le présent document décrit certaines des limites plus courants Microsoft Azure. Notez que cela ne couvre pas actuellement tous les services Azure. Au fil du temps, ces limites seront développés et mis à jour pour couvrir plus de la plate-forme.

> [AZURE. REMARQUE] Si vous souhaitez augmenter la limite ci-dessus le **Limite par défaut**Vous pouvez [Ouvrez une demande de support clientèle en ligne gratuitement](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peuvent être élevés au-dessus de la **Limite maximale** valeur dans les tableaux ci-dessous. Si il n'y a aucune **Limite maximale** colonne, puis la ressource spécifiée n'a pas de limites réglables.


### Limites et le gestionnaire de ressources d'Azur

Il est maintenant possible de combiner plusieurs ressources Azure dans à un seul groupe de ressource d'Azure. Lorsque vous utilisez des groupes de ressources, limites qui étaient autrefois globales deviennent gérés au niveau régional avec le gestionnaire de ressources d'Azur. Pour plus d'informations sur les groupes de ressources Azure, consultez [À l'aide de groupes de ressources à gérer vos ressources d'Azur](resource-group-portal.md).

Dans les limites ci-dessous, une nouvelle table a été ajoutée pour tenir compte des différences dans les limites lorsque vous utilisez le gestionnaire de ressources d'Azur. Par exemple, il y a un **Limites de l'abonnement** table et un **Limites de l'abonnement - Azur Resource Manager** tableau. Lorsqu'une limite s'applique aux deux scénarios, il apparaît uniquement dans le premier tableau. Sauf indication contraire, les limites sont globaux dans toutes les régions.

> [AZURE. REMARQUE] Il est important de souligner que des quotas pour les ressources dans les groupes de ressources Azure sont par-région accessible par votre abonnement et ne sont pas par abonnement, comme le sont les quotas de gestion de service. Nous allons utiliser les quotas de base à titre d'exemple. Si vous avez besoin de demander une augmentation des quotas avec l'appui des carottes, vous devez décider combien de carottes vous voulez utiliser dans quelles régions et ensuite faire une demande spécifique pour les quotas de base Azure Resource Group pour les montants et les régions que vous souhaitez. Par conséquent, si vous devez utiliser 30 noyaux en Europe occidentale pour exécuter votre application ; vous devriez demander spécifiquement 30 noyaux en Europe de l'Ouest. Mais vous n'aurez pas un quota de base augmenter dans d'autres régions - Europe de l'Ouest seulement auront le quota de 30-core.
<!-- -->
As a result, you may find it useful to consider deciding what your Azure Resource Group quotas need to be for your workload in any one region, and request that amount in each region into which you are considering deployment. See [troubleshooting deployment issues](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) for more help discovering your current quotas for specific regions.

## Limites de l'abonnement

[AZURE. INCLURE [Azure-abonnement-limites](../includes/azure-subscription-limits.md)]

### Limites de l'abonnement - Azur Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez le gestionnaire de ressources d'Azur et les groupes de ressources de Azure. Des limites qui n'ont pas changé avec le gestionnaire de ressources Azure ne figurent pas ci-dessous. Veuillez vous reporter au tableau précédent pour ces limites.

[AZURE. INCLURE [Azure-subscription-Limits-Azure-Resource-Manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Groupe de ressources limites

[AZURE. INCLURE [Azure-ressources-groupes-limites](../includes/azure-resource-groups-limits.md)]


## Limites de Machines virtuelles

[AZURE. INCLURE [Azure-virtuel-machines-limites](../includes/azure-virtual-machines-limits.md)]


### Limites des Machines virtuelles - Azur Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez le gestionnaire de ressources d'Azur et les groupes de ressources de Azure. Des limites qui n'ont pas changé avec le gestionnaire de ressources Azure ne figurent pas ci-dessous. Veuillez vous reporter au tableau précédent pour ces limites.

[AZURE. INCLURE [Azure-Virtual-Machines-Limits-Azure-Resource-Manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Limites de réseautage

[AZURE. INCLURE [Azure-virtuel-réseau-limites](../includes/azure-virtual-network-limits.md)]


### Mise en réseau des limites – Azur Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez le gestionnaire de ressources d'Azur et les groupes de ressources de Azure. Des limites qui n'ont pas changé avec le gestionnaire de ressources Azure ne figurent pas ci-dessous. Veuillez vous reporter au tableau précédent pour ces limites.

[AZURE. INCLURE [Azure-Virtual-Network-Limits-Azure-Resource-Manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Limites de stockage

### Limites de stockage standard 

[AZURE. INCLURE [Azure-stockage-limites](../includes/azure-storage-limits.md)]

Pour plus d'informations sur les limites de compte de stockage, voir [Azure Storage évolutivité et cibles de rendement](../articles/storage/storage-scalability-targets.md).


### Limites de stockage haut de gamme

[AZURE. INCLURE [Azure-stockage-limites-premium-stockage](../includes/azure-storage-limits-premium-storage.md)]


### Limites de stockage - Azur Resource Manager

[AZURE. INCLURE [Azure-Storage-Limits-Azure-Resource-Manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Limites de Cloud Services

[AZURE. INCLURE [Azure-nuage-services-limites](../includes/azure-cloud-services-limits.md)]


## Limites de Service App - Web Apps, Apps mobiles, API Apps, Apps logique

[AZURE. INCLURE [Azure-sites Web-limites](../includes/azure-websites-limits.md)]

## Limites de lot

[AZURE. INCLURE [Azure-lot-limites](../includes/azure-batch-limits.md)]


## DocumentDB limites

[AZURE. INCLURE [Azure-documentdb-limites](../includes/azure-documentdb-limits.md)]


## Limites de l'Engagement mobile

[AZURE. INCLURE [Azure-mobile-engagement-limites](../includes/azure-mobile-engagement-limits.md)]


## Limites de recherche

[AZURE. INCLURE [Azure-Rechercher-limites](../includes/azure-search-limits.md)]

Pour plus d'informations sur les limites de recherche Azure, voir [Limites et contraintes](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Limites de base de données SQL

[AZURE. INCLURE [limites de base de données-sql-d'Azur](../includes/azure-sql-database-limits.md)]

Pour plus d'informations sur les limites de la base de données SQL, consultez les rubriques suivantes :

 - [Niveaux de Service de base de données SQL Azure (éditions)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Niveaux de Service de base de données SQL Azure et niveaux de Performance](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Quotas de base de données débit unité (DTU)](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [Limites de ressources de base de données SQL](sql-database/sql-database-resource-limits.md)

## Limites de service de presse

[AZURE. INCLURE [Azure-mediaservices-limites](../includes/azure-mediaservices-limits.md)]

## Limites de Services mobiles

[AZURE. INCLURE [Mobile-services-limites](../includes/mobile-services-limits.md)]

## Limites de service Bus

[AZURE. INCLURE [Azure-servicebus-limites](../includes/service-bus-quotas-table.md)]

## Limites de flux de données Analytique

[AZURE. INCLURE [Stream-analytique-limites-table](../includes/stream-analytics-limits-table.md)]

## Limites de l'Active Directory

[AZURE. INCLURE [AAD-service-limites](../includes/active-directory-service-limits-include.md)]


## Limites de RemoteApp Azur

[AZURE. INCLURE [Azure-remoteapp-limites](../includes/azure-remoteapp-limits.md)]

## Limites du système StorSimple

[AZURE. INCLURE [storsimple-limites-table](../includes/storsimple-limits-table.md)]


## Limites opérationnelles Insights

[AZURE. INCLURE [aperçus-limites d'utilisation](../includes/operational-insights-limits.md)]

## Limites de sauvegarde

[AZURE. INCLURE [Azure-sauvegarde-limites](../includes/azure-backup-limits.md)]

## Limites de récupération de site

[AZURE. INCLURE [site-récupération-limites](../includes/site-recovery-limits.md)]

## Limites de gestion API

[AZURE. INCLURE [API-management-service-limites](../includes/api-management-service-limits.md)]

## Limites du Cache Redis Azur

[AZURE. INCLURE [redis-cache-service-limites](../includes/redis-cache-service-limits.md)]

## Limites de la clé de voûte

[AZURE. INCLURE [clé de voûte-limites](../includes/key-vault-limits.md)]

## Authentification à facteurs multiples
[AZURE. INCLURE [Azure-mfa-service-limites](../includes/azure-mfa-service-limits.md)]

## Voir aussi

[Comprendre les augmentations et les limites d'Azur](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Machine virtuelle et Cloud Service tailles pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)
