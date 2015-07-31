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

# Abrechnung-Benachrichtigungen für Ihre Microsoft Azure Abonnements einrichten

Sind Sie besorgt über wie viel Sie jeden Monat Ihre Azure Abonnements ausgeben? Wenn Sie das Kontoadministrator ein himmelblau-Abonnement sind, können Sie die Azure Billing Alert-Service erstellen Sie angepasste Abrechnung Warnungen, mit denen Sie überwachen und verwalten Abrechnung Aktivität für Ihre Azure-Konten.

Dieser Service ist eine Dienstleistung mit Vorschau, also das erste, was, das Sie tun müssen, ist, melden Sie sich für es - Besuch <a href="https://account.windowsazure.com/PreviewFeatures">der Seite Vorschau Features </a> im Verwaltungsportal Azure Konto, dies zu tun.

## Legen Sie die Warnung Schwellenwert und e-Mail-Empfänger

Nach der für Ihr Abonnement erhalten Sie die e-Mail-Bestätigung, die der Abrechnungsdienst eingeschaltet ist, besuchen <a href="https://account.windowsazure.com/Subscriptions">der Seite Abonnements</a> im Konto Portal. Klicken Sie auf das Abonnement zu überwachen, und klicken Sie dann auf die gewünschte **Warnungen**.

![][Image1]

Klicken Sie anschließend **Warnung hinzufügen** Erstellen Sie Ihre erste eins - können Sie insgesamt fünf Abrechnung Alarme pro Abonnement, mit eine andere Schwelle und bis zu zwei e-Mail-Empfänger für jede Warnung einrichten.

![][Image2]

Wenn Sie eine Warnung hinzufügen, geben sie einen eindeutigen Namen, wählen Sie eine Ausgabe Schwelle und wählen Sie die e-Mail-Adressen, an die Benachrichtigungen gesendet werden. Sie können entweder beim Einrichten der Schwellenwerts ein **Abrechnung-Total** oder eine **Monetäre Gutschrift** aus der **Warnung für** Liste. Für die Abrechnung insgesamt wird eine Warnung gesendet, wenn Abo Ausgaben den Schwellenwert überschreitet. Für eine monetäre Gutschrift wird eine Warnung gesendet, wenn die Währung Kredite unter die Grenze fallen. Monetäre Gutschriften gelten in der Regel für kostenlose Testversionen und Abonnements, die MSDN-Konten zugeordnet.

![][Image3]

Azure unterstützt beliebige e-Mail-Adresse jedoch nicht überprüfen, ob die e-Mail Adresse funktioniert, also überprüfen Sie noch einmal nach Tippfehlern.

## Prüfen Sie auf Ihre Benachrichtigungen

Nachdem Sie Warnungen einrichten, das Account Center listet sie und zeigt, wieviel mehr Sie einrichten können. Für jede Warnung sehen Sie das Datum und Zeit, die sie gesendet wurde, ob es eine Warnung für Billing Gesamt oder monetäre Gutschrift ist, und die Grenze, die Sie einrichten. Das Datums- und Uhrzeitformat ist 24-Stunden koordinieren (Weltzeit) und das Datum Format jjjj-mm-tt. Klicken Sie auf das Plus-Zeichen für eine Warnung in der Liste zu bearbeiten, oder klicken Sie auf den Papierkorb, um es zu löschen.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
