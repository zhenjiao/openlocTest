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

# Microsoft Azure Vorschau Portal Übersicht

Das Microsoft Azure-Vorschau-Portal ist eine zentrale Stelle, wo man bereitstellen und verwalten Sie Ihre Azure Ressourcen.  Dieses Tutorial wird Sie mit dem Portal vertraut zu machen und zeigen, wie Sie einige dieser wichtigen Funktionen verwenden:
- A **umfassende Markt** Das lässt Sie stöbern Sie in Tausenden von Produkten von Microsoft und anderen Anbietern, die erworben und/oder bereitgestellt werden können.
- A **einheitlichen und skalierbaren durchsuchen-Erfahrung** Das macht es leicht, die Ressourcen, die Sie interessieren und führen verschiedene Verwaltungsvorgänge zu finden.
- **Konsequentes Management-Seiten** (oder Messer), die können Sie Verwalten von Azure die Vielzahl von Diensten durch eine konsistente Weise aufzudecken, Einstellungen, Aktionen, Gebührenzählung Informationen, Überwachung und Nutzung Gesundheitsdaten und viel mehr.
- A **persönliche Erfahrung** dass können, die Sie erstellen einen benutzerdefinierten Startbildschirm, der mit den Informationen angezeigt, den Sie immer, wenn möchten, Sie einloggen.  Sie können auch jeder Management-Blades anpassen, die Fliesen enthalten.

 ![Azurblaue Portal UI Orientierung][UIOrientation]

## Bevor Sie loslegen

