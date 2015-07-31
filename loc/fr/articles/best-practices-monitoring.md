<properties
   pageTitle="Monitoring and diagnostics guidance | Microsoft Azure"
   description="Best practices for monitoring distributed applications in the cloud."
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

# Conseils de surveillance et de diagnostic

![](media/best-practices-monitoring/pnp-logo.png)

## Vue d'ensemble
Applications distribuées et service s'exécutant dans le nuage sont, par leur nature même, des pièces complexes de logiciels qui comportent de nombreuses pièces mobiles. Dans un environnement de production, il est important d'être en mesure de suivre la façon dont les utilisateurs utilisent votre système, l'utilisation des ressources trace et généralement de surveiller la santé et les performances de votre système. Cette information peut servir comme un outil diagnostique pour détecter et corriger les problèmes et aussi pour aider à déceler les problèmes potentiels et les empêcher de se produire.

## Surveillance et diagnostic des scénarios
Surveillance active vous faire une idée de la façon dont un système fonctionne et êtes un élément crucial dans le maintien des objectifs de qualité de service. Quelques scénarios courants pour la collecte des données de surveillance comprennent :

- Veiller à ce que le système reste en bonne santé.
- Suivi de la disponibilité du système et de ses éléments constitutifs.
- Maintien du rendement afin de s'assurer que le débit du système ne se dégrade pas de façon inattendue comme le volume de travail augmente.
- Garantir que la rencontre de système que toute SLA a convenu avec les clients.
- Protéger la confidentialité et la sécurité du système, les utilisateurs et leurs données.
- Suivi des opérations effectuées audit ou fins réglementaires.
- Surveillance de l'usage quotidien des système et aider à repérer les tendances qui pourraient entraîner des problèmes si elles ne sont pas abordées.
- Suivi des problèmes qui se produisent, du rapport initial par le biais de l'analyse des causes possibles, de rectification, de mises à jour logicielles qui en découle et de déploiement.
- Opérations de traçage et de débogage versions logicielles.

> [AZURE. REMARQUE] Cette liste se veut pas exhaustive. Ce document met l'accent sur ces scénarios dans les situations les plus courantes pour effectuer la surveillance, mais il peut y avoir d'autres qui sont moins courants ou spécifiques à votre environnement.

Les sections suivantes décrivent ces scénarios plus en détail. Les informations pour chaque scénario sont discutées dans le format suivant :

- Un bref aperçu du scénario.
- Les conditions typiques de ce scénario.
- Les données d'instrumentation brut requises pour appuyer le scénario et les sources possibles de cette information.
- Comment ces données brutes peuvent être analysées et combinées pour générer des informations de diagnostic significatives.

## Surveillance de la santé
Un système est en bonne santé si elle est en cours d'exécution et capable de traiter les demandes. La surveillance de la santé vise à générer une capture instantanée de l'état de santé actuel du système pour vous permettre de vérifier que tous les composants du système fonctionnent comme prévu.

### Exigences en matière de surveillance sanitaire
Un opérateur doit être alerté rapidement (en quelques secondes) si une partie quelconque du système est considérée comme malsain. L'exploitant doit être en mesure de déterminer quelles parties du système fonctionnent normalement, et quelles parties ont des problèmes. Système de santé peut être mis en évidence grâce à un système de feux de circulation ; rouge pour malsain (le système est arrêté), jaune pour partiellement sain (le système fonctionne avec des fonctionnalités réduites) et vert pour complètement sain.

Un système complet de contrôle d'état permet à un opérateur de creuser à travers le système pour afficher l'état de santé de sous-systèmes et de composants. Par exemple, si l'ensemble du système soit décrite comme partiellement sain, l'opérateur doit être capable de zoomer et de déterminer quelle fonctionnalité est actuellement indisponible.

### Exigences de données sources, instrumentation et données de collection
Les données brutes nécessaires pour soutenir la surveillance de la santé peuvent être générées à la suite de :

- Suivi de l'exécution des demandes des utilisateurs. Cette information peut servir à déterminer les demandes qui ont réussi, qui ont échoué, et chaque demande combien de temps prend.
- Surveillance de l'utilisateur synthétique. Ce processus simule les étapes effectuées par un utilisateur et s'ensuit une série prédéfinie d'étapes. Les résultats de chaque étape doivent être capturés.
- Enregistrement des exceptions, les erreurs et avertissements. Ces informations peuvent être capturées à la suite des instructions de traçage incorporées dans le code d'application, mais aussi de récupérer des informations à partir des journaux d'événement de tout service référencé par le système.
- Surveillance de la santé de tout service de tiers utilisés par le système. Cela pourrait exiger la récupération et l'analyse des données de santé fournies par ces services, et cette information pourrait prendre une variété de formats.
- Surveillance de point de terminaison. Ce mécanisme est décrit plus en détail dans la disponibilité de section de contrôle.
- Collecte de données sur le rendement ambiant, par exemple l'utilisation du processeur d'arrière-plan ou activité e/s (incluant le réseau).

### Analyse des données de santé
Le principal objectif de la surveillance de la santé est d'indiquer rapidement si le système est en cours d'exécution. Chaud analyse des données immédiates peut déclencher une alerte si un composant critique est détecté d'être malsain (il ne parvient pas à répondre à une série consécutive de pings, par exemple). L'opérateur peut alors prendre les mesures correctives.

A more advanced system might include a predictive element that performs a cold analysis over recent and current workloads to spot trends and determine whether the system is likely to remain healthy or whether additional resources are going to be required. This predictive element should be based on critical performance metrics, such as the rate of requests directed at each service or subsystem, the response times of these requests, and the volume of data flowing into and out of each service. If the value of any metric exceeds a defined threshold the system can raise an alert to enable an operator or auto-scaling (if available) to take the preventative actions necessary to maintain system health. These actions might involve adding resources, restarting one or more services that are failing, or applying throttling to lower-priority requests.

## Analyse de la disponibilité
Un système véritablement sain requiert que les composants et sous-systèmes qui composent le système sont disponibles. Analyse de la disponibilité est étroitement liée à la surveillance de la santé, mais considérant que la surveillance de la santé fournit une vue immédiate de l'état de santé actuel du système, analyse de la disponibilité se préoccupe de la disponibilité du système et ses composants pour produire des statistiques concernant le temps de disponibilité du système de suivi.

Dans de nombreux systèmes, certains composants (par exemple, une base de données) sont configurés avec redondance intégrée pour permettre le basculement rapide en cas de faute grave ou perte de la connectivité. Idéalement, les utilisateurs ne devraient pas être au courant qu'une telle défaillance s'est produite, mais d'une perspective de surveillance de la disponibilité, il est nécessaire de recueillir autant d'informations que possible sur ces échecs pour essayer de déterminer la cause et prendre des mesures correctives pour les empêcher de se reproduire.

Les données nécessitées pour suivre la disponibilité peuvent dépendre de plusieurs facteurs de niveau inférieur, dont beaucoup peuvent être spécifiques à l'application, système et environnement. Un système de contrôle efficace capture les données de disponibilité qui correspond à ces facteurs de bas niveau et puis agrège pour donner une vue d'ensemble du système. Par exemple, dans un système de commerce électronique, les fonctionnalités d'entreprise qui permet au client de passer des commandes peuvent dépendre du référentiel dans les détails de la commande sont stockés et le système de paiement qui gère les transactions monétaires pour payer pour ces commandes. La disponibilité de la partie de l'ordonnance de placement du système est donc fonction de la disponibilité du référentiel et le sous-système de paiement.

### Exigences pour la surveillance de la disponibilité
Un opérateur doit être également en mesure d'afficher la disponibilité historique du système ou sous-système et utiliser cette information pour repérer des tendances qui peuvent être à l'origine un ou plusieurs sous-système d'échouer périodiquement (services commencent-ils à l'échec à un moment donné de la journée qui correspond aux heures de pointe traitement?)

En plus d'offrir une vue immédiate et historique de la disponibilité de chaque sous-système, une solution de surveillance devrait aussi être capable d'alerter rapidement un opérateur lorsque l'un ou plus de services en cas d'échec ou d'utilisateurs ne peuvent pas se connecter aux services. Ce n'est pas simplement une question de chaque service de surveillance, mais également examiner les actions exécutées par chaque utilisateur, si ces mesures échouent lorsqu'ils tentent de communiquer avec un service. Dans une certaine mesure, un degré d'échec de connexion est normal et peut être due à des erreurs passagères, mais il peut être utile permettre au système déclencher une alerte du nombre de connectivité d'échouer à un sous-système spécifié pendant un laps de temps donné.

### Exigences de données sources, instrumentation et données de collection
Comme pour la surveillance de la santé, les données brutes nécessaires pour appuyer l'analyse de la disponibilité peuvent être due à l'utilisateur synthétique suivi et journalisation des exceptions, des failles et alertes qui peuvent apparaître. En outre, les données de disponibilité peuvent être obtenues d'effectuer le suivi de point de terminaison. L'application peut exposer un ou plusieurs effets sur la santé, chaque test accès à une zone fonctionnelle au sein du système. Le système de surveillance peut ping chaque point de terminaison selon un calendrier défini et recueillir les résultats (réussite ou échec).

Tous les délais d'attente et pannes de connectivité de réseau et les tentatives de connexion doivent être enregistrés. Toutes les données devraient être horodatées.

<a name="analyzing-availablity-data"></a>
### Analyse des données de disponibilité
Les données d'instrumentation doivent être agrégées et corrélées pour prendre en charge les types d'analyse suivants :

- La disponibilité immédiate du système et des sous-systèmes.
- Les taux de défaut de disponibilité du système et des sous-systèmes. Idéalement un opérateur devrait être en mesure de corréler les échecs avec des activités spécifiques ; ce qui se passait lorsque le système a échoué ?
- Une vue historique des taux de défaillance du système ou des sous-systèmes travers n'importe lequel spécifié laps de temps et le chargement du système (nombre de demandes d'utilisateurs par exemple) quand une erreur s'est produite.
- Les raisons de la non-disponibilité du système ou des sous-systèmes. Par exemple, service non exécuté, perte de connectivité, connecté mais expirer et erreurs connectés mais retour.

