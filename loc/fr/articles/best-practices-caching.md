<properties
   pageTitle="Caching guidance | Microsoft Azure"
   description="Guidance on caching to improve performance and scalability."
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

# Conseils de mise en cache

![](media/best-practices-caching/pnp-logo.png)

La mise en cache est une technique courante qui vise à améliorer les performances et
évolutivité d'un système en copiant temporairement fréquemment consultée données
stockage rapide situé à proximité de l'application. Si ce stockage de données rapide
se trouve plus près à l'application que la source d'origine puis la mise en cache
peuvent améliorer considérablement les temps de réponse pour les applications clientes en servant
données plus rapidement. La mise en cache est plus efficace lorsqu'un client d'instance à plusieurs reprises
lit les mêmes données, en particulier si les données demeurent relativement stables et
le magasin de données originale est lent par rapport à la vitesse de la mémoire cache, est
sous réserve d'un niveau élevé de la prétention, ou est loin lorsque réseau latence
peut causer des accès d'être lent.

## Mise en cache dans les applications distribuées

Applications distribuées implémentent en général ou pour les deux la
stratégies suivantes lors de la mise en cache de données :

- En utilisant un cache privé, où les données sont conservées localement sur l'ordinateur qui exécute une instance d'une application ou un service.
- En utilisant un cache partagé, agissant comme une source commune, qui est accessible par plusieurs procédés et/ou machines.

