<properties
   pageTitle="Content Delivery Network (CDN) guidance | Microsoft Azure"
   description="Guidance on Content Delivery Network (CDN) to deliver high bandwidth content hosted in Azure."
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

# Contenu orientation Delivery Network (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Vue d'ensemble

Le Microsoft Azure Content Delivery Network (CDN) offre aux développeurs une solution globale pour fournir des contenus haut débit qui sont hébergé dans Azure. Le CDN met en cache les objets accessibles au public, chargés de stockage blob Azure ou un dossier d'application à placés stratégiquement endroits afin de fournir une bande passante maximale de livraison contenu aux utilisateurs. Il est généralement utilisé pour fournir des contenus statiques tels que les images, feuilles de style, documents, fichiers, scripts côté client et des pages HTML.

Les principaux avantages d'utiliser le CDN sont une latence plus faible et accélérer la livraison de contenus aux utilisateurs quel que soit leur localisation géographique en ce qui concerne le centre de données où l'application est hébergée, et une réduction de la charge sur l'application elle-même parce qu'elle est relevée du traitement requis d'accès et de livrer le contenu. Cette réduction de charge peut contribuer à accroître les performances et l'évolutivité de l'application, en plus de minimiser le coût Hébergement en réduisant les ressources de traitement requis pour atteindre un certain niveau de performances et de disponibilité.

Vous pouvez être en mesure d'utiliser d'autres systèmes de réseau de distribution de contenu qui ne sont pas implémentées par Azure dans vos applications si l'Azure CDN ne répond pas à vos besoins. Alternativement, vous pouvez être en mesure d'utiliser l'Azure CDN pour les applications hébergées avec d'autres fournisseurs en exposant le contenu statique dans Azure storage ou dans les instances de calcul Azure.  
![](media/best-practices-cdn/CDN.png)

## Comment et pourquoi le CDN est utilisé

Les utilisations typiques pour le CDN incluent :  

- Fournir des ressources statiques pour les applications clientes, souvent d'un site Web. Ceux-ci peuvent être des images, des feuilles de style, documents, fichiers, scripts côté client, pages HTML, des fragments HTML ou tout autre contenu que le serveur n'a pas besoin de modifier pour chaque demande. L'application peut créer des éléments lors de l'exécution et les rendre disponibles au CDN (par exemple, en créant une liste de manchettes actuel), mais il ne le fait pas pour chaque demande.

- Livrant le contenu statique et partagé public aux périphériques tels que les téléphones mobiles et tablettes où l'application elle-même est un service web qui propose une API pour les clients. En plus de contenu que le serveur n'a pas besoin de modifier pour chaque demande, le CDN peut livrer des ensembles de données statique pour le client à utiliser - peut-être pour générer l'interface utilisateur du client. Par exemple, il pourrait être utilisé pour livrer des documents JSON ou XML.

- Desserte des sites entiers qui se composent de contenu statique publique uniquement aux clients, sans nécessiter aucun dédié calculent les ressources.

- Le streaming de fichiers vidéo au client sur demande. Vidéo bénéficie de la faible latence et une connectivité fiable disponible depuis les centres de données situés dans le monde qui offrent des connexions de CDN.

- Généralement d'améliorer l'expérience des utilisateurs, surtout ceux situés loin d'emplacement de centre de données de l'application qui subirait autrement latence plus élevée. Une grande proportion de la taille totale du contenu dans une application web est souvent statique et à l'aide de la ca peut aider à maintenir la performance et globale de l'utilisateur une expérience tout en éliminant la nécessité de déployer l'application sur plusieurs centres de données.

- Faire face à la charge croissante sur les applications que dispositifs de fixes et mobiles de service qui font partie de l'Internet des objets (IDO). Le grand nombre de ces dispositifs et appareils pourrait facilement submerger l'application si elle était tenue de traiter les messages à diffusion générale et gérer la distribution de mise à jour du firmware directement.