Vous pouvez calculer la pourcentage de disponibilité d'un service sur une période de temps en utilisant la formule :

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Ceci est utile pour les SLA fins ([Surveillance de la SLA](#SLA-monitoring) est décrit plus en détail plus loin dans ce guide). La définition de _Temps d'arrêt_ dépend du service. Par exemple, Visual Studio Online définit temps d'arrêt comme la période au cours de laquelle les tentatives du client pour se connecter au service prennent plus de 120 secondes et toutes les base lecture et écriture opérations échouent une fois la connexion établie dans ce délai.

## Surveillance du rendement
Comme le système est placé sous le stress de plus en plus comme le volume de l'augmentation des utilisateurs et la taille des ensembles de données que ces utilisateurs accès se développe, l'échec éventuel d'un ou plusieurs composants devienne probable. Fréquemment, défaillance d'un composant est précédée d'une baisse des performances. Si vous êtes en mesure de détecter une telle diminution, vous pouvez prendre des mesures proactives pour remédier à la situation.

Performances du système dépend de plusieurs facteurs. Chaque facteur est généralement mesurée à l'aide d'indicateurs de Performance clés (KPI), tels que le nombre de transactions de base de données par seconde, ou le volume de requêtes réseau correctement traitées dans un délai donné. Certains de ces indicateurs de performance clés peuvent être disponibles comme des indicateurs de rendement précis, tandis que d'autres peuvent provenir d'une combinaison de paramètres.

> [AZURE. REMARQUE] Détermination des performances de mauvaise ou bonne nécessite une bonne compréhension du niveau de performance au cours de laquelle le système doit être capable de courir. Il faut observer le système alors qu'il fonctionne sous une charge typique et capture les données pour chaque indicateur de performance clé sur une période de temps. Ce qui peut impliquer le système sous une charge simulée en cours d'exécution dans un environnement de test et de collecte des données appropriées avant de déployer le système dans un environnement de production.

> Vous devez également vous assurer que la surveillance à des fins de performance ne devienne pas un fardeau injustifié sur le système. Vous pouvez être en mesure d'ajuster dynamiquement le niveau de détail concernant les données qui rassemble la processus de suivi des performances.

### Exigences en matière d'analyse des performances
Pour examiner les performances du système, l'exploitant devra généralement voir informations y compris :

- Le taux de réponse des requêtes des utilisateurs.
- Le nombre de demandes d'utilisateurs simultanés.
- Le volume du trafic réseau.
- Les tarifs selon quelles affaires transactions sont terminées.
- Le délai moyen de traitement des demandes.

Il peut également être utile de fournir des outils qui permettent à un opérateur aider les corrélations spots, tels que :

- Le nombre d'utilisateurs simultanés par rapport à la demande les temps de latence (combien de temps faut-il pour commencer le traitement d'une demande après que l'utilisateur a envoyé).
- Le nombre d'utilisateurs simultanés par rapport au temps de réponse moyen (combien de temps faut-il pour remplir une demande, après il a commencé à traiter).
- Le volume des demandes par rapport au nombre d'erreurs de traitement.

Ainsi que cette information fonctionnelle de haut niveau, un opérateur doit également être en mesure d'obtenir une vue détaillée de l'exécution pour chaque composant du système. Ces données sont généralement fournies à l'aide des compteurs de performance de bas niveau telles que les informations de suivi :

- Utilisation de la mémoire,
- Nombre de threads,
- Temps de traitement de CPU,
- Longueur de file d'attente de requête,
- Disque ou taux d'e/s réseau et erreurs,
- Nombre d'octets écrits ou lire,
- Indicateurs de middleware, comme la longueur de la file d'attente.

Toutes les visualisations doivent permettre à un utilisateur spécifier un intervalle de temps ; les données affichées pourraient être un instantané de la situation actuelle et/ou une vue historique des performances.

L'exploitant doit être en mesure de déclencher une alerte basée sur toute mesure de performance pour toute valeur donnée au cours de n'importe quel intervalle de temps spécifié.

### Exigences de données sources, instrumentation et données de collection
Les données de haut niveau de performance (débit, nombre d'utilisateurs simultanés, le nombre de transactions commerciales, des taux d'erreur, etc.) peuvent être recueillies par contrôlant l'évolution des demandes des utilisateurs, car ils arrivent et passent par le système. Cela implique l'intégration des instructions de suivi à des moments clés dans le code d'application ainsi que des informations de calendrier. Tous les défauts, les exceptions et les avertissements devraient être saisies avec suffisamment de données pour pouvoir être corrélée avec les demandes qui leur a causé. Le journal IIS est une autre source utile.

Si possible, vous devriez également capturer des données de performance pour tous les systèmes externes que l'application utilise. Ces systèmes externes pourraient constituer leurs propres compteurs de performance ou d'autres caractéristiques pour demander les données de performance. Si ce n'est pas possible, vous devez enregistrer des informations telles que l'heure de début et heure de la fin de chaque demande faite à un système externe, ainsi que l'État (succès, échec ou avertissement) de l'opération. Par exemple, vous pouvez utiliser une approche de chronomètre aux demandes de temps ; démarrer une minuterie en cours d'exécution au démarrage de la requête et ensuite arrêter le chronomètre lorsque la demande est terminée.

Données de performances à basse altitude pour des composants individuels dans un système peuvent être disponibles par le biais de fonctionnalités telles que les compteurs de performance Windows et diagnostics d'Azur.

### Analyse des données de performance
Une grande partie de le œuvre de l'analyse consiste à agréger des données de performance par type de requête de l'utilisateur (par exemple l'ajout d'un élément dans un panier ou d'effectuer le processus de commande dans un système de commerce électronique) et/ou le sous-système ou service auquel chaque demande est envoyée.

Une autre exigence commune est résumant les données sur le rendement en percentiles choisis. Par exemple, déterminer les temps de réponse pour 99 % des demandes, 95 % des demandes et 70 % des demandes. Il peut y avoir des cibles de la SLA, ou autres objectifs fixés pour chaque centile. Les résultats en cours doivent être signalés dans les deux temps quasi-réel pour aider à déceler les problèmes immédiats, mais aussi agrégé au fil du temps plus longtemps à des fins statistiques.

Dans le cas de problèmes de latence, impact sur les performances, l'exploitant devrait être rapidement en mesure d'identifier la cause du goulot en examinant chaque étape effectuée par chaque demande le temps de latence. Les données de performance doivent donc fournir un moyen de corrélation entre les mesures du rendement pour chaque étape pour les attacher à une demande spécifique.

Selon les besoins de visualisation, il peut être utile générer et stocker un cube de données contenant des vues des données brutes afin de permettre l'interrogation complexe ad hoc et l'analyse de l'information sur le rendement.

## Surveillance de la sécurité
Tous les systèmes commerciaux qui incluent des données sensibles doivent mettre en place une structure de sécurité. La complexité du mécanisme de sécurité est généralement fonction de la sensibilité des données. Dans un système qui oblige les utilisateurs à être authentifié, vous devez enregistrer toutes les tentatives de connexion, si elles omettent ou réussissent. En outre, toutes les opérations effectuées et les détails de toutes les ressources accédés par un utilisateur authentifié doivent être connectés. Lorsque l'utilisateur met fin à leur session et fermeture de la session, ces informations doivent également être enregistrées.

Surveillance peut être en mesure de faciliter la détection des attaques sur le système. Par exemple, un grand nombre de tentatives de connexion qui ont échoué peut indiquer une attaque par force brute, ou une montée inattendue dans les demandes pourrait être le résultat d'une attaque DDoS. Vous devez être préparé à suivre toutes les demandes à toutes les ressources quelle que soit la source de ces demandes ; un système avec une vulnérabilité de connexion susceptibles d'exposer accidentellement des ressources vers le monde extérieur sans nécessiter qu'un utilisateur se connecte en fait.

### Exigences relatives à la surveillance de la sécurité
Les aspects les plus essentiels de la surveillance de la sécurité devraient permettre à un opérateur de rapidement :

- Détecter les tentatives d'intrusions par une entité non authentifiée,
- Identifier des tentatives par des entités pour effectuer des opérations sur les données pour lesquelles ils n'ont pas reçu l'accès,
- Déterminer si le système, ou une partie du système, est attaqué de l'extérieur ou à l'intérieur (par exemple, un utilisateur authentifié malveillant susceptible de tenter d'abattre le système).

Pour prendre en charge ces exigences, un opérateur doit être notifié :

- De tout répété échec des tentatives de connexion faites par le même compte dans un délai spécifié.
- Si le même compte authentifié tente à plusieurs reprises à accéder à une ressource interdite pendant une période de temps spécifiée.
- Si un grand nombre de demandes non authentifiées ou non autorisés se produire au cours d'une période de temps spécifiée.

L'information à un opérateur doit inclure l'adresse de l'hôte de la source pour chaque demande. Si des violations de sécurité proviennent régulièrement une plage particulière d'adresses, puis ces hôtes pourraient être bloqués.

A key part in maintaining the security of a system is being able to quickly detect actions that deviate from the usual pattern. Information such as the number of failed and/or successful login requests can be displayed visually to help detect whether there is a spike in activity at an unusual time (such as users logging in at 3am and performing a large number of operations when their working day starts at 9am). This information can also be used to help configure time-based autoscaling, For example, if an operator observes that a large number of users regularly log in at a particular time of day, the operator can arrange to start additional authentication services to handle the volume of work, and then shut these additional services down when the peak has passed.

### Exigences de données sources, instrumentation et données de collection
La sécurité est un aspect global des systèmes plus distribués, et les données pertinentes sont susceptibles d'être générés en plusieurs points dans un système. Vous devriez envisager une approche Security Information and Event Management (SIEM) pour recueillir les informations relatives à la sécurité résultant d'événements déclenchés par l'application, matériel réseau, serveurs, firewalls, logiciel antivirus et autres éléments de prévention d'intrusion.

Surveillance de la sécurité peut intégrer des données provenant d'outils qui ne font pas partie de votre application, tels que les utilitaires permettant d'identifier les activités de numérisation portuaires par des organismes externes ou des filtres de réseau qui détectent les tentatives d'obtenir l'accès non authentifié à votre application et vos données.

Dans tous les cas les données recueillies doivent permettent à un administrateur déterminer la nature de toute attaque et prendre les contre-mesures appropriées.

### Analyse de données de sécurité
Une fonctionnalité de contrôle de la sécurité est la variété de sources dont les données se pose. Les différents formats et le niveau de détail exigent souvent une analyse complexe des données capturées pour attacher ensemble dans un thread cohérent d'informations. Mis à part le plus simple des cas (par exemple détecter un grand nombre d'échecs de connexion, ou des tentatives répétées pour obtenir un accès non autorisé aux ressources critiques), il ne serait pas possible exécuter tout traitement automatisé complexe de la sécurité des données, et au lieu de cela, il peut être préférable d'écrire ces données, horodatées mais sinon dans sa forme originale, à un référentiel sécurisé pour permettre une analyse manuelle expertise.

<a name="SLA-monitoring"></a>

## Surveillance de la SLA
Beaucoup de systèmes commerciaux qui prennent en charge les clients payants font garantie quant à la performance du système sous la forme de la SLA. Essentiellement, SLA affirment que le système peut gérer un volume défini de travail suivant un calendrier préétabli et sans perdre les informations critiques. SLA surveillance traite de veiller à ce que le système peut SLA mesurables.

> [AZURE. REMARQUE] Suivi de la SLA est étroitement liée à la surveillance du rendement, mais considérant que la surveillance du rendement vise à s'assurer que le système fonctionne _façon optimale_, Suivi de la SLA est régie par une obligation contractuelle qui définit ce qu'est _façon optimale_ signifie en réalité.

SLA est souvent définis en termes de :

- Disponibilité générale du système. Par exemple, une organisation peut garantir que le système sera disponible pour 99,9 % du temps ; Cela équivaut à pas plus de 9 heures d'indisponibilité par an, ou une dizaine de minutes par semaine.
- Débit de fonctionnement. Cet aspect est souvent exprimé en une ou plusieurs clés laisses de haute mer, tels que garantissant que le système sera capable de supporter jusqu'à 100 000 demandes d'utilisateurs simultanés ou gérer 10 000 transactions d'affaires simultanées.
- Temps de réponse opérationnelle. Le système peut également apporter des garanties concernant la vitesse à laquelle les demandes sont traitées, telles que 99 % de toutes les transactions commerciales va terminer dans les 2 secondes, et aucune transaction unique ne prendra plue de 10 secondes.

> [AZURE. REMARQUE] Certains contrats pour des systèmes commerciaux peuvent également inclure des SLA concernant le soutien à la clientèle, comme toutes les requêtes de bureau aide vont déclencher une réponse dans les 5 minutes, et que 99 % de tous les problèmes devraient être prises en compte sous 1 jour ouvrable. Efficace [suivi des problèmes](#issue-tracking) (décrit plus loin dans cette section) est essentiel pour relever les SLA comme celles-ci.

### Exigences pour la surveillance de la SLA
Au niveau plus élevé, l'exploitant doit être en mesure de déterminer en un coup de œil si le système se réunit le SLA convenu ou non et si pas alors à descendre et à examiner les facteurs sous-jacents afin de déterminer les raisons de la médiocre performance.

Des indicateurs de haut niveau typiques qui peuvent être représentés visuellement comprennent :

- Le pourcentage de disponibilité du service.
- Le débit de l'application (mesuré en termes de transactions réussies et/ou d'opérations par seconde).
- Le nombre de demandes d'application de réussite/échec.
- Le nombre de défauts d'application et système, les exceptions et les avertissements.

Tous ces indicateurs doivent pouvoir être filtré par un délai donné.

Une application en nuage comprendra probablement un certain nombre de composants et de sous-systèmes. Un opérateur doit être capable de sélectionner un indicateur de haut niveau et de voir comment il est composé de la santé des éléments sous-jacents. Par exemple, si le temps de disponibilité de l'ensemble du système est inférieure à une valeur acceptable, un opérateur doit être capable de zoomer et de déterminer quels éléments contribuent à cet échec.

> [AZURE. REMARQUE] Disponibilité du système doit être défini avec soin. Dans un système qui utilise la redondance pour assurer une disponibilité maximale, des instances individuelles d'éléments peuvent échouer, mais le système peut rester fonctionnel. Disponibilité du système tel que présenté par la surveillance de la santé devrait indiquer la disponibilité globale de chaque élément, et non pas nécessairement la question de savoir si le système a effectivement arrêté. En outre, échecs peuvent être isolés, donc même si un système spécifique n'est pas disponible le reste du système pourrait rester disponible, bien qu'avec une diminution des fonctionnalités (dans un système de commerce électronique, une défaillance dans le système peut empêcher un client de passer des commandes, mais le client pourrait encore être en mesure de parcourir le catalogue de produits).

Pour alerter les fins, le système devrait être en mesure de déclencher un événement si aucun des indicateurs haut niveau dépasse un seuil déterminé. Les détails de bas niveau des divers facteurs qui constituent l'indicateur de haut niveau devraient être disponibles sous forme de données contextuelles au système d'alerte.

### Exigences de données sources, instrumentation et données de collection
Les données brutes nécessaires pour faciliter le suivi de la SLA sont similaires à celle requise pour le contrôle de la performance ainsi que de certains aspects de la santé et l'analyse de la disponibilité (voir les sections pour plus de détails). Vous pouvez capturer ces données par :

- Effectuer le suivi de point de terminaison.
- Enregistrement des exceptions, les erreurs et avertissements.
- Suivi de l'exécution des demandes des utilisateurs.
- La disponibilité de tout service de tiers utilisés par le système de surveillance.
- À l'aide de compteurs et mesures de performances.

Toutes les données doivent être chronométrées et horodatées.

### Analyse des données de la SLA
Les données d'instrumentation doivent être agrégées pour générer une image de la performance globale du système et pour soutenir l'exploration afin de permettre l'examen de la performance des sous-systèmes sous-jacent. Par exemple, vous devriez être en mesure de :

- Calculer le nombre total de demandes d'utilisateurs pendant une période donnée et de déterminer le taux de réussite et l'échec de ces demandes.
- Combiner les temps de réponse des requêtes de l'utilisateur pour générer une vue d'ensemble des temps de réponse de système.
- Analyser l'état d'avancement du saut de requêtes utilisateur le temps de réponse d'une requête donnée vers le bas dans les temps de réponse des éléments de travail individuels dans cette demande.  
- Déterminer la disponibilité globale du système comme une pourcentage de disponibilité pour une période déterminée.
- Analyser la disponibilité de temps du pourcentage de chacun des composants individuels et des services dans le système. Cela peut impliquer l'analyse de journaux de générer par des services tiers.

Beaucoup de systèmes commerciaux est tenus de signaler les chiffres de performance réelle contre la SLA convenue pour une période déterminée, généralement un mois. Cette information peut servir à calculer les crédits ou autres formes de remboursements pour les clients si la SLA n'est pas atteints au cours de cette période. Vous pouvez calculer la disponibilité pour un service en utilisant la technique décrite dans la section [Analyse des données de disponibilité](#analyzing-availability-data).

À des fins internes, une organisation peut également suivre le nombre et la nature des incidents ayant provoqué des services à l'échec. Apprendre à résoudre ces problèmes rapidement, ou l'éliminer complètement, contribuera à réduire les temps d'arrêt et de respecter les SLA.

## L'audit
Selon la nature de la demande, il peut y avoir statutaires ou d'autres réglementations qui spécifient les exigences pour l'audit des opérations effectuées par les utilisateurs et enregistrement d'accès aux données de tous les. L'audit peut fournir des preuves liant des clients à des demandes précises ; la non-répudiation est un facteur important dans de nombreux systèmes de commerce électronique pour aider à maintenir la confiance entre un client et l'organisme responsable de l'application ou le service.

### Exigences pour l'audit
L'analyste doit être en mesure de retracer la séquence des opérations commerciales effectuées par les utilisateurs afin que vous pouvez reconstruire des actions des utilisateurs. Ceci peut être nécessaire, simplement comme une question de record, ou dans le cadre d'une enquête judiciaire.

Informations d'audit sont très sensibles car il inclura probablement des données qui identifie les utilisateurs du système ainsi que les tâches qu'ils accomplissent. Pour cette raison, il est fort probable que les informations d'audit vont être visualisées sous la forme de rapports uniquement disponibles pour les analystes de confiance plutôt qu'en utilisant un système interactif qui prend en charge les opérations graphiques détaillés. Un analyste devrait être en mesure de générer un ensemble de rapports, par exemple liste des activités de tous les utilisateurs qui se produisent pendant un laps de temps spécifié, détaillant la chronologie de l'activité pour un seul utilisateur ou la liste des opérations de la séquence exécutés contre une ou plusieurs ressources.

### Exigences de données sources, instrumentation et données de collection
Les principales sources d'information pour l'audit peuvent inclure :

- Le système de sécurité qui gère l'authentification des utilisateurs.
- Tracer les journaux enregistrant l'activité de l'utilisateur.
- Selon les journaux de sécurité, suivi de toutes les demandes de réseau identifiables et non identifiables.

Le format des données de vérification et la façon dont elle est stockée pourrait être guidée par exigences réglementaires. Par exemple, il n'est pas possible de nettoyer les données de quelque façon (il doit être enregistré dans son format original) et accès au référentiel dans lequel on le tient doit être protégé afin d'empêcher toute manipulation criminelle.

### Analyse des données de vérification
L'analyste doit être en mesure d'accéder aux données brutes dans son intégralité sous sa forme originale. Mis à part l'obligation de produire en commun les rapports de vérification, les outils utilisés pour analyser ces données sont susceptibles d'être spécialisé et gardé externes au système.

## Surveillance de l'utilisation
Surveillance de l'utilisation des pistes comment les fonctionnalités et les composants d'une application sont utilisés. Les données recueillies peuvent être utilisées pour :

- Déterminer quelles fonctions sont largement utilisées et déterminent des points chauds potentiels dans le système. Éléments haut-trafic pourraient bénéficier d'une répartition fonctionnelle ou même la réplication afin de répartir la charge plus uniformément. Cette information permet également de déterminer quelles fonctions sont rarement utilisées et sont des candidats possibles pour la retraite ou de remplacement dans une future version du système.
- Obtenir des informations sur les événements opérationnels du système sous des conditions normales d'utilisation. Par exemple, dans un site de commerce électronique, vous pouvez enregistrer les informations statistiques sur le nombre de transactions et le volume de clients qui sont responsables de ceux-ci. Cette information pourrait être utilisée pour la planification de la capacité que le nombre de clients augmente.
- Détecter (peut-être indirectement) la satisfaction de l'utilisateur avec les performances ou les fonctionnalités du système. Par exemple, si un grand nombre de clients dans un système de commerce électronique régulièrement abandonne leurs caddies cela pourrait être dû à un problème avec la fonctionnalité d'extraction.
- Générer des informations de facturation. Une application commerciale ou mutualisée service peut facturer le client pour les ressources qu'ils utilisent.
- Faire respecter des quotas. Si un utilisateur dans un système de plusieurs locataire dépasse leur quota payé de temps ou de ressources de traitement pendant une période déterminée, leur accès peut être limité ou traitement étranglé.

### Exigences pour la surveillance de l'utilisation
Afin d'examiner l'utilisation du système, l'exploitant devra généralement voir informations y compris :

- Le nombre de demandes étant traitées par chaque sous-système et réalisé à chaque ressource.
- Le travail accompli par chaque utilisateur.
- Le volume de stockage de données occupé par chaque utilisateur.
- Les ressources étant accessibles par chaque utilisateur.

Un opérateur doit également être capable de générer des graphiques, par exemple, afficher les utilisateurs les plus gourmands en ressources, ou les ressources plus fréquemment consultés.

### Exigences de données sources, instrumentation et données de collection
Suivi de l'utilisation peut être effectuée à un niveau relativement élevé, en notant l'heure de début et de fin de chaque demande et de la nature de la demande (lire, écrire et ainsi de suite, selon la ressource en question). Vous pouvez obtenir ces informations par :

- Suivi de l'activité des utilisateurs.
- Compteurs de performance capture mesurant l'utilisation pour chaque ressource.
- Surveillance de l'utilisation CPU et i/o des opérations effectuées par chaque utilisateur.

Pour fins de facturation, vous devez également pouvoir identité quels utilisateurs sont responsables d'effectuer les opérations et les ressources qui utilisent ces opérations. Les informations collectées doivent être suffisamment détaillées pour permettre une facturation exacte.

<a name="issue-tracking"></a>
## Suivi des problèmes
Clients et autres utilisateurs peuvent signaler des problèmes en cas d'événements imprévus ou comportement dans le système. Suivi des problèmes craint pour gérer ces questions, en les associant à des efforts pour résoudre les problèmes sous-jacents dans le système et informer les clients des solutions possibles.

### Exigences pour le suivi des problèmes
Suivi des problèmes sont souvent réalisée à l'aide d'un système distinct qui permet aux opérateurs d'enregistrer et de signaler les détails des problèmes rapportés par les utilisateurs. Ces détails peuvent inclure des informations telles que les tâches que l'utilisateur tente d'exécuter, les symptômes du problème, la séquence des événements et toute erreur ou messages d'avertissement ont été délivrées.

### Exigences de données sources, instrumentation et données de collection
La source de données initiales pour les données de suivi des problèmes est l'utilisateur qui a signalé le problème en premier lieu. L'utilisateur peut être en mesure de fournir des données supplémentaires comme un vidage sur incident (si l'application inclut un composant qui s'exécute sur le bureau), un instantané de l'écran, la date et l'heure à laquelle l'erreur s'est produite ainsi que toute autre information environnementale telles que leur emplacement.

Cette information peut être utilisé pour se nourrir à l'effort de débogage et aider à construire un carnet de commandes des versions futures du logiciel.

### Analyser les données de suivi des problèmes
Différents utilisateurs peuvent signaler le même problème, et le problème de système de suivi doit associer des rapports communs ensemble.

Les progrès de l'effort de débogage doivent être enregistrée contre chaque rapport de problème, et lorsque le problème est résolu le client peut être informé de la solution.

Si un utilisateur signale un problème reconnu avec une solution connue dans le numéro de système de suivi, l'exploitant doit être en mesure d'informer l'utilisateur de la solution immédiatement.

## Opérations de traçage et de débogage versions logicielles
Lorsqu'un utilisateur signale un problème, l'utilisateur est souvent seulement conscient de l'impact immédiat qu'il a sur ses opérations, et l'utilisateur peut seulement rapporter les résultats de leur propre expérience à un opérateur responsable de l'entretien du système. Ces expériences sont habituellement juste un symptôme visible d'une ou plusieurs des problèmes fondamentaux. Dans de nombreux cas, l'analyste devra creuser à travers la chronologie des opérations sous-jacentes pour établir la cause racine du problème (ce processus est dénommé _Analyse des causes profondes_).

> [AZURE. REMARQUE] Analyse des causes profondes peut découvrir des inefficacités dans la conception d'une application. Dans ces situations, il peut être possible de retravailler les éléments concernés et de les déployer dans le cadre d'une version ultérieure. Ce processus exige un contrôle minutieux, et les composants mis à jour doivent être étroitement surveillés.

### Exigences pour le traçage et de débogage
Pour le suivi des événements inattendus et autres problèmes, il est essentiel que les données de surveillance fournit suffisamment d'informations non seulement sur les problèmes qui se produisent à un niveau élevé, mais inclut également des détails suffisants pour permettre un analyste à remonter aux origines de ces questions et de reconstituer la séquence des événements qui ont eu lieu. Cette information doit être suffisante pour permettre à un analyste diagnostiquer la cause première de tous les problèmes afin qu'un développeur peut apporter les modifications nécessaires pour les empêcher de se reproduire.

### Exigences de données sources, instrumentation et données de collection
Dépannage peut implique le suivi de toutes les méthodes (et leurs paramètres) appelés dans le cadre d'une opération visant à mettre en place un arbre représentant le flux logique par le système lorsqu'un client fait une demande spécifique. Exceptions et avertissements générés par le système à la suite de ce flux doivent être capturées et enregistrées.

Pour prendre en charge le débogage, le système peut fournir des raccordements que permettent à un opérateur capturer les informations d'État à des points cruciaux dans le système, ou fournir des informations détaillées étape par étape comme un progrès des opérations sélectionnées. Capture de données à ce niveau de détail peut imposer une charge sur le système devrait être un processus temporaire, principalement utilisé lors d'une série très inhabituelle d'événements est difficile à reproduire, ou lorsqu'une nouvelle version de l'un ou plusieurs éléments dans un système nécessitent un suivi attentif pour s'assurer qu'elles fonctionnent comme prévu.

## Le pipeline de surveillance et diagnostic
Un système distribué à grande échelle de surveillance pose un défi important, et chacun des scénarios décrits dans la section précédente ne devrait pas nécessairement être considérée isolément. Il est susceptible d'être un chevauchement significatif dans les données de surveillance et de diagnostics nécessaires pour chaque situation, bien que ces données peuvent doivent être traitées et présentées de différentes manières. Pour ces raisons, vous devez prendre une vue globale de la surveillance et le diagnostic.

Vous pouvez envisager le processus de diagnostic et de surveillance tout comme un pipeline qui comprend les étapes illustrés à la Figure 1.

![](media/best-practices-monitoring/Pipeline.png)

_La figure 1.
Les étapes du pipeline surveillance et diagnostic_

Figure 1 highlights how the data for monitoring and diagnostics can come from a variety of data sources. The Instrumentation/Collection stage is concerned with instrumentation; determining which data to capture, how to capture it, and how to format this data so that it can be easily examined. The Analysis/Diagnosis phase takes the raw data and uses it to generate meaningful information that can be used to determine the state of the system. This information can be used to make decisions about possible actions to take, and the results can be fed back into the Instrumentation/Collection phase. The Visualization/Alerting stage phase presents a consumable view of the system state; it could display information in near real-time by using a series of dashboards, and it could generates reports, graphs, and charts to provide a historical view of the data that can help identify long-term trends. If information indicates that a KPI is likely to exceed acceptable bounds, then this stage can also trigger an alert to an operator. In some cases, an alert can also be used to trigger an automated process that attempts to take corrective actions, such as auto-scaling.

Notez que ces mesures constituent un processus de flux continu où les étapes sont passent en parallèle. Idéalement, toutes les phases doivent être configurables dynamiquement ; à certains moments, surtout lorsqu'un système a été récemment déployé ou connaît des problèmes, il peut être nécessaire de recueillir des données étendues sur une base plus fréquente. À d'autres moments, il devrait être possible de revenir à la capture d'un niveau de base d'informations essentielles pour vérifier que le système fonctionne correctement.

En outre, tout le processus de surveillance doit envisager une solution direct et permanente qui est soumis à des ajustements et améliorations à la suite de vos commentaires. Par exemple, vous pourriez commencer avec nombreux facteurs pour déterminer l'intégrité du système de mesure, mais au fil du temps l'analyse pourrait mener à un raffinement comme vous jetez des mesures qui ne sont pas pertinentes, vous permettant de mieux se concentrer sur les données dont vous avez besoin tout en minimisant le bruit de fond.

## Sources de données de surveillance et de diagnostiques
The information used by the monitoring process can come from several sources, as illustrated in Figure 1. At the application level, information comes from trace logs incorporated into the code of the system. Developers should follow a standard approach for tracking the flow of control through their code (for example, on entry to a method emit a trace message that specifies the name of the method, the current time, the value of each parameter, and any other pertinent information. Recording the entry and exit times could also prove useful). You should log all exceptions and warnings, and ensure that you retain a full trace of any nested exceptions and warnings. Ideally, you should also capture information that identifies the user running the code together with activity correlation information (to track requests as they pass through the system), and log attempts to access all resources such as message queues, databases, files, and other dependent services; this information can be used for metering and auditing purposes.

Faire de nombreuses applications utilisent des bibliothèques et de cadres pour effectuer des tâches courantes telles que l'accès à un magasin de données ou de communication sur un réseau. Ces cadres peuvent être configurables à la sortie de leurs propres messages de trace et des informations de diagnostic crues tels que les taux de transaction, les données transmission réussites et les échecs et ainsi de suite.

> [AZURE. REMARQUE] Nombreuses infrastructures modernes publient automatiquement les performances et les événements de trace, et capturer ces informations, il suffit de fournir un moyen pour récupérer et stocker où il peut être traité et analysé.

Le système d'exploitation sur lequel l'application s'exécute peut être une source d'informations de bas niveau à l'échelle du système, tels que des compteurs de performance indiquant le taux de I/O, utilisation de la mémoire et l'utilisation du processeur. Erreurs de système d'exploitation (par exemple, le refus d'ouvrir un fichier correctement) peuvent également être déclarées.

Vous devriez également considérer l'infrastructure sous-jacente et les composants sur lequel votre système s'exécute. Services de stockage, réseaux virtuels et les machines virtuelles peuvent être sources de compteurs de performance de niveau infrastructure importante et autres données de diagnostic.

Si votre application utilise d'autres services externes, tel qu'un serveur web ou un système de gestion de base de données, ces services peuvent publier leurs propres informations de traçage, les journaux et les compteurs de performance. Citons les vues de gestion dynamique SQL Server pour le suivi des opérations exécutées sur une base de données SQL Server, et trace de Internet Information Server consigne pour l'enregistrement des demandes effectuées sur un serveur web.

Comme les éléments d'un système sont modifiés et les nouvelles versions déployées, il est important de pouvoir issues de l'attribut, les événements et les paramètres pour chaque version. Cette information devrait être liée à la canalisation de sortie afin que les problèmes avec une version spécifique d'un composant peuvent être suivies rapidement et corrigées.

Problèmes de sécurité peuvent se produire à n'importe quel point dans le système. Par exemple, un utilisateur peut tenter d'ouvrir une session avec un ID d'utilisateur non valide ou mot de passe ; un utilisateur authentifié peut essayer et obtenir un accès non autorisé à une ressource ; ou un utilisateur peut fournir une clé non valide ou obsolète pour accéder à des informations chiffrées. Liés à la sécurité des informations sur les demandes réussies et défaillants doivent toujours être consignées.

La section [Instrumentation d'une Application](#instrumenting-an-application) contient d'autres directives sur les renseignements que vous devez capturer, mais il existe une variété de stratégies que vous pouvez utiliser pour recueillir cette information en premier lieu :

- **Application/système de surveillance**. Cette stratégie utilise des sources internes au sein de l'application, les cadres d'application système d'exploitation et infrastructure. Le code de l'application elle-même peut générer ses propres données aux points remarquables de surveillance pendant le cycle de vie d'une demande de client. L'application peut inclure des instructions de traçage qui peuvent être sélectivement activées ou désactivées selon les circonstances. Il serait également possible d'injecter des diagnostics dynamiquement à l'aide d'un cadre de diagnostic. Ces cadres fournissent généralement des plugins qui peuvent attacher aux divers points d'instrumentation dans votre code et saisir les données de trace à ces points.

En outre, votre code et/ou l'infrastructure sous-jacente peut déclencher des événements à des points critiques. Les agents qui sont configurés pour écouter ces événements de surveillance permet d'enregistrer les informations d'événement.

- **Surveillance de l'utilisateur réel**. Cette approche enregistre les interactions entre un utilisateur et l'application et indique le débit de chaque demande et de réponse. Cette information peut avoir un double usage : il peut être utilisé pour la mesure de son utilisation par chaque utilisateur, et il peut être utilisé pour déterminer si les utilisateurs reçoivent une qualité de service (par exemple, temps de réponse rapide, faible latence et minimes erreurs qui se produisent). Les données saisies peuvent servir à identifier les sujets de préoccupation où les défaillances se produisent plus fréquemment et éléments où le système ralentit, peut-être en raison de points chauds dans la demande ou d'une autre forme de goulot d'étranglement. Si cette approche est soigneusement mis en œuvre, il peut être possible de reconstituer les flux des utilisateurs par le biais de l'application pour le débogage et à des fins de test.

	> [AZURE. IMPORTANT] Les données saisies par les utilisateurs réels de surveillance devraient considérer comme très sensibles car elle peut comporter des documents confidentiels. Si vous enregistrez les données capturées il doit être stocké en toute sécurité. Si les données sont utilisées pour la performance de surveillance ou des fins de débogage, tous les renseignements personnels identifiables doivent être éliminés en premier.

- **Contrôle utilisateur synthétique**. Dans cette approche, vous écrivez votre propre client de test qui simule un utilisateur et exécute une série configurable mais typique d'opérations. Vous pouvez suivre la performance du test client pour aider à déterminer l'état du système. Vous pouvez également utiliser plusieurs instances de la client de test dans le cadre d'une opération de test de charge pour établir comment le système réagit sous contrainte, et quelle sorte de contrôle de sortie est généré dans ces conditions.

	> [AZURE. REMARQUE] Vous pouvez implémenter l'utilisateur véritable et synthétique suivi en incluant le code qui retrace et moment de l'exécution des appels de méthode et d'autres pièces essentielles d'une application.

- **Profilage**. Cette approche est principalement destinée à la surveillance et d'amélioration des performances des applications. Plutôt que fonctionnant au niveau fonctionnel employé par contrôle utilisateur véritable et synthétique, il capture les informations de niveau inférieur comme l'application s'exécute. Le profilage peut être implémenté par échantillonnage périodique de l'état de l'exécution d'une application (déterminer quel morceau de code que l'application s'exécute à un moment donné dans le temps), ou en utilisant l'instrumentation qui insère des sondes dans le code à des moments importants (par exemple, le début et la fin d'un appel de méthode) et enregistre les méthodes qui ont été invoquées, à quelle heure , et combien de temps qu'a chaque appel. Ces données peuvent ensuite être analysées pour déterminer quelles parties de la demande pourraient causer des problèmes de performances.

- **Surveillance du point de terminaison**. Cette technique utilise un ou plusieurs paramètres de diagnostics exposés par l'application spécifiquement pour permettre un suivi. Un point de terminaison fournit une voie dans le code d'application et peut retourner des informations sur la santé du système. Points de terminaison différents pourraient porter sur divers aspects de la fonctionnalité. Vous pouvez écrire votre propre client de diagnostics qui envoie des demandes périodiques à ces points de terminaison et d'assimiler les réponses. Cette démarche est décrite plus en détail par le [Modèle de suivi de point de terminaison santé](https://msdn.microsoft.com/library/dn589789.aspx) sur le site Web de Microsoft.

Pour une protection maximale, vous devez utiliser ces techniques en combinaison avec l'autre.

<a name="instrumenting-an-application"></a>
## Instrumentation d'une application
L'instrumentation est un élément essentiel de la procédure de suivi ; vous pouvez seulement prendre des décisions importantes sur la performance et la santé d'un système si vous capturez les données qui vous permet de prendre ces décisions en premier lieu. Les informations que vous vous réunissez en utilisant les instruments devraient être suffisantes pour vous permettre d'évaluer la performance, de diagnostiquer les problèmes et de prendre des décisions sans nécessiter que vous devez vous connecter à un serveur de production distant pour effectuer le suivi (débogage) manuellement.

Les données d'instrumentation comprendra généralement les informations enregistrées dans les journaux de suivi et mesures :

- The contents of a trace log can be the result of textual data written by the application, binary data created as the result of a trace event (if the application is using Event Tracing for Windows – ETW), or they can be generated from system logs that record events arising from parts of the infrastructure, such as a web server. Textual log messages are often designed to be human-readable, but they should also be written in a format that enables them to be easily parsed by an automated system. You should also categorize logs; don't write all trace data to a single log but use separate logs to record the trace output from different operational aspects of the system. This enables you to quickly filter log messages by reading from the appropriate log rather than having to process a single lengthy file. Never write information that has different security requirements (such as audit information and debugging data) to the same log.

	> [AZURE. REMARQUE] Un journal peut être mis en œuvre sous forme de fichier sur le système de fichiers, ou elle pourrait être tenue dans un autre format comme un blob dans stockage blob. Informations du journal pourraient également lieu à stockage plus structuré, comme les lignes d'une table.

- Metrics simplement sera généralement une mesure ou le comte de certains aspect ou une ressource dans le système à une heure spécifique avec un ou plusieurs associés balises ou dimensions (parfois dénommé un _échantillon_). Une instance unique d'une métrique n'est généralement pas utile en vase clos ; métrique doit plutôt être capturées au fil du temps. La question clé à considérer est quels paramètres vous devez enregistrer et comment fréquemment. Générer des données pour des mesures trop souvent peut imposer une charge supplémentaire importante sur le système, alors que capture metrics rarement peut vous amener à manquer les circonstances conduisant à un événement important. Les considérations variera de la métrique de la métrique. Par exemple, utilisation de l'UC sur un serveur peut varier considérablement de seconde à seconde, mais une utilisation élevée ne devient un problème si elle est longue sur un nombre de minutes.

<a name="information-for-correlating-data"></a>
### Informations de corrélation des données
You can easily monitor individual system-level performance counters, capture metrics for resources, and obtain application trace information from various log files, but some forms of monitoring require the analysis and diagnostics stage in the monitoring pipeline to correlate the data retrieved from several sources. This data may take several forms in the raw data, and the analysis process must be provided with sufficient instrumentation data to be able to map these different forms. For example, at the application framework level, a task might be identified by a thread ID but within an application the same work might be associated with the user ID for the user performing that task. Furthermore, there is unlikely to be a 1:1 mapping between threads and user requests as asynchronous operations may reuse the same threads to perform operations on behalf of more than one user.  To complicate matters further, a single request may be handled by more than one thread as execution flows through the system. If possible, associate each request with a unique activity ID that is propagated through the system as part of the request context (the technique for generating and including activity IDs in trace information will be dependent on the technology being used to capture the trace data).

Toutes les données de surveillance devraient être horodatées de la même manière. Par souci de cohérence, enregistrer toutes les dates et les heures en utilisant le temps universel coordonné. Cela aidera pour vous permettre de plus facilement des séquences de trace d'événements.

> [AZURE. REMARQUE] Ordinateurs en réseaux et différents fuseaux horaires ne peuvent pas synchronisés, donc vous ne devriez pas dépendre à l'aide de timestamps seul pour corréler les données d'instrumentation qui s'étend sur plusieurs machines.

### Quelle information doivent inclure les données d'instrumentation ?
Considérez les points suivants lorsque vous décidez qui vous devez collecter les données d'instrumentation :

- Informations capturées par les événements de suivi devraient être machine et lisible par l'homme.  Vous devez adopter des schémas bien définis pour cette information faciliter le traitement automatisé de données entre les systèmes et assurent la cohérence d'opérations et d'ingénierie du personnel en lisant les journaux. Inclure des informations environnementales, telles que l'environnement de déploiement, l'ordinateur sur lequel le processus s'exécute, les détails du processus et la pile des appels.  
- Le profilage peut imposer une surcharge significative sur le système et ne doit être activé que lorsque cela est nécessaire. Profilage à l'aide d'instrumentation enregistre un événement (comme un appel de méthode) chaque fois qu'il apparaît, alors que seulement l'échantillonnage enregistre les événements sélectionnés. La sélection pourrait être basé sur le temps – une fois toutes les N secondes, ou de fréquence – une fois que toutes les N prie. Si les événements se produisent très fréquemment, le profilage par instrumentation peut causer un fardeau trop et lui-même un impact sur les performances globales. Dans ce cas, la méthode d'échantillonnage peut être préférable. Toutefois, si la fréquence des phénomènes est faible, alors échantillonnage pourrait leur manquer et dans ce cas les instruments pourrait être la meilleure approche.
- Fournir un contexte suffisant pour permettre à un développeur ou un administrateur de déterminer la provenance de chaque demande. Ceci peut inclure une forme quelconque d'activité ID identifiant une instance spécifique d'une demande et les informations qui peuvent servir à établir une corrélation entre cette activité avec le travail de calcul effectué et les ressources utilisées. Notez que ce travail pouvant traverser des limites de processus et des machines. Pour le comptage, le cadre devrait également inclure (directement ou indirectement par l'intermédiaire des autres informations en corrélation) une référence au client qui a provoqué la demande à faire. Ce contexte fournit des informations précieuses sur l'état de l'application à la fois que les données de surveillance a été capturées.
- Enregistrer toutes les demandes et les lieux ou les régions d'où ces demandes sont faites. Cette information peut aider à déterminer s'il y a n'importe quel hotspot emplacement précis et fournir des données qui peuvent être utiles pour déterminer s'il doit repartitionner une application ou les données qu'il utilise.
- Enregistrer et saisir les détails des exceptions avec soin. Informations de débogage souvent critiques sont perdues en raison de la gestion des exceptions pauvres. Capturer tous les détails des exceptions levées par l'application, y compris des exceptions internes et autres informations de contexte, y compris la pile des appels si possible.
- Be consistent in the data that the different elements of your application capture as this can assist in analyzing events and correlating them with user requests. Consider utilizing a comprehensive and configurable logging package to gather information rather than depending on developers implementing different parts of the system all adopting the same approach. Gather data from key performance counters, such as the volume of I/O being performed, network utilization, number of requests, memory use, and CPU utilization. Some infrastructure services may provide their own specific performance counters, such as the number of connections to a database, the rate at which transactions are being performed, and the number of transactions that succeed or fail. Applications might also define their own specific performance counters.
- Ouvrez une session tous les appels effectués à des services externes, tels que les systèmes de base de données, services web ou autres au niveau du système services fournis dans le cadre de l'infrastructure (Azure mise en cache par exemple). Enregistrer les informations sur le temps nécessaire pour effectuer chaque appel et le succès ou l'échec de l'appel. Si possible, capturer des informations sur toutes les tentatives et des échecs pour des erreurs passagères qui se produisent.

### Assurer la compatibilité avec les systèmes de télémétrie
Dans de nombreux cas, l'information produite par l'instrumentation est générée comme une série d'événements et passée à un système de télémesure distinct pour traitement et analyse. Un système de télémétrie est généralement indépendamment de toute application spécifique ou de la technologie, mais s'attend à des informations à suivre un format spécifique, généralement défini par un schéma. Le schéma spécifie efficacement un contrat définissant les champs de données et les types qui peut ingérer le système de télémétrie. Le schéma doit être généralisé pour permettre aux données provenant d'une gamme de plates-formes et de périphériques.

Un schéma commun doit inclure les champs qui sont communes à tous les événements d'instrumentation, tels que le nom de l'événement, l'heure de l'événement, l'adresse IP de l'expéditeur et les indications nécessaires pour la mise en corrélation avec d'autres événements (par exemple, un ID utilisateur, un ID de périphérique et un ID d'application). N'oubliez pas que les événements peuvent être déclenchés par n'importe quel nombre de dispositifs, donc le schéma ne devrait pas être dépendant du type de dispositif. En outre, les événements pour la même application peuvent être déclenchés par un certain nombre de différents dispositifs ; l'application peut prendre en charge itinérant ou quelque autre forme de croix-dispositif de distribution. Idéalement, la majorité de ces champs doit correspondre à la sortie de la bibliothèque de journalisation utilisée pour capturer les événements.

Le schéma peut aussi inclure les champs domaine qui se rapportent à un scénario particulier commun entre les différentes applications. Cela pourrait être des informations sur les exceptions, démarrage de l'application et événements de la fin et succès d'appel API web services ou échec. Toutes les applications qui utilisent le même ensemble de champs domaine doivent émettre le même ensemble d'événements, permettant à un ensemble de communes rapports et analytique à construire.

Enfin, un schéma peut contenir des champs personnalisés pour capturer les détails des événements spécifiques à l'application.

### Méthodes conseillées pour l'instrumentation d'applications
La liste suivante résume les méthodes conseillées pour l'instrumentation d'une application distribuée en cours d'exécution dans le nuage.

- Rendre les journaux faciles à lire et facile à analyser. Utilisation structurée de journalisation lorsque c'est possible. Être concis et descriptif dans les messages du journal.
- Dans tous les journaux, identifier la source et fournir le contexte et des informations de chronométrage comme chaque enregistrement de journal est écrit.
- Utilisez le même fuseau horaire et le même format pour tous les horodateurs. Cela aidera à établir une corrélation entre les événements pour les opérations qui s'étendent de matériel et services qui s'exécutent dans différentes régions géographiques.
- Classer les journaux et écrire des messages dans le fichier journal approprié.
- Ne pas divulguer des informations sensibles sur le système ou des informations personnelles sur les utilisateurs. Frottez cette information avant il se connecte, mais faire en sorte que les détails pertinents sont conservées. Par exemple, retirer des chaînes de connexion de base de données de l'ID et le mot de passe, mais écrire les autres informations dans le journal afin qu'un analyste peut déterminer que le système accède à la base de données correct. Connecter toutes les exceptions critiques, mais permettent à l'administrateur activer la journalisation sur et en dehors pour les niveaux inférieurs des exceptions et des avertissements. En outre, capturer et enregistrer toutes les informations de logique de nouvelle tentative. Ces données peuvent être utiles pour la surveillance de la santé transitoire du système.
- Trace des appels de processus, tels que les requêtes de base de données ou des services web externes.
- Ne mélangez pas enregistrer les messages avec les exigences de sécurité différentes dans le même fichier journal. Par exemple, n'écrivez debug et informations dans le même journal d'audit.
- À l'exception des événements de l'audit, tous les appels de journalisation doivent être opérations fire-and-forget qui ne doivent pas bloquer la progression des opérations de l'entreprise. Événements de l'audit sont exceptionnels car ils sont essentiels à l'entreprise et peuvent être considérées comme un élément fondamental des activités commerciales.
- Exploitation forestière doit être extensible et n'ont pas toutes les dépendances directes sur des cibles concrètes. Par exemple, au lieu d'écrire des informations à l'aide de _System.Diagnostics.Trace_, définissez une interface abstraite (tels que _ILogger_) qui expose les méthodes d'enregistrement et qui peuvent être implémentée à l'aide de tout moyen approprié.
- Tout enregistrement doit être infaillible et ne devrait jamais déclencher des erreurs en cascade. Exploitation forestière ne doit pas lever d'exception.
- Traiter l'instrumentation comme un processus itératif en cours et consultez les journaux régulièrement, pas seulement quand il y a un problème.

## Collecte et stockage de données
The collection stage of the monitoring process is concerned with retrieving the information generated by instrumentation, formatting this data to make it easier to consume by the analysis/computation phase, and saving the transformed data in reliable and accessible storage. The instrumentation data that you gather from different parts of a distributed system could be held in a variety of locations and with varying formats. For example, your application code might generate trace log files, and generate application event log data, while performance counters that monitor key aspects of the infrastructure that your application uses could be captured by using other technologies.  Any third-party components and services that your application uses may provide instrumentation information in different formats, using separate trace files, blob storage, or even a custom data store.

Collecte des données est souvent réalisée en mettant en place un service de collecte qui peut fonctionner de manière autonome de l'application qui génère les données d'instrumentation. La figure 2 illustre un exemple de cette architecture, mettant en évidence le sous-système de collecte de données instrumentation.

![](media/best-practices-monitoring/TelemetryService.png)

_La figure 2.
Collecte de données d'instrumentation_

Notez qu'il s'agit d'une vue simplifiée. Le service de collecte n'est pas nécessairement un processus unique et peut comporter plusieurs éléments constitutifs fonctionnant sur des machines différentes, comme décrit dans les sections suivantes. En outre, si l'analyse de certaines données de télémétrie doit être effectuée rapidement (analyse, à chaud comme décrit dans la section [Soutenir l'analyse chaud, chaud et froid](#supporting-hot-warm-and-cold-analysis) plus loin dans ce document), les composantes locales opérant en dehors du service de collecte peuvent s'exécuter les tâches d'analyse immédiatement. La figure 2 illustre cette situation pour des événements sélectionnés ; après traitement analytique, les résultats peuvent être envoyés directement à la visualisation et le sous-système d'alerte. Données soumises à une analyse tiède ou froide se trouvent dans stockage tandis qu'il attend le traitement.

Pour les applications Azure et services, Azure Diagnostics (WAD) fournit une solution possible pour capturer des données. WAD recueille des données provenant des sources suivantes pour chaque nœud de calcul, il regroupe ensemble et puis il télécharge sur Azure storage :

- Journaux d'Azur
- Journaux IIS
- Journaux IIS n'a pas pu demander
- Journaux d'événements Windows
- Compteurs de performance
- Vidages
- Journaux d'Azur Diagnostic infrastructure  
- Journaux d'erreurs personnalisées

Pour plus d'informations, consultez l'article [Azure : Bases de télémétrie et de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) sur le site Web de Microsoft.

### Stratégies de collecte de données d'instrumentation
Compte tenu de la nature élastique du nuage et d'éviter la nécessité de récupérer manuellement les données télémétriques de chaque nœud dans le système, vous devriez prendre des dispositions pour les données à transférer vers un emplacement central et consolidée. Dans un système qui s'étend sur plusieurs centres de données, il peut être utile d'abord collecter, consolider et stocker des données sur une base de région par région et puis agrégation des données régionales dans un seul système central.

Pour optimiser l'utilisation de bande passante, vous pouvez choisir de transférer des données moins urgentes en morceaux, comme des lots. Toutefois, les données ne doivent pas être retardées indéfiniment, surtout si elle contient des informations sensibles au temps.

#### _En tirant et en poussant les données d'instrumentation_
Le sous-système de collecte de données instrumentation peut soit activement extraient les données d'instrumentation les divers journaux et d'autres sources pour chaque instance de l'application (la _modèle d'extraction_), ou il peut agir comme un récepteur passif attend les données à envoyer des composants qui constituent chaque instance de l'application (la _modèle push_).

Une approche à mettre en oeuvre le modèle d'extraction consiste à utiliser des agents de surveillance exécutés localement avec chaque instance de l'application. Un agent de surveillance est un processus distinct qui récupère périodiquement télémétrie (tire) des données recueillies au niveau du nœud local et écrit ces informations directement dans un stockage centralisé qui est partagé par toutes les instances de l'application. C'est le mécanisme mis en place par WAD. Chaque instance d'un rôle web ou travailleur Azure peut être configuré pour capture de diagnostic et d'autres informations de trace qui sont stockées localement. L'agent de surveillance qui longe chaque copie les données spécifiées à Azure storage. La page [Configuration des Diagnostics pour les ordinateurs virtuels et de Cloud Azur Services](https://msdn.microsoft.com/library/azure/dn186185.aspx) sur le site Web Microsoft fournissent des informations supplémentaires sur ce processus. Certains éléments, tels que les journaux IIS, crash dumps et message d'erreur personnalisé vers blob storage, tandis que les données du journal des événements Windows, événements ETW et compteurs de performance, les journaux est écrits est enregistré dans le stockage de la table. La figure 3 illustre ce mécanisme :

![](media/best-practices-monitoring/PullModel.png)

_La figure 3.
À l'aide d'un agent de surveillance pour tirer des renseignements et écrire dans un stockage partagé_

> [AZURE. REMARQUE] À l'aide d'un agent de surveillance est idéal pour capturer les données d'instrumentation naturellement extraites d'une source de données, comme l'information provenant des vues de gestion SQL Server, ou la longueur d'une file d'attente du Bus de services Azure.

Pour plus d'informations sur la configuration et l'utilisation d'Azur Diagnostics, visitez le [Collecter les données de journalisation à l'aide des Diagnostics d'Azur](https://msdn.microsoft.com/library/azure/gg433048.aspx) page sur le site Web de Microsoft.

Données de télémétrie pour une petite application qui s'exécute sur un nombre limité de nœuds peuvent facilement être stockées dans un seul emplacement à l'aide de l'approche décrite juste. Toutefois, une application mondiale, hautement évolutive et complexe nuage pourrait facilement générer des énormes volumes de données parmi la centaine de rôles web et de travail, éclats de base de données et autres services. Ce flot de données pourrait facilement submerger la bande passante de I/O disponible avec un seul emplacement central. Par conséquent, votre solution de télémétrie doit être évolutive pour l'empêcher agissant comme un goulot d'étranglement, comme le système se développe et idéalement comporter un degré de redondance pour réduire les risques de perdre des données de surveillance importantes (comme les données d'audit ou facturation) si le système tombe en panne.