Dans les deux cas, la mise en cache peut être exécutée côté client (en les fournissant des processus
l'interface utilisateur pour un système, telle qu'une application web de navigateur ou de bureau),
ou côté serveur (par le processus de fourniture des services d'affaires
en cours d'exécution à distance).

### La mise en cache privé

Le plus simple type de cache est un magasin en mémoire, tenu l'adresse
l'espace d'un processus unique et accessible directement par le code qui s'exécute
dans ce processus. Ce type de cache est très rapide d'accès, et il peut
fournir une stratégie extrêmement efficace pour stocker des quantités modestes de
les données statiques comme la taille d'un cache sont généralement limitées par la
volume de mémoire disponible sur l'ordinateur qui héberge le processus. Si vous
besoin de plus d'informations qu'est physiquement possible dans la mémoire, de cache
vous pouvez écrire les données en mémoire cache dans le système de fichiers local. Cette volonté
nécessairement être plus lent accéder aux données tenue en mémoire, mais devrait
toujours être plus rapide et plus fiable que la récupération des données sur un réseau.

Si vous avez plusieurs instances d'une application qui utilise ce modèle
en cours d'exécution simultanément, chaque instance d'application aura son propre
cache indépendant détenant sa propre copie des données.

Vous devriez penser d'un cache comme un instantané des données originales sur certains
point dans le passé. Si ces données ne sont pas statiques, il est probable que
instances d'application différents peut contenir différentes versions de la
données dans leurs caches. Par conséquent, la même requête effectuée par ces
instances peuvent retourner des résultats différents, comme illustré à la Figure 1.

![À l'aide d'un cache en mémoire dans différentes instances d'une application](media/best-practices-caching/Figure1.png)

_Figure 1: En utilisant un cache en mémoire dans différentes instances d'une application_

### Partagé la mise en cache

En utilisant un cache partagé peut aider à atténuer l'inquiétude que les données peuvent
diffèrent dans chaque cache, comme peut se produire avec la mise en cache en mémoire. Partagé
la mise en cache s'assure que les instances d'application différents voient même
affichage des données mises en cache en localisant le cache dans un emplacement distinct,
généralement organisée dans le cadre d'un service séparé, comme illustré à la Figure 2.

![En utilisant un cache partagé_](media/best-practices-caching/Figure2.png)

_Figure 2: En utilisant un cache partagé_

Un avantage important de l'approche de mise en cache partagée est la
évolutivité, il peut aider à fournir. Plusieurs services de cache partagé sont
implémentée à l'aide d'un cluster de serveurs et utiliser le logiciel qui
répartit les données sur le cluster de manière transparente. Un
instance d'application envoie simplement une demande pour le service de cache,
et l'infrastructure sous-jacente est responsable de la détermination de la
emplacement des données mises en cache dans le cluster. Vous pouvez facilement faire évoluer la
cache en ajoutant plus de serveurs.

Les inconvénients de la mise en cache partagé l'approche qui sont le cache
est plus lent d'accès car il est n'est plus maintenu localement à chaque
instance de l'application et l'obligation de mettre en œuvre un séparé
service cache peut-être ajouter à la solution de complexité.

## Considérations relatives à l'aide de la mise en cache

Les sections suivantes décrivent en détail les considérations
pour la conception et à l'aide d'un cache.

### Quand doivent données être mis en cache ?

La mise en cache peut considérablement améliorer les performances, l'évolutivité et disponibilité. Les données plus
que vous avez et plus le nombre d'utilisateurs qui doivent accéder à ces données, le plus élevées
les avantages de la mise en cache de devenir en réduisant la latence et contention associé de manutention
stockent de gros volumes de demandes simultanées dans les données originales. Par exemple, une base de données
peut prendre en charge un nombre limité de connexions simultanées, mais la récupération des données depuis un partage
cache plutôt qu'à la base de données sous-jacente permet à une application cliente pour accéder à ces données
même si le nombre de connexions disponibles est actuellement épuisé. En outre, si la
base de données devient indisponible, les applications clientes peuvent être en mesure de continuer à l'aide de la
les données stockées dans le cache.

Vous devez envisager la mise en cache de données qui sont lue fréquemment, mais qui est modifiées est rare
(les données ont une forte proportion des opérations de lecture par rapport à des opérations d'écriture). Toutefois,
vous ne devez pas utiliser le cache comme le magasin faisant autorité d'informations critiques ; vous devriez
s'assurer que toutes les modifications que votre application ne peut pas se permettre de perdre sont toujours enregistrées dans un
magasin de données persistantes. De cette façon, si le cache n'est pas disponible, votre application peut
toujours continuer à fonctionner à l'aide de la Banque de données et vous ne perdrez pas important
informations.

### Types de stratégies de population data et cache

La clé à l'aide d'un cache efficacement consiste à déterminer les données plus appropriées à
cache et il cache le moment venu. Les données peuvent être ajoutées au cache sur
demande la première fois, qu'il est récupéré par une application, afin que l'application doit
extraire les données en une seule fois les données de magasin et les accès suivants peuvent être satisfaits
en utilisant le cache.

Par ailleurs, un cache peut être partiellement ou entièrement rempli avec les données à l'avance,
en général lorsque l'application démarre (une approche dite de semis). Toutefois, il peut
ne pas être souhaitable de mettre en œuvre les semis pour un cache volumineux que cette approche peut imposer
une charge soudaine, élevée sur le magasin de données original lorsque l'application démarre en cours d'exécution.

Souvent, une analyse des modèles d'utilisation peut aider à décider s'il faut entièrement ou partiellement
préremplir un cache et de choisir les données qui doivent être mises en cache. Par exemple, il
serait probablement utile pour amorcer le cache avec les données de profil utilisateur statique pour
les clients qui utilisent l'application régulièrement (peut-être tous les jours), mais pas pour
clients qui utilisent l'application seulement une fois par semaine.

La mise en cache en général fonctionne bien avec les données immuable ou que des changements
que rarement. Exemples incluent des informations de référence tels que des produits et prix
informations dans une application de commerce électronique ou des ressources statiques partagées qui coûtent cher
pour construire. Tout ou partie de ces données peuvent être chargés dans le cache d'application
démarrage pour réduire la demande en ressources et pour améliorer les performances. Il peut également être
approprié d'avoir un processus d'arrière-plan que périodiquement mises à jour de données de référence
le cache pour s'assurer que c'est à jour, ou actualise le cache lorsque référence
modifications de données.

La mise en cache peut être moins utile pour dynamic data, bien qu'il existe certaines exceptions à la
Cet examen (voir la section mise en cache des données hautement dynamiques plus loin dans ce
conseils pour plus d'informations). Lorsque les données originales régulièrement changent, soit
les informations mises en cache peuvent devenir obsolètes très rapidement ou la charge du maintien de la
le cache synchronisé avec le magasin de données original réduit l'efficacité des
la mise en cache. Notez qu'un cache n'a pas d'inclure les données complètes pour une
entité. Par exemple, si un élément de données représente un objet à plusieurs valeurs, comme une banque
client avec nom, adresse et le solde du compte, certains de ces éléments peuvent
rester statique (le nom et l'adresse), tandis que d'autres (par exemple, le solde du compte)
peut être plus dynamique. Dans ces situations, il pourrait être utile pour mettre en cache statique
certaines parties des données et récupérer uniquement (ou calculer) les autres informations comme
et lorsque c'est nécessaire.

Analyse de performance stable et son utilisation doit être effectué pour déterminer si
Préremplissage des ou à la demande de chargement du cache, ou une combinaison des deux, est
appropriées. La décision doit être fondée sur une combinaison de la volatilité et
modèle d'utilisation des données. Analyse utilisation et les performances de cache est
particulièrement important dans les applications qui rencontrent des charges lourdes et doivent être
hautement évolutive. Par exemple, dans les scénarios hautement évolutives, il peut être judicieux de
les semences du cache afin de réduire la charge sur le magasin de données aux heures de pointe.

Mise en cache peut également être utilisé pour éviter de refaire les calculs car l'application est
en cours d'exécution. Si une opération transforme les données ou effectue un calcul compliqué,
Il peut enregistrer les résultats de l'opération dans le cache. Si le même calcul
Il faut par la suite, que l'application peut extraire simplement les résultats de
le cache.

Une application peut modifier les données détenues dans une cache, mais vous devriez considérer la
cache comme un magasin de données transitoires qui pourrait disparaître à tout moment. Ne pas ranger
des données valables uniquement dans le cache, mais assurez-vous que vous conservez les informations
dans le magasin original données aussi bien. De cette façon, si le cache doit devenir
pas disponible, vous minimisez les risques de perte de données.

### La mise en cache de données hautement dynamiques

Stockage de l'information qui peuvent imposer des modifications rapidement dans un magasin de données persistantes
une surcharge sur le système. Par exemple, considérez un dispositif qui continuellement des rapports
l'État ou quelque autre mesure. Si une application choisit de ne pas mettre en cache ce
données au motif que les informations mises en cache sera presque toujours dépassées, puis
la même considération ne pouvait être vraie lors du stockage et de récupération de ces informations
depuis le magasin de données ; le temps nécessaire pour enregistrer et extraire ces données elle peut avoir
a changé. Dans une telle situation, examiner les avantages de stocker la dynamique
informations directement dans le cache au lieu du magasin de données persistantes. Si le
données sont non critique et ne nécessitent pas d'être vérifiés, alors il n'importe pas
Si le changement occasionnel est perdu.

### Gestion des données d'expiration dans un cache

Dans la plupart des cas, qui s'est tenues dans un cache de données sont une copie de la donnée contenue dans les données d'origine
magasin. Les données dans le magasin de données initial peuvent changer après qu'il a été mis en cache, provoquant
les données mises en cache à devenir obsolètes. Mise en cache de nombreux systèmes vous permettent de configurer le
cache d'expiration des données et de réduire la période pour laquelle les données peuvent être obsolètes.

Lorsque les données mises en cache expire il est supprimé du cache, et l'application doit
récupérer les données de la Banque de données originales (on peut mettre le nouvellement récupérés
informations en cache). Vous pouvez définir une stratégie d'expiration par défaut lorsque vous
configurer le cache. Dans de nombreux services de cache, vous pouvez également stipuler l'expiration
période pour des objets individuels lorsque vous les stocker par programme dans le cache
(certains caches vous permettent de spécifier la période d'expiration une valeur absolue, ou
comme une valeur décalée, qui provoque l'élément soit supprimé du cache si elle n'est pas
consultée dans le délai imparti. Ce paramètre remplace n'importe quel cache à l'échelle
stratégie d'expiration, mais uniquement pour les objets spécifiés.

> [AZURE. REMARQUE] Tenir compte de la période d'expiration pour le cache et les objets qu'il contient avec soin. Si vous faites c'est trop court, objets expirera trop rapidement et vous permettra de réduire les avantages de l'utilisation du cache. Si vous faites la période trop longue, vous risquez des données deviennent obsolètes.

Il est également possible que le cache peut remplir si données sont autorisées à rester
résident depuis longtemps. Dans ce cas, toute demande d'ajouter de nouveaux éléments à la
cache peut entraîner certains éléments à enlever par la force, dans un processus connu sous le nom
éviction. Services de cache généralement expulser des données sur un moins récemment utilisé (LRU)
base, mais vous pouvez généralement substituer cette stratégie et empêcher des éléments de
expulsées. Toutefois, si vous adoptez cette approche, vous risquez votre cache excédant la
mémoire qu'il a de disponible et une application qui tente d'ajouter un élément
au cache échoue avec une exception.

Certaines implémentations de mise en cache peuvent fournir des politiques d'expulsion supplémentaires. Ces
comprennent généralement la politique plus récemment utilisés (dans l'espoir que la
données ne sera pas exigées à nouveau), first-in-first-out politique (des données plus anciennes sont
expulsé d'abord), ou la suppression explicite basé sur un événement déclenché (tels que le
données en cours de modification).

### Invalider les données dans un cache côté client

Données stockées dans un cache côté client sont généralement considérée comme être en dehors de
les auspices du service qui fournit les données au client ; un service
ne peut directement forcer un client d'ajouter ou de supprimer les informations d'un
cache côté client. Cela signifie qu'il est possible pour un client qui utilise
un cache mal configuré (par exemple, expiration politiques ne sont pas
correctement mis en œuvre) de continuer à utiliser les informations périmées mis en cache
localement, lorsque les informations contenues dans la source de données d'origine a changé.

Si vous générez une application web qui sert des données sur HTTP
connexion, vous pouvez forcer implicitement un client web (telle qu'un navigateur ou
proxy Web) pour extraire les informations les plus récentes, si une ressource est mise à jour
en changeant l'URI de la ressource. Les clients Web utilisent généralement l'URI
d'une ressource comme clé dans le cache côté client, donc changer l'URI
provoque le client web d'ignorer toute version précédemment mis en cache d'un
ressources et aller chercher la nouvelle version à la place.

## Gestion d'accès concurrentiel dans un cache

Les caches sont souvent conçus pour être partagé par plusieurs instances d'un
demande. Chaque instance de l'application peut lire et modifier des données dans
le cache. Par conséquent, les mêmes problèmes d'accès concurrentiel qui se posent avec
n'importe quel magasin de données partagées sont également applicables à un cache. Dans une situation
où une application doit modifier les données contenues dans le cache, vous pouvez
devons nous assurer que les mises à jour effectuées par une seule instance de l'application
n'écrasez pas aveuglément les modifications effectuées par une autre instance.

Selon la nature des données et la probabilité de collisions,
vous pouvez adopter l'une des deux méthodes d'accès concurrentiel :

- __Optimiste.__ L'application vérifie si les données dans le cache a changé car il a été récupéré, immédiatement avant la mise à jour. Si les données sont toujours les mêmes, le changement est possible. Dans le cas contraire, l'application doit décider s'il faut mettre à jour (la logique métier qui anime cette décision sera spécifique à l'application). Cette approche est adapté aux situations où les mises à jour sont peu fréquentes, ou où les collisions sont peu probables.
- __Pessimiste.__ L'application verrouille les données dans le cache quand il récupère à empêcher une autre instance de modifier les données. Ce processus garantit que les collisions ne peuvent se produire, mais pourraient bloquer les autres instances qui doivent traiter les mêmes données. Accès concurrentiel pessimiste peut influer sur l'évolutivité de la solution et doit être utilisé uniquement pour les opérations de courte durées. Cette approche peut convenir pour les situations où les collisions sont plus probables, surtout si une application met à jour plusieurs éléments dans le cache et veille à ce que ces modifications sont appliquées de manière cohérente.

### Mise en œuvre de l'évolutivité et haute disponibilité et amélioration des performances

Un cache ne doit pas être le principal référentiel des données ; C'est le rôle
du magasin de données original dont le cache est rempli. Le
magasin de données d'origine est chargée d'assurer la persistance de la
données.

Veillez à ne pas introduire des dépendances critiques sur la disponibilité
d'un service de cache partagé dans vos solutions. Une demande doit être
en mesure de continuer à fonctionner si le service de cache partagé
n'est pas disponible ; l'application ne devrait pas pendre ou échouer en attendant
pour le service de cache de reprendre. Par conséquent, l'application doit être
préparé pour détecter la disponibilité du service de la cache et se replier
les données d'origine stocker si le cache est inaccessible. Le
[Modèle de disjoncteur](http://msdn.microsoft.com/library/dn589784.aspx) est utile pour la gestion de ce scénario. Le
service fournissant un cache peut être récupéré et une fois il devient
disponible le cache peut être repeuplé car les données sont lues forme la
magasin de données original, suivant une stratégie telles que la [Modèle cache-part](http://msdn.microsoft.com/library/dn589799.aspx).

Cependant, retomber dans le magasin de données original si le cache est
temporairement indisponible peut avoir un impact de l'évolutivité du système ;
alors que le magasin de données est être récupéré du magasin de données original
pourrait être inondé de demandes de données résultant de délais d'attente et
connexions échouées. Une stratégie que vous devriez considérer est de
mettre en place un cache local privé dans chaque instance d'une application
ainsi que les communes qui en cache toutes les instances de l'application
accès. Lorsque l'application récupère un élément, il peut tout d'abord vérifier
dans son cache local, puis le cache partagé et enfin l'original
magasin de données. Le cache local peut être rempli avec les données de la
cache partagé, ou la base de données si le cache partagé n'est pas disponible.
Cette approche nécessite une configuration minutieuse afin d'éviter la section locale
cache devenant trop vicié en ce qui concerne le cache partagé, mais il
agit comme un tampon si le cache partagé est inaccessible. Figure 3
montre cette structure.

![À l'aide d'un cache local en privé avec un cache partagé_](media/best-practices-caching/Caching3.png)
_Figure 3: À l'aide d'un cache local en privé avec un cache partagé_

À l'appui de grandes caches qui contiennent des données relativement longue durée de vie, certains
cache services offrent une option de haute disponibilité qui implémente
basculement automatique si le cache n'est plus disponible. Cette approche
implique généralement de répliquer les données mises en cache stockées sur une primaire
serveur de cache pour un serveur de cache secondaire et passer à la
connectivité ou serveur secondaire si la défaillance du serveur principal est
perdu. Pour réduire la latence associée d'écriture multiple
destinations, lorsque les données sont écrites dans la mémoire cache sur le primaire
Server, la réplication vers le serveur secondaire peut se produire
de façon asynchrone.  Cette approche mène à la possibilité que certains
informations mises en cache peuvent être perdues en cas de panne, mais la
proportion de ces données doit être petite par rapport à l'ensemble
taille du cache.

Si un cache partagé est grand, il peut être bénéfique à la partition du
données mises en cache dans l'ensemble de nœuds pour réduire les risques de contention et
améliorer l'évolutivité. Plusieurs caches partagés soutiennent la capacité de
Ajouter dynamiquement (et supprimer) les nœuds et rééquilibrer les données dans l'ensemble
partitions. Cette approche peut impliquer de clustering par lequel le
collection de nœuds est présentée aux applications clientes comme un
cache transparente, simple, mais en interne, les données sont dispersées
entre les nœuds suivant certains prédéfinis stratégie de distribution
qui équilibre la charge uniformément. Le [Document d'orientation de partitionnement de données](http://msdn.microsoft.com/library/dn589795.aspx)
sur Microsoft site Web fournit plus d'informations possible
stratégies de partitionnement.

Mise en cluster peut également ajouter d'autres disponibilité du cache ; Si un
nœud échoue, le reste de la mémoire cache est toujours accessible.
Mise en cluster est fréquemment utilisé en conjonction avec la réplication
et le basculement ; chaque nœud peut être reproduit et la réplique
rapidement mis en ligne si le nœud échoue.

Beaucoup lire et écrire des opérations comprendront vraisemblablement de données unique
valeurs ou objets. Toutefois, il peut y avoir des moments où il est
nécessaire pour stocker ou récupérer des gros volumes de données rapidement.
Par exemple, ensemencement d'un cache pourrait impliquer des centaines d'écriture ou
des milliers d'articles pour le cache, ou une demande doive
extraire le cache comme un grand nombre d'éléments connexes
partie de la même demande. Fournissent de nombreuses caches à grande échelle lot
opérations à ces fins, ce qui permet à une application cliente
empaqueter un volume important d'éléments dans une seule demande et
réduire les frais généraux associé à effectuer un grand nombre
de petites requêtes.

## Mise en cache et la cohérence

Le modèle de Cache-part dépend de l'instance de l'application
remplissage du cache ayant accès à la plus récente et
version cohérente des données. Dans un système qui implémente
cohérence éventuelle (comme un magasin de données répliquées) ce qui peut
n'est ne pas le cas. Une instance d'une application susceptible de modifier un
données de point et invalident la version mise en cache de l'élément. Un autre
instance de l'application peut-être tenter de lire cet article de
cache, ce qui provoque une cache-miss, alors qu'elle lit les données de la
données stockent et l'ajoute au cache. Toutefois, si le magasin de données
n'a pas été entièrement synchronisé avec les autres réplicas du
instance de l'application peut lire et remplir le cache avec le
ancienne valeur.

Pour plus d'informations sur la gestion de cohérence des données, consultez la
Page conseils de cohérence de données sur le site Web de Microsoft.

### Protection des données en mémoire cache

Quel que soit le service de cache que vous utilisez, vous devez envisager
Comment protéger les données conservées dans le cache de non autorisé
accès. Il existe deux principales préoccupations :

- La confidentialité des données dans le cache.
- La confidentialité des données qu'elle coule entre le cache et le
  application à l'aide de la mémoire cache.

Pour protéger les données dans le cache, le service de cache peut mettre en œuvre
mécanisme d'authentification qui exigeraient que les applications
s'identifient, et une autorisation guichet qui
indique quelles identités peuvent accéder aux données dans le cache, et
les opérations (lecture et écriture) que celles de ces identités
autorisé à effectuer. Pour réduire les frais généraux liés à
lire et écrire des données, une fois une identité a été accordée
écriture et/ou l'accès en lecture dans le cache, ce qui permet à l'identité
toutes les données dans le cache. Si vous devez restreindre l'accès à
sous-ensembles des données mises en cache, vous pouvez :

- Diviser le cache en partitions (à l'aide de cache différent
  les serveurs) et seulement accorder l'accès aux identités de la
  les partitions qu'ils devraient être autorisés à utiliser, ou
- Crypter les données dans chaque sous-ensemble en utilisant des clés différentes
  et seulement fournir les clés de cryptage aux identités qui
  devraient avoir accès à chaque sous-ensemble. Une application cliente
  peut encore être en mesure de récupérer toutes les données dans le cache,
  mais il ne sera en mesure de décrypter les données pour lesquelles il
  a les clés.

Pour protéger les données qu'elle coule dans et hors de la mémoire cache vous
reposent sur les fonctionnalités de sécurité fournies par le réseau
les infrastructures que les applications clientes utilisent pour se connecter à la
cache. Si le cache est implémenté à l'aide d'un serveur sur place
au sein de la même organisation qui héberge les applications clientes,
puis l'isolement du réseau lui-même ne peut pas exiger de vous
prendre des mesures supplémentaires. Si le cache est situé à distance et
nécessite une connexion TCP ou HTTP via un réseau public (par exemple
comme l'Internet), vous devez envisager d'implémenter SSL.

## Considérations relatives à la mise en œuvre de la mise en cache avec Microsoft Azure

Azure offre Azure Redis Cache. Il s'agit d'une mise en œuvre
de l'open source Cache Redis qui fonctionne comme un service dans un
Azur Centre de données. Il offre un service de mise en cache qui peut être
accessible à partir de n'importe quelle application Azure, si l'application
est implémenté comme un service de Cloud Computing, un site Web ou à l'intérieur un
Machine virtuelle d'Azur. Caches peuvent être partagés par client
applications qui possèdent la clé d'accès appropriés.

Redis est une solution de mise en cache de haute performance qui fournit
disponibilité, évolutivité et sécurité. Il s'exécute en général
en tant que service réparties sur une ou plusieurs machines dédiées et
tente de stocker autant d'informations que possible dans la mémoire
assurer un accès rapide. Cette architecture est destinée à fournir
une latence faible et un débit élevé en réduisant la nécessité de
effectuer les opérations d'e/s lentes.

Le cache d'Azur Redis est compatible avec bon nombre des différentes
API utilisées par les applications clientes. Si vous avez existante
les applications qui utilisent déjà Redis en cours d'exécution sur site, la
Azur Redis cache fournit un chemin de migration rapide vers la mise en cache
dans le nuage.

> [AZURE. REMARQUE] Azure fournit également le Service de Cache géré. Ceci
  service est basé sur le moteur de Cache AppFabric Microsoft. Il
  vous permet de créer un cache distribué qui peut être partagé
  par des applications faiblement couplées. Le cache est hébergé sur
  serveurs haute performance dans un datacenter d'Azur.
  Toutefois, cette option n'est pas recommandée et est seulement
  prévus pour prendre en charge les applications existantes qui ont été construites
  pour l'utiliser. Pour tous les nouveaux développements, utiliser le Redis Azure
  Mettre en cache au lieu de cela.
>
> En outre, Azure prend en charge la mise en cache dans le rôle. Cette fonctionnalité
  vous permet de créer un cache spécifique à un service de Cloud Computing.
  Le cache est hébergé par les instances d'un rôle web ou ouvrier, et
  sont accessibles uniquement par les rôles de fonctionnement dans le cadre de la même
  (une unité de déploiement est l'ensemble de l'unité de déploiement des services nuage
  des instances de rôle déployés comme un service de cloud pour un particulier
  région). Le cache est en clusters et toutes les instances de la
  rôle au sein de la même unité de déploiement qui hébergent le cache
  font partie du même cluster de cache. Applications existantes
  que dans le rôle d'utilisation mise en cache peut continuer à le faire, mais
  migration vers Azure Redis Cache peut apporter plus d'avantages.
  Pour plus d'informations sur l'opportunité d'utiliser Azure Redis Cache
  ou un en-rôle en cache, consultez la page
  [Quelle offre Azure Cache est bon pour moi ?](http://msdn.microsoft.com/library/azure/dn766201.aspx) sur le site Web de Microsoft.


### Caractéristiques des Redis

Redis est plus qu'un serveur de cache simple ; Il fournit une mémoire distribuée
base de données avec un ensemble étendu de commande qui prend en charge plusieurs scénarios courants,
comme décrit dans la section Use-cases pour Redis mise en cache plus loin dans ce
document. La présente section résume certaines des principales caractéristiques qui Redis
fournit.

### Redis comme une base de données en mémoire

Redis prend en charge la lecture et l'écriture des opérations. Contrairement à nombreuses caches (qui devraient être considérés comme des magasins de données transitoires), écritures peuvent être protégées contre la défaillance du système soit en étant stocké dans périodiquement dans un fichier snapshot local ou dans un fichier journal append-only. Toutes les écritures sont asynchrones et ne bloquent pas les clients lire et écrire des données. Redis quand démarre, il lit les données dans le fichier journal ou de capture instantanée et l'utilise pour construire le cache en mémoire. Pour plus d'informations, consultez [Redis Persistence](http://redis.io/topics/persistence) sur le site Redis.

> [AZURE. REMARQUE] Redis ne garantit pas que toutes les écritures sont sauvegardées en cas
  d'une défaillance catastrophique, mais au pire vous devriez perdre seulement une quelques secondes
  valeur de données. N'oubliez pas qu'un cache n'est pas censé agir comme un
  source de données qui font autorité et c'est la responsabilité des applications
  l'utilisation du cache pour s'assurer que les données critiques est enregistré avec succès à un
  magasin de données approprié. Pour plus d'informations, voir le modèle de Cache-Aside.

#### Redis des types de données

Redis est un magasin de la clé-valeur, où les valeurs peuvent contenir des types simples ou des structures de données complexes comme les hachages, listes et définit. Il prend en charge un ensemble d'opérations atomiques sur ces types de données. Clés peuvent être permanents ou étiqueté avec un peu de temps pour vivre à quel point la clé et sa valeur correspondante sont automatiquement supprimés du cache. Pour plus d'informations sur redis clés et valeurs, visitez la page [Une Introduction aux types de données Redis et abstractions](http://redis.io/topics/data-types-intro) sur le site Redis.

#### Redis réplication et clustering

Redis prend en charge la réplication maître/subordonné pour aider à assurer la disponibilité et de maintenir un débit ; les opérations d'écriture vers un nœud maître Redis sont répliquées vers un ou plusieurs nœuds subordonnés et les opérations de lecture peuvent être servi par le maître ou l'un des subordonnés. Dans le cas d'un partage réseau, subordonnés peuvent continuer à servir des données et puis transparente resynchroniser avec le maître, lorsque la connexion est rétablie. Pour plus de détails, visitez le [Réplication](http://redis.io/topics/replication) page sur le site Redis.

Redis prévoit également la mise en cluster, vous permettant de partitionner les données des éclats sur des serveurs et de répartir la charge de manière transparente. Cette fonctionnalité améliore l'évolutivité car nouveau Redis serveurs peuvent être ajoutés et les données repartitionnées comme la taille de la mémoire cache augmente. En outre, chaque serveur du cluster peut être répliquée à l'aide de la réplication maître/subordonné pour assurer la disponibilité sur chaque nœud du cluster. Pour plus d'informations sur le clustering et sharding, visitez le [Redis page tutoriel Cluster](http://redis.io/topics/cluster-tutorial) sur le site Redis.

> [AZURE. REMARQUE] Azur Cache Redis ne soutient pas actuellement de clustering. Si vous souhaitez créer un cluster Redis vous pouvez construire votre propre serveur Redis personnalisé. Pour plus d'informations, consultez la section construction Custom Redis Cache plus loin dans ce document.

### Redis utilisation mémoire

Un cache Redis a une taille finie en fonction des ressources disponibles sur l'ordinateur hôte. Lorsque vous configurez un serveur Redis, vous pouvez spécifier la quantité maximale de mémoire, qu'il peut utiliser. Une clé dans une cache Redis peut être configurée avec une heure d'expiration, après quoi il est automatiquement supprimé du cache. Cette fonctionnalité peut empêcher le cache en mémoire d'être rempli de données anciennes ou obsolètes.

Comme la mémoire se remplit, Redis peut d'expulser automatiquement en suivant un certain nombre de politiques clés et leurs valeurs. La valeur par défaut est LRU (moins récemment utilisé), mais vous pouvez également sélectionner autres politiques telles que l'expulsion des touches au hasard, ou la désactivation d'expulsion au total (auquel cas, tente d'ajouter des éléments au cache échouera si elle est pleine). La page [Utilisant le Redis comme un Cache LRU](http://redis.io/topics/lru-cache) fournit des informations supplémentaires.

### Redis des transactions et des lots

Redis enables a client application to submit a series of operations that read and write data in the cache as an atomic transaction. All of the commands in the transaction are guaranteed to be executed sequentially and no commands issued by other concurrent clients will be interwoven between them. However, these are not true transactions as a relational database would perform them. Transaction processing consists of two stages; command queuing and command execution. During the command queuing stage, the commands that comprise the transaction are submitted by the client. If some sort of error occurs at this point (such as a syntax error, or the wrong number of parameters) then Redis will refuse to process the entire transaction and discard it. During the execution phase, Redis performs each queued command in sequence. If a command fails during this phase Redis will continue with the next queued command and it does not roll back the effects of any commands that have already been executed. This simplified form of transaction helps to maintain performance and avoid performance problems caused by contention. Redis does implement a form of optimistic locking to assist in maintaining consistency. For detailed information about transactions and locking with Redis, visit the [Page des transactions](http://redis.io/topics/transactions) sur le site Redis.

Redis prend également en charge le traitement par lots non transactionnelle des demandes. Le protocole Redis que les clients utilisent pour envoyer des commandes à un serveur Redis permet à un client d'envoyer une série d'opérations dans le cadre de la même demande. Ceci peut aider à réduire la fragmentation des paquets sur le réseau. Lorsque le lot est traité, chaque commande est exécutée. Contrairement à une transaction, si une de ces commandes sont mal formée, ils seront rejetés mais les commandes restantes seront effectuées. Aussi, il n'y a aucune garantie sur l'ordre dans lequel les commandes dans le lot seront traitées.

### Redis sécurité

Redis se concentre uniquement sur fournissant un accès rapide aux données et est conçu pour s'exécuter dans un environnement fiable et accessible uniquement par les clients de confiance. Redis ne prend en charge un modèle de sécurité limitée basé sur l'authentification de mot de passe (il est possible de supprimer l'authentification complètement, bien que ce n'est pas recommandé). Tous les clients authentifiés partagent le même mot de passe global et ont accès aux mêmes ressources. Si vous avez besoin de sécurité de connexion plus complète, vous devez implémenter votre propre couche de sécurité devant le Redis server et toutes les demandes clientes ne doivent traverser cette couche supplémentaire ; Redis ne devrait pas être exposé directement aux clients non fiables ou non authentifiés.

Vous pouvez restreindre l'accès aux commandes en désactivant leur ou renommant (et seulement fournir à des clients privilégiés avec les nouveaux noms).

Redis ne supporte pas directement toute forme de cryptage des données pour l'ensemble des codages doivent être effectuées par les applications clientes. En outre, Redis ne fournit pas de toute forme de transport security, donc si vous avez besoin protéger les données qui circulent sur le réseau, vous devez implémenter un proxy SSL.

Pour plus d'informations, visitez le [Redis sécurité](http://redis.io/topics/security) page sur le site Redis.

> [AZURE. REMARQUE] Azur Redis Cache fournit sa propre couche de sécurité grâce auquel les clients se connecter ; le Redis sous-jacent
  les serveurs ne sont pas exposés au réseau public.

### L'utilisation du cache d'Azur Redis

Azure Redis Cache permet d'accéder à Redis serveurs exécutant sur des serveurs hébergés dans un centre de données d'Azur ; Il agit comme une façade qui fournit un contrôle d'accès et de sécurité. Vous pouvez approvisionner un cache en utilisant le portail Azure Management. Le portail fournit un certain nombre de configurations prédéfinies, allant d'un cache de 53GB fonctionnant comme un service qui prend en charge les communications SSL (pour la vie privée) et maître/esclave réplication avec un SLA de disponibilité de 99,9 %, vers le bas pour un 250 MB cache sans réplication (aucune garantie de disponibilité) en cours d'exécution sur un matériel partagé.

En utilisant le portail Azure Management vous pouvez également configurer la stratégie d'éviction du cache et contrôler l'accès au cache en ajoutant des utilisateurs aux rôles fournies ; Propriétaire, collaborateur, lecteur. Ces rôles définissent les opérations que les membres peuvent effectuer. Par exemple, les membres du rôle propriétaire ont un contrôle complet sur le cache (y compris la sécurité) et de son contenu, membres du rôle de contributeur peuvent lire et écrire des informations dans le cache, et les membres du rôle lecteur ne peuvent récupérer des données depuis le cache.

La plupart des tâches administratives sont effectuées via le portail de gestion d'Azur, et pour cette raison que la plupart des commandes disponibles dans la version standard de Redis administratives ne sont pas disponibles, y compris la possibilité de modifier la configuration par programme, arrêter le serveur Redis, configurer des esclaves supplémentaires, ou force enregistrement des données sur le disque.

The Azure management portal includes a convenient graphical display that enables you to monitor the performance of the cache. For example, you can view the number of connections being made, the number of requests performed, the volume of reads and writes, and the number of cache hits versus cache misses. Using this information you can determine the effectiveness of the cache and if necessary switch to a different configuration or change the eviction policy. Additionally, you can create alerts that send email messages to an administrator if one or more critical metrics fall outside of an expected range. For example, if the number of cache misses exceeds a specified value in the last hour, an administrator could be alerted as the cache may be too small or data may be being evicted too quickly.

Vous pouvez également surveiller les CPU, la mémoire et l'utilisation du réseau pour le cache.

> [AZURE. REMARQUE] Azur Redis Cache est censé agir uniquement comme un cache plutôt que d'une base de données. En conséquence, il n'implémente pas actuellement Redis persistance.

Pour plus amples informations et des exemples montrant comment créer et configurer un Azure Redis Cache, visitez la page [Tour de Cache Redis Azur](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog d'Azur.

## L'état de session mise en cache et une sortie HTML

Si vous construire ASP.NET web applications qu'exécuter à l'aide de rôles Azure web, vous pouvez enregistrer informations d'état de session et affichage HTML dans un Cache de Redis Azure. Le fournisseur d'état de Session pour Azure Redis Cache vous permet de partager des informations de session entre les différentes instances d'application web ASP.NET et est très utile en cas de batterie de serveurs web où n'existe pas d'affinité du client-serveur et la mise en cache de session données en mémoire ne serait pas approprié.

En utilisant le fournisseur d'état de Session avec Azure Redis Cache offre plusieurs avantages, notamment :

- Il peut partager l'état de session parmi un grand nombre d'instances d'une application web ASP.NET, offrant une évolutivité améliorée,
- Il prend en charge les accès simultané, contrôlé pour les mêmes données d'état de session pour plusieurs lecteurs et un seul rédacteur, et
- Il peut utiliser la compression pour économiser de la mémoire et améliorer les performances du réseau.

Pour plus d'informations, visitez le [Fournisseur d'état de Session ASP.NET pour Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn690522.aspx) page sur le site Web de Microsoft.

> [AZURE. REMARQUE] Ne pas utiliser le fournisseur d'état de Session pour Azure Redis Cache pour les applications ASP.NET qui s'exécutent à l'extérieur de l'environnement Azure. La latence d'accès à la cache à partir en dehors de l'Azure permet d'éliminer les avantages de la mise en cache de données.

De même, le fournisseur de Cache de sortie pour Azure Redis Cache vous permet d'enregistrer les réponses HTTP générés par une application de web ASP.NET. En utilisant le fournisseur de Cache de sortie avec Azure Redis Cache peut améliorer les temps de réponse des applications qui rendent une sortie HTML complexe ; les instances d'application générant des réponses similaires peuvent faire utilisent des fragments sortie partagés dans le cache, plutôt que de générer ce code de HTML de sortie nouveau.  Pour plus d'informations, visitez le [Fournisseur de Cache de sortie ASP.NET pour Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn798898.aspx) page sur le site Web de Microsoft.

## Construction d'un cache Redis personnalisée

The Azure Redis cache acts as a façade to the underlying Redis servers. Currently it supports a fixed set of configurations but does not provide for Redis clustering. If you require an advanced configuration that is not covered by the Azure Redis cache (such as a cache bigger than 53GB) you can build and host your own Redis servers by using Azure virtual machines. This is a potentially complex process as you may need to create several VMs to act as master and subordinate nodes if you want to implement replication. Furthermore, if you wish to create a cluster, then you will need multiple masters and subordinate servers; a minimal clustered, replication topology that provides a high degree of availability and scalability comprises at least 6 VMs organized as 3 pairs of master/subordinate servers (a cluster must contain at least 3 master nodes). Each master/subordinate pair should be located close together to minimize latency, but each set of pairs can be running in different Azure datacenters located in different regions if you wish to locate cached data close to the applications that are most likely to use it. The page [Redis en cours d'exécution sur un ordinateur virtuel au Linux CentOS dans Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) sur Microsoft site promenades à travers un exemple montrant comment créer et configurer un nœud Redis fonctionnant comme un VM Azure.

Notez que si vous implémentez votre propre cache Redis de cette façon, vous êtes responsable de la surveillance, la gestion et la sécurisation du service.

## Partitionnement d'un cache Redis

Cache de partitionnement consiste à diviser le cache sur plusieurs ordinateurs. Cette structure vous offre plusieurs avantages au fil à l'aide d'un serveur de cache unique, y compris :

- Création d'un cache qui est beaucoup plus grand que peut être stocké sur un serveur unique.
- Distribution de données entre serveurs, amélioration de la disponibilité. Si un serveur tombe en panne ou deviennent inaccessible, que les données qu'elle détient sont indisponibles ; les données sur les serveurs restants sont toujours accessibles. Pour un cache, c'est pas essentiel que les données mises en cache est uniquement une copie transitoire des données dans une base de données et des données mises en cache sur un serveur qui devient inaccessible peuvent être mis en cache au lieu de cela sur un autre serveur.
- Répartissant la charge sur les serveurs, ce qui améliore les performances et l'évolutivité.
- Géolocationnent données à proximité des utilisateurs qui accèdent, réduisant la latence.

Un cache, la forme la plus courante de partitionnement est sharding. Dans cette stratégie, chaque partition (ou éclat) est un cache de Redis à part entière. Données sont dirigées vers une partition spécifique en utilisant la logique sharding, qui peut utiliser une variété d'approches pour distribuer les données. Le [Sharding Pattern](http://msdn.microsoft.com/library/dn589797.aspx) fournit plus d'informations sur l'implémentation sharding.

Pour implémenter le partitionnement dans un cache Redis, vous pouvez adopter une des approches suivantes :

- _Requête côté serveur de routage._ Dans cette technique, une application cliente envoie une demande à tout le
  Redis serveurs qui composent le cache (probablement le serveur le plus proche). Chaque serveur Redis stocke
  métadonnées qui décrivent la partition qu'il détient et contient également des informations sur les
  les partitions sont situées sur d'autres serveurs. Le Redis server examine la demande du client et si elle
  peut être résolu localement il va effectuer l'opération demandée, sinon il transmettra le
  demander au serveur approprié. Ce modèle est implémenté par Redis clustering et est
  décrits plus en détail sur le [Redis le tutoriel de cluster](http://redis.io/topics/cluster-tutorial) page sur le site Redis. Redis clustering
  est transparente pour les applications clientes et Redis autres serveurs peuvent être ajoutés au cluster
  (et les données re-partitionnées) sans nécessiter que vous reconfigurer les clients.

  > [AZURE. IMPORTANT] Azur Cache Redis ne soutient pas actuellement Redis clustering. Si vous souhaitez
  mettre en œuvre cette approche, alors vous devez construire un cache Redis personnalisé, comme décrit précédemment.

- _Partitionnement de côté client._ Dans ce modèle, l'application cliente contient une logique (éventuellement en
  la forme d'une bibliothèque) que routes les demandes vers le serveur approprié de Redis. Cette approche
  peut être utilisé avec Azure Redis Cache ; créer plusieurs Caches Redis Azure (un pour chaque donnée
  partition) et implémentez le code côté client qui achemine les requêtes vers le bon
  cache. Si le schéma de partitionnement change (si les Caches de Redis Azure supplémentaires sont créés,
  applications clientes peuvent par exemple), doivent être reconfigurés.

- _Partitionnement assisté par le proxy._ Dans ce schéma, les applications clientes envoient des requêtes à un
  service de proxy intermédiaire qui comprend comment les données sont partitionnées, puis routes
  la demande vers le serveur approprié de Redis. Cette approche peut également être utilisée avec Azure
  Redis Cache ; le service proxy peut être implémenté comme un service de cloud Azure. Ceci
  approche nécessite un niveau supplémentaire de complexité à mettre en œuvre le service, et
  demandes peuvent prendre plus de temps à réaliser qu'avec un partitionnement côté client.

La page [Partitionnement : Comment diviser les données entre plusieurs instances de Redis](http://redis.io/topics/partitioning)
sur le Redis site Web fournit de plus amples informations sur l'implémentation du partitionnement avec Redis.

### Mise en œuvre d'applications clientes de Redis cache

Redis prend en charge les applications clientes écrites dans nombreux langages de programmation. Si vous créez des applications nouvelles en utilisant le .NET Framework, l'approche recommandée consiste à utiliser la bibliothèque cliente de StackExchange.Redis. Cette bibliothèque fournit un modèle objet .NET Framework qui fait abstraction des détails de connexion à un serveur Redis, envoi des commandes et la réception des réponses. Il est disponible dans Visual Studio comme un package NuGet. Vous pouvez utiliser cette même bibliothèque pour se connecter à un cache d'Azur Redis, ou un cache Redis personnalisé, hébergé sur une machine virtuelle.

Pour vous connecter à un serveur Redis, vous utilisez la méthode statique `Connect` méthode de la `ConnectionMultiplexer` classe. La connexion qui crée cette méthode est conçue pour être utilisée tout au long de la durée de vie de l'application cliente, et la même connexion peut être utilisée par plusieurs threads simultanés ; ne pas rebrancher et débrancher chaque fois que vous effectuez une opération de Redis que cela peut nuire aux performances. Vous pouvez spécifier les paramètres de connexion, telles que l'adresse de l'hôte Redis et le mot de passe. Si vous utilisez le cache Redis Azure, le mot de passe que c'est soit la clé primaire ou secondaire généré pour Azure Redis Cache en utilisant le portail Azure Management.

Après que vous être connecté au serveur Redis, vous pouvez obtenir un handle sur la base de données Redis qui sert de cache. La connexion Redis fournit le `GetDatabase` méthode pour ce faire. Vous pouvez ensuite récupérer les éléments du cache et stocker des données dans le cache à l'aide de la `StringGet` et `StringSet` Méthodes. Ces méthodes s'attendre à une clé comme paramètre, et soit renvoyer l'élément de cache qui a une contrepartie (valeur`StringGet`) ou ajouter l'élément au cache avec cette clé (`StringSet`).

Selon l'emplacement du serveur Redis, nombreuses opérations peuvent encourir une latence même si une demande est transmise au serveur et une réponse est renvoyée au client. La bibliothèque StackExchange fournit des versions asynchrones des nombreuses méthodes qu'il expose pour aider les applications clientes à rester réactive. Ces méthodes prennent en charge la [Task-based Asynchronous Pattern](http://msdn.microsoft.com/library/hh873175.aspx) dans le .NET Framework.

L'extrait de code suivant montre une méthode nommée `RetrieveItem` Cela illustre un exemple d'une implémentation du modèle cache-part basé sur Redis et la bibliothèque StackExchange. La méthode accepte une valeur de clé de chaîne et tente de récupérer l'élément correspondant dans le cache Redis en appelant le `StringGetAsync` méthode (la version asynchrone de `StringGet`). Si l'élément est introuvable, elle est lue dans la source de données sous-jacente à la `GetItemFromDataSourceAsync` méthode (qui est une méthode locale et ne fait pas partie de la bibliothèque StackExchange), puis ajouté au cache à l'aide de la `StringSetAsync` méthode, alors il peut être trouvé plus rapidement la prochaine fois.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Le `StringGet` et `StringSet` méthodes ne se limitent pas à extraire ou stocker des valeurs de chaîne ; ils peuvent prendre n'importe quel élément sérialisé comme un tableau d'octets. Si vous avez besoin enregistrer un objet .NET, vous pouvez sérialiser en tant que flux d'octets et utilisez la méthode StringSet pour écrire dans le cache. De même, vous pouvez lire un objet de cache à l'aide de la méthode StringGet et désérialiser un objet .NET. Le code suivant montre un ensemble de méthodes d'extension pour l'interface IDatabase (la méthode de GetDatabase d'une connexion Redis retourne un `IDatabase` objet) et un exemple de code qui utilise ces méthodes pour lire et écrire un BlogPost objet dans le cache :

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Le code suivant illustre une méthode nommée `RetrieveBlogPost` qui utilise ces méthodes d'extension pour lire et écrire un sérialisable `BlogPost` objet dans le cache suivant le modèle de cache-part :

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis supporte commande pipelining si une application cliente envoie plusieurs requêtes asynchrones. Redis possible de multiplexer les requêtes utilisant la même connexion plutôt que de recevoir et de répondre aux commandes dans un ordre strict. Cette approche permet de réduire la latence en faisant une utilisation plus efficace du réseau. L'extrait de code suivant montre un exemple qui récupère les détails de deux clients en même temps. Le code fait valoir les deux demandes et effectue ensuite un autre traitement (non illustré) avant d'attendre de recevoir les résultats. La méthode Wait de l'objet cache est similaire à la méthode .NET Framework Task.Wait :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La page [Développer pour Azur Redis Cache](http://msdn.microsoft.com/library/azure/dn690520.aspx) sur Microsoft site Web fournit plus d'informations sur la façon d'écrire des applications clientes peuvent utiliser le cache d'Azur Redis. Des renseignements supplémentaires sont disponibles sur le [Page de l'utilisation de base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sur le site de StackExchange.Redis et la page [Pipelines et multiplexeurs](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) sur le même site Web fournit plus d'informations sur les opérations asynchrones et pipelining Redis et la bibliothèque StackExchange.  La section Use-Cases pour le Redis cache plus loin dans ce guide donne des exemples de certaines des techniques plus avancées que vous pouvez appliquer aux données détenues dans une cache Redis.

## Cas d'utilisation pour la mise en cache des Redis

The simplest use of Redis for caching concerns storing key/value pairs where the value is an uninterpreted string of arbitrary length that can contain any binary data (it is essentially  an array of bytes that can be treated as a string). This scenario was illustrated in the section Implementing Redis Cache Client Applications earlier in this guidance. You should note that keys also contain uninterpreted data, so you can use any binary information as the key, although the longer the key is the more space it will take to store, and the longer it will take to perform lookup operations. For usability and ease of maintenance design your keyspace carefully and use meaningful (but not verbose) keys. For example, use structured keys such as "customer:100" to represent the key for the customer with ID 100 rather than simply "100". This scheme enables you to easily distinguish between values that store different data types. For example, you could also use the key "orders:100" to represent the key for the order with ID 100.

Mis à part les chaînes binaires unidimensionnels, une valeur dans une paire clé/valeur de Redis peut aussi tenir plus structurée, information, y compris des listes, établit (triés et non triés) et hache. Redis fournit un ensemble complet de commande qui peut de manipuler ces types, et beaucoup de ces commandes sont disponibles pour les applications .NET Framework grâce à une bibliothèque de client comme StackExchange. La page [Une introduction aux types de données Redis et abstractions](http://redis.io/topics/data-types-intro) sur le Redis site Web fournit un aperçu plus détaillé de ces types et les commandes que vous pouvez utiliser pour les manipuler.

Cette section récapitule quelques cas d'utilisation communes pour ces types de données et des commandes.

### Exécution atomique et opérations par lots

Redis soutient une série d'opérations atomiques de get et set sur des valeurs de chaîne. Ces opérations de supprimer les risques de course possibles qui pourraient se produire lors de l'utilisation séparée `GET` et `SET` commandes. Les opérations disponibles comprennent :

- `INCR`, `INCRBY`, `DECR`, et `DECRBY` qui effectuer atomique increment et decrement, opérations sur
  valeurs de données numériques entières. La bibliothèque StackExchange fournit des versions surchargées de la
  `IDatabase.StringIncrementAsync` et `IDatabase.StringDecrementAsync` méthodes pour effectuer
  ces opérations et le retour la valeur résultante est stockée dans le cache. Le code suivant
  extrait de code illustre l'utilisation de ces méthodes :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET` qui récupère la valeur associée à une clé et il prend une nouvelle valeur. Le
  StackExchange bibliothèque rend cette opération disponible à travers le `IDatabase.StringGetSetAsync`
  méthode. L'extrait de code ci-dessous montre un exemple de cette méthode. Copiez le code suivant retourne le courant
  valeur associée à la clé «: compteur de données » de l'exemple précédent et réinitialise la valeur
  pour cette clé à zéro, tout dans le cadre de la même opération :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` et `MSET`, qui peut renvoyer ou modifier un ensemble de valeurs de chaîne en une seule opération. Le
  `IDatabase.StringGetAsync` et `IDatabase.StringSetAsync` les méthodes sont surchargées pour prendre en charge
  Cette fonctionnalité, comme illustré dans l'exemple suivant :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

Vous pouvez également combiner plusieurs opérations en une seule transaction Redis comme décrit dans la section Transactions Redis et des lots de ce guide. La bibliothèque StackExchange soutient pour les transactions à travers le `ITransaction` interface. Vous pouvez créer un objet ITransaction en utilisant la méthode IDatabase.CreateTransaction et appeler des commandes à la transaction en utilisant les méthodes fournies `ITransaction` objet. Le `ITransaction` interface permet d'accéder à un ensemble similaire de méthodes telles que la `IDatabase` l'interface sauf que toutes les méthodes sont asynchrones ; ils sont uniquement effectuées lorsque la `ITransaction.Execute` la méthode est appelée. La valeur retournée par la méthode execute indique si la transaction a été créée avec succès (vrai) ou il a échoué (false).

L'extrait de code suivant montre un exemple que deux compteurs incréments et décréments dans le cadre de la même opération :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

N'oubliez pas que les transactions Redis sont contrairement aux transactions de bases de données relationnelles. La méthode Execute simplement en file d'attente toutes les commandes qui composent la transaction pour l'exécution, et si l'un d'entre eux est mal formé, puis, la transaction est abandonnée. Si toutes les commandes ont été mises en attente avec succès, chaque commande sera exécutée de façon asynchrone. Si n'importe quelle commande échoue, les autres seront encore continuer le traitement. Si vous avez besoin de vérifier qu'une commande s'est terminée correctement vous devez extraire les résultats de la commande à l'aide de la propriété Result de la tâche correspondante, comme indiqué dans l'exemple ci-dessus. Lire le résultat propriété se bloque jusqu'à ce que la tâche est terminée.

Pour plus d'informations, consultez le [Transactions en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) page sur le site StackExchange.Redis.

Pour effectuer des opérations de traitement par lots, vous pouvez utiliser l'interface de IBatch de la bibliothèque StackExchange. Cette interface permet d'accéder à un ensemble de méthodes similaires comme l'interface IDatabase sauf que toutes les méthodes sont asynchrones. Vous créez un objet IBatch à l'aide de la méthode de IDatabase.CreateBatch et puis exécutez le lot à l'aide de la méthode IBatch.Execute, comme illustré dans l'exemple suivant. Simplement, copiez le code suivant définit une valeur de chaîne et incrémente et décrémente les mêmes compteurs utilisés dans l'exemple précédent et affiche les résultats :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Il est important de comprendre que contrairement à une transaction, si une commande dans un lot échoue parce qu'il est mal formé les autres commandes peuvent toujours exécuter ; la méthode IBatch.Execute ne retourne pas une indication de succès ou d'échec.

### Exécution d'opérations de cache fire-and-forget

Redis prend en charge les opérations de fire-and-forget à l'aide de la commande drapeaux. Dans ce cas, le client simplement lance une opération mais n'a aucun intérêt dans le résultat et n'attend pas la commande restant à accomplir. L'exemple suivant montre comment exécuter la commande INCR comme une opération de fire-and-forget :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Expirant automatiquement les clés

Lorsque vous stockez un élément dans un cache Redis, vous pouvez spécifier un délai après lequel le point sera automatiquement supprimé du cache. Vous pouvez également interroger plus combien de temps une clé a avant sa date d'expiration à l'aide de la `TTL` commande ; Cette commande est disponible pour les applications StackExchange en utilisant la méthode IDatabase.KeyTimeToLive.

L'extrait de code suivant montre un exemple de réglage heure d'expiration de 20 secondes sur une touche et l'interrogation de la durée de vie restante de la clé :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Vous pouvez également définir le délai d'expiration à une date précise et une heure en utilisant la commande EXPIRE, disponible dans la bibliothèque StackExchange comme la méthode de KeyExpireAsync :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Astuce :_ Vous pouvez supprimer manuellement un élément du cache à l'aide de la commande DEL, qui est disponible via la bibliothèque StackExchange comme la méthode de IDatabase.KeyDeleteAsync.

### L'utilisation des balises à Croix-corrélat éléments mis en cache

Un jeu de Redis est une collection de plusieurs éléments qui partagent une seule clé. Vous pouvez créer un jeu en utilisant la commande de la DDAS. Vous pouvez récupérer les éléments dans un ensemble en utilisant la commande SMEMBERS. La bibliothèque StackExchange implémente la commande SADD via la méthode IDatabase.SetAddAsync et la commande SMEMBERS avec la méthode IDatabase.SetMembersAsync. Vous pouvez également combiner des ensembles existants pour créer de nouveaux ensembles en utilisant les SDIFF (différence ensembliste), frittage (intersection définie) et des commandes SUNION (union définie). La bibliothèque StackExchange unifie ces opérations dans la méthode IDatabase.SetCombineAsync ; le premier paramètre de cette méthode spécifie l'opération ensembliste pour exécuter.

Les extraits de code suivants montrent comment les ensembles peuvent être utiles pour rapidement stocker et récupérer des collections d'éléments associés. Copiez le code suivant utilise le type de BlogPost décrit dans la section application Redis Cache les Applications clientes. Un objet BlogPost contient quatre champs — un ID, un titre, un score de classement et une collection de balises. Le premier extrait de code ci-dessous montre les exemples de données utilisés pour remplir une liste c# d'objets BlogPost :

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Vous pouvez stocker les balises pour chaque objet BlogPost comme un ensemble dans un cache Redis et associer chaque jeu avec l'ID de la BlogPost. Cela permet à une application trouver rapidement toutes les balises appartenant à un blog spécifique. Pour activer la recherche dans la direction opposée et trouver tous les messages de blog qui partagent une balise spécifique, vous pouvez créer un autre jeu qui tient le blog posts faisant référence à l'ID de la balise dans la clé :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Ces structures vous permettent d'exécuter plusieurs requêtes courantes très efficacement. Par exemple, vous pouvez trouver et afficher toutes les balises pour blog post 1 comme ceci :

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Vous pouvez trouver toutes les balises qui sont communs aux blog post 1 et blog post 2 en effectuant une opération d'intersection définie, comme suit :

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Et vous pouvez trouver tous les messages de blog qui contiennent une balise spécifique :

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Éléments de constat récemment accédé

A common problem required by many applications is to find the most recently accessed items. For example, a blogging site might want to display information about the most recently read blog posts. You can implement this functionality by using a Redis list. A Redis list contains multiple items that share the same key, but the list acts as a double-ended queue. You can push items on to either end of the list by using the LPUSH (left push) and RPUSH (right push) commands. You can retrieve items from either end of the list by using the LPOP and RPOP commands.  You can also return a set of elements by using the LRANGE and RRANGE commands. The code snippets below show how you can perform these operations by using the StackExchange library. This code uses the BlogPost type from the previous examples. As a blog post is read by a user, the title of the blog post is pushed onto a list associated with the key "blog:recent_messages"dans le cache de Redis en utilisant la méthode IDatabase.ListLeftPushAsync :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Comme la lecture des messages blog, leurs titres sont poussés sur la même liste. La liste est classée par l'ordre dans lequel ils ont été ajoutés ; le plus récemment lu blogue est vers l'extrémité gauche de la liste (si le même message de blog est lu que plus d'une fois, il aura plusieurs entrées dans la liste). Vous pouvez afficher les titres des postes plus récemment lus en utilisant la méthode IDatabase.ListRange. Cette méthode prend la clé qui contient la liste, un point de départ et un point d'arrivée. Le code suivant extrait les titres les 10 postes de blog (les numéros de 0 à 9) à la fin de l'extrême gauche de la liste :

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Notez que ListRangeAsync ne supprime pas les éléments de la liste ; pour ce faire, vous pouvez utiliser les méthodes IDatabase.ListLeftPopAsync et IDatabase.ListRightPopAsync.

Pour empêcher la liste de croître indéfiniment, vous pouvez régulièrement réforme points en taillant la liste. L'extrait de code ci-dessous, supprime tout, mais les 5 plus à gauche les éléments de la liste :

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Mise en œuvre d'un chef de bord

Les éléments dans un ensemble par défaut ne sont pas détenus dans un ordre spécifique. Vous pouvez créer un ensemble ordonné en utilisant la commande ZADD (la méthode IDatabase.SortedSetAdd dans la bibliothèque StackExchange). Les éléments sont triés à l'aide d'une valeur numérique appelée un score fourni comme paramètre à la commande. L'extrait de code suivant ajoute le titre d'un billet de blog pour une liste ordonnée. Dans l'exemple, chaque billet de blog possède également un champ de partition qui contient le classement du blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Vous pouvez récupérer les titres de post de blog et les notes dans l'ordre de score de croissant à l'aide de la méthode IDatabase.SortedSetRangeByRankWithScores :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE. REMARQUE] La bibliothèque de StackExchange fournit également la méthode IDatabase.SortedSetRangeByRankAsync qui retourne les données dans l'ordre de score, mais ne renvoie pas les scores.

Vous pouvez également récupérer des éléments dans l'ordre décroissant des scores et limiter le nombre d'éléments retournés en fournissant des paramètres supplémentaires à la méthode IDatabase.SortedSetRangeByRankWithScoresAsync. L'exemple suivant affiche les titres et les partitions les 10 postes de blog rang haut de la page :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

L'exemple suivant utilise la méthode IDatabase.SortedSetRangeByScoreWithScoresAsync qui vous permet de limiter les éléments retournés à ceux qui s'inscrivent dans un score donné gamme :

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### La messagerie à l'aide de canaux

En dehors d'agissant comme un cache de données, un serveur Redis fournit la messagerie grâce à un mécanisme de haute performance éditeur/abonné. Les applications clientes peuvent s'abonner à un canal, et autres applications ou services peuvent publier des messages sur le canal. Les applications abonnées recevront ensuite ces messages et puissent les traiter.

Pour vous abonner à canal, Redis fournit la commande SUBSCRIBE. Cette commande attend le nom d'un ou plusieurs canaux sur lesquels l'application accepte les messages. La bibliothèque StackExchange inclut l'interface ISubscription qui permet à une application .NET Framework pour vous inscrire et publier sur canaux. Vous créez un objet ISubscription en utilisant la méthode GetSubscriber de la connexion au serveur Redis et puis écoutez les messages sur un canal à l'aide de la méthode SubscribeAsync de cet objet. L'exemple de code suivant montre comment s'abonner à un canal nommé « messages : blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Le premier paramètre de la méthode Subscribe est le nom du canal. Ce nom suit les mêmes conventions que celui utilisé par touches dans le cache et peut contenir des données binaires, bien qu'il est conseillé d'utiliser des chaînes relativement courts et significatifs afin d'obtenir la maintenabilité et la bonne exécution. Vous devez également noter que l'espace de noms utilisé par les canaux est distinct de celui utilisé par touches, donc vous pouvez avoir des canaux et des clés qui ont le même nom, même si cela peut rendre votre code d'application plus difficile à maintenir.

Le deuxième paramètre est un délégué Action. Ce délégué s'exécute de façon asynchrone, chaque fois qu'un nouveau message s'affiche sur le canal. Cet exemple affiche simplement le message sur la console (le message contiendra le titre d'un article de blog).

Pour publier sur un canal, une application peut utiliser la commande Redis la publication. La bibliothèque de StackExchange fournit la méthode IServer.PublishAsync pour effectuer cette opération. L'extrait de code suivant montre comment publier un message sur le canal « messages : blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Il y a plusieurs points, que vous devez comprendre le mécanisme de publication/abonnement :

- Plusieurs abonnés peuvent s'inscrire sur le même canal, et ils recevront tous les messages publiés sur ce canal.
- Les abonnés reçoivent seulement les messages qui ont été publiés après que qu'ils ont souscrites. Canaux n'est pas mis en mémoire tampon, et une fois qu'un message est publié l'infrastructure Redis pousse le message à chaque abonné et le supprime ensuite.
- Par défaut, les messages sont reçus par les abonnés dans l'ordre dans lequel ils sont envoyés. Dans un système très actif avec un grand nombre
  des messages et les nombreux abonnés et des éditeurs, garantissant la livraison séquentielle des messages peut ralentir les performances du système. If
  chaque message est indépendant et l'ordre est indifférent vous pouvez activer le traitement simultané par le système Redis qui peut aider à
  améliorer la souplesse. Vous pouvez y parvenir en un client StackExchange en définissant la PreserveAsyncOrder de la connexion utilisée par
  l'abonné à false :
  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Lignes directrices et modèles liés

Le modèle suivant peut-être également être pertinent pour votre scénario, lors de l'implémentation de la mise en cache dans vos applications :

- [Modèle cache-part](http://msdn.microsoft.com/library/dn589799.aspx): Ce modèle explique comment charger des données à la demande dans une cache dans un magasin de données. Cette tendance contribue également à maintenir la cohérence entre les données contenues dans le cache et les données dans le magasin de données originale.
- Le [Sharding Pattern](http://msdn.microsoft.com/library/dn589797.aspx) fournit des informations sur l'implémentation du partitionnement horizontal afin d'améliorer l'évolutivité lors du stockage et accéder à gros volumes de données.

## Plus d'informations

- Le [Classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) page sur le site Web de Microsoft.
- Le [Cache de Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) page sur le site Web de Microsoft.
- Le [Quelle offre Azure Cache est bon pour moi ?](http://msdn.microsoft.com/library/azure/dn766201.aspx) page sur le site Web de Microsoft.
- Le [Modèle de configuration](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) page sur le site Web de Microsoft.
- Le [Task-based Asynchronous Pattern](http://msdn.microsoft.com/library/hh873175.aspx) page sur le site Web de Microsoft.
- Le [Pipelines et multiplexeurs](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) page sur le repo StackExchange.Redis GitHub.
- Le [Redis Persistence](http://redis.io/topics/persistence) page sur le site Redis.
- Le [Page de réplication](http://redis.io/topics/replication) sur le site Redis.
- Le [Redis Cluster tutoriel](http://redis.io/topics/cluster-tutorial) page sur le site Redis.
- Le [Partitionnement : Comment diviser les données entre plusieurs instances de Redis](http://redis.io/topics/partitioning) page sur le site Redis.
- La page [Utilisant le Redis comme un Cache LRU](http://redis.io/topics/lru-cache) sur le site Redis.
- Le [Page des transactions](http://redis.io/topics/transactions) sur le site Redis.
- Le [Redis sécurité](http://redis.io/topics/security) page sur le site Redis.
- La page [Tour de Cache Redis Azur](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog d'Azur.
- La page [Redis en cours d'exécution sur une machine virtuelle Linux de CentOS](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) dans Azure sur le site Web de Microsoft.
- Le [Fournisseur d'état de Session ASP.NET pour Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn690522.aspx) page sur le site Web de Microsoft.
- Le [Fournisseur de Cache de sortie ASP.NET pour Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn798898.aspx) page sur le site Web de Microsoft.
- La page [Développer pour Azur Redis Cache](http://msdn.microsoft.com/library/azure/dn690520.aspx) sur le site d'Azur.
- La page [Une Introduction aux types de données Redis et abstractions](http://redis.io/topics/data-types-intro) sur le site Redis.
- Le [Utilisation de base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) page sur le site StackExchange.Redis.
- Le [Transactions en Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) page sur le repo StackExchange.Redis.
- Le [Guide de partitionnement de données](http://msdn.microsoft.com/library/dn589795.aspx) sur le site Web de Microsoft.
