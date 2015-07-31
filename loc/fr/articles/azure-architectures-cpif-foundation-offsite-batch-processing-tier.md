<properties 
   pageTitle="Offsite Batch Processing Tier (Azure Architecture Patterns)" 
   description="The Offsite Batch Processing Tier pattern is part of the Infrastructure area, which is described extensively in the CPIF Architecture document." 
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

# Hors site Batch Processing Tier (modèles d'Architecture d'Azur)

Le [Cadres d'intégration Cloud plate-forme (FIPC)](azure-architectures-cpif-overview.md) apporte la charge de travail pour l'intégration des applications dans une Solution de Cloud Microsoft intégration.  

FIPC décrit comment les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail axés sur le Cloud grâce aux capacités hybrid cloud plate-forme et de la gestion d'Azure, System Center et Windows Server. 

Le **Niveau de traitement hors site par lots** modèle fait partie de la **Infrastructure** zone, qui est abondamment décrite dans le document Architecture du FIPC. 

##  Niveau de traitement hors site par lots

La couche de traitement hors site lot caractéristiques de conception détails modèle l'azur et services nécessaires à la prestation de traitement de données back-end qui est les deux faute tolérant et évolutive.  Ces services sont réalisés comme les rôles de travail dans les services cloud Azure, qui actuellement peut être déployée sur n'importe quel centre de données Azure.   

Charges de traitement par lots sont uniques, car ils fournissent en général peu ou pas l'interface utilisateur.  Un exemple de ce type de charge sur les lieux de travail serait un Service Windows s'exécutant sur Windows Server.  Lors de l'examen de ce type de charge de travail dans un environnement de Cloud Computing, il serait inutile de déployer un serveur complet pour exécuter une charge de travail, quand il est vraiment nécessaire calculer, connectivité réseau et de stockage.  Le rôle du travailleur est la mise en œuvre de la présente sur Azure. 

Par définition, un lot traitement travail exécuté dans Azure est une charge de travail qui se connecte à une ressource, fournit une logique métier (informatique) et fournit une sortie.  Les ressources d'entrée et de sortie sont définies par l'utilisateur et peuvent varier de fichiers plats, blobs dans stockage blob Azure, une base de données NoSQL ou bases de données relationnelles.   

La logique métier est implémentée dans un rôle de travailleur Azur, généralement en définissant la logique métier requise dans une bibliothèque .NET.  Alors que le déploiement d'un rôle de travailleur sur Azure est une opération simple, un rôle de travail qui est faute tolérant et évolutive le déploiement nécessite une conception qui prend en considération, comment le service est exécuté et entretenu dans Azure.  Ce modèle décrira en détail la conception qui estime que ces exigences et décrit comment ils peuvent être mis en œuvre. 

## Modèle architectural Overview 

Ce document décrit un modèle pour le traitement par lots hors site utilisant des instances de rôle de travailleur contenues dans un service de cloud dans Azure.  Les pièces essentielles à cette conception sont indiqués ci-dessous.  Ce diagramme illustre les instances minimales nécessaires pour atteindre la tolérance aux pannes.  Des instances supplémentaires peuvent être déployés pour améliorer les performances du service.  En outre, AutoScaling peut être activé pour aider à plus grande échelle les instances de temps ou de paramètres serveur supplémentaires. 

##  Ressources supplémentaires
[Niveau de traitement par lots (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Voir aussi
[Architecture de la FIPC](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Charge globale équilibrée Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Charger la couche données équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Réseaux hybrides](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Niveau de recherche Azur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

