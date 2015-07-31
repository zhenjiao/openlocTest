<properties
   pageTitle="Background jobs guidance | Microsoft Azure"
   description="Guidance on background tasks that run independently of the user interface."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Orientation de travaux de fond

![](media/best-practices-background-jobs/pnp-logo.png)


## Vue d'ensemble

Plusieurs types d'applications nécessitent des tâches de fond qui s'exécutent indépendamment de l'interface utilisateur (UI). Les exemples incluent des traitements par lots, intensifs, traitement des tâches et des processus tels que les flux de travail de longue durée. Tâches en arrière-plan peuvent être exécutées sans nécessiter l'intervention de l'utilisateur ; l'application peut commencer le travail et puis continuer à traiter des requêtes interactives des utilisateurs. Ceci peut aider à réduire la charge sur l'application interface utilisateur, qui peut améliorer la disponibilité et réduire les temps de réponse interactive.

Par exemple, si une application doit générer les miniatures des images téléchargées par les utilisateurs, il peut faire cela comme une tâche en arrière-plan et enregistrer la miniature au stockage lorsque vous avez terminé sans que les utilisateurs qui ont besoin d'attendre que le processus pour compléter. De la même manière, un utilisateur en passant une commande peut initier un flux de travail de fond qui traite de l'ordre, alors que l'interface utilisateur permet à l'utilisateur de continuer à naviguer sur le site Web. Lorsque la tâche en arrière-plan est terminée, il peut mettre à jour les données de commandes stockées et envoyer un email à l'utilisateur de confirmer la commande.

Si l'on considère que pour mettre en œuvre une tâche comme une tâche en arrière-plan, les principaux critères est si la tâche peut s'exécuter sans intervention de l'utilisateur et sans l'interface utilisateur qui ont besoin d'attendre pour le travail à effectuer. Les tâches qui requièrent l'utilisateur ou l'interface utilisateur à patienter pendant qu'ils sont terminés peuvent ne pas convenir comme des tâches en arrière-plan.

## Types de tâches en arrière-plan

Tâches en arrière-plan ont généralement un ou plusieurs des caractéristiques suivantes :

