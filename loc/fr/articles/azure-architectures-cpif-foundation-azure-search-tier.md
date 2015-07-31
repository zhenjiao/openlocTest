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

# Recherche d'Azur Tier (modèles d'Architecture d'Azur)

Le [Cadres d'intégration Cloud plate-forme (FIPC)](azure-architectures-cpif-overview.md) apporte la charge de travail pour l'intégration des applications dans une Solution de Cloud Microsoft intégration.  

FIPC décrit comment les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail axés sur le Cloud grâce aux capacités hybrid cloud plate-forme et de la gestion d'Azure, System Center et Windows Server. 

Le **Niveau de recherche Azur** modèle fait partie de la **Fondation** zone, qui est abondamment décrite dans le document Architecture du FIPC. 

##  Niveau de recherche Azur

Le niveau de recherche Azure caractéristiques de conception détails modèle l'azur et services nécessaires à la prestation des services de recherche qui peuvent fournir une haute disponibilité et des performances prévisibles au-delà des frontières géographiques et fournit un modèle architectural à l'aide d'Azur recherche en offrant une Solution de recherche.  Recherche d'Azur est un « recherche-as-a-service » construit dans Azure de Microsoft qui permet aux développeurs d'incorporer des fonctionnalités de recherche dans les applications sans avoir à déployer, gérer ou maintenir des services d'infrastructure pour fournir cette fonctionnalité aux applications. Ce modèle vise à fournir une solution répétable conçu pour être utilisé dans différentes situations et la conception. 

## Modèle architectural Overview 

Ce document décrit un modèle de noyau pour l'utilisation d'Azur recherche avec deux variantes du noyau pour démontrer la gamme architecturale du service.  Le modèle de base se compose d'Azur recherche et entourant les services Azure et vise à fournir des conseils pour créer des designs de bout en bout.  Les variations du motif, spécifiquement le Service partagé et modes d'accès concurrentiel, sont également incluses dans cette section pour fournir une orientation fondée sur des exigences différentes, les accords de niveau de Service (SLA) et les autres conditions spécifiques. 

##  Ressources supplémentaires
[Azur recherche Tier (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

## Voir aussi
[Architecture de la FIPC](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Charge globale équilibrée Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Charger la couche données équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Réseaux hybrides](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)