- Faire face aux pics et déferle dans la demande sans exiger l'application à l'échelle, en évitant l'augmentation conséquente les frais de fonctionnement. Par exemple, si une mise à jour est rejeté dans un système d'exploitation, pour un périphérique matériel, comme un modèle de routeur, ou pour les périphériques consommateurs comme une smart TV, il y aura un énorme pic de la demande tel qu'il est téléchargé par des millions d'utilisateurs et de périphériques sur une courte période.  

- Le tableau suivant montre des exemples du temps médian au premier octet depuis différents emplacements géographiques. Le rôle de web cible est déployé d'Azur ouest nous. Il y a une forte corrélation entre la plus poussée en raison de la CAN et la proximité d'un nœud CDN. Une liste d'emplacements de nœud Azure CDN est disponible à [Emplacements de noeud bleu azur Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/gg680302.aspx).  

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Time to First Byte (Origin)</p></th><th><p>Time to First Byte (CDN)</p></th><th><p>% faster for CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* London, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Has an Azure CDN node in the same city.  
* Has an Azure CDN node in a neighboring city.  


## Défis  

Il y a plusieurs défis à prendre en compte lorsque vous envisagez d'utiliser le CDN :  

- **Déploiement** Vous devez décider où charger du contenu pour le CDN de (l'origine d'où le CDN va chercher le contenu), et si vous avez besoin déployer du contenu dans plus d'un système de stockage (tels que le CDN et à un autre emplacement).

- Votre mécanisme de déploiement d'application doit tenir de déploiement du contenu statique compte et ressources ainsi que déployer les fichiers d'application tels que des pages ASPX. Par exemple, il peut exiger une étape distincte pour charger le contenu dans stockage blob Azure.

- **Gestion des versions et cache-control** Vous devez considérer comment vous mettre à jour de contenu statique et déployer de nouvelles versions. Le CDN ne prévoit pas actuellement un mécanisme de chasse d'eau contenu afin que les nouvelles versions sont disponibles. Il s'agit d'un défi similaire à gérer côté client mise en cache, comme par exemple un navigateur web.

- **Test** Il peut être difficile d'effectuer des tests en local de vos paramètres de RNC lorsque vous développez et testez une application localement ou dans la mise en scène.

- **SEO** Contenus tels que des images et documents sont servis d'un domaine différent lorsque vous utilisez ils CAN, qui aura un effet sur le référencement pour ce contenu.

- **Sécurité** Nombreux services RNC comme Azure CDN n'offrent pas actuellement de n'importe quel type de contrôle d'accès pour le contenu.

- **Résilience** CDN est un potentiel point unique de défaillance d'une application. Il a une plus faible disponibilité SLA que blob storage (qui peut être utilisé pour fournir des contenus directement) alors vous devrez peut-être envisager d'implémenter un mécanisme de secours pour le contenu critique.

- Les clients peuvent se connecter depuis un environnement qui n'autorise pas l'accès aux ressources sur le CDN. Cela pourrait être un environnement de sécurité restreint qui limite l'accès à seulement un ensemble de sources connues, ou qui empêche le chargement des ressources de rien d'autre que l'origine de la page. Par conséquent, une implémentation de secours sera nécessaire.

- Vous devez implémenter un mécanisme pour surveiller votre disponibilité du contenu par le biais de la CAN.

Scénarios où ca peut être moins utiles comprennent :  

- Lorsque le contenu a un faible taux de succès et donc peut être consultés quelques fois, ou une seule fois, au cours de la période de validité de durée de vie. La première fois qu'un élément est téléchargé, vous subissez des frais de transaction deux (depuis l'origine à la CAN et ensuite de l'EUR au client).

- Lorsque les données sont privées, comme pour les grandes entreprises ou les écosystèmes de chaîne d'approvisionnement.


## Orientations générales et les bonnes pratiques

En utilisant le CDN est une bonne façon de réduire la charge sur votre application et maximiser la disponibilité et les performances. Vous devriez considérer ceci pour l'ensemble du contenu approprié et votre application utilise des ressources. Considérez les points suivants lorsque vous concevez votre stratégie d'utiliser le CDN :  