- CPU intensives emplois tels que des calculs mathématiques, analyse du modèle structurel et plus.
- I/O intensives travaux tels que l'exécution d'une série d'opérations de stockage ou d'indexation de fichiers.
- Lots par exemple les données mises à jour ou de traitement régulier.
- Flux de travail telles que l'exécution des commandes de longue durée ou fourniture des services et systèmes.
- Traitement de données sensible où la tâche est remise à un endroit plus sûr pour le traitement. Par exemple, vous pouvez voulez pas traiter des données sensibles dans un rôle de web et utilisez plutôt un motif tel que [Contrôleur d'accès](http://msdn.microsoft.com/library/dn589793.aspx) pour transférer les données vers un rôle de fond isolé qui a accès à protected storage.

## Déclencheurs

Tâches en arrière-plan peuvent être lancées de plusieurs façons différentes. Effectivement, tous d'entre eux tombent dans l'une des catégories suivantes :

- [**Pilotés par les déclencheurs des événements**](#event-driven-triggers). La tâche est lancée en réponse à un événement, généralement une mesure prise par un utilisateur ou une étape dans un workflow.
- [**Programme axé sur les déclencheurs**](#schedule-driven-triggers). La tâche est appelée sur un calendrier basé sur une minuterie. C'est peut-être une planification récurrente, ou un appel unique spécifié pour une date ultérieure

### Pilotés par les déclencheurs des événements

Pilotés par invocation des événements utilise un déclencheur pour démarrer la tâche en arrière-plan. Exemples d'utilisation de déclencheurs d'événements conduit comprennent :

- L'interface utilisateur ou un autre emploi place un message dans une file d'attente. Le message contient des données concernant une action qui a eu lieu, comme l'utilisateur de passer une commande. La tâche d'arrière-plan écoute sur cette file d'attente et détecte l'arrivée d'un nouveau message. Il lit le message et utilise les données dedans comme l'entrée de la tâche en arrière-plan.
- L'interface utilisateur ou un autre emploi enregistre ou met à jour une valeur dans le stockage. La tâche d'arrière-plan surveille le stockage et détecte les modifications apportées. Il lit les données et l'utilise comme l'entrée de la tâche en arrière-plan.
- L'interface utilisateur ou un autre emploi fait une demande à un point de terminaison, par exemple un HTTPS URI, ou une API exposée comme un service web. Il transmet les données nécessaires pour terminer la tâche en arrière-plan dans le cadre de la demande. Le point de terminaison ou au service web appelle la tâche en arrière-plan, qui utilise les données en entrée.

Des exemples typiques de tâches adaptées à l'événement piloté par invocation incluent image processing, workflows, envoi d'informations aux services à distance, envoi de messages e-mail, fourniture de nouveaux utilisateurs dans les applications multi-locataires et bien plus encore.

### Programme axé sur les déclencheurs

Invocation de calendrier conduit utilise une minuterie pour démarrer la tâche en arrière-plan. Exemples d'utilisation horaire piloté par déclencheurs :

- Un timer exécute localement au sein de l'application ou dans le cadre du système d'exploitation de l'application appelle une tâche en arrière-plan sur une base régulière.
- Une minuterie en cours d'exécution dans une autre application ou un service de minuteur comme planificateur d'Azure, envoie une requête à un service web ou API sur une base régulière. Le service web ou API appelle la tâche en arrière-plan.
- Un processus distinct ou une application démarre une minuterie qui provoque la tâche en arrière-plan est appelée une fois après un délai spécifié, ou à un moment précis.

Des exemples typiques de tâches adaptées à l'invocation de calendrier conduit incluent routines de traitement par lots telles que mise à jour listes de produits connexes pour les utilisateurs en fonction de leur comportement récent, tâches de traitement systématique des données telles que la mise à jour des index ou en générant accumulent des résultats, analyse des données pour des rapports quotidiens, nettoyage de rétention de données, contrôles de cohérence des données et plus.

Si vous utilisez un programme axé sur les tâches qui doivent s'exécuter comme une instance unique, être au courant de ce qui suit :

- Si l'instance de calcul qui exécute le planificateur (par exemple, une Machine virtuelle à l'aide de tâches planifiées de Windows) est mis à l'échelle, vous avez plusieurs instances du planificateur en cours d'exécution et que ceux-ci pourraient démarrer plusieurs instances de la tâche.
- Si les tâches s'exécutent plus longtemps que la période entre les événements de planificateur, le planificateur peut démarrer une autre instance de la tâche, tandis que l'autre est toujours en cours.

## Retour de résultats

Tâches en arrière-plan exécutent de manière asynchrone dans un processus distinct, ou même un emplacement distinct, de l'interface utilisateur ou le processus qui a appelé la tâche en arrière-plan. Idéalement, les tâches de fond sont des opérations « fire and forget », et leur progression de l'exécution n'a aucune incidence sur l'interface utilisateur ou le processus appelant. Cela signifie que le processus appelant n'attend pas pour l'achèvement des tâches et par conséquent ne peut pas détecter automatiquement lorsque la tâche se termine.
Si vous avez besoin d'une tâche de fond pour communiquer avec la tâche appelante pour indiquer la progression ou achèvement, vous devez implémenter un mécanisme pour cela. Quelques exemples sont :

- Valeur d'indicateur État écrire stockage accessible à la tâche UI ou d'appel, qui peut surveiller ou vérifier cette valeur si nécessaire. Autres données que la tâche d'arrière-plan doit renvoyer à l'appelant peuvent être placées dans la même mémoire.
- Créer une file d'attente de réponse qui écoute à l'interface utilisateur ou l'appelant. La tâche d'arrière-plan peut envoyer des messages à la file d'attente indiquant le statut et accomplissement. Les données que la tâche d'arrière-plan doit renvoyer à l'appelant peuvent être placées dans les messages. Si vous utilisez Azure Service Bus, vous pouvez utiliser le **ReplyTo** et **CorrelationId** Propriétés d'implémenter cette fonctionnalité. Pour plus d'informations, consultez [Corrélation dans le Bus de Service négocié par messagerie](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exposer un API ou un point de terminaison de la tâche en arrière-plan qui pouvez accéder à l'interface utilisateur ou l'appelant pour obtenir des informations d'État. Les données que la tâche d'arrière-plan doit renvoyer à l'appelant peuvent être incluses dans la réponse.
- Ont la tâche d'arrière-plan à rappeler à l'interface utilisateur ou l'appelant via une API pour indiquer l'État à des moments prédéfinis ou à la fin. Cela pourrait être par le biais d'événements déclenchés localement ou une publication et abonnement mécanisme. Les données que la tâche d'arrière-plan doit renvoyer à l'appelant peuvent être incluses dans la charge utile de demande ou d'un événement.

## Environnement d'hébergement

Vous pouvez héberger des tâches en arrière-plan à l'aide d'une gamme de services de la plateforme Azure différents :

- [**Sites Web d'Azur**](#azure-web-sites-and-webjobs). Vous pouvez utiliser WebJobs pour exécuter les travaux personnalisée basée sur une gamme de différents types de script ou programme exécutable dans le cadre du site Web.
- [**Azur Cloud Services web et travail des rôles**](#azure-cloud-services-web-and-worker-roles). Vous pouvez écrire du code dans un rôle qui s'exécute en tâche de fond.
- [**Azur des Machines virtuelles**](#azure-virtual-machines). Si vous avez un service Windows ou si vous souhaitez utiliser le planificateur de tâches de Windows, il est fréquent d'accueillir vos tâches en arrière-plan dans une machine virtuelle dédiée.

Les sections suivantes décrivent chacune de ces options plus en détail et des considérations pour vous aider à choisir l'option appropriée.

### Des Sites Web d'Azur et WebJobs

Vous pouvez utiliser Azure WebJobs pour exécuter les travaux personnalisés comme des tâches de fond au sein d'une application Azure Web Sites hébergés. WebJobs pouvez exécuter des scripts ou programmes exécutables dans le cadre de votre site Web comme un processus continu, ou en réponse à un événement déclencheur d'Azure planificateur ou des facteurs externes tels que les changements de blobs de stockage et les files d'attente. Emplois peuvent être démarrés et arrêtés à la demande et arrêter de façon appropriée. Si un WebJob continuellement en cours d'exécution échoue, il est automatiquement redémarré. Mesures de relance et d'erreur sont configurables.

Lorsque vous configurez un WebJob :

- Si vous souhaitez que la tâche de répondre à un événement axé sur la détente, il doit être configuré comme **Fonctionner en continu**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_données/emplois/continu.
- Si vous souhaitez que la tâche de répondre à un programme axé sur la détente, il doit être configuré comme **Run sur un calendrier**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_données/emplois/déclenché.
- Si vous choisissez le **Exécutées à la demande** option lorsque vous configurez un travail, il va exécuter le même code que le **Run sur un calendrier** option lorsque vous le démarrez.

WebJobs Azur s'exécutent dans le sandbox du site, ce qui signifie qu'ils peuvent accéder aux variables d'environnement et partager des informations telles que les chaînes de connexion avec le site. Le travail a accès à l'identifiant unique de l'ordinateur qui exécute le travail. La chaîne de connexion nommée **AzureJobsStorage** donne accès aux files d'attente de Azure storage, blobs et des tables pour les données d'application et Service de Bus pour la messagerie et de communication. La chaîne de connexion nommée **AzureJobsDashboard** fournit l'accès aux fichiers journaux action revendicative.

WebJobs Azur possèdent les caractéristiques suivantes :

- **Sécurité**: WebJobs sont protégés par les informations d'identification de déploiement du site Web.
- **Types de fichiers supportés**: WebJobs peut être définie à l'aide de scripts de commandes (.cmd), les fichiers batch (.bat), les scripts PowerShell (.ps1), bash scripts shell (.sh), PHP scripts (.php), scripts Python (.py), le code JavaScript (.js) et des programmes exécutables (.exe, .jar, etc.).
- **Déploiement**: Scripts et exécutables peuvent être déployées en utilisant le portail Azure, créé et déployé à l'aide de la [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) Add-in pour Visual Studio ou le [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs), à l'aide de la [Azur WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md), ou en les copiant directement aux endroits suivants :
  - pour déclenché l'exécution : site/wwwroot/app_données/emplois/déclenché / {nom d'emploi}
  - pour l'exécution continue : site/wwwroot/app_données/emplois/continu / {nom d'emploi}
- **Exploitation forestière**: Console.Out est réputée (marquée) INFO et Console.Error comme erreur. Surveillance et diagnostic des informations accessibles via le portail Azure, et les fichiers journaux peuvent être téléchargés directement depuis le site. Elles sont enregistrées dans les localités suivantes :
  - pour déclenché l'exécution : Vfs/données/emplois/continu/jobName
  - pour l'exécution continue : Vfs/données/emplois/déclenché/jobName
- **Configuration**: WebJobs peut être configuré à l'aide du portail, l'API REST et PowerShell. Un fichier de configuration nommé settings.job dans le même répertoire racine comme le script de travail peut être utilisé pour fournir des informations de configuration pour un emploi. Par exemple :
  - {"arrêt_attendre_temps": 60}
  - {"est_Singleton": true}

### Considérations

- Par défaut, WebJobs échelle avec le site. Toutefois, les emplois peuvent être configurés pour s'exécuter sur une instance unique en définissant la **est_Singleton** Propriété Configuration. Instance unique WebJobs sont utiles pour les tâches que vous ne souhaitez pas échelle ou exécuter en tant que multiple simultanée instances, telles que la ré-indexation, analyse des données et des tâches similaires.
- Pour minimiser l'impact des travaux sur la performance du site, pensez à créer une instance Azure Web Sites vide dans un nouveau Plan de Service App pour hôte WebJobs qui peut être longue en cours d'exécution ou de la ressource intensif.

### Plus d'informations

- [WebJobs Azur Ressources recommandées](websites-webjobs-resources/) répertorie les nombreuses ressources utiles, les téléchargements et échantillons pour WebJobs.

## Azur Cloud Services web et travail des rôles

Tâches d'arrière-plan peuvent être exécutés dans un rôle web ou dans un rôle de travail distinct. La décision que d'utiliser un travailleur rôle devrait être basée sur un examen des exigences d'élasticité et d'évolutivité, de durée de vie opérationnelle, relâchez la cadence, la sécurité, tolérance aux pannes, contention, complexité et l'architecture logique. Pour plus d'informations, consultez [Calculer le modèle de Consolidation de ressource](http://msdn.microsoft.com/library/dn589778.aspx).

Il y a plusieurs façons de mettre en œuvre des tâches d'arrière-plan dans un rôle de Services de Cloud :

- Créer une implémentation de la **RoleEntryPoint** dans le rôle de classe et ses méthodes permettent d'exécuter des tâches en arrière-plan. Les tâches exécuter dans le contexte de WaIISHost.exe et peut utiliser le **GetSetting** méthode de la **CloudConfigurationManager** classe pour charger les paramètres de configuration. Pour plus d'informations, consultez [Cycle de vie (Services en nuage)](#lifecycle-cloud-services-).
- Les tâches de démarrage permet d'exécuter des tâches en arrière-plan lorsque l'application démarre. Pour les tâches de continuer à fonctionner dans le jeu de fond de forcer le **taskType** propriété **arrière-plan** (si vous ne le faites pas, le processus de démarrage d'application sera arrêter et attendre que la tâche se termine). Pour plus d'informations, consultez [Exécuter les tâches de démarrage dans Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- Utiliser le SDK de WebJobs pour mettre en œuvre des tâches d'arrière-plan comme WebJobs qui commencent comme une tâche de démarrage. Pour plus d'informations, consultez [Débuter avec le SDK de WebJobs d'Azur](websites-dotnet-webjobs-sdk-get-started.md).
- Utiliser une tâche de démarrage pour installer un service Windows qui exécute une ou plusieurs tâches de fond. Vous devez définir le **taskType** propriété **arrière-plan** afin que le service s'exécute en arrière-plan. Pour plus d'informations, consultez [Exécuter les tâches de démarrage dans Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

### Exécution de tâches de fond dans le rôle de web

Le principal avantage de l'exécution de tâches de fond dans le rôle de web est l'économie de coûts d'hébergement parce que rien n'oblige à déployer des rôles supplémentaires.

### Exécution de tâches de fond dans un rôle de travail

Exécution de tâches de fond dans un rôle de travailleur présente plusieurs avantages :

- Il vous permet de gérer la mise à l'échelle séparément pour chaque type de rôle. Vous devrez par exemple, plusieurs instances d'un rôle de web pour supporter la charge actuelle, mais les instances moins le rôle de travailleur qui exécute des tâches en arrière-plan. Mise à l'échelle des instances de calcul de tâche de fond séparément des rôles UI peut réduire coût hébergement, tout en conservant des performances acceptables.
- Il décharge de la charge de traitement pour les tâches de fond dans le rôle du web. Le rôle de web qui fournit l'interface utilisateur peut rester réactif, et il se peut que les instances moins sont nécessaires pour justifier un volume donné de demandes d'utilisateurs.
- Il vous permet de mettre en oeuvre la séparation des préoccupations. Chaque type de rôle peut implémenter un ensemble spécifique de tâches clairement définies et connexes. Ceci rend la conception et la maintenance du code plus facile parce qu'il est moins interdépendance du code et des fonctionnalités entre chaque rôle.
- Il peut aider à isoler les données et les processus sensibles. Par exemple, les rôles de web qui implémentent l'interface utilisateur n'avez pas besoin d'avoir accès aux données sont gérés et contrôlés par un rôle de travail. Cela peut être utile pour renforcer la sécurité, surtout lorsque vous utilisez un modèle tel que le [Modèle de contrôleur d'accès](http://msdn.microsoft.com/library/dn589793.aspx).  

### Considérations

Considérer les points suivants lors du choix où et comment déployer des tâches en arrière-plan lorsque vous utilisez des rôles web et de travail des Services de Cloud :

- Hébergement des tâches d'arrière-plan dans un site web existant rôle peut faire l'économie d'un rôle de travail distincts pour ces tâches en cours d'exécution, mais il est susceptible d'affecter les performances et la disponibilité de l'application, si il existe un conflit pour le traitement et les autres ressources. À l'aide d'un rôle de travail distinct protège le rôle de web de l'impact des tâches de fond intensif longue course ou une ressource.
- Si vous hébergez des tâches en arrière-plan à l'aide de la **RoleEntryPoint** classe, vous pouvez facilement ce déplacer à un autre rôle. Par exemple, si vous créez la classe dans un rôle de web et plus tard décider de vous devez exécuter les tâches d'un rôle de travailleur, vous pouvez déplacer le **RoleEntryPoint** implémentation de la classe dans le rôle de travailleur.
- Les tâches de démarrage sont conçus pour exécuter un programme ou un script. Déploiement d'une tâche en arrière-plan sous forme de programme exécutable peut être plus difficile, surtout si elle exige également le déploiement des assemblys dépendants. Il peut être plus facile à déployer et à utiliser un script pour définir une tâche en arrière-plan lorsque vous utilisez des tâches de démarrage.
- Exceptions qui causent une tâche de fond pour ne pas ont un impact différent selon la manière qu'ils sont hébergés :
  - Si vous utilisez le **RoleEntryPoint** approche de la classe, une tâche qui a échoué provoquera le rôle à redémarrer pour que la tâche redémarre automatiquement. Cela peut affecter la disponibilité de l'application. Pour éviter cela, assurez-vous que vous incluez dans la gestion des exceptions robuste le **RoleEntryPoint** classe et toutes les tâches de fond. Utilisez le code pour redémarrer les tâches qui échouent lorsque cela est approprié et lever l'exception pour redémarrer le rôle que si vous ne pouvez pas récupérer gracieusement de l'échec dans votre code.
  - Si vous utilisez des tâches de démarrage, vous êtes responsable de la gestion de l'exécution de la tâche et en vérifiant si elle échoue.
- Gestion et suivi des tâches de démarrage sont plus difficile que l'utilisation de la **RoleEntryPoint** approche de la classe. Cependant, l'Azure WebJobs SDK inclut un tableau de bord pour le rendre plus facile à gérer WebJobs que vous initiez à travers les tâches de démarrage.

### Plus d'informations

- [Calculer le modèle de Consolidation de ressource](http://msdn.microsoft.com/library/dn589778.aspx)
- [Débuter avec le SDK de WebJobs d'Azur](websites-dotnet-webjobs-sdk-get-started/)

## Azur des Machines virtuelles

Tâches d'arrière-plan peuvent être mis en œuvre d'une manière qui les empêche d'avoir déployé sur Azure Web Sites ou Services en nuage, ou cela ne peut pas commode. Des exemples typiques sont des services Windows et utilitaires tiers et programmes exécutables. Il peut également inclure des programmes écrits pour un environnement d'exécution différent de celui hébergeant l'application ; par exemple, il peut être un programme Unix ou Linux que vous souhaitez exécuter à partir d'une application Windows ou .NET. Vous pouvez choisir parmi une gamme de systèmes d'exploitation pour un ordinateur virtuel d'Azur et exécuter votre service ou exécutable sur cette machine virtuelle.

Pour vous aider à choisir le moment d'utiliser des ordinateurs virtuels, voir [Comparaison de sites Web, Cloud Services et Machines virtuelles Azur](choose-web-site-cloud-service-vm.md). Pour plus d'informations sur les options pour [Les ordinateurs virtuels, voir Machine virtuelle et Cloud Service tailles pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Pour plus d'informations sur les systèmes d'exploitation et des images prédéfinies disponibles pour les ordinateurs virtuels, voir [Galerie des Machines virtuelles d'Azur](http://azure.microsoft.com/gallery/virtual-machines/).

Pour lancer la tâche en arrière-plan dans une autre machine virtuelle, vous avez un éventail d'options :

- Vous pouvez exécuter la tâche à la demande directement à partir de votre application en envoyant une demande à un point de terminaison qui expose la tâche, en passant toutes les données qui requiert la tâche. Ce point de terminaison appelle la tâche.
- Vous pouvez configurer la tâche s'exécutera sur un calendrier à l'aide d'un planificateur ou une minuterie disponible dans votre système d'exploitation choisi. Par exemple, sous Windows vous pouvez utiliser le planificateur de tâches Windows pour exécuter des scripts et des tâches, ou, si vous disposez de SQL Server installées sur l'ordinateur virtuel, vous pouvez utiliser l'Agent SQL Server pour exécuter des scripts et des tâches.
- Vous pouvez utiliser Azure planificateur pour initier la tâche en ajoutant un message à une file d'attente qui écoute à la tâche, ou en envoyant une demande à une API qui expose la tâche.

Voir la section précédente [Déclencheurs](#triggers) Pour plus d'informations sur comment vous pouvez lancer des tâches en arrière-plan.  

### Considérations

Considérez les points suivants lorsque vous décidez de déployer des tâches en arrière-plan dans une machine virtuelle d'Azur :

- Hébergement des tâches d'arrière-plan dans un ordinateur virtuel d'Azur distinct fournit souplesse et permet un contrôle précis sur l'initiation, l'exécution, planification et affectation des ressources. Toutefois, il augmentera DUREE coût si une machine virtuelle doit être déployée juste pour exécuter des tâches en arrière-plan.
- Il n'y a aucune installation pour surveiller les tâches dans le portail Azure et aucune capacité de redémarrage automatique pour les tâches ayant échouées, même si vous pouvez surveiller l'état de base de la machine virtuelle et le gérer à l'aide de la [Cmdlets de gestion de Service Azur](http://msdn.microsoft.com/library/azure/dn495240.aspx). Cependant, il n'y a pas d'installations pour contrôler les processus et les threads dans les nœuds de calcul. En général, à l'aide d'une machine virtuelle nécessitera des efforts supplémentaires pour mettre en place un mécanisme qui collecte les données d'instrumentation à la tâche et du système d'exploitation dans la machine virtuelle. Une solution qui peut être appropriée consiste à utiliser la [Pack d'administration System Center pour Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Vous pouvez envisager de créer des sondes de surveillance qui sont exposées par le biais de points de terminaison HTTP. Le code de ces sondes puisse effectuent des vérifications de la santé, recueillir des informations opérationnelles et statistiques, ou collecter des informations d'erreur et retourner à une application de gestion. Pour plus d'informations, consultez [Modèle de suivi de point de terminaison santé](http://msdn.microsoft.com/library/dn589789.aspx).

### Plus d'informations

- [Machines virtuelles](http://azure.microsoft.com/services/virtual-machines/) sur le site d'Azur
- [Azure Virtual Machines FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

## Considérations de conception

Il y a plusieurs facteurs fondamentaux à considérer lors de la conception des tâches en arrière-plan. Les sections suivantes traitent de partitionnement, des conflits et la coordination.

## Partitionnement

Si vous décidez d'inclure les tâches de fond au sein d'une instance de calcul existantes (par exemple, un site Web, web rôle, existant rôle worker ou machine virtuelle), vous devez considérer comment cela affectera les attributs de qualité de l'instance de calcul et la tâche d'arrière-plan lui-même. Ces facteurs vont vous aider à décider s'il faut regrouper les tâches avec l'instance de calcul existantes ou séparer en une instance de calcul distincte :

- **Disponibilité**: Tâches d'arrière-plan ne peuvent pas besoin d'avoir le même niveau de disponibilité que d'autres parties de la demande, en particulier l'interface utilisateur et autres parties directement impliquées dans l'interaction avec l'utilisateur. Tâches d'arrière-plan peuvent être plus tolérants de latence, pannes de connexion rejugé, et d'autres facteurs qu'affectent la disponibilité car les opérations peuvent être en file d'attente. Toutefois, il doit y avoir une capacité suffisante pour empêcher la sauvegarde des requêtes qui pourraient bloquer les files d'attente et incidence sur l'application dans son ensemble.
- **Évolutivité**: Tâches d'arrière-plan sont susceptibles d'avoir une exigence d'évolutivité différente de l'interface utilisateur et les éléments interactifs de l'application. Mise à l'échelle de l'interface utilisateur peut être nécessaire pour répondre aux pics de la demande, tandis que les tâches de fond en suspens pourraient être achevés durant les périodes moins achalandées par un moins nombre d'instances de calcul.
- **Résilience**: Échec d'une instance de calcul que seulement les tâches de fond hôtes peuvent affecter pas fatalement l'application dans son ensemble si les demandes de ces tâches peuvent être en file d'attente ou reportées jusqu'à ce que la tâche soit à nouveau disponible. Si l'instance de calcul et/ou de tâches peuvent être relancés dans un intervalle approprié, utilisateurs de l'application ne peuvent pas nuire.
- **Sécurité**: Tâches d'arrière-plan peuvent avoir différentes exigences ou restrictions que l'interface utilisateur ou d'autres parties de la demande. En utilisant une instance de calcul distincte, vous pouvez spécifier un environnement de sécurité différents pour les tâches. Vous pouvez également utiliser des modèles tels que Gatekeeper pour isoler les instances de calcul du fond de l'interface utilisateur afin de maximiser la sécurité et la séparation.
- **Performances**: Vous pouvez choisir le type d'instance de calcul pour les tâches de fond correspondre précisément aux exigences de performance des tâches. Cela peut signifier en utilisant une option de calcul moins coûteuse si les tâches ne nécessitent pas les mêmes capacités de traitement que l'interface utilisateur, ou une instance plus grande s'ils nécessitent des ressources et des capacités supplémentaires.
- **Facilité de gestion**: Tâches d'arrière-plan peuvent avoir un rythme différent de développement et le déploiement de code principal de l'application ou de l'interface utilisateur. Leur déploiement pour une instance de calcul distincte peut simplifier les mises à jour et le versioning.
- **Coût**: Ajout d'instances de calcul pour exécuter arrière-plan tâches augmente les coûts d'hébergement. Vous devriez soigneusement considérer le compromis entre la capacité supplémentaire et ces surcoûts.

Pour plus d'informations, consultez [Modèle d'élection de chef](http://msdn.microsoft.com/library/dn568104.aspx) et [Modèle de consommateurs concurrente](http://msdn.microsoft.com/library/dn568101.aspx).

## Conflits

Si vous avez plusieurs instances d'une tâche en arrière-plan, il est possible qu'ils seront en compétition pour l'accès aux ressources et services tels que des bases de données et le stockage. Cet accès simultané peut entraîner des conflits de ressources, qui peuvent entraîner des conflits dans la disponibilité des services et à l'intégrité des données dans le stockage. Conflits de ressources peuvent être résolus en utilisant une approche de verrouillage pessimiste pour empêcher les instances concurrentes d'une tâche à partir simultanément accès à un service, ou de corrompre les données.

Une autre approche pour résoudre les conflits est de définir les tâches de fond comme un singleton, afin qu'il n'y a jamais une seule instance en cours d'exécution. Cependant, ceci élimine les avantages de performances et une fiabilité qui pourrait fournir une configuration de plusieurs instances, en particulier si l'interface utilisateur peut fournir suffisamment de travail pour occuper plus d'une tâche en arrière-plan. Il est essentiel de veiller à ce que la tâche d'arrière-plan peut redémarrer automatiquement, et qu'il possède une capacité suffisante pour faire face aux pics de demande. Ceci peut être réalisé en attribuant une instance de calcul avec des ressources suffisantes, en mettant en place un mécanisme de file d'attente qui peut stocker des demandes pour une exécution ultérieure lorsque la demande diminue, ou par une combinaison de ces techniques.

## Coordination

Les tâches d'arrière-plan peuvent être complexe et nécessiter plusieurs différentes tâches à exécuter pour produire un résultat ou à toutes les exigences. Il est fréquent dans ces scénarios de diviser la tâche en plus petit les étapes discrètes ou sous-tâches qui peuvent être exécutées par plusieurs consommateurs. Multi-étapes emplois peuvent être plus efficace et plus souple car les étapes individuelles peuvent être réutilisables dans plusieurs travaux. Il est également facile à ajouter, supprimer ou modifier l'ordre des étapes.

Coordination des tâches et des étapes multiples peut être difficile, mais il y a trois modèles courants, que vous pouvez utiliser pour orienter votre mise en œuvre d'une solution :

- **Décomposition d'une tâche en plusieurs étapes réutilisables**. Une application peut devoir effectuer une variété de tâches plus ou moins complexes sur les informations qu'il traite. Une approche simple mais inflexible à la mise en œuvre de la présente demande pourrait consister à faire cet usinage comme module monolithique. Toutefois, cette approche est susceptible de réduire les possibilités de refactorisation du code, optimisation ou réutiliser si des pièces du même traitement sont nécessaires ailleurs dans l'application. Pour plus d'informations, consultez [Tubes et modèle de filtres](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gérer l'exécution des étapes d'une tâche**. Une application peut accomplir des tâches qui comportent un certain nombre de mesures, dont certaines peuvent appeler télé-services ou accéder aux ressources distantes. Les étapes individuelles peuvent être indépendants des uns des autres, mais ils sont orchestrées par la logique de l'application qui implémente la tâche. Pour plus d'informations, consultez [Scheduler Agent superviseur Pattern](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gestion de récupération pour les étapes d'une tâche qui ne parviennent pas**. Une application peut besoin de défaire le travail effectué par une série d'étapes, qui définissent ensemble une opération finalement cohérente, si une ou plusieurs des étapes échouent. Pour plus d'informations, consultez [Modèle de Transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx).

## Cycle de vie (Services en nuage)

 Si vous décidez d'implémenter des tâches en arrière-plan pour des applications de Cloud Services qui utilisent des rôles web et de travail à l'aide de la **RoleEntryPoint** classe, il est important de comprendre le cycle de vie de cette classe afin de le pour utiliser correctement.

Rôles de Web et de travail passent par une série de phases distinctes qu'ils Démarrer, exécutent et arrêter. Le **RoleEntryPoint** classe expose une série d'événements qui indiquent quand ces étapes sont produisent. Vous utilisez ceux-ci pour initialiser, exécuter et arrêter vos tâches d'arrière-plan personnalisé. Le cycle complet est :

- D'Azur, charge l'assembly du rôle et il cherche une classe qui dérive de **RoleEntryPoint**.
- S'il trouve cette classe, il appelle **RoleEntryPoint.OnStart()**. Vous substituez cette méthode pour initialiser vos tâches en arrière-plan.
- Après la **OnStart** méthode se termine, Azure appelle **Application_Start()** global de l'application fichier si cela est présent (par exemple, Global.asax dans un rôle de web ASP.NET en cours d'exécution).
- Appels d'Azur **RoleEntryPoint.Run()** sur un nouveau thread de premier plan qui s'exécute en parallèle avec **OnStart()**. Vous substituez cette méthode pour démarrer vos tâches en arrière-plan.
- Lorsque la méthode Run se termine, Azure appelle d'abord **Application_End()** global de l'application fichier si cela est présent, puis appelle **RoleEntryPoint.OnStop()**. Vous substituez le **OnStop** méthode pour arrêter vos tâches en arrière-plan, nettoyer les ressources, disposer d'objets et la fermeture des connexions qui les tâches pourraient avoir utilisé.
- Le processus hôte de travailleur Azur rôle est arrêté. À ce stade, le rôle est recyclé et se remettra en marche.

Pour plus d'informations et un exemple d'utilisation des méthodes de la **RoleEntryPoint** classe, voir [Calculer le modèle de Consolidation de ressource](http://msdn.microsoft.com/library/dn589778.aspx).

## Considérations

Considérez les points suivants lorsque vous planifiez comment vous allez exécuter des tâches de fond dans un rôle web ou travailleur :

- La valeur par défaut **Courir** implémentation de la méthode dans le **RoleEntryPoint** classe contienne un appel à **Timeout.Infinite** qui garde le rôle vivant indéfiniment. Si vous substituez la **Courir** méthode (qui est généralement nécessaire pour exécuter les tâches d'arrière-plan) vous ne pouvez accepter votre code à la sortie de la méthode, à moins que vous voulez recycler l'instance de rôle.
- Une implémentation standard de la **Courir** méthode inclut du code pour démarrer les tâches en arrière-plan, et un élément de la boucle qui vérifie périodiquement l'état de toutes les tâches de fond. Il peut redémarrer tout qui échouent ou surveiller des jetons d'annulation qui indiquent les tâches sont terminées.
- Si une tâche d'arrière-plan lève une exception non gérée, cette tâche devrait être recyclée tout en permettant à d'autres tâches d'arrière-plan dans le rôle de continuer de fonctionner. Toutefois, si l'exception est déclenchée par la corruption des objets en dehors de la tâche, telles que le stockage partagé, l'exception devrait être gérée par votre **RoleEntryPoint** classe, toutes les tâches devraient être annulés et les **Courir** méthode a permis à la fin. Azur va ensuite redémarrer le rôle.
- Utilisation du **OnStop** méthode pour faire une pause ou tuer des tâches en arrière-plan et nettoyer les ressources. Il peut s'agir d'arrêt des tâches longues ou à plusieurs étapes, et il est essentiel d'examiner comment cela peut être fait pour éviter les incohérences de données. Si une instance de rôle s'arrête pour une raison autre qu'un arrêt initié par l'utilisateur, le code s'exécutant le **OnStop** méthode doit terminer dans les cinq minutes avant qu'elle soit interrompue par la force. S'assurer que votre code peut accomplir en ce moment, ou peut tolérer ne pas en cours d'exécution jusqu'à la fin.  
- L'équilibreur de charge Azure commence à diriger la circulation au rôle d'instance lorsque le **RoleEntryPoint.OnStart** méthode retourne la valeur true. Par conséquent, envisager de mettre tout votre code d'initialisation la **OnStart** méthode afin que les instances de rôle qui n'initialisent pas avec succès la volonté ne reçoivent pas tout trafic.
- Vous pouvez utiliser les tâches de démarrage en plus des méthodes de la **RoleEntryPoint** classe. Vous devez utiliser les tâches de démarrage pour initialiser tous les paramètres que vous deviez changer de l'équilibrage de la charge d'Azur parce que ces tâches seront exécute avant que le rôle reçoit toutes les demandes. Pour plus d'informations, consultez [Exécuter les tâches de démarrage dans Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- Si il y a une erreur dans une tâche de démarrage, il peut obliger le rôle de redémarrer continuellement. Cela peut vous empêcher d'effectuer un échange VIP revenir à une version précédemment mises en scène parce que le swap requiert un accès exclusif au rôle, et cela ne peut être obtenue même si le rôle redémarre. Pour résoudre ce problème :
	-  Ajoutez le code suivant au début de la **OnStart** et **Courir** méthodes dans votre rôle :

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Ajoutez la définition de la **Gel** paramètre comme une valeur booléenne à le ServiceDefinition.csdef et ServiceConfiguration.*fichiers de.cscfg pour le rôle et affectez-lui **faux**. Si le rôle rentre dans un mode de redémarrage répété, vous pouvez modifier le paramètre de **true** pour geler l'exécution de rôle et lui permettre d'être échangé avec une version antérieure.

## Facteurs de résilience

Tâches d'arrière-plan doivent être souple afin de fournir des services fiables à l'application. Lors de la planification et la conception de tâches en arrière-plan, considérez les points suivants :

- Tâches d'arrière-plan doivent être en mesure de gérer correctement le rôle ou service redémarre sans endommager les données ou introduction d'incohérence dans l'application. Pour les tâches de longue durée ou à plusieurs étapes, envisagez d'utiliser _vérifier le pointage_ en enregistrant l'état des emplois dans un stockage persistant, ou sous forme de messages dans une file d'attente si cela est approprié. Par exemple, vous pouvez conserver les informations d'État dans un message dans une file d'attente et mise à jour incrémentielle ces informations d'État avec la progression des tâches pour que la tâche peut être réalisée à partir du dernier point de contrôle connu bon au lieu de redémarrer depuis le début. Lors de l'utilisation de files d'attente de Azure Service Bus, vous pouvez utiliser les sessions de message pour permettre le même scénario. Séances vous permettent d'enregistrer et récupérer l'état de traitement de la demande en utilisant le [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) Méthodes. Pour plus d'informations sur la conception fiable processus multi-étapes et workflows, consultez [Scheduler Agent superviseur Pattern](http://msdn.microsoft.com/library/dn589780.aspx).
- Lorsque vous utilisez des rôles web ou travailleur d'accueillir plusieurs tâches en arrière-plan, concevoir votre substitution de la **Courir** méthode à suivre pour a échoué ou a décroché les tâches et les redémarre. Si ce n'est pas pratique, et que vous utilisez un rôle de travailleur, forcer le rôle de travailleur pour redémarrer en sortant de la **Courir** méthode.
- Lors de l'utilisation de files d'attente pour communiquer avec les tâches en arrière-plan, les files d'attente peuvent agir comme un tampon pour stocker les requêtes envoyées aux tâches tandis que la demande est supérieure à charge habituelle. Cela permet aux tâches à rattraper avec l'interface utilisateur pendant les périodes creuses. Cela signifie également que le rôle de recyclage ne bloquera pas l'interface utilisateur. Pour plus d'informations, consultez [Charge en fonction des file d'attente de mise à niveau modèle](http://msdn.microsoft.com/library/dn589783.aspx). Si certaines tâches sont plus importants que d'autres, envisagez d'implémenter la [Modèle de file d'attente de priorité](http://msdn.microsoft.com/library/dn589794.aspx) pour s'assurer que ces tâches s'exécutent avant moins importants.
- Tâches en arrière-plan qui commencent par ou autrement traitent les messages doivent être conçus pour supporter des incohérences telles que les messages qui arrivent dans le désordre, messages qui à plusieurs reprises provoquent une erreur (souvent dénommée _messages incohérents_) et les messages qui sont livrés en plus d'une fois. Considérez ce qui suit :
  - Messages that must be processed in a specific order, such as those that change data based on its existing value (for example, adding a value to an existing value), may not arrive in the original order they were sent. Alternatively, they may be handled by different instances of a background task in a different order due to varying loads on each instance. Messages that must be processed in a specific order should include a sequence number, key, or some other indicator that background tasks can use to ensure they are processed in the correct order. If you are using Azure Service Bus, you can use message sessions to guarantee the order of delivery. However, it is usually more efficient where possible to design the process so that the message order is not important.
  - Typically, a background task will peek messages in the queue, which temporarily hides them from other message consumers, and then delete the messages after they have been successfully processed. If a background task fails when processing a message, that message will reappear on the queue after the peek timeout expires, and will be processed by another instance of the task or during the next processing cycle of this instance. If the message consistently causes an error in the consumer, it will block the task, the queue, and eventually the application itself when the queue becomes full. Therefore, it is vital to detect and remove poison messages from the queue. If you are using Azure Service Bus, messages that cause an error can be moved automatically or manually to an associated dead letter queue.
  - Files d'attente sont garantis au _moins une fois_ delivery mechanisms, but they may deliver the same message more than once. In addition, if a background task fails after processing a message but before deleting it from the queue, the message will become available for processing again. Background tasks should be idempotent, which means that processing the same message more than once does not cause an error or inconsistency in the application’s data. Some operations are naturally idempotent, such as setting a stored value to a specific new value. However, operations such as adding a value to an existing stored value without checking that the stored value is still the same as when the message was originally sent will cause inconsistencies.  Azure Service Bus queues can be configured to automatically remove duplicated messages.
  - Certains systèmes de messagerie, tels que Azure storage files d'attente et les files d'attente de Bus de Service Azure, prend en charge une propriété de comte de-queue qui indique le nombre de fois qu'un message a été lu dans la file d'attente. Cela peut être utile dans la gestion des messages incohérents et répétés. Pour plus d'informations, consultez [Apprêt de messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx) et [Idempotence Patterns](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Considérations de mise à l'échelle et la performance

Tâches d'arrière-plan doivent offrir des performances suffisantes afin de ne pas bloquer l'application, ou provoquer des incohérences en raison de l'opération différé lorsque le système est sous charge. En règle générale, les performances sont améliorées par les instances de calcul qui hébergent les tâches de fond d'échelle. Lors de la planification et la conception de tâches en arrière-plan, considérer les points suivants autour d'évolutivité et de performances :

- Autoscaling Azur prend en charge (l'évolution et mise à l'échelle en) basé sur la demande actuelle et de la charge, ou sur un calendrier prédéfini, pour Sites Web, web Services Cloud et rôles de travail et des ordinateurs virtuels hébergés déploiements. Cette fonction permet d'assurer l'application comme un ensemble a des possibilités suffisantes de rendement tout en minimisant les coûts d'exécution.
- Lorsque les tâches de fond ont une capacité de performance différents des autres pièces d'une demande de Services Cloud (par exemple, l'interface utilisateur ou les composants tels que la couche d'accès aux données), hébergement les tâches de fond ensemble dans un rôle de travail distinct permet l'interface utilisateur et l'arrière-plan des rôles de tâche à l'échelle de manière indépendante pour gérer la charge. Si plusieurs tâches de fond ont des possibilités de performance significativement différents les uns des autres, envisager les divisant en rôles de travail séparé et mise à l'échelle de chaque rôle type indépendamment, mais notez que ceci peut augmenter la duree des coûts par rapport à la combinaison de toutes les tâches dans des rôles moins.
- Simplement mise à l'échelle les rôles peut-être pas suffisante pour prévenir la perte de performances sous charge. Vous devrez peut-être également évoluer les files d'attente de stockage et d'autres ressources afin d'éviter un point unique de l'ensemble de sa chaîne, devenir un goulot d'étranglement de traitement. Aussi, pensez à autres limitations, telles que le débit maximal de stockage et d'autres services de l'application et les tâches de fond dépendent.
- Tâches d'arrière-plan doivent être conçus pour la mise à l'échelle. Par exemple, ils doivent être en mesure de détecter dynamiquement le nombre de files d'attente de stockage en usage pour l'écouter sur ou envoyer des messages à la file d'attente appropriée.
- Par défaut, WebJobs échelle avec leur instance Azure Web Sites associée. Toutefois, si vous voulez un WebJob à exécuter en tant qu'une seule instance, vous pouvez créer un fichier Settings.job contenant les données JSON **{"est_Singleton": true}**. Cela force Azure pour exécuter uniquement une seule instance de le WebJob, même s'il existe plusieurs instances du site associé, qui peut être une technique utile pour les tâches planifiées qui doivent s'exécuter comme une seule instance.

## Modèles liés

- [Apprêt de messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx)
- [AutoScaling orientation](http://msdn.microsoft.com/library/dn589774.aspx)
- [Modèle de Transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx)
- [Modèle de consommateurs concurrentes](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calculer l'orientation de partitionnement](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calculer le modèle de Consolidation de ressource](http://msdn.microsoft.com/library/dn589778.aspx)
- [Modèle de contrôleur d'accès](http://msdn.microsoft.com/library/dn589793.aspx)
- [Modèle d'élection chef](http://msdn.microsoft.com/library/dn568104.aspx)
- [Tubes et modèle de filtres](http://msdn.microsoft.com/library/dn568100.aspx)
- [Modèle de file d'attente de priorité](http://msdn.microsoft.com/library/dn589794.aspx)
- [Charge en fonction des file d'attente de mise à niveau modèle](http://msdn.microsoft.com/library/dn589783.aspx)
- [Scheduler Agent superviseur Pattern](http://msdn.microsoft.com/library/dn589780.aspx)

## Plus d'informations

- [Mise à l'échelle des Applications Azure avec rôles de travail](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [L'exécution de tâches de fond](http://msdn.microsoft.com/library/ff803365.aspx)
- [Cycle de vie de démarrage rôle Azur](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (article de blog)
- [Le cycle de vie de rôle Azur Cloud Services](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vidéo)
- [Débuter avec le SDK de WebJobs d'Azur](websites-dotnet-webjobs-sdk-get-started/)
- [Azur files d'attente et les files d'attente Service Bus - comparaison et contraste](http://msdn.microsoft.com/library/hh767287.aspx)
- [Comment faire pour activer les Diagnostics dans un Service de Cloud Computing](http://msdn.microsoft.com/library/dn482131.aspx)