Pour résoudre ces problèmes, vous pouvez implémenter les files d'attente. La figure 4 illustre cette structure. Dans cette architecture, l'agent de surveillance local (s'il peut être configuré de manière appropriée) ou données personnalisé service de collecte (sinon) données de messages à une file d'attente et un processus séparé en cours d'exécution de façon asynchrone (le Service de rédaction de stockage à la Figure 4) prend les données dans cette file d'attente et écrit dans le stockage partagé. Une file d'attente de messages est adapté pour ce scénario car il fournit au moins une fois sémantique de veiller à ce qu'une fois validé, les données en file d'attente ne seront pas perdues. Le Service de rédaction de stockage peut être implémenté à l'aide d'un rôle de travail distinct.

![](media/best-practices-monitoring/BufferedQueue.png)

_La figure 4.
À l'aide d'une file d'attente pour les données d'instrumentation tampon_

Le service de collecte des données locales peut ajouter des données à une file d'attente dès sa réception. La file d'attente agit comme un tampon et le stockage écriture service peut récupérer et écrire les données à son propre rythme. Par défaut, une file d'attente fonctionne sur une base premier-in-first-out, mais vous pouvez donner la priorité à messages pour accélérer leur file d'attente s'ils contiennent des données qui doivent être traitées plus rapidement. Pour plus d'informations, consultez le [File d'attente prioritaire](https://msdn.microsoft.com/library/dn589794.aspx) modèle. Alternativement, vous pouvez utiliser des canaux différents (comme sujets de Service de Bus) pour des données directes vers différentes destinations selon la forme du traitement analytique nécessaire.

Pour l'évolutivité, vous pouvez exécuter plusieurs instances du stockage par écrit le service. S'il y a un volume élevé d'événements, vous pouvez utiliser un concentrateur d'événements à envoyer les données de ressources de calcul différentes pour le traitement et le stockage.

<a name="consolidating-instrumentation-data"></a>
#### _Consolider les données d'instrumentation_
The instrumentation data retrieved by the data collection service from a single instance of an application gives a localized view of the health and performance of that instance. To assess the overall health of the system, it is necessary to consolidate some aspects of the data in the local views together. This can be performed after the data has been stored, but in some cases it could also be achieved as the data is collected. Rather than being written directly to shared storage, the instrumentation data could pass through a separate data consolidation service which combines data and acts as a filter and cleanup process. For example, instrumentation data that includes the same correlation information such as an activity ID can be amalgamated (it is possible that a user starts performing a business operation on one node, and then gets transferred to another node in the event of node failure or depending on how load-balancing is configured). This process can also detect and remove any duplicated data (always a possibility if the telemetry service uses message queues to push instrumentation data out to storage). Figure 5 illustrates an example of this structure.

![](media/best-practices-monitoring/Consolidation.png)

_La figure 5.
En utilisant un service distinct pour consolider et nettoyer les données d'instrumentation_

### Stocker les données d'instrumentation
The previous discussions have depicted a rather simplistic view of the way in which instrumentation data is stored. In reality, it can make sense to store the different types of information by using technologies that are most appropriate to the way in which each type is likely to be used. For example, Azure blob and table storage have some similarities in the way in which they are accessed, but have limitations concerning the operations that you can perform using them and the granularity of the data that they hold is quite different. If you need to perform more analytical operations or require full-text search capabilities on the data, it may be more appropriate to use data storage that provides capabilities that are optimized for specific types of queries and data access. For example, performance counter data could be stored in a SQL database to enable ad-hoc analysis; trace logs might be better stored in Azure DocumentDB; security information could be written to HDFS; information requiring full-text search could be stored by using Elastic Search (which can also speed searches by using rich indexing.) You can implement an additional service that periodically retrieves the data from shared storage, partitions and filters the data according to its purpose, and then writes it to an appropriate set of data stores as shown in Figure 6. An alternative approach is to include this functionality in the consolidation and cleanup process and write the data directly to these stores as it is retrieved rather than saving it in an intermediate shared storage area. Each approach has its advantages and disadvantages. Implementing a separate partitioning service lessens the load on the consolidation and cleanup service, and enables at least some of the partitioned data to be regenerated if necessary (depending on how much data is retained in shared storage). However, it consumes additional resources, and there may be a delay between the instrumentation data being received from each application instance and this data being converted into actionable information.

![](media/best-practices-monitoring/DataStorage.png)

_La figure 6.
Le partitionnement des données selon analytiques et les besoins de stockage_

Les mêmes données d'instrumentation peuvent être requises pour plus d'un but. Par exemple, les compteurs de performance peuvent être utilisés pour présenter un tableau historique des performances du système au fil du temps, mais cette information pourrait également être combinée avec d'autres données d'utilisation pour générer des informations de facturation client. Dans ces situations, les mêmes données peuvent être envoyées à plus d'une destination, comme une base de données de document qui peut agir comme un magasin à long terme pour tenir vos informations de facturation et un magasin multidimensionnel pour le traitement analytique complexe performance.

Vous devriez également considérer combien d'urgence, les données sont requises. Données fournit des informations d'alerte doit pouvoir accéder rapidement et donc devant être tenues en stockage de données rapide et indexées ou structurées de façon à optimiser les requêtes qui exécute le système d'alerte. Dans certains cas, il peut être nécessaire pour le service de télémétrie qui rassemble les données sur chaque nœud de formater et de sauvegarder des données localement afin qu'une instance locale du système d'alerte peut informer rapidement de tout problème. Les mêmes données peuvent être expédiées vers le stockage écriture service affichés dans les diagrammes précédents et centralisés si il est aussi nécessaire à d'autres fins.

Informations utilisées pour en savoir plus considéré comme analyse, signalé et pour repérer les tendances historiques sont moins urgentes et peuvent être stockées d'une manière qui prend en charge l'exploration de données et des requêtes ad hoc. Pour plus d'informations, reportez-vous à la section [Soutenir l'analyse chaud, chaud et froid](#supporting-hot-warm-and-cold-analysis) plus loin dans ce document.

#### _Rotation et conservation des données_
Instrumentation can generate considerable volumes of data. This data can be held in several places, starting with the raw log files, trace files, and other information captured at each node to the consolidated, cleaned, and partitioned view of this data held in shared storage. In some cases, once the data has been processed and transferred the original raw source data can be removed from each node. In other cases, it may be necessary or simply useful to save the raw information. For example, data generated for debugging purposes may be best left available in its raw form but can then be discarded quite quickly once any bugs have been rectified. Performance data often has a longer life to enable it to be used to spot performance trends and for capacity planning. The consolidated view of this data is usually kept on-line for a finite period to enable fast access, after which it might be archived or discarded. Data gathered for metering and billing customers may need to be saved indefinitely. Additionally, regulatory requirements might dictate that information collected for auditing and security purposes will also need to be archived and saved. This data is also sensitive and may need to be encrypted or otherwise protected to prevent tampering. You should never record information such as users' passwords or other information that could be used to commit identity fraud; such details should be scrubbed from the data before it is stored.

#### _Sous-échantillonnage_
Il est souvent utile stocker des données historiques pour pouvoir repérer les tendances à long terme. Plutôt que de sauver les anciennes données dans son intégralité, il peut être possible de ré-échantillonner les données afin de réduire sa résolution et économiser sur les coûts de stockage. À titre d'exemple, plutôt que sauver les indicateurs de performance de minute en minute, données plus d'un mois pourraient être consolidées se pour former une opinion heure par heure.

### Meilleures pratiques pour recueillir et stocker des informations de journalisation
La liste suivante récapitule les meilleures pratiques pour la capture et le stockage des informations de journalisation.

- L'agent de surveillance ou le service de collecte des données doit s'exécuter comme un service d'out-of-process et devrait être simple à déployer.
- Toutes les sorties de l'agent de surveillance ou le service de collecte de données doit être un format agnostique qui est indépendant de la machine, système d'exploitation ou protocole de réseau. Par exemple, émettent des informations dans un format auto-descriptif tels que JSON, MessagePack, ou Protobuf plutôt que ETL/ETW. En utilisant un format standard permet au système de construction de pipelines de traitement ; composants qui lire, transforment et exporter des données sous la forme convenue peuvent être facilement intégrés.
- Le processus de collecte de données et de surveillance doit être infaillible et ne doit pas déclencher les conditions d'erreur en cascade.
- Dans le cas d'une défaillance passagère lors de l'envoi des informations à un récepteur de données du service de collecte données ou agent de surveillance doivent être prêts à réorganiser les données télémétriques afin que l'information la plus récente est envoyée d'abord (le service de collecte de données/agent surveillance peut choisir déplacer les données anciennes, ou enregistrez-le localement et de le transmettre plus tard pour le rattraper, à sa seule discrétion).

## Analyse des données et diagnostic des problèmes de
Une place importante dans le processus de surveillance et diagnostic d'analyse des données recueillies afin d'obtenir une image du bien-être global du système. Vous devriez avez défini vos propres indicateurs de performance clés et les données de performance, et il est important de comprendre comment vous pouvez structurer les données qui ont été recueillies pour répondre à vos besoins d'analyse. Il est également important de comprendre comment les données saisies dans les différents indicateurs et des fichiers journaux est corrélée, comme cette information peut être la clé pour une séquence d'événements de suivi et aider à diagnostiquer des problèmes qui se posent.

Comme décrit dans la section [Consolider les données d'Instrumentation](#consolidating-instrumentation-data), the data for each part of the system is typically captured locally, but generally needs to be combined with data generated at other sites that participate in the system. This information requires careful correlation to ensure that data is combined accurately. For example, the usage data for an operation may span a node hosting a web site to which a user connects, a node running a separate service accessed as part of this operation, and data storage held on a further node. This information needs to be tied together to provide an overall view of the resource and processing usage for the operation. Some pre-processing and filtering of data might occur on the node on which the data is captured, while aggregation and formatting is more likely to occur on a central node.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Soutenir l'analyse chaud, chaud et froid
Analyse et le formatage des données pour la visualisation, de rapports et alertes fins peuvent être un processus complexe qui utilise son propre ensemble de ressources. Certaines formes de surveillance sont temps critiques et nécessitent une analyse immédiate des données soit efficace. Ceci est connu comme _analyse chaud_. Les exemples incluent les analyses requises permettant d'alerter et de certains aspects de la surveillance de la sécurité (tels que la détection d'une attaque contre le système). Données nécessaires à ces fins doivent être rapidement disponibles et structuré pour un traitement efficace ; il certains cas il peut être nécessaire de déplacer l'analyse de traitement pour les nœuds individuels sur lesquels les données sont conservées.

Autres formes d'analyse sont moins urgentes et peuvent exiger certains calculs et agrégation une fois les données brutes ont été reçues. Ceci est connu comme _analyse chaud_. Analyse des performances souvent tombe dans cette catégorie. Dans ce cas, un événement isolé, unique performance est peu susceptible d'être statistiquement significative (elle pourrait être causée par un pic soudain ou glitch), tandis que les données d'une série d'événements doivent fournir une image plus fiable de la performance du système. Analyse chaud peut également servir pour aider à diagnostiquer des problèmes de santé. Un événement de santé est généralement traité en procédant à une analyse chaude et peut déclencher une alerte immédiatement. Un opérateur doit être capable de percer les raisons pour l'événement en examinant les données de la voie chaude ; ces données doivent contenir des informations sur les événements qui ont mené à la question qui a provoqué l'événement d'État.

Certains types de surveillance génèrent des données à plus long terme, et l'analyse peut être effectuée à une date ultérieure, éventuellement selon un calendrier prédéfini. Dans certains cas, l'analyse devrez peut-être effectuer un filtrage complexe de gros volumes de données capturées sur une période de temps. Ceci est connu comme _analyse froide_. La condition essentielle est que les données sont stockées en toute sécurité une fois qu'il a été capturé. Par exemple utilisation de surveillance et de vérification nécessitent une image fidèle de l'état du système à points réguliers dans le temps, mais ces informations d'État n'ont pas à être disponible pour le traitement immédiatement, elles ont été recueillies.  Analyse froide peut également être utilisé pour fournir des données pour l'analyse prédictive de la santé. Les données historiques recueillies sur une période déterminée de temps peuvent être utilisées en conjonction avec les données actuelles de la santé (document provient le chemin réactif) pour repérer les tendances qui peuvent causer rapidement des problèmes de santé. Dans ces cas, il peut être nécessaire déclencher une alerte pour permettre des mesures correctives à prendre.

### Corrélation des données
The data captured by instrumentation can provide a snapshot of the system state, but the purpose of analysis is to make this data actionable. For example, what has caused an intense I/O loading at the system level at a specific time? Is it the result of a large number of database operations? Is this reflected in the database response times, the number of transactions per second, and application response times at the same juncture? If so, then one remedial action that may reduce the load could be to shard the data over more servers. In addition, exceptions can arise as a result of a fault in any level of the system, and an exception in one level often triggers another fault in the level above. For these reasons you need to be able to correlate the different types of monitoring data at each level to produce an overall view of the state of the system and the applications that are executing on it. You can then use this information to make decisions about whether the system is functioning acceptably or not, and determine what can be done to improve the quality of the system.

Comme décrit dans la section [Informations de corrélation des données](#information-for-correlating-data), vous devez vous assurer que les données d'instrumentation brute comprennent suffisamment de renseignements ID contexte et activité pour prendre en charge les agrégations requises de corrélation des événements. En outre, ces données pourraient se tiendra dans différents formats, et il peut être nécessaire d'analyser cette information pour le convertir dans un format normalisé aux fins d'analyse.

### Dépannage et diagnostic des problèmes de
Diagnostic nécessite de pouvoir déterminer la cause de défauts ou un comportement inattendu, y compris l'analyse de cause racine. L'information requise en général comprend :

- Informations détaillées de journaux des événements et des traces pour l'ensemble du système ou un sous-système spécifié pendant une fenêtre de temps spécifiée.
- Toutes les traces de la pile résultant des exceptions et des erreurs de n'importe quel niveau spécifié qui se produisent au sein du système ou un sous-système spécifié pendant une période déterminée de temps.
- Crash dumps pour n'importe quel processus a échoué ou n'importe où dans le système pour un sous-système spécifié pendant une fenêtre de temps spécifiée.
- L'activité enregistre les opérations étant effectuées par tous les utilisateurs ou pour les utilisateurs sélectionnés pendant une période déterminée de temps d'enregistrement.

Analyse des données pour des fins de dépannage souvent nécessite une connaissance technique approfondie de l'architecture du système et les divers éléments qui composent la solution. En conséquence, elle nécessite souvent un grand degré d'intervention manuelle pour interpréter les données, d'établir la cause des problèmes et recommander une stratégie appropriée pour y remédier. Il peut être approprié il suffit de stocker une copie de cette information dans son format original et la rendre disponible pour l'analyse froide par un expert.

## Visualisation des données et élever des alertes
Un aspect important de tout système de surveillance est la possibilité de présenter les données de telle façon qu'un opérateur peut rapidement repérer des tendances ou des problèmes, et pour informer rapidement un opérateur si un événement important s'est produit qui peut-être nécessiter une attention.

Présentation des données peut prendre plusieurs formes, y compris la visualisation à l'aide de tableaux de bord, alertes et rapports.

### Visualisation avec tableaux de bord
The most common way to visualize data is to use dashboards that can display information as a series of charts, graphs, or some other pictorial manner. These items could be parameterized, and an analyst should be able to select the important parameters (such as the time period) for any specific situation. Dashboards can be organized hierarchically, with top-level dashboards giving an overall view of each aspect of the system but with the facility to enable an operator to drill down to the details. For example, a dashboard that depicts the overall disk I/O for the system should allow an analyst to dig into the data and view the I/O rates for each individual disk to ascertain whether one or more specific devices account for a disproportionate volume of traffic. Ideally the dashboard should also display related information, such as the source of each request (the user or activity) that is generating this I/O. This information could then be used to determine whether (and how) to spread the load more evenly across devices, and whether the system would perform better if more devices were added. A dashboard might also be able to use color-coding or some other visual cues to indicate values that appear anomalous or that are outside an expected range. Using the previous example, a disk with an I/O rate approaching its maximum capacity over an extended period (a hot disk) could be highlighted in red, a disk with an I/O rate that periodically runs at its maximum limit over short periods (a warm disk) could be highlighted in yellow, and a disk exhibiting normal usage could be displayed in green.

Notez que pour un système de tableau de bord travailler efficacement, il doit avoir les données brutes avec qui travailler. Si vous construisez votre propre système de tableau de bord, ou utilisant un tableau de bord mis au point par un autre organisme, vous devez comprendre quelles données d'instrumentation vous devez collecter, à quel niveau de granularité, et comment il doit être formaté pour la consommation par le tableau de bord.

Un bon tableau de bord n'affiche pas seulement les informations, il fournit un moyen pour permettre à un analyste de poser des questions ad hoc sur cette information. Certains systèmes fournissent des outils de gestion qu'un opérateur peut utiliser pour effectuer ces tâches et d'explorer les données sous-jacentes. Par ailleurs, selon le référentiel utilisé pour contenir ces informations, il serait possible d'interroger ces données directement, ou l'importer dans des outils tels que Microsoft Excel pour une analyse plus approfondie et les rapports.

> [AZURE. REMARQUE] Vous devez restreindre l'accès aux tableaux de bord au personnel autorisé ; Cette information peut être commercialement sensible. Vous devez également protéger les données sous-jacentes présentées par le tableau de bord pour empêcher les utilisateurs de changer.

### Élever des alertes
Alerter est le processus d'analyse des données de surveillance et l'instrumentation et de générer une notification si un événement important est détecté.

Alerte sert à s'assurer que le système reste en bonne santé, sensible et sûr. C'est une partie importante de tout système qui rend les performances, la disponibilité et des garanties de confidentialité aux utilisateurs, et les données doivent éventuellement être traité immédiatement. Un opérateur doive être avisé de l'événement qui a déclenché l'alerte. Alerte peut également servir pour appeler des fonctions système telles qu'autoscaling.

Alerte généralement varie selon les données d'instrumentation suivants :

- Événements de sécurité. Si les journaux d'événements indiquent que répétée d'authentification et/ou échecs d'autorisation sont produisent, le système peut être attaqué et un opérateur doit être informé.
- Indicateurs de performance. Le système doit réagir rapidement si une mesure de performance particulière dépasse un seuil déterminé.
- Informations de disponibilité. Si un défaut est détecté, qu'il peut être nécessaire de redémarrer rapidement un ou plusieurs sous-systèmes ou basculement vers une ressource de secours. Des pannes répétées dans un sous-système peuvent indiquer plus vives inquiétudes.

Les opérateurs peuvent recevoir des informations d'alerte à l'aide de nombreux canaux de distribution tels que le courrier électronique, un dispositif de téléavertisseur ou un message texte SMS. Une alerte peut également inclure une indication de l'importance de la situation est apparu. Beaucoup de systèmes d'alerte abonné groupes d'entraide, et tous les opérateurs qui sont membres du même groupe peuvent être envoyées par le même ensemble d'alertes.

An alerting system should be customizable and the appropriate values from the underlying instrumentation data could be provided as parameters. This approach enables an operator to filter data and focus on those thresholds or combinations of values which are of interest. Note that in some cases, the raw instrumentation data could be provided to the alerting system, while in other situations it might be more appropriate to supply aggregated data (for example, an alert could be triggered if the CPU utilization for a node exceeded 90% over the last 10 minutes). The details provided to the alerting system should also include any appropriate summary and context information; this data can help to reduce the possibility of false-positive events tripping an alert.

### Établissement de rapports
Signalement est utilisé pour générer une vue d'ensemble du système et peut comporter des données historiques ainsi que les informations actuelles. Déclaration exigences elles-mêmes entrent dans deux grandes catégories : reporting opérationnel et des rapports de sécurité.

Reporting opérationnel inclut généralement les aspects suivants :

- Agrégation des statistiques qui vous permettent de comprendre l'utilisation des ressources de l'ensemble du système ou des sous-systèmes spécifiés pendant une fenêtre de temps spécifiée.
- Identifier les tendances dans l'utilisation des ressources pour l'ensemble du système ou des sous-systèmes spécifiés pendant une période de temps donnée.
- Surveillance les exceptions qui ont eu lieu dans tout le système ou en spécifié sous-systèmes pendant une période de temps donnée.
- Détermination de l'efficacité de l'application en ce qui concerne les ressources déployées et comprendre si le volume des ressources (et leurs coûts associés) peuvent être réduites sans impact sur les performances inutilement.

Rapports de sécurité se préoccupe de suivi de l'utilisation du système par le client et peut inclure :

- Audit des opérations de l'utilisateur ; Cela nécessite des requêtes individuelles effectuées par chaque utilisateur ainsi que les dates et les heures d'enregistrement. Les données doivent être structurées afin de permettre à un administrateur de reconstituer rapidement la séquence d'opérations exécutées par un utilisateur spécifique sur une période donnée.
- Utilisation des ressources suivi par utilisateur ; Cela exige l'inscription comment chaque demande pour un accès utilisateur différentes ressources qui font partie du réseau et pour combien de temps. Un administrateur doit être en mesure d'utiliser ces données pour générer un rapport de l'utilisation par l'utilisateur sur une période spécifique, peut-être pour fins de facturation.

Dans de nombreux cas, les rapports peuvent être générés par les traitements par lots selon un calendrier défini (temps de latence n'est pas normalement un problème), mais ils devraient également être disponibles pour la production sur une base ad hoc si nécessaire.  Par exemple, si vous stockez des données dans une base de données relationnel telles que base de données de SQL Azure, vous pouvez utiliser un outil tel que SQL Server Reporting Services pour extraire et formater les données et présenter comme un ensemble de rapports.

## Lignes directrices et modèles liés
- L'orientation Autoscaling décrit comment diminuer la gestion frais généraux en réduisant la nécessité pour un opérateur de constamment surveiller les performances d'un système et prendre des décisions concernant l'ajout ou la suppression des ressources.
- Le [Modèle de suivi de point de terminaison santé](https://msdn.microsoft.com/library/dn589789.aspx) décrit comment implémenter des contrôles fonctionnels au sein d'une application des outils externes accessibles par l'intermédiaire de points de terminaison exposés à intervalles réguliers.
- Le [File d'attente prioritaire](https://msdn.microsoft.com/library/dn589794.aspx) modèle montre comment prioriser les messages en file d'attente afin que les demandes urgentes sont reçus et peuvent être traitées avant les messages moins urgents.

## Plus d'informations
- L'article [Surveiller, diagnostiquer et dépanner Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md) sur le site Web de Microsoft.
- L'article [Azure : Bases de télémétrie et de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) sur le site Web de Microsoft.
- La page [Collecter les données de journalisation à l'aide des Diagnostics d'Azur](https://msdn.microsoft.com/library/azure/gg433048.aspx) sur le site Web de Microsoft.
- La page [Configuration des Diagnostics pour les ordinateurs virtuels et de Cloud Azur Services](https://msdn.microsoft.com/library/azure/dn186185.aspx) sur le site Web de Microsoft.
- Le [Azure Redis Cache](http://azure.microsoft.com/services/cache/), [DocumentDB Azur](http://azure.microsoft.com/services/documentdb/), et [HDInsight](http://azure.microsoft.com/services/hdinsight/) pages sur le site Web de Microsoft.
- La page [L'utilisation de files d'attente de Service Bus](http://azure.microsoft.com/) sur le site Web de Microsoft.
- L'article [SQL Server Business Intelligence dans les Machines virtuelles d'Azur](https://msdn.microsoft.com/library/azure/jj992719.aspx) sur le site Web de Microsoft.
- La page [Compréhension, surveillance des alertes et des Notifications dans Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx) sur le site Web de Microsoft.
- Le [Aperçus de l'application](app-insights-get-started/) page sur le site Web de Microsoft.
