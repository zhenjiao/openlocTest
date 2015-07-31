<properties 
   pageTitle="Cloud Platform Integration Framework - Azure Architecture Patterns" 
   description="The Cloud Platform Integration Framework provides workload integration guidance for onboarding applications into a Microsoft Cloud Solution consisting of architectural patterns for Microsoft Azure" 
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


# Cadres d'intégration Cloud plate-forme (modèles d'Architecture d'Azur)

Le Cloud plate-forme Integration Framework (FIPC) apporte son intégration la charge de travail pour l'intégration des applications dans une Solution de Cloud de Microsoft. 

FIPC décrit comment les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail axés sur le Cloud grâce aux capacités hybrid cloud plate-forme et de la gestion d'Azure, System Center et Windows Server. Les domaines de la FIPC ont été décomposées en les fonctions suivantes :

![Partie de Tags sur les ressources et lames de groupe de ressources](./media/azure-architecture-cpif-overview/overview.png)

##  Architecture de la FIPC

Ces deux environnements de cloud publics et privés fournissent des éléments communs pour soutenir le fonctionnement des charges de travail complexes. Alors que ces architectures sont relativement bien connus dans les environnements physiques et virtuels locaux traditionnels, les constructions dans le Microsoft Azure exigent une planification supplémentaire rationaliser les capacités d'infrastructure et de la plate-forme trouvées dans des environnements de cloud public. Pour soutenir le développement d'un service ou une application hébergée dans Azure, une série de motifs sont nécessaires décrivant les différents composants nécessaires pour composer une Solution de charge de travail donnée.  

Ces modèles architecturaux entrent dans les catégories suivantes :

- **Infrastructure**– Microsoft Azure est une Solution d'Infrastructure - et Platform-as-a-Service, constitué de plusieurs services et capacités sous-jacentes.  Ces services peuvent largement être décomposées en services de calcul, de stockage et de réseau, mais il existe plusieurs possibilités qui peuvent tomber en dehors de ces définitions.  Modèles d'infrastructure en détail une zone fonctionnelle donnée d'Azure de Microsoft qui est tenu de fournir un service donné à une ou plusieurs Solutions hébergées dans un abonnement Azur donné. 
- **Fondation** – Lorsque vous composez une application multi-niveaux ou un service dans Azure, plusieurs composants doivent être utilisés en combinaison pour fournir un environnement d'hébergement adapté.  Patrons de la Fondation composent un ou plusieurs services de Microsoft Azure pour soutenir une couche donnée de fonctionnalités dans une application. Cela peut nécessiter l'utilisation d'un ou plusieurs composants décrits dans les modèles d'infrastructure décrites ci-dessus. Par exemple, la couche de présentation d'une application multi-tiers nécessite des capacités de calcul, de réseau et de stockage dans Azure pour devenir fonctionnels.  Patrons de la Fondation visent à se composer avec d'autres modèles dans le cadre d'une Solution donnée.
- **Solution** – Modèles de solution sont composées de profils infrastructure et/ou Fondation de représenter une demande de fin ou de service en cours d'élaboration.  Il est prévu que des solutions complexes ne seraient pas élaborées indépendamment des autres modèles.  Au contraire, ils devraient utiliser les composants et les interfaces définies dans chacune des catégories de configuration décrites ci-dessus.    

## Concepts de modèle Architectural bleu azur

Pour soutenir le développement d'architectures de Solution dans Azure, une série de guides de modèle sont fournis pour des scénarios courants.   Alors que ces guides de scénario seront construites au fil du temps, envisagé de types inclure :

- Charge globale équilibrée Web Tier 
- Charger la couche données équilibrée
- Niveau de traitement par lots
- Réseaux hybrides
- Recherche d'Azur 

##  Ressources supplémentaires
[FIPC Architecture (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

## Voir aussi
[Charge globale équilibrée Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Charger la couche données équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Mise en réseau d'Azur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Recherche d'Azur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)