- **Origine ** Déploiement de contenu par le biais de la ca suffit vous permet de spécifier un point de terminaison HTTP (port 80) que le service RNC utilisera pour accéder et mettre en cache le contenu. + Le point de terminaison peut spécifier un conteneur de stockage blob Azure qui contient le contenu statique souhaité à livrer par le CDN. Le conteneur doit être marqué comme public. Seulement les blobs dans un conteneur public disposant d'un accès en lecture publique sera disponibles via le CDN.

- Le point de terminaison peut spécifier un dossier nommé **CDN** dans la racine de l'une des couches de calcul de l'application (par exemple un rôle web ou une machine virtuelle). Les résultats des demandes de ressources, y compris les ressources dynamiques tels que des pages ASPX, vont être mis en cache sur le CDN. La période de mise en cache minimale est de 300 secondes. Toute période plus courte empêchera le contenu déployé au CDN (voir la section"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Contrôle de cache</a>« Pour plus d'informations).

- Si vous utilisez les Sites Web d'Azur, le point de terminaison est défini dans le dossier racine du site en sélectionnant le site lors de la création de l'instance CDN. Tout le contenu du site sera disponible via le CDN.

- Dans la plupart des cas, pointant vers votre point de terminaison CDN à un dossier dans une des couches de votre application calcul offrira plus de souplesse et de contrôle. Par exemple, il rend plus facile de gérer les besoins de routage actuels et futurs et de générer dynamiquement du contenu statique comme les miniatures d'images.

- Vous pouvez utiliser des chaînes de requête pour différencier les objets dans le cache lorsque le contenu est fourni de sources dynamiques tels que des pages ASPX. Toutefois, ce comportement peut être désactivé par un paramètre dans le portail de gestion lorsque vous spécifiez le point de terminaison CDN. Lors de la livraison contenu de stockage blob, chaînes de requête sont traités en tant que littéraux de chaîne donc deux éléments qui portent le même nom mais des chaînes de requête différents seront stockées comme éléments séparés sur le CDN.

- Vous pouvez utiliser la réécriture d'URL pour les ressources tels que les scripts et autres contenus pour éviter de déplacer vos fichiers vers le dossier d'origine CDN.

- Lorsque vous utilisez des objets BLOB Azure storage pour contenir le contenu pour la CAN, le URL des ressources dans les blobs est sensible à la casse pour le nom de conteneur et blob.

- Lorsque vous utilisez les Sites Web d'Azur, vous spécifiez le chemin d'accès à l'instance de ca dans les liens aux ressources. Par exemple, le texte suivant spécifie un fichier d'image dans le **Images** dossier du site qui sera remis par le CDN :

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Déploiement** Contenu statique peut doivent être configurés et déployés indépendamment de l'application si vous ne l'incluez pas dans le package de déploiement d'application ou processus. Examiner comment cela affectera le versioning approche vous permet de gérer aussi bien les composants de l'application et le contenu de la ressource statique.

- Examiner comment groupement (combinant plusieurs fichiers en un seul fichier) et minification (suppression de caractères inutiles tels que des espaces blancs, caractères de nouvelle ligne, commentaires et autres personnages) pour le script et les fichiers CSS seront traitées. Ces communément techniques utilisées peuvent réduire les temps de chargement pour les clients et sont compatibles avec la diffusion de contenus par le CDN. Pour plus d'informations, consultez [Groupage et Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Si vous avez besoin déployer le contenu d'un emplacement supplémentaire, ce sera une étape supplémentaire dans le processus de déploiement. Si l'application met à jour le contenu pour le CDN, peut-être à intervalles réguliers ou en réponse à un événement, il doit stocker le contenu mis à jour dans n'importe quel endroits supplémentaires ainsi que le point de terminaison pour la CAN.

- Vous ne pouvez pas configurer un point de terminaison CDN pour une application déployée dans Azure mise en scène, ou dans l'émulateur local Azure dans Visual Studio. Cela affectera les tests unitaires, tests fonctionnels et tests de prédéploiement finaux. Pour cela, vous devez autoriser en mettant en place un mécanisme de rechange. Par exemple, vous pouvez pré-déployer le contenu à la CAN à l'aide d'une application personnalisée temporaire ou utilitaire et effectuer des tests au cours de la période, qu'il est mis en cache. Également utiliser des directives de compilation ou de constantes globales pour contrôler où l'application charge les ressources de. Par exemple, lors de l'exécution en mode débogage il pourrait charger des ressources telles que les faisceaux de script côté client et tout autre contenu d'un dossier local et utilisez le CDN lors de l'exécution en mode release.

- Le CDN ne supporte pas les capacités de compression native. Cependant, il livrera le contenu qui est déjà compressé, tels que les fichiers zip et gzip. Lorsque vous utilisez un dossier d'application comme le point de terminaison CDN, le serveur peut compresser un contenu par défaut de la même manière que lorsque les livrer directement à un navigateur web ou un autre type de client. Celle-ci repose sur la **Accepter-encodage** valeur envoyée par le client. Dans Azure, la valeur par défaut est de compresser automatiquement le contenu lors de l'utilisation du processeur est inférieure à 50 %. Modification des paramètres et peut nécessiter l'utilisation d'une tâche de démarrage pour activer la compression de sortie dynamique dans IIS si vous utilisez les Services en nuage pour héberger l'application. Voir [Activation de la compression gzip avec Azure CDN à travers un rôle Web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) Pour plus d'informations.

- **Routage et gestion des versions** Vous devrez peut-être utiliser des instances différentes de CDN ; par exemple, lorsque vous déployez une nouvelle version de l'application, que vous pouvez utiliser un CDN différent. Si vous utilisez le stockage blob Azure comme l'origine du contenu, vous pouvez simplement créer un compte de stockage distinct ou un récipient séparé et pointez le point de terminaison CDN. Si vous utilisez le **CDN** dossier racine dans l'application comme le point de terminaison CDN que vous pouvez utiliser des techniques de réécriture d'URL pour diriger les demandes vers un autre dossier.

- Ne pas utiliser la chaîne de requête pour désigner les différentes versions de l'application dans les liens vers des ressources sur le RNC parce que, lors de l'établissement teneur de stockage blob Azure, la chaîne de requête est le nom de la ressource (le nom de l'objet blob). Elle peut aussi affecter comment le client met en cache les ressources.

- Déploiement des nouvelles versions du contenu statique lorsque vous mettez à jour une application peut être un défi si les ressources précédentes sont mis en cache sur le CDN. Pour plus d'informations, consultez la section"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Contrôle de cache</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Contrôle de cache**+ Considérer comment vous voulez gérer la mise en cache et à quelles couches de l'application. Par exemple, lorsque vous utilisez un dossier comme l'origine de ca, vous pouvez spécifier la mise en cache des pages qui génèrent le contenu et l'expiration de toutes les ressources de contenu dans un dossier spécifique. Vous pouvez également spécifier des propriétés de cache pour la CAN et pour le client à l'aide d'en-têtes HTTP standard. Bien que vous devriez déjà être gérer la mise en cache sur le serveur et le client, en utilisant le CDN vous aidera à vous rendre plus conscient de comment votre contenu est mis en cache et où.

- Pour empêcher les objets de disponible sur le CDN vous pouvez les supprimer depuis l'origine (conteneur d'objets blob ou application **CDN** dossier racine), enlever ou supprimer le point de terminaison CDN, ou — dans le cas de stockage blob — rendre le contenant ou privé de blob. Toutefois, les éléments seront retirés le CDN uniquement lorsque leur durée de vie (TTL) expire.

- Si aucun délai d'expiration de cache n'est spécifié (par exemple, lorsque le contenu est chargé de stockage blob), il va être mis en cache sur le CDN jusqu'à 72 heures.

- Dans une application web, vous pouvez définir la mise en cache et l'expiration de tout le contenu en utilisant le **clientCache** élément dans la **system.webServer/staticContent** section d'un fichier web.config. Vous pouvez placer un fichier web.config dans le dossier afin qu'elle affecte les fichiers dans tous les sous-dossiers et les fichiers dans ce dossier.

- Si vous utilisez une technique dynamique tel qu'ASP.NET pour créer le contenu pour le CDN, veillez à spécifier le **Cache.SetExpires** propriété sur chaque page. Le CDN pas en cache la sortie de pages qui utilisent le paramètre de mise en cache par défaut du public.  La période d'expiration de cache a une valeur appropriée pour s'assurer que le contenu n'est pas mis au rebut et rechargé à partir de l'application à intervalles très courts.  

- **Sécurité** Le CDN peut fournir du contenu sur HTTPS (SSL) en utilisant le certificat fourni par le CDN, mais il sera également disponible sur HTTP ainsi. Vous ne pouvez pas bloquer l'accès HTTP aux éléments dans le CDN. Vous devrez peut-être utiliser HTTPS pour demander le contenu statique qui s'affiche dans les pages chargées via HTTPS (par exemple un panier d'achat) pour éviter des avertissements de navigateur de contenu mixte.

- Des services CDN, tels l'Azure CDN, n'offrent pas actuellement toutes les installations de contrôle d'accès sécuriser l'accès au contenu. Vous ne pouvez pas utiliser des Signatures d'accès partagé (SAS) avec le CDN.

- Si vous livrez des scripts côté client à l'aide de la CAN, vous pouvez rencontrer des problèmes si ces scripts utilisent un **XMLHttpRequest** appel pour effectuer des requêtes HTTP pour d'autres ressources telles que des données, d'images ou de polices dans un domaine différent. De nombreux navigateurs web empêchent Croix-origine partage (SCRO) à moins que le serveur web est configuré pour définir les en-têtes de réponse appropriée de la ressource. Pour plus d'informations sur la SCRO, consultez la section « Atténuation des menaces » dans le guide <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Considérations de sécurité API</span>. Vous pouvez configurer le CDN pour prendre en charge de la SCRO: + si l'origine d'où vous livrent du contenu est stockage blob Azure, vous pouvez ajouter un **CorsRule** pour les propriétés du service. La règle peut spécifier les origines autorisées pour la SCRO requêtes, les méthodes autorisées telles que GET et l'âge maximal en secondes pour la règle (la période dans laquelle le client doit demander les ressources liées après le chargement du contenu original). Pour plus d'informations, consultez [Croix-origine ressource partage (SCRO) prise en charge pour les Services de stockage d'Azur](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Si l'origine d'où vous livrent du contenu est un dossier dans l'application, tels que la **CDN** dossier racine, vous pouvez configurer des règles de trafic sortant dans le fichier de configuration d'application pour définir un **Access-Control-Allow-Origin** en-tête sur toutes les réponses. Pour plus d'informations sur l'utilisation de règles de réécriture, voir [Module de réécriture d'URL](http://www.iis.net/learn/extensions/url-rewrite-module). Notez que cette technique n'est pas possible lorsque vous utilisez les Sites Web d'Azur.

- **Domaines personnalisés**+ La plupart CDNs, y compris l'Azure CDN, vous permettent pour spécifier un nom de domaine personnalisé et l'utiliser pour accéder aux ressources via le CDN. Vous pouvez également configurer un nom de sous-domaine personnalisé en utilisant un **CNAME** enregistrer dans votre DNS. En utilisant cette approche peut fournir une couche supplémentaire d'abstraction et de contrôle.

- Si vous utilisez un **CNAME**, vous ne pouvez pas (à la rédaction de ce guide) également utiliser SSL parce que le CDN utilise son propre certificat SSL unique, et cela ne correspondra pas à vos noms de domaine/sous-domaine personnalisé.

- **Secours CDN** Vous devez envisager comment votre application sera face à une défaillance ou d'indisponibilité temporaire de la CAN. Les applications clientes peuvent être en mesure d'utiliser des copies des ressources qui ont été mis en cache localement (sur le client) au cours de précédentes demandes, ou ils peuvent utiliser le code qui détecte les défaillances et demande à la place des ressources depuis l'origine (le dossier d'application ou un conteneur Azure blob qui contient les ressources) si le ca n'est pas disponible.

- **SEO** Si le référencement est une considération importante dans votre application, assurez-vous que vous: + inclure une **Rel** canonique en-tête de chaque page ou de la ressource.

- Utiliser un **CNAME** sous-domaine enregistrer et accéder aux ressources à l'aide de ce nom.

- Considérer l'impact du fait que l'adresse IP de la ca peut être un pays ou une région qui diffère de celui de l'application elle-même.

- Lorsque vous utilisez le stockage blob Azure comme origine, maintenir la même structure de fichiers de ressources sur le CDN comme dans les dossiers d'application.


- **Surveillance & Exploitation forestière** Comprennent le CDN dans le cadre de votre application de pilotage de la stratégie afin de détecter et de mesurer les pannes ou prolongé des occurrences de latence.

- Activer la journalisation pour le CDN et l'inclure dans le cadre de vos activités quotidiennes.

- **Coût d'implication** Vous êtes facturé pour les deux transferts de données sortantes de la RNC et les opérations de stockage lorsque le CA charge les données de votre application. Vous devriez régler cache réaliste expiration de contenu afin d'assurer la fraîcheur, mais pas si court à causer un rechargement répétées du contenu de l'application ou le stockage au CDN de blob. Cependant, des périodes de très longue expiration rendent plus difficile à supprimer des éléments dans le CDN parce que vous devez attendre pour qu'ils expirent.

- Les éléments qui sont rarement téléchargés devra débourser les frais de deux transaction sans fournir aucune réduction significative dans la charge du serveur.  



## Exemple de code
Cette section contient des exemples de code et de techniques pour travailler avec le CDN.  


## Réécriture d'URL
Ce qui suit à l'exception d'un fichier Web.config à la racine d'une application Cloud Services hébergés montre comment effectuer la réécriture d'URL lorsque vous utilisez le CDN. Demandes de l'EUR pour le contenu, qu'il met en cache sont redirigées vers des dossiers spécifiques au sein de la racine de l'application basée sur le type de la ressource (par exemple les scripts et images).  

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

L'ajout des règles de réécriture effectue les redirections suivantes :  

- La première règle vous permet d'intégrer une version dans le nom de fichier d'une ressource, qui est alors ignorée. Par exemple, **Nom de fichier_v123.jpg **est réécrit sous la forme **Filename.jpg**.

- Les quatre prochaines règles montrent comment faire pour rediriger des demandes si vous ne souhaitez pas stocker les ressources dans un dossier nommé **CDN** dans la racine du rôle web. Les règles de la carte la **CDN/Images**, **CDN/contenu**, **CDN/Scripts**, et **CDN/bundles** URL dans leurs dossiers respectifs de racine dans le rôle du web.
À l'aide de réécriture d'URL vous oblige à faire quelques changements pour le regroupement des ressources.  


## Groupage et minification ##

Groupés et minification peuvent être gérées par ASP.NET. Dans un projet MVC, vous définissez vos Packs dans **BundleConfig.cs**. Une référence au lot de script compressée est créée en appelant le **Script.Render** méthode, généralement dans le code dans la classe d'affichage. Cette référence contient une chaîne de requête qui inclut une table de hachage, qui est basé sur le contenu du paquet. Si le contenu de l'offre groupée change, le hachage généré va aussi changer.  
Par défaut, les instances de l'Azure CDN ont la **Statut de chaîne de requête** réglage désactivé. Afin que les faisceaux de script mis à jour pour être traités correctement par le CDN, vous devez activer le **Statut de chaîne de requête** paramètre pour l'instance CDN. Notez que c'est peut-être une heure ou plus avant de créer le CDN et modifier les paramètres en vigueur.  


## Plus d'informations
+ [Azure CDN](http://azure.microsoft.com/services/cdn/)
+ [Vue d'ensemble de l'azur Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Servir du contenu de Azure CDN dans votre Application Web](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Intégrer un service de Cloud Computing avec Azure CDN](cdn-cloud-service-with-cdn.md)
+ [Méthodes conseillées pour le réseau de livraison de contenu Azur](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)
