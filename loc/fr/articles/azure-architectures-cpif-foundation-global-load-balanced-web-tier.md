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

# Charge globale équilibrée Web Tier (modèles d'Architecture d'Azur)

Le [Cadres d'intégration Cloud plate-forme (FIPC)](azure-architectures-cpif-overview.md) apporte la charge de travail pour l'intégration des applications dans une Solution de Cloud Microsoft intégration. 

FIPC décrit comment les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail axés sur le Cloud grâce aux capacités hybrid cloud plate-forme et de la gestion d'Azure, System Center et Windows Server. 

Le **Charge globale équilibrée Web Tier** modèle fait partie de la **Fondation** zone, qui est abondamment décrite dans le document Architecture du FIPC. 

##  Charge globale équilibrée Web Tier

La couche Global Load Balanced Web caractéristiques de conception détails modèle l'azur et services nécessaires à la prestation des services de la couche web pouvant fournir une haute disponibilité et des performances prévisibles au-delà des frontières géographiques. 

Pour l'application de ce modèle de design web tier est définie comme un niveau de service fournissant HTTP/HTTPS traditionnel contenu ou les services d'application de chaque manière isolée ou dans le cadre d'une application web à plusieurs niveaux.  Dans ce modèle, l'équilibrage de la charge de la couche web est fournie tant localement dans la région et dans toutes les régions. D'un point de vue calcul, ces services peuvent être fournis par le biais de machines virtuelles Microsoft Azure, de sites web ou une combinaison des deux.  Machines virtuelles fournissant des services web peut contenir du contenu à l'aide de toute prise en charge Microsoft Windows ou Linux commentaires d'exploitation réseau de distribution dans Microsoft Azure. 


## Modèle architectural Overview 

Ce document décrit un modèle permettant d'accéder aux services web ou le contenu du serveur web entre plusieurs zones géographiques aux fins de redondance et de disponibilité.  Les services essentiels sont illustrées ci-dessous sans attention aux contraintes de plateforme web ou méthodologie de développement au sein du service web lui-même.  Il existe deux variantes à ce modèle – qui héberge le contenu web ou des services sur des machines virtuelles (à l'aide d'Azur pris en charge les systèmes d'exploitation et la famille) et l'autre qui utilise les sites Web d'Azur.  Le diagramme suivant est une illustration simple des services pertinents et comment ils sont utilisés dans le cadre de ce modèle à l'aide de l'exemple des machines virtuelles.   

##  Ressources supplémentaires
[Global Load Balanced Web Tier (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

## Voir aussi
[Architecture de la FIPC](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Charger la couche données équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Réseaux hybrides](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Niveau de recherche Azur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

