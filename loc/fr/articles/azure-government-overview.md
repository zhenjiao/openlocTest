<properties 
   pageTitle="Azure Government Overview" 
   description="This article provides an overview of the Azure Government Cloud capabilities and the trustworthy design and security used to support compliance applicable to federal, state, and local government organizations and their partners. " 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="03/13/2015"
   ms.author="john.harvey@microsoft.com"/>

#  Vue d'ensemble du gouvernement de Microsoft Azure 

<p> Microsoft Azure Government is a physically and network isolated instance of Microsoft Azure.  This developers guide will provide details on the differences that application developers and administrators would need to interact and work with these seperate regions of Azure.


## <a name="Overview"></a>Vue d'ensemble

Gouvernement d'Azur est un nuage de gouvernement-Communauté (GCC) conçu pour prendre en charge des scénarios de gouvernement stratégiques nécessitant une vitesse, échelle, sécurité, conformité et économie pour les organismes gouvernementaux américains.   Il a été développé basé sur une vaste expérience de Microsoft, offrant des logiciels, sécurité, conformité et contrôles dans les autres offres de cloud de Microsoft tels que public Azure, Office 365, GCC Office 365, Microsoft CRM en ligne etc.. 

En outre, gouvernement d'Azure est conçu pour satisfaire le niveau de sécurité supérieur et respect a besoin de charges de travail sensible, dévoué, secteur Public américain a conclu dans les règlements comme risque fédéral des États-Unis et autorisation de programme de gestion (FedRAMP), ministère de la défense Enterprise Cloud Service Broker (SEBC), Criminal Justice Information Services (CJIS) stratégie de sécurité et le Health Insurance Portability et Accountability Act (HIPAA).     

Voici une vue Sommaire de l'infrastructure Azure gouvernement Cloud, tissu, services et infrastructures qui sont disponibles pour aider les organismes gouvernementaux à construire des solutions cloud hybride pour atteindre leurs objectifs.  Comme certains services ci-dessous sont uniquement disponibles en avant-première, veuillez consulter le [page des régions](http://azure.microsoft.com/regions/#services) des services comme la plus à jour qui sont généralement disponibles sont répertoriés.

![][2]

Le gouvernement d'Azur comprend les composants principaux d'Infrastructure-as-a-Service (IaaS) et Platform-as-a-Service (PaaS).  Cela comprend des infrastructures, réseau, stockage, gestion des données, la gestion des identités et beaucoup d'autres services.  Gouvernement d'Azur prend en charge les fonctionnalités grandes publics clients Azure ont tiré comme géostationnaire réplication de données et mise à l'échelle automatique. Microsoft a été reconnu comme le leader dans les deux <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> et <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">Pâques<a/> par les principaux analystes de l'industrie.

En plus de fournir les services robustes et les caractéristiques du public Azure, Azure gouvernement fournit un certain nombre de fonctionnalités pour garantir les U.S. entités gouvernementales que leurs données sont sécurisées en fournissant :

- **Instance physique et isolé du réseau** – L'environnement gouvernemental Azure est une instance totalement distincte de Microsoft Azure public et seulement utilisé par des organismes qualifiés de gouvernement US et fournisseurs de solutions.

- **Sécurité, confidentialité & Compliance** -Microsoft a mis en place son cadre solide de contrôle sécurité, confidentialité et respect plus des contrôles rigoureux supplémentaires pour répondre aux exigences de niveau plus élevés dans les niveaux d'Impact SEBC et CJIS. 

- **Stockage de données** – L'environnement gouvernemental Azure gère 2 centres de données plus de 500 milles apart. Toutes les données client géré est stocké dans les centres de données Continental United States (CONUS)

- **Personnel des États-Unis** – Azur tout gouvernement opérateurs et administrateurs sont blindés de citoyens américains.

- **Gestion des identités** – Gestion des identités au sein de l'environnement du gouvernement d'Azure est une instance distincte de l'Azure Active Directory.

- **Compliance** – Microsoft investit continuellement afin d'atteindre et de maintenir la rigueur et de changer les exigences fédérales, étatiques et locaux tels que FedRAMP, CJIS, SEBC et HIPAA pour le gouvernement américain cloud solutions. 

- **Intégration Cloud** – Gouvernement azure fournit un environnement intégré avec Office 365 gouvernement permettant une seule authentification à travers les nuages et services améliorés dont 1 to d'espace de stockage de OneDrive.

Gouvernement d'Azur permet également aux organisations de maintenir leurs investissements technologiques existants et tirer parti des avantages des services en nuage.  Étant donné que le gouvernement d'Azure est une plateforme interopérable nuage, produits et organismes techniques peuvent créer des applications qui sont plus ouvrir depuis le sol.  Les organismes peuvent choisir les outils, services, système d'exploitation, architecture et structures telles que Windows, Linux, Oracle, SharePoint, .NET, Java, PHP et Node.js, pour leurs solutions de cloud. La flexibilité de la plate-forme Azure gouvernement permet de nouvelles formes de collaboration entre organismes, développement d'applications et l'intégration.  

Organismes de gouvernement américains intéressés par les services en nuage peuvent être assurés que Azure gouvernement fournit une échelle énorme et pratiques de sécurité rigoureux pour satisfaire leurs besoins en constante évolution. 







## <a name="Features"></a> Fonctionnalités actuellement disponibles au sein du gouvernement de Microsoft Azure
Azur gouvernement a actuellement les services suivants offerts dans les régions tant nous GOV IOWA et gouvernement des États-Unis VIRGINIA :

- Machines virtuelles
- Services Cloud
- Stockage
- Active Directory
- Planificateur de
- Réseaux virtuels
- Base de données SQL
- Fichiers d'Azur
- Services de médias
- Traffic Manager
- Bus de service

Autres services sont disponibles, et plus de services seront ajoutés en permanence.  Pour obtenir la liste la plus récente des services, veuillez consulter le [page des régions](http://azure.microsoft.com/regions/#services) qui mettra en valeur chaque région disponibles et leurs services.  

Actuellement, nous GOV Iowa et nous GOV Virginia sont les centres de données soutenant le gouvernement Azure.  Veuillez consulter la page régions ci-dessus pour actuel des centres de données et services disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>Prochaines étapes

Si vous êtes intéressé à en apprendre plus et tout gouvernement Azure veuillez tirer parti de certains des liens ci-dessous.

- **<A href="http://azure.com/gov">L'acquisition et l'accès à Azure gouvernement</a>**

- **<A href="/azure-government-developer-guide">Guide du développeur gouvernement Azur</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
