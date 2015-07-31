<properties
   pageTitle="Gain insights into your Microsoft Azure resource consumption"
   description="Provides a conceptual overview of the Azure Billing Usage and RateCard APIs, which are used to provide insights into Azure resource consumption and trends."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="07/7/2015"
   ms.author="mobandyo;bryanla"/>

# Mieux comprendre votre consommation de ressources de Microsoft Azure 

Clients et partenaires exigent la capacité de prédire avec précision et de gérer leurs coûts d'Azur.  Comme ils passent d'un Capex à un modèle Opex, ils doivent également pouvoir faire showback vs analyse de rétrofacturation, ainsi que fournir la fidélité de la mode dans l'estimation et la facturation, en particulier pour les déploiements de grand nuage. 

L'utilisation des ressources d'Azur et taux carte API discutaient dans cette adresse article ces besoins, en permettant de mieux comprendre votre consommation de ressources d'Azur.  

## Introduction à l'utilisation des ressources d'Azur et tarifs API 

L'utilisation des ressources d'Azur et RateCard APIs sont implémentés comme un fournisseur de ressources, dans le cadre de la famille des API exposées par le gestionnaire de ressources d'Azur.  

### Utilisation des ressources d'Azur API (aperçu)
Clients et partenaires peuvent utiliser l'API d'utilisation de ressource Azure pour obtenir leurs données de consommation estimée de bleu azur. Les caractéristiques comprennent :
	
- **Azur Role-based Access Control** -Clients et partenaires peuvent configurer leurs stratégies d'accès sur le [Aperçu d'Azur portail](https://portal.azure.com) ou par l'intermédiaire [Les applets de commande PowerShell Azur](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications peuvent accéder à des données d'utilisation de l'abonnement. Les appelants doivent utiliser standards jetons Azure Active Directory pour l'authentification. L'appelant doit également être ajouté au rôle soit le lecteur, le propriétaire ou le contributeur pour avoir accès à des données d'utilisation pour un abonnement Azur particulier.

- **Agrégations horaires ou journalier** -Appelants peuvent spécifier s'ils veulent que leurs données d'utilisation Azure en seaux horaire ou journalier seaux. La valeur par défaut est tous les jours.

- **Métadonnées d'instance fournies (y compris les balises de ressource)** -Niveau de l'instance détails comme l'uri de la ressource entièrement qualifiée (/subscriptions/ {abonnement id} /..), ainsi que la ressource balises d'informations et de ressources groupe seront fournis dans la réponse. Ceci aidera les clients de façon déterministe et allouer par programme l'utilisation par les balises, pour des cas d'utilisation comme Croix-charge.

- **Métadonnées de ressource fournies** -Détails ressources telles que le nom de compteur, catégorie de compteur, compteur sub catégorie, unité et la région seront également passés dans la réponse, de donner le nombre d'appelants une meilleure compréhension de ce qui a été consommé. Nous travaillons aussi à aligner la terminologie de métadonnées de ressources à travers le portail Azure, Azur utilisation CSV, EA CSV et autres expériences au public, pour permettre aux clients de corréler les données à travers des expériences de facturation.

- **Utilisation pour tous les types d'offre** – Usage data will be accessible for all offer types including Pay-as-you-go, MSDN, Monetary commitment, Monetary credit, and EA among others.

### Azure Resource RateCard API (Preview)
Customers and partners can use the Azure Resource RateCard API to get the list of available Azure resources, along with estimated pricing information for each. The features include:

- **Azur Role-based Access Control** -Clients et partenaires peuvent configurer leurs stratégies d'accès sur le [Aperçu d'Azur portail](https://portal.azure.com) ou par l'intermédiaire [Les applets de commande PowerShell Azur](powershell-install-configure.md) to specify which users or applications can get access to the RateCard data. Callers must use standard Azure Active Directory tokens for authentication. The caller must also be added to either the Reader, Owner or Contributor role to get access to the usage data for a particular Azure subscription.
	
- **Support for Pay-as-you-go, MSDN, Monetary commitment, and Monetary credit offers (EA not supported)** - This API provides Azure offer-level rate information, vs. subscription-level.  The caller of this API must pass in the offer information to get resource details and rates.  As EA offers have customized rates per enrollment, we are unable to provide the EA rates at this time.

## Scenarios

Here are some of the scenarios that are made possible with the combination of the Usage and the RateCard APIs:

- **Azure spend during the month** - Customers can use the Usage and RateCard APIs in combination to get better insights into their cloud spend during the month, by analyzing the hourly and daily buckets of usage and charge estimates. 

- **Set up alerts** – Customers and partners can set up resource-based or monetary-based alerts on their cloud consumption by getting the estimated consumption and charge estimate using the Usage and the RateCard API.

- **Predict bill** – Customers and partners can get their estimated consumption and cloud spend and apply machine learning algorithms to predict what their bill would be at the end of the billing cycle.

- **Pre-consumption cost analysis** – Customers can also use the RateCard API to predict how much their bill would be if they were to move their workloads to Azure, by providing desired usage numbers. If customers have existing workloads in other clouds or private clouds, they can also map their usage with the Azure rates to get a better estimate of their estimated Azure spend. This provides an enhanced view of what can be obtained via the [Azure Pricing Calculator](http://azure.microsoft.com/pricing/calculator/), as (for example) our Billing partners provide the ability to pivot on offer and compare/contrast between different offer types beyond Pay-As-You-Go, including Monetary commitment and Monetary credit. The APIs also provide the ability to do cost estimation changes by region, enabling the type of what-if analysis required to make deployment decisions, as deploying resources in different DCs around the world can have a direct impact on total cost.

- **What-if analysis** -

	- Customers and partners can determine whether it would be more cost-effective to run their workloads in another region, or on another configuration of the Azure resource. Azure resource costs may differ based on the Azure region in which they are running, and this allows customers and partners to get cost optimizations.

	- Customers and partners can also determine if another Azure offer type gives a better rate on an Azure resource.

## Partner solutions

[Utilisation de Microsoft Azure et tarifs API permettre Cloudyn de prévoir ITFM clients](billing-usage-rate-card-partner-solution-cloudyn.md) describes the integration experience offered by Azure Billing API partner [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  This article provides detailed coverage of their experiences, including a short video which shows how an Azure customer can use Cloudyn and the Azure Billing APIs to gains insights from their Azure consumption data. 

[Nuage de Cruiser et Azure de Microsoft API intégration de facturation](billing-usage-rate-card-partner-solution-cloudcruiser.md) describes how [Cloud Cruiser's Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) works directly from the  WAP portal, enabling customers to seamlessly manage both the operational and financial aspects of their Microsoft Azure private or hosted public cloud from a single user interface.   

## Prochaines étapes
+ Check out the [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) for more details on both APIs, which are part of the set of APIs provided by the Azure Resource Manager.
+ If you would like to dive right into the sample code, check out our [Microsoft Azure Billing API Code Samples on Github](https://github.com/Azure/BillingCodeSamples).

## Learn more
+ See the [Azure Resource Manager Overview](resource-group-overview.md) article to learn more about the Azure Resource Manager.
+ For additional information on the suite of tools necessary to help in gaining an understanding of cloud spend, please refer to  Gartner article [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


