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

# Hintergrund-Arbeitsplätze-Anleitung

![](media/best-practices-background-jobs/pnp-logo.png)


## Übersicht

Viele erfordern Anwendungen Hintergrundaufgaben, die unabhängig von der Benutzeroberfläche (UI) zu betreiben. Beispiele sind Batch-Jobs, intensive Verarbeitung Aufgaben und Prozesse wie z. B. Workflows mit langer Laufzeit. Hintergrundaufträge können ohne Benutzerinteraktion ausgeführt werden; die Anwendung kann der Auftrag gestartet und dann weiter auf die interaktive Benutzer-Anforderungen verarbeitet. Dies kann helfen, um die Belastung für die Anwendungsbenutzeroberfläche zu minimieren, die Verbesserung der Verfügbarkeit und interaktive Reaktionszeiten verkürzen können.

Wenn eine Anwendung erforderlich ist, um Miniaturansichten der Bilder, die von Nutzern hochgeladen zu generieren, kann es beispielsweise dazu als Hintergrund-Job und die Miniaturansicht in Speicher, wenn Sie fertig sind zu speichern, ohne den Benutzer müssen warten, bis der Prozess abgeschlossen. In gleicher Weise kann ein Benutzer eine Bestellung ein Hintergrund-Workflows initiieren, das die Reihenfolge verarbeitet, während die Benutzeroberfläche ermöglicht den Benutzer das Durchsuchen der Website weiter. Wenn der Hintergrund-Job abgeschlossen ist, kann es die gespeicherten Aufträge-Daten aktualisieren und senden Sie eine e-Mail an den Benutzer, die Bestätigung der Bestellung.

Bei der Prüfung, ob eine Aufgabe als Hintergrundjob zu implementieren, ist die Hauptkriterien kann ob die Aufgabe ohne Benutzereingriff und ohne Benutzeroberfläche ausgeführt müssen warten, bis der Auftrag abgeschlossen. Aufgaben, die der Benutzer oder die Benutzeroberfläche zu warten, bis sie abgeschlossen sind erfordern möglicherweise nicht als Hintergrundjobs geeignet.

## Typen von Hintergrundjobs

Hintergrundjobs haben normalerweise eine oder mehrere der folgenden Merkmale:

- CPU intensive Jobs wie mathematische Berechnungen, Tragwerksmodell Analyse und mehr.
- I/O intensive Jobs wie Ausführen einer Reihe von Speicher-Transaktionen oder Dateien zu indizieren.
- Batch-Jobs wie nächtlichen Datenaktualisierung oder geplante Verarbeitung.
- Workflows wie Auftragserfüllung mit langer Laufzeit oder Bereitstellung von Diensten und Systemen.
- Sensible Datenverarbeitung, wo die Aufgabe an einem sichereren Ort zur Verarbeitung übergeben wird. Beispielsweise können Sie keine sensible Daten innerhalb einer Webserverrolle verarbeiten möchten, und verwenden Sie stattdessen ein Muster wie [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) um die Daten einer isolierten Hintergrund-Rolle übertragen werden, der Zugriff auf geschützter Speicher.

## Trigger

Hintergrundaufträge können auf verschiedene Weise initiiert werden. Effektiv, fallen alle von ihnen in einer der folgenden Kategorien:

