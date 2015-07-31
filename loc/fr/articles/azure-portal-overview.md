<properties
	pageTitle="Microsoft Azure preview portal overview"
	description="Learn how to use the Microsoft Azure preview portal."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="04/28/2015"
	ms.author="dwrede"/>

# Vue d'ensemble de portail d'aperçu Microsoft Azure

Le portail de prévisualisation de Microsoft Azure est un lieu central où vous pouvez configurer et gérer vos ressources d'Azur.  Ce tutoriel va vous familiariser avec le portail et vous montrer comment utiliser certaines de ces fonctions clées :
- A **marché global** qui vous permet de parcourir des milliers d'objets de Microsoft et d'autres fournisseurs qui peuvent être achetés et/ou mis en service.
- A **expérience de navigation unifiée et évolutive** qui le rend facile de trouver les ressources vous souciez et effectuez diverses opérations de gestion.
- **Pages de gestion cohérente** (ou lames) qui vous permettent de gérer grande variété de l'Azure de services par l'entremise de manière cohérente d'exposer les paramètres, actions, facturation information, surveillance et utilisation de données sur la santé et beaucoup plus.
- A **expérience personnelle** que vous permet de que créer un écran de démarrage personnalisé qui présente les informations que vous souhaitez voir lorsque vous vous connectez.  Vous pouvez également personnaliser une des pales du management qui contiennent des tuiles.

 ![Azur UI portail Orientation][UIOrientation]

## Avant de commencer

Vous aurez besoin d'un abonnement valide Azure de passer par ce tutoriel.  Si vous n'en avez pas, alors [Inscrivez-vous pour un essai gratuit](http://azure.microsoft.com/pricing/free-trial/) aujourd'hui.  Une fois que vous avez un abonnement, vous pouvez accéder au portail [https://Portal.Azure.com].

## Comment créer une ressource

Azur a un marché avec des milliers d'articles que vous pouvez créer à partir d'un seul endroit.  Disons que vous voulez créer un nouveau Windows Server 2012 VM.  Le + nouveau hub est votre point d'entrée dans un ensemble organisé de catégories recommandés sur le marché.  Chaque catégorie possède un ensemble restreint d'éléments recommandés ainsi qu'un lien vers le marché complet qui montre toutes les catégories et les Rechercher. Pour créer ce nouveau Windows Server 2012 VM, effectuez les actions suivantes :  

1.	Windows Server 2012 est présenté, alors vous pouvez la sélectionner dans la catégorie Compute.  
2.	Remplir des intrants de base sur un formulaire.
3.	Cliquez sur « Créer » et vous êtes VM commencera à disposition immédiatement.

Le centre de notifications vous avertit lorsque votre ressource a été créée et une lame de gestion ouvrira (vous pouvez toujours accéder à des ressources plus tard).

![Catégories portails][PortalCategories]


## Comment trouver vos ressources

Vous pouvez toujours broche fréquemment consulté ressources à vos variations, mais vous pourriez avoir besoin accéder à quelque chose que vous n'accédez fréquemment.  Le hub de parcourir ci-dessous est votre façon d'accéder à l'ensemble de vos ressources.  Vous pouvez filtrer par abonnement, choisissez/redimensionnement de colonnes et accédez à des lames de gestion en cliquant sur des éléments individuels.

![Parcourir les Hub][BrowseHub]

## Comment gérer et déléguer l'accès à une ressource

De cette lame, que vous pouvez vous connecter à l'ordinateur virtuel à l'aide de bureau à distance, surveiller les principaux indicateurs de performance, contrôler l'accès à cette machine virtuelle à l'aide de l'accès basé sur les rôles (RBAC), configurer la machine virtuelle et effectuer d'autres tâches de gestion important.  Déléguer l'accès basé sur le rôle est essentiel à la gestion à l'échelle.  Cliquez sur [ici](role-based-access-control-configure.md) pour en savoir plus à ce sujet. Pour déléguer l'accès à une ressource, effectuez les actions suivantes :

1.	Accédez à vos ressources.
2.	Cliquez sur « Tous les paramètres » dans la section Essentials.
3.	Cliquez sur « Utilisateurs » dans la liste paramètres.
4.	Cliquez sur « Ajouter » dans la barre de commandes.
5.	Choisir un utilisateur et un rôle.

![Gérer une ressource][ManageResource]

## Comment personnaliser une lame de ressource

Azure préconfigure les lames pour vos ressources, mais les tuiles sur ces lames sont les vôtres au contrôle.  Vous pouvez facilement aller dans personnaliser le mode pour ajouter, supprimer, redimensionner, ou ré-arranger les tuiles. Pour personnaliser une lame, effectuez les actions suivantes :

1.	Accédez à vos ressources.
2.	Cliquez sur le «... » en haut de la lame que vous souhaitez personnaliser.
3.	Cliquez sur « Ajouter des pièces ».
4.	Commencer à glisser / déposer des pièces.  

![Personnalisation des lames][CustomizeBlades]

## Comment obtenir de l'aide

Si jamais vous avez un problème, nous sommes là pour vous.  Le portail a une page d'aide et de support qui peut vous orienter dans la bonne direction.  Selon le votre [plan d'appui](http://azure.microsoft.com/support/plans/), vous pouvez également créer des tickets de support directement dans le portail.  Après avoir créé un ticket de support, vous pouvez gérer le cycle de vie du billet au sein du portail. Vous pouvez obtenir à la page d'aide et de support en accédant à parcourir-> aide + support.  

![Aide et support][HelpSupport]

## Résumé

Passons en revue ce que vous avez appris dans ce tutoriel :
- Vous avez appris à vous inscrire, obtenir un abonnement et accédez au portail
- Tu as orienté avec le portail UI et appris comment créer et naviguer sur des ressources
- Vous avez appris à créer une ressource et parcourir les ressources
- Vous avez appris sur les lames de structure ou de gestion, et comment vous pouvez toujours gérer différents types de ressources
- Vous avez appris comment personnaliser le portail afin de mettre l'information vous vous souciez de l'avant et au centre
- Vous avez appris à contrôler l'accès aux ressources à l'aide de l'accès basé sur les rôles (RBAC)
- Vous avez appris comment obtenir aide et support

Le portail de prévisualisation de Microsoft Azure simplifie radicalement la construire et gérer vos applications dans le nuage.  Jetez un oeil à la [blog de gestion](http://azure.microsoft.com/blog/topics/management/) pour mettre à jour car nous sommes constamment [l'écoute de vos commentaires](http://feedback.azure.com/forums/223579-azure-preview-portal) et apporter des améliorations.  [Blog de ScottGu](http://weblogs.asp.net/scottgu) est un autre excellent endroit pour rechercher toutes les mises à jour d'Azur.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
