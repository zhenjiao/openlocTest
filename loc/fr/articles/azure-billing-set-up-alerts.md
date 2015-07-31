<properties 
	pageTitle="Set up billing alerts for your Microsoft Azure subscriptions" 
	description="Describes how you can set up alerts on your Azure bill so you can avoid billing surprises." 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# Configurer des alertes de facturation pour vos abonnements Microsoft Azure

Êtes-vous préoccupé de combien vous dépensez chaque mois pour votre abonnement Azur ? Si vous êtes l'administrateur du compte pour un abonnement d'Azur, vous pouvez utiliser le Service d'alerte de facturation Azure pour créer sur mesure facturation des alertes qui vous aident à surveiller et gérer des activité de facturation pour vos comptes d'Azur.

Ce service est un service de prévisualisation, donc la première chose que vous avez à faire est Inscrivez-vous pour lui - visiter <a href="https://account.windowsazure.com/PreviewFeatures">la page Aperçu fonctions </a> dans le portail de gestion de compte Azure pour cela.

## Définir les destinataires alertes de seuil et courriel

Après avoir reçu l'e-mail de confirmation que le service de facturation est activé pour votre abonnement, visitez <a href="https://account.windowsazure.com/Subscriptions">la page abonnements</a> dans le portail de compte. Cliquez sur l'abonnement que vous voulez surveiller et puis cliquez sur **Alertes**.

![][Image1]

Ensuite, cliquez sur **Ajouter alerte** pour créer votre premier - vous pouvez configurer un total de cinq alertes facturation par abonnement, avec un seuil différent et jusqu'à deux destinataires de message pour chaque alerte.

![][Image2]

Lorsque vous ajoutez une alerte, vous lui donnez un nom unique, choisissez un seuil de dépense et choisissez les adresses e-mail, laquelle les alertes seront envoyées. Lorsque vous configurez le seuil, vous pouvez choisir entre un **Total de la facturation** ou un **Crédit monétaire** de la **Alerte pour** liste. Pour un total de facturation, une alerte est envoyée lorsque l'abonnement dépenses dépassent le seuil. Pour un crédit monétaire, une alerte est envoyée lorsque crédits monétaires de descendent en dessous de la limite. Les crédits monétaires s'appliquent habituellement aux essais libres et les abonnements associés aux comptes MSDN.

![][Image3]

Azure prend en charge n'importe quelle adresse de courriel, mais ne vérifie pas que les œuvres d'adresse de courriel, vérifiez donc pour les fautes de frappe.

## Vérifiez sur vos alertes

Après avoir configuré des alertes, le Centre compte répertorie et montre combien plus vous pouvez mettre en place. Pour chaque alerte, vous voyez la date et heure qu'il a été envoyé, que ce soit un signalement aux fins de facturation totale ou crédit monétaire et la limite que vous configurez. Le format de date et d'heure est 24 heures heure universelle coordonnée (UTC) et la date est au format aaaa-mm-jj. Cliquez sur le signe d'une alerte dans la liste pour le modifier, ou cliquez sur la Corbeille pour le supprimer.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