- [**Ereignisgesteuert Trigger**](#event-driven-triggers). Die Aufgabe wird in Reaktion auf ein Ereignis in der Regel eine Aktion, die von einem Benutzer oder ein Schritt in einem Workflow gestartet.
- [**Zeitplan-gesteuerte Trigger**](#schedule-driven-triggers). Die Aufgabe wird nach einem Zeitplan basierend auf einen Timer aufgerufen. Dies kann einem wiederkehrenden Zeitplan oder einem einmaligen Aufruf für einen späteren Zeitpunkt angegeben sein.

### Ereignisgesteuert Trigger

Ereignisgesteuert Aufruf wird ein Trigger verwendet, um den Hintergrund starten. Beispiele für ereignisgesteuerte Trigger:

- Die Benutzeroberfläche oder eine andere Stelle platziert eine Nachricht in einer Warteschlange. Die Nachricht enthält Daten über eine Aktion, die, wie der Benutzer, die eine Bestellung stattgefunden hat. Der Hintergrundtask dieser Warteschlange überwacht und erkennt die Ankunft einer neuen Nachricht. Es liest die Nachricht und verwendet die Daten darin als Eingabe für die hintergrund-Job.
- Die Benutzeroberfläche oder eine andere Stelle speichert oder einen Wert im Speicher aktualisiert. Der Hintergrundtask überwacht den Speicher und erkennt Änderungen. Es liest die Daten und verwendet es als Eingabe für die hintergrund-Job.
- Die Benutzeroberfläche oder eine andere Arbeit stellt eine Anfrage an einen Endpunkt, z. B. ein HTTPS-URI oder eine API ausgesetzt als Web-Service. Es leitet die Daten, die erforderlich, um die Hintergrund-Task als Teil der Anforderung abzuschließen. Der Endpunkt oder Web-Dienst Ruft den Hintergrundtask, der die Daten als Eingabe verwendet.

Typische Beispiele für Aufgaben geeignet, ereignisgesteuerte Aufruf: Bildverarbeitung, Arbeitsabläufe, Übersendung von Informationen über remote Services, Senden von e-Mail-Nachrichten, die Bereitstellung von neuer Benutzern in mandantenfähige Applikationen und vieles mehr.

### Zeitplan-gesteuerte Trigger

Zeitplan-gesteuerte Aufruf verwendet einen Timer der Hintergrundtask gestartet. Beispiele für Zeitplan getrieben Trigger:

- Ein Timer läuft lokal innerhalb der Anwendung oder als Teil des Betriebssystems die Anwendung ruft eine Hintergrundaufgabe in regelmäßigen Abständen.
- Ein Timer läuft in eine andere Anwendung oder einen Zeitgeber-Dienst wie Azure Scheduler, sendet eine Anforderung an eine API oder Web-Dienst regelmäßig. Die API oder einen Webdienst aufruft Hintergrundtask.
- Ein separater Prozess oder eine Anwendung startet einen Zeitgeber, der Ursachen den Hintergrundtask einmal nach einer angegebenen Zeitspanne oder zu einem bestimmten Zeitpunkt aufgerufen werden.

Typische Beispiele für Aufgaben geeignet, Zeitplan-gesteuerte Aufruf gehören Batch-Verarbeitung-Routinen wie Aktualisierung Verwandte Produkte Listen für Benutzer basierend auf deren jüngstes Verhalten, routinemäßige Datenverarbeitung Aufgaben wie Aktualisierung Indizes oder generieren Ergebnisse, Analyse der Daten für Tagesberichte, Aufbewahrung Datenbereinigung und Konsistenzprüfungen der Daten angesammelt.

Verwenden Sie einen Zeitplan, getrieben von Aufgabe, die als einzelne Instanz ausgeführt werden muss, sollten Sie Folgendes beachten:

- Wenn die Compute-Instanz, die den Planer (z. B. eine virtuelle Maschine mithilfe von geplante Tasks von Windows) ausgeführt wird skaliert wird, haben Sie mehrere Instanzen des Zeitplanungsmoduls ausgeführt und diese könnten mehrere Instanzen der Aufgabe beginnen.
- Wenn Aufgaben länger als der Zeitraum zwischen Ereignisse Aufgabenplanung ausführen, kann der Planer eine andere Instanz der Aufgabe starten, während das vorhergehende noch ausgeführt wird.

## Zurückgeben von Ergebnissen

Hintergrundaufträge ausführen asynchron in einem separaten Prozess oder sogar einem anderen Speicherort, aus der Benutzeroberfläche oder der Prozess, der den Hintergrund-Task aufgerufen. Im Idealfall Hintergrundaufgaben sind "fire and forget" Operationen und deren Ausführungsfortschritt hat keine Auswirkungen auf die Benutzeroberfläche und der aufrufende Prozess. Dies bedeutet, dass der aufrufende Prozess nicht auf die Erfüllung der Aufgaben wartet, und daher kann nicht automatisch erkennen, wenn die Aufgabe beendet.
Benötigen Sie ein Hintergrundtask zu kommunizieren die aufrufende Aufgabe um Fortschritte oder Abschluss anzugeben, müssen Sie dafür einen Mechanismus implementieren. Einige Beispiele sind:

- Speicher, der für die Aufgabe UI oder Anrufer zugänglichen überwachen oder wählen Sie diese Option, wenn erforderlich kann schreiben Sie Status Indikatorwert. Andere Daten, die der Hintergrundtask an den Aufrufer zurückgeben muss, können in denselben Speicher abgelegt werden.
- Richten Sie eine Antwort-Warteschlange, der auf der Benutzeroberfläche oder der Anrufer hört. Der Hintergrundtask kann Nachrichten an die Warteschlange angibt, Status und den Abschluss senden. Daten, die der Hintergrundtask an den Aufrufer zurückgeben muss, können in den Nachrichten platziert werden. Wenn Sie die Azure Service-Bus verwenden, können Sie die **ReplyTo** und **CorrelationId** Eigenschaften, die diese Funktionalität zu implementieren. Weitere Informationen finden Sie unter [Korrelation Service Bus vermittelte Messaging](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Setzen Sie eine API oder Endpunkt von der Hintergrundtask, der die UI oder Anrufer zugreifen kann, um das Abrufen von Statusinformationen. Daten, die der Hintergrundtask an den Aufrufer zurückgeben muss, können in der Antwort enthalten sein.
- Haben Sie den Hintergrundtask Rückruf an der Benutzeroberfläche oder Anrufer über eine API, um Status an vordefinierten Punkten oder nach Abschluss anzugeben. Dies könnte durch lokal ausgelösten Ereignisse oder durch eine veröffentlichen und abonnieren Mechanismus. Daten, die der Hintergrundtask an den Aufrufer zurückgeben muss, können in der Anforderung oder Ereignis Nutzlast aufgenommen werden.

## Hosting-Umgebung

Sie können Tasks im Hintergrund unter Verwendung einer Anzahl von anderen Plattform Azure Services gehostet werden:

- [**Azurblaue Websites**](#azure-web-sites-and-webjobs). WebJobs können Sie benutzerdefinierte Aufträge auf der Grundlage von eine Reihe von verschiedenen Arten von Skripts oder ausführbare Programm im Rahmen der Website ausführen.
- [**Azure Cloud Services Web und Worker-Rollen**](#azure-cloud-services-web-and-worker-roles). Code innerhalb einer Rolle, die als Hintergrundtask ausgeführt wird, können Sie schreiben.
- [**Azurblaue virtueller Maschinen**](#azure-virtual-machines). Wenn Sie einen Windows-Dienst oder den Windows-Taskplaner verwenden möchten, ist es üblich, Ihre Hintergrund-Tasks in einem dedizierten virtuellen Computer hosten.

In den folgenden Abschnitten beschreiben jede dieser Optionen genauer und umfassen Überlegungen um Ihnen zu helfen die entsprechende Option wählen.

### Himmelblau-Websites und WebJobs

Azure WebJobs können Sie benutzerdefinierte Aufträge als Hintergrund-Tasks in einer Azure-Websites gehostet-Anwendung auszuführen. WebJobs können Skripts oder ausführbare Programme im Rahmen Ihrer Website als einen kontinuierlichen Prozess, oder als Antwort auf ein auslösendes Ereignis von Azure Scheduler oder externe Faktoren wie Änderungen Speicher Blobs und Nachrichtenwarteschlangen ausführen. Arbeitsplätze können gestartet und auf Nachfrage beendet und ordnungsgemäß heruntergefahren werden. Eine kontinuierlich laufende WebJob fehlschlägt, wird es automatisch neu gestartet. Aktionen "Wiederholen" und Fehler sind konfigurierbar.

Wenn Sie eine WebJob konfigurieren:

- Wenn den Auftrag auf eine ereignisgesteuerte Trigger reagieren soll, sollte es als konfiguriert werden **Kontinuierlich ausgeführt**. Das Skript oder Programm wird in den Ordner mit dem Namen Website/Wwwroot/app gespeichert._Daten/Arbeitsplätze/kontinuierliche.
- Wenn den Auftrag auf einem Zeitplan getrieben Auslöser reagieren soll, sollte es als konfiguriert werden **Nach einem Zeitplan ausgeführt**. Das Skript oder Programm wird in den Ordner mit dem Namen Website/Wwwroot/app gespeichert._Daten/Arbeitsplätze/ausgelöst.
- Wenn Sie sich entscheiden die **Führen Sie auf Nachfrage** Option, wenn Sie konfigurieren einen Job, es führt den gleichen Code wie die **Nach einem Zeitplan ausgeführt** Option, wenn Sie ihn starten.

Himmelblau WebJobs werden in der Sandbox der Website, das heißt sie können Zugriff auf Umgebungsvariablen, und teilen Informationen z. B. Verbindungszeichenfolgen mit der Website ausgeführt. Der Job hat Zugang zu den eindeutigen Bezeichner für die Maschine, die Ausführung des Auftrags. Die Verbindungszeichenfolge mit dem Namen **AzureJobsStorage** ermöglicht den Zugriff auf Azure Speicher Warteschlangen, Blobs und Tabellen für Anwendungsdaten und Service Bus für messaging und Kommunikation. Die Verbindungszeichenfolge mit dem Namen **AzureJobsDashboard** ermöglicht den Zugriff auf die Job-Action-Log-Dateien.

Azurblaue WebJobs haben folgende Merkmale:

- **Sicherheit**: Die Anmeldeinformationen der Bereitstellung der Website sind WebJobs geschützt.
- **Unterstützte Dateitypen**: WebJobs mit Befehlsskripte (.cmd), Batch-Dateien (. bat), PowerShell-Skripts (ps1) definiert werden können, bash Shell-Skripte (.sh), PHP-Skripte (.php), Python-Skripte (.py), JavaScript-Code (JS) und ausführbare Programme (.exe, .jar und mehr).
- **Bereitstellung**: Skripts und ausführbare Dateien bereitgestellt werden können, mit dem Azure Portal erstellt und bereitgestellt, mithilfe der [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) Add-in für Visual Studio oder die [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs), mit der [Azurblaue WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md), oder kopieren Sie sie direkt an den folgenden Speicherorten:
  - für Ausführung ausgelöst: Website/Wwwroot/app_Daten/Arbeitsplätze/ausgelöst / {job Name}
  - für die kontinuierliche Ausführung: Website/Wwwroot/app_Daten/Arbeitsplätze/kontinuierliche / {job Name}
- **Protokollierung**: Console.Out wird (markierte) als INFO und Console.Error als Fehler behandelt. Überwachung und Diagnose-Informationen mit dem Azure Portal zugegriffen werden kann, und die Log-Dateien können direkt von der Website heruntergeladen werden. Sie werden an den folgenden Speicherorten gespeichert:
  - für Ausführung ausgelöst: Vfs/Daten/Arbeitsplätze/kontinuierliche/JobName
  - für die kontinuierliche Ausführung: Vfs/Daten/Arbeitsplätze/ausgelöst/JobName
- **Konfiguration**: WebJobs kann mithilfe des Portals, der REST-API und PowerShell konfiguriert werden. Eine Konfigurationsdatei namens settings.job im selben Root-Verzeichnis, wie das Job-Skript verwendet werden kann, um die Konfigurationsinformationen für einen Auftrag benötigt. Zum Beispiel:
  - {"beenden_warten_Zeit": 60}
  - {"ist_Singleton": wahre}

### Überlegungen zur

- In der Standardeinstellung WebJobs-Skala mit der Website. Jedoch Arbeitsplätze können konfiguriert werden für einzelne Instanz durch Festlegen der **ist_Singleton** Configuration-Eigenschaft auf true fest. Einzelne Instanz WebJobs eignen sich für Aufgaben, die Sie nicht möchten, skalieren oder Ausführen als gleichzeitige Multiple Instanzen, z. B. erneute Indizierung, Datenanalyse und ähnliche Aufgaben.
- Um die Auswirkungen der Beschäftigung auf die Leistung der Website zu minimieren, sollten Sie eine leere Instanz der Azure-Websites in eine neue App Service Plan auf Host WebJobs, die möglicherweise lange laufen oder eine Ressource intensive zu erstellen.

### Weitere Informationen

- [Azurblaue WebJobs Empfohlene Ressourcen](websites-webjobs-resources/) Listet die vielen nützlichen Ressourcen, Downloads und Beispiele für WebJobs.

## Azure Cloud Services Web und Worker-Rollen

Hintergrundaufgaben können innerhalb einer Webserverrolle oder in einer separaten Arbeitsprozess-Rolle ausgeführt werden. Die Entscheidung ob einen Arbeitnehmer verwenden Rolle unter Beachtung der Skalierbarkeit und Elastizität Anforderungen, Task-Lebensdauer, beruhen sollte release Trittfrequenz, Sicherheit, Fehlertoleranz, Konflikte, Komplexität und die logische Architektur. Weitere Informationen finden Sie unter [Ressource-Konsolidierung-Muster zu berechnen](http://msdn.microsoft.com/library/dn589778.aspx).

Es gibt mehrere Möglichkeiten zur Implementierung von Hintergrundaufgaben in eine Cloud-Services-Rolle:

- Eine Implementierung der die **RoleEntryPoint** Klasse in der Rolle und seine Methoden zum Ausführen von Tasks im Hintergrund verwenden. Die Aufgaben im Zusammenhang mit WaIISHost.exe ausgeführt und können die **GetSetting** Methode der **CloudConfigurationManager** Klasse, Konfigurationseinstellungen zu laden. Weitere Informationen finden Sie unter [Lebenszyklus (Cloud-Services)](#lifecycle-cloud-services-).
- Verwenden Sie die Startaufgaben Hintergrundaufgaben ausgeführt, wenn die Anwendung gestartet wird. Die Aufgaben weiterhin im Hintergrund Set ausgeführt zu erzwingen die **taskType** Eigenschaft zu **Hintergrund** (wenn Sie dies nicht tun, der Startvorgang der Anwendung wird anhalten und warten auf die Aufgabe zu beenden). Weitere Informationen finden Sie unter [Azure Startaufgaben einmünden](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- Verwenden Sie das WebJobs-SDK Hintergrundaufgaben als WebJobs implementieren, die als ein Start-Vorgang initiiert werden. Weitere Informationen finden Sie unter [Erste Schritte mit dem azurblauen WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md).
- Verwenden Sie eine Startaufgabe, einen Windows-Dienst installieren, der eine oder mehrere Tasks im Hintergrund ausführt. Sie müssen festlegen, die **taskType** Eigenschaft zu **Hintergrund** damit der Dienst im Hintergrund ausgeführt wird. Weitere Informationen finden Sie unter [Azure Startaufgaben einmünden](http://msdn.microsoft.com/library/azure/hh180155.aspx).

### Ausführen von Hintergrundaufgaben in die Rolle "Webserver"

Der Hauptvorteil der Ausführung von Tasks im Hintergrund in die Rolle "Webserver" ist das Speichern im hosting Kosten, denn es keine Verpflichtung zur Bereitstellung von zusätzlicher Rollen gibt.

### Ausführen von Hintergrundaufgaben in eine Arbeiter-Rolle

Ausführung von Hintergrundaufgaben in einer Arbeitnehmer-Rolle hat mehrere Vorteile:

- Es können Sie verwalten, Skalierung separat für jede Art von Rolle. Beispielsweise müssen Sie eventuell weitere Instanzen einer Web-Rolle, die aktuelle Auslastung zu unterstützen, aber weniger Instanzen der Rolle der Arbeiter, die Tasks im Hintergrund ausführt. Skalierung Hintergrund Task Compute Instanzen getrennt von den UI-Rollen kann hosting Kosten reduzieren und gleichzeitig eine akzeptablen Leistung.
- Es entlastet den Verarbeitungsaufwand für Hintergrundaufgaben aus der Webserverrolle. Die Webserverrolle, die die Benutzeroberfläche bietet reaktionsfähig bleiben kann, und es kann bedeuten, dass weniger Instanzen erforderlich sind, um ein bestimmtes Volumen der Anfragen von Nutzern zu unterstützen.
- Es erlaubt Ihnen, die Trennung von Bereichen zu implementieren. Jede Rollentyp kann eine bestimmte Gruppe von klar definierten und verwandte Aufgaben implementieren. Dies macht die Gestaltung und Pflege des Codes einfacher, da gibt es weniger Abhängigkeit von Code und die Funktionalität zwischen den einzelnen Rollen.
- Es kann helfen, um sensible Prozesse und Daten zu isolieren. Webrollen, die die Benutzeroberfläche implementieren müssen beispielsweise nicht auf Daten zugreifen, die verwaltet und kontrolliert durch eine Arbeiter-Rolle ist. Dies ist hilfreich bei der Stärkung der Sicherheit, vor allem, wenn eine Muster wie z. B. die [Gatekeeper-Muster](http://msdn.microsoft.com/library/dn589793.aspx).  

### Überlegungen zur

Beachten Sie die folgenden Punkte bei der Auswahl, wie und wo Hintergrundaufgaben bereitstellen, bei der Verwendung von Cloud-Services-Web und Worker-Rollen:

- Hosting Hintergrundaufgaben in eine vorhandene Web-Rolle kann die Kosten des Betriebs einer separaten Arbeitsprozess Rolle nur für diese Aufgaben speichern, aber es wird voraussichtlich Auswirkungen auf die Leistung und Verfügbarkeit der Anwendung, wenn Konflikte für Verarbeitung und andere Ressourcen bestehen. Mit einer separaten Arbeitsprozess-Rolle schützt die Rolle "Webserver" vor den Auswirkungen von lange laufen oder Ressource intensive Tasks im Hintergrund.
- Wenn Sie host Hintergrundaufgaben, die mit der **RoleEntryPoint** Klasse, können Sie leicht dadurch zu einer anderen Rolle verschieben. Beispielsweise wenn Sie die Klasse in einer Webserverrolle und später erstellen entscheiden, müssen Sie die Aufgaben in einer Rolle Arbeitnehmer ausführen können Sie verschieben die **RoleEntryPoint** Implementierung der Klasse in die Rolle der Arbeiter.
- Startaufgaben sollen ein Programm oder ein Skript ausführen. Bereitstellen von hintergrund-Job als ausführbares Programm möglicherweise schwieriger, vor allem wenn es auch Bereitstellung von abhängigen Assemblys erfordert. Es kann einfacher Bereitstellung und Verwendung eines Skripts Hintergrundjob definieren, bei der Verwendung von Startaufgaben sein.
- Ausnahmen, die eine Hintergrundaufgabe fehlschlagen verursachen haben eine unterschiedliche Auswirkungen, je nach der Weise, dass sie gehostet werden:
  - Bei Verwendung der **RoleEntryPoint** Klasse Konzept, eine fehlgeschlagene Aufgabe führt dazu, dass die Rolle neu starten, damit der Task startet automatisch neu. Dies kann die Verfügbarkeit der Anwendung auswirken. Um dies zu verhindern, sicherzustellen, dass Sie robust Ausnahmebehandlung in der **RoleEntryPoint** Klasse und alle Hintergrundaufgaben. Verwenden Sie Code, um Aufgaben neu zu starten, die fehlschlagen, wo dies angemessen ist, und die Ausnahme um die Rolle neu zu starten, nur, wenn Sie nicht ordnungsgemäß aus dem Scheitern innerhalb des Codes wiederherstellen können.
  - Wenn Sie Startaufgaben verwenden, sind Sie verantwortlich für die Verwaltung der Taskausführung und Kontrolle scheitert sie.
- Verwaltung und Überwachung Startaufgaben ist schwieriger als mit der **RoleEntryPoint** Klasse Konzept. WebJobs-Azure-SDK enthalten jedoch eine Auswertung zu erleichtern, WebJobs verwalten, die Sie durch Startaufgaben initiieren.

### Weitere Informationen

- [Ressource-Konsolidierung-Muster zu berechnen](http://msdn.microsoft.com/library/dn589778.aspx)
- [Erste Schritte mit dem azurblauen WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started/)

## Azurblaue virtueller Maschinen

Hintergrundaufgaben können in einer Weise, die verhindert, dass sie auf Azure-Websites oder Cloud-Dienste bereitgestellt werden, oder dies kann nicht bequem. Typische Beispiele sind Windows-Diensten und Dienstprogramme von Drittanbietern und ausführbare Programme. Es kann auch für eine Ausführungsumgebung anders als, die die Anwendung hostet geschriebenen Programme umfassen. Beispielsweise kann es einem UNIX- oder Linux-Programm sein, die von einer Windows oder .NET Anwendung ausgeführt werden soll. Sie können aus einer Palette von Betriebssystemen für eine Azure virtuelle Maschine auswählen und Ihren Dienst oder die ausführbare Datei auf dieser virtuellen Maschine ausgeführt.

Damit Sie wählen, wenn virtuelle Maschinen nutzen können, finden Sie unter [Azurblaue Websites, Cloud-Services und virtuelle Maschinen-Vergleich](choose-web-site-cloud-service-vm.md). Für Informationen zu den Optionen für [Virtuelle Maschinen, finden Sie unter Virtual Machine und Cloud-Service-Größen für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Weitere Informationen über die Betriebssysteme und vorgefertigte Bilder für virtuelle Maschinen finden Sie unter [Azurblaue virtuelle Maschinen-Galerie](http://azure.microsoft.com/gallery/virtual-machines/).

Um der Hintergrundtask auf einem separaten virtuellen Computer zu starten, müssen Sie eine Reihe von Optionen:

- Sie können den Vorgang bei Bedarf direkt aus Ihrer Anwendung ausführen, durch Senden einer Anforderung an einen Endpunkt, den die Aufgabe verfügbar macht, übergeben Sie alle Daten, die die Aufgabe erfordert. Dieser Endpunkt Ruft die Aufgabe.
- Sie können die Aufgabe nach einem Zeitplan mit einem Planer oder Zeitgeber verfügbar in Ihrem gewählten Betriebssystem ausgeführt konfigurieren. Z. B. unter Windows können Sie Windows-Taskplaner, Skripte und Aufgaben auszuführen, oder wenn Sie SQL Server auf dem virtuellen Computer installiert haben, können Sie die SQL Server-Agent zum Ausführen von Skripten und Aufgaben.
- Azure-Planer können Sie um die Aufgabe zu initiieren, durch Hinzufügen einer Nachricht an eine Warteschlange, der der Task überwacht oder durch Senden einer Anforderung an eine API, die die Aufgabe verfügbar macht.

Siehe den obigen Abschnitt [Trigger](#triggers) für weitere Informationen können Sie Tasks im Hintergrund starten.  

### Überlegungen zur

Beachten Sie die folgenden Punkte bei der Entscheidung, ob Hintergrundaufgaben in einer Azure VM bereitgestellt:

- Hosting von Hintergrundaufgaben in einer separaten Azure Virtual Machine bietet Flexibilität und ermöglicht die präzise Steuerung Einleitung, Ausführung, Planung und Zuweisung von Ressourcen. Jedoch wird es Laufzeit Kosten erhöhen, wenn eine virtuelle Maschine bereitgestellt werden müssen, nur um die Tasks im Hintergrund laufen.
- Es gibt keine Möglichkeit, die Aufgaben in der Azure Portal und keine automatische Neustart-Funktion für fehlgeschlagene Vorgänge zu überwachen, obwohl Sie grundlegenden Status des virtuellen Computers überwachen und verwalten es mit der [Cmdlets zur Verwaltung von himmelblau Service](http://msdn.microsoft.com/library/azure/dn495240.aspx). Allerdings gibt es keine Einrichtungen zur Steuerung von Prozessen und Threads in Compute-Knoten. In der Regel erfordert mit einer virtuellen Maschine zusätzlichen Aufwand, einen Mechanismus zu implementieren, der Daten von Instrumentierung in der Aufgabe und das Betriebssystem des virtuellen Computers sammelt. Eine Lösung, die geeignet ist, verwenden Sie die [System Center Management Pack für Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Sie können prüfen, Erstellen von Überwachung Messsonden, die über HTTP-Endpunkte verfügbar sind. Der Code für diese Prüfpunkte könnte Gesundheit Überprüfungen durchführen, operativer Informationen und Statistiken zu sammeln oder Fehlerinformationen sammeln und an eine Anwendung zurückgeben. Weitere Informationen finden Sie unter [Gesundheit-Endpunkt-Überwachung-Muster](http://msdn.microsoft.com/library/dn589789.aspx).

### Weitere Informationen

- [Virtuelle Maschinen](http://azure.microsoft.com/services/virtual-machines/) auf der Azure-website
- [Azurblaue virtuelle Maschinen-FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

## Überlegungen zum Entwurf

Es gibt einige grundlegende Faktoren zu beachten, beim Entwerfen von Hintergrundaufgaben. Den folgenden Abschnitten werden die Partitionierung, Konflikte und Koordination.

## Partitionierung

Entscheiden Sie sich für Hintergrundaufgaben innerhalb einer vorhandenen Compute Instanz (z. B. eine Website, Web-Rolle, vorhandene Arbeitnehmer Rolle oder virtuelle Maschine) enthalten, müssen Sie berücksichtigen, wie dies die Qualitätsmerkmalen der Compute-Instanz mit der Hintergrundtask selbst auswirkt. Diese Faktoren werden Ihnen helfen zu entscheiden, ob er die Aufgaben mit den vorhandenen Compute Instanz zusammen zu suchen, oder in eine separate Compute Instanz trennen:

- **Verfügbarkeit**: Hintergrund-Tasks müssen möglicherweise nicht das gleiche Maß an Verfügbarkeit als andere Teile der Anwendung, insbesondere die Benutzeroberfläche und andere Teile, die Interaktion mit dem Benutzer direkt beteiligt haben. Hintergrundaufgaben können Latenz und Wiederversuchte Verbindungsfehler, toleranter und andere Faktoren die Verfügbarkeit beeinflussen, da die Vorgänge in die Warteschlange gestellt werden können. Es muss jedoch ausreichender Kapazität um zu verhindern, Sicherung der Anforderungen, die Warteschlangen zu blockieren und die Anwendung als Ganzes beeinflussen könnte.
- **Skalierbarkeit**: Hintergrund-Tasks werden voraussichtlich einen unterschiedlichen Skalierbarkeit auf die Benutzeroberfläche und die interaktiven Teile der Anwendung benötigen. Skalierung der Benutzeroberflächenautomatisierungs möglicherweise erforderlich, Gipfeln in der Nachfrage, zu erfüllen, während hervorragende Hintergrundaufgaben in weniger belebten Zeiten konnte, durch eine geringere abgeschlossen werden Anzahl von Compute Instanzen.
- **Ausfallsicherheit**: Ausfall einer Compute Instanz, dass nur Gastgeber Hintergrundaufgaben nicht tödlich die Anwendung als Ganzes beeinflussen können, wenn die Anforderungen für diese Aufgaben in die Warteschlange gestellt werden können, oder, bis die Aufgabe verschoben steht wieder zur Verfügung. Wenn die Compute Instanz bzw. Aufgaben in einen angemessenen Zeitraum neu gestartet werden können, können Benutzer der Anwendung nicht betroffen.
- **Sicherheit**: Hintergrundaufgaben können unterschiedliche Sicherheitsanforderungen oder Einschränkungen als die UI oder anderen Teilen der Anwendung haben. Mithilfe einer separaten Compute-Instanz können Sie eine unterschiedliche Sicherheitsumgebung für Aufgaben festlegen. Mustern wie Gatekeeper können Sie auch um die Hintergrund Compute Instanzen von der Benutzeroberfläche zu isolieren, um maximale Sicherheit und Trennung.
- **Leistung**: Sie können den Typ der Compute Instanz für Hintergrundtasks, insbesondere die Leistungsanforderungen der Aufgaben übereinstimmen. Dies kann bedeuten, eine weniger teure Compute-Option verwenden, wenn die Aufgaben nicht dieselben Verarbeitungsfunktionen wie die Benutzeroberfläche oder eine größere Instanz erforderlich ist, wenn sie zusätzliche Kapazitäten und Ressourcen benötigen.
- **Verwaltbarkeit**: Hintergrundaufgaben können einen unterschiedlichen Entwicklung und Bereitstellung von Rhythmus aus der Hauptanwendung-Code oder die Benutzeroberfläche haben. Bereitstellung in einem separaten Compute Instanz kann Updates und Versionsverwaltung vereinfachen.
- **Kosten**: Hinzufügen von Compute Instanzen zum Hintergrund ausführen Aufgaben erhöht, die hosting-Kosten. Sie sollten sorgfältig den Kompromiss zwischen Kapazität und diese Mehrkosten.

Weitere Informationen finden Sie unter [Führer-Wahl-Muster](http://msdn.microsoft.com/library/dn568104.aspx) und [Konkurrierende Verbraucher Muster](http://msdn.microsoft.com/library/dn568101.aspx).

## Konflikte

Wenn Sie mehrere Instanzen eines Hintergrundjobs haben, ist es möglich, dass sie für den Zugriff auf Ressourcen und Dienste wie Datenbanken und Speicher konkurrieren werden. Diese gleichzeitige Zugriff kann Ressourcenkonflikte, führen die Konflikte in der Verfügbarkeit der Dienste und die Integrität der Daten im Speicher führen können. Ressourcenkonflikte kann gelöst werden, mithilfe eines pessimistischen Sperren Ansatzes zu verhindern, dass konkurrierende Instanzen eines Vorgangs gleichzeitig den Zugriff auf einen Dienst oder Daten zu beschädigen.

Ein weiterer Ansatz zur Konfliktlösung soll Hintergrundaufgaben als Singleton, zu definieren, so dass es immer nur eine Instanz ausgeführt wird. Jedoch dadurch die Zuverlässigkeit und Leistung Vorteile, die eine Konfiguration mit mehreren Instanzen liefern kann, vor allem, wenn die Benutzeroberfläche ausreichend Arbeit um mehr als ein Hintergrundtask beschäftigt zu halten zur Verfügung stellen kann. Es ist wichtig, um sicherzustellen, dass die Hintergrundaufgabe automatisch neu starten kann und ausreichenden Kapazität für die Spitzen der Nachfrage zu bewältigen hat. Dies kann erreicht werden durch die Zuweisung einer Compute Instanz mit ausreichenden Ressourcen durch Implementieren einen Warteschlangen-Mechanismus, der Anforderungen zur späteren Ausführung, wenn die Nachfrage sinkt speichern kann, oder durch eine Kombination dieser Techniken.

## Koordinierung

Die Hintergrund-Tasks können komplex sein und erfordern mehrere einzelne Aufgaben ausführen um ein Ergebnis zu erzielen oder um alle Anforderungen zu erfüllen. Es ist üblich in diesen Szenarien zu teilen die Aufgabe in kleineren diskreten Schritten oder Teilvorgänge, die von mehreren Consumern ausgeführt werden können. Mehrstufige Arbeitsplätze können effizienter und flexibler sein, weil einzelne Schritte in mehrere Jobs wiederverwendbare sein können. Es ist auch einfach hinzufügen, entfernen oder ändern die Reihenfolge der Schritte.

Mehrere Aufgaben und Schritte zu koordinieren, kann schwierig sein, aber es gibt drei gängige Muster, die Sie verwenden können, um Ihre Implementierung einer Lösung führen:

- **Zerlegen eines Vorgangs in mehrere wiederverwendbare Schritte**. Eine Anwendung kann erforderlich sein, um eine Vielzahl von Aufgaben unterschiedlicher Komplexität der Informationen auszuführen, die it-Prozesse. Ein einfacher, aber unflexibel Ansatz zur Umsetzung dieser Anwendung könnte diese Verarbeitung als monolithische Modul durchführen. Allerdings dürfte sich dieser Ansatz zur Verringerung der Chancen für die Umgestaltung des Codes, optimiert oder es wiederverwenden, wenn Teile der gleichen Verarbeitung an anderer Stelle innerhalb der Anwendung erforderlich sind. Weitere Informationen finden Sie unter [Rohre und Filter-Muster](http://msdn.microsoft.com/library/dn568100.aspx).
- **Verwaltung der Ausführung der Schritte für eine Aufgabe**. Eine Anwendung kann es sich um Aufgaben, die eine Reihe von Schritten, umfassen, von die einige entfernte Plattformaufrufdienste oder Remoteressourcen zugreifen kann. Die einzelnen Schritte können unabhängig voneinander, aber sie sind von der Anwendungslogik, die die Aufgabe implementiert orchestriert. Weitere Informationen finden Sie unter [Scheduler-Agent Supervisor Muster](http://msdn.microsoft.com/library/dn589780.aspx).
- **Verwalten von Recovery für Schritte einer Aufgabe, die nicht**. Eine Anwendung muss möglicherweise die Arbeit durch eine Reihe von Schritten, die zusammen einen schließlich konsistenten Vorgang zu definieren, wenn eine oder mehrere der Schritte nicht rückgängig zu machen. Weitere Informationen finden Sie unter [Kompensierende Transaktion Muster](http://msdn.microsoft.com/library/dn589804.aspx).

## Lebenszyklus (Cloud-Services)

 Entscheiden Sie sich für Hintergrund-Jobs für Cloud-Services-Anwendungen zu implementieren, die mithilfe von Web und Worker Rollen mithilfe der **RoleEntryPoint** Klasse, ist es wichtig, den Lebenszyklus dieser Klasse zu verstehen, um es richtig zu verwenden.

Web und Worker Rollen durchlaufen eine Reihe von Phasen, wie sie start, ausführen und beenden. Die **RoleEntryPoint** -Klasse stellt eine Reihe von Ereignissen, die angeben, wann diese Phasen auftreten. Sie verwenden diese initialisieren, ausführen und beenden Ihre benutzerdefinierten Hintergrund-Aufgaben. Der komplette Zyklus ist:

- Azure die Rolle Assembly geladen und sucht sie nach einer Klasse, die von **RoleEntryPoint**.
- Wenn sie diese Klasse findet, ruft es **RoleEntryPoint.OnStart()**. Sie überschreiben diese Methode, um Ihre Hintergrund-Tasks zu initialisieren.
- Nach der **OnStart** Abschluss-Methode, aufruft Azure **Anwendung_Start()** in der Anwendung Global ist die Datei, wenn diese (z. B. "Global.asax" in einer Webserverrolle ausgeführt ASP.NET) vorhanden.
- Azurblaue Aufrufe **RoleEntryPoint.Run()** auf ein neues Vordergrundthread, die parallel ausgeführt wird **OnStart()**. Sie überschreiben Sie diese Methode, um Ihre Tasks im Hintergrund starten.
- Wenn die Run-Methode beendet wird, ruft zunächst Azure **Anwendung_End()** in der Anwendung Global ist die Datei, wenn diese Gegenwart und dann Anrufe **RoleEntryPoint.OnStop()**. Überschreiben Sie die **OnStop** Methode Ihre Hintergrundaufgaben, mehr Ressourcen bereinigen, entsorgen von Objekten und Schließen von Verbindungen, die die Aufgaben benutzt haben können.
- Der Azure Rolle Host Arbeitsprozess ist beendet. Zu diesem Zeitpunkt die Rolle wird recycelt werden und wird neu gestartet.

Für weitere Informationen und ein Beispiel zur Verwendung der Methoden des der **RoleEntryPoint** Klasse, siehe [Ressource-Konsolidierung-Muster zu berechnen](http://msdn.microsoft.com/library/dn589778.aspx).

## Überlegungen zur

Beachten Sie die folgenden Punkte, wenn Sie planen, wie Sie Hintergrundaufgaben in einer Web- oder Arbeitnehmer-Rolle ausgeführt werden:

- Der Standard **Ausführen** Methodenimplementierung in der **RoleEntryPoint** Klasse enthält einen Aufruf **Thread.Sleep(Timeout.Infinite)** Das hält die Rolle lebendig auf unbestimmte Zeit. Wenn Sie überschreiben die **Ausführen** Methode (die normalerweise erforderliche Hintergrundaufgaben ausführen) Sie dürfen nicht den Code um die Methode zu verlassen, wenn Sie die Rolleninstanz wiederverwenden möchten.
- Eine typische Implementierung von der **Ausführen** Methode enthält Code zum Starten der Hintergrundaufgaben und eine Schleifenkonstruktion, die Bundesland alle Hintergrundaufgaben in regelmäßigen Abständen überprüft. Es kann alle neu starten, die fehlschlagen, oder überwachen Sie Abbruch-Token, die angeben, dass Aufträge abgeschlossen haben.
- Wenn eine Hintergrundaufgabe eine nicht behandelte Ausnahme auslöst, sollte diese Aufgabe recycelt werden gleichzeitig andere Hintergrund-Tasks in der Rolle weiter ausgeführt werden. Jedoch, wenn die Ausnahme durch Korruption von Objekten außerhalb der Task, z. B. freigegebenen Speicher verursacht wird die Ausnahme sollten von behandelt werden Ihre **RoleEntryPoint** Klasse, alle Aufgaben wird abgebrochen, und die **Ausführen** Methode, die erlaubt, zu beenden. Azure wird dann die Rolle neu gestartet.
- Nutzung der **OnStop** Methode zum Anhalten oder töten Hintergrundaufgaben und Ressourcen bereinigen. Das kann bedeuten, lang andauernde oder mehrstufige Aufgaben zu beenden, und es ist wichtig zu prüfen, wie dies erreicht werden kann, um Dateninkonsistenzen zu vermeiden. Wenn aus irgendeinem Grund als ein Benutzer initiierte Herunterfahren, in ausgeführten Code eine Rolleninstanz stoppt die **OnStop** Methode muss abgeschlossen, innerhalb von fünf Minuten bevor sie gewaltsam beendet wird. Stellen Sie sicher, dass Ihr Code in dieser Zeit abgeschlossen oder tolerieren kann, nicht zum Abschluss ausgeführt.  
- Azure Lastenausgleich beginnt Regelung des Verkehrs auf die Rolle Instanz, wenn die **RoleEntryPoint.OnStart** Methode gibt true zurück. Daher sollten Sie setzen Ihre Initialisierungscode in der **OnStart** Methode sodass Rolleninstanzen, die nicht erfolgreich Will initialisieren keiner Datenverkehr empfangen.
- Können Sie Startaufgaben neben der **RoleEntryPoint** Klasse. Sie sollten die Startaufgaben verwenden, um alle Einstellungen zu initialisieren, müssen Sie in der Azure-Load-Balancer zu ändern, da diese Aufgaben ausgeführt werden, bevor die Rolle alle Anforderungen erhält. Weitere Informationen finden Sie unter [Azure Startaufgaben einmünden](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- Wenn in einem Start-Vorgang ein Fehler vorliegt, kann es die Rolle ständig Neustart erzwingen. Dies können Sie verhindern, einen VIP-Swap auf eine zuvor bereitgestellte Version ausführen, weil der Swap exklusiven Zugriff auf die Rolle erfordert, und dies kann nicht abgerufen werden, während die Rolle neu gestartet wird. Um dieses Problem zu beheben:
	-  Fügen Sie den folgenden Code an den Anfang der **OnStart** und **Ausführen** Methoden in Ihrer Funktion:

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

   - Fügen Sie die Definition der **Einfrieren** Wenn Sie als booleschen Wert auf die ServiceDefinition.csdef und Channelsund.*.cscfg-Dateien für die Rolle und legen Sie es auf **falsch**. Wenn die Rolle in einem wiederholten Neustart-Modus geht, können Sie die Einstellung zu ändern. **wahr** zum Einfrieren Rolle Ausführung und ermöglichen es, mit einer früheren Version ausgetauscht werden.

## Belastbarkeit-Überlegungen

Hintergrundaufgaben muß widerstandsfähig, um zuverlässige Dienste an die Anwendung zu bieten. Bei der Planung und Gestaltung von Hintergrundaufgaben, beachten Sie die folgenden Punkte:

- Hintergrundaufgaben muss Rolle oder Dienst-Neustart ordnungsgemäß zu behandeln, ohne Daten zu beschädigen oder die Einführung von Inkonsistenz in der Anwendung. Für lang andauernde oder mehrstufige Aufgaben erwägen _Überprüfen Sie, zeigen_ durch Speichern des Zustands von Arbeitsplätzen im permanenten Speicher oder als Nachrichten in einer Warteschlange, wenn ist dies angebracht. Beispielsweise können Statusinformationen in einer Nachricht in einer Warteschlange beibehalten und inkrementell aktualisieren diese Zustandsinformationen mit dem Aufgabenfortschritt, so dass die Aufgabe aus dem letzten bekannten guten Checkpoint statt Neustarten von Anfang verarbeitet werden kann. Bei der Verwendung von Azure Service Bus Warteschlangen können Nachricht-Sitzungen Sie um das gleiche Szenario zu aktivieren. Sitzungen können Sie speichern und Abrufen des Verarbeitungsstatus Anwendung mithilfe der [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) und [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) Methoden. Weitere Informationen zu entwerfen sichere mehrstufige Prozesse und Workflows finden Sie unter [Scheduler-Agent Supervisor Muster](http://msdn.microsoft.com/library/dn589780.aspx).
- Wenn Web oder Worker Rollen zum Hosten mehrerer Hintergrundaufgaben, entwerfen die Überschreibung von der **Ausführen** Methode für Überwachung fehlgeschlagen oder ins Stocken geraten Aufgaben und Neustarten. Dies ist nicht praktikabel, und Sie verwenden eine Arbeiter-Rolle, zwingen die Arbeiter-Rolle neu starten durch Ausstieg aus der **Ausführen** -Methode.
- Wenn Sie Warteschlangen verwenden zur Kommunikation mit Hintergrundaufgaben, können die Warteschlangen fungieren als Puffer zum Speichern von Anfragen zu den Aufgaben, während die Anwendung unter höher als übliche Belastung ist. Dadurch können die Aufgaben der Benutzeroberfläche weniger anstrengenden Zeiten aufzuholen. Es bedeutet auch, dass die Rolle zu Wiederverwertung die Benutzeroberfläche nicht blockiert werden. Weitere Informationen finden Sie unter [Warteschlangenbasierten Load Abgleich Muster](http://msdn.microsoft.com/library/dn589783.aspx). Wenn einige Aufgaben wichtiger als andere sind, sollten Sie die Umsetzung der [Priorität Warteschlange Muster](http://msdn.microsoft.com/library/dn589794.aspx) um sicherzustellen, dass diese Aufgaben vor weniger wichtigen ausgeführt.
- Hintergrund-Tasks, die von initiiert werden oder anderweitig Nachrichten müssen so konzipiert sein, dass Unstimmigkeiten wie Nachrichten eintreffen Kaput, Nachrichten zu behandeln, die immer wieder dazu führen, einen Fehler dass (oft bezeichnet als _nicht verarbeitbare Nachrichten_), und Nachrichten, die mehr als einmal geliefert werden. Beachten Sie Folgendes:
  - Messages that must be processed in a specific order, such as those that change data based on its existing value (for example, adding a value to an existing value), may not arrive in the original order they were sent. Alternatively, they may be handled by different instances of a background task in a different order due to varying loads on each instance. Messages that must be processed in a specific order should include a sequence number, key, or some other indicator that background tasks can use to ensure they are processed in the correct order. If you are using Azure Service Bus, you can use message sessions to guarantee the order of delivery. However, it is usually more efficient where possible to design the process so that the message order is not important.
  - Typically, a background task will peek messages in the queue, which temporarily hides them from other message consumers, and then delete the messages after they have been successfully processed. If a background task fails when processing a message, that message will reappear on the queue after the peek timeout expires, and will be processed by another instance of the task or during the next processing cycle of this instance. If the message consistently causes an error in the consumer, it will block the task, the queue, and eventually the application itself when the queue becomes full. Therefore, it is vital to detect and remove poison messages from the queue. If you are using Azure Service Bus, messages that cause an error can be moved automatically or manually to an associated dead letter queue.
  - Warteschlangen sind bei garantiert. _mindestens einmal im_ delivery mechanisms, but they may deliver the same message more than once. In addition, if a background task fails after processing a message but before deleting it from the queue, the message will become available for processing again. Background tasks should be idempotent, which means that processing the same message more than once does not cause an error or inconsistency in the application’s data. Some operations are naturally idempotent, such as setting a stored value to a specific new value. However, operations such as adding a value to an existing stored value without checking that the stored value is still the same as when the message was originally sent will cause inconsistencies.  Azure Service Bus queues can be configured to automatically remove duplicated messages.
  - Einige Messagingsystemen wie Azure Speicher Warteschlangen und Warteschlangen mit Azure Service Bus, unterstützen eine De-queue Count-Eigenschaft, die angibt, wie oft, die eine Nachricht aus der Warteschlange gelesen wurde. Dies kann hilfreich im Umgang mit wiederholter und nicht verarbeitbare Nachrichten sein. Weitere Informationen finden Sie unter [Asynchrones Messaging Primer](http://msdn.microsoft.com/library/dn589781.aspx) und [Idempotency Muster](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Skalierung und Performance Überlegungen

Hintergrundaufgaben müssen bieten ausreichend Leistung um sicherzustellen sie nicht die Anwendung zu blockieren, oder Unstimmigkeiten aufgrund verspäteter Operation verursachen, wenn das System ausgelastet ist. In der Regel wird die Leistung verbessert, durch die Skalierung der Compute-Instanzen, die die Hintergrund-Tasks zu hosten. Bei der Planung und Gestaltung von Hintergrundaufgaben, betrachten Sie die folgenden Punkte, um Skalierbarkeit und Performance:

- Himmelblau unterstützt automatische Skalierung (Skalieren und Skalierung zurück in) auf der Grundlage von aktuellen Nachfrage und Auslastung, oder nach einem vordefinierten Zeitplan für Websites, Cloud Services Web und Worker-Funktionen und virtuellen Computern gehostet Bereitstellungen. Verwenden Sie diese Funktion, Sicherstellung die Anwendung als Ganzes ausreichende Leistungsfähigkeit hat bei gleichzeitiger Minimierung der Kosten der Laufzeit.
- Hintergrundaufgaben eine unterschiedliche Leistungsfähigkeit von den anderen Teilen einer Cloud Services-Anwendung (z. B. UI oder Komponenten wie der Datenzugriffsebene) verfügen, können hosting-Hintergrund-Tasks zusammen in einer separaten Arbeitsprozess Rolle der Benutzeroberfläche und der Hintergrund Aufgabe Rollen zu unabhängig voneinander zu skalieren, um die Last zu verwalten. Wenn mehrere Tasks im Hintergrund deutlich unterschiedliche Leistungsfähigkeit voneinander haben, sollten Sie sie in separaten Worker-Funktionen unterteilen und Skalierung jeder Rolle geben Sie unabhängig, aber beachten Sie, dass dies die Laufzeit Kosten im Vergleich zu kombinieren alle Aufgaben zu weniger Rollen erhöhen kann.
- Einfach die Rollen Skalierung möglicherweise nicht so Leistungsverlust unter Last zu verhindern. Sie müssen auch zur Skalierung von Speicher Warteschlangen und anderen Ressourcen an einen einzigen Punkt des Gesamtsystems zu verhindern Verarbeitung Kette immer einen Engpass. Überlegen Sie auch, andere Einschränkungen, z. B. den maximalen Durchsatz der Speicher und weitere Dienstleistungen der Anwendungdes und die Hintergrund-Tasks abhängig.
- Hintergrund-Tasks sind für Skalierung auszulegen. Sie müssen z. B. die Anzahl der Speicher-Warteschlangen im Einsatz um an empfangen oder Senden von Nachrichten an die entsprechende Warteschlange dynamisch zu erkennen.
- In der Standardeinstellung WebJobs Skala mit ihren zugehörigen Azure Web-Sites-Instanz. Allerdings, wenn ein WebJob als nur eine Instanz laufen soll, können Sie eine Settings.job-Datei mit den JSON-Daten erstellen **{"ist_Singleton": wahre}**. Dies zwingt Azure, nur eine Instanz der WebJob ausgeführt, selbst wenn mehrere Instanzen der zugehörigen Website, die eine nützliche Technik für geplante Aufträge sein kann, die als nur eine einzige Instanz ausgeführt werden muss.

## Verwandte Entwurfsmuster

- [Asynchrones Messaging Primer](http://msdn.microsoft.com/library/dn589781.aspx)
- [Automatische Skalierung Anleitung](http://msdn.microsoft.com/library/dn589774.aspx)
- [Kompensierende Transaktion Muster](http://msdn.microsoft.com/library/dn589804.aspx)
- [Konkurrierende Verbraucher Muster](http://msdn.microsoft.com/library/dn568101.aspx)
- [Berechnen Sie Anleitung-Partitionierung](http://msdn.microsoft.com/library/dn589773.aspx)
- [Ressource-Konsolidierung-Muster zu berechnen](http://msdn.microsoft.com/library/dn589778.aspx)
- [Gatekeeper-Muster](http://msdn.microsoft.com/library/dn589793.aspx)
- [Führer-Wahl-Muster](http://msdn.microsoft.com/library/dn568104.aspx)
- [Rohre und Filter-Muster](http://msdn.microsoft.com/library/dn568100.aspx)
- [Priorität Warteschlange Muster](http://msdn.microsoft.com/library/dn589794.aspx)
- [Warteschlangenbasierten Load Abgleich Muster](http://msdn.microsoft.com/library/dn589783.aspx)
- [Scheduler-Agent Supervisor Muster](http://msdn.microsoft.com/library/dn589780.aspx)

## Weitere Informationen

- [Skalierung der azurblaue Anwendungen mit Worker-Funktionen](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Ausführen von Tasks im Hintergrund](http://msdn.microsoft.com/library/ff803365.aspx)
- [Azurblaue Rolle Startup-Lebenszyklus](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (Blog-Eintrag)
- [Azure Cloud Services-Rolle-Lebenszyklus](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (Video)
- [Erste Schritte mit dem azurblauen WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started/)
- [Azurblaue Warteschlangen und Service Bus Warteschlangen - verglichen und kontrastiert](http://msdn.microsoft.com/library/hh767287.aspx)
- [Aktivieren der Diagnose in einem Cloud-Service](http://msdn.microsoft.com/library/dn482131.aspx)