Sie benötigen ein gültiges Azure Abonnement dieses Tutorial durchlaufen.  Wenn Sie nicht ein, dann haben [Melden Sie sich für einen kostenlosen Testzugang](http://azure.microsoft.com/pricing/free-trial/) Heute ist.  Sobald Sie ein Abonnement haben, können Sie im Portal unter zugreifen. [https://Portal.Azure.com].

## Gewusst wie: Erstellen einer Ressource

Azurblau hat einen Marktplatz mit Tausenden von Artikeln, die Sie von einem Ort erstellen können.  Nehmen wir an, dass Sie eine neue Windows Server 2012 VM erstellen möchten.  Die + neuer Hub ist Ihr Einstieg in eine kuratierte Reihe Empfohlene Kategorien vom Marktplatz.  Jede Kategorie hat eine kleine Gruppe von Top-Angebote zusammen mit einem Link zum vollständigen Markt, der alle Kategorien und Suche zeigt. Um die neue Windows Server 2012 VM zu erstellen, führen Sie die folgenden Aktionen:  

1.	Windows Server 2012 wird gekennzeichnet, damit Sie es aus der Compute-Kategorie auswählen können.  
2.	Einige grundlegenden Eingaben in einem Formular ausfüllen.
3.	Klicken Sie auf 'Erstellen' und Sie sind VM startet sofort bereitstellen.

Die Benachrichtigungen Nabe warnt Sie, wenn Ihre Ressource erstellt wurde und eine Verwaltungs-Blade wird geöffnet (Sie können immer durchsuchen auf Ressourcen später).

![Portal-Kategorien][PortalCategories]


## So finden Sie Ihre Ressourcen

Sie können jederzeit polig häufig Zugriff auf Ressourcen zu Ihrem Startboard, aber möglicherweise müssen, um etwas zu suchen, die Sie häufig zugreifen nicht.  Der durchsuchen-Hub unten abgebildet ist Ihr Weg, um alle Ressourcen.  Sie können Filtern nach Abonnement, Spalten auswählen/Resize und navigieren Sie zu dem Management-Blades durch Anklicken der einzelnen Elemente.

![Durchsuchen von Hub][BrowseHub]

## Verwalten und Delegieren des Zugriffs auf eine Ressource

Überwachen Sie aus dieser Klinge, die Sie auf den virtuellen Computer mithilfe von Remotedesktop verbinden können wesentlicher Kennzahlen zu, Steuern Sie den Zugriff auf diese VM mit rollenbasierten Zugriff (RBAC), konfigurieren Sie den virtuellen Computer und führen Sie andere wichtige Aufgaben aus.  Delegieren der Zugriff basierend auf Rolle ist entscheidend für die Verwaltung im Maßstab.  Klicken Sie auf [Hier](role-based-access-control-configure.md) um mehr darüber zu erfahren. Gehen folgendermaßen Sie vor, um Zugriff auf eine Ressource zu delegieren:

1.	Suchen Sie Ihr Hilfsmittel.
2.	Klicken Sie auf "Alle Einstellungen" im Abschnitt Essentials.
3.	Klicken Sie in der Einstellungsliste 'Benutzer'.
4.	Klicken Sie in der Befehlsleiste auf "Hinzufügen".
5.	Wählen Sie einen Benutzer und eine Rolle.

![Verwalten einer Ressource][ManageResource]

## Eine Ressource-Klinge anpassen

Azurblau preconfigures die klingen für Ihre Ressourcen, aber die Fliesen auf diese klingen sind Ihnen zu kontrollieren.  Sie können leicht gehen in anpassen-Modus zum Hinzufügen, entfernen, ändern Sie die Größe oder die Fliesen neu zu ordnen. Um eine Klinge anzupassen, führen Sie die folgenden Aktionen:

1.	Suchen Sie Ihr Hilfsmittel.
2.	Klicken Sie auf die '...' an der Spitze der Klinge, die Sie anpassen möchten.
3.	Klicken Sie auf 'Hinzufügen Teile'.
4.	Drag &amp; Drop Teile zu starten.  

![Anpassen von Blades][CustomizeBlades]

## So erhalten Sie Hilfe

Wenn Sie ein Problem haben, sind wir hier für Sie.  Das Portal hat eine Hilfe und Support-Seite, die Sie in die richtige Richtung zeigen können.  Je nach Ihrer [Support-plan](http://azure.microsoft.com/support/plans/), Sie können Support-Tickets auch direkt im Portal erstellen.  Nachdem Sie ein Supportticket haben, können Sie den Lebenszyklus des Tickets von innerhalb des Portals verwalten. Auf der Seite Hilfe und Unterstützung erhalten Sie durch die Navigation zu durchsuchen-> Hilfe + Unterstützung.  

![Hilfe und support][HelpSupport]

## Zusammenfassung

Betrachten wir einmal, was Sie in diesem Tutorial gelernt:
- Sie haben gelernt, wie anmelden, erhalten ein Abonnement und wechseln in das portal
- Sie haben mit dem Portal UI orientiert und gelernt zu erstellen und Durchsuchen von Ressourcen
- Sie haben gelernt, wie erstellen Sie eine Ressource und Ressourcen durchsuchen
- Sie haben gelernt, über die Struktur oder Management-Blades und wie Sie verschiedene Arten von Ressourcen konsequent verwalten können
- Sie haben gelernt, wie zur Anpassung von Informationen bringen Sie sich interessieren, um den Dreh- und Angelpunkt
- Sie haben gelernt, wie Sie die Steuerung des Zugriffs auf Ressourcen mithilfe von rollenbasierten Zugriff (RBAC)
- Sie haben gelernt, wie Sie Hilfe und support

Das Microsoft Azure-Vorschau-Portal vereinfacht radikal erstellen und Verwalten von Anwendungen in der Cloud.  Werfen Sie einen Blick auf die [Management-blog](http://azure.microsoft.com/blog/topics/management/) um auf dem laufenden halten, wie wir ständig [zuhören, Feedback](http://feedback.azure.com/forums/223579-azure-preview-portal) und Verbesserungen.  [ScottGu's blog](http://weblogs.asp.net/scottgu) ist ein weiterer großartiger Ort für alle himmelblau-Updates zu suchen.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
