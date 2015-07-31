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

# Niveau de données multisites (modèles d'Architecture d'Azur)

Le [Cadres d'intégration Cloud plate-forme (FIPC)](azure-architectures-cpif-overview.md) apporte la charge de travail pour l'intégration des applications dans une Solution de Cloud Microsoft intégration. 

FIPC décrit comment les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail axés sur le Cloud grâce aux capacités hybrid cloud plate-forme et de la gestion d'Azure, System Center et Windows Server. 

Le **Couche données multi-sites** modèle fait partie de la **Fondation** zone, qui est abondamment décrite dans le document Architecture du FIPC. 

## Couche données multi-sites

La couche de données sur plusieurs sites caractéristiques de conception détails modèle l'azur et services nécessaires à la prestation des services de la couche données pouvant fournir une haute disponibilité et des performances prévisibles au-delà des frontières géographiques. Pour l'application de ce modèle de design, une couche de données est définie comme un niveau de service fournissant services de plate-forme de données traditionnels en soit de manière isolée ou dans le cadre d'une application multi-niveaux.  Dans ce modèle, l'équilibrage de la charge de la couche données est fourni tant localement dans la région et dans toutes les régions.   

Introduite avec SQL Server 2012, groupes de disponibilité de AlwaysOn est une fonction haute disponibilité et de reprise après sinistre qui repose entièrement sur les Services d'Infrastructure Azure.  On trouvera des informations détaillées et l'annonce officielle de soutien pour les groupes de disponibilité de AlwaysOn sur Windows Azure Service d'Infrastructure dans l'article de groupes de disponibilité de AlwaysOn.   

Ce document fournit une vue d'ensemble architectural d'une couche de données multisites dans Azure utilisant les groupes de disponibilité de AlwaysOn SQL. Avec un option lecture seule nœud secondaire dans un datacenter Azur supplémentaire pour d'autre fonctionnalités et la reprise. À l'aide de SQL AlwaysOn dans Azure fournit un niveau de haute disponibilité de données pouvant être consommée par les niveaux web ou application.  

Alors que ce document met l'accent sur les pratiques et les modèles architecturaux, instructions de déploiement complète se trouvent dans les tutoriels officiels, qui décrivent la configuration des groupes de disponibilité de AlwaysOn dans Azure et la configuration de l'écouteur de groupe de disponibilité de AlwaysOn. 

## Modèle architectural Overview 

Ce document décrit un modèle permettant d'accéder au contenu de Microsoft SQL Server entre plusieurs zones géographiques aux fins de redondance et de disponibilité.  Les services essentiels sont illustrées ci-dessous sans attention à l'application ou la couche web qui accède aux données elles-mêmes.  Le diagramme suivant est une illustration simple des services pertinents et comment ils sont utilisés dans le cadre de ce modèle.   

Chacun des secteurs de service principaux sont décrits plus en détail selon le schéma. 
 
![Partie de Tags sur les ressources et lames de groupe de ressources](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Ressources supplémentaires
[Couche de données charge équilibrée (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Voir aussi
[Architecture de la FIPC](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Charge globale équilibrée Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Réseaux hybrides](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Niveau de recherche Azur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
