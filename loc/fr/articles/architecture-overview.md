<properties 
	pageTitle="Application Architecture on Microsoft Azure" 
	description="Architecture overview that covers common design patterns" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Application architecture sur Microsoft Azure
Ressources pour construire des applications qui utilisent Microsoft Azure.

Modèles de conception architecturale ##Azure
Microsoft publie une série de modèles de conception architecturale pour vous aider à composer vos propres conceptions personnalisées. Les modèles sont destinés à être concis guides architectes qui peuvent être composées ensemble afin de guider sur la façon de meilleur effet de levier la plateforme Azure de Microsoft pour résoudre les besoins de votre organisation.


[Vue d'ensemble](../azure-architectures-cpif-overview/) - 
[Réseaux hybrides](../azure-architectures-cpif-infrastructure-hybrid-networking/) - 
[Traitement par lots hors site](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) -
[Couche données multi-sites](../azure-architectures-cpif-foundation-multi-site-data-tier/) -
[Couche de web équilibrée de la charge globale](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) -
[Niveau de recherche Azur](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Chaque motif contient
 
- Une description de service
- Une liste de Services Azure pour exploiter le modèle
- Schémas architectes
- Dépendances architecturales
- Limitations de conception ou de considérations qui peuvent influer sur le modèle
- Interfaces et points de terminaison
- Les anti-modèles
- Considérations clés architecture de haut niveau y compris disponibilité et résilience, SLA composite pour des services utilisés, échelle et performance, coût et considérations opérationnelles.

Plans d'architecture ##Microsoft

Microsoft publie un ensemble de haut niveau architecture plans montrant comment créer des types spécifiques de systèmes incluent Mirosoft produits et services Microsoft Azure. Chaque modèle inclut un fichier Visio 2D que vous pouvez télécharger et modifier, une 3D plus coloré PDF file pour présenter le plan d'action et une vidéo qui se promène à travers la version 3D. Voir 
[Microsoft Architecture Blueprints](http://msdn.microsoft.com/dn630664). 

Les diagrammes de plan 2D utilisent le nuage et entreprise symbole défini mentionnées ci-dessous.  

Les PDF de modèle 3D sont créés dans un outil autre que Microsoft, mais un modèle Visio est en cours d'élaboration. Voir un [Vidéo de formation bêta du modèle ici.](http://aka.ms/3dBlueprintTemplate). Pour obtenir la version bêta de Visio 3d modèle de plan d'action, par courriel [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). 

![Diagramme 3D Microsoft Architecture Blueprint](./media/architecture-overview/BluePrintThumb.jpg)

##Cloud et entreprise symbole/Icon set

[Télécharger le nuage et entreprise symbole/Icon set](http://aka.ms/CnESymbols) pour créer des matériaux techniques qui décrivent Azure, Windows Server, SQL Server et autres produits de Microsoft. Vous pouvez les utiliser dans les diagrammes d'architecture, des supports de formation, présentations, fiches techniques, infographie, livres blancs et même 3 livres de parti si le livre forme les gens à utiliser les produits Microsoft. Les symboles sont en Visio et PNG formats. Instructions sur la façon d'utiliser le png dans PowerPoint sont incluses. 

Le jeu de symboles de navires trimestriellement et est mis à jour comme nouveaux services sont libérés. Si vous voulez un aperçu de la toute dernière version, qui peut-être comprendre des services Azure supplémentaires, envoyer un courriel [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).  

Nous aimerions savoir ce que vous pensez, donc il ya des instructions de rétroaction dans le téléchargement. Si vous avez utilisé les symboles, remplir la petite question 5 [enquête sur les](http://aka.ms/azuresymbolssurveyv2) ou écrivez-nous à l'adresse ci-dessus pour nous faire savoir si elles sont utiles et comment vous les utilisez.  

Symboles supplémentaires sont disponibles dans la [Microsoft Office Visio stencil](http://www.microsoft.com/en-us/download/details.aspx?id=35772), bien qu'ils ne sont pas optimisés pour les schémas architectes comme l'ensemble de la CnE est.  

![Nuages et entreprise symbole/Icon set](./media/architecture-overview/CnESymbols.png)

##Design modèles
Microsoft Patterns and Practices a publié le livre [Modèles de conception de nuage](http://msdn.microsoft.com/library/dn568099.aspx) qui est disponible sur MSDN et en téléchargement PDF. Il y a aussi une affiche grand format disponible qui répertorie tous les modèles. 

![Modèles et pratiques Cloud Patterns Poster](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Architecture infographie
Microsoft publie plusieurs architecture liés affiches/infographie. Il s'agit notamment [Développement d'Applications réelles Cloud](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) et [Mise à l'échelle avec les Services en nuage](http://azure.microsoft.com/documentation/infographics/cloud-services/) . 

![Azur Architecture infographie](./media/architecture-overview/AzureArchInfographicThumb.jpg)
